# Proyecto de Reserva de Habitaciones

Este proyecto es una aplicación web desarrollada en Django para la reserva de habitaciones. Los usuarios pueden registrarse, iniciar sesión, ver una lista de habitaciones disponibles y realizar reservas.

## Requisitos

- SQLite (o cualquier otra base de datos compatible con Django)
- Visual Studio Code (VSCode)
- ﻿asgiref==3.8.1
- certifi==2024.6.2
- charset-normalizer==3.3.2
- dj-rest-auth==6.0.0
- Django==5.0.6
- django-allauth==0.63.3
- django-filter==24.2
- djangorestframework==3.15.1
- idna==3.7
- requests==2.32.3
- sqlparse==0.5.0
- tzdata==2024.1
- urllib3==2.2.2

## Instalación

1. **Clona este repositorio:**
    ```bash
    git clone https://github.com/CrisDenisse/hotel_booking.git
    cd hotel_booking
    ```

2. **Crea y activa un entorno virtual:**
    ```bash
    python -m venv env
    env\Scripts\activate
    ```
3. **Instala las dependencias del proyecto:**
    ```bash
    pip install django
    pip install djangorestframework
    pip install django-filter
    pip install requests
    pip install dj_rest_auth
    pip install django-allauth

    ```

4. **Creación de archivo de dependencias requerements.txt**
    ```bash
    pip freeze > requirements.txt
    ```

5. **Navega al directorio que contiene el archivo `manage.py`:**

    ```bash
    cd hotel_booking
    ```

6. **Aplica las migraciones de la base de datos:**
    ```bash
    python manage.py migrate
    ```

7. **Crea un superusuario para acceder al panel de administración:**
    ```bash
    python manage.py createsuperuser
    ```

8. **Inicia el servidor de desarrollo:**
    ```bash
    python manage.py runserver
    ```

9. **Abre tu navegador web y navega a `http://127.0.0.1:8000` para ver la aplicación en funcionamiento.**

## Configuración

Configura tu base de datos y otros ajustes en `settings.py` según tus necesidades. Algunos ajustes clave incluyen:

- **DATABASES**: Configuración de la base de datos.
- **INSTALLED_APPS**: Aplicaciones instaladas.
- **MIDDLEWARE**: Middleware utilizado.
- **TEMPLATES**: Configuración de las plantillas.

## Configuración en Visual Studio Code

Para un desarrollo eficiente en VSCode, se recomienda instalar las siguientes extensiones:

1. **Python**: Proporciona soporte para Python, incluyendo IntelliSense, linting y depuración.
2. **Django**: Extiende las capacidades de VSCode para soportar el desarrollo con Django.
3. **Pylint**: Para el análisis estático del código Python.
4. **Prettier**: Para formatear el código automáticamente.

## Estructura del Proyecto

```plaintext
Proyecto de Reserva de Habitaciones
hotel_booking/
├── hotel_booking/
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── reservations/
│   ├── migrations/
│   │   ├── 001_initial.html
│   │   ├── 002_room_image_url_room_other_images.html
│   │   └── _init_.py
│   ├── templates/
│   │   ├── registration/
│   │   │   ├── login.html
│   │   │   └── register.html
│   │   ├── reservations/
│   │   │   ├── home.html
│   │   │   ├── login.html
│   │   │   ├── ocuppancy_stats.html
│   │   │   ├── reservations_confirmation.html
│   │   │   ├── reserve_room.html
│   │   │   ├── room_detail.html
│   │   │   ├── room_list.html
│   │   │   └── signup.html
│   │   ├── __init__.py
│   │   ├── admin.py
│   │   ├── apps.py
│   │   ├── forms.py
│   │   ├── models.py
│   │   ├── serializers.py
│   │   ├── tests.py
│   │   ├── urls.py
│   │   └── views.py
│   ├── db.sqlite3
│   └── manage.py
└── README.md
└── requirements.txt
```
- hotel_booking: Contiene la configuración del proyecto Django.
- migrations: Aplicación que maneja las funcionalidades relacionadas con las habitaciones.
- templates: Plantillas HTML.

Comentarios en el Código
A continuación se incluyen comentarios en el código. Estos comentarios deben colocarse para mejorar la legibilidad y comprensión del código.

