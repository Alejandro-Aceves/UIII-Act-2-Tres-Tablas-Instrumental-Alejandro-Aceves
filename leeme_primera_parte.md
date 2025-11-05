# Proyecto: UIII Act 2 Tres Tablas Prompt Alejandro Aceves 5J

> Raíz del proyecto: `UIII Act 2 Tres Tablas Prompt Alejandro Aceves 5J`  
> Dentro de esta carpeta se coloca el proyecto Django `backend_instrumental` y la app `app_instrumental`.

---

## Estructura de carpetas (sugerida)
```
UIII Act 2 Tres Tablas Prompt Alejandro Aceves 5J/
├─ backend_instrumental/        # Django project
│  │  ├─ __init__.py
│  │  ├─ settings.py
│  │  ├─ urls.py
│  │  └─ wsgi.py
├─ app_instrumental/            # Django app
│  ├─ migrations/
│  ├─ templates/
│  │  ├─ base.html
│  │  ├─ header.html
│  │  ├─ navbar.html
│  │  ├─ footer.html
│  │  ├─ inicio.html
│  │  └─ instrumentos/
│  │     ├─ agregar_instrumento.html
│  │     ├─ ver_instrumento.html
│  │     ├─ actualizar_instrumento.html
│  │     └─ borrar_instrumento.html
│  ├─ admin.py
│  ├─ apps.py
│  ├─ models.py
│  ├─ views.py
│  ├─ urls.py
│  └─ __init__.py
│  ├─ manage.py
├─ .venv/
```

---

## Comandos (terminal) - pasos para crear y ejecutar el proyecto

```bash
# 1. Crear carpeta raíz
mkdir "UIII Act 2 Tres Tablas Prompt Alejandro Aceves 5J"
cd "UIII Act 2 Tres Tablas Prompt Alejandro Aceves 5J"

# 2. Crear entorno virtual (".venv") y activarlo (Windows / Linux/macOS)
python -m venv .venv

# Windows (PowerShell)
.venv\Scripts\Activate.ps1

# Windows (cmd)
.venv\Scripts\activate

# Linux / macOS
source .venv/bin/activate

# 3. Activar intérprete de Python en VSCode: seleccionar la carpeta .venv como intérprete (UI en VSCode)

# 4. Instalar Django
pip install django

# 5. Crear proyecto Django (nombre del proyecto: backend_instrumental)
django-admin startproject backend_instrumental .

# Nota: el '.' inicial crea el proyecto en la carpeta actual (raíz solicitada).

# 6. Crear la app app_instrumental
python manage.py startapp app_instrumental

# 7. Realizar migraciones iniciales
python manage.py makemigrations
python manage.py migrate

# 8. Crear superusuario (opcional)
python manage.py createsuperuser

# 9. Ejecutar servidor en el puerto 8036 (instrucción final del documento)
python manage.py runserver 8036

# (Alternativa mencionada en el documento: puerto 8429)
# python manage.py runserver 8429
```

---

## backend_instrumental/settings.py (fragmentos importantes)
```python
# Agrega 'app_instrumental' a INSTALLED_APPS
INSTALLED_APPS = [
    # apps Django por defecto...
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    # app del proyecto
    'app_instrumental',
]

# Configura templates (asegúrate que DIRS incluya la carpeta templates global si necesitas)
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],  # usando templates dentro de cada app (app_instrumental/templates)
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

---

## backend_instrumental/urls.py (proyecto)
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_instrumental.urls')),
]
```

---

