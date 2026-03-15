# 现在我们来回溯一下这次的开发步骤
基于python/django框架的web开发
# *第一部分，静态网站的搭建*
## 一、配置环境 
conda激活虚拟环境，并且安装django包；

## 二、创建项目
cd进项目文件夹,然后执行  
> *django-admin startproject test_web*

然后就会生成django框架的项目文件夹test_web，在下面会有两个内容：
1. 文件夹，同样名为test_web
2. 文件manage.py 

此后我们提及test_web文件夹均指这两个内容中的test_web文件夹
## 三、创建应用
在项目根目录下执行：
> *python manage.py startapp test_app*

随后会在项目根目录下产生一个应用文件夹，名字就叫test_web

然后要及时的在test_web文件夹中的settings.py中**注册应用**

## 四、编辑视图函数
进入test_app文件夹中的views.py

编辑视图函数一般可以定义成：
```
def home(request):
	return render(request,'test_app/home.html')
```
ps:一般情况下，函数名多用小写以_链接
随后有三步：
1. 在test_app文件夹中创建一个**templates**文件夹  
2. 在其下在创建一个**test_app**文件夹(和应用文件夹同名)  
3. 在其下创建一个名为**home.html**的文件，作为返回的网页内容,就完成了

> 这里使用的是模板文件的方式返回网页内容，当然也可以直接使用函数HttpResponse进行返回:
```
#引入HttpResponse函数
from django.http import HttpResponse

#重新定义函数
def home(request):
    return HttpResponse("This is a test web")

#或者像下面这样，直接引入html语言
def home(request):
    html_content=
    """
    //在这里写上完整的html文件内容
    """
    return HttpReponse(html_content)
```

#### 两者的对比：

|方式|上手难度|文件结构|
|----|-----|------|
|模板文件|高，对于新手需要理解的内容较多|项目文件更加整齐，在大型项目开发的过程中更加常用，便于维护和更改|
|HttpResponse|低，直观，显然，语法逻辑一致|项目文件代码紊乱，views.py文件中混杂python语言和html+css+js语言，当路由分配变多之后文件将非常冗长|

## 五、分配路由
随后在test_web文件夹中的urls.py中分配路由即可
```
urlpatterns = [
    path("admin/", admin.site.urls),
    path("",views.home)
]
```

这里意思就是：

| url | 作用 |
|-----|----|  
|`域名/admin/`| 调用进入后台的函数|
|`域名/`|调用views.py中的home函数|

这属于集中式路由，当然还有一种是分布式路由：  
```
from django.urls import path,include  

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog/', include('blog.urls')),      
    path('user/', include('user.urls')),      
    path('shop/', include('shop.urls'))
]
```
这是项目级路由
> 这就把路由分配到了具体的应用中进行二次分配，常用于有多个应用的情况下，不同应用下面还有其他的页面需要分配路由  

然后进入相应的应用的文件夹(在这里就是blog,user,shop三个应用文件夹)中新建一个文件urls.py
```
from django.urls import path
from . import views  #从当前文件夹

urlpatterns=[
    path = ('add/',views.add,name='add'),
    path = ('delete/',views.delete,name='delete')
]
```
这就是应用级路由  
其中name=''这个参数相当于为这个url起了一个名字，调用时无需再打出完整网址，只需要用名字代替即可

#### 两者的对比：

|路由类型|小项目|大项目|
|---|---|---|
|集中式路由|小项目页面不多，集中式路由简洁清晰，文件结构更简单，适合新手上手|应用多，应用中功能复杂，页面更多，全部堆砌在项目级urls.py中，非常庞杂|
|分布式路由|杀鸡焉用牛刀，化简为繁|量身定做，项目级路由导航到对应的应用，应用级路由导航到各个应用内的各种功能|

## 六、运行测试
执行命令：
> *python manage.py runserver*

就可以看到终端返回一个网址：  
注意这只是域名，具体的网页需要根据你分配的路由加上子域名进行访问

---
这是最简单的网页，不涉及数据库的内容，以下内容开始增加关于数据库的内容 不过在此之前我们回答几个问题
## *plus:answer some questions*：
### 1. 注册应用是什么意思？

