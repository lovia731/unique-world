# unique-world
"""homework"""

#作业完成过程

1.仓库及目录的建立

(1)在github服务器上创建一个仓库 我的账号：lovia731 仓库名：unique-world

(2)在本地gitbash命令行中运行命令生成公私钥对 ssh-keygen

（3）将 ~/.ssh/id_rsa.pub文件的内容复制到git服务器的ssh 秘钥中 cat ~/.ssj/id_rsa.pub

（4）在本地建立一个与仓库名一样的目录 git clone git@github.com:lovia731/unique-world.git

（5）cd 仓库名目录，准备开始后面的编码工作 cd unique-world/

2.新建Django 工程，并提交到仓库

yunpan： （1）在仓库目录创建django工程 django-admin startproject yunpan

（2）确认Django项目是否真的创建成功 python manage.py runserver

（3）进入目录 cd yunpan

(4)创建yunpan工程下的应用miaochuan python manage.py startapp miaochuan

(5)验证工程是否创建成功 python manage.py runserver

（6）增加一个.gitignore 文件写入两行： **.sqlite3 **.pyc 将所有不需要提交到仓库的sqlite3文件和pyc文件过滤掉（在add操作时自动忽略掉）

（7）把新增加的工程文件提交到远程仓库 git add . git commit -m "yunpan" git push

mysite: （1）在仓库目录创建django工程 django-admin startproject mysite

（2）确认Django项目是否真的创建成功 python manage.py runserver

（3）进入目录 cd mysite

(4)创建mysite工程下的应用news,polls python manage.py startapp news python manage.py startapp polls

(5)验证工程是否创建成功 python manage.py runserver

3.添加文件上传功能 各目录文件代码： (1)MYSITE/news/admin.py from django.contrib import admin from . import models

admin.site.register(models.Student) #admin.site.register(models.Article) #admin.site.register(models.Reporter)

(2)MYSITE/news/models.py from django.db import models

class Student(models.Model): full_name = models.CharField(max_length=70) #age = models.IntegerField() class Sex(models.IntegerChoices): MALE = 1, '男' FEMALE = 2, '女' OTHER = 3, '其他'

sex = models.IntegerField(choices=Sex.choices)

def __str__(self):
    return self.full_name

class Homework(models.Model): commit_date = models.DateField(auto_now=True) headline = models.CharField(max_length=200) attach = models.FileField() remark = models.TextField() student = models.ForeignKey(Student, on_delete=models.CASCADE)

def __str__(self):
    return self.headline

(3)MYSITE/news/urls.py from django.urls import path

from . import views

urlpatterns = [ path('hw/create/', views.HomeworkCreate.as_view()), #path('articles/int:year/',view.year_archive), #path('articles/int:year/int:month/', views.month_archive), #path('articles/int:year/int:month/int:pk/', views.article_detail), ]

(4)MYSITE/news/views.py from django.shortcuts import render

from .models import Student, Homework

from django.views.generic.edit import CreateView

''' def year_archive(request, year): a_list = Article.objects.filter(pub_date__year=year) context = {'year': year, 'article_list': a_list} return render(request, 'news/year_archive.html', context) '''

class HomeworkCreate(CreateView): model = Homework template_name = 'homework_form.html' fields = ['headline','attach','remark', 'student']

(5)MYSITE/news/templates/homework_form.html
{% csrf_token %} {{ form.as_p }}

(6)MYSITE/news/templates/year_archive.html {% extends "base.html" %}

{% block title %}Articles for {{ year }}{% endblock %}

{% block content %}
Article for {{ year }}

{% for article in article_list %}

{{ article.headline }}

By {{ article.reporter.full_name }}

Published {{ article.pub_date|date:"F j, Y" }}
{% endfor %} {% endblock %}

(7)MYSITE/news/base.html {% load static %}
    <title>{% block title %}{% endblock %}</title>          {% block content %}{% endblock %}

(8)MYSITE/mysite/urls.py from django.contrib import admin from django.urls import path, include

urlpatterns = [ path('admin/', admin.site.urls), path('news/', include('news.urls')) ]

(9)MYSITE/migrations/0001_initial.py
Generated by Django 3.1.2 on 2020-11-23 08:20

from django.db import migrations, models import django.db.models.deletion

class Migration(migrations.Migration):

initial = True

dependencies = [
]

operations = [
    migrations.CreateModel(
        name='Reporter',
        fields=[
            ('id', models.AutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
            ('full_name', models.CharField(max_length=70)),
        ],
    ),
    migrations.CreateModel(
        name='Article',
        fields=[
            ('id', models.AutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
            ('pub_date', models.DateField()),
            ('headline', models.CharField(max_length=200)),
            ('content', models.TextField()),
            ('reporter', models.ForeignKey(on_delete=django.db.models.deletion.CASCADE, to='news.reporter')),
        ],
    ),
]

(10)MYSITE/migrations/0002_auto_20201130_1526.py
Generated by Django 3.1.2 on 2020-11-30 07:26

from django.db import migrations, models import django.db.models.deletion

class Migration(migrations.Migration):

dependencies = [
    ('news', '0001_initial'),
]

operations = [
    migrations.CreateModel(
        name='Homework',
        fields=[
            ('id', models.AutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
            ('commit_date', models.DateField()),
            ('headline', models.CharField(max_length=200)),
            ('attach', models.FileField(upload_to='')),
            ('remark', models.TextField()),
        ],
    ),
    migrations.CreateModel(
        name='Student',
        fields=[
            ('id', models.AutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
            ('full_name', models.CharField(max_length=70)),
            ('sex', models.IntegerField(choices=[(1, '男'), (2, '女'), (3, '其他')])),
        ],
    ),
    migrations.DeleteModel(
        name='Article',
    ),
    migrations.DeleteModel(
        name='Reporter',
    ),
    migrations.AddField(
        model_name='homework',
        name='student',
        field=models.ForeignKey(on_delete=django.db.models.deletion.CASCADE, to='news.student'),
    ),
]

(11)MYSITE/manage.py #!/usr/bin/env python """Django's command-line utility for administrative tasks.""" import os import sys

def main(): """Run administrative tasks.""" os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'mysite.settings') try: from django.core.management import execute_from_command_line except ImportError as exc: raise ImportError( "Couldn't import Django. Are you sure it's installed and " "available on your PYTHONPATH environment variable? Did you " "forget to activate a virtual environment?" ) from exc execute_from_command_line(sys.argv)

if name == 'main': main()

这一部分代码： cd mysite/ python manage.py makemigrations python manage.py createsuperuser sqlite3 git status git commit -m"增加news app的基本功能" .\manage.py makemigrations python manage.py migrate git add . git commit -m " 添加文件上传功能" git push python manage.py runserver

4.在Django云盘系统上建立相关数据

（1）http://localhost:8000/admin/news/student/ add student

(2)http://localhost:8000/admin/news/reporter/

(3)http://localhost:8000/news/articles/2020/

#遇到的问题及解决办法 （1）fatal:unable to auto-detect email adress 解决： git config --global user.email "lovia731@1652369007@qq.com" git config --global user.name "lovia731"

（2）公共密钥设置成了私人密钥 解决：删除私人密钥，在仓库中重新设置一个公共密钥

(3)报错"invalid non-printable character U+00A0 (, line 1)" 解决：上网搜索报错含义，发现是异常空格问题，于是把空格重新打了一遍，然后就成功运行了。
