---
title: Java_Web_File
date: 2017-04-04 20:57:47
tags: [Java,Web,File,Upload,Download]
categories: Server
---

## File_Upload_Download

#### 0. Forward

​	本篇记录如何在 Java Web 中实现简单的文件的上传和下载功能。



#### 1. Prepare

​	实现文件上传功能，我们一般要用到开源库：[Commons FileUpload](http://commons.apache.org/proper/commons-fileupload/)，之后简称FileUpload

​	而此开源库又依赖于：[ Commons IO](http://commons.apache.org/proper/commons-fileupload/dependencies.html)，

​	因此需要将两个库都添加到我们的项目中。



#### 2. Upload

##### 2.0 Intro

首先，我们需要了解一下一个协议--[RFC 1867](http://www.ietf.org/rfc/rfc1867.txt)。

根据官网描述：

> RFC 1867             Form-based File Upload in HTML        November 1995
>
>  This proposal makes two changes to HTML:
>
>    1) Add a FILE option for the TYPE attribute of INPUT.
>    2) Allow an ACCEPT attribute for INPUT tag, which is a list of  media types or type patterns allowed for the input.
>
>    In addition, it defines a new MIME media type, multipart/form-data,
>    and specifies the behavior of HTML user agents when interpreting a
>    form with ENCTYPE="multipart/form-data" and/or``` <INPUT type="file">```
>    tags.
>
>    These changes might be considered independently, but are all
>    necessary for reasonable file upload.

  其实很简单，[RFC 1867](http://www.ietf.org/rfc/rfc1867.txt) 协议，向 HTML 中添加了 File 控件，也即是我们经常用到的```<INPUT TYPE="file">```控件，允许用户将多种格式的文件包含在表单数据中并提交，并为这种表单数据类型定义了一种编码格式：```multipart/form-data```。

顺便总结一下表单的**ENCTYPE**属性:

|             ENCTYPE值              |                   作用描述                   |
| :-------------------------------: | :--------------------------------------: |
| application/x-www-form-urlencoded |      在发送前编码所有字符（默认），将表单数据编码为键值对的形式       |
|        multipart/form-data        | 整个表单数据被编码为一个整体，每一个Input标签以及对应的数据对应为一部分，浏览器会使用Boundary（各个浏览器可能有所不同）将每个Input数据分割开来。在使用包含文件上传控件的表单时，必须使用此编码格式。 |
|            text/plain             |         空格转换为 "+" 加号，但不对特殊字符编码。          |



**一个基本的```multipart/form-data```编码类型表单：**

```html
<FORM ENCTYPE="multipart/form-data" ACTION="_URL_" METHOD=POST>
File to process: <INPUT NAME="userfile1" TYPE="file">
<INPUT TYPE="submit" VALUE="Send File">
</FORM>
```

而我们使用的开源库：[Commons FileUpload](http://commons.apache.org/proper/commons-fileupload/)就是通过解析遵从[RFC 1867](http://www.ietf.org/rfc/rfc1867.txt)协议的表单数据，从而获取到提交的文件数据，并进行保存。

##### 2.1 Usage

###### Principle

[Commons FileUpload](http://commons.apache.org/proper/commons-fileupload/)的机制的官方介绍：

>A file upload request comprises an ordered list of *items* that are encoded according to [RFC 1867](http://www.ietf.org/rfc/rfc1867.txt), "Form-based File Upload in HTML". FileUpload can parse such a request and provide your application with a list of the individual uploaded items. Each such item implements the `FileItem` interface, regardless of its underlying implementation.
>
>Each file item has a number of properties that might be of interest for your application. For example, every item has a name and a content type, and can provide an `InputStream` to access its data. On the other hand, you may need to process items differently, depending upon whether the item is a regular form field - that is, the data came from an ordinary text box or similar HTML field - or an uploaded file. The`FileItem` interface provides the methods to make such a determination, and to access the data in the most appropriate manner.
>
>FileUpload creates new file items using a `FileItemFactory`. This is what gives FileUpload most of its flexibility. The factory has ultimate control over how each item is created. The factory implementation that currently ships with FileUpload stores the item's data in memory or on disk, depending on the size of the item (i.e. bytes of data). However, this behavior can be customized to suit your application.

**个人理解：**

​	一个文件上传请求由一串遵从[RFC 1867](http://www.ietf.org/rfc/rfc1867.txt)协议要求的编码格式的数据项组成。**FileUpload**能够解析文件上传请求，并且将每一个数据项分离为单独的实现了```FileItem```接口的对象，交由开发者进行针对性的处理。

​	每一个```FileItem```对象都有一个名称和数据类，我们可以通过`InputStream`对象来获取每个```FileItem```中的数据。我们也可以判断每个```FileItem```是何种类型，是普通的表单标签还是文件标签，以此进行针对性的处理。																			

​	FileUpload通过`FileItemFactory`对象来控制并创建```FileItem```。简单点说，FileItemFactory是一个存储文件上传表单数据的缓冲区，根据表单数据的大小，我们可以有选择的将数据存放在内存还是磁盘中。

###### Implement

了解了原理后，通过具体代码来看：

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        // Set Request and Response
        request.setCharacterEncoding("utf-8");
        response.setCharacterEncoding("utf-8");
        response.setContentType("text/JavaScript");
        response.setHeader("Access-Control-Allow-Origin","*");

  		// 首先，获取我们想要将文件存放的路径，此处选择的是工程目录WEB-INF文件夹下的upload文件夹
        String savePath = this.getServletContext().getRealPath("/WEB-INF/upload");
        // 根据存放路径创建文件，从而进行判断
  		File file = new File(savePath);
  		// 如果文件不存在，说明不存在文件夹 upload ，新建该文件夹并重新获取路径。
        if(!file.exists()){
            System.out.println("savePath+\"目录不存在，需要创建\"");
            file.mkdir();// 创建文件夹upload
            savePath = this.getServletContext().getRealPath("/WEB-INF/upload");
        }

  		// 开始进行上传文件的处理
        try{
          
          	// 0. 先创建一个FileItemFactory实例
            DiskFileItemFactory factory = new DiskFileItemFactory();
          	// 1. 根据Factory实例，创建文件上传表单请求解析器ServletFileUpload
            ServletFileUpload upload = new ServletFileUpload(factory);
          	// 2. 根据解析器解析文件上传请求，得到解析后的FileItem集合
          	List<FileItem> list = upload.parseRequest(request);
          	// 3. 对FileItem集合进行遍历，逐一处理
            for (FileItem item: list) {
                if(item.isFormField()){
                  	// 4. 如果该FileItem是普通表单标签，则只获取键值即可，并进行需要的处理
                  	// 此处没有做处理，只是提取出了数据
                    String name = item.getFieldName();
                    String value = item.getString("UTF-8");
                    System.out.println(name + "=" + value);
                }else {
                  	// 5. 判断为文件上传标签，进行文件处理
                    String fileName = item.getName();// 获取文件全路径名称
                    System.out.println("FileName:" + fileName);
                    if(fileName == null || fileName.trim().equals("")){
                        continue;
                    }// 文件有效性判断，可忽略
					
                  	// 6. 获取的文件的名称
                  	// 例如，全路径名称可能为"User/Jaho/Info.md"
                  	// 通过字符串截取操作，获得文件名 "Info.md"
                    fileName = fileName.substring(fileName.lastIndexOf("/") + 1);
                  	// 7. 获取FileItem的InputStream实例，以此来访问FileItem数据
                    InputStream in = item.getInputStream();
                  	// 8. 创建文件输出流，将FileItem数据保存为文件
                    FileOutputStream outputStream = new FileOutputStream(savePath + "/" + fileName);			
                  	// 9. 为文件输出流创建一个缓冲区，每此写1K的数据
                    byte buffer[] = new byte[1024];
                    int len = 0;
                  	// 10. 循环读取数据并写入。
                    while ((len = in.read(buffer)) > 0){
                        outputStream.write(buffer,0,len);
                    }
                  	
                  	// 11. 最后，关闭文件输出、输入流
                    in.close();
                    outputStream.close();
                }
            }
          // 一些错误的处理
        }catch (FileUploadBase.FileSizeLimitExceededException e) {
            e.printStackTrace();
            PrintWriter out = response.getWriter();
            out.print("单个文件超出最大值！！！");
            return;
        }catch (FileUploadBase.SizeLimitExceededException e) {
            e.printStackTrace();
            PrintWriter out = response.getWriter();
            out.print("上传文件的总的大小超出限制的最大值！！！");
            return;
        }catch (Exception e) {
            PrintWriter out = response.getWriter();
            out.print("文件上传失败！");
            e.printStackTrace();
        }
    }
```



#### 3. Download

##### 3.0 Intro

​	文件下载较为简单。

​	最简单的方法，将下载文件的URL以超链接方式展示在浏览器，点击即可下载。然而，这种情况下，如果下载文件格式浏览器可解析，浏览器就会进行打开操作，而不是下载操作。

​	因此我们需要使用 Servlet 来进行正常的文件下载操作。

​	思路：通知浏览器进行文件下载操作，并使用数据输出流想浏览器发送数据。

##### 3.1 Implement

通过代码来看：

**后台：**

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
  
        // Set Request and Response
        request.setCharacterEncoding("utf-8");
        response.setCharacterEncoding("utf-8");
        response.setHeader("Access-Control-Allow-Origin","*");

  		// 首先，获取我们想要将提取文件的路径，此处选择的是工程目录WEB-INF文件夹下的download文件夹
        String filePath = this.getServletContext().getRealPath("/WEB-INF/download");
        // 获取需要下载的文件名称(当然，也可以自己进行指定)
  		String fileName = request.getParameter("fileName");
  		// 拼接文件的全路径
        String fullFilePath = filePath + "/" + fileName;
  		// 0. 设置返回的数据类型(文件)
        response.setContentType(getServletContext().getMimeType(fileName));
  		// 1. 通过此条语句，通知浏览器进行文件下载操作
        response.setHeader("Content-Disposition", "attachment;filename="+new String(fileName.getBytes("utf-8"), "ISO-8859-1"));// 为避免中文名乱码，使用UTF-8编码文件名
        
  		// 2. 根据需要下载的文件获取数据输入流，从而读取文件数据
  		FileInputStream in = new FileInputStream(fullFilePath);
  		// 3. 创建数据输出流，将数据传输给浏览器
        OutputStream outputStream = response.getOutputStream();
  		// 4. 创建缓冲区，大小为 1K
        byte buffer[] = new byte[1024];
        int len = 0;
  		// 5. 循环，将数据传输给浏览器
        while ((len = in.read(buffer)) > 0){
            outputStream.write(buffer,0,len);
        }

  		// 6. 关闭数据输出输入流
        in.close();
        outputStream.close();
    }
```

**前台**，使用超链接，链接地址为Servlet的地址。点击该超链接，浏览器即可开始文件下载操作。





