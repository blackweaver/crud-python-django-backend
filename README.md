# @Python

## Creo entorno virtual para correr el proyecto

`python3 -m ven myenv
source ../myenv/bin/activate`

## Instalar Django

`pip install django
pip install djangorestframework `

## Crear proyecto

`django-admin startproject DjangoAPI`

## Abro Visual Studio Code

`code .`


## Correr proyecto en Visual S

`source myenv/bin/activate` or `source ../myenv/bin/activate`
`cd ..`
`cd DjangoAPI`
`python manage.py runserver`


## Frente al error de MIDDLEWARE

`MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    # 'django.contrib.auth.middleware.SessionAuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]`

## Instalar SQLiteStudio y abrir base de datos creada por Django

https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15

`pip install django-cors-headers`

## Agrecar cors para acceder al dominio de la API

`CORS_ORIGIN_ALLOW_ALL = True
CORS_ORIGIN_WHITELIST = ('http://www.google.com')

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleWare',`

## Crear APP

`python3 manage.py startapp EmployeeApp`


## Settings aplication definition

`
INSTALLED_APPS = [
    ...
    'corsheaders',
    'EmployeeApp.apps.EmployeeappConfig',
    'rest_framework'
]
`

## Create a makemigrations for create db tables

`python3 manage.py migrate EmployeeApp`


## Ejectute migrate

`python3 manage.py makemigrations EmployeeApp`


## Create serializers:

```python
from rest_framework import serializers
from EmployeeApp.models import Departments, Employees

class DepartmentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Departments
        fields = ('DepartmentId',
                  'DepartmentName')

class EmployeeSerializer(serializers.ModelSerializer):
    class Meta:
        model = Employees
        fields = ('EmployeeId',
                  'EmployeeName',
                  'Department',
                  'DateOfJoining',
                  'PhotoFileName')

```


# API methods in views file:

```python
from django.shortcuts import render
from django.views.decorators.csrf import csrf_exempt
from rest_framework.parsers import JSONParser
from django.http.response import JsonResponse

from EmployeeApp.models import Departments, Employees
from EmployeeApp.serializers import DepartmentSerializer, EmployeeSerializer

@csrf_exempt
def departmentApi(request, id=0):
    if request.method == 'GET':
        departments = Departments.objects.all()
        department_serializer = DepartmentSerializer(departments, many=True)
        return JsonResponse(department_serializer.data, safe=False)
    elif request.method == 'POST':
        department_data = JSONParser().parse(request)
        department_serializer = DepartmentSerializer(data=department_data)
        if department_serializer.is_valid():
            department_serializer.save()
            return JsonResponse("Added Successfully!!", safe=False)
        return JsonResponse("Failed to Add.", safe=False)

    elif request.method == 'PUT':
        department_data = JSONParser().parse(request)
        departments = Departments.objects.get(DepartmentId=department_data['DepartmentId'])
        department_serializer = DepartmentSerializer(departments, data=department_data)
        if department_serializer.is_valid():
            department_serializer.save()
            return JsonResponse("Updated Successfully!!", safe=False)
        return JsonResponse("Failed to Update.", safe=False)

    elif request.method == 'DELETE':
        department = Departments.objects.get(DepartmentId=id)
        department.delete()
        return JsonResponse("Deleted Successfully!!", safe=False)

@csrf_exempt
def employeeApi(request, id=0):
    if request.method == 'GET':
        employees = Employees.objects.all()
        employee_serializer = EmployeeSerializer(employees, many=True)
        return JsonResponse(employee_serializer.data, safe=False)
    elif request.method == 'POST':
        employee_data = JSONParser().parse(request)
        employee_serializer = EmployeeSerializer(data=employee_data)
        if employee_serializer.is_valid():
            employee_serializer.save()
            return JsonResponse("Added Successfully!!", safe=False)
        return JsonResponse("Failed to Add.", safe=False)

    elif request.method == 'PUT':
        employee_data = JSONParser().parse(request)
        employees = Employees.objects.get(EmployeeId=employee_data['EmployeeId'])
        employee_serializer = EmployeeSerializer(employees, data=employee_data)
        if employee_serializer.is_valid():
            employee_serializer.save()
            return JsonResponse("Updated Successfully!!", safe=False)
        return JsonResponse("Failed to Update.", safe=False)

    elif request.method == 'DELETE':
        employee = Employees.objects.get(EmployeeId=id)
        employee.delete()
        return JsonResponse("Deleted Successfully!!", safe=False)
```

