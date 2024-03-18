* 一份不太简短的LaTeX介绍：https://github.com/CTeX-org/lshort-zh-cn
* 所有操作均以反斜杠开头
* begin和end之间属于一个操作环境，一个环境内的文字格式相同

```latex
\documentclass [UTF8]{ctexart}   #定义文件类型为 中英文混合

\title{标题}  #begin前均为前言
\anthot{作者}
\data{\today日期}

\begin{document}  #文章正文开始，即显示出的内容
\maketitle  #导入前言


操作


\end{document}  #文章结束
```

* 文字操作

```latex
\textbf{加粗}
\textit{斜体}
\underline{下划线}
1个换行符   ---一个空格
2个换行符   ---另起一段
```

* 章节操作

```latex
\part{第1部分}
\chapter{第一章}
\section{1.1}
\subsection{1.1.1}
\subsubsection{1，1，1，1}
```

* 图片操作

```latex
\usepackage{praphicx}  #前言中调用图片包

\begin{figure}  #将图片放入一个环境中调整
\centering      #图片居中显示
\includegraphics[wide=0.5\textwidth]{name} #参数调整大小，name为导入的图片名称
\caption{图片标题}
\end{figure}

```

* 列表操作

```latex
#无序列表
\begin{itemize}
\item 列表项1    #每一个列表项以item开头
\item 列表项2
\end{itemize}

#有序列表
\begin{enumerate}
\item 列表项1    #每一个列表项以item开头
\item 列表项2
\end{enumerate}
```

* 公式操作

```latex
#行内公式，$....$，美元符之间
这是一个公式:$a=b+c$

#单独一行
\begin{eqution}
a=b+c
\end{eqution}

#简写
\[
a=b+c
\]

#复杂公式从latex.91maths.com/获取复制
```

* 表格操作

```latex
\begin{table}
\center                   #将表格居中显示
\begin{tabular}{c|c|c}    #c居中 r靠右 l靠左 p定义列宽 |显示分割线
\hline                    #\hline  显示行线
单元格1 & 单元格2 & 单元格3  #每个单元格用&隔开
\hline
单元格1 & 单元格2 & 单元格3
\hline\hline              #显示双横线
单元格1 & 单元格2 & 单元格3
\hline
\caption{表格标题}         #表格标题
\end{tabular}
```





`2023-3-18测试github....`

![github图片](https://github.com/ZhangYuQiao326/study-nodes/blob/main/picture/image.png?raw=true)

![image-20240318094819830](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240318094819830.png)

![image-20240318094801232](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240318094801232.png)



测试图片资源2

![资源](https://github.com/ZhangYuQiao326/study_nodes_pictures/blob/main/image.png?raw=true)

测试type+picgo+github

![image-20240318103508498](https://cdn.jsdelivr.net/gh/ZhangYuQiao326/study_nodes_pictures@main/img/image-20240318103508498.png)

ss

测试 picgo2

![image-20240318104503506](https://cdn.jsdelivr.net/gh/ZhangYuQiao326/study_nodes_pictures@main/img/image-20240318104503506.png)
