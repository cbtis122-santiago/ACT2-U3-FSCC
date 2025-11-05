# Guía paso a paso (Primera parte) — Proyecto In-N-Out (Django, Python, VS Code)

Autor: **Ing. Santiago Carrasco**
Versión: UIII_In_N_Out_0421
Puerto del servidor: **8421**

Voy a darte todo lo necesario: comandos, estructura de carpetas, archivos con código listo para copiar/pegar (models, views, urls, admin, templates) y las instrucciones para crear, activar y ejecutar el proyecto.
No se utiliza `forms.py` y nos enfocaremos **solo en el CRUD de Sucursal** (las otras dos entidades quedan definidas pero pendientes de CRUD).

---

## 1 — Crear la carpeta del proyecto

Abre tu terminal (PowerShell / CMD / Terminal) y ejecuta:

```bash
mkdir UIII_InNOut_0421
cd UIII_InNOut_0421
```

---

## 2 — Abrir VS Code sobre la carpeta

Si tienes VS Code en PATH:

```bash
code .
```

(Esto abre VS Code directamente en `UIII_InNOut_0421`).

---

## 3 — Abrir terminal en VS Code

En VS Code:
**Terminal → New Terminal** o usa **Ctrl + `**.

---

## 4 — Crear entorno virtual `.venv`

```bash
python -m venv .venv
```

---

## 5 — Activar el entorno virtual

### Windows PowerShell

```bash
.\.venv\Scripts\Activate.ps1
```

Si se bloquea por políticas, ejecuta:

```bash
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```

y vuelve a activar.

### macOS / Linux

```bash
source .venv/bin/activate
```

Verás `(.venv)` al inicio de la línea.

---

## 6 — Activar intérprete de Python en VS Code

En VS Code:
**Ctrl+Shift+P → Python: Select Interpreter → elige `.venv`**.

---

## 7 — Instalar Django

```bash
pip install --upgrade pip
pip install django
```

Verifica:

```bash
python -m django --version
```

---

## 8 — Crear proyecto backend_InNOut sin duplicar carpeta

Desde la raíz:

```bash
django-admin startproject backend_InNOut .
```

Esto creará `manage.py` y la carpeta `backend_InNOut/`.

---

## 9 — Crear la aplicación principal

```bash
python manage.py startapp app_InNOut
```

---

## 10 — models.py

Edita `app_InNOut/models.py` y pega lo siguiente:

```python
from django.db import models

# ==========================================
# MODELO: SUCURSAL
# ==========================================
class Sucursal(models.Model):
    nombre_tienda = models.CharField(max_length=100)
    direccion = models.CharField(max_length=255, blank=True)
    ciudad = models.CharField(max_length=100)
    codigo_postal = models.CharField(max_length=10, blank=True)
    telefono_tienda = models.CharField(max_length=20, blank=True)
    fecha_apertura = models.DateField(null=True, blank=True)
    es_drive_thru = models.BooleanField(default=True)

    def __str__(self):
        return self.nombre_tienda


# ==========================================
# MODELO: TRABAJADOR
# ==========================================
class Trabajador(models.Model):
    nombre = models.CharField(max_length=100)
    apellido = models.CharField(max_length=100)
    puesto = models.CharField(max_length=50)
    fecha_contratacion = models.DateField()
    email = models.EmailField(unique=True)
    telefono_personal = models.CharField(max_length=20, blank=True)
    sucursal = models.ForeignKey(Sucursal, on_delete=models.CASCADE, related_name="trabajadores")

    def __str__(self):
        return f"{self.nombre} {self.apellido}"


# ==========================================
# MODELO: PEDIDO
# ==========================================
class Pedido(models.Model):
    fecha_pedido = models.DateTimeField(auto_now_add=True)
    numero_orden = models.PositiveIntegerField()
    total_pedido = models.DecimalField(max_digits=7, decimal_places=2)
    tipo_orden = models.CharField(max_length=50, help_text="Comer aquí o Para llevar")
    nombre_cliente_temporal = models.CharField(max_length=100, blank=True)
    pagado = models.BooleanField(default=False)
    trabajador = models.ForeignKey(Trabajador, on_delete=models.SET_NULL, null=True, blank=True, related_name="pedidos")

    def __str__(self):
        return f"Pedido #{self.numero_orden}"
