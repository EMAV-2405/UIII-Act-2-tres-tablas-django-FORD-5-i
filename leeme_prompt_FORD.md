Claro, aquí tienes el procedimiento detallado paso a paso, incluyendo los comandos y el código necesario para crear tu proyecto Ford en Django, enfocándonos en el CRUD de Vehículos.

-----

### 1-3: Configuración Inicial y VS Code

1.  **Crear Carpeta del Proyecto:**

      * Abre una terminal (CMD, PowerShell, o la terminal de tu S.O.)
      * Navega hasta donde quieras crear tu proyecto (ej. `cd Documents/Projects`).
      * Crea la carpeta:
        ```bash
        mkdir UIII_Ford_0493
        ```

2.  **Abrir VS Code sobre la Carpeta:**

      * En la misma terminal, escribe:
        ```bash
        code .
        ```

    *(Esto abrirá VS Code en la carpeta `UIII_Ford_0493`)*

3.  **Abrir Terminal en VS Code:**

      * Usa el atajo: **`Ctrl + Ñ`** (o **` Ctrl + Shift +  `**\`\*\* si tu teclado está en inglés).
      * O ve al menú: `Terminal` \> `Nueva terminal`.

-----

### 4-8: Entorno Virtual y Django

4.  **Crear Carpeta Entorno Virtual:**

      * En la terminal de VS Code, ejecuta:
        ```bash
        python -m venv .venv
        ```

    *(Espera a que se cree la carpeta `.venv`)*

5.  **Activar el Entorno Virtual:**

      * **En Windows (PowerShell/CMD):**
        ```bash
        .\.venv\Scripts\activate
        ```
      * **En macOS/Linux (Bash/Zsh):**
        ```bash
        source .venv/bin/activate
        ```

    *(Deberías ver `(.venv)` al inicio de tu línea de comandos)*

6.  **Activar Intérprete de Python (en VS Code):**

      * Usa el atajo: **`Ctrl + Shift + P`** para abrir la paleta de comandos.
      * Escribe y selecciona: `Python: Select Interpreter` (Python: Seleccionar Intérprete).
      * Elige el intérprete que tenga la ruta `.\.venv\Scripts\python.exe` (o similar).

7.  **Instalar Django:**

      * En la terminal de VS Code (con el entorno activado), ejecuta:
        ```bash
        pip install django
        ```

8.  **Crear Proyecto `backend_Ford` (sin duplicar carpeta):**

      * El punto `.` al final es crucial.
        ```bash
        django-admin startproject backend_Ford .
        ```

    *(Esto crea `manage.py` y la carpeta `backend_Ford` dentro de `UIII_Ford_0493`)*

-----

### 9-11: Servidor y Aplicación

9.  **Ejecutar Servidor en Puerto 8093:**

      * Prueba que la instalación funcione:
        ```bash
        python manage.py runserver 8093
        ```

10. **Copiar y Pegar el Link:**

      * Abre tu navegador web (Chrome, Firefox, etc.).
      * Copia el enlace que aparece en la terminal (usualmente `http://127.0.0.1:8093/`) y pégalo en la barra de direcciones.
      * Deberías ver la página de bienvenida de Django.
      * Presiona **`Ctrl + C`** en la terminal para detener el servidor.

11. **Crear Aplicación `app_Ford`:**

      * Ejecuta:
        ```bash
        python manage.py startapp app_Ford
        ```

    *(Esto creará la carpeta `app_Ford`)*

-----

### 12-12.5: Modelos y Migraciones

12. **Definir los Modelos:**

      * Abre el archivo `app_Ford/models.py`.
      * Borra el contenido que viene por defecto y pega el siguiente código:

    <!-- end list -->

    ```python
    # app_Ford/models.py
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
    ```

12.5. **Realizar las Migraciones:**
\* En la terminal, ejecuta estos dos comandos en orden:
\* **Paso 1: `makemigrations`** (Prepara los cambios de los modelos)
` bash python manage.py makemigrations  `
\* **Paso 2: `migrate`** (Aplica los cambios a la base de datos)
` bash python manage.py migrate  `

-----

### 13-14: Vistas (Views) de Vehículo

13. **Enfoque en Vehículo:** (Confirmado, solo trabajaremos en el CRUD de Vehículo).

14. **Crear las Funciones en `app_Ford/views.py`:**

      * Abre `app_Ford/views.py` y reemplaza su contenido con este código:

    <!-- end list -->

    ```python
    # app_Ford/views.py
    from django.shortcuts import render, redirect, get_object_or_404
    from .models import Vehiculo
    # Importaremos los otros modelos cuando los necesitemos
    # from .models import Empleado, Venta 

    # ==========================================
    # VISTAS PRINCIPALES
    # ==========================================

    def inicio_ford(request):
        """
        Renderiza la página de inicio.
        """
        return render(request, 'inicio.html')

    # ==========================================
    # VISTAS CRUD DE VEHÍCULO
    # ==========================================

    def agregar_vehiculo(request):
        """
        Vista para agregar un nuevo vehículo.
        Maneja GET (mostrar formulario) y POST (guardar datos).
        """
        if request.method == "POST":
            # Recoger datos del formulario (sin validación, como se solicitó)
            marca = request.POST.get('marca')
            modelo = request.POST.get('modelo')
            anio = request.POST.get('anio')
            color = request.POST.get('color')
            numero_serie = request.POST.get('numero_serie')
            precio = request.POST.get('precio')
            cantidad = request.POST.get('cantidad_disponible')

            # Crear y guardar el nuevo objeto Vehiculo
            Vehiculo.objects.create(
                marca=marca,
                modelo=modelo,
                anio=anio,
                color=color,
                numero_serie=numero_serie,
                precio=precio,
                cantidad_disponible=cantidad
            )
            # Redirigir a la lista de vehículos después de guardar
            return redirect('ver_vehiculos')
        
        # Si es GET, solo muestra el formulario
        return render(request, 'vehiculos/agregar_vehiculo.html')


    def ver_vehiculos(request):
        """
        Muestra una lista de todos los vehículos en la base de datos.
        """
        # Obtener todos los objetos Vehiculo
        todos_los_vehiculos = Vehiculo.objects.all()
        # Preparar el contexto para la plantilla
        contexto = {
            'vehiculos': todos_los_vehiculos
        }
        return render(request, 'vehiculos/ver_vehiculos.html', contexto)


    def actualizar_vehiculo(request, id):
        """
        Muestra el formulario con los datos actuales del vehículo 
        para que el usuario pueda editarlos. (Maneja GET)
        """
        # Buscar el vehículo por su ID o mostrar error 404 si no existe
        vehiculo_a_actualizar = get_object_or_404(Vehiculo, id=id)
        
        contexto = {
            'vehiculo': vehiculo_a_actualizar
        }
        return render(request, 'vehiculos/actualizar_vehiculo.html', contexto)


    def realizar_actualizacion_vehiculo(request, id):
        """
        Procesa los datos enviados desde el formulario de actualización. 
        (Maneja POST)
        """
        # Buscar el vehículo que vamos a actualizar
        vehiculo_a_actualizar = get_object_or_404(Vehiculo, id=id)
        
        if request.method == "POST":
            # Actualizar los campos del objeto con los datos del POST
            vehiculo_a_actualizar.marca = request.POST.get('marca')
            vehiculo_a_actualizar.modelo = request.POST.get('modelo')
            vehiculo_a_actualizar.anio = request.POST.get('anio')
            vehiculo_a_actualizar.color = request.POST.get('color')
            vehiculo_a_actualizar.numero_serie = request.POST.get('numero_serie')
            vehiculo_a_actualizar.precio = request.POST.get('precio')
            vehiculo_a_actualizar.cantidad_disponible = request.POST.get('cantidad_disponible')
            
            # Guardar los cambios en la base de datos
            vehiculo_a_actualizar.save()
            
            # Redirigir a la lista de vehículos
            return redirect('ver_vehiculos')
        
        # Si alguien intenta acceder a esta URL por GET, lo redirigimos
        return redirect('ver_vehiculos')


    def borrar_vehiculo(request, id):
        """
        Elimina un vehículo de la base de datos.
        """
        # Buscar el vehículo a eliminar
        vehiculo_a_borrar = get_object_or_404(Vehiculo, id=id)
        
        # Eliminar el objeto
        vehiculo_a_borrar.delete()
        
        # Redirigir a la lista de vehículos
        return redirect('ver_vehiculos')
    ```

-----

### 15-20: Plantillas Base (Templates)

15. **Crear Carpeta `templates`:**

      * Dentro de la carpeta `app_Ford`, crea una nueva carpeta llamada `templates`.
      * Ruta: `UIII_Ford_0493/app_Ford/templates/`

16. **Crear Archivos HTML Base:**

      * Dentro de `app_Ford/templates/`, crea los siguientes 5 archivos:

17. **Código para `base.html` (Con Bootstrap y Bootstrap Icons):**

    ```html
    {% load static %}
    <!DOCTYPE html>
    <html lang="es">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>{% block titulo %}Sistema Ford{% endblock %}</title>
        
        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" 
              rel="stylesheet" 
              integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" 
              crossorigin="anonymous">
        
        <link rel="stylesheet" 
              href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.3/font/bootstrap-icons.min.css">

        <style>
            body {
                /* Un color de fondo suave */
                background-color: #f8f9fa; 
                /* Padding para evitar que el footer fijo tape contenido */
                padding-bottom: 100px; 
            }
            /* Colores suaves para la navegación */
            .navbar-custom {
                background-color: #f0f3f5;
            }
            .footer-custom {
                background-color: #e9ecef;
            }
        </style>
    </head>
    <body class="d-flex flex-column min-vh-100">

        {% include 'header.html' %}

        {% include 'navbar.html' %}

        <main class="container mt-4 flex-grow-1">
            {% block contenido %}
            {% endblock %}
        </main>

        {% include 'footer.html' %}

        <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" 
                integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" 
                crossorigin="anonymous"></script>
    </body>
    </html>
    ```

18. **Código para `navbar.html`:**

    ```html
    <nav class="navbar navbar-expand-lg navbar-custom border-bottom shadow-sm">
        <div class="container">
            <a class="navbar-brand fw-bold text-primary" href="{% url 'inicio' %}">
                <i class="bi bi-car-front-fill"></i>
                Sistema de Administración Ford
            </a>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNavDropdown">
                <ul class="navbar-nav ms-auto">
                    
                    <li class="nav-item">
                        <a class="nav-link active" aria-current="page" href="{% url 'inicio' %}">
                            <i class="bi bi-house-door"></i> Inicio
                        </a>
                    </li>
                    
                    <li class="nav-item dropdown">
                        <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownVehiculos" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                            <i class="bi bi-truck"></i> Vehículos
                        </a>
                        <ul class="dropdown-menu" aria-labelledby="navbarDropdownVehiculos">
                            <li><a class="dropdown-item" href="{% url 'agregar_vehiculo' %}">Agregar vehículo</a></li>
                            <li><a class="dropdown-item" href="{% url 'ver_vehiculos' %}">Ver vehículos</a></li>
                            </ul>
                    </li>
                    
                    <li class="nav-item">
                        <a class="nav-link disabled" href="#">
                            <i class="bi bi-people"></i> Empleados
                        </a>
                    </li>
                    
                    <li class="nav-item">
                        <a class="nav-link disabled" href="#">
                            <i class="bi bi-cash-coin"></i> Ventas
                        </a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    ```

19. **Código para `footer.html` (Fijo al final):**

    ```html
    {% load tz %} <footer class="footer mt-auto py-3 footer-custom border-top fixed-bottom">
        <div class="container text-center">
            <span class="text-muted">
                &copy; {% now "Y" %} Derechos de Autor | 
                Fecha del Sistema: {% now "d/m/Y H:i" %} |
                Creado por Ing. Emiliano Avilez, Cbtis 128
            </span>
        </div>
    </footer>
    ```

    *Nota: Para que `{% now %}` funcione correctamente, debemos asegurarnos que `django.template.context_processors.tz` esté en `TEMPLATES` en `settings.py`. (Django usualmente lo incluye por defecto).*

20. **Código para `inicio.html`:**

    ```html
    {% extends 'base.html' %}

    {% block titulo %}Inicio - Ford{% endblock %}

    {% block contenido %}
    <div class="container mt-5">
        <div class="row">
            <div class="col-md-7">
                <h1 class="display-4 fw-bold">Bienvenido al Sistema de Administración Ford</h1>
                <p class="lead">
                    Este sistema permite gestionar los vehículos, empleados y ventas
                    de la concesionaria.
                </p>
                <hr class="my-4">
                <p>
                    Utilice la barra de navegación superior para acceder a las 
                    diferentes secciones del sistema.
                </p>
                <ul>
                    <li><strong>Vehículos:</strong> Agregue nuevos autos, vea el inventario, actualice precios y elimine unidades.</li>
                    <li><strong>Empleados:</strong> (Próximamente) Gestión del personal.</li>
                    <li><strong>Ventas:</strong> (Próximamente) Registro de ventas y facturación.</li>
                </ul>
            </div>
            <div class="col-md-5 d-flex align-items-center justify-content-center">
                <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/3e/Ford_logo_2003.svg/2560px-Ford_logo_2003.svg.png" 
                     class="img-fluid rounded shadow-sm" 
                     alt="Logo de Ford">
                
            </div>
        </div>
    </div>
    {% endblock %}
    ```

**Código para `header.html` (Opcional, pero solicitado):**
*(Puedes dejarlo vacío si no lo necesitas, o poner un logo simple)*

```html
```

-----

### 21-23: Plantillas CRUD de Vehículo

21. **Crear Subcarpeta `vehiculos`:**

      * Dentro de `app_Ford/templates/`, crea una carpeta llamada `vehiculos`.
      * Ruta: `UIII_Ford_0493/app_Ford/templates/vehiculos/`

22. **Crear Archivos HTML del CRUD:**

      * Dentro de `app_Ford/templates/vehiculos/`, crea los siguientes 4 archivos:

**Código para `agregar_vehiculo.html`:**
*(Usamos un formulario HTML estándar, sin `forms.py`)*

```html
{% extends 'base.html' %}

{% block titulo %}Agregar Vehículo{% endblock %}

{% block contenido %}
<div class="row justify-content-center">
    <div class="col-md-8">
        <div class="card shadow-sm">
            <div class="card-header bg-primary text-white">
                <h3 class="mb-0">Agregar Nuevo Vehículo</h3>
            </div>
            <div class="card-body" style="background-color: #fdfdfd;">
                
                <form action="" method="POST">
                    {% csrf_token %} <div class="row">
                        <div class="col-md-6 mb-3">
                            <label for="marca" class="form-label">Marca</label>
                            <input type="text" class="form-control" id="marca" name="marca" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label for="modelo" class="form-label">Modelo</label>
                            <input type="text" class="form-control" id="modelo" name="modelo" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label for="anio" class="form-label">Año</label>
                            <input type="number" class="form-control" id="anio" name="anio" min="1990" max="2030" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label for="color" class="form-label">Color</label>
                            <input type="text" class="form-control" id="color" name="color">
                        </div>
                    </div>

                    <div class="row">
                        <div class="col-md-12 mb-3">
                            <label for="numero_serie" class="form-label">Número de Serie (VIN)</label>
                            <input type="text" class="form-control" id="numero_serie" name="numero_serie" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label for="precio" class="form-label">Precio (MXN)</label>
                            <input type="number" class="form-control" id="precio" name="precio" step="0.01" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label for="cantidad_disponible" class="form-label">Cantidad Disponible</label>
                            <input type="number" class="form-control" id="cantidad_disponible" name="cantidad_disponible" value="1" min="0" required>
                        </div>
                    </div>

                    <hr>
                    
                    <div class="d-grid gap-2 d-md-flex justify-content-md-end">
                        <a href="{% url 'ver_vehiculos' %}" class="btn btn-outline-secondary">
                            Cancelar
                        </a>
                        <button type="submit" class="btn btn-primary">
                            <i class="bi bi-floppy-fill"></i> Guardar Vehículo
                        </button>
                    </div>

                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

**Código para `ver_vehiculos.html` (Tabla):**

```html
{% extends 'base.html' %}

{% block titulo %}Ver Vehículos{% endblock %}

{% block contenido %}
<div class="d-flex justify-content-between align-items-center mb-3">
    <h1 class="mb-0">Inventario de Vehículos</h1>
    <a href="{% url 'agregar_vehiculo' %}" class="btn btn-success">
        <i class="bi bi-plus-circle"></i> Agregar Nuevo
    </a>
</div>

<div class="card shadow-sm">
    <div class="card-body">
        <div class="table-responsive">
            
            <table class="table table-hover table-striped align-middle">
                <thead class="table-light">
                    <tr>
                        <th scope="col">ID</th>
                        <th scope="col">Marca</th>
                        <th scope="col">Modelo</th>
                        <th scope="col">Año</th>
                        <th scope="col">Color</th>
                        <th scope="col">No. Serie</th>
                        <th scope="col">Precio (MXN)</th>
                        <th scope="col">Disponibles</th>
                        <th scope="col" class="text-center">Acciones</th>
                    </tr>
                </thead>
                <tbody>
                    {% for v in vehiculos %}
                    <tr>
                        <th scope="row">{{ v.id }}</th>
                        <td>{{ v.marca }}</td>
                        <td>{{ v.modelo }}</td>
                        <td>{{ v.anio }}</td>
                        <td>{{ v.color|default:"N/A" }}</td>
                        <td>{{ v.numero_serie }}</td>
                        <td>${{ v.precio }}</td>
                        <td>{{ v.cantidad_disponible }}</td>
                        <td class="text-center">
                            <a href="{% url 'actualizar_vehiculo' v.id %}" 
                               class="btn btn-warning btn-sm" 
                               title="Editar">
                                <i class="bi bi-pencil-square"></i>
                            </a>
                            
                            <button type="button" class="btn btn-danger btn-sm" 
                                    data-bs-toggle="modal" 
                                    data-bs-target="#confirmarBorrarModal{{ v.id }}"
                                    title="Borrar">
                                <i class="bi bi-trash3-fill"></i>
                            </button>

                            <div class="modal fade" id="confirmarBorrarModal{{ v.id }}" tabindex="-1" aria-labelledby="modalLabel{{ v.id }}" aria-hidden="true">
                                <div class="modal-dialog">
                                    <div class="modal-content">
                                        <div class="modal-header">
                                            <h5 class="modal-title" id="modalLabel{{ v.id }}">Confirmar Eliminación</h5>
                                            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                                        </div>
                                        <div class="modal-body text-start">
                                            ¿Estás seguro de que deseas eliminar el vehículo: 
                                            <strong>{{ v.marca }} {{ v.modelo }} ({{ v.anio }})</strong>?
                                            <p class="text-danger mt-2">Esta acción no se puede deshacer.</p>
                                        </div>
                                        <div class="modal-footer">
                                            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancelar</button>
                                            <a href="{% url 'borrar_vehiculo' v.id %}" class="btn btn-danger">
                                                Eliminar Permanentemente
                                            </a>
                                        </div>
                                    </div>
                                </div>
                            </div>

                        </td>
                    </tr>
                    {% empty %}
                    <tr>
                        <td colspan="9" class="text-center text-muted">
                            No hay vehículos registrados en el inventario.
                        </td>
                    </tr>
                    {% endfor %}
                </tbody>
            </table>

        </div>
    </div>
</div>

{% endblock %}
```

**Código para `actualizar_vehiculo.html`:**
*(Nota: El `action` del formulario apunta a `realizar_actualizacion_vehiculo`)*

```html
{% extends 'base.html' %}

{% block titulo %}Actualizar Vehículo{% endblock %}

{% block contenido %}
<div class="row justify-content-center">
    <div class="col-md-8">
        <div class="card shadow-sm">
            <div class="card-header bg-warning text-dark">
                <h3 class="mb-0">Actualizar Vehículo (ID: {{ vehiculo.id }})</h3>
            </div>
            <div class="card-body" style="background-color: #fdfdfd;">
                
                <form action="{% url 'realizar_actualizacion_vehiculo' vehiculo.id %}" method="POST">
                    {% csrf_token %} <div class="row">
                        <div class="col-md-6 mb-3">
                            <label for="marca" class="form-label">Marca</label>
                            <input type="text" class="form-control" id="marca" name="marca" value="{{ vehiculo.marca }}" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label for="modelo" class="form-label">Modelo</label>
                            <input type="text" class="form-control" id="modelo" name="modelo" value="{{ vehiculo.modelo }}" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label for="anio" class="form-label">Año</label>
                            <input type="number" class="form-control" id="anio" name="anio" value="{{ vehiculo.anio }}" min="1990" max="2030" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label for="color" class="form-label">Color</label>
                            <input type="text" class="form-control" id="color" name="color" value="{{ vehiculo.color }}">
                        </div>
                    </div>

                    <div class="row">
                        <div class="col-md-12 mb-3">
                            <label for="numero_serie" class="form-label">Número de Serie (VIN)</label>
                            <input type="text" class="form-control" id="numero_serie" name="numero_serie" value="{{ vehiculo.numero_serie }}" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label for="precio" class="form-label">Precio (MXN)</label>
                            <input type="number" class="form-control" id="precio" name="precio" value="{{ vehiculo.precio }}" step="0.01" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label for="cantidad_disponible" class="form-label">Cantidad Disponible</label>
                            <input type="number" class="form-control" id="cantidad_disponible" name="cantidad_disponible" value="{{ vehiculo.cantidad_disponible }}" min="0" required>
                        </div>
                    </div>

                    <hr>
                    
                    <div class="d-grid gap-2 d-md-flex justify-content-md-end">
                        <a href="{% url 'ver_vehiculos' %}" class="btn btn-outline-secondary">
                            Cancelar
                        </a>
                        <button type="submit" class="btn btn-warning">
                            <i class="bi bi-save-fill"></i> Guardar Cambios
                        </button>
                    </div>

                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

**Código para `borrar_vehiculo.html`:**
*(No es estrictamente necesario, ya que usamos un Modal de Bootstrap y el borrado es directo en la vista `borrar_vehiculo`, pero si quisieras una página de confirmación dedicada, aquí estaría).*
**Nota:** Dado que la vista `borrar_vehiculo` en el Paso 14 elimina y redirige, este archivo `borrar_vehiculo.html` no se utiliza en el flujo actual (el modal lo reemplaza).

-----

### 24-26: Configuración de URLs y Settings

24. **Crear `urls.py` en `app_Ford`:**

      * Dentro de la carpeta `app_Ford`, crea un nuevo archivo llamado `urls.py`.
      * Pega el siguiente contenido:

    <!-- end list -->

    ```python
    # app_Ford/urls.py
    from django.urls import path
    from . import views

    urlpatterns = [
        # ==========================================
        # URLS PRINCIPALES
        # ==========================================
        path('', views.inicio_ford, name='inicio'),

        # ==========================================
        # URLS CRUD DE VEHÍCULO
        # ==========================================
        
        # Create (Agregar)
        path('vehiculos/agregar/', views.agregar_vehiculo, name='agregar_vehiculo'),
        
        # Read (Ver)
        path('vehiculos/ver/', views.ver_vehiculos, name='ver_vehiculos'),
        
        # Update (Actualizar)
        # Paso 1: Mostrar el formulario de actualización (GET)
        path('vehiculos/actualizar/<int:id>/', views.actualizar_vehiculo, name='actualizar_vehiculo'),
        # Paso 2: Procesar la actualización (POST)
        path('vehiculos/realizar_actualizacion/<int:id>/', views.realizar_actualizacion_vehiculo, name='realizar_actualizacion_vehiculo'),

        # Delete (Borrar)
        path('vehiculos/borrar/<int:id>/', views.borrar_vehiculo, name='borrar_vehiculo'),
    ]
    ```

25. **Agregar `app_Ford` a `settings.py`:**

      * Abre el archivo `backend_Ford/settings.py`.
      * Busca la lista `INSTALLED_APPS`.
      * Agrega `'app_Ford'` al inicio (o final) de la lista:

    <!-- end list -->

    ```python
    # backend_Ford/settings.py

    INSTALLED_APPS = [
        'app_Ford', # <--- AÑADIR ESTA LÍNEA
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
    ]
    ```

26. **Configurar `urls.py` de `backend_Ford`:**

      * Abre el archivo `backend_Ford/urls.py`.
      * Necesitas importar `include` y añadir la ruta de `app_Ford`.
      * Reemplaza el contenido con esto:

    <!-- end list -->

    ```python
    # backend_Ford/urls.py
    from django.contrib import admin
    from django.urls import path, include  # <-- Asegúrate de importar 'include'

    urlpatterns = [
        path('admin/', admin.site.urls),
        
        # Añade esta línea para conectar con app_Ford/urls.py
        # Cualquier URL que no sea 'admin/' será manejada por 'app_Ford.urls'
        path('', include('app_Ford.urls')), 
    ]
    ```

-----

### 27-31: Admin, Migraciones Finales y Ejecución

27. **Registrar Modelos en `admin.py` y Migrar:**

      * Abre `app_Ford/admin.py`.
      * Pega el siguiente código para registrar los 3 modelos (aunque solo usemos Vehículo por ahora):

    <!-- end list -->

    ```python
    # app_Ford/admin.py
    from django.contrib import admin
    from .models import Empleado, Vehiculo, Venta

    # Registrar los modelos para que aparezcan en el panel de admin
    admin.site.register(Empleado)
    admin.site.register(Vehiculo)
    admin.site.register(Venta)
    ```

      * (Opcional pero recomendado) Como modificaste el `admin.py`, y por si acaso, vuelve a correr migraciones (aunque no debería haber cambios en los modelos):
        ```bash
        python manage.py makemigrations
        python manage.py migrate
        ```

28. (Colores suaves y código sencillo: Aplicado en HTML/CSS/Vistas)

29. (Estructura completa: Realizada)

30. (Proyecto funcional: Listo para probar)

31. **Finalmente: Ejecutar Servidor en Puerto 8093:**

      * ¡Estás listo\! Corre el servidor:
        ```bash
        python manage.py runserver 8093
        ```
      * Abre tu navegador en `http://127.0.0.1:8093/`.
      * Deberías ver la página de inicio.
      * Navega a `Vehículos` \> `Ver vehículos` o `Agregar vehículo` para probar el CRUD.

-----

Claro, aquí está la estructura de carpetas y archivos principales de tu proyecto `UIII_Ford_0493` después de seguir todos los pasos:

```
UIII_Ford_0493/
│
├── .venv/                      <-- Carpeta del Entorno Virtual
│
├── app_Ford/                   <-- Tu aplicación Django
│   ├── migrations/             <-- Archivos de migración de la BD
│   │   ├── __init__.py
│   │   └── 0001_initial.py     <-- (Este se crea con makemigrations)
│   │
│   ├── templates/              <-- Carpeta de plantillas HTML
│   │   ├── vehiculos/          <-- Subcarpeta para el CRUD de Vehículos
│   │   │   ├── agregar_vehiculo.html
│   │   │   ├── actualizar_vehiculo.html
│   │   │   └── ver_vehiculos.html
│   │   │
│   │   ├── base.html           <-- Plantilla principal (con Bootstrap)
│   │   ├── footer.html
│   │   ├── header.html
│   │   ├── inicio.html
│   │   └── navbar.html
│   │
│   ├── __init__.py
│   ├── admin.py                <-- (Donde registraste los modelos)
│   ├── apps.py
│   ├── models.py               <-- (Donde están Empleado, Vehiculo, Venta)
│   ├── tests.py
│   ├── urls.py                 <-- (El que creaste manualmente para la app)
│   └── views.py                <-- (Donde están inicio_ford, agregar_vehiculo, etc.)
│
├── backend_Ford/               <-- Carpeta del Proyecto (Configuración)
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py             <-- (Donde añadiste 'app_Ford' a INSTALLED_APPS)
│   ├── urls.py                 <-- (El principal, donde usaste 'include')
│   └── wsgi.py
│
├── db.sqlite3                  <-- Tu base de datos (se crea con 'migrate')
│
└── manage.py                   <-- El script para administrar el proyecto
```
