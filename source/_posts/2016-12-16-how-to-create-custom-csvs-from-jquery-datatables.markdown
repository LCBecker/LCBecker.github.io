---
layout: post
title: "How To Create Custom CSVs from jQuery Datatables"
date: 2016-12-16 09:59:05 -0500
comments: true
categories: how-to, csv, datatables
---
It's official! I've been a software engineer who has been gainfully employed for more than __1 year__. And so, to celebrate, this post will be a technical one! Where I work, there's a scenario that pops up quite often:

> We'd like to be able to export the data in a table located on _x_ page of our application as a CSV file.

It sounds simple enough. We use jQuery Datatables to generate most of the tables in our application. And of course, Datatables actually provides the ability to [save the table as a CSV](https://datatables.net/reference/button/csv) or another file type. But unfortunately, there are some problems with using Datatables' built in CSV export:
1. Pagination: If you dynamically fill each page with data by making new ajax calls, then the CSV export will only export the active page, not all data.
2. You might want to include some data that's not displayed in the datatable, such as hidden values, or those used as part of a link for a row.

This is when you need to generate a custom CSV from your datatable. Here's the process:

1. Create an export button near your datatable. Simple enough.
2. Next, you need to get the raw data. There are 2 ways to do this, depending on your situation. If there's no pagination and all of your data is contained in the datatable, you can simply do:
```
var dt = $("#idOfMyDatatable").DataTable();
var data = dt.rows().data();
```
If you're using pagination and need to retrieve all data, you can make an ajax request to your backend, probably to the same function you use to populate your datatable in the first place, but you can update it to retrieve all data by setting the `iDisplayLength` value to the `recordsDisplay` value. That's the total number of values in the table (The 100 in 'Viewing 1-10 of 100'):
```
var dt = $("#idOfMyDatatable").DataTable();
dt.on('init.dt', function(e) {
  var params = table.ajax.params();
  var iDisplayLength = dt.page.info().recordsDisplay;
  $.ajax({
    url: "/getDTJson/?iColumns=" + params.iColumns + "&iDisplayStart=0&iDisplayLength=" + iDisplayLength + "&sSearch=" + params.sSearch,
    type: 'GET',
    success: function(data) {
      //This is our next step - working with the data. For now, you've retrieved it!
    }
    });
});
```
3. So, once we have our data, we can begin to craft our CSV file. We'll need to set up our basics like the headers and some basic file information. We'll also need to create an array, where each value is a line in the CSV.
```
var headings = ["Heading1", "Heading2", "Heading3"];
var lineArray = [];
lineArray.push("data:application/csv;charset=utf-8," + headings);
```
4. Adding your actual data depends on how you retrieved your data in step 2.
  * If you're getting your data from the datatable itself, ie: `var data = dt.rows().data();`, your data will look a little something like

  ```
  {"0": ["value1", "value2", "value3", "value4"], "1":["value1", "value2", "value3", "value4"]}
  ```
  You will need to loop through each array in the data object, pulling out only the values you want and storing those in a new array. If those values could possible contain a comma, make sure you escape them. Finally, join all values in your array into a string using a comma separator and store that string in your line array:

  ```
  for(var i = 0; i < data.length; i++) {
    var lineWithSelectedValues = [];
    lineWithSelectedValues.push("\"" + data[i][0] + "\"");
    lineWithSelectedValues.push(data[i][2]);
    lineWithSelectedValues.push("\"" + data[i][3] + "\"");
    var csvLine = lineWithSelectedValues.join(",");
    lineArray.push(csvLine);
  }
  ```
    * If you had to make a new ajax call to retrieve your data, your data will be in a two-dimensional array under the key `aaData`. Your data will look something like
    ```
    {"aaData": [["value1", "value2", "value3", "value4"],["value1", "value2", "value3", "value4"]]}
    ```
    You will need to loop through each nested array and extract only the values you want. Usually, I do this via a separate function, but for ease, I'll combine it into my code sample:

    ```
    for (var i = 0; i < data.aaData.length; i++) {
      var dataArray = data.aaData[i];
      var lineWithSelectedValues = [];
      lineWithSelectedValues.push("\"" + dataArray[0] + "\"");
      lineWithSelectedValues.push(dataArray[2]);
      lineWithSelectedValues.push("\"" + dataArray[3] + "\"");
      var csvLine = lineWithSelectedValues.join(",");
      lineArray.push(csvLine);
    }
    ```

5. Our last step is to finalize our CSV content and hook up our export button to actually provide the ability to download the CSV. No matter how you got your data, you should have an array `var lineArray` that contains comma separated strings. We need to convert that to being just one string, with each string-row ending in a new line.
```
var csvContent = lineArray.join("\n");
var fileName = 'MyDatatbleCsv';
$("#exportBtn").attr({
  'href': encodeURI(csvContent),
  'download': fileName + '.csv',
  'target': '_blank'
  });
```
And there you have it! This is how I create a custom CSV from a datatable. I hope this helps you in your endeavors!
