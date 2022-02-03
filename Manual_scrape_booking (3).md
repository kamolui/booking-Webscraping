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


```python
! pip install selectorlib
! pip install selenium
```

    Collecting selectorlib
      Downloading selectorlib-0.16.0-py2.py3-none-any.whl (5.8 kB)
    Requirement already satisfied: pyyaml>=3.12 in d:\conda\lib\site-packages (from selectorlib) (5.4.1)
    Requirement already satisfied: Click>=6.0 in d:\conda\lib\site-packages (from selectorlib) (7.1.2)
    Collecting parsel>=1.5.1
      Downloading parsel-1.6.0-py2.py3-none-any.whl (13 kB)
    Collecting w3lib>=1.19.0
      Downloading w3lib-1.22.0-py2.py3-none-any.whl (20 kB)
    Requirement already satisfied: six>=1.6.0 in d:\conda\lib\site-packages (from parsel>=1.5.1->selectorlib) (1.15.0)
    Requirement already satisfied: lxml in d:\conda\lib\site-packages (from parsel>=1.5.1->selectorlib) (4.6.3)
    Collecting cssselect>=0.9
      Downloading cssselect-1.1.0-py2.py3-none-any.whl (16 kB)
    Installing collected packages: w3lib, cssselect, parsel, selectorlib
    Successfully installed cssselect-1.1.0 parsel-1.6.0 selectorlib-0.16.0 w3lib-1.22.0
    Collecting selenium
      Downloading selenium-4.0.0-py3-none-any.whl (954 kB)
    Collecting trio-websocket~=0.9
      Downloading trio_websocket-0.9.2-py3-none-any.whl (16 kB)
    Requirement already satisfied: urllib3[secure]~=1.26 in d:\conda\lib\site-packages (from selenium) (1.26.4)
    Collecting trio~=0.17
      Downloading trio-0.19.0-py3-none-any.whl (356 kB)
    Requirement already satisfied: sniffio in d:\conda\lib\site-packages (from trio~=0.17->selenium) (1.2.0)
    Requirement already satisfied: cffi>=1.14 in d:\conda\lib\site-packages (from trio~=0.17->selenium) (1.14.5)
    Requirement already satisfied: attrs>=19.2.0 in d:\conda\lib\site-packages (from trio~=0.17->selenium) (20.3.0)
    Requirement already satisfied: sortedcontainers in d:\conda\lib\site-packages (from trio~=0.17->selenium) (2.3.0)
    Requirement already satisfied: idna in d:\conda\lib\site-packages (from trio~=0.17->selenium) (2.10)
    Collecting outcome
      Downloading outcome-1.1.0-py2.py3-none-any.whl (9.7 kB)
    Requirement already satisfied: async-generator>=1.9 in d:\conda\lib\site-packages (from trio~=0.17->selenium) (1.10)
    Requirement already satisfied: pycparser in d:\conda\lib\site-packages (from cffi>=1.14->trio~=0.17->selenium) (2.20)
    Collecting wsproto>=0.14
      Downloading wsproto-1.0.0-py3-none-any.whl (24 kB)
    Requirement already satisfied: certifi in d:\conda\lib\site-packages (from urllib3[secure]~=1.26->selenium) (2020.12.5)
    Requirement already satisfied: cryptography>=1.3.4 in d:\conda\lib\site-packages (from urllib3[secure]~=1.26->selenium) (3.4.7)
    Requirement already satisfied: pyOpenSSL>=0.14 in d:\conda\lib\site-packages (from urllib3[secure]~=1.26->selenium) (20.0.1)
    Requirement already satisfied: six>=1.5.2 in d:\conda\lib\site-packages (from pyOpenSSL>=0.14->urllib3[secure]~=1.26->selenium) (1.15.0)
    Collecting h11<1,>=0.9.0
      Downloading h11-0.12.0-py3-none-any.whl (54 kB)
    Installing collected packages: outcome, h11, wsproto, trio, trio-websocket, selenium
    Successfully installed h11-0.12.0 outcome-1.1.0 selenium-4.0.0 trio-0.19.0 trio-websocket-0.9.2 wsproto-1.0.0
    

# El código
----
Creamos el folder del proyecto llamándolo Booking_scraping, en el folder añade un archivo .py con nombre scrape.py. Copia el código de abajo a ese archivo.


