# 将多页的网页表格下载为csv
例如：[https://ngdc.cncb.ac.cn/ictcf/Resource.php](https://ngdc.cncb.ac.cn/ictcf/Resource.php
将以下脚本在浏览器console中执行后可以得到完整的csv表格
```javascript
// Quick and simple export target #table_id into a csv
function table_as_csv(table_id, save_top = true, save_bottom = true, separator = ',') {
    // Select rows from table_id
    var rows = document.querySelectorAll('table#' + table_id + ' tr');
    // Construct csv
    var csv = [];
    for (var i = 0; i < rows.length; i++) {
        var row = [], cols = rows[i].querySelectorAll('td, th');
        for (var j = 0; j < cols.length; j++) {
            // Clean innertext to remove multiple spaces and jumpline (break csv)
            var data = cols[j].innerText.replace(/(\r\n|\n|\r)/gm, '').replace(/(\s\s)/gm, ' ')
            // Escape double-quote with double-double-quote (see https://stackoverflow.com/questions/17808511/properly-escape-a-double-quote-in-csv)
            data = data.replace(/"/g, '""');
            // Push escaped string
            row.push('"' + data + '"');
        }
        csv.push(row.join(separator));
    }
    if (!save_top) csv = csv.slice(start=1);
    if (!save_bottom) csv = csv.slice(start=0, end=-1);
    // console.log(csv);
    // console.log(save_top, save_bottom);
    return csv;
}

function download_csv(csv, table_id) {
    var csv_string = csv.join('\n');
    // Download it
    var filename = 'export_' + table_id + '_' + new Date().toLocaleDateString() + '.csv';
    var link = document.createElement('a');
    link.style.display = 'none';
    link.setAttribute('target', '_blank');
    link.setAttribute('href', 'data:text/csv;charset=utf-8,' + encodeURIComponent(csv_string));
    link.setAttribute('download', filename);
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
}

var start = 1;
var num_pages = 77;
var page_list = Array.from({length: num_pages}, (_, i) => i + start)
var table_id = "Results";
var csvs = [];
for (let i of page_list) {
    Submit(i);
    console.log(i);
    var res = table_as_csv(table_id, save_top=(i==start), save_bottom=false);
    csvs = csvs.concat(res);
}
// console.log(csvs);
download_csv(csvs, table_id)
```
