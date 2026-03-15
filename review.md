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