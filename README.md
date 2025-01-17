# SimpleXLSX class 0.8.9 (Official)
[<img src="https://img.shields.io/endpoint.svg?url=https%3A%2F%2Fshieldsio-patreon.herokuapp.com%2Fshuchkin" />](https://www.patreon.com/shuchkin) [<img src="https://img.shields.io/github/license/shuchkin/simplexlsx" />](https://github.com/shuchkin/simplexlsx/blob/master/license.md) [<img src="https://img.shields.io/github/stars/shuchkin/simplexlsx" />](https://github.com/shuchkin/simplexlsx/stargazers) [<img src="https://img.shields.io/github/forks/shuchkin/simplexlsx" />](https://github.com/shuchkin/simplexlsx/network) [<img src="https://img.shields.io/github/issues/shuchkin/simplexlsx" />](https://github.com/shuchkin/simplexlsx/issues)

Parse and retrieve data from Excel XLSx files. MS Excel 2007 workbooks PHP reader.
No addiditional extensions need (internal unzip + standart SimpleXML parser)
XLSX only, MS Excel 97 php reader [here](https://github.com/shuchkin/simplexls)  

**Sergey Shuchkin** <sergey.shuchkin@gmail.com> 2010-2019<br/>

*Hey, bro, please ★ the package for my motivation :)* 

## Basic Usage
```php
if ( $xlsx = SimpleXLSX::parse('book.xlsx') ) {
	print_r( $xlsx->rows() );
} else {
	echo SimpleXLSX::parseError();
}
```
```
Array
(
    [0] => Array
        (
            [0] => ISBN
            [1] => title
            [2] => author
            [3] => publisher
            [4] => ctry
        )

    [1] => Array
        (
            [0] => 618260307
            [1] => The Hobbit
            [2] => J. R. R. Tolkien
            [3] => Houghton Mifflin
            [4] => USA
        )

)
```
```
// SimpleXLSX::parse( $filename, $is_data = false, $debug = false, $skip_empty_rows = false ): SimpleXLSX (or false)
```

## Installation
The recommended way to install this library is [through Composer](https://getcomposer.org).
[New to Composer?](https://getcomposer.org/doc/00-intro.md)

This will install the latest supported version:
```bash
$ composer require shuchkin/simplexlsx
```
or download class [here](https://github.com/shuchkin/simplexlsx/blob/master/src/SimpleXLSX.php)

## Examples
### XLSX to html table
```php
if ( $xlsx = SimpleXLSX::parse('book.xlsx') ) {
	echo '<table border="1" cellpadding="3" style="border-collapse: collapse">';
	foreach( $xlsx->rows() as $r ) {
		echo '<tr><td>'.implode('</td><td>', $r ).'</td></tr>';
	}
	echo '</table>';
	// or $xlsx->toHTML();	
} else {
	echo SimpleXLSX::parseError();
}
```
### XLSX read cells, out commas and bold headers
```php
echo '<pre>';
if ( $xlsx = SimpleXLSX::parse( 'xlsx/books.xlsx' ) ) {
	foreach ( $xlsx->rows() as $r => $row ) {
		foreach ( $row as $c => $cell ) {
			echo ($c > 0) ? ', ' : '';
			echo ( $r === 0 ) ? '<b>'.$cell.'</b>' : $cell;
		}
		echo '<br/>';
	}
} else {
	echo SimpleXLSX::parseError();
}
echo '</pre>';
```
### XLSX get sheet names and sheet indexes
```php
if ( $xlsx = SimpleXLSX::parse( 'xlsx/books.xlsx' ) ) {
	print_r( $xlsx->sheetNames() );
}
// Sheet numeration started 0
```
```
Array
(
    [0] => Sheet1
    [1] => Sheet2
    [2] => Sheet3
)
```
### Gets extend cell info by ->rowsEx()
```php
print_r( SimpleXLSX::parse('book.xlsx')->rowsEx() );
```
```
Array
(
    [0] => Array
        (
            [0] => Array
                (
                    [type] => s
                    [name] => A1
                    [value] => ISBN
                    [href] => 
                    [f] => 
                    [format] => 
                    [r] => 1
                )

            [1] => Array
                (
                    [type] => 
                    [name] => B1
                    [value] => 2016-04-12 13:41:00
                    [href] => 
                    [f] => 
                    [format] => m/d/yy h:mm
                    [r] => 2
                )
```
### Select Sheet
```php
$xlsx = SimpleXLSX::parse('book.xlsx');
print_r( $xlsx->rows(1) ); // Sheet numeration started 0, we select second worksheet
```
### Get sheet by index 
```php
$xlsx = SimpleXLSX::parse('book.xlsx');	
echo 'Sheet Name 2 = '.$xlsx->sheetName(1);
```
### XLSX::parse remote data
```php
if ( $xlsx = SimpleXLSX::parse('http://www.example.com/example.xlsx' ) ) {
	$dim = $xlsx->dimension(1); // don't trust dimension extracted from xml
	$num_cols = $dim[0];
	$num_rows = $dim[1];
	echo $xlsx->sheetName(1).':'.$num_cols.'x'.$num_rows;
} else {
	echo SimpleXLSX::parseError();
}
```
### XLSX::parse memory data
```php
// For instance $data is a data from database or cache    
if ( $xlsx = SimpleXLSX::parse( $data, true ) ) {
	print_r( $xlsx->rows() );
} else {
	echo SimpleXLSX::parseError();
}
```
### Get Cell (slow)
```php
echo $xlsx->getCell(0, 'B2'); // The Hobbit
``` 
### DateTime helpers
```php
echo $xlsx->getCell(0,'C2'); // 2016-04-12 13:41:00
$xlsx->setDateTimeFormat('d.m.Y H:i');
echo $xlsx->getCell(0,'C2'); // 12.04.2016 13:41
$xlsx->setDateTimeFormat('U'); // returns as unixtimestamp
echo  $xlsx->getCell(0,'C2'); // 1460468460
$excel_date = $xlsx->setDateTimeFormat( NULL ); // returns as excel datetime
echo $excel_date // 42472.570138889
echo date('m/d/Y', $xlsx->unixstamp( $excel_date )); // 04/12/2016 
```
### Rows with header values as keys
```php
if ( $xlsx = SimpleXLSX::parse('books.xlsx')) {
	// Produce array keys from the array values of 1st array element
	$header_values = $rows = [];
	foreach ( $xlsx->rows() as $k => $r ) {
		if ( $k === 0 ) {
			$header_values = $r;
			continue;
		}
		$rows[] = array_combine( $header_values, $r );
	}
	print_r( $rows );
}
```
```
Array
(
    [0] => Array
        (
            [ISBN] => 618260307
            [title] => The Hobbit
            [author] => J. R. R. Tolkien
            [publisher] => Houghton Mifflin
            [ctry] => USA
        )
    [1] => Array
        (
            [ISBN] => 908606664
            [title] => Slinky Malinki
            [author] => Lynley Dodd
            [publisher] => Mallinson Rendel
            [ctry] => NZ
        )
)
```
### Debug
```php
ini_set('error_reporting', E_ALL );
ini_set('display_errors', 1 );

if ( $xlsx = SimpleXLSX::parse('books.xlsx', false, true ) ) {
	echo $xlsx->toHTML();
} else {
	echo SimpleXLSX::parseError();
}
```
### Classic OOP style 
```php
$xlsx = new SimpleXLSX();
$xlsx->debug = true; // display errors
$xlsx->skipEmptyRows = true; // skip empty rows
if ($xlsx->parseFile('books.xlsx')) {
	print_r( $xlsx->rows() );
} else {
	echo 'xlsx error: '.$xlsx->error();
}
```
More examples [here](https://github.com/shuchkin/simplexlsx/tree/master/examples)

### Error Codes
SimpleXLSX::ParseErrno(), $xlsx->errno()<br/>
<table>
<tr><th>code</th><th>message</th><th>comment</th></tr>
<tr><td>1</td><td>File not found</td><td>Where file? UFO?</td></tr>
<tr><td>2</td><td>Unknown archive format</td><td>ZIP?</td></tr>
<tr><td>3</td><td>XML-entry parser error</td><td>bad XML</td></tr>
<tr><td>4</td><td>XML-entry not found</td><td>bad ZIP archive</td></tr>
<tr><td>5</td><td>Entry not found</td><td>File not found in ZIP archive</td></tr>
<tr><td>6</td><td>Worksheet not found</td><td>Not exists</td></tr>
</table>	

## History
```
v0.8.9 (2019-08-15) fixed release version 
v0.8.8 (2019-06-19) removed list( $x, $y ), added bool $xlsx->skipEmptyRows, $xlsx->parseFile( $filename ), $xlsx->parseData( $data ), release 0.8.8
v0.8.7 (2019-04-18) empty rows fixed, release 0.8.7
v0.8.6 (2019-04-16) 1900/1904 bug fixed
v0.8.5 (2019-03-07) SimpleXLSX::ParseErrno(), $xlsx->errno() returns error code
v0.8.4 (2019-02-14) detect datetime values, mb_string.func_overload=2 support .!. Bitrix
v0.8.3 (2018-11-14) getCell - fixed empty cells and rows, safe now, but very slow
v0.8.2 (2018-11-09) fix empty cells and rows in rows() and rowsEx(), added setDateTimeFormat( $see_php_date_func )
v0.8.1 rename simplexlsx.php to SimpleXLSX.php, rename parse_error to parseError fix _columnIndex, add ->toHTML(), GNU to MIT license
v0.7.13 (2018-06-18) get sheet indexes bug fix
v0.7.12 (2018-06-17) $worksheet_id to $worksheet_index, sheet numeration started 0
v0.7.11 (2018-04-25) rowsEx(), added row index "r" to cell info
v0.7.10 (2018-04-21) fixed getCell, returns NULL if not exits
v0.7.9 (2018-03-17) fixed sheetNames() (namespaced or not namespaced attr)
v0.7.8 (2018-01-15) remove namespace prefixes (hardcoded)
v0.7.7 (2017-10-02) XML External Entity (XXE) Prevention (<!ENTITY xxe SYSTEM "file: ///etc/passwd" >]>)
v0.7.6 (2017-09-26) if worksheet_id === 0 (default) then detect first sheet (for LibreOffice capabilities)
v0.7.5 (2017-09-10) ->getCell() - fixed
v0.7.4 (2017-08-22) ::parse_error() - to get last error in "static style"
v0.7.3 (2017-08-14) ->_parse fixed relations reader, added ->getCell( sheet_id, address, format ) for direct cell reading
v0.7.2 (2017-05-13) ::parse( $filename ) helper method
v0.7.1 (2017-03-29) License added
v0.6.11 (2016-07-27) fixed timestamp()
v0.6.10 (2016-06-10) fixed search entries (UPPERCASE)
v0.6.9 (2015-04-12) $xlsx->datetime_format to force dates out
v0.6.8 (2013-10-13) fixed dimension() where 1 row only, fixed rowsEx() empty cells indexes (Daniel Stastka)
v0.6.7 (2013-08-10) fixed unzip (mac), added $debug param to _constructor to display errors
v0.6.6 (2013-06-03) +entryExists()
v0.6.5 (2013-03-18) fixed sheetName()
v0.6.4 (2013-03-13) rowsEx(), _parse(): fixed date column type & format detection
v0.6.3 (2013-03-13) rowsEx(): fixed formulas, added date type 'd', added format 'format'
					dimension(): fixed empty sheet dimension
                    + sheetNames() - returns array( sheet_id => sheet_name, sheet_id2 => sheet_name2 ...)
v0.6.2 (2012-10-04) fixed empty cells, rowsEx() returns type and formulas now
v0.6.1 (2012-09-14) removed "raise exception" and fixed _unzip
v0.6 (2012-09-13) success(), error(), __constructor( $filename, $is_data = false )
v0.5.1 (2012-09-13) sheetName() fixed
v0.5 (2012-09-12) sheetName()
v0.4 sheets(), sheetsCount(), unixstamp( $excelDateTime )
v0.3 - fixed empty cells (Gonzo patch)
```