```

---

## 11 — Migraciones

```bash
python manage.py makemigrations app_InNOut
python manage.py migrate
```

---

## 12 — CRUD de Sucursal (views.py)

Edita `app_InNOut/views.py`:

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Sucursal

def inicio_in_n_out(request):
    total_sucursales = Sucursal.objects.count()
    sucursales = Sucursal.objects.all().order_by('-fecha_apertura')[:5]
    return render(request, 'inicio.html', {'total_sucursales': total_sucursales, 'sucursales': sucursales})

def agregar_sucursal(request):
    if request.method == 'POST':
        nombre = request.POST.get('nombre_tienda')
        direccion = request.POST.get('direccion')
        ciudad = request.POST.get('ciudad')
        codigo_postal = request.POST.get('codigo_postal')
        telefono = request.POST.get('telefono_tienda')
        fecha = request.POST.get('fecha_apertura')
        drive_thru = True if request.POST.get('es_drive_thru') == 'on' else False

        Sucursal.objects.create(
            nombre_tienda=nombre,
            direccion=direccion,
            ciudad=ciudad,
            codigo_postal=codigo_postal,
            telefono_tienda=telefono,
            fecha_apertura=fecha,
            es_drive_thru=drive_thru
        )
        return redirect('ver_sucursales')
    return render(request, 'sucursal/agregar_sucursal.html')

def ver_sucursales(request):
    sucursales = Sucursal.objects.all()
    return render(request, 'sucursal/ver_sucursales.html', {'sucursales': sucursales})

def actualizar_sucursal(request, sucursal_id):
    sucursal = get_object_or_404(Sucursal, id=sucursal_id)
    return render(request, 'sucursal/actualizar_sucursal.html', {'sucursal': sucursal})

def realizar_actualizacion_sucursal(request, sucursal_id):
    sucursal = get_object_or_404(Sucursal, id=sucursal_id)
    if request.method == 'POST':
        sucursal.nombre_tienda = request.POST.get('nombre_tienda')
        sucursal.direccion = request.POST.get('direccion')
        sucursal.ciudad = request.POST.get('ciudad')
        sucursal.codigo_postal = request.POST.get('codigo_postal')
        sucursal.telefono_tienda = request.POST.get('telefono_tienda')
        sucursal.es_drive_thru = True if request.POST.get('es_drive_thru') == 'on' else False
        sucursal.save()
        return redirect('ver_sucursales')
    return redirect('ver_sucursales')

def borrar_sucursal(request, sucursal_id):
    sucursal = get_object_or_404(Sucursal, id=sucursal_id)
    if request.method == 'POST':
        sucursal.delete()
        return redirect('ver_sucursales')
    return render(request, 'sucursal/borrar_sucursal.html', {'sucursal': sucursal})
```

---

## 13 — urls.py (app_InNOut)

Crea `app_InNOut/urls.py`:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_in_n_out, name='inicio'),
    path('sucursal/agregar/', views.agregar_sucursal, name='agregar_sucursal'),
    path('sucursal/ver/', views.ver_sucursales, name='ver_sucursales'),
    path('sucursal/actualizar/<int:sucursal_id>/', views.actualizar_sucursal, name='actualizar_sucursal'),
    path('sucursal/actualizar/realizar/<int:sucursal_id>/', views.realizar_actualizacion_sucursal, name='realizar_actualizacion_sucursal'),
    path('sucursal/borrar/<int:sucursal_id>/', views.borrar_sucursal, name='borrar_sucursal'),
]
```

---

## 14 — Configurar backend_InNOut/urls.py

Edita `backend_InNOut/urls.py`:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_InNOut.urls')),
]
```

---

## 15 — Registrar app y modelos en admin.py

En `backend_InNOut/settings.py`, agrega `'app_InNOut'` en `INSTALLED_APPS`.

En `app_InNOut/admin.py`:

```python
from django.contrib import admin
from .models import Sucursal, Trabajador, Pedido

@admin.register(Sucursal)
class SucursalAdmin(admin.ModelAdmin):
    list_display = ('nombre_tienda', 'ciudad', 'telefono_tienda', 'es_drive_thru', 'fecha_apertura')

@admin.register(Trabajador)
class TrabajadorAdmin(admin.ModelAdmin):
    list_display = ('nombre', 'apellido', 'puesto', 'sucursal')

@admin.register(Pedido)
class PedidoAdmin(admin.ModelAdmin):
    list_display = ('numero_orden', 'total_pedido', 'pagado', 'fecha_pedido')
```

---

## 16 — Crear estructura templates

Ruta: `app_InNOut/templates/`
Archivos:

* `base.html`
* `navbar.html`
* `footer.html`
* `inicio.html`
* Subcarpeta `sucursal/` con los HTML para CRUD.

*(Puedes usar el mismo estilo Bootstrap del ejemplo de Cinepolis).*

---

## 17 — Ejecutar servidor

```bash
python manage.py runserver 8421
```

Abre:
**[http://127.0.0.1:8421/](http://127.0.0.1:8421/)**

---

## 18 — Próximos pasos

* Subir este contenido como `leeme_primera_parte.md` a tu repositorio en GitHub.
* Adjuntar la imagen del modelo relacional (recortada desde DBDesigner) al `README.md`.
* Subir el PDF de tu prompt al mismo repositorio.
* Realizar commit con mensaje:
  `"Primera parte del proyecto In-N-Out — CRUD Sucursal completado"`.
