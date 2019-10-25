---
title: Java_Jxl
date: 2017-03-18 00:32:35
tags: [Java,JXl]
categories: Server
---


## Java-Jxl



### 0. Summary

Jxl是一个开源的Java Excel API项目，可以通过Java语言对 Microsoft Excel 进行操作。除了Jxl之外，还有Apache的一个POI项目，也可以操作Excel，Jxl较为轻量，上手简单，功能不够POI那样丰富，但是已经可以满足基本需求了。

利用 JXL ，可以从现有的Excel文件中读入数据，对这些数据进行操作，比如存入数据库，方便进行增删改查，也可以将数据导出到 Excel文件中，例如将数据库中的数据进行统计并生成统计表，而且 JXL 还支持单元格的合并、文字排版等多种操作。



### 1. Prepare

[点击下载 Jxl.jar](https://sourceforge.net/projects/jxl/)

下载后导入到项目中就可以开始使用了

[点击查看 Jxl 官方文档](http://jxl.sourceforge.net/javadoc/index.html)

### 2. Basic Usage

##### 2.1 Create a Workbook（工作簿）

要使用 Jxl 对Excel进行操作，首先需要创建一个Workbook实例，这个实例通过文件路径对应一个Excel文件。

###### 2.1.1 如果我们需要从现有的Excel文件中读取数据，需要使用该文件的路径创建一个Workbook

示例代码如下：

```java
filepath:文件路径，String 类型

//通过文件路径生成一个Inputstream对象
//根据这个对象,通过Workbook单例模式（这一点是本人猜测的）的getWorkbook方法构建Workbook实例
//这个Workbook实例是只读的，不能对源文件作出修改
InputStream inputStream = new FileInputStream(filePath);
Workbook workbook = Workbook.getWorkbook(inputStream);
```

###### 2.1.2 如果我们需要新建一个Excel文件并导出数据，需要创建一个新文件，并使用这个新文件的文件路径创建一个Workbook

```java
filePath和fileName皆是String类型
// 通过新建文件并将这个文件实例传入createWorkbook方法构建workbook实例
Workbook workbook = Workbook.createWorkbook(new File(filePath + fileName + ".xls"));
```



##### 2.2 Operate a sheet

**读取数据模式：**

###### 2.2.1 Get a sheet

一个Excel文件中或许有多个sheet（工作表）

*通过*Workbook.getSheets()*方法我们可以获取到这个Excel文件中的所有sheet*

*通过*Workbook.getSheet(int)*方法我们可以获取到这个Excel文件中的某一个sheet*

```java
// 通过Workbook的getSheets()方法获取Excel文件中所有sheet
Sheet[] sheets = workbook.getSheets();
// 通过Workbook的getSheet()方法获取Excel文件中第一张sheet
Sheet sheet = workbook.getSheet(0);
```

###### 2.2.2 Operate the sheet

一个sheet的基本单位为cell（单元格）。

我们可以通过坐标访问每一个cell，并且获取这个cell的数据类型，以及所填充的数据。

要确定坐标我们首先需要知道这个sheet有多少 row（行），多少column（列）

示例代码如下：

```java
readSheet：Sheet 类型
  
//获取Sheet表中所包含的总列数
int rsColumns = readSheet.getColumns();
//获取Sheet表中所包含的总行数
int rsRows = readSheet.getRows();

//获取sheet中某一行中的所有sheet
// 传入1， 获取该row的所有cell
// 参数从 0 开始 ！！！
Cell[] row = readSheet.getRow(1);

// 通过坐标获取某个cell
// 传入参数(0，2) 代表获取第0列，第2行位置的单元格
// 参数的顺序一定不要弄错， 先是列，后是行
 Cell cell = readSheet.getCell(0,2);

// 获取cell对应的数据
// getContents()方法会无视单元格的数据格式，皆以String类型返回所填充的内容
String content = cell.getContents();
```

如果要想保留数据格式或类型，可以使用 getType() 方法获取数据类型
根据返回的数据类型做判断后调用相应的方法获取保持源数据类型的内容
示例代码如下：

String类型

```
if(cell.getType() == CellType.LABEL){  
	LabelCell labelCell = (LabelCell) cell;  
	String str = labelCell.getString();  
}  
```

Number类型

```
if(cell.getType() == CellType.NUMBER){  
	NmberCell numCell = (NumberCell) cell;  
	double number = numCell.getValue();  
} 
```

 Date类型

```
if(cell.getType() == CellType.DATE){  
	DateCell datecell = (DateCell) ce11;  
	Date date = datece11.getDate();  
}  
```

获取到cell的内容后的操作就因人而异了



**写入数据模式**

###### 2.2.3 Create a sheet

用WritableWorkbook对象的createSheet方法创建Sheet

```java
//createSheet("sheetNameString", sheetIndex);
//此方法有两个参数：
//sheetNameString是Sheet的名字
//sheetIndex是Sheet数组的下标，下标从0开始，表示第一个Sheet

WritableSheet writableSheet = workbook.createSheet(sheetName,0);
```

###### 2.2.4 operate the sheet

我们通过创建一个 cell ，并且设置 cell 的格式，位置，数据等，来填充 sheet

示例代码如下：

```java
// 我们可以创建一个文字格式用于单元格
// Title-Center-Format
WritableCellFormat titleFormat;
titleFormat = new WritableCellFormat(TitleFont);
titleFormat.setBorder(Border.ALL, BorderLineStyle.THIN); // 边框
titleFormat.setVerticalAlignment(VerticalAlignment.CENTRE); // 文字垂直对齐
titleFormat.setAlignment(Alignment.CENTRE); // 文字水平对齐
titleFormat.setWrap(false); // 文字是否换行


// 创建一个label用作sheet的标题，并且合并单元格
// new Label()有四个参数
// 前两个参数是Label在sheet中的坐标，仍旧是先列后行，从0开始
// 第三个参数是Label中所要填充的数据
// 第四个参数是Label所要使用的格式
sheetTitle:Sting 类型
Label sheetTitleLabel = new Label(0,0,sheetTitle,titleFormat);
// 将该Label填充到sheet中
writableSheet.addCell(sheetTitleLabel);
```

还可以使用sheet的mergeCells()方法进行单元格合并等操作

示例代码如下：

```java
// mergeCells()有四个参数
// 前两个参数是合并区域左上角cell的坐标，仍旧是先列后行，从0开始
// 后两个参数是合并区域右下角cell的坐标，仍旧是先列后行，从0开始

numOfColumn ： int类型
writableSheet.mergeCells(0,0,numOfColumn - 1,0);
```

还可以设置其他类型的Label，并填充，更详细的用法可查见官方文档。（说不定哪天也会更新）



### 3. Final

当我们填充完sheet后需要执行write() 方法保留这些数据

最终完成我们对sheet的操作后，我们需要关闭workbook，释放内存

示例代码如下：

```java
// 写入Excel工作表
workbook.write();
// 关闭Excel工作薄对象
workbook.close();
```

