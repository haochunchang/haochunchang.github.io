---
layout: post
mathjax: true
published: true
title: "The Issues when I tested a React component that upload files through axios"
date: "2021-08-30"
category: [Programming]
---

## The FileUploader component

There is a React component that sends POST request to API with its `selectedFile` in JSON format when `handleSubmit` is called. 

```jsx
class FileUploader extends React.Component {

  constructor(props) {
    super(props);
    this.state = { selectedFile: null };
  }

	clickButton = () => {
    document.getElementById("submit_button").click();
  }

  setSelectedFile = (event) => {
    this.setState({
      selectedFile: event.target.files[0]
    });
  }

	postData = (endpoint, data) => {
	  axios.post(endpoint, data)
	    .then((res) => {
	      this.props.handler(res.data);
     }).catch((err) => {
        throw new Error(err);
     });
	}

	readJSONFile = (file) => {
    return new Promise((resolve, reject) => {
      const reader = new FileReader();
      reader.addEventListener("load", (event) => {
        resolve(JSON.parse(event.target.result));
      });
      reader.readAsText(file);
      reader.onerror = (e) => { reject(e); };
    })
  }

	handleSubmit = () => {
		// ...
		this.readJSONFile(this.state.selectedFile)
		    .then((data) => {
			    this.postData("<The API endpoint>", data);
		    }).catch((err) => {
		      throw new Error(err);
		    });
		// ...

	render() {
		return (
			// ...
			<input
        type="file"
        accept="application/json"
        onChange={(e) => this.setSelectedFile(e)}
        hidden />
          <div className="submitbtn" onClick={this.clickButton}>
            <input id="submit_button" type="submit" value="Submit"/>
          </div>
			//...
		)
	}
```

## The issue in the tests (using Jest & Enzyme)

### Issue 1: the mocked `axios.post` does not get called.

- In the test, I wanted to test clicking the submit button actually follows the logic and triggers `axios`, therefore I wrote the following tests:
    - Constructs dummy json file to be selected by FileUploader.
    - Mocks:
        - `axios.post` to let it resolves into a mock data object.
        - the onClick callback `clickButton` with `handleSubmit` (to skip `document.getElementById`)
    - Simulates a click event to test if mocked `axios.post` was called.

```jsx
import { shallow, mount } from 'enzyme';
import axios from 'axios';

import FileUploader from '../file_uploader';

jest.mock('axios');
axios.post.mockResolvedValue({});
handler = jest.fn();
handler.mockImplementation(() => { return "completed" });
wrapper = mount(<FileUploader handler={handler}/>);

test('click submit triggers post JSON requests', async () => {
    let blob = new Blob([JSON.stringify({ data: [0.1] })]);
    let file = new File(
				[blob], 'dummy.json', { type: 'application/json' }
		);
    const fileEvent = {
        target: { files: [file] }
    };
    wrapper.instance().setSelectedFile(fileEvent);

    const handleClick = jest.spyOn(wrapper.instance(), 'clickButton');
    handleClick.mockImplementation(wrapper.instance().handleSubmit);
    wrapper.instance().forceUpdate();

    wrapper.find('.submitbtn').simulate('click');
    expect(handleClick).toHaveBeenCalledTimes(1);
		
		// Failed, described in issue1
    expect(axios.post).toHaveBeenCalledTimes(1);
    expect(axios.post).toHaveBeenCalledWith(
        '<The API endpoint>',
        { data: [0.1] }
    );
	
		// Failed, described in issue2
		expect(handler).toHaveBeenCalled();
});
```

After trying lots of search, it seems that the `load` event of the `FileReader` never get called during the tests. 

Many answers from the Internet mocks the `FileReader` as there is no need to test the `FileReader` API in our own tests.

> Mostly inspired by:
> 

[React how to test function called in FileReader onload function](https://stackoverflow.com/questions/47963118/react-how-to-test-function-called-in-filereader-onload-function)

### The workaround

Since we can assume `FileReader` is working correctly **AND** the uploading is working correctly in manual testing,  I created another mock function to replace the reading JSON part:

```jsx
test('click submit triggers post JSON requests', async () => {
		const readJSONFileSpy = jest.spyOn(
				wrapper.instance(), 'readJSONFile'
		);
    readJSONFileSpy.mockImplementation(async () => {
        await wrapper.instance().postData(
            '<The API endpoint>', { data: [0.1] }
        );
    });
		// Construct dummy file, simulate click...
});
```

This mock simply skips `FileReader` , `JSON.parse` and calls `postData` directly.

---

### Issue 2: the `this.props.handler` is called but not detected in Jest

In this test, another issue is the handler passed into `FileUploader` : the handler is not called even after solving issue 1.

With `console.log` I was able to see that the handler returns the result from mocked `axios.post` , however, the test still failed at `expect(handler).toHaveBeenCalled();` .

```jsx
postData = (endpoint, data) => {
	axios.post(endpoint, data)
	   .then((res) => {
				// I can see the console.log output after npm run test
				console.log(this.props.handler(res.data));
	      this.props.handler(res.data);
     }).catch((err) => {
        throw new Error(err);
     });
}
```

Equivalently, the following test failed:

```jsx
test('postData really calls the passed in handler', () => {
   wrapper.instance().postData(
	   '<The API endpoint>', { data: [0.1] }
   );
   expect(handler).toHaveBeenCalledTimes(1);
})
```

### The workaround

Prepend `await` to the simulate function solves this issue:

```jsx
//...
await wrapper.find('.submitbtn').simulate('click');
//...
```

## Thoughts

- I am still not sure why the `load` event was not fired in the tests. Even though I used async / await to make the test wait.
- In retrospect, this test with this workaround and all mocks actually tests the logic flow (which I wrote) from "clicking the button" to "calling `axios.post`".
- I encountered these issues during my work in writing ReactJS component. Maybe the component, the tests, the pattern can have lots of improvement. Or maybe this test was actually meaningless except giving me false confidence.