## app_instrumental/models.py
```python
from django.db import models

# ==========================================
# MODELO: INSTRUMENTO
# ==========================================
class Instrumento(models.Model):
    id_instrumento = models.AutoField(primary_key=True)
    nombre = models.CharField(max_length=255)
    marca = models.CharField(max_length=255)
    modelo = models.CharField(max_length=255)
    precio_venta = models.DecimalField(max_digits=10, decimal_places=2)
    cantidad_stock = models.IntegerField()
    categoria = models.CharField(max_length=255)
    fecha_ingreso = models.DateField()

    def __str__(self):
        return self.nombre

# ==========================================
# MODELO: VENTA  (dejado para futura implementación)
# ==========================================
class Venta(models.Model):
    id_venta = models.AutoField(primary_key=True)
    fecha_venta = models.DateField()
    total_venta = models.DecimalField(max_digits=10, decimal_places=2)
    iva_aplicado = models.DecimalField(max_digits=5, decimal_places=2)
    no_transaccion = models.CharField(max_length=255)
    nombre_vendedor = models.CharField(max_length=255)
    registro_caja = models.CharField(max_length=255)

    def __str__(self):
        return f"Venta {self.id_venta} - {self.fecha_venta}"

# ==========================================
# MODELO: DETALLE_VENTA  (dejado para futura implementación)
# ==========================================
class DetalleVenta(models.Model):
    id_venta = models.ForeignKey(Venta, on_delete=models.CASCADE)
    id_instrumento = models.ForeignKey(Instrumento, on_delete=models.CASCADE)
    cantidad = models.IntegerField()
    precio_unitario = models.DecimalField(max_digits=10, decimal_places=2)
    subtotal = models.DecimalField(max_digits=10, decimal_places=2)
    costo_instrumento = models.DecimalField(max_digits=10, decimal_places=2)
    no_linea = models.IntegerField()
    nota_detalle = models.CharField(max_length=255, blank=True, null=True)
    porcentaje_descuento_item = models.DecimalField(max_digits=5, decimal_places=2, default=0.00)

    class Meta:
        unique_together = ('id_venta', 'no_linea')

    def __str__(self):
        return f"Detalle Venta {self.id_venta.id_venta} - Línea {self.no_linea}"
```

---

## app_instrumental/admin.py
```python
from django.contrib import admin
from .models import Instrumento, Venta, DetalleVenta

@admin.register(Instrumento)
class InstrumentoAdmin(admin.ModelAdmin):
    list_display = ('id_instrumento', 'nombre', 'marca', 'modelo', 'precio_venta', 'cantidad_stock', 'categoria', 'fecha_ingreso')

@admin.register(Venta)
class VentaAdmin(admin.ModelAdmin):
    list_display = ('id_venta', 'fecha_venta', 'total_venta', 'no_transaccion', 'nombre_vendedor')

@admin.register(DetalleVenta)
class DetalleVentaAdmin(admin.ModelAdmin):
    list_display = ('id_venta', 'id_instrumento', 'no_linea', 'cantidad', 'precio_unitario', 'subtotal')
```

---

## app_instrumental/urls.py (app)
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_instrumental, name='inicio_instrumental'),
    path('instrumentos/agregar/', views.agregar_instrumento, name='agregar_instrumento'),
    path('instrumentos/ver/', views.ver_instrumento, name='ver_instrumento'),
    path('instrumentos/actualizar/<int:id>/', views.actualizar_instrumento, name='actualizar_instrumento'),
    path('instrumentos/realizar_actualizacion/<int:id>/', views.realizar_actualizacion_instrumento, name='realizar_actualizacion_instrumento'),
    path('instrumentos/borrar/<int:id>/', views.borrar_instrumento, name='borrar_instrumento'),
]
```

---

## app_instrumental/views.py (CRUD para Instrumentos)
```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Instrumento
from django.utils import timezone

def inicio_instrumental(request):
    return render(request, 'inicio.html')

def agregar_instrumento(request):
    if request.method == 'POST':
        nombre = request.POST.get('nombre')
        marca = request.POST.get('marca')
        modelo = request.POST.get('modelo')
        precio_venta = request.POST.get('precio_venta') or 0
        cantidad_stock = request.POST.get('cantidad_stock') or 0
        categoria = request.POST.get('categoria')
        fecha_ingreso = request.POST.get('fecha_ingreso') or timezone.now().date()

        instrumento = Instrumento.objects.create(
            nombre=nombre,
            marca=marca,
            modelo=modelo,
            precio_venta=precio_venta,
            cantidad_stock=cantidad_stock,
            categoria=categoria,
            fecha_ingreso=fecha_ingreso
        )
        return redirect('ver_instrumento')
    return render(request, 'instrumentos/agregar_instrumento.html')

def ver_instrumento(request):
    instrumentos = Instrumento.objects.all()
    return render(request, 'instrumentos/ver_instrumento.html', {'instrumentos': instrumentos})

def actualizar_instrumento(request, id):
    instrumento = get_object_or_404(Instrumento, id_instrumento=id)
    return render(request, 'instrumentos/actualizar_instrumento.html', {'instrumento': instrumento})

def realizar_actualizacion_instrumento(request, id):
    instrumento = get_object_or_404(Instrumento, id_instrumento=id)
    if request.method == 'POST':
        instrumento.nombre = request.POST.get('nombre')
        instrumento.marca = request.POST.get('marca')
        instrumento.modelo = request.POST.get('modelo')
        instrumento.precio_venta = request.POST.get('precio_venta') or instrumento.precio_venta
        instrumento.cantidad_stock = request.POST.get('cantidad_stock') or instrumento.cantidad_stock
        instrumento.categoria = request.POST.get('categoria')
        instrumento.fecha_ingreso = request.POST.get('fecha_ingreso') or instrumento.fecha_ingreso
        instrumento.save()
        return redirect('ver_instrumento')
    return redirect('actualizar_instrumento', id=id)

