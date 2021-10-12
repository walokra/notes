# React

## Examples

* [Multistep form using useReducer and useContext](https://codesandbox.io/s/usecontext-usereducer-state-9gjoc?file=/src/App.tsx)

## JSON to CSV

```javascript
<Button
  variant="contained"
  color="secondary"
  onClick={() => JSONToCSVConvertor(jsonData, 'mydata')}
>
  Download CSV
</Button>

const JsonToCSV = (JsonData) => {
  // If JSONData is not an object then JSON.parse will parse the JSON string in an Object
  const arrData =
    typeof JsonData !== 'object' ? JSON.parse(JsonData) : JsonData;

  let csvData = '';

  // Generate the Label/Header
  let titleRow = '';

  // Extract the label from 1st index of on array
  // Convert each value to string and comma-seprated
  // eslint-disable-next-line no-return-assign
  Object.keys(arrData[0]).forEach((k) => (titleRow += `${k},`));

  titleRow = titleRow.slice(0, -1);

  // Append Label row with line break
  csvData += `${titleRow}\r\n`;

  // 1st loop is to extract each row
  // eslint-disable-next-line no-plusplus
  for (let i = 0; i < arrData.length; i++) {
    let dataRow = '';

    // 2nd loop will extract each column and convert it in string comma-seprated
    // Convert each value to string and comma-seprated
    // eslint-disable-next-line no-return-assign
    Object.keys(arrData[i]).forEach((k) => (dataRow += `"${arrData[i][k]}",`));

    dataRow.slice(0, dataRow.length - 1);

    // Add a line break after each row
    csvData += `${dataRow}\r\n`;
  }

  if (csvData === '') {
    console.log('[JsonToCSV] Invalid data');
    return [];
  }

  return csvData;
};

const JSONToCSVConvertor = (JsonData, title) => {
  const csvData = JsonToCSV(JsonData);
  let fileName = 'Report_';
  // Remove the blank-spaces from the title and replace it with an underscore
  fileName += title.replace(/ /g, '_');

  // Initialize file format you want csv or xls
  const uri = `data:text/csv;charset=utf-8,${escape(csvData)}`;

  // Generate a temp <a /> tag
  const link = document.createElement('a');
  link.href = uri;

  // Set the visibility hidden so it will not effect on your web-layout
  link.style = 'visibility:hidden';
  link.download = `${fileName}.csv`;

  // Append the anchor tag and remove it after automatic click
  document.body.appendChild(link);
  link.click();
  document.body.removeChild(link);
};
```
