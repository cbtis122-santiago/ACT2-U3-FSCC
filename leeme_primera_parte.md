# Guía paso a paso (Primera parte) — Proyecto In-N-Out (Django, Python, VS Code)

Autor: **Ing. Santiago Carrasco**
Voy a darte todo lo necesario: comandos, estructura de carpetas, archivos con código listo para copiar/pegar (models, views, urls, admin, templates) y las instrucciones para crear, activar y ejecutar el proyecto en el puerto **8421**.
Trabajo con la petición de **no usar forms.py** y de enfocarnos por ahora en **SUCURSAL** (las otras dos entidades quedan definidas pero su CRUD queda pendiente).

---

## 1 — Crear la carpeta del proyecto

Abre tu terminal (PowerShell / CMD / Terminal) y ejecuta:

```bash
# desde la carpeta donde quieras crear el proyecto
mkdir UIII_InNOut_0421
cd UIII_InNOut_0421
```

---

## 2 — Abrir VS Code sobre la carpeta

Si tienes code en PATH:

```bash
code .
```

(esto abre VS Code en `UIII_InNOut_0421`).

También puedes abrir VS Code y usar **File → Open Folder...** y seleccionar `UIII_InNOut_0421`.

---

## 3 — Abrir terminal en VS Code

En VS Code: menú **Terminal → New Terminal** (o Ctrl+ `) — te abre la terminal en la raíz del proyecto.

---

## 4 — Crear carpeta/entorno virtual .venv desde la terminal de VS Code

(Comando multiplataforma — usa la versión python correcta)

```bash
# Windows / macOS / Linux (asegúrate que 'python' apunta a Python 3.x)
python -m venv .venv
```

Esto crea la carpeta `.venv` dentro de `UIII_InNOut_0421`.

---

## 5 — Activar el entorno virtual

### Windows PowerShell

```bash
.\.venv\Scripts\Activate.ps1
```

Si te bloquea por política (ExecutionPolicy), puedes ejecutar en PowerShell como administrador:

```bash
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```

y luego activar.

### Windows CMD

```bash
.\.venv\Scripts\activate
```

### macOS / Linux

```bash
source .venv/bin/activate
```

Cuando esté activo verás `(.venv)` al inicio de la línea de tu terminal.

---

## 6 — Activar intérprete de Python en VS Code

En VS Code: **Ctrl+Shift+P → escribe Python: Select Interpreter**.
Selecciona la ruta al intérprete dentro de tu venv, p. ej. `UIII_InNOut_0421\.venv\Scripts\python` (Windows) o `.venv/bin/python` (mac/linux).
Opcional: reinicia la ventana si pide recargar.

---

## 7 — Instalar Django

Con venv activado:

```bash
pip install --upgrade pip
pip install django
```

Verifica:

```bash
python -m django --version
```

---

## 8 — Crear proyecto backend_InNOut SIN duplicar carpeta

Para evitar que Django cree una carpeta extra `backend_InNOut/backend_InNOut`, utiliza el punto `.` al crear el proyecto dentro de la carpeta actual.

Desde `UIII_InNOut_0421`:

```bash
django-admin startproject backend_InNOut .
```

El `.` indica "crear el proyecto aquí" (no crear subcarpeta adicional).
Ahora verás `manage.py` y el paquete `backend_InNOut/`.

---

## 9 — Ejecutar servidor en el puerto 8421

Antes de ejecutar, crear app y migraciones. Pero para probar:

```bash
python manage.py runserver 8421
```

Abre en el navegador:
[http://127.0.0.1:8421/](http://127.0.0.1:8421/) o [http://localhost:8421/](http://localhost:8421/)

(Si VS Code te pide permisos de firewall acepta para localhost).

---

## 10 — Copiar y pegar el link en el navegador

Abre tu navegador y pega:
[http://127.0.0.1:8421/](http://127.0.0.1:8421/) (o [http://localhost:8421/](http://localhost:8421/))

---

## 11 — Crear aplicación app_InNOut

Con venv activado y en la raíz del proyecto:

```bash
python manage.py startapp app_InNOut
```

Esto crea la carpeta `app_InNOut` con sus archivos.

---

## 12 — models.py

Copia/pega el siguiente contenido en `app_InNOut/models.py`.
Incluye los tres modelos (**Sucursal**, **Trabajador**, **Pedido**) — trabajaremos CRUD sólo para **Sucursal** por ahora.

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
    tipo_orden = models.CharField(max_length=50, help_text="Comer ahí o Para llevar")
    nombre_cliente_temporal = models.CharField(max_length=100, blank=True)
    pagado = models.BooleanField(default=False)
    trabajador = models.ForeignKey(Trabajador, on_delete=models.SET_NULL, null=True, blank=True, related_name="pedidos")

    def __str__(self):
        return f"Pedido #{self.numero_orden}"
```

---

## 12.5 — Procedimiento para realizar migraciones (makemigrations y migrate)

Primero registra la app en settings (ver paso 25). Después:

```bash
# crear migraciones para app_InNOut
python manage.py makemigrations app_InNOut

# aplicar migraciones a la base de datos
python manage.py migrate
```

---

## 13 — Primero trabajamos con el MODELO: SUCURSAL

Las vistas, urls y templates que te doy a continuación enfocan CRUD en **Sucursal** únicamente.
Trabajador y Pedido quedan en models.py para el futuro.

---

## 14 — views.py de app_InNOut

Reemplaza el contenido de `app_InNOut/views.py` por este (funcional, sin forms.py, sin validaciones):

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Sucursal

# Página de inicio del sistema
def inicio_in_n_out(request):
    total_sucursales = Sucursal.objects.count()
    sucursales = Sucursal.objects.all().order_by('-fecha_apertura')[:5]
    return render(request, 'inicio.html', {'total_sucursales': total_sucursales, 'sucursales': sucursales})

# Mostrar formulario para agregar sucursal
def agregar_sucursal(request):
    if request.method == 'POST':
        nombre = request.POST.get('nombre_tienda')
        direccion = request.POST.get('direccion')
        ciudad = request.POST.get('ciudad')
        codigo_postal = request.POST.get('codigo_postal')
        telefono = request.POST.get('telefono_tienda')
        fecha = request.POST.get('fecha_apertura')
        es_drive_thru = True if request.POST.get('es_drive_thru') == 'on' else False

        Sucursal.objects.create(
            nombre_tienda=nombre,
            direccion=direccion,
            ciudad=ciudad,
            codigo_postal=codigo_postal,
            telefono_tienda=telefono,
            fecha_apertura=fecha,
            es_drive_thru=es_drive_thru
        )
        return redirect('ver_sucursales')

    return render(request, 'sucursal/agregar_sucursal.html')

# Ver todas las sucursales
def ver_sucursales(request):
    sucursales = Sucursal.objects.all().order_by('nombre_tienda')
    return render(request, 'sucursal/ver_sucursales.html', {'sucursales': sucursales})

# Mostrar formulario con datos para actualizar
def actualizar_sucursal(request, sucursal_id):
    sucursal = get_object_or_404(Sucursal, id=sucursal_id)
    return render(request, 'sucursal/actualizar_sucursal.html', {'sucursal': sucursal})

# Procesar la actualización (POST)
def realizar_actualizacion_sucursal(request, sucursal_id):
    sucursal = get_object_or_404(Sucursal, id=sucursal_id)
    if request.method == 'POST':
        sucursal.nombre_tienda = request.POST.get('nombre_tienda')
        sucursal.direccion = request.POST.get('direccion')
        sucursal.ciudad = request.POST.get('ciudad')
        sucursal.codigo_postal = request.POST.get('codigo_postal')
        sucursal.telefono_tienda = request.POST.get('telefono_tienda')
        sucursal.fecha_apertura = request.POST.get('fecha_apertura')
        sucursal.es_drive_thru = True if request.POST.get('es_drive_thru') == 'on' else False
        sucursal.save()
        return redirect('ver_sucursales')
    return redirect('ver_sucursales')

# Confirmación y borrado
def borrar_sucursal(request, sucursal_id):
    sucursal = get_object_or_404(Sucursal, id=sucursal_id)
    if request.method == 'POST':
        sucursal.delete()
        return redirect('ver_sucursales')
    return render(request, 'sucursal/borrar_sucursal.html', {'sucursal': sucursal})
```

---

## 15 — Crear carpeta templates dentro de app_InNOut

Ruta: `app_InNOut/templates/`

Dentro de ella habrá:

* `base.html`
* `header.html`
* `navbar.html`
* `footer.html`
* `inicio.html`
* Subcarpeta `sucursal/` con los html del CRUD.

---

## 16 — Archivos HTML principales

A continuación te doy plantillas simples y con Bootstrap CDN.

*(Las mismas del ejemplo original, adaptadas a In-N-Out y a tu nombre en el footer.)*

**Crea app_InNOut/templates/footer.html:**

```html
<footer class="fixed-footer text-center">
  <div class="container">
    <small>
      &copy; {% now "Y" %} In-N-Out — Todos los derechos reservados.
      &nbsp;|&nbsp; Creado por Ing. Santiago Carrasco
    </small>
  </div>
</footer>
```

*(Los demás archivos siguen el mismo formato que el ejemplo de Cinepolis, cambiando los nombres de entidad y texto.)*

---

## 24 — urls.py en app_InNOut

Crea `app_InNOut/urls.py` con este contenido:

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

## 25 — Agregar app_InNOut en settings.py de backend_InNOut

Edita `backend_InNOut/settings.py`, en `INSTALLED_APPS` añade `'app_InNOut'`, por ejemplo:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    # apps locales
    'app_InNOut',
]
```

---

## 26 — Configurar urls.py de backend_InNOut para enlazar con app_InNOut

Edita `backend_InNOut/urls.py`:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_InNOut.urls')),  # rutas principales de la app
]
```

---

## 27 — Registrar modelos en admin.py y volver a migrar/crear superuser

En `app_InNOut/admin.py`:

```python
from django.contrib import admin
from .models import Sucursal, Trabajador, Pedido

