# Reading-excel-data-using-next.js
1. FileUpload Component

```
jsx
Copy code
"use client"; // This line ensures that the component is rendered on the client side

import { useState } from 'react';
import * as XLSX from 'xlsx';

const FileUpload = () => {
  const [data, setData] = useState([]);

```

Explanation:

- "use client";This directive indicates that the component should be rendered on the client side. This is necessary in Next.js to use client-side features such as React hooks.
- import { useState } from 'react';Imports the useState hook from React. useState is used to manage the component’s state.
- import * as XLSX from 'xlsx';Imports all functionalities from the xlsx library. This library is used for reading and writing Excel files.
- const [data, setData] = useState([]);Initializes a state variable data with an empty array. setData is the function used to update this state. This state will store the parsed data from the Excel file.

2. handleFileChange Function

```
jsx
Copy code
  const handleFileChange = (e) => {
    const file = e.target.files[0];
    if (file) {
      const reader = new FileReader();
      
      reader.onload = (event) => {
        const binaryStr = event.target.result;
        const workbook = XLSX.read(binaryStr, { type: 'binary' });
        const firstSheetName = workbook.SheetNames[0];
        const worksheet = workbook.Sheets[firstSheetName];
        const jsonData = XLSX.utils.sheet_to_json(worksheet, { header: 1 });
        setData(jsonData);
      };

      reader.readAsBinaryString(file);
    }
  };

```

Explanation:

- const file = e.target.files[0];Retrieves the selected file from the file input. e.target.files is an array of files, and [0] gets the first file (in case multiple files are selected).
- if (file) { ... }Checks if a file was selected. If so, the code proceeds to read the file.
- const reader = new FileReader();Creates a new FileReader object to read the file contents.
- reader.onload = (event) => { ... }Defines a function to be executed when the file is successfully read. This function will process the file's content.
- const binaryStr = event.target.result;Retrieves the file’s content as a binary string from the FileReader.
- const workbook = XLSX.read(binaryStr, { type: 'binary' });Reads the binary string and converts it into a workbook object using xlsx.
- const firstSheetName = workbook.SheetNames[0];Gets the name of the first sheet in the workbook.
- const worksheet = workbook.Sheets[firstSheetName];Accesses the worksheet object for the first sheet.
- const jsonData = XLSX.utils.sheet_to_json(worksheet, { header: 1 });Converts the worksheet data into a JSON array. header: 1 indicates that the first row should be used as headers, and each subsequent row is an array of cell values.
- setData(jsonData);Updates the data state with the parsed JSON data.
- reader.readAsBinaryString(file);Starts reading the file as a binary string.

3. Table Component

```
jsx
Copy code
const Table = ({ data }) => {
  if (data.length === 0) return <p>No data to display</p>;

  return (
    <table>
      <thead>
        <tr>
          {data[0].map((header, index) => (
            <th key={index}>{header}</th>
          ))}
        </tr>
      </thead>
      <tbody>
        {data.slice(1).map((row, rowIndex) => (
          <tr key={rowIndex}>
            {row.map((cell, cellIndex) => (
              <td key={cellIndex}>{cell}</td>
            ))}
          </tr>
        ))}
      </tbody>
    </table>
  );
};

```

Explanation:

- if (data.length === 0) return <p>No data to display</p>;If there is no data, it displays a message saying "No data to display".
- <table> ... </table>Constructs an HTML table to display the data.
- <thead> ... </thead>Contains the table header. It maps the first row of data (assumed to be headers) to <th> elements.
- <tbody> ... </tbody>Contains the table body. It maps the remaining rows of data to <tr> elements. Each cell in the row is displayed as a <td> element.
- Component Signature
  
```
jsx
Copy code
const Table = ({ data }) => { ... }

```
This defines a functional React component called Table. The component receives a prop named data. This data is expected to be a 2D array (an array of arrays), where each inner array represents a row of data (including the first row for headers).

Conditional Rendering:

```
jsx
Copy code
if (data.length === 0) return <p>No data to display</p>;

```
- This checks if the data array is empty (i.e., if it has a length of 0).
- If data is empty, the component returns a <p> tag with the text "No data to display". This prevents rendering an empty table and informs the user that there’s no content available.

Table Structure:

```
jsx
Copy code
return (
  <table>
    <thead>
      <tr>
        {data[0].map((header, index) => (
          <th key={index}>{header}</th>
        ))}
      </tr>
    </thead>
    <tbody>
      {data.slice(1).map((row, rowIndex) => (
        <tr key={rowIndex}>
          {row.map((cell, cellIndex) => (
            <td key={cellIndex}>{cell}</td>
          ))}
        </tr>
      ))}
    </tbody>
  </table>
);

```
This block of code returns the HTML structure of the table. It is composed of two main parts: the table header (<thead>) and the table body (<tbody>).

Table Header (<thead>)

```
jsx
Copy code
<thead>
  <tr>
    {data[0].map((header, index) => (
      <th key={index}>{header}</th>
    ))}
  </tr>
</thead>

```
- The first row in data (data[0]) is assumed to contain the headers (column names).
- data[0].map((header, index) iterates over each item in the first row (data[0]), and for each item:
	- It creates a <th> (table header cell) for each header.
	- key={index} is a React-specific attribute that uniquely identifies each <th> element based on its position (index), ensuring React can efficiently re-render the table when needed.

Table Body (<tbody>)

```
jsx
Copy code
<tbody>
  {data.slice(1).map((row, rowIndex) => (
    <tr key={rowIndex}>
      {row.map((cell, cellIndex) => (
        <td key={cellIndex}>{cell}</td>
      ))}
    </tr>
  ))}
</tbody>

```
- data.slice(1) removes the first row (which contains the headers) and returns all rows starting from the second row. This represents the actual table data (excluding the headers).
- .map((row, rowIndex) iterates over each row of data. For each row:
	- It creates a <tr> (table row).
	- row.map((cell, cellIndex) further iterates over each cell in the row. For each cell:
		- It creates a <td> (table data cell) to represent the cell's content inside the table row.
		- key={cellIndex} is again a unique identifier for each <td>, based on its position in the row.

Recap:

1. If data is empty, it displays a message saying "No data to display".
2. If data has content:
	- It uses the first row of data to create the table headers inside the <thead>.
	- It uses the remaining rows (starting from the second) to create the table's content inside the <tbody>.
3. Each cell (<td>) and header (<th>) is rendered by iterating through the data array and dynamically creating table rows and columns.

Example of data:

If data looks like this:
```
javascript
Copy code
[
  ['Name', 'Age', 'Country'],   // Header row
  ['Alice', 25, 'USA'],         // Data rows
  ['Bob', 30, 'Canada'],
  ['Charlie', 22, 'UK']
]

```
The rendered table would look like:
```
html
Copy code
<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Age</th>
      <th>Country</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Alice</td>
      <td>25</td>
      <td>USA</td>
    </tr>
    <tr>
      <td>Bob</td>
      <td>30</td>
      <td>Canada</td>
    </tr>
    <tr>
      <td>Charlie</td>
      <td>22</td>
      <td>UK</td>
    </tr>
  </tbody>
</table>
```