# Media library:

```python

from pathlib import Path
import os


# Build paths inside the project like this: os.path.join(BASE_DIR, ...)
BASE_DIR = Path(__file__).resolve(strict=True).parent.parent

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR,"media")

```

## URLs

```python

from django.conf.urls import url
from EmployeeApp import views

from django.conf.urls.static import static
from django.conf import settings

urlpatterns=[
    url(r'^department/$',views.departmentApi),
    url(r'^department/([0-9]+)$',views.departmentApi),

    url(r'^employee/$',views.employeeApi),
    url(r'^employee/([0-9]+)$',views.employeeApi),
    
    url(r'^SaveFile$', views.SaveFile)
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)

```

## Serializer

```typescript
from rest_framework import serializers
from EmployeeApp.models import Departments, Employees

class DepartmentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Departments
        fields = ('DepartmentId',
                  'DepartmentName')

class EmployeeSerializer(serializers.ModelSerializer):
    class Meta:
        model = Employees
        fields = ('EmployeeId',
                  'EmployeeName',
                  'Department',
                  'DateOfJoining',
                  'PhotoFileName')
```

## VIEWs

```python

from django.shortcuts import render
from django.views.decorators.csrf import csrf_exempt
from rest_framework.parsers import JSONParser
from django.http.response import JsonResponse

from EmployeeApp.models import Departments, Employees
from EmployeeApp.serializers import DepartmentSerializer, EmployeeSerializer

from django.core.files.storage import default_storage

# Create your views here.

@csrf_exempt
def departmentApi(request, id=0):
    if request.method == 'GET':
        departments = Departments.objects.all()
        department_serializer = DepartmentSerializer(departments, many=True)
        return JsonResponse(department_serializer.data, safe=False)
    elif request.method == 'POST':
        department_data = JSONParser().parse(request)
        department_serializer = DepartmentSerializer(data=department_data)
        if department_serializer.is_valid():
            department_serializer.save()
            return JsonResponse("Added Successfully!!", safe=False)
        return JsonResponse("Failed to Add.", safe=False)

    elif request.method == 'PUT':
        department_data = JSONParser().parse(request)
        departments = Departments.objects.get(DepartmentId=department_data['DepartmentId'])
        department_serializer = DepartmentSerializer(departments, data=department_data)
        if department_serializer.is_valid():
            department_serializer.save()
            return JsonResponse("Updated Successfully!!", safe=False)
        return JsonResponse("Failed to Update.", safe=False)

    elif request.method == 'DELETE':
        department = Departments.objects.get(DepartmentId=id)
        department.delete()
        return JsonResponse("Deleted Successfully!!", safe=False)

@csrf_exempt
def employeeApi(request, id=0):
    if request.method == 'GET':
        employees = Employees.objects.all()
        employee_serializer = EmployeeSerializer(employees, many=True)
        return JsonResponse(employee_serializer.data, safe=False)

    elif request.method == 'POST':
        employee_data=JSONParser().parse(request)
        employee_serializer = EmployeeSerializer(data=employee_data)
        if employee_serializer.is_valid():
            employee_serializer.save()
            return JsonResponse("Added Successfully!!" , safe=False)
        return JsonResponse("Failed to Add.",safe=False)

    elif request.method == 'PUT':
        employee_data = JSONParser().parse(request)
        employees = Employees.objects.get(EmployeeId=employee_data['EmployeeId'])
        employee_serializer = EmployeeSerializer(employees, data=employee_data)
        if employee_serializer.is_valid():
            employee_serializer.save()
            return JsonResponse("Updated Successfully!!", safe=False)
        return JsonResponse("Failed to Update.", safe=False)

    elif request.method == 'DELETE':
        employee = Employees.objects.get(EmployeeId=id)
        employee.delete()
        return JsonResponse("Deleted Successfully!!", safe=False)


@csrf_exempt
def SaveFile(request):
    file = request.FILES['uploadedFile']
    file_name = default_storage.save(file.name,file)

    return JsonResponse(file_name,safe=False)

```