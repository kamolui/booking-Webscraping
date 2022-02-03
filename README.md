# Scraping Booking
------
Obteniendo la información de todos los hoteles disponibles en la Ciudad de México, datos recolectados de la página Booking.com

Este manual te mostrara como realizar scraping de la información disponible en la página Booking.com para hoteles en la CDMX, usando Python y Selectorlib.

## ¿Como hacer scraping en Booking?

* Busca en Booking.com hoteles con tus condiciones como localización, Ckeck in Date, Check Out Date, Cuartos, Numero de personas, etc. 
* Copia la información que ingresaste.
* Ingresa esta información en el scrape.py (se muestra más adelante).
* El Scraping descargara este link automáticamente.
* Entonces obtendremos el HTML usando Selenium y Selectorlib Template para extraer campos como Nombre, Coordenadas, Cuartos, etc.
* El Scraper guardara los datos en un archivo CSV.

Este Scraper obtendrá los siguientes datos (puedes agregar más campos si lo deseas).

* Nombre del Hotel
* Dirección del Hotel
* Precio del Hotel
* Precio sin Descuento 
* Puntaje
* Calificación
* Tipo de Cuarto
* Impuestos
* Cantidad de Camas
* Huéspedes 
* Comentarios
* Coordenadas

## Instala los paquetes necesarios para correr el Scraper de Booking
Necesitaremos los siguientes paquetes para realizar el Scraping

1.- Selenium, para hacer request y descargar el contenido HTML del link de Booking.

2.- SelectorLib, paquetería de Python para extraer datos usando el archivo YAML que crearemos con la extensión de Google.

```
! pip install selectorlib
! pip install selenium
```