- 首先使用`python manage.py startapp test_app`命令之后会在项目根目录下创建了一个名叫test_app的文件夹，这个文件夹中有着很多文件

- 但是，此时此刻他只是一个独立的文件夹，相当于你在电脑的任何一个位置创建的任何一个普通文件夹，没有任何特殊的地位

- 当你在test_web文件夹的settings.py文件中注册了这个文件夹之后，这个文件夹将和项目联系在一起，成为这个项目的一个子文件，而不是独立的文件夹

> **用一句话总结**：如果你天赋异禀，可以记住这个文件夹中所有的文件及其内容，那么你可以不使用startapp这个命令，转而手动创建一个文件夹，然后手动填充内容，效果是完全一样的，没有任何区别

### 2. 分配路由什么意思
首先我们了解一下什么是**路由**，路由简单来说就是路线的分配  

输入起点和终点,路由会告诉你路线  
几个例子：  

|例子|起点|目标|路由|
|---|---|---|---|
|交通路由|当前路口|目的地|导航系统规划的路线|
|邮政路由|寄件地|收件地址|邮局的分拣和转运系统|
|网络路由|你的电脑|目标网站服务器|路由器决定数据包走哪一条光纤|
|Web框架路由|用户访问url|处理这个访问请求的代码|框架根据URL分配情况找到对应的函数|

**本质是一种映射，需求和解决需求的方法之间的映射**

那么在这个开发过程中，就是网址与函数的映射，当用户访问一个url的时候，django就会在urlpatterns中进行匹配，匹配成功则执行对应的函数，返回相应的函数

---

# *第二部分，网站的动态化*
基于静态网站的所有内容之后，我们有了更动态的要求，譬如一个博客网站能否记录过往提交的内容，而不是每次打开都是空白  

### 而这个功能就需要 ***数据库*** 发挥作用

## 七、创建模型
在应用目录test_app下的models.py中编辑数据库表结构
```
class Todo(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    time = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title
```

首先是定义一个class，我们来详细看一下这段代码：

1. 类名一般用大驼峰命名法，和函数命名有所区别
2. `(models.Model)` 这是一个类的继承语法，models 是django.db模块下的子模块，Model是models模块中的一个类，表示 Todo 这个类继承了models模块中Model类
3. 下面的三个字段 title,content,time 属于自定义的字段，()中是函数可加的参数
4. 最后定义的函数`__str__`是python内置的特殊方法，self参数表示指向对象本身，返回对象的title，这个函数会在后台数据库中发挥作用，显示title,而不是 object(1) 这样的内容  
简而言之:"当需要把这个对象当作字符串使用时，就用它的title字段来代表它。"

> 现在模型构建完了，但是一般都会在后台注册一下，便于管理员进入后台管理数据（虽然不注册并不会影响网站运行）：  
打开test_app文件夹中的admin.py文件
> ```
#首先导入类
from .models import Todo
#然后注册
admin.site.register(Todo)
> ```

## 八、迁移文件

构建完模型之后，就需要开始造数据表了，不过在此之前需要“画图纸”，执行命令： 

> *python manage.py makemigrations*

这时,在test_app文件夹下面的migrations文件夹中就会创建一个名为`0001_initial`的文件，这就是迁移文件，也就是创建表格的图纸

当有了图纸之后，就可以正式创建表格了，执行命令：

> *python manage.py migrate*

**这时，数据表就正式创建好了，但此时还是空表，没有数据**

**注意**：每次makemigrations都会生成一个数据库表的图纸，只有migrate之后才会更新数据表，所以，一旦更改model的结构了，就要执行这两步骤，才能让数据库表成功更改  


---
下面，我们来分析现在的情况：  
我们有了页面，有了数据库表，也就是说我们有了存储数据的能力。  

那么，问题就自然而然的产生了：  
1. 管理员如何修改数据库的数据
2. 如何把数据库的数据呈现在网站上
3. 如何让用户和数据库进行交互，修改数据内容

---
不过在此之前，我们来回答几个问题

## *plus: answer some questions:*

### 1. python语言操控数据库？
ORM(Object-Relational Mapping，对象关系映射技术)，在这里体现在python语言来控制数据库，而不是直接使用对应数据库的语法

