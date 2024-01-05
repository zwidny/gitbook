# Celery In Practical

## 1. 运行
--app=module 参数执行逻辑如下
1. 寻找module中属性为app的变量
2. 寻找module中属性为celery的变量
3. 寻找module中变量为Celery Application的变量
4. 如果都没有找到， 在proj.celery中安装以上逻辑寻找

### 举例说明: 对于单文件的
1. celery app 
```python
# File: tasks.py
from celery import Celery

app = Celery('tasks', broker='pyamqp://guest@localhost//')

@app.task
def add(x, y):
    return x + y

```
2. 运行指令如下
```shell
celery -A tasks worker -l INFO
```
### 举例说明: 对于复杂情况
1. celery app
```python
# File: proj.celery_app.py
from celery import Celery

app = Celery('proj', broker='pyamqp://guest@localhost//')

@app.task
def add(x, y):
    return x + y

```
2. 运行指令如下
```shell
celery -A proj.celery_app worker -l INFO
```

