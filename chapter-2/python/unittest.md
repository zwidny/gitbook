# Python 单元测试技术分享

## 故事背景

小白, 小黑, 小黄三人负责两个产品(A，B)的开发， 小白负责公共模块的开发， 其提供的模块
会交给小黑和小黄，小黑和小黄在其基础上继续开发形成产品A和产品B
<img alt="image" height="300" src="../../images/unittest-a.png" width="400"/>


刚开始的时候一切都进展顺利， 忽然某一天小白发现其模块里面的输出结果精度可以更精确， 之前是
74.5 精确到小数点后一位， 现在可以精确到小数点后两位即74.49了。小白觉得改动很小，而且
是提升了准确性，于是询问了小黑后，发现对其业务没有影响后，想着小黄应该也差不多， 便直接上线了。
上线后发现也没有任何明显报错， 于是这件事便平静的过去了。


某天， 产品经理在统计数据时发现小黄负责的B业务的最近投诉相比往期高出了15个百分点，这个很不正常，
由于小黄本人最近未上线新代码， 所以也不知道是啥问题。 于是便和产品经理坐下来分析数据， 结果发现
投诉的用户大部分是说产品耍猴， 同样的数据之前给的这个结论，现在给的是另外一个结论。 进一步调查发现
投诉的用户基本上处于74.4这个水平，之前他们普遍的得分普遍为74.5, 而74.5是一个用户划分的分界线，
此结论会指导用户购买不同的商品，不同结论的商品价格差距很大, 因此导致大量用的户投诉。


## 避免
从以上案例我们可以看到， 问题其实还不少。 接下来我们将主要从增加测试用例的角度来说明如何避免问题的产生，
或者当问题产生时，能够及时的发现。


时间回到小黄开发产品B时， 由于小白的模块对小黄的用户划分有十分重要的影响， 一旦小白模块发生改变，势必对
小黄的业务会产生影响， 为了使影响可控或者说早发现，小黄决定在开发产品B时增加相关的分界线的测试用例: 对于确定的
输入， 小白的模块应该给出74.5的结果， 如果给出的结果非74.5则说明产生了非预期的问题， 需要商量一下如何解决了

## 技术实现
测试的意义上面已经概述过了， 接下来将主要介绍如何在开发阶段写好单元测试。(以python语言为主)

### 基本单元测试

#### 背景介绍

由于需要验证文件的完整性问题， 我们需要提供一个可以计算文件MD5的函数。

#### 代码实现
目录结构
```text
utils
├── __init__.py
└── misc.py          # MD5函数实现文件

```

`misc.py`内容如下
```python
import typing
import hashlib


def get_file_md5sum(file: typing.IO)->str:
    """计算文件的md5值"""
    if not file:
        raise ValueError("File must be file object.")
    file.seek(0)
    file_hash = hashlib.md5()
    while chunk := file.read(8192):
        if isinstance(chunk, str):
            chunk = chunk.encode('utf8')
        file_hash.update(chunk)
    file.seek(0)
    return file_hash.hexdigest()

```
#### 单元测试

##### 1. 创建测试文件  

为了进行单元测试， 我们需要创建一个测试文件， 以`test_`开头， 目录结果如下  
```text
   utils
   ├── __init__.py
   ├── misc.py
   └── test_misc.py
```

##### 2. 增加测试用例

```python
import os
from unittest import TestCase

from .misc import get_file_md5sum

DIR = os.path.dirname(os.path.abspath(__file__))
IMG_PATH = os.path.join(DIR, 'misc.py')


class MiscTest(TestCase):

    def test_get_file_md5sum(self):
        md5 = 'c5eabf65e9fbc19bed582e681e1d38de'
        with open(IMG_PATH, 'rb') as f:
            md5_sum = get_file_md5sum(f)

        self.assertEqual(md5, md5_sum)

```

##### 3. 运行测试用例  

+ 运行所有测试用例

    ```text
    python -m unittest
    ```

+ 运行指定测试用例  

    ```text
    python -m unittest utils/test_misc.py
    ```

如果运行正确， 应该输出如下信息

```text
.
----------------------------------------------------------------------
Ran 1 test in 0.001s

OK
```

### 包含三方服务的单元测试

#### 背景介绍

现在接到需求，我们要实现眼底的左右眼识别。 具体流程如下， 首先接收图片， 然后调用三方服务，判断是否为眼底图像，
如果非眼底图像， 程序终止执行， 返回-1， 如果是眼底图像判断其左右眼.

#### 代码实现
目录结构
```text
utils
├── __init__.py
├── misc.py          # MD5函数实现文件
└── guess_eye_location.py    # 判断左右眼
```

`guess_eye_location.py` 内容如下
```python
import requests

def is_fundus_image(file_path: str) -> bool:
    """调用三方服务，判断文件是否是眼底图像"""
    files = {'file': open(file_path, 'rb')}
    resp = requests.post(url='https://api.xxx.com', files=files)
    return resp.json()


def guess_eye_type(file_path: str) -> int:
    """判断眼睛类型
    return(int):
      -1: 非眼底图片
      0 : 右眼
      1 : 左眼
    """
    # 判断是否是眼底照片
    is_fundus = is_fundus_image(file_path)
    if not is_fundus:
      return -1
    # 判读左右眼
    return 0
      
```

#### 单元测试

对于三方服务的情况下， 我们需要使用mock来模拟应答。具体如下

##### 1. 创建测试文件
```text
   utils
   ├── __init__.py
   ├── misc.py
   ├── guess_eye_location.py
   ├── test_guess_eye_location.py
   └── test_misc.py
```

##### 2. 增加包含mock的测试用例
`test_guess_eye_location.py`

```python
from unittest import TestCase
from unittest.mock import patch

from .guess_eye_location import guess_eye_type


class MockResponse(object):
    status_code = 200

    def json(self):
        """"""
        return 0


class GuessEyeTest(TestCase):

    @patch('requests.post', return_value=MockResponse())
    def test_send_text(self, x):
        file_path = __file__ 
        result = guess_eye_type(file_path)
        self.assertEqual(result, -1)

```
##### 3. 运行测试用例  
+ 运行指定测试用例

    ```text
    python -m unittest utils/test_guess_eye_location.py
    ```

如果运行正确， 应该输出如下信息

```text
.
----------------------------------------------------------------------
Ran 1 test in 0.001s

OK
```
