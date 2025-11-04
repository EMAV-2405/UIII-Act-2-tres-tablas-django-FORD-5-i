🏗️ Fase 1: Configuración del Proyecto y Entorno
Sigue estos pasos en tu terminal (como Git Bash, CMD o PowerShell).

1. Crear carpeta del Proyecto

Bash

mkdir UIII_Ford_0493
2. Abrir VS Code en la carpeta

Bash

cd UIII_Ford_0493
code .
3. Abrir terminal en VS Code

Usa el atajo Ctrl + Shift + Ñ (o `Ctrl + ``).

O ve al menú Terminal > New Terminal.

4. Crear entorno virtual “.venv”

Bash

# Asegúrate de estar en la carpeta UIII_Ford_0493
python -m venv .venv
5. Activar el entorno virtual

En Windows (Git Bash):

Bash

source .venv/Scripts/activate
En Windows (CMD/PowerShell):

Bash

.\.venv\Scripts\activate
En macOS/Linux:

Bash

source .venv/bin/activate
(Tu terminal debería mostrar (.venv) al inicio)

6. Activar intérprete de Python (En VS Code)

Abre la paleta de comandos: Ctrl + Shift + P.

Escribe y selecciona: Python: Select Interpreter.

Elige la opción que dice ./.venv/Scripts/python.exe (o similar).

7. Instalar Django

Bash

pip install django
8. Crear proyecto backend_Ford (sin duplicar carpeta)

Importante: Nota el punto (.) al final. Esto crea el manage.py en la carpeta actual (UIII_Ford_0493).

Bash

django-admin startproject backend_Ford .
9. Ejecutar servidor (Prueba inicial)

Bash

python manage.py runserver 8093
10. Copiar y pegar el link

Abre tu navegador (Chrome, Firefox, etc.).

Copia la dirección que aparece en la terminal: http://127.0.0.1:8093/.

Deberías ver la página de bienvenida de Django.

Presiona Ctrl + C en la terminal para detener el servidor.

11. Crear aplicación app_Ford

Bash

python manage.py startapp app_Ford
📋 Fase 2: Modelos y Base de Datos
12. (Modelo) app_Ford/models.py

Abre el archivo app_Ford/models.py y pega el siguiente contenido:

Python

from django.db import models

# ==========================================
# MODELO: EMPLEADO
# ==========================================

class Empleado(models.Model):
    nombre = models.CharField(max_length=100)
    apellido = models.CharField(max_length=100)
    puesto = models.CharField(max_length=100)
    telefono = models.CharField(max_length=30, blank=True, null=True)
    email = models.EmailField(blank=True, null=True)
    fecha_contratacion = models.DateField(blank=True, null=True)
    salario = models.DecimalField(max_digits=10, decimal_places=2, blank=True, null=True)
    
    def __str__(self):  
        return f"{self.nombre} {self.apellido}"  

# ==========================================
# MODELO: VEHÍCULO
# ==========================================
class Vehiculo(models.Model):
    marca = models.CharField(max_length=100)
    modelo = models.CharField(max_length=100)
    anio = models.PositiveIntegerField()
    color = models.CharField(max_length=50, blank=True, null=True)
    numero_serie = models.CharField(max_length=100, unique=True)
    precio = models.DecimalField(max_digits=12, decimal_places=2)
    cantidad_disponible = models.PositiveIntegerField(default=1)
    
    def __str__(self):  
        return f"{self.marca} {self.modelo} ({self.anio})"  


# ==========================================
# MODELO: VENTA
# ==========================================
class Venta(models.Model):
    vehiculo = models.ForeignKey(Vehiculo, on_delete=models.CASCADE, related_name='ventas')
    empleado = models.ForeignKey(Empleado, on_delete=models.SET_NULL, null=True, blank=True, related_name='ventas')
    cliente_nombre = models.CharField(max_length=150)
    cliente_telefono = models.CharField(max_length=50, blank=True, null=True)
    fecha_venta = models.DateField(auto_now_add=True)
    total = models.DecimalField(max_digits=12, decimal_places=2)
    metodo_pago = models.CharField(max_length=50, blank=True, null=True)
    folio = models.CharField(max_length=100, blank=True, null=True)
    
    def __str__(self):  
        return f"Venta {self.folio or self.id}"
25. Agregar app_Ford en backend_Ford/settings.py

Abre backend_Ford/settings.py y busca la lista INSTALLED_APPS. Agrega 'app_Ford' al inicio:

Python

INSTALLED_APPS = [
    'app_Ford',  # <--- AGREGAR AQUÍ
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
12.5. Realizar las migraciones

Ejecuta estos dos comandos en la terminal:

Bash

python manage.py makemigrations
python manage.py migrate
🖥️ Fase 3: Plantillas Base (Templates)
15. Crear carpeta templates

Dentro de la carpeta app_Ford, crea una nueva carpeta llamada templates.

16-17. app_Ford/templates/base.html (Con Bootstrap)

Crea el archivo base.html dentro de app_Ford/templates/ y pega esto:

HTML

<!doctype html>
<html lang="es">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>{% block title %}Sistema Ford{% endblock %}</title>
    
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
    
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.3/font/bootstrap-icons.min.css">

    <style>
        /* Ajuste para que el contenido no quede debajo del footer fijo */
        body {
            margin-bottom: 100px; 
        }
    </style>
</head>
<body class="bg-light">

    {% include 'navbar.html' %}

    <main class="container mt-4">
        {% block content %}
        {% endblock %}
    </main>

    {% include 'footer.html' %}

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
</body>
</html>
16, 18. app_Ford/templates/navbar.html

Crea el archivo navbar.html dentro de app_Ford/templates/ y pega esto:

HTML

{% load static %}
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.3/font/bootstrap-icons.min.css">

<nav class="navbar navbar-expand-lg navbar-dark bg-dark sticky-top" data-bs-theme="dark">
    <div class="container-fluid">
        <a class="navbar-brand" href="{% url 'inicio' %}">
            <i class="bi bi-car-front-fill"></i>
            Sistema de Administración Ford
        </a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNavDropdown">
            <ul class="navbar-nav ms-auto">
                <li class="nav-item">
                    <a class="nav-link" aria-current="page" href="{% url 'inicio' %}">
                        <i class="bi bi-house-door"></i> Inicio
                    </a>
                </li>
                
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="bi bi-truck"></i> Vehículos
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="{% url 'agregar_vehiculo' %}">Agregar vehículo</a></li>
                        <li><a class="dropdown-item" href="{% url 'ver_vehiculos' %}">Ver vehículos</a></li>
                        </ul>
                </li>
                
                <li class="nav-item">
                    <a class="nav-link" href="#">
                        <i class="bi bi-people"></i> Empleados
                    </a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#">
                        <i class="bi bi-tags"></i> Ventas
                    </a>
                </li>
            </ul>
        </div>
    </div>
</nav>
16, 19. app_Ford/templates/footer.html

Crea el archivo footer.html dentro de app_Ford/templates/ y pega esto:

HTML

<footer class="bg-dark text-white text-center py-3 position-fixed bottom-0 w-100">
    <div class="container">
        <p class="mb-0">
            &copy; {% now "Y" %} Derechos de autor | Creado por Ing. Emiliano Avilez, Cbtis 128
        </p>
    </div>
</footer>
16, 20. app_Ford/templates/inicio.html

Crea el archivo inicio.html dentro de app_Ford/templates/ y pega esto:

HTML

{% extends 'base.html' %}

{% block title %}Inicio - Sistema Ford{% endblock %}

{% block content %}
<div class="container mt-5">
    <div class="row">
        <div class="col-md-7">
            <h1 class="display-4">Bienvenido al Sistema de Administración Ford</h1>
            <p class="lead">
                Este sistema permite gestionar los vehículos, empleados y ventas del concesionario.
                Utilice la barra de navegación superior para acceder a las diferentes secciones.
            </p>
            <hr class="my-4">
            <p>
                Actualmente, el módulo de **Vehículos** está activo, permitiendo:
            </p>
            <ul>
                <li>Registrar nuevos vehículos.</li>
                <li>Ver el inventario existente.</li>
                <li>Actualizar la información de los vehículos.</li>
                <li>Eliminar vehículos del sistema.</li>
            </ul>
            <a class="btn btn-primary btn-lg" href="{% url 'ver_vehiculos' %}" role="button">Ver Inventario</a>
        </div>
        <div class="col-md-5">
            <img src="https://www.ford.mx/content/dam/Ford/website-assets/latam/mx/nameplate/raptor-r/2024/overlay/raptor-r-desktop.jpg" 
                 class="img-fluid rounded shadow" 
                 alt="Ford Raptor">
        </div>
    </div>
</div>
{% endblock %}
🚗 Fase 4: CRUD de Vehículos (Views y Templates)
21. Crear subcarpeta vehiculos

Dentro de app_Ford/templates/, crea una nueva carpeta llamada vehiculos.

22. app_Ford/templates/vehiculos/agregar_vehiculo.html

Crea este archivo y pega el siguiente código:

HTML

{% extends 'base.html' %}

{% block title %}Agregar Vehículo{% endblock %}

{% block content %}
<div class="card shadow-sm col-md-8 mx-auto">
    <div class="card-header">
        <h4 class="mb-0">Registrar Nuevo Vehículo</h4>
    </div>
    <div class="card-body">
        <form method="POST">
            {% csrf_token %} <div class="row">
                <div class="col-md-6 mb-3">
                    <label for="marca" class="form-label">Marca</label>
                    <input type="text" class="form-control" id="marca" name="marca" required>
                </div>
                <div class="col-md-6 mb-3">
                    <label for="modelo" class="form-label">Modelo</label>
                    <input type="text" class="form-control" id="modelo" name="modelo" required>
                </div>
            </div>

            <div class="row">
                <div class="col-md-6 mb-3">
                    <label for="anio" class="form-label">Año</label>
                    <input type="number" class="form-control" id="anio" name="anio" required>
                </div>
                <div class="col-md-6 mb-3">
                    <label for="color" class="form-label">Color</label>
                    <input type="text" class="form-control" id="color" name="color">
                </div>
            </div>

            <div class="mb-3">
                <label for="numero_serie" class="form-label">Número de Serie (VIN)</label>
                <input type="text" class="form-control" id="numero_serie" name="numero_serie" required>
            </div>

            <div class="row">
                <div class="col-md-6 mb-3">
                    <label for="precio" class="form-label">Precio</label>
                    <input type="number" class="form-control" id="precio" name="precio" step="0.01" required>
                </div>
                <div class="col-md-6 mb-3">
                    <label for="cantidad" class="form-label">Cantidad Disponible</label>
                    <input type="number" class="form-control" id="cantidad" name="cantidad" value="1" required>
                </div>
            </div>

            <hr>
            <button type="submit" class="btn btn-primary">Guardar Vehículo</button>
            <a href="{% url 'ver_vehiculos' %}" class="btn btn-secondary">Cancelar</a>
        </form>
    </div>
</div>
{% endblock %}
22. app_Ford/templates/vehiculos/ver_vehiculos.html

Crea este archivo y pega el siguiente código:

HTML

{% extends 'base.html' %}

{% block title %}Ver Vehículos{% endblock %}

{% block content %}
<div class="card shadow-sm">
    <div class="card-header d-flex justify-content-between align-items-center">
        <h4 class="mb-0">Inventario de Vehículos</h4>
        <a href="{% url 'agregar_vehiculo' %}" class="btn btn-primary btn-sm">
            <i class="bi bi-plus-circle"></i> Agregar Nuevo
        </a>
    </div>
    <div class="card-body">
        <div class="table-responsive">
            <table class="table table-hover table-striped align-middle">
                <thead class="table-dark">
                    <tr>
                        <th>ID</th>
                        <th>Marca</th>
                        <th>Modelo</th>
                        <th>Año</th>
                        <th>Color</th>
                        <th>No. Serie</th>
                        <th>Precio</th>
                        <th>Stock</th>
                        <th>Acciones</th>
                    </tr>
                </thead>
                <tbody>
                    {% for v in vehiculos %}
                    <tr>
                        <td>{{ v.id }}</td>
                        <td>{{ v.marca }}</td>
                        <td>{{ v.modelo }}</td>
                        <td>{{ v.anio }}</td>
                        <td>{{ v.color|default:"N/A" }}</td>
                        <td>{{ v.numero_serie }}</td>
                        <td>${{ v.precio }}</td>
                        <td>{{ v.cantidad_disponible }}</td>
                        <td>
                            <a href="{% url 'actualizar_vehiculo' v.id %}" class="btn btn-info btn-sm" title="Ver/Editar">
                                <i class="bi bi-pencil-square"></i>
                            </a>
                            <a href="{% url 'borrar_vehiculo' v.id %}" class="btn btn-danger btn-sm" title="Borrar">
                                <i class="bi bi-trash"></i>
                            </a>
                        </td>
                    </tr>
                    {% empty %}
                    <tr>
                        <td colspan="9" class="text-center">No hay vehículos registrados.</td>
                    </tr>
                    {% endfor %}
                </tbody>
            </table>
        </div>
    </div>
</div>
{% endblock %}
22. app_Ford/templates/vehiculos/actualizar_vehiculo.html

Crea este archivo y pega el siguiente código:

HTML

{% extends 'base.html' %}

{% block title %}Actualizar Vehículo{% endblock %}

{% block content %}
<div class="card shadow-sm col-md-8 mx-auto">
    <div class="card-header">
        <h4 class="mb-0">Actualizar Vehículo: {{ vehiculo.marca }} {{ vehiculo.modelo }}</h4>
    </div>
    <div class="card-body">
        
        <form method="POST" action="{% url 'realizar_actualizacion_vehiculo' vehiculo.id %}">
            {% csrf_token %} <div class="row">
                <div class="col-md-6 mb-3">
                    <label for="marca" class="form-label">Marca</label>
                    <input type="text" class="form-control" id="marca" name="marca" value="{{ vehiculo.marca }}" required>
                </div>
                <div class="col-md-6 mb-3">
                    <label for="modelo" class="form-label">Modelo</label>
                    <input type="text" class="form-control" id="modelo" name="modelo" value="{{ vehiculo.modelo }}" required>
                </div>
            </div>

            <div class="row">
                <div class="col-md-6 mb-3">
                    <label for="anio" class="form-label">Año</label>
                    <input type="number" class="form-control" id="anio" name="anio" value="{{ vehiculo.anio }}" required>
                </div>
                <div class="col-md-6 mb-3">
                    <label for="color" class="form-label">Color</label>
                    <input type="text" class="form-control" id="color" name="color" value="{{ vehiculo.color|default:'' }}">
                </div>
            </div>

            <div class="mb-3">
                <label for="numero_serie" class="form-label">Número de Serie (VIN)</label>
                <input type="text" class="form-control" id="numero_serie" name="numero_serie" value="{{ vehiculo.numero_serie }}" required>
            </div>

            <div class="row">
                <div class="col-md-6 mb-3">
                    <label for="precio" class="form-label">Precio</label>
                    <input type="number" class="form-control" id="precio" name="precio" step="0.01" value="{{ vehiculo.precio }}" required>
                </div>
                <div class="col-md-6 mb-3">
                    <label for="cantidad" class="form-label">Cantidad Disponible</label>
                    <input type="number" class="form-control" id="cantidad" name="cantidad" value="{{ vehiculo.cantidad_disponible }}" required>
                </div>
            </div>

            <hr>
            <button type="submit" class="btn btn-success">Guardar Cambios</button>
            <a href="{% url 'ver_vehiculos' %}" class="btn btn-secondary">Cancelar</a>
        </form>
    </div>
</div>
{% endblock %}
22. app_Ford/templates/vehiculos/borrar_vehiculo.html

Crea este archivo y pega el siguiente código:

HTML

{% extends 'base.html' %}

{% block title %}Confirmar Borrado{% endblock %}

{% block content %}
<div class="card shadow-sm col-md-6 mx-auto border-danger">
    <div class="card-header bg-danger text-white">
        <h4 class="mb-0">Confirmar Eliminación</h4>
    </div>
    <div class="card-body">
        <p class="fs-5">
            ¿Estás seguro de que deseas eliminar permanentemente el vehículo: 
            <strong>{{ vehiculo.marca }} {{ vehiculo.modelo }} ({{ vehiculo.anio }})</strong>?
        </p>
        <p class="text-muted">Esta acción no se puede deshacer.</p>
        
        <form method="POST">
            {% csrf_token %}
            <button type="submit" class="btn btn-danger">Sí, Eliminar</button>
            <a href="{% url 'ver_vehiculos' %}" class="btn btn-secondary">Cancelar</a>
        </form>
    </div>
</div>
{% endblock %}
14. app_Ford/views.py (Funciones CRUD)

Abre app_Ford/views.py y reemplaza su contenido con esto:

Python

from django.shortcuts import render, redirect, get_object_or_404
from .models import Vehiculo, Empleado, Venta # Importamos todos los modelos

# Create your views here.

def inicio_ford(request):
    """
    Vista para la página de inicio.
    """
    context = {
        'mensaje': 'Bienvenido al sistema Ford'
    }
    return render(request, 'inicio.html', context)

# --- VISTAS DE VEHÍCULO ---

def agregar_vehiculo(request):
    """
    Vista para agregar un nuevo vehículo.
    Maneja GET (mostrar formulario) y POST (guardar datos).
    """
    if request.method == 'POST':
        # (23) No usamos forms.py, leemos los datos directo del request.POST
        # (29) No hay validación de entrada
        marca = request.POST.get('marca')
        modelo = request.POST.get('modelo')
        anio = request.POST.get('anio')
        color = request.POST.get('color')
        numero_serie = request.POST.get('numero_serie')
        precio = request.POST.get('precio')
        cantidad = request.POST.get('cantidad')

        # Creamos y guardamos el nuevo objeto Vehiculo
        Vehiculo.objects.create(
            marca=marca,
            modelo=modelo,
            anio=anio,
            color=color,
            numero_serie=numero_serie,
            precio=precio,
            cantidad_disponible=cantidad
        )
        # Redirigimos al listado de vehículos
        return redirect('ver_vehiculos')

    # Si es GET, solo mostramos el formulario
    return render(request, 'vehiculos/agregar_vehiculo.html')


def ver_vehiculos(request):
    """
    Vista para mostrar todos los vehículos registrados en una tabla.
    """
    vehiculos = Vehiculo.objects.all()
    context = {
        'vehiculos': vehiculos
    }
    return render(request, 'vehiculos/ver_vehiculos.html', context)


def actualizar_vehiculo(request, id):
    """
    Vista para MOSTRAR el formulario de actualización con los datos
    de un vehículo específico.
    (Maneja solo GET)
    """
    # Buscamos el vehículo por su ID, si no existe da error 404
    vehiculo = get_object_or_404(Vehiculo, id=id)
    context = {
        'vehiculo': vehiculo
    }
    return render(request, 'vehiculos/actualizar_vehiculo.html', context)


def realizar_actualizacion_vehiculo(request, id):
    """
    Vista para PROCESAR la actualización de un vehículo.
    (Maneja solo POST)
    """
    vehiculo = get_object_or_404(Vehiculo, id=id)
    
    if request.method == 'POST':
        vehiculo.marca = request.POST.get('marca')
        vehiculo.modelo = request.POST.get('modelo')
        vehiculo.anio = request.POST.get('anio')
        vehiculo.color = request.POST.get('color')
        vehiculo.numero_serie = request.POST.get('numero_serie')
        vehiculo.precio = request.POST.get('precio')
        vehiculo.cantidad_disponible = request.POST.get('cantidad')
        
        vehiculo.save() # Guardamos los cambios en la BD
        
        return redirect('ver_vehiculos')
    
    # Si alguien intenta acceder por GET, lo mandamos a la lista
    return redirect('ver_vehiculos')


def borrar_vehiculo(request, id):
    """
    Vista para borrar un vehículo.
    GET: Muestra la confirmación.
    POST: Borra el objeto.
    """
    vehiculo = get_object_or_404(Vehiculo, id=id)
    
    if request.method == 'POST':
        vehiculo.delete() # Elimina el registro
        return redirect('ver_vehiculos')
        
    # Si es GET, mostramos la página de confirmación
    context = {
        'vehiculo': vehiculo
    }
    return render(request, 'vehiculos/borrar_vehiculo.html', context)
🔗 Fase 5: URLs y Configuración Final
24. Crear app_Ford/urls.py

Crea un nuevo archivo llamado urls.py dentro de la carpeta app_Ford y pega esto:

Python

from django.urls import path
from . import views

urlpatterns = [
    # URL de inicio
    path('', views.inicio_ford, name='inicio'),
    
    # URLs de Vehículo (CRUD)
    path('vehiculos/agregar/', views.agregar_vehiculo, name='agregar_vehiculo'),
    path('vehiculos/ver/', views.ver_vehiculos, name='ver_vehiculos'),
    
    # URL para mostrar el formulario de actualización (GET)
    path('vehiculos/actualizar/<int:id>/', views.actualizar_vehiculo, name='actualizar_vehiculo'),
    
    # URL para procesar la actualización (POST)
    path('vehiculos/realizar_actualizacion/<int:id>/', views.realizar_actualizacion_vehiculo, name='realizar_actualizacion_vehiculo'),
    
    # URL para borrar (GET y POST)
    path('vehiculos/borrar/<int:id>/', views.borrar_vehiculo, name='borrar_vehiculo'),
]
26. Configurar backend_Ford/urls.py

Abre el archivo backend_Ford/urls.py (el que está junto a settings.py) y modifícalo para que incluya las URLs de app_Ford:

Python

from django.contrib import admin
from django.urls import path, include  # <-- Asegúrate de importar 'include'

urlpatterns = [
    path('admin/', admin.site.urls),
    # Agregamos esta línea para que la URL raíz (http://127.0.0.1:8093/)
    # sea manejada por 'app_Ford.urls'
    path('', include('app_Ford.urls')), 
]
27. Registrar Modelos en app_Ford/admin.py

Abre app_Ford/admin.py y registra los tres modelos:

Python

from django.contrib import admin
from .models import Empleado, Vehiculo, Venta

# (27) Registramos los modelos aunque solo trabajemos con Vehiculo en el front
admin.site.register(Empleado)
admin.site.register(Vehiculo)
admin.site.register(Venta)
27. Volver a realizar migraciones (Opcional pero buena práctica)

Como modificamos admin.py, no se requieren migraciones, pero si hubieras tocado models.py, deberías ejecutar:

Bash

python manage.py makemigrations
python manage.py migrate
(Nota: Si quieres un superusuario para ver el /admin, ejecuta: python manage.py createsuperuser)

🚀 Fase 6: Ejecución Final
31. Ejecutar servidor en el puerto 8093

¡Proyecto listo! Ejecuta el servidor:

Bash

python manage.py runserver 8093
Abre tu navegador y ve a http://127.0.0.1:8093/. Deberías ver la página de inicio, y el CRUD de vehículos (Agregar, Ver, Actualizar, Borrar) debería ser totalmente funcional.
