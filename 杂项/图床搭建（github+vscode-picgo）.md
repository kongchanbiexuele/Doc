# 图床搭建说明

> creatTime： 2024/6/4
> updateTime: 2024/6/4
> author:     shizx

## 1. 工具准备

- vscode: [vscode官网下载连接](https://code.visualstudio.com/Download)
- picgo插件：[vscode插件安装教程](https://blog.csdn.net/m0_51160509/article/details/135354673)
- github: [github官网地址](https://github.com/)
- jsDelivr: []

## 2. 配置

### 2.1. GITHUB配置

1. 创建github仓库

    - 填写 Repository name: 仓库名称
    - 填写 Description：仓库描述
    - 选择 Public/Private：所有人可见/自己可见
    - 跳过 其他项：... 自己gpt一下
    - 点击 create repository

    ![image](https://cdn.jsdelivr.net/gh/kongchanbiexuele/Pic/Doc/image.png)

2. 初始化github仓库

    - 创建 本地创建和仓库同名文件夹
    - 确认 git环境搭建正常
    - 执行 github给出的初始化命令

    ![image-1](https://cdn.jsdelivr.net/gh/kongchanbiexuele/Pic/Doc/image-1.png)

3. 配置github仓库，生成token

    - 点击 右上头像 -> settings

        ![image-2](https://cdn.jsdelivr.net/gh/kongchanbiexuele/Pic/Doc/image-2.png)
    - 点击 左侧栏 -> 拉到最下 -> Developer Settings

        ![image-3](https://cdn.jsdelivr.net/gh/kongchanbiexuele/Pic/Doc/image-3.png)
    - 点击 左侧栏 -> Personal access tokens -> Tokens(classic)

        ![image-4](https://cdn.jsdelivr.net/gh/kongchanbiexuele/Pic/Doc/image-4.png)
    - 点击 Generate new token -> Generate new token(classic)
    
        ![image-5](https://cdn.jsdelivr.net/gh/kongchanbiexuele/Pic/Doc/image-5.png)

    - 填写 Note: token的用途
    - 选择 Expiration：token的过期时间
    - 选择 Select scopes：作用范围，这里选择repo就好

        ![image-6](https://cdn.jsdelivr.net/gh/kongchanbiexuele/Pic/Doc/image-6.png)
    - 点击 Create token，生成最终的token，记得及时保存，后面就看不到了

        ![image-7](https://cdn.jsdelivr.net/gh/kongchanbiexuele/Pic/Doc/image-7.png)

### 2.2. Picgo配置

1. vscode下载PicGo

    ![image-8](https://cdn.jsdelivr.net/gh/kongchanbiexuele/Pic/Doc/image-8.png)

2. 配置PicGo

    - 点击 左下角设置 -> 设置

    ![image-9](https://cdn.jsdelivr.net/gh/kongchanbiexuele/Pic/Doc/image-9.png)
    - 填写 picgo -> 如图设置

    ![image-10](https://cdn.jsdelivr.net/gh/kongchanbiexuele/Pic/Doc/image-10.png)
    ![image-11](https://cdn.jsdelivr.net/gh/kongchanbiexuele/Pic/Doc/image-11.png)

## 3. 使用PicGo插件

1. 快捷键

| 系统         | 从剪切板上传图片 | 从资源管理器上传图片 | 从输入框上传图片 |
|  ---         |            --- |                 --- |            --- |
| Windows/Unix | Ctrl + Alt + U |      Ctrl + Alt + E | Ctrl + Alt + O |
| MAC          | Cmd + Opt + U  |       Cmd + Opt + E |  Cmd + Opt + O |

2. 使用示例，WIN系统+从资源管理器上传图片

- 输入 Ctrl + Alt + E，弹出资源管理窗
- 选中 所有图片
- 点击 打开，文档界面生成对应的链接

    ![20240604140542](https://cdn.jsdelivr.net/gh/kongchanbiexuele/Pic/Doc/20240604140542.png)
    ![image-12](https://github.com/kongchanbiexuele/Pic/blob/main/Doc/image-12.png?raw=true)