def borrar_instrumento(request, id):
    instrumento = get_object_or_404(Instrumento, id_instrumento=id)
    if request.method == 'POST':
        instrumento.delete()
        return redirect('ver_instrumento')
    return render(request, 'instrumentos/borrar_instrumento.html', {'instrumento': instrumento})
```

---

## Templates

### templates/base.html
```html
<!doctype html>
<html lang="es">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Sistema de Administración Instrumental</title>
    <!-- Bootstrap CDN -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
  </head>
  <body class="d-flex flex-column min-vh-100">
    {% include 'header.html' %}
    {% include 'navbar.html' %}
    <main class="container my-4">
      {% block content %}{% endblock %}
    </main>
    {% include 'footer.html' %}
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
  </body>
</html>
```

### templates/header.html
```html
<header class="bg-light py-3">
  <div class="container">
    <h1 class="h4">Sistema de Administración Instrumental</h1>
  </div>
</header>
```

### templates/navbar.html
```html
<nav class="navbar navbar-expand-lg navbar-light bg-white border-bottom">
  <div class="container">
    <a class="navbar-brand" href="{% url 'inicio_instrumental' %}">Instrumental</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navMenu">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navMenu">
      <ul class="navbar-nav me-auto">
        <li class="nav-item"><a class="nav-link" href="{% url 'inicio_instrumental' %}">Inicio</a></li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" id="instrumentosDropdown" role="button" data-bs-toggle="dropdown">Instrumentos</a>
          <ul class="dropdown-menu" aria-labelledby="instrumentosDropdown">
            <li><a class="dropdown-item" href="{% url 'agregar_instrumento' %}">Agregar instrumento</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_instrumento' %}">Ver instrumento</a></li>
            <li><a class="dropdown-item" href="#">Actualizar instrumento</a></li>
            <li><a class="dropdown-item" href="#">Borrar instrumento</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" id="ventasDropdown" role="button" data-bs-toggle="dropdown">Ventas</a>
          <ul class="dropdown-menu" aria-labelledby="ventasDropdown">
            <li><a class="dropdown-item" href="#">Agregar venta</a></li>
            <li><a class="dropdown-item" href="#">Ver venta</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" id="detalleDropdown" role="button" data-bs-toggle="dropdown">Detalle_venta</a>
          <ul class="dropdown-menu" aria-labelledby="detalleDropdown">
            <li><a class="dropdown-item" href="#">Agregar detalle_venta</a></li>
            <li><a class="dropdown-item" href="#">Ver detalle_venta</a></li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</nav>
```

### templates/footer.html
```html
<footer class="bg-dark text-white py-3 mt-auto">
  <div class="container d-flex justify-content-between">
    <div>© {{ now|date:"Y" }} - Creado por Ing. Alejandro Aceves, Cbtis 128</div>
    <div>Fecha del sistema: {{ now }}</div>
  </div>
</footer>
```

> Nota: en las vistas que renderizan plantillas, si quieres que `now` esté disponible puedes usar `from django.utils import timezone` y pasar `{'now': timezone.now()}` en el contexto o crear un context processor.

### templates/inicio.html
```html
{% extends 'base.html' %}
{% block content %}
<div class="row">
  <div class="col-md-8">
    <h2>Bienvenido al Sistema de Administración Instrumental</h2>
    <p>Este sistema permite gestionar instrumentos, ventas y detalle de ventas.</p>
  </div>
  <div class="col-md-4">
    <img src="https://images.unsplash.com/photo-1511379938547-c1f69419868d" class="img-fluid rounded" alt="Instrumental">
  </div>
</div>
{% endblock %}
```

### templates/instrumentos/agregar_instrumento.html
```html
{% extends 'base.html' %}
{% block content %}
<h3>Agregar Instrumento</h3>
<form method="post">
  {% csrf_token %}
  <div class="mb-3">
    <label>Nombre</label>
    <input class="form-control" name="nombre" required>
  </div>
  <div class="mb-3">
    <label>Marca</label>
    <input class="form-control" name="marca">
  </div>
  <div class="mb-3">
    <label>Modelo</label>
    <input class="form-control" name="modelo">
  </div>
  <div class="mb-3">
    <label>Precio venta</label>
    <input class="form-control" name="precio_venta" type="number" step="0.01">
  </div>
  <div class="mb-3">
    <label>Cantidad stock</label>
    <input class="form-control" name="cantidad_stock" type="number">
  </div>
  <div class="mb-3">
    <label>Categoria</label>
    <input class="form-control" name="categoria">
  </div>
  <div class="mb-3">
    <label>Fecha ingreso</label>
    <input class="form-control" name="fecha_ingreso" type="date">
  </div>
  <button class="btn btn-primary" type="submit">Agregar</button>
