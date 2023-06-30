[官方手册](https://django-filter.readthedocs.io/en/stable/ref/filterset.html)

模型

```python
class Tasks(models.Model):
    name = models.CharField(max_length=255, help_text="名称")
    age = models.IntegerField(null=True,blank=True, help_text='年龄')
```

过滤器【会被视图引用】
```python
class TasksFilter(django_filters.rest_framework.FilterSet):
  min_age = filters.NumberFilter(field_name="price", lookup_expr='gte')  # 过滤年龄大小
  max_age = filters.NumberFilter(field_name="price", lookup_expr='lte')
  class Meta:
    model = Tasks
    fields = '__all__'
```

视图
```python
class TasksViewSet(rest_framework.viewsets.ViewSet):
  queryset = Tasks.objects.all()
  serializer_class = xxx
  filter_backends = (filters.DjangoFilterBackend,)  # 这个有什么用没弄懂
  # ###############这里使用过滤器###############
  # 这俩二选一，不能同时存在
  filterset_class = TasksFilter 
  filterset_fields = ['name','age']  # 这个字段创建了，就可以不用创建TasksFilter类了，相当于创建了一个只有Meta的过滤器类
  # ###############这里使用过滤器###############
```
