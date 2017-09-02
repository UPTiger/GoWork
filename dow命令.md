del F:\_desktop.ini /f /s /q /a （F代表你要操作的盘符，如果是C盘就把F改成C）
强制删除F盘下所有目录内（包括X盘本身）的_desktop.ini文件并且不提示是否删除。
/f 表示强制删除文件
/s表示子目录都要删除该文件
/q表示无声，不提示
/a根据属性选择要删除的文件
R 只读文件 S 系统文件
H 隐藏文件 A 存档文件
- 表示“否”的前缀

ren *.abc *.

@echo off
del *.java /f /s /q /a
for /f "delims=" %%i in ('dir /a-d /b /s \*.java.txt') do (
ren "%%i" "%%~ni" )
pause