@admin.register(Sucursal)
class SucursalAdmin(admin.ModelAdmin):
    list_display = ('nombre_tienda', 'ciudad', 'telefono_tienda', 'es_drive_thru', 'fecha_apertura')
    search_fields = ('nombre_tienda', 'ciudad')
    list_filter = ('es_drive_thru',)

@admin.register(Trabajador)
class TrabajadorAdmin(admin.ModelAdmin):
    list_display = ('nombre', 'apellido', 'puesto', 'sucursal')

@admin.register(Pedido)
class PedidoAdmin(admin.ModelAdmin):
    list_display = ('numero_orden', 'total_pedido', 'tipo_orden', 'pagado', 'fecha_pedido')
```

Luego:

```bash
# crear migraciones (si aún no lo hiciste después de añadir app y models)
python manage.py makemigrations
python manage.py migrate

# crear superusuario para admin
python manage.py createsuperuser
```

---

## 27 (segunda aparición) — Por ahora solo trabajar con “Sucursal”

Las vistas, urls y templates anteriores se centran sólo en administrar **Sucursal**.
**Trabajador** y **Pedido** están en models.py pero no tienen vistas ni templates (pendiente).

---

## 28 — Estética: colores suaves y modernos

Las plantillas usan Bootstrap, sombras suaves (card-soft) y tipografía predeterminada de Bootstrap para mantener diseño limpio y moderno.
Puedes personalizar style en base.html.

---

## 29 — Estructura completa inicial de carpetas y archivos

Ejemplo de árbol principal (después de aplicar lo anterior):

```
UIII_InNOut_0421/
├─ .venv/
├─ manage.py
├─ backend_InNOut/
│  ├─ __init__.py
│  ├─ settings.py
│  ├─ urls.py
│  └─ wsgi.py
├─ app_InNOut/
│  ├─ migrations/
│  ├─ templates/
│  │  ├─ base.html
│  │  ├─ navbar.html
│  │  ├─ footer.html
│  │  ├─ inicio.html
│  │  └─ sucursal/
│  │     ├─ agregar_sucursal.html
│  │     ├─ ver_sucursales.html
│  │     ├─ actualizar_sucursal.html
│  │     └─ borrar_sucursal.html
│  ├─ __init__.py
│  ├─ admin.py
│  ├─ apps.py
│  ├─ models.py
│  ├─ tests.py
│  ├─ urls.py
│  └─ views.py
```

---

## 30 — Proyecto totalmente funcional (pasos finales)

Asegúrate que `app_InNOut` está en `INSTALLED_APPS`.

Ejecuta migraciones:

```bash
python manage.py makemigrations
python manage.py migrate
```

Crea superusuario (opcional para admin):

```bash
python manage.py createsuperuser
```

Ejecuta servidor en puerto 8421:

```bash
python manage.py runserver 8421
```

Abre en navegador: [http://127.0.0.1:8421/](http://127.0.0.1:8421/) — deberías ver la página de inicio.

Accede a **Sucursal → Ver/Agregar/Editar/Borrar** desde la barra de navegación.

---

## 31 — Finalmente ejecutar servidor en el puerto 8421

(Ya incluido arriba; repito el comando final)

```bash
python manage.py runserver 8421
```

---

### Notas rápidas, recomendaciones y consideraciones

* No validé entradas (según tu instrucción).
* Si en el futuro quieres validación, podemos añadir `forms.py` o validaciones en `views`.
* Puedes configurar `STATIC_URL` y `STATICFILES_DIRS` para CSS e imágenes personalizadas.
* Si deseas, se puede generar el diagrama ERD en `.drawio` o imagen para README.md.

---

**Proyecto In-N-Out (Primera Parte) — CRUD SUCURSAL**
Autor: *Ing. Santiago Carrasco*
CBTis 128
