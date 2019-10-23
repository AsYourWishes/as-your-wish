# python相关问题记录

1. TypeError: 'NoneType' object is not callable

   None类型不是一个可以可以调用（callable）对象

   赋值语句错误，值为None

   callable对象是指一个后面可以加（）的对象，一般把调用函数时（）去掉

2. IDEA打开之前的python项目，不显示存在的包和模块，并且报以下错误：

   Cannot load settings from file 'C:\Users\Wyh\IdeaProjects\learnPython\learnPython.iml': File C:\Users\Wyh\IdeaProjects\learnPython\learnPython.iml does not exist Please correct the file content

   原因：丢失了learnPython.iml文件

   解决办法：删除IDEA项目列表中此项目，并重新导入项目