```python
! pip install maya
```

    Collecting maya
      Downloading maya-0.6.1-py2.py3-none-any.whl (12 kB)
    Collecting pendulum>=2.0.2
      Downloading pendulum-2.1.2-cp38-cp38-win_amd64.whl (129 kB)
    Collecting snaptime
      Downloading snaptime-0.2.4.tar.gz (2.9 kB)
    Requirement already satisfied: pytz in d:\conda\lib\site-packages (from maya) (2021.1)
    Collecting tzlocal
      Using cached tzlocal-4.1-py3-none-any.whl (19 kB)
    Collecting dateparser>=0.7.0
      Downloading dateparser-1.1.0-py2.py3-none-any.whl (288 kB)
    Collecting humanize
      Downloading humanize-3.12.0-py3-none-any.whl (90 kB)
    Requirement already satisfied: regex!=2019.02.19,!=2021.8.27 in d:\conda\lib\site-packages (from dateparser>=0.7.0->maya) (2021.4.4)
    Requirement already satisfied: python-dateutil in d:\conda\lib\site-packages (from dateparser>=0.7.0->maya) (2.8.1)
    Collecting pytzdata>=2020.1
      Downloading pytzdata-2020.1-py2.py3-none-any.whl (489 kB)
    Requirement already satisfied: six>=1.5 in d:\conda\lib\site-packages (from python-dateutil->dateparser>=0.7.0->maya) (1.15.0)
    Requirement already satisfied: setuptools in d:\conda\lib\site-packages (from humanize->maya) (52.0.0.post20210125)
    Collecting pytz-deprecation-shim
      Using cached pytz_deprecation_shim-0.1.0.post0-py2.py3-none-any.whl (15 kB)
    Collecting tzdata
      Using cached tzdata-2021.5-py2.py3-none-any.whl (339 kB)
    Collecting backports.zoneinfo
      Downloading backports.zoneinfo-0.2.1-cp38-cp38-win_amd64.whl (38 kB)
    Building wheels for collected packages: snaptime
      Building wheel for snaptime (setup.py): started
      Building wheel for snaptime (setup.py): finished with status 'done'
      Created wheel for snaptime: filename=snaptime-0.2.4-py3-none-any.whl size=3594 sha256=713e8087889135d2f431057198c984058f44108aa73332e8e12bef24e6f69a4c
      Stored in directory: c:\users\pata_\appdata\local\pip\cache\wheels\ef\75\ba\deb00489e86fe8ed045887958363a3ddf664227d0bab7f03a2
    Successfully built snaptime
    Installing collected packages: tzdata, backports.zoneinfo, pytz-deprecation-shim, tzlocal, pytzdata, snaptime, pendulum, humanize, dateparser, maya
    Successfully installed backports.zoneinfo-0.2.1 dateparser-1.1.0 humanize-3.12.0 maya-0.6.1 pendulum-2.1.2 pytz-deprecation-shim-0.1.0.post0 pytzdata-2020.1 snaptime-0.2.4 tzdata-2021.5 tzlocal-4.1
    


