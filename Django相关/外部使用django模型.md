# 外部使用django模型



## 配置环境变量

```python
import sys
import django

sys.path.insert(0,'/xx/xx/xx')  # 将django工程的绝对路径写入环境变量,使能够引用django工程内的包
os.environ.setdefault('DJANGO_SETTINGS_MODULE','xxx.settings')  # 指定django环境，让django知道使用哪个配置文件
django.setup() 
```

