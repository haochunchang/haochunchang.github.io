---
layout: post
title: "Some caveats in Golang when accessing Database"
published: true
mathjax: true
date: "2022-03-25"
category: [Programming]
---


- In Postgres `numeric` column data type, Golang `sql` package will return `string([]uint8)` type. Therefore I used `strconv.ParseFloat` to parse it into float.
- Golang struct has default zero values (e.g. int will defaults to 0) for each field if you didn’t specify it.
    - This means that:
        - If we insert the value into the db, the db will record the value as 0.
        - But if we skip the value if it is 0, then db will record it as `NULL` and will be recognized as `nil` . (If we skip 0, then db simply ignore the value 0 which may not be the case we want)
    - To check if the data exists before insertion, we can use pointer field so that we can check if the pointer is `nil`.
        - It doesn’t affect json unmarshal since Golang automatically handle the pointer to point to the object and decode the json.

### Conclusion

Need to decide what behavior should be when dealing with empty input data.

This time I decided to skip insertion if underlying data are “all empty”, but inserting default zero values if partial data is missing.

One can also skip inserting missing value in partial data, but that needs to utilize pointer field for each one you want to skip.
