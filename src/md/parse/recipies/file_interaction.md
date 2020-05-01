---
title: File system interaction
description: Read and write UTF-8 CSV files 
keywords: ['csv', 'parse', 'parser', 'recipe', 'file', 'fs', 'read', 'write', 'utf8', 'utf-8', 'bom']
sort: 5
---

# File system interaction

This recipe illustrates how to read and write to a UTF-8 file with a byte order mark (BOM).

Let's assume we are reading a file with 3 records. The file has been previously written with this script:

```js
const fs = require('fs').promises;

fs.writeFile(
  `${__dirname}/input.csv`,
  [
    '\ufeff', // BOM
    'a,1\n',  // First record
    'b,2\n',  // Second record
  ].join(''),
  { encoding: 'utf8' }
);
```

We can now access the CSV content of this file with the native Node.js File System module `fs`. `csv-parse` does not provide any file access method, and using the native `fs` module in conjunction with `csv-parse` is easy and natural.

You must first choose the right API. This package exposed multiple APIs, all backed by the same parsing algorithm and supporting the same options. Which API you choose is outside the scope of this page, but their differences are documented in the [API section](/parse/api/).

The easiest way is using the [sync API](/parse/api/sync/). To use this API, simply read the file and pass it as an argument to `parse`, which will return the result as an array of records. Records may be printed to the console and written to a file one JSON per line for each record:

```js
const fs = require('fs');
const parse = require('csv-parse/lib/sync');
// Read the content
fs.readFile(`${__dirname}/input.csv`, (err, data) => {
  if (err) throw err;
  
  // Parse the csv content
  const records = parse(data);
  
  // Print records to the console
  for (let record of records) {
    console.info(record);
  }
  
  // Write a file with one JSON per line for each record
  ws = fs.createWriteStream(`${__dirname}/output.csv`);
  for (let record of records) {
    ws.write(`${JSON.stringify(record)}\n`);
  }
  ws.end();
});
```

Alternatively, you could use the [Stream API](/parse/api/stream/) by [piping a file-readable stream](/parse/recipies/stream_pipe/) to the parser transform stream which is itself piped into a writable stream.
