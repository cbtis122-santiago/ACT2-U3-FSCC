Primera parte Proyecto: In-N-Out. Lenguaje: Python. Framework: Django. Editor: VS code.

Procedimiento para crear carpeta del Proyecto: UIII_in_n_out_0421

Procedimiento para abrir vs code sobre la carpeta UIII_in_n_out_0421

Procedimiento para abrir terminal en vs code

Procedimiento para crear carpeta entorno virtual “.venv” desde terminal de vs code

Procedimiento para activar el entorno virtual.

Procedimiento para activar intérprete de python.

Procedimiento para instalar Django

Procedimiento para crear proyecto backend_in_n_out sin duplicar carpeta.

Procedimiento para ejecutar servidor en el puerto 8421

Procedimiento para copiar y pegar el link en el navegador.

Procedimiento para crear aplicación app_in_n_out

Aqui el modelo models.py

Python

from django.db import models
import datetime

# ==========================================
# MODELO: SUCURSAL (7 campos + id)
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
# MODELO: TRABAJADOR (7 campos + id)
# ==========================================
class Trabajador(models.Model):
    # 6 campos de datos
    nombre = models.CharField(max_length=100)
    apellido = models.CharField(max_length=100)
    puesto = models.CharField(max_length=50)
    fecha_contratacion = models.DateField()
    email = models.EmailField(unique=True)
    telefono_personal = models.CharField(max_length=20, blank=True)

    # --- CAMPO 7: Relación 1 (1:M) ---
    # Una Sucursal (1) tiene muchos Trabajadores (M)
    sucursal = models.ForeignKey(
        Sucursal, 
        on_delete=models.CASCADE, # Si se borra la sucursal, se van los empleados
        related_name="trabajadores"
    )

    def __str__(self):
        return f"{self.nombre} {self.apellido}"

# ==========================================
# MODELO: PEDIDO (7 campos + id)
# ==========================================
class Pedido(models.Model):
    # 6 campos de datos
    fecha_pedido = models.DateTimeField(auto_now_add=True)
    numero_orden = models.PositiveIntegerField()
    total_pedido = models.DecimalField(max_digits=7, decimal_places=2)
    tipo_orden = models.CharField(max_length=50, help_text="Comer ahi, Para llevar")
    nombre_cliente_temporal = models.CharField(max_length=100, blank=True)
    pagado = models.BooleanField(default=False)

    # --- CAMPO 7: Relación 2 (1:M) ---
    # Un Trabajador (1) toma muchos Pedidos (M)
    trabajador = models.ForeignKey(
        Trabajador, 
        on_delete=models.SET_NULL, # Si despiden al trabajador, el pedido queda
        null=True,
        blank=True,
        related_name="pedidos"
    )

    def __str__(self):
        return f"Pedido #{self.numero_orden}"
Procedimiento para realizar las migraciones (makemigrations y migrate).

Primero trabajamos con el MODELO: SUCURSAL

En views.py de app_in_n_out crear las funciones con sus códigos correspondientes (inicio_in_n_out, agregar_sucursal, actualizar_sucursal, realizar_actualizacion_sucursal, borrar_sucursal)

Crear la carpeta “templates” dentro de “app_in_n_out”.

En la carpeta templates crear los archivos html (base.html, header.html, navbar.html, footer.html, inicio.html).

En el archivo base.html agregar bootstrap para css y js.

En el archivo navbar.html incluir las opciones ( “Sistema de Administración In-N-Out”, “Inicio”, “Sucursal”,en submenu de Sucursal(Agregar Sucursal, Ver Sucursales, Actualizar Sucursal, Borrar Sucursal), “Trabajadores” en submenu de Trabajadores(Agregar Trabajador, Ver Trabajadores, Actualizar Trabajador, Borrar Trabajador), “Pedidos” en submenu de Pedidos(Agregar Pedido, Ver Pedidos, Actualizar Pedido, Borrar Pedido), incluir iconos a las opciones principales, no en los submenu.

En el archivo footer.html incluir derechos de autor, fecha del sistema y “Creado por Ing. Santiago Carrasco, Cbtis 128” y mantenerla fija al final de la página.

En el archivo inicio.html se usa para colocar información del sistema más una imagen tomada desde la red sobre In-N-Out.

Crear la subcarpeta sucursal dentro de app_in_n_out\templates.

Crear los archivos html con su codigo correspondientes de (agregar_sucursal.html, ver_sucursales.html mostrar en tabla con los botones ver, editar y borrar, actualizar_sucursal.html, borrar_sucursal.html) dentro de app_in_n_out\templates\sucursal.

No utilizar forms.py.

Procedimiento para crear el archivo urls.py en app_in_n_out con el código correspondiente para acceder a las funciones de views.py para operaciones de crud en sucursales.

Procedimiento para agregar app_in_n_out en settings.py de backend_in_n_out

Realizar las configuraciones correspondiente a urls.py de backend_in_n_out para enlazar con app_in_n_out

Procedimiento para registrar los modelos en admin.py y volver a realizar las migraciones.

Por lo pronto solo trabajar con “Sucursal” dejar pendiente # MODELO: TRABAJADOR y # MODELO: PEDIDO

Utilizar colores suaves, atractivos y modernos, el código de las páginas web sencillas.

No validar entrada de datos.

Al inicio crear la estructura completa de carpetas y archivos.

Proyecto totalmente funcional.

Finalmente ejecutar servidor en el puerto 8421.
