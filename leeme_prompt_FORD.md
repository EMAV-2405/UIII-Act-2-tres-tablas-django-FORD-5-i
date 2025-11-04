1. Crear carpeta del proyecto y abrir VS Code
En tu terminal (PowerShell / CMD / Bash):
# 1. Crear carpeta del proyecto y entrar mkdir UIII_Ford_0493 cd UIII_Ford_0493 # 2. Abrir VS Code en la carpeta code .

2. Crear entorno virtual “.venv” y activarlo, activar intérprete en VS Code
(En Windows PowerShell)
python -m venv .venv # activar .\.venv\Scripts\Activate.ps1 # (PowerShell) # en cmd: # .\.venv\Scripts\activate.bat
(En macOS / Linux)
python3 -m venv .venv source .venv/bin/activate
En VS Code: pulsa Ctrl+Shift+P → Python: Select Interpreter → selecciona ./.venv (para que use el intérprete del entorno).

3. Instalar Django
pip install django

4. Crear proyecto backend_Ford sin duplicar carpeta
Dentro de UIII_Ford_0493 ejecutar con el punto para no crear carpeta extra:
django-admin startproject backend_Ford .
(esto crea manage.py y la carpeta backend_Ford correctamente sin duplicar)

5. Crear la app app_Ford
python manage.py startapp app_Ford

6. Estructura de carpetas (resultado esperado)
UIII_Ford_0493/
├─ .venv/
├─ manage.py
├─ backend_Ford/
│  ├─ __init__.py
│  ├─ settings.py
│  ├─ urls.py
│  └─ wsgi.py
├─ app_Ford/
│  ├─ migrations/
│  ├─ templates/
│  │  ├─ base.html
│  │  ├─ header.html
│  │  ├─ navbar.html
│  │  ├─ footer.html
│  │  ├─ inicio.html
│  │  └─ vehiculos/
│  │     ├─ agregar_vehiculo.html
│  │     ├─ ver_vehiculos.html
│  │     ├─ actualizar_vehiculo.html
│  │     └─ borrar_vehiculo.html
│  ├─ static/
│  │  └─ app_Ford/
│  │     └─ css/
│  │        └─ style.css
│  ├─ admin.py
│  ├─ apps.py
│  ├─ models.py
│  ├─ views.py
│  ├─ urls.py
│  └─ tests.py



7. models.py (usa tú ya provisto; aquí lo dejo con indentación y solo Vehiculo activo para trabajar — pero incluiré Empleado y Venta tal como pediste, aunque trabajaremos por ahora solo con Vehículo)
app_Ford/models.py
from django.db import models # ========================== # MODELO: EMPLEADO (pendiente de uso) # ========================== class Empleado(models.Model): nombre = models.CharField(max_length=100) apellido = models.CharField(max_length=100) puesto = models.CharField(max_length=100) telefono = models.CharField(max_length=30, blank=True, null=True) email = models.EmailField(blank=True, null=True) fecha_contratacion = models.DateField(blank=True, null=True) salario = models.DecimalField(max_digits=10, decimal_places=2, blank=True, null=True) def __str__(self): return f"{self.nombre} {self.apellido}" # ========================== # MODELO: VEHÍCULO (trabajamos con este) # ========================== class Vehiculo(models.Model): marca = models.CharField(max_length=100) modelo = models.CharField(max_length=100) anio = models.PositiveIntegerField() color = models.CharField(max_length=50, blank=True, null=True) numero_serie = models.CharField(max_length=100, unique=True) precio = models.DecimalField(max_digits=12, decimal_places=2) cantidad_disponible = models.PositiveIntegerField(default=1) def __str__(self): return f"{self.marca} {self.modelo} ({self.anio})" # ========================== # MODELO: VENTA (pendiente de uso) # ========================== class Venta(models.Model): vehiculo = models.ForeignKey(Vehiculo, on_delete=models.CASCADE, related_name='ventas') empleado = models.ForeignKey(Empleado, on_delete=models.SET_NULL, null=True, blank=True, related_name='ventas') cliente_nombre = models.CharField(max_length=150) cliente_telefono = models.CharField(max_length=50, blank=True, null=True) fecha_venta = models.DateField(auto_now_add=True) total = models.DecimalField(max_digits=12, decimal_places=2) metodo_pago = models.CharField(max_length=50, blank=True, null=True) folio = models.CharField(max_length=100, blank=True, null=True) def __str__(self): return f"Venta {self.folio or self.id}"