### 2. makemigrations 和 migrate的工作方式

- 首先每一次创建或更改models的时候，只是我们自己更改了，数据库表还原封不动；

- 然后执行makemigrations之后就会创建新数据库表的“图纸”，就是在migrations文件夹之下的那些文件，此时数据库表仍然没有发生变化；

- 随后执行migrate的时候，首先会读取哪些文件是没有被migrate过的，然后将这些没有migrate的文件迁移一下，就会创建出最新的数据库表，此时数据库表才真正发生了变化

---

## 九、管理员修改数据库的数据
现在数据库创建好了，不过里面还是空的，我们怎么能操作里面的数据呢？  

目前来看只有一个方法，就是**进入后台**

首先需要创建一个superuser账号（竟不是管理员，而是超级用户）

> *python manage.py createsuperuser*

然后输入名称，回车；  
输入邮箱，或者直接回车；  
输入密码，回车；  
确认密码，回车；  
注意：输入的时候是不显示字符的，这不是bug

创建完成之后，就可以运行:

> *python manage.py runserver*

进入网站，在url上加上/admin/子域名，登录后就可以进入后台了，在这里可以看到自己已经创建的模型，可以add,可以delete，对数据进行管理

## 十、在页面上展示数据库的数据
现在，数据库也有了，你也可以进入后台操作数据了，那么用户打开网站照样看不到这些数据，所以，下一步就是怎么把数据展示在web页面上，这就要对视图函数和模板文件进行修改了  

#### 1. 修改views.py

```
from django.shortcuts import render
from .models import Todo

def home(request):
    todos = Todo.objects.all()
    return render(request,'test_app/home.html',{'todos':todos})
```

- 我们来分析一下这段语法：
    - 首先，是两个导入，一个导入render函数，一个导入我们定义的类Todo
    - 下面是def函数的内容：
        - `todos = Todo.objects.all()`  
            - `todos`是一种django框架中的特殊容器(QuerySet),外观是list形式，可以用索引访问其中元素，内中元素是class，可以调用接口函数  
            - `.objects`是自带的管理器，提供一系列操作方法，比如all(),filter(),get()等等

        - `return render(request,'test_app/home.html',{'todos':todos})`  
            - `test_app/home.html`是模板文件路径  
            - `{'todos':todos}`是一个字典，传到模板文件中，表示在模板文件中可以使用键值{{todos}}通过双大括号进行调用  
            在这里，第一个单引号中的todos是键值，表示在模板文件中调用的方式，第二个todos是定义函数时候创建的临时变量(QuerySet容器)

现在关于视图函数的部分搞明白了之后，就是在模板文件中怎么写了，注意想要调用数据库的时候，用键值{{todos}}调用

#### 2. 修改模板文件(home.html)

```
<!DOCTYPE html>
<html>
<head>
    <title>待办事项列表</title>
    <style>
        body { font-family: Arial; max-width: 800px; margin: 0 auto; padding: 20px; }
        .todo-item { border: 1px solid #ddd; margin: 10px 0; padding: 15px; border-radius: 5px; }
        .todo-title { font-size: 1.2em; font-weight: bold; color: #333; }
        .todo-content { margin: 10px 0; color: #666; }
        .todo-time { color: #999; font-size: 0.9em; }
    </style>
</head>
<body>
    <h1>我的待办事项</h1>
    
    {% if todos %}
        {% for todo in todos %}
            <div class="todo-item">
                <div class="todo-title">{{ todo.title }}</div>
                <div class="todo-content">{{ todo.content }}</div>
                <div class="todo-time">创建时间：{{ todo.time }}</div>
            </div>
        {% endfor %}
    {% else %}
        <p>暂无待办事项，快去添加吧！</p>
    {% endif %}
</body>
</html>
```

我们只看body部分的内容，css部分先不考虑

1. **第一层，分支结构:**  
`{% if todos %}`和`{% else %}`
todos是QuerySet容器，和python中一样，空为False,否则为True  
那么这里就是表示：  
如果todos中有元素，则执行`{% if todos %}`的内容  
如果todos中没有元素，则执行`{% else %}`的内容

