---
title: tmux使用小结
categories:
- Linux学习
tags:
- tmux
---
tmux是可以在一个终端窗口运行多个终端会话的软件，并且各个窗口之间可以灵活新建、切换、关闭等，同时tmux支持挂起，退出后任务仍旧可以继续运行，因此要养成在tmux中操作的好习惯。

> 内容仅做个人笔记，方便快速查询  

**tmux功能**
1. 分屏
2. 允许断开Terminal连接后，继续运行进程  

**tmux结构**
- 一个tmux可以包含多个session，一个session可以包含多个window，一个window可以包含多个pane。
- 实例：
    - tmux：
        - session 0：
            - window 0：
                - pane 0
                - pane 1
                - pane 2
                - ...
            - window 1
            - window 2
            - ...
        - session 1
        - session 2
        - ...

**tmux操作**

> 注: 以下提到的ctrl + a操作基于使用yxc的传家宝三件套配置文件而言，正常tmux对应操作是ctrl + b，鼠标功能也是添加传家宝三件套后支持。
1. tmux: 新建一个session，其中包含一个window，window中包含一个pane，pane中打开了一个shell对话框。
2. 按下ctrl + a后手指松开，然后按%：可以将当前pane左右平分成两个pane。
3. 按下ctrl + a后手指松开，然后按"(半角双引号)：将当前pane上下平分成两个pane。
4. ctrl + d：关闭当前pane；如果当前window的所有pane均已关闭，则自动关闭window；如果当前session的所有windown均已关闭，则自动关闭session。
5. 鼠标点击可以选pane。
6. 按下ctrl + a后手指松开，然后按方向键：可以选择相邻的pane。
7. 鼠标拖动pane之间的分割线，可以调整分割线的位置。
8. 按住ctrl + a的同时按方向键，可以调整pane之间的分割线的位置。
9. 按下ctrl + a后手指松开，然后按d：挂起当前session。
10. tmux a： 打开之前挂起的session。
12. 按下ctrl + a后手指松开，然后按s：选择其它session。  
    - 方向键 上：选择上一项 session/window/pane
    - 方向键 下：选择下一项 session/window/pane
    - 方向键 右：展开当前项 session/window
    - 方向键 左：闭合当前项 session/window
13. 按下ctrl + a后手指松开，然后按c：在当前session中创建一个新的window。
14. 按下ctrl + a后手指松开，然后按w：选择其它window，操作方法同12
15. 按下ctrl + a后手指松开，然后按pageup:翻阅当前pane内的内容。
16. 鼠标滚轮：翻阅当前pane内的内容。
17. 在tmux中选中文本时，需要按住shift键。（仅支持windows和linux，不支持mac）
18. tmux中复制/粘贴文本的通用方式：
    - 按下ctrl + a后松开手指，然后按[
    - 用鼠标选中文本，被选中的文本会准备自动复制到tmux的剪切板
    - 按下ctrl + a后松开手指，然后按]，会将剪贴板中的内容粘贴到光标处