8. Registrar app en settings.py
Abre backend_Ford/settings.py y en INSTALLED_APPS agrega 'app_Ford',:
INSTALLED_APPS = [ 'django.contrib.admin', 'django.contrib.auth', 'django.contrib.contenttypes', 'django.contrib.sessions', 'django.contrib.messages', 'django.contrib.staticfiles', 'app_Ford', # <-- agregar ]
Además (si no está) asegúrate de tener configurado STATIC_URL = '/static/' y el TEMPLATES con DIRS: [] (las plantillas se detectan en app templates por defecto).

9. admin.py — registrar modelos
app_Ford/admin.py
from django.contrib import admin from .models import Empleado, Vehiculo, Venta @admin.register(Empleado) class EmpleadoAdmin(admin.ModelAdmin): list_display = ('nombre', 'apellido', 'puesto', 'telefono', 'email') @admin.register(Vehiculo) class VehiculoAdmin(admin.ModelAdmin): list_display = ('marca', 'modelo', 'anio', 'numero_serie', 'precio', 'cantidad_disponible') search_fields = ('marca', 'modelo', 'numero_serie') @admin.register(Venta) class VentaAdmin(admin.ModelAdmin): list_display = ('folio', 'vehiculo', 'cliente_nombre', 'fecha_venta', 'total')
Luego ejecutarás migraciones (paso 12).

10. urls.py de la app app_Ford
Crea app_Ford/urls.py:
from django.urls import path from . import views app_name = 'app_Ford' urlpatterns = [ path('', views.inicio_ford, name='inicio'), # Vehículos path('vehiculos/agregar/', views.agregar_vehiculo, name='agregar_vehiculo'), path('vehiculos/', views.ver_vehiculos, name='ver_vehiculos'), path('vehiculos/actualizar/<int:vehiculo_id>/', views.actualizar_vehiculo, name='actualizar_vehiculo'), path('vehiculos/guardar_actualizacion/<int:vehiculo_id>/', views.realizar_actualizacion_vehiculo, name='realizar_actualizacion_vehiculo'), path('vehiculos/borrar/<int:vehiculo_id>/', views.borrar_vehiculo, name='borrar_vehiculo'), ]

11. urls.py del proyecto backend_Ford/urls.py
Modifica para incluir app_Ford:
from django.contrib import admin from django.urls import path, include urlpatterns = [ path('admin/', admin.site.urls), path('', include('app_Ford.urls', namespace='app_Ford')), ]

12. views.py — funciones solicitadas (CRUD Vehículo)
app_Ford/views.py
from django.shortcuts import render, redirect, get_object_or_404 from .models import Vehiculo from django.utils import timezone # Inicio def inicio_ford(request): return render(request, 'inicio.html', {}) # Agregar vehículo (muestra form y procesa POST) def agregar_vehiculo(request): if request.method == 'POST': # Sin validaciones, directamente crear Vehiculo.objects.create( marca=request.POST.get('marca', '').strip(), modelo=request.POST.get('modelo', '').strip(), anio=int(request.POST.get('anio') or 0), color=request.POST.get('color', '').strip(), numero_serie=request.POST.get('numero_serie', '').strip(), precio=request.POST.get('precio') or 0, cantidad_disponible=int(request.POST.get('cantidad_disponible') or 1), ) return redirect('app_Ford:ver_vehiculos') return render(request, 'vehiculos/agregar_vehiculo.html', {}) # Ver vehículos def ver_vehiculos(request): vehiculos = Vehiculo.objects.all().order_by('-id') return render(request, 'vehiculos/ver_vehiculos.html', {'vehiculos': vehiculos}) # Página para editar vehículo (muestra el form con datos) def actualizar_vehiculo(request, vehiculo_id): veh = get_object_or_404(Vehiculo, id=vehiculo_id) return render(request, 'vehiculos/actualizar_vehiculo.html', {'vehiculo': veh}) # Procesar actualización def realizar_actualizacion_vehiculo(request, vehiculo_id): if request.method == 'POST': veh = get_object_or_404(Vehiculo, id=vehiculo_id) veh.marca = request.POST.get('marca', '').strip() veh.modelo = request.POST.get('modelo', '').strip() veh.anio = int(request.POST.get('anio') or veh.anio) veh.color = request.POST.get('color', '').strip() veh.numero_serie = request.POST.get('numero_serie', '').strip() veh.precio = request.POST.get('precio') or veh.precio veh.cantidad_disponible = int(request.POST.get('cantidad_disponible') or veh.cantidad_disponible) veh.save() return redirect('app_Ford:ver_vehiculos') # Borrar vehículo (confirmación GET -> mostrar, POST -> borrar) def borrar_vehiculo(request, vehiculo_id): veh = get_object_or_404(Vehiculo, id=vehiculo_id) if request.method == 'POST': veh.delete() return redirect('app_Ford:ver_vehiculos') return render(request, 'vehiculos/borrar_vehiculo.html', {'vehiculo': veh})

13. Templates principales
Crea app_Ford/templates/ y los archivos:
base.html
<!DOCTYPE html> <html lang="es"> <head> <meta charset="UTF-8" /> <meta name="viewport" content="width=device-width, initial-scale=1" /> <title>Sistema Ford</title> <!-- Bootstrap (CDN) --> <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet"> <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script> <!-- Estilos propios --> <link rel="stylesheet" href="{% static 'app_Ford/css/style.css' %}"> </head> <body class="bg-soft"> {% include 'header.html' %} {% include 'navbar.html' %} <main class="container my-4"> {% block content %}{% endblock %} </main> {% include 'footer.html' %} </body> </html>
header.html
<header class="py-3 mb-3 bg-white shadow-sm"> <div class="container d-flex align-items-center"> <img src="https://www.ford.com/etc/designs/ford-apps/brand-assets/images/f-150-logo.svg" alt="Ford" style="height:48px; margin-right:12px;"> <div> <h4 class="m-0">Sistema de Administración Ford</h4> <small class="text-muted">Proyecto: UIII_Ford_0493</small> </div> </div> </header>
navbar.html
<nav class="navbar navbar-expand-lg navbar-light bg-white border-bottom"> <div class="container"> <a class="navbar-brand d-flex align-items-center" href="{% url 'app_Ford:inicio' %}"> <span class="me-2">🚗</span> Sistema de Administración Ford </a> <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navContent"> <span class="navbar-toggler-icon"></span> </button> <div class="collapse navbar-collapse" id="navContent"> <ul class="navbar-nav ms-auto"> <li class="nav-item"><a class="nav-link" href="{% url 'app_Ford:inicio' %}">Inicio</a></li> <li class="nav-item dropdown"> <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">Vehículos 🚘</a> <ul class="dropdown-menu"> <li><a class="dropdown-item" href="{% url 'app_Ford:agregar_vehiculo' %}">Agregar vehículo</a></li> <li><a class="dropdown-item" href="{% url 'app_Ford:ver_vehiculos' %}">Ver vehículos</a></li> </ul> </li> <li class="nav-item"><a class="nav-link" href="#">Empleados 👥</a></li> <li class="nav-item"><a class="nav-link" href="#">Ventas 💳</a></li> </ul> </div> </div> </nav>
footer.html
<footer class="footer mt-auto py-3 bg-white border-top fixed-bottom"> <div class="container d-flex justify-content-between"> <div>© Derechos reservados</div> <div id="fecha-sistema">{{ now|default:None }}</div> <div>Creado por Ing. Emiliano Avilez, Cbtis 128</div> </div> </footer> <!-- Mostrar fecha con template tag simple --> {% load tz %} {% now "Y-m-d" as now %}
(NOTA: el footer está fijo. Si produce solapamiento, se puede ajustar en CSS.)
inicio.html
{% extends 'base.html' %} {% block content %} <div class="row"> <div class="col-md-7"> <h2>Bienvenido al Sistema Ford</h2> <p class="lead">Sistema de administración para vehículos. Módulo actual: Vehículos.</p> <ul> <li>Agregar, ver, editar y borrar vehículos.</li> <li>Interfaz simple con colores suaves y diseño moderno.</li> </ul> </div> <div class="col-md-5"> <img src="https://www.ford.com/cmslibs/content/dam/brand_ford/en_us/brand/resources/general/ford-blue-oval.png" alt="Ford" class="img-fluid rounded shadow-sm"> </div> </div> {% endblock %}

14. Templates de vehiculos/
Crea carpeta app_Ford/templates/vehiculos/ y archivos:
agregar_vehiculo.html
{% extends 'base.html' %} {% block content %} <h3>Agregar vehículo</h3> <form method="post"> {% csrf_token %} <div class="row g-2"> <div class="col-md-6"><input class="form-control" name="marca" placeholder="Marca" required></div> <div class="col-md-6"><input class="form-control" name="modelo" placeholder="Modelo" required></div> <div class="col-md-3"><input class="form-control" name="anio" placeholder="Año" type="number"></div> <div class="col-md-3"><input class="form-control" name="color" placeholder="Color"></div> <div class="col-md-6"><input class="form-control" name="numero_serie" placeholder="Número de serie" required></div> <div class="col-md-4"><input class="form-control" name="precio" placeholder="Precio" type="text"></div> <div class="col-md-2"><input class="form-control" name="cantidad_disponible" placeholder="Cantidad" type="number"></div> </div> <div class="mt-3"> <button class="btn btn-primary">Agregar</button> <a href="{% url 'app_Ford:ver_vehiculos' %}" class="btn btn-secondary">Cancelar</a> </div> </form> {% endblock %}
ver_vehiculos.html
{% extends 'base.html' %} {% block content %} <h3>Vehículos</h3> <a class="btn btn-success mb-3" href="{% url 'app_Ford:agregar_vehiculo' %}">+ Agregar vehículo</a> <table class="table table-striped table-bordered"> <thead class="table-light"> <tr> <th>#</th> <th>Marca</th> <th>Modelo</th> <th>Año</th> <th>N. Serie</th> <th>Precio</th> <th>Cantidad</th> <th>Acciones</th> </tr> </thead> <tbody> {% for v in vehiculos %} <tr> <td>{{ forloop.counter }}</td> <td>{{ v.marca }}</td> <td>{{ v.modelo }}</td> <td>{{ v.anio }}</td> <td>{{ v.numero_serie }}</td> <td>{{ v.precio }}</td> <td>{{ v.cantidad_disponible }}</td> <td> <a class="btn btn-sm btn-primary" href="{% url 'app_Ford:actualizar_vehiculo' v.id %}">Editar</a> <a class="btn btn-sm btn-danger" href="{% url 'app_Ford:borrar_vehiculo' v.id %}">Borrar</a> </td> </tr> {% empty %} <tr><td colspan="8" class="text-center">No hay vehículos.</td></tr> {% endfor %} </tbody> </table> {% endblock %}
actualizar_vehiculo.html
{% extends 'base.html' %} {% block content %} <h3>Actualizar vehículo</h3> <form method="post" action="{% url 'app_Ford:realizar_actualizacion_vehiculo' vehiculo.id %}"> {% csrf_token %} <div class="row g-2"> <div class="col-md-6"><input class="form-control" name="marca" value="{{ vehiculo.marca }}"></div> <div class="col-md-6"><input class="form-control" name="modelo" value="{{ vehiculo.modelo }}"></div> <div class="col-md-3"><input class="form-control" name="anio" value="{{ vehiculo.anio }}" type="number"></div> <div class="col-md-3"><input class="form-control" name="color" value="{{ vehiculo.color }}"></div> <div class="col-md-6"><input class="form-control" name="numero_serie" value="{{ vehiculo.numero_serie }}"></div> <div class="col-md-4"><input class="form-control" name="precio" value="{{ vehiculo.precio }}"></div> <div class="col-md-2"><input class="form-control" name="cantidad_disponible" value="{{ vehiculo.cantidad_disponible }}" type="number"></div> </div> <div class="mt-3"> <button class="btn btn-primary">Guardar</button> <a class="btn btn-secondary" href="{% url 'app_Ford:ver_vehiculos' %}">Cancelar</a> </div> </form> {% endblock %}
borrar_vehiculo.html
{% extends 'base.html' %} {% block content %} <h3>Eliminar vehículo</h3> <p>¿Deseas borrar el vehículo <strong>{{ vehiculo }}</strong>?</p> <form method="post"> {% csrf_token %} <button class="btn btn-danger">Sí, borrar</button> <a class="btn btn-secondary" href="{% url 'app_Ford:ver_vehiculos' %}">Cancelar</a> </form> {% endblock %}

15. Static CSS (colores suaves, modernos)
Crea app_Ford/static/app_Ford/css/style.css con contenido:
/* Colores suaves y modernos */ body.bg-soft { background: #f6f8fa; color: #2c3e50; } header, .navbar, footer { background: white; } h2, h3 { color: #1f3b5a; } .footer { font-size: 0.9rem; } .table thead { background: #f1f5f9; } .btn-primary { background-color: #3b82f6; border-color: #3b82f6; } .btn-success { background-color: #34d399; border-color: #34d399; }

16. Habilitar static files en templates
Asegúrate de cargar static en base.html si no se cargó:
En base.html arriba (después de <body> o en top of template) agrega {% load static %} si no está. (Ya fue referenciado en el ejemplo.)

17. Migraciones (makemigrations / migrate)
Desde la raíz del proyecto (UIII_Ford_0493):
python manage.py makemigrations app_Ford python manage.py migrate
Si añadiste modelos antes, podrías hacer simplemente:
python manage.py makemigrations python manage.py migrate

18. Crear superusuario (opcional para admin)
python manage.py createsuperuser # completa usuario, email y contraseña
Accederás a /admin/ para ver Empleado, Vehiculo, Venta.

19. Ejecutar servidor en el puerto 8093
python manage.py runserver 8093
Luego copia el link que muestra (por ejemplo http://127.0.0.1:8093/) y pégalo en el navegador.

20. Notas importantes / Requerimientos cumplidos
Trabajamos solamente con Vehículo (Empleado y Venta registrados pero pendientes de usar).
No se utiliza forms.py. Los formularios son HTML simples y procesados desde views.py con request.POST.
No validaciones (tal como pediste).
Plantillas: base.html, header.html, navbar.html, footer.html, inicio.html.
Subcarpeta vehiculos con los cuatro templates (agregar/ver/actualizar/borrar).
Admin: modelos registrados.
Colores suaves y estilo sencillo en style.css.
Estructura completa de carpetas listada.
Servidor en puerto 8093.

21. Comandos resumen (rápido)
mkdir UIII_Ford_0493 && cd UIII_Ford_0493 python -m venv .venv # activar .venv pip install django django-admin startproject backend_Ford . python manage.py startapp app_Ford # realizar las ediciones de archivos como se muestran arriba... python manage.py makemigrations python manage.py migrate python manage.py createsuperuser # opcional python manage.py runserver 8093

Si quieres, te doy ahora:
los archivos listos para descargar (zipped) — puedo generarlos aquí,
o puedo pegar cualquier archivo completo que prefieras (por ejemplo views.py o settings.py) si lo quieres exactamente listo para crear.
¿Qué prefieres ahora? (Puedo pegar cualquier archivo completo que te falte o generar un zip con todo listo).
Tú dijiste:
siguele hasta el 31
ChatGPT Plus
Entendido, continúo la numeración de procedimientos hasta el 31, siguiendo el formato que traías en el proyecto Ford con Django:

✅ Continuación de Procedimientos Proyecto Ford – Django (del 21 al 31)
Crear archivo urls.py dentro de la app ford
type nul > ford/urls.py
En Linux/Mac: touch ford/urls.py
Configurar rutas en ford/urls.py
from django.urls import path from . import views urlpatterns = [ path('', views.inicio, name='inicio'), ]
Registrar rutas de la app en FordProject/urls.py
from django.contrib import admin from django.urls import path, include urlpatterns = [ path('admin/', admin.site.urls), path('', include('ford.urls')), ]
Crear carpeta templates dentro de la app
mkdir ford/templates
Crear archivo inicio.html en ford/templates
<!DOCTYPE html> <html lang="es"> <head> <meta charset="UTF-8"> <title>Ford México</title> </head> <body> <h1>Bienvenido al Proyecto Ford</h1> </body> </html>
Crear función inicio en views.py
from django.shortcuts import render def inicio(request): return render(request, 'inicio.html')
Configurar carpeta de templates en settings.py
Buscar TEMPLATES y modificar:
'DIRS': [BASE_DIR / 'ford/templates'],
Crear carpeta static dentro de la app
mkdir ford/static mkdir ford/static/css mkdir ford/static/img mkdir ford/static/js
Configurar archivos estáticos en settings.py
STATICFILES_DIRS = [ BASE_DIR / 'ford/static' ]
Crear archivo CSS principal ford/static/css/style.css
body{ background-color: #f2f2f2; font-family: Arial, Helvetica, sans-serif; } h1{ color: navy; }
Probar el servidor nuevamente
python manage.py runserver