2. **第二层，循环结构：**
`{% for todo in todos %}`和`{% endfor %}`  
这是个循环语法，todo是循环变量，循环次数为todos中的元素数量，调用内部的循环体代码

3. **第三层，循环体：**
```
<div class="todo-item">
    <div class="todo-title">{{ todo.title }}</div>
    <div class="todo-content">{{ todo.content }}</div>
    <div class="todo-time">创建时间：{{ todo.time }}</div>
</div>
```
这里是很简单的html语法，唯一不同的就是通过{{todo.title}}调用了数据库的内容，我们来分析一下这个过程：  
- 这里`todo`是循环变量，实际上就是`todos`中某个元素
- 然后我们在views.py中以字典的形式将数据传到模板文件中`{'todos':todos}`,通过`todos`(键,第一个)可以调用`todos`(值，第二个，是一个容器)中的数据

---

到这里，再进入网站主页，就可以看到历史记录了。那么问题来了，现在所有的内容只能通过后台增加和删除，最后一步，做出网站内的用户交互页面，让用户可以增添记录和删除记录

---

## 十一、制作用户交互功能

### *1 add功能*
我们首先处理信息的增加功能，即让用户可以添加数据，在这里，我们使用新的页面,并且调用django的表单模块

#### 1. 增加模板文件作为增加时的网站页面
```
<body>
    <h1>新建待办</h1>
    
    <form method="post">
        {% csrf_token %}
        
        {{ form.as_p }}
        
        <button type="submit" class="btn btn-primary">保存</button>
        <a href="{% url 'todo_home' %}">取消</a>
    </form>
</body>
```

我们来简单解释一下这里的代码：  
1. 首先`{% csrf_token %}`,是安全标识，否则网站无法打开
2. 其次`{{ form.as_p }}`,这是表单的引入，将每一部分用`<p>`标签包裹
3. 下面就是button和form的联动了，我们用一个表格来说明

|button,type属性|form,method属性|说明|
|---|---|---|
|submit|post/get|submit触发表单向服务器发送请求，请求类型是post，还是get|
|button|无需|button属性触发之后，激发html文件中js代码的执行|

#### 2. 创建表单文件
```
class TodoForm(forms.ModelForm):

    class Meta:
        model = Todo  
        fields = ['title', 'content']  
        
        <!-- 下面是对表单样式的设置，不是骨架 -->
        labels = {
            'title': '标题',
            'content': '内容',
        }
        
        widgets = {
            'title': forms.TextInput(attrs={
                'class': 'form-control',
                'placeholder': '请输入标题',
                'maxlength': 200
            }),
            'content': forms.Textarea(attrs={
                'class': 'form-control',
                'placeholder': '请输入详细内容',
                'rows': 5
            }),
        }
```
相当于创建了一个“形如表单”的html，也就是不用你写完整的html，只需要调用form中封装的属性就可以，最后仍然会被转换成html渲染在页面上

#### 3. 修改主页模板文件，并分配路由
增加链接标签把新模板和主页用链接链接，随后在urls.py中为新页面分配路由

#### 4. 修改视图函数
```
def add(request):
    
    if request.method == 'POST':
        
        form = TodoForm(request.POST)  
        if form.is_valid():  
            form.save()      
            return redirect('todo_home')
    else:
        
        form = TodoForm()
    
    
    return render(request, 'test_app/todo_add.html', {'form': form})
```

我们来说明一下这个代码：
1. `if request.method == 'POST'`  
表示服务器接收到的是POST请求(这在模板文件中提到过，这里的post请求就来自于模板文件中的`<form>`标签中的`method='post'`属性)

2. `form = TodoForm(request.POST)`  
这里`request.POST`就是用户提交的内容，可以直接打印，这里`TodoForm`创建了这个类的一个对象,`()`内就是初始化的内容，这里就是用户所填的内容，左值form就是这个对象的名字，起别的也可以

3. `form = TodoForm()`  
这就是`else`的执行内容，创建对象，初始化为空

4. `return render(request, 'test_app/todo_add.html', {'form': form})`  
这是render函数，最后以键form在模板文件中调用函数中的form对象