models.py
```bash
from django.db import models

class Room(models.Model):
    """
    Modelo que representa una habitación en el sistema.
    """
    room_number = models.CharField(max_length=10, unique=True)  # Número de habitación único
    room_type = models.CharField(max_length=50)  # Tipo de habitación (e.g., Simple, Doble)
    description = models.TextField()  # Descripción de la habitación
    price = models.DecimalField(max_digits=8, decimal_places=2)  # Precio de la habitación

    def __str__(self):
        return self.room_number

class Reservation(models.Model):
    """
    Modelo que representa una reserva de habitación.
    """
    room = models.ForeignKey(Room, on_delete=models.CASCADE)  # Habitación reservada
    check_in = models.DateField()  # Fecha de check-in
    check_out = models.DateField()  # Fecha de check-out
    user = models.ForeignKey('auth.User', on_delete=models.CASCADE)  # Usuario que realiza la reserva

    def __str__(self):
        return f'Reserva de {self.user.username} en la habitación {self.room.room_number}'
"""
```
views.py
```bash
from django.shortcuts import render, get_object_or_404, redirect
from .models import Room, Reservation
from .forms import ReservationForm

def home_view(request):
    """
    Vista para la página principal.
    """
    return render(request, 'home.html')

def room_list_view(request):
    """
    Vista para mostrar la lista de habitaciones.
    """
    rooms = Room.objects.all()  # Obtener todas las habitaciones
    return render(request, 'room_list.html', {'rooms': rooms})

def reserve_room_view(request, room_id):
    """
    Vista para reservar una habitación.
    """
    room = get_object_or_404(Room, id=room_id)  # Obtener la habitación o mostrar un 404
    if request.method == 'POST':
        form = ReservationForm(request.POST)
        if form.is_valid():
            # Guardar la reserva
            reservation = form.save(commit=False)
            reservation.room = room
            reservation.user = request.user
            reservation.save()
            return redirect('home')
    else:
        form = ReservationForm()
    return render(request, 'reserve_room.html', {'room': room, 'form': form})
"""
```
urls.py
```bash
from django.urls import path
from . import views

urlpatterns = [
    path('', views.home_view, name='home'),
    path('rooms/', views.room_list_view, name='room_list'),
    path('reserve/<int:room_id>/', views.reserve_room_view, name='reserve_room'),
]
"""
```
## API
A continuación se detallan algunas de las principales vistas y URL:

- Login: url('login', views.login_view)
- Register: url('register', views.register_view)
- Home: url('home', views.home_view)
- Room List: url('rooms', views.room_list_view)
- Reserve Room: url('reserve', views.reserve_room_view)

## Contribuciones
Si deseas contribuir a este proyecto, por favor haz un fork del repositorio y envía un pull request. Todas las contribuciones son bienvenidas.

### Archivos y Comentarios

#### `models.py`

```python
from django.db import models

class Room(models.Model):
    """
    Modelo que representa una habitación en el sistema.
    """
    room_number = models.CharField(max_length=10, unique=True)  # Número de habitación único
    room_type = models.CharField(max_length=50)  # Tipo de habitación (e.g., Simple, Doble)
    description = models.TextField()  # Descripción de la habitación
    price = models.DecimalField(max_digits=8, decimal_places=2)  # Precio de la habitación

    def __str__(self):
        return self.room_number

class Reservation(models.Model):
    """
    Modelo que representa una reserva de habitación.
    """
    room = models.ForeignKey(Room, on_delete=models.CASCADE)  # Habitación reservada
    check_in = models.DateField()  # Fecha de check-in
    check_out = models.DateField()  # Fecha de check-out
    user = models.ForeignKey('auth.User', on_delete=models.CASCADE)  # Usuario que realiza la reserva

    def __str__(self):
        return f'Reserva de {self.user.username} en la habitación {self.room.room_number}'
views.py

from django.shortcuts import render, get_object_or_404, redirect
from .models import Room, Reservation
from .forms import ReservationForm

def home_view(request):
    """
    Vista para la página principal.
    """
    return render(request, 'home.html')

def room_list_view(request):
    """
    Vista para mostrar la lista de habitaciones.
    """
    rooms = Room.objects.all()  # Obtener todas las habitaciones
    return render(request, 'room_list.html', {'rooms': rooms})

def reserve_room_view(request, room_id):
    """
    Vista para reservar una habitación.
    """
    room = get_object_or_404(Room, id=room_id)  # Obtener la habitación o mostrar un 404
    if request.method == 'POST':
        form = ReservationForm(request.POST)
        if form.is_valid():
            # Guardar la reserva
            reservation = form.save(commit=False)
            reservation.room = room
            reservation.user = request.user
            reservation.save()
            return redirect('home')
    else:
        form = ReservationForm()
    return render(request, 'reserve_room.html', {'room': room, 'form': form})
urls.py
python
Copiar código
from django.urls import path
from . import views

urlpatterns = [
    path('', views.home_view, name='home'),
    path('rooms/', views.room_list_view, name='room_list'),
    path('reserve/<int:room_id>/', views.reserve_room_view, name='reserve_room'),
]