```python
print('Las reservaciones deben ser menores a 30 días. Los días y meses deben ser escritos con números')


# Datos de entrada para que Booking nos muestre los precios por fecha y huespedes
ch_y = '2021'
ch_m = input('Mes de entrada: ')
while ch_m.isdigit()==False:
    print("escribiste letras, escribe con número el mes")
    ch_m = input('Mes de entrada: ')
ch_d =input('Día de entrada: ')
while ch_d.isdigit()==False:
    print("escribiste letras, escribe con número el día")
    ch_d = input('Día de entrada: ')

co_y = '2021'
co_m = input('Mes de salida: ')
while co_m.isdigit()==False:
    print("escribiste letras, escribe con número el mes")
    co_m = input('Mes de salida: ')
co_d = input('Día de salida: ')
while co_d.isdigit()==False:
    print("escribiste letras, escribe con número el día")
    co_d = input('día de entrada: ')


#n_adlt = input('Adultos: ')
n_adlt = '2'


from time import sleep
import pandas as pd
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By
import time

import random
import selectorlib
from selectorlib import Extractor
from selenium import webdriver
import time
import pandas as pd

options = webdriver.ChromeOptions()
options.add_argument('--start-maximized')
options.add_argument('--disable-extensions')


PATH = r"D:\Documentos\Inteligencia Fsical\webscrapping\booking\chromedriver.exe"
driver = webdriver.Chrome(PATH, options=options) # Navegador a usar 


#Numero de registros que se usarán
driver.get('https://www.booking.com/searchresults.es-mx.html?aid=375648&label=msn-b3YDyMo0k8Dj47_gBBiUxA-80470542858153%3Atikwd'+
       '-80470558197349%3Aloc-119%3Aneo%3Amte%3Adec%3Aqsbooking&sid=879aa0e7594d497286f4b18dc15ff64b&sb=1&src=searchresults&src'+
       '_elem=sb&error_url=https%3A%2F%2Fwww.booking.com%2Fsearchresults.es-mx.html%3Faid%3D375648%3Blabel%3Dmsn-b3YDyMo0k8Dj47_'+
       'gBBiUxA-80470542858153%253Atikwd-80470558197349%253Aloc-119%253Aneo%253Amte%253Adec%253Aqsbooking%3Bsid%3D879aa0e7594d4972'+
       '86f4b18dc15ff64b%3Btmpl%3Dsearchresults%3Bac_click_type%3Db%3Bac_position%3D0%3Bcheckin_month%3D'+str(ch_m)+
       '%3Bcheckin_monthday%3D'+str(ch_d)+'%3Bcheckin_year%3D'+str(ch_y)+'%3Bcheckout_month%3D'+str(co_m)+
       '%3Bcheckout_monthday%3D'+str(co_d)+'%3Bcheckout_year%3D'+co_y+
       '%3Bclass_interval%3D1%3Bdest_id%3D-1658079%3Bdest_type%3Dcity%3Bdtdisc%3D0%3Bfrom_sf%3D1%3Bgroup_adults%3D1%3Bgroup_'+
       'children%3D0%3Biata%3DMEX%3Binac%3D0%3Bindex_postcard%3D0%3Blabel_click%3Dundef%3Bno_rooms%3D1%3Boffset%3D0%3Bpostcard%3D0%3B'+
       'raw_dest_type%3Dcity%3Broom1%3DA%3Brows%3D25%3Bsb_price_type%3Dtotal%3Bsearch_selected%3D1%3Bshw_aparth%3D1%3Bslp_r_'+
       'match%3D0%3Bsrc%3Dindex%3Bsrc_elem%3Dsb%3Bsrpvid%3D6dc989dd88cd0220%3Bss%3DCiudad%2520de%2520M%25C3%25A9xico%252C%2520'+
       'M%25C3%25A9xico%2520DF%252C%2520M%25C3%25A9xico%3Bss_all%3D0%3Bss_raw%3Dciudad%2520de%3Bssb%3Dempty%3Bsshis%3D0%3Btop'+
       '_ufis%3D1%26%3B&ss=Ciudad+de+M%C3%A9xico&is_ski_area=0&ssne=Ciudad+de+M%C3%A9xico&ssne_untouched=Ciudad+de+M%C3%A9xico&'+
       'city=-1658079&checkin_year='+str(ch_y)+'&checkin_month='+str(ch_m)+'&checkin_monthday='+
       str(ch_d)+'&checkout_year='+str(co_y)+'&checkout_month='+str(co_m)+'&checkout_monthday='+
       str(co_d)+'&group_adults='+str(n_adlt)+'&group_children=0&no_rooms=1&sb_changed_dates=1&from_sf=1')

registro = driver.find_element_by_xpath('//h1[@class="_30227359d _0db903e42"]')
registro = registro.text
registro
reg=[]
reg.append(registro)
reg=pd.DataFrame(reg)
reg= reg.replace(" alojamientos encontrados","", regex=True)
reg= reg.replace("Ciudad de México: ","", regex=True)
reg= reg.replace(",","", regex=True)
reg=pd.to_numeric(reg[0])[0]

# Url princiapl de la pagina Booking que ya contiene la informacion sobre CDMX, a este link se 
# le agregan los datos del primer paso, para obtener los resultados deseados 

url = ['https://www.booking.com/searchresults.es-mx.html?aid=375648&label=msn-b3YDyMo0k8Dj47_gBBiUxA-80470542858153%3Atikwd'+
       '-80470558197349%3Aloc-119%3Aneo%3Amte%3Adec%3Aqsbooking&sid=879aa0e7594d497286f4b18dc15ff64b&sb=1&src=searchresults&src'+
       '_elem=sb&error_url=https%3A%2F%2Fwww.booking.com%2Fsearchresults.es-mx.html%3Faid%3D375648%3Blabel%3Dmsn-b3YDyMo0k8Dj47_'+
       'gBBiUxA-80470542858153%253Atikwd-80470558197349%253Aloc-119%253Aneo%253Amte%253Adec%253Aqsbooking%3Bsid%3D879aa0e7594d4972'+
       '86f4b18dc15ff64b%3Btmpl%3Dsearchresults%3Bac_click_type%3Db%3Bac_position%3D0%3Bcheckin_month%3D'+str(ch_m)+
       '%3Bcheckin_monthday%3D'+str(ch_d)+'%3Bcheckin_year%3D'+str(ch_y)+'%3Bcheckout_month%3D'+str(co_m)+
       '%3Bcheckout_monthday%3D'+str(co_d)+'%3Bcheckout_year%3D'+co_y+
       '%3Bclass_interval%3D1%3Bdest_id%3D-1658079%3Bdest_type%3Dcity%3Bdtdisc%3D0%3Bfrom_sf%3D1%3Bgroup_adults%3D1%3Bgroup_'+
       'children%3D0%3Biata%3DMEX%3Binac%3D0%3Bindex_postcard%3D0%3Blabel_click%3Dundef%3Bno_rooms%3D1%3Boffset%3D0%3Bpostcard%3D0%3B'+
       'raw_dest_type%3Dcity%3Broom1%3DA%3Brows%3D25%3Bsb_price_type%3Dtotal%3Bsearch_selected%3D1%3Bshw_aparth%3D1%3Bslp_r_'+
       'match%3D0%3Bsrc%3Dindex%3Bsrc_elem%3Dsb%3Bsrpvid%3D6dc989dd88cd0220%3Bss%3DCiudad%2520de%2520M%25C3%25A9xico%252C%2520'+
       'M%25C3%25A9xico%2520DF%252C%2520M%25C3%25A9xico%3Bss_all%3D0%3Bss_raw%3Dciudad%2520de%3Bssb%3Dempty%3Bsshis%3D0%3Btop'+
       '_ufis%3D1%26%3B&ss=Ciudad+de+M%C3%A9xico&is_ski_area=0&ssne=Ciudad+de+M%C3%A9xico&ssne_untouched=Ciudad+de+M%C3%A9xico&'+
       'city=-1658079&checkin_year='+str(ch_y)+'&checkin_month='+str(ch_m)+'&checkin_monthday='+
       str(ch_d)+'&checkout_year='+str(co_y)+'&checkout_month='+str(co_m)+'&checkout_monthday='+
       str(co_d)+'&group_adults='+str(n_adlt)+'&group_children=0&no_rooms=1&sb_changed_dates=1&from_sf=1']

# Lista donde se guardaran todos los links de la pagina Booking
l = []


# Booking muestra 25 hoteles por pagina, entonces cargaremos 25 hoteles por link, /
# cambiando el valor "set" del link y añadiendo la informacion del paso 1
for pages in range(25,reg+25,25):

    urls = 'https://www.booking.com/searchresults.es-mx.html?aid=375648&label=msn-b3YDyMo0k8Dj47_gBBiUxA-80470542858153%3Atikwd-80470558197349%3Aloc-119%3Aneo%3Amte%3Adec%3Aqsbooking&sid=879aa0e7594d497286f4b18dc15ff64b&tmpl=searchresults&checkin_month='+str(ch_m)+'&checkin_monthday='+str(ch_d)+'&checkin_year='+str(ch_y)+'&checkout_month='+str(co_m)+'&checkout_monthday='+str(co_d)+'&checkout_year='+str(co_y)+'&city=-1658079&class_interval=1&dest_id=-1658079&dest_type=city&dtdisc=0&from_sf=1&group_adults='+str(n_adlt)+'&group_children=0&inac=0&index_postcard=0&label_click=undef&no_rooms=1&postcard=0&room1=A%2CA&sb_price_type=total&shw_aparth=1&slp_r_match=0&src=searchresults&src_elem=sb&srpvid=2e79718f93550095&ss=Ciudad%20de%20M%C3%A9xico&ss_all=0&ssb=empty&sshis=0&ssne=Ciudad%20de%20M%C3%A9xico&ssne_untouched=Ciudad%20de%20M%C3%A9xico&top_ufis=1&rows=25&offset=' + str(pages)
    
    l.append(urls) # Se guardan todos los links generados en la lista l

# Lista final donde se encuentran todos los link de Booking, se suman el link principal y todos los links generados
urls = url + l

# Lista donde se guardaran los datos de los hoteles en formato Dict
hoteles = []


# Se recorren todos los links
for i in range(len(urls)):
    
    # Selenium nos lleva a cada link
    driver.get(urls[i])

    #Tiempo de espera aleatorio para que cargue la pag
    time.sleep(random.uniform(.1,3.75))

    # Obtenemos el HTML del driver, extraemos la informacion con SelectorLib Extractor.
    raw_html = driver.page_source # HTML de la pag
    extracted_text = Extractor.from_yaml_file('booking.yml') # Extraccion de la info 

    raw_data = extracted_text.extract(raw_html) # Datos en crudo

    data = raw_data['Hotel'] # Guardamos la informacion de los hoteles
    hoteles.append(data) # Se guarda la info en la lista hoteles

#Informacion de la primer pagina en formato Dic
#print(hoteles[0])

#Lista donde se guardaran todos los Df con la informacion
u = []


# Recorremos todos los dict y los transformamos en df
for j in range(len(urls)):
    
    df = pd.DataFrame.from_dict(hoteles[j])
    u.append(df)

df = pd.concat(u) #Union de todos los df

l = df.columns.values


# Limpiamos los df, ya que cada celda era una lista y podria dar problemas
for i in range(len(l)):
    df[l[i]] = df[l[i]].apply(pd.Series)

    
df["Price"]=df["Price"].str[4:]  
df["Discount"]=df["Discount"].str[4:]
df['Tax'] = df['Tax'].str[6:]

df['Price'] = df['Price'].replace(',','', regex=True)
df['Tax'] = df['Tax'].replace('de impuestos y cargos', '', regex=True)
df['Tax'] = df['Tax'].replace('e impuestos y cargos', '', regex=True)
df['Tax'] = df['Tax'].replace(',', '', regex=True)
df['Coments'] = df['Coments'].replace('comentarios', '', regex=True)
df['Discount'] = df['Discount'].replace(',', '', regex=True)
df['Coments'] = df['Coments'].replace(',', '', regex=True)
df['Coments'] = df['Coments'].replace('externos', '', regex=True)
df["Bed"]= df["Bed"].replace("\n", "", regex=True)

#A, B = df["Cor"].str.split(",", 1).str
#df["coordenadas"]= B+","+A
#df=df.drop(["Cor","Stars"], axis=1)

df['Price'] = pd.to_numeric(df['Price'])
df['Tax'] = pd.to_numeric(df['Tax'])
df['Coments'] = pd.to_numeric(df['Coments'])
df['Discount'] = pd.to_numeric(df['Discount'])
   
df

df.describe()

from datetime import date, datetime

today = date.today()
#dd/mm/YY

f_consulta = today.strftime("%d/%m/%Y")

df["Consulta"] = f_consulta

d_e = str(ch_d)+'/'+str(ch_m)+'/2021'

import maya
d_e = maya.parse(d_e).datetime(naive=False)

df["Entrada"] = d_e.date()

d_s = str(co_d)+'/'+str(co_m)+'/2021'
d_s = maya.parse(d_s).datetime(naive=False)

df["Salida"] = d_s.date()

df["Estancia"] = 1

#df.to_excel('booking_month'+str(ch_m)+'_day'+str(ch_d)+'_emonth'+str(co_m)+'_eday'+str(co_d)+'.xlsx',
#         index=False, encoding="utf-16")

driver.quit()
print('Se obtuvo la informacion conexito!!!')
```

    Las reservaciones deben ser menores a 30 días. Los días y meses deben ser escritos con números
    Mes de entrada: 12
    Día de entrada: 23
    Mes de salida: 12
    Día de salida: 30
    

    <ipython-input-1-3129a588cab2>:50: DeprecationWarning: executable_path has been deprecated, please pass in a Service object
      driver = webdriver.Chrome(PATH, options=options) # Navegador a usar
    <ipython-input-1-3129a588cab2>:71: DeprecationWarning: find_element_by_* commands are deprecated. Please use find_element() instead
      registro = driver.find_element_by_xpath('//h1[@class="_30227359d _0db903e42"]')
    


    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    pandas\_libs\lib.pyx in pandas._libs.lib.maybe_convert_numeric()
    

    ValueError: Unable to parse string "5  externos"

    
    During handling of the above exception, another exception occurred:
    

    ValueError                                Traceback (most recent call last)

    <ipython-input-1-3129a588cab2> in <module>
        179 df['Price'] = pd.to_numeric(df['Price'])
        180 df['Tax'] = pd.to_numeric(df['Tax'])
    --> 181 df['Coments'] = pd.to_numeric(df['Coments'])
        182 df['Discount'] = pd.to_numeric(df['Discount'])
        183 
    

    D:\conda\lib\site-packages\pandas\core\tools\numeric.py in to_numeric(arg, errors, downcast)
        152         coerce_numeric = errors not in ("ignore", "raise")
        153         try:
    --> 154             values = lib.maybe_convert_numeric(
        155                 values, set(), coerce_numeric=coerce_numeric
        156             )
    

    pandas\_libs\lib.pyx in pandas._libs.lib.maybe_convert_numeric()
    

    ValueError: Unable to parse string "5  externos" at position 276



