---
layout: post
title:  "Using highland.js to work with large files"
date:   2017-06-05 09:00:00
---

# Using highland.js to work with large files

[highland.js](http://highlandjs.org) is a really useful library for working with data in JavaScript as streams. It gives you nice, easily understandable chains of transforms to data.

Streams in Node.js are really useful for working with large files. If you don't use streams to read large files in Node.js you will get errors.

Here are some example functions that demonstrate Highlands working with big files.

For test data I downloaded a dump of [stackoverflow.com comments](https://archive.org/download/stackexchange/stackoverflow.com-Comments.7z). Unzipped this file is just over 14GB. I just copied it a couple of times to get multiple files.

### Setup

```javascript
const fs = require("fs");
const hl = require('highland');

const sourceFiles = ['./Comments1.xml', './Comments1.xml', './Comments1.xml'];
const destFile = './Out.xml';
```

### Merge

```javascript
function merge() {
  const writableStream = fs.createWriteStream(destFile);

  hl(sourceFiles)
    .map(fs.createReadStream)
    .flatMap(hl)
    .pipe(writableStream);
}
```

### First lines

```javascript
function firstLines() {
  hl(sourceFiles)
    .map(fs.createReadStream)
    .flatMap(nodeStream =>
      hl(nodeStream)
        .split()
        .take(1)
    )
    .each(result => console.log(result));
}
```

### Count lines

```javascript
function countLines() {
  hl(sourceFiles)
    .map(fs.createReadStream)
    .flatMap(nodeStream =>
      hl(nodeStream)
        .split()
        .reduce(0, (total, line) => total + 1))
    .reduce(0, (total, count) => total + count)
    .toCallback((err, result) => console.log(result));
}
```
