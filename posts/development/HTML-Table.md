---
title: HTML-Table
date: 2017-03-18 14:59:14
tags: [HTML,JS,Table]
categories: Web
---

## HTML Table Usage

### 0.Summary

表格由 ```<table>```标签来定义。

每个表格由行组成，由 ```<tr>``` 标签定义。

每行被分割为若干单元格，表头单元格由 ```<th>``` 标签定义，内容单元格由 `<td>`标签定义。

单元格可以包含文本、图片、列表、段落、表单、水平线、表格等等。

表格标题由 `<caption>`  标签定义，嵌入`<table>`标签内部



### 1.Dynamic Usage

##### 1.0 Set Cell ContentEditable

有时候需要允许用户对表格中的数据进行修改，一些人使用 上百行 js 代码通过监听鼠标点击单元格，然后向该单元格中嵌套一个Input，最后将Input的内容赋予该单元格，然而，殊不知，**HTML 的单元格本身就有可编辑属性，名为contenteditable，只要将该属性设置为true即可允许用户编辑该单元格**



##### 1.1 Traverse Table

既然允许用户编辑表格中的数据，我们就需要对新的表格数据进行一些操作，例如，规则检查，或者保存并上传数据到服务器，这时候就需要对表格进行遍历，利用jQuery对表格进行遍历十分简单。

示例代码如下:

```javascript
// 首先需要通过ID获取表格元素
// 通过添加 tr 标签对表格的每一行进行遍历
// 不需要对表头进行遍历，因此使用gt(0)，就可以跳过第0行
// 利用each()函数进行遍历
// 向each参数中写入一个function用来对每一行进行处理
// function的参数 "i" 是each循环的次数，从0开始，不需要此参数也可以去掉
$("#lessonTable tr:gt(0)").each(function(i){
  	// 此时each 函数每次传入一个 <tr> 节点
  	// 需要对单元格进行操作，就需要获取该 <tr> 节点下的所有 <td> 子节点
  	// 通过 children("td") 获取该 <tr> 节点下的所有 <td> 子节点
	var row =  $(this).children("td");
  	// 通过 eq() 方法从 row 中获取单元格元素，eq() 返回对应下标下的节点，从 0 开始计数
  	// 通过text() 方法获取该单元格的内容
	var lessonNames = row.eq(0).text();
	var lessonCodes = row.eq(1).text();
	var lessonCredits = row.eq(3).text();
	var lessonExam = row.eq(6).text();
	var lessonSemester = row.eq(5).text();
});
```



##### 1.2 Add New Row to Table

当HTML页面中已经有了表格元素，并且需要向表格中添加新的一行时，需要使用 JS 动态添加。

步骤：

1. 获取表格元素（例如通过表格的ID）
2. 获取新的表格行所要填充的数据
3. 使用元素的append方法为表格添加新的表格行

示例代码如下：

###### 创建不可编辑的表格

```javascript
// 通过jQuery获取ID为"lessonTable"的表格元素
// 将填充数据传入createNewRow方法，获取表格行DOM元素
// 调用append方法添加DOM元素
$("#lessonTable").append(createNewRow(ol.lessonName,ol.lessonCode,ol.lessonCredit,ol.lessonCreditHours,ol.lessonSemester,ol.lessonExamine,ol.lessonRemark));

// 根据填充数据创建表格行DOM元素，获取表格行DOM元素
function createNewRow(olName,olCode,olCredit,olHours,olSemester,olExamine,olRemark){
	var str="<tr><td>"+olName +"</td><td>" + olCode + "</td><td>"
	+ olCredit + "</td><td>" + olHours + "</td><td>" + olSemester+ " " +
	"</td><td>" + olExamine + "</td><td>" + olRemark + "</td></tr>";
	return str;
}
```

###### 创建可编辑的表格,设置单元格的contenteditable属性为true

```javascript
function createNewRow(lName,lCode,lType){
	var str="<tr><td contenteditable='true'>"+lName +"</td><td contenteditable='true'>"
    + lCode + "</td><td contenteditable='true'>"+ lType + "</td></tr>";
	return str;
}
```

##### 1.3 Delete a Row in Table

有时候需要用户点击表格某一行实现删除该行操作，但是如果允许用户编辑该表格，鼠标事件就会冲突，不能分辨用户的编辑操作和删除操作。

因此，实现的具体思想是，为每一单元行添加一个不可编辑的删除操作单元格，此单元格被点击后调用删除函数从表格中删除该表格行，这些基本上需要从创建该表格行时就实现。

示例代码如下：

```javascript
// 在表格行末尾添加了一个单元格，显示"删除"
// 被点击时调用 deleteRow(obj) 方法
function createNewRow(lName,lCode,lType){
	var str="<tr><td contenteditable='true'>"+lName +"</td><td contenteditable='true'>"
    + 	lCode + "</td><td contenteditable='true'>"+ lType 
    + "</td><td class='delete' onclick='deleteRow(this)'>删除</td></tr>";
	return str;
}

// 表格被点击时调用此方法
// 传入的 obj 参数是该单元格元素
// 通过 bj.parentElement 方法获取它的父节点 <tr>
// 获取它的父节点 <tr> 的.rowIndex 属性
function deleteRow(obj){
	let row = obj.parentElement.rowIndex;
  	// 获取表格元素
	var lessonTable = document.getElementById("lessonTable");
  	// 通过传入 rowIndex 删除该表格行
	lessonTable.deleteRow(row);
}
```