```python
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Hotel_name</th>
      <th>Location</th>
      <th>Price</th>
      <th>Discount</th>
      <th>Rating_n</th>
      <th>Rating</th>
      <th>Type_room</th>
      <th>Tax</th>
      <th>Bed</th>
      <th>Guest</th>
      <th>Coments</th>
      <th>coments</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Hostal Regina Down Town Mexico City</td>
      <td>Centro histórico de México D.F., Ciudad de México</td>
      <td>4195.0</td>
      <td>3985</td>
      <td>9.2</td>
      <td>Fantástico</td>
      <td>Habitación Doble Estándar con baño compartido</td>
      <td>818.0</td>
      <td>2 camas matrimoniales</td>
      <td>7 noches, 2 adultos</td>
      <td>930</td>
      <td>930.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Ibis Mexico Alameda</td>
      <td>Centro histórico de México D.F., Ciudad de México</td>
      <td>NaN</td>
      <td>8781</td>
      <td>7.8</td>
      <td>Bien</td>
      <td>Habitación Estándar - 1 cama grande</td>
      <td>1712.0</td>
      <td>1 cama matrimonial</td>
      <td>7 noches, 2 adultos</td>
      <td>2653</td>
      <td>2653.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Hyatt House Mexico City Santa Fe</td>
      <td>Santa Fe, Ciudad de México</td>
      <td>NaN</td>
      <td>9590</td>
      <td>8.9</td>
      <td>Fabuloso</td>
      <td>Habitación Estándar - Cama extragrande</td>
      <td>1870.0</td>
      <td>1 cama King size</td>
      <td>7 noches, 2 adultos</td>
      <td>591</td>
      <td>591.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>NH Collection México City Centro Histórico</td>
      <td>Centro histórico de México D.F., Ciudad de México</td>
      <td>NaN</td>
      <td>8028</td>
      <td>8.7</td>
      <td>Fabuloso</td>
      <td>Habitación Doble Premium - 1 o 2 camas</td>
      <td>1566.0</td>
      <td>Camas: 1 doble o 2 individuales</td>
      <td>7 noches, 2 adultos</td>
      <td>701</td>
      <td>701.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Hotel Metropol</td>
      <td>Centro histórico de México D.F., Ciudad de México</td>
      <td>NaN</td>
      <td>6741</td>
      <td>8.4</td>
      <td>Muy bien</td>
      <td>Habitación Estándar - 1 cama grande</td>
      <td>1314.0</td>
      <td>1 cama matrimonial</td>
      <td>7 noches, 2 adultos</td>
      <td>832</td>
      <td>832.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Ilivedorms</td>
      <td>Ciudad de México</td>
      <td>NaN</td>
      <td>7700</td>
      <td>7.8</td>
      <td>Bien</td>
      <td>Estudio con 1 cama grande</td>
      <td>1502.0</td>
      <td>2 suites privadas</td>
      <td>7 noches, 2 adultos</td>
      <td>12</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Hotel de Mexico BelAir Collection</td>
      <td>Nápoles, Ciudad de México</td>
      <td>NaN</td>
      <td>26036</td>
      <td>8.7</td>
      <td>Fabuloso</td>
      <td>Habitación Individual Estándar</td>
      <td>5077.0</td>
      <td>2 camas individuales</td>
      <td>7 noches, 2 adultos</td>
      <td>339</td>
      <td>339.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Mambú Hostal</td>
      <td>Venustiano Carranza, Ciudad de México</td>
      <td>4900.0</td>
      <td>4190</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Habitación Individual</td>
      <td>860.0</td>
      <td>2 camas individuales</td>
      <td>7 noches, 2 adultos</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Roof top apartment</td>
      <td>Coyoacán, Ciudad de México</td>
      <td>8332.0</td>
      <td>7915</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Apartamento de 1 dormitorio</td>
      <td>1625.0</td>
      <td>2 departamentos completos • 2 recámaras • 2 baños</td>
      <td>7 noches, 2 adultos</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Habitaciones Económicas CDMX</td>
      <td>Ciudad de México</td>
      <td>5040.0</td>
      <td>4788</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Habitación Individual Estándar con baño compar...</td>
      <td>983.0</td>
      <td>2 camas individuales</td>
      <td>7 noches, 2 adultos</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>834 rows × 12 columns</p>
</div>




```python
df.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Price</th>
      <th>Tax</th>
      <th>coments</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>574.000000</td>
      <td>829.000000</td>
      <td>647.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>16697.388502</td>
      <td>2896.197829</td>
      <td>380.493045</td>
    </tr>
    <tr>
      <th>std</th>
      <td>33703.814599</td>
      <td>6682.284188</td>
      <td>648.259603</td>
    </tr>
    <tr>
      <th>min</th>
      <td>723.000000</td>
      <td>57.000000</td>
      <td>3.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>6176.500000</td>
      <td>1054.000000</td>
      <td>28.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>10290.000000</td>
      <td>1593.000000</td>
      <td>131.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>17850.000000</td>
      <td>2877.000000</td>
      <td>479.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>437757.000000</td>
      <td>104268.000000</td>
      <td>9204.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
