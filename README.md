# 用于删除项目中没有引用的图片
## 原理 利用`python`和`bash`命令, 遍历项目中的图片, 如果没有就执行`delete`
搜索图片
```
os.popen('echo $PATH')
```
是否引用
```
os.popen('echo $PATH').read()
```
删除
```
os.system('rm -rf dir')
```
需要忽略的项目中使用`for in`命名的图片(image1...等), 需要写一个正则来判别是否在`ignore`的列表内
```
ignores = {r'image*\d+'}
```
检查`ignores`
```
def is_ignore(str):
    for ignore in ignores:
        if re.match(ignore, str):
            return True
    return False
```
## 使用方法 在工程目录下执行下面命令
```
python un_used.py
```
## 完成代码
```
# coding=utf-8
import glob
import os
import re

path = 'ios'

ignores = {r'image_\d+'}
images = glob.glob('ios/images.xcassets/*/*.imageset')

def find_un_used():
    img_names = [os.path.basename(pic)[:-9] for pic in images]
    unused_imgs = []
    for i in range(0, len(images)):
        pic_name = img_names[i]
        if is_ignore(pic_name):
            continue
        command = 'ag "%s" %s' % (pic_name, path)
        result = os.popen(command).read()
        if result == '':
            unused_imgs.append(images[i])
            print 'remove %s' % (images[i])
            os.system('rm -rf %s' % (images[i]))
            
    text_path = 'unused.txt'
    tex = '\n'.join(sorted(unused_imgs))
    os.system('echo "%s" > %s' % (tex, text_path))
    print 'unuse res:%d' % (len(unused_imgs))
    print 'Done!'

def is_ignore(str):
    for ignore in ignores:
        if re.match(ignore, str):
            return True
    return False

if __name__ == '__main__':
find_un_used()

```
