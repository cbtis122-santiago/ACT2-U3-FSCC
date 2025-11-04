# Proyecto: In-N-Out

**Lenguaje:** Python  
**Framework:** Django  
**Editor:** VS Code

---

## Procedimientos de Configuración y Desarrollo

### 1. Crear la carpeta del proyecto

Para comenzar, crea la carpeta del proyecto con el siguiente comando en la terminal:

```bash
mkdir UIII_in_n_out_0421
2. Abrir VS Code sobre la carpeta del proyecto
Abre la carpeta del proyecto en VS Code con el siguiente comando:

bash
Copiar código
code UIII_in_n_out_0421
3. Abrir terminal en VS Code
En VS Code, abre la terminal integrada con Ctrl + ~ o desde el menú Terminal > New Terminal.

4. Crear el entorno virtual
Desde la terminal de VS Code, ejecuta el siguiente comando para crear un entorno virtual en la carpeta .venv:

bash
Copiar código
python -m venv .venv
5. Activar el entorno virtual
Para activar el entorno virtual, ejecuta:

Windows:

bash
Copiar código
.venv\Scripts\activate
Mac/Linux:

bash
Copiar código
source .venv/bin/activate
6. Activar el intérprete de Python en VS Code
En la parte inferior izquierda de VS Code, haz clic en el selector de intérprete de Python y selecciona el entorno virtual .venv.

7. Instalar Django
Instala Django con el siguiente comando en la terminal de VS Code:

bash
Copiar código
pip install django
8. Crear el proyecto backend_in_n_out
Crea el proyecto de Django con el siguiente comando:

bash
Copiar código
django-admin startproject backend_in_n_out .
Este comando creará el proyecto Django dentro de la carpeta actual sin duplicar la carpeta.

9. Ejecutar el servidor en el puerto 8421
Para ejecutar el servidor de desarrollo en el puerto 8421, utiliza el siguiente comando:

bash
Copiar código
python manage.py runserver 8421
10. Copiar y pegar el link en el navegador
Abre tu navegador y visita la URL:

cpp
Copiar código
http://127.0.0.1:8421/
11. Crear la aplicación app_in_n_out
Ahora, crea la aplicación app_in_n_out con el siguiente comando:

bash
Copiar código
python manage.py startapp app_in_n_out
Modelos en models.py
En el archivo models.py de la aplicación app_in_n_out, define los modelos según lo siguiente:

Modelo: Sucursal
python
Copiar código
from django.db import models

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
Modelo: Trabajador
python
Copiar código
class Trabajador(models.Model):
    nombre = models.CharField(max_length=100)
    apellido = models.CharField(max_length=100)
    puesto = models.CharField(max_length=50)
    fecha_contratacion = models.DateField()
    email = models.EmailField(unique=True)
    telefono_personal = models.CharField(max_length=20, blank=True)
    
    sucursal = models.ForeignKey(
        Sucursal, 
        on_delete=models.CASCADE, 
        related_name="trabajadores"
    )

    def __str__(self):
        return f"{self.nombre} {self.apellido}"
Modelo: Pedido
python
Copiar código
class Pedido(models.Model):
    fecha_pedido = models.DateTimeField(auto_now_add=True)
    numero_orden = models.PositiveIntegerField()
    total_pedido = models.DecimalField(max_digits=7, decimal_places=2)
    tipo_orden = models.CharField(max_length=50)
    nombre_cliente_temporal = models.CharField(max_length=100, blank=True)
    pagado = models.BooleanField(default=False)
    
    trabajador = models.ForeignKey(
        Trabajador, 
        on_delete=models.SET_NULL, 
        null=True,
        blank=True,
        related_name="pedidos"
    )

    def __str__(self):
        return f"Pedido #{self.numero_orden}"
Realizar Migraciones
Ejecuta los siguientes comandos para realizar las migraciones:

bash
Copiar código
python manage.py makemigrations
python manage.py migrate
Crear las Funciones en views.py
En views.py de app_in_n_out, agrega las funciones necesarias para CRUD de las sucursales:

python
Copiar código
from django.shortcuts import render, redirect
from .models import Sucursal
from .forms import SucursalForm

# Función para la página de inicio
def inicio_in_n_out(request):
    return render(request, 'inicio.html')

# Función para agregar una nueva sucursal
def agregar_sucursal(request):
    if request.method == 'POST':
        form = SucursalForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect('ver_sucursales')
    else:
        form = SucursalForm()
    return render(request, 'agregar_sucursal.html', {'form': form})

# Función para ver las sucursales
def ver_sucursales(request):
    sucursales = Sucursal.objects.all()
    return render(request, 'ver_sucursales.html', {'sucursales': sucursales})

# Función para actualizar una sucursal
def actualizar_sucursal(request, pk):
    sucursal = Sucursal.objects.get(id=pk)
    if request.method == 'POST':
        form = SucursalForm(request.POST, instance=sucursal)
        if form.is_valid():
            form.save()
            return redirect('ver_sucursales')
    else:
        form = SucursalForm(instance=sucursal)
    return render(request, 'actualizar_sucursal.html', {'form': form})

# Función para borrar una sucursal
def borrar_sucursal(request, pk):
    sucursal = Sucursal.objects.get(id=pk)
    if request.method == 'POST':
        sucursal.delete()
        return redirect('ver_sucursales')
    return render(request, 'borrar_sucursal.html', {'sucursal': sucursal})
Configuración de las Plantillas HTML
Crea la carpeta templates dentro de app_in_n_out, y dentro de ella, los archivos HTML correspondientes.

Estructura de Archivos
css
Copiar código
app_in_n_out/
└── templates/
    └── base.html
    └── header.html
    └── navbar.html
    └── footer.html
    └── inicio.html
    └── sucursal/
        └── agregar_sucursal.html
        └── ver_sucursales.html
        └── actualizar_sucursal.html
        └── borrar_sucursal.html
Código para base.html
html
Copiar código
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>In-N-Out</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css">
</head>
<body>
    {% include 'header.html' %}
    {% include 'navbar.html' %}
    
    <div class="container mt-4">
        {% block content %}{% endblock %}
    </div>
    
    {% include 'footer.html' %}
</body>
</html>
Código para navbar.html
html
Copiar código
<nav class="navbar navbar-expand-lg navbar-light bg-light">
    <a class="navbar-brand" href="#">In-N-Out</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarNav">
        <ul class="navbar-nav">
            <li class="nav-item active"><a class="nav-link" href="#">Inicio</a></li>
            <li class="nav-item dropdown">
                <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">Sucursal</a>
                <ul class="dropdown-menu" aria-labelledby="navbarDropdown">
                    <li><a class="dropdown-item" href="#">Agregar Sucursal</a></li>
                    <li><a class="dropdown-item" href="#">Ver Sucursales</a></li>
                    <li><a class="dropdown-item" href="#">Actualizar Sucursal</a></li>
                    <li><a class="dropdown-item" href="#">Borrar Sucursal</a></li>
                </ul>
            </li>
            <li class="nav-item"><a class="nav-link" href="#">Trabajadores</a></li>
            <li class="
