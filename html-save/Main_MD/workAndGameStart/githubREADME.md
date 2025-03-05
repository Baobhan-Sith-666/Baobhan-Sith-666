# 项目名称：抽象启动器

> 这是两个业余程序员在摸鱼时间做的小软件。使用Qt和PYthon制作。

## 关于项目

这就是一个启动器，里面包装了“画图”以及“游戏”

## git方法（非常重要）

1. 首先你需要到 **百度网盘** 中下载core文件文件夹：core_PSMEE-2nd.zip
  - 链接: https://pan.baidu.com/s/1O2cjpyVZXn84qMEbl2_1mA?pwd=tzac 提取码: tzac
 
2. 然后git clone项目。
- 主体框架使用的是QT，我使用的是 **MINGW** 编译器，python使用的版本的 **3.13.1**

3. 将文件主题放在你喜欢的一个文件夹中，记得是英文（QT对中文的支持很差）
  -  我使用的是 QT 6.8.2 版本
  -  先创建一个空的工程，然后自己debug和realse一下，让它自己创建出来 **build** 文件夹
  -  然后在 **build** 文件夹中进入 **debug** 然后再进入里面的 **debug**
  -  将 **core** 文件夹放在这个文件夹中。
  -  最后将你的C++文件放进你的工程项目，然后自己替换一下UI文件。
  -  在重新构建之前，先删掉你的工程目录底下所有的 **ui_xxx.h** 文件。
  -  在QT中选择 “清除” - “qmake” - “重新构建”。等到完成。
4. 我的工程目录：
  ![image](https://i.postimg.cc/52RDkd8J/1-1.png)
  ![image](https://i.postimg.cc/mDxJBYd6/1-2.png)
  ![image](https://i.postimg.cc/pd54wGBF/1-3.png)
  ![image](https://i.postimg.cc/C57QGWwW/1-4.png)
  ![image](https://i.postimg.cc/QxNP8xbs/1-5.png)
- 我们使用的是打开外部的 **exe** 来启动程序。
![image](https://i.postimg.cc/rsMHt821/1-6.png)
  - 在 **source** 文件夹中 我们放置了源文件
![image](https://i.postimg.cc/qBxSHC2j/1-7.png)

5. 内部快照：
   ![屏幕截图 2025-03-05 174113](https://i.postimg.cc/4dTLrJdD/1-8.png)
   ![屏幕截图 2025-03-05 174200](https://i.postimg.cc/Z5CMN72M/1-9.png)
   ![屏幕截图 2025-03-05 174211](https://i.postimg.cc/g2QBHyPk/1-10.png)
   ![image](https://i.postimg.cc/MTbPwhfv/1-11.png)
   ![image](https://i.postimg.cc/Znbf5V6j/1-12.png)
   ![image](https://i.postimg.cc/T1ttLg9x/1-13.png)
   ...