</form>
{% endblock %}
```

### templates/instrumentos/ver_instrumento.html
```html
{% extends 'base.html' %}
{% block content %}
<h3>Instrumentos</h3>
<table class="table table-striped">
  <thead>
    <tr>
      <th>ID</th><th>Nombre</th><th>Marca</th><th>Modelo</th><th>Precio</th><th>Stock</th><th>Categoria</th><th>Fecha</th><th>Acciones</th>
    </tr>
  </thead>
  <tbody>
    {% for inst in instrumentos %}
    <tr>
      <td>{{ inst.id_instrumento }}</td>
      <td>{{ inst.nombre }}</td>
      <td>{{ inst.marca }}</td>
      <td>{{ inst.modelo }}</td>
      <td>{{ inst.precio_venta }}</td>
      <td>{{ inst.cantidad_stock }}</td>
      <td>{{ inst.categoria }}</td>
      <td>{{ inst.fecha_ingreso }}</td>
      <td>
        <a class="btn btn-sm btn-info" href="{% url 'actualizar_instrumento' inst.id_instrumento %}">Editar</a>
        <a class="btn btn-sm btn-danger" href="{% url 'borrar_instrumento' inst.id_instrumento %}">Borrar</a>
      </td>
    </tr>
    {% empty %}
    <tr><td colspan="9">No hay instrumentos.</td></tr>
    {% endfor %}
  </tbody>
</table>
{% endblock %}
```

### templates/instrumentos/actualizar_instrumento.html
```html
{% extends 'base.html' %}
{% block content %}
<h3>Actualizar Instrumento</h3>
<form method="post" action="{% url 'realizar_actualizacion_instrumento' instrumento.id_instrumento %}">
  {% csrf_token %}
  <div class="mb-3"><label>Nombre</label><input class="form-control" name="nombre" value="{{ instrumento.nombre }}"></div>
  <div class="mb-3"><label>Marca</label><input class="form-control" name="marca" value="{{ instrumento.marca }}"></div>
  <div class="mb-3"><label>Modelo</label><input class="form-control" name="modelo" value="{{ instrumento.modelo }}"></div>
  <div class="mb-3"><label>Precio venta</label><input class="form-control" name="precio_venta" value="{{ instrumento.precio_venta }}" type="number" step="0.01"></div>
  <div class="mb-3"><label>Cantidad stock</label><input class="form-control" name="cantidad_stock" value="{{ instrumento.cantidad_stock }}" type="number"></div>
  <div class="mb-3"><label>Categoria</label><input class="form-control" name="categoria" value="{{ instrumento.categoria }}"></div>
  <div class="mb-3"><label>Fecha ingreso</label><input class="form-control" name="fecha_ingreso" value="{{ instrumento.fecha_ingreso }}" type="date"></div>
  <button class="btn btn-success" type="submit">Guardar cambios</button>
</form>
{% endblock %}
```

### templates/instrumentos/borrar_instrumento.html
```html
{% extends 'base.html' %}
{% block content %}
<h3>Borrar Instrumento</h3>
<p>¿Desea eliminar el instrumento <strong>{{ instrumento.nombre }}</strong>?</p>
<form method="post">
  {% csrf_token %}
  <button class="btn btn-danger" type="submit">Eliminar</button>
  <a class="btn btn-secondary" href="{% url 'ver_instrumento' %}">Cancelar</a>
</form>
{% endblock %}
```

---

## Consideraciones importantes
- No se usa `forms.py` (solicitado).
- No se realiza validación de entrada (tal y como pide el enunciado).
- Colores suaves y diseño sencillo con Bootstrap.
- Se trabajó principalmente con el modelo `Instrumento`. `Venta` y `DetalleVenta` están listados pero su CRUD queda pendiente.
- Asegúrate de ejecutar `python manage.py makemigrations app_instrumental` y `python manage.py migrate` después de agregar los modelos.
- Registrar la app en `INSTALLED_APPS` y reiniciar el servidor si realizas cambios en `settings.py`.

---

## Archivos adicionales de ejemplo (manage.py se genera automáticamente por Django)
# Fin del documento