import seaborn as sns
import matplotlib.pyplot as plt
import numpy as np


sns.displot(np.log(df['Price']),kde = False)
plt.show()
```


    
![png](output_10_0.png)
    



```python
import matplotlib.pyplot as plt
import seaborn as sns
sns.set_theme(style="ticks")

df['Price_log'] = np.log(df['Price'])
df['Tax_log'] = np.log(df['Tax'])
df['Coments_log'] = np.log(df['coments'])


sns.pairplot(df[['Price_log', 'Tax_log', 'Coments_log']])

```




    <seaborn.axisgrid.PairGrid at 0x17d978901f0>




    
![png](output_11_1.png)
    


El código de arriba hará lo siguiente:
* Te pedirá los datos necesarios para realizar tu búsqueda de hoteles.
* Definirá el navegador a usar, en este caso Chrome.
* Ira a la página de Booking.com con la información proporcionada y guardara el enlace.
* Guardara todas las páginas con resultados de Booking.com.
* Ira a cada uno de los links que guardo y obtendrá el código HTML.
* Extraerá solo los datos solicitados y los guardará en formato Dict.
* Convertirá esos Dict a DF, para una exportación más sencilla.
* Guardara el DF como archivo .csv.

## Crear el Selectorlib Template para realizar el Scraping en Booking.com
---
Notaras que en el código de arriba hay un archivo llamado `Booking_c.YML` este archivo hará el código más simple y conciso. Todo gracias a la herramienta de Web Sraping Selectorlib.

Selectorlib es una herramienta que hace la selección, remarcado y extracción de datos de páginas web más fácil y visual. La extensión Selectorlib de Google te permite marcar los datos que necesitas, crea los selectores CSS o los XPATH necesarios para extraer los datos. Y al final obtener una vista preliminar de los mismos.

Puedes leer más del uso de Selectorlib aquí https://selectorlib.com/getting-started.html.


Aquí se muestra como marcamos los campos de los datos que necesitamos extraer usando la extensión de Google Selectorlib.

![alt text](images/photo4945146201708276099.jpg)

Una vez que creaste el template, da click en 'Highlight' para remarcar y tener una vista previa de todos tus selectores. Finalmente, da click en 'Export', descarga el archivo YAML y ese archivo es el archivo Booking_c.yml.

![alt text](images/photo4945146201708276100.jpg)

En resumen este programa tiene la capacidad de acceder a los datos de interés de la página de booking. Es un trabajo que requiere primero obtener los datos desde la inspección de la página, a la par que se usa Selectorlib; una vez que se ha recolectado la información requerida, es imperativo guardarla en un formato "yml", de manera que sea compatible con el programa y las librerías de Python. No olvidar instalar las librerías de Selenium y Selectorlib, pues son los pilares del código.
Al introducir los datos de meses y días, se debe tener cuidado de introducir fechas coherentes, pues de poner fechas previas al día que se está usando el programa, regresará un error.
El programa correrá de tal manera que recolectará la información de cada hotel, página por página, hasta llegar al último hotel; creará un data frame con las siguientes columnas:
Hotel_name, el nombre del hotel; Location, su dirección; Price, el precio; Discount, el descuento, si es que se aplica; Rating_n, la calificación numérica; Rating, calificación categórica; Type_room, tipo de cuarto; Tax, el impuesto que se pueda aplicar; Bed, tipo y cantidad de camas; Guest, noche y cantidad de adultos y Coments, los comentarios de quienes han usado el hotel.
Al final se obtendrá un data frame con el nombre "df" junto con su descripción de estadísticos.

Así es como se ve nuestro Template de Booking.com.

![alt text](images/photo4945146201708276101.jpg)

## Corriendo el Scraping
---
* Para correr el scraping ve a booking.com, busca los hoteles que necesitas, guarda la información que ingresaste.

* Corre el archivo .py que creaste.
Este paso dependerá mucho del IDE que uses para correr Python, yo recomiendo los siguientes:
    1.	Spyder
    2.	JupyterNotebook
    3.	Pycharm
    4.	Google Colab

Los primeros 2 los encuentras en tu entorno Anaconda.

* Agrega la información que guardaste de los hoteles cuando te la solicite el programa.

* Espera a que el programa busque todos los links.

* Visualiza los resultados.

Si estas usando Jupyter Notebooks (como este archivo) puedes correr el programa aquí mismo, dando click a la celda donde se encuentre el código y presiona `shift + enter`. El programa correrá, te pedirá la información necesaria y cuando termine mostrará los resultados en el notebook.
Si estas viendo el archivo markdown no será posible correr el código aquí. 

Aquí hay un ejemplo de los datos que da el programa.


![alt text](images/photo4945146201708276102.jpg)


```python
pip install nbconvert
```

    Requirement already satisfied: nbconvert in d:\conda\lib\site-packages (6.0.7)
    Requirement already satisfied: traitlets>=4.2 in d:\conda\lib\site-packages (from nbconvert) (5.0.5)
    Requirement already satisfied: mistune<2,>=0.8.1 in d:\conda\lib\site-packages (from nbconvert) (0.8.4)
    Requirement already satisfied: jupyterlab-pygments in d:\conda\lib\site-packages (from nbconvert) (0.1.2)
    Requirement already satisfied: nbformat>=4.4 in d:\conda\lib\site-packages (from nbconvert) (5.1.3)
    Requirement already satisfied: defusedxml in d:\conda\lib\site-packages (from nbconvert) (0.7.1)
    Requirement already satisfied: nbclient<0.6.0,>=0.5.0 in d:\conda\lib\site-packages (from nbconvert) (0.5.3)
    Requirement already satisfied: jupyter-core in d:\conda\lib\site-packages (from nbconvert) (4.7.1)
    Requirement already satisfied: testpath in d:\conda\lib\site-packages (from nbconvert) (0.4.4)
    Requirement already satisfied: pygments>=2.4.1 in d:\conda\lib\site-packages (from nbconvert) (2.8.1)
    Requirement already satisfied: jinja2>=2.4 in d:\conda\lib\site-packages (from nbconvert) (2.11.3)
    Requirement already satisfied: bleach in d:\conda\lib\site-packages (from nbconvert) (3.3.0)
    Requirement already satisfied: pandocfilters>=1.4.1 in d:\conda\lib\site-packages (from nbconvert) (1.4.3)
    Requirement already satisfied: entrypoints>=0.2.2 in d:\conda\lib\site-packages (from nbconvert) (0.3)
    Requirement already satisfied: MarkupSafe>=0.23 in d:\conda\lib\site-packages (from jinja2>=2.4->nbconvert) (1.1.1)
    Requirement already satisfied: jupyter-client>=6.1.5 in d:\conda\lib\site-packages (from nbclient<0.6.0,>=0.5.0->nbconvert) (6.1.12)
    Requirement already satisfied: nest-asyncio in d:\conda\lib\site-packages (from nbclient<0.6.0,>=0.5.0->nbconvert) (1.5.1)
    Requirement already satisfied: async-generator in d:\conda\lib\site-packages (from nbclient<0.6.0,>=0.5.0->nbconvert) (1.10)
    Requirement already satisfied: python-dateutil>=2.1 in d:\conda\lib\site-packages (from jupyter-client>=6.1.5->nbclient<0.6.0,>=0.5.0->nbconvert) (2.8.1)
    Requirement already satisfied: tornado>=4.1 in d:\conda\lib\site-packages (from jupyter-client>=6.1.5->nbclient<0.6.0,>=0.5.0->nbconvert) (6.1)
    Requirement already satisfied: pyzmq>=13 in d:\conda\lib\site-packages (from jupyter-client>=6.1.5->nbclient<0.6.0,>=0.5.0->nbconvert) (20.0.0)
    Requirement already satisfied: pywin32>=1.0 in d:\conda\lib\site-packages (from jupyter-core->nbconvert) (227)
    Requirement already satisfied: ipython-genutils in d:\conda\lib\site-packages (from nbformat>=4.4->nbconvert) (0.2.0)
    Requirement already satisfied: jsonschema!=2.5.0,>=2.4 in d:\conda\lib\site-packages (from nbformat>=4.4->nbconvert) (3.2.0)
    Requirement already satisfied: pyrsistent>=0.14.0 in d:\conda\lib\site-packages (from jsonschema!=2.5.0,>=2.4->nbformat>=4.4->nbconvert) (0.17.3)
    Requirement already satisfied: six>=1.11.0 in d:\conda\lib\site-packages (from jsonschema!=2.5.0,>=2.4->nbformat>=4.4->nbconvert) (1.15.0)
    Requirement already satisfied: setuptools in d:\conda\lib\site-packages (from jsonschema!=2.5.0,>=2.4->nbformat>=4.4->nbconvert) (52.0.0.post20210125)
    Requirement already satisfied: attrs>=17.4.0 in d:\conda\lib\site-packages (from jsonschema!=2.5.0,>=2.4->nbformat>=4.4->nbconvert) (20.3.0)
    Requirement already satisfied: packaging in d:\conda\lib\site-packages (from bleach->nbconvert) (20.9)
    Requirement already satisfied: webencodings in d:\conda\lib\site-packages (from bleach->nbconvert) (0.5.1)
    Requirement already satisfied: pyparsing>=2.0.2 in d:\conda\lib\site-packages (from packaging->bleach->nbconvert) (2.4.7)
    Note: you may need to restart the kernel to use updated packages.
    


```python

```
