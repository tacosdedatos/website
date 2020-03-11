---
layout: post
current: post
cover:  assets/detrasdelavis/001.png
navigation: True
title: Generando datasets de Kaggle automáticamente
date: 2020-03-11 10:00:00
tags: [blog, python, kaggle]
class: post-template
subclass: 'post tag-blog'
author: ioexception
---

Sé que Tacos de Datos está enfocado principalmente al resultado visual, pero creo que es importante tratar de resaltar qué es lo que hay detrás de. Quisiera inaugurar esta sección que llamaré *"Detrás de la visualización"* para cubrir temas de extracción, procesamiento y almacenamiento de datos; en pocas palabras: ingeniería de datos. *Y es que, además de preparar los tacos, también es bueno saber cómo conseguir los ingredientes.*

PD: Mira cuál es el resultado de lo que estoy explicando aquí: [Medium Daily Digests on Kaggle](https://www.kaggle.com/ioexception/medium-daily-digests).

## Fuente de los datos y su recolección   

Ya tenía tiempo queriendo hacer un dataset con información extraída de Medium.com. Al principio, pensé en hacer *web scraping* masivamente en las publicaciones de Medium; el problema es que no proporcionan un sitio web ni fácil de *crawlear* ni fácil de extraer información... luego pensé en ese "Medium Daily Digest", un *newsletter* que recibo todos los días, en él, Medium me envía historias que "me pueden interesar"; Recuerdo que cuando me suscribí, tuve que elegir mis temas preferidos, y desde entonces siempre recibo un correo con artículos sobre ellos.

Con eso en mente, me propuse crear 20 cuentas de correo electrónico diferentes (en realidad son alias para una sola cuenta) y luego usar cada una de ellas para suscribirse al mentado "Medium Daily Digest", cada cuenta asociada con cinco diferentes (pero un tanto relacionados) temas. Y desde entonces he estado recibiendo 20 correos electrónicos, todos los días con títulos, subtítulos y URLs de posts.

De vez en cuando inicio sesión en esas cuentas, abro algunos correos electrónicos para evitar que dichas cuentas se eliminen de la sindicación de Medium [porque esta gente sabe cuándo no lees sus correos electrónicos]((https://www.theverge.com/2019/7/3/20681508/tracking-pixel-email-spying-superhuman-web-beacon-open-tracking-read-receipts-location)). También es de esperarse que los temas que ofrecen puedan cambiar con el tiempo, y en algún momento los intereses asociados de algunas cuentas pueden cambiar, en ese caso tendré que iniciar sesión en Medium y modificar las cuentas  manualmente, pero en su mayor parte la recopilación de datos se ejecuta de manera automática. 

Por cierto, no estoy seguro de si el conjunto de datos será útil para alguien, pero espero que la forma en que lo construí con Python sea útil para cualquiera con datos más importantes.


## Leyendo los correos del servidor 

Como lo mencioné anteriormente, los correos electrónicos llegan a mi cuenta, y no es divertido tener que descargar 20 correos electrónicos manualmente todos los días, por lo tanto, por primera vez en este artículo: Python al rescate.

Para acceder a los correos electrónicos en mi servidor, uso el protocolo IMAP; Esto me permite leerlos sin tenerlos que borrar del servidor, en caso de que quiera acceder a ellos en el futuro. Para esto, a pesar de que Python ofrece el módulo `imaplib` para interactuar con dichos servidores, decidí usar el paquete `imapclient` que, en mi opinión, hace que el código sea un poco más limpio y comprensible. Leer todos los correos electrónicos de una cuenta es tan fácil como muestra este fragmento de código:

```python
import email
from imapclient import IMAPClient

with IMAPClient(host=imap_server, use_uid=True) as client:  
    client.login(account, password)  
    messages = client.search(["NOT", "DELETED"])  
    for message_id in messages:  
        fetched = client.fetch(message_id, "RFC822")  
        data = fetched[message_id]  
        email_message = email.message_from_bytes(data[b"RFC822"])
        yield message_id, email_message
```

Como puede ver, usamos `IMAPClient` como *context manager*, luego iniciamos sesión con nuestras credenciales de correo electrónico (sí, es una pena que tengamos que usar nuestra contraseña directamente); después de iniciar sesión, buscamos todos los correos electrónicos que no hemos eliminado (`NOT` `DELETED`), esto devolverá una lista de cadenas que contienen los identificadores para cada mensaje; podemos iterar sobre estos identificadores y usar el cliente ejecutándo `fetch` para obtener cada mensaje, especificando que queremos la propiedad `RFC822` del correo electrónico. 

El valor de retorno de `fetch` será un diccionario de la forma `{ message_id: { b"RFC822": (bytes) } }`, podemos transformar fácilmente estos bytes en una representación de correo electrónico más pytónica mediante el uso de la función `message_from_bytes`. Puedes ver mi implementación real en la función [`read_from_mail`](https://github.com/fferegrino/medium-collector/blob/v0.0.0/medium_collector/download/reader.py#L6) en mi repositorio.

## Extrayendo datos del correo electrónico  

Teniendo un correo electrónico con el que sea fácil trabajar en Python, me referiré una publicación anterior sobre [cómo leer trabajar con correos electrónicos con Python](https://dev.to/fferegrino/reading-emails-with-python-4o72). Una vez que podemos *"leer"* el mensaje, podemos acceder a propiedades como `To`, `From`, `Subject` y `Date`, así como acceder al contenido real del correo electrónico. Para referencia, mira este fragmento de código donde `message` es lo que obtenemos al llamar a `message_from_bytes` en el fragmento de código anterior:

```python
import datetime
import quopri

parts = {part.get_content_type(): part for part in message.get_payload()}  
html = quopri.decodestring(parts["text/html"].get_payload()).decode("utf8")   
mail_info = {  
    "id": message["Message-ID"],  
    "to": message["To"],  
    "from": message["From"],  
    "date": datetime.datetime.strptime(  
        message.get("Date"), "%a, %d %b %Y %H:%M:%S +0000 (%Z)"  
    ),  
}
```

El resultado final de este fragmento es producir un diccionario (`mail_info`) que contenga información básica del correo electrónico y la versión html del correo electrónico (`html`). Puedes ver mi implementación real en la función [`parse_mail`](https://github.com/fferegrino/medium-collector/blob/v0.0.0/medium_collector/download/parser.py#L34).

Para trabajar con el HTML del correo electrónico utilicé BeautifulSoup, no entraré en detalles, pero como siempre en *web scraping*, fue un proceso de prueba y error obtener la estructura adecuada para extraer información del contenido del correo. Puedes ver la función de extracción completa [aquí](https://github.com/fferegrino/medium-collector/blob/master/medium_collector/download/parser.py#L50), todo se reduce a una lista de diccionarios (uno para cada publicación en el correo electrónico) con la siguiente información: *section_title*, *post_title*, *post_subtitle*, *post_url*, *author_name*, *author_handle*, *site_name*, *site_slug*, *members_only*:

![Extraction description](https://i.imgur.com/jmkVSgz.png)  

<small>(en restrospectiva, pude haber extraído el tiempo de lectura... tal vez para la siguiente versión)</small>.

## Guardando la información  

Después de descargar la información, esta es almacenada en dos archivos csv:

 - *mails.csv*, que es un archivo de todos los correos electrónicos recibidos, este contiene:
	 - **id**: un identificador único del correo electrónico
	 - **date**: la fecha y hora en que se recibió el correo electrónico
	 - **to**: un hash de la cuenta de correo electrónico a la que se envió este correo electrónico
	 - **from**: la cuenta de correo electrónico utilizada para enviar el correo electrónico (siempre es la misma)
	 - **subject**:  el asunto del correo electrónico
 - *articles_mails.csv*, contiene la información extraída de cada correo electrónico, asociada al correo electrónico del que provienen:  
	 - **mail_id**: un identificador único del correo electrónico del que proviene este artículo, corresponde a **id** en el archivo `mails.csv`  
	 - **post_url**: la url del artículo
	 - **post_title**: el título del artículo  
	 - **post_subtitle**: el subtítulo del artículo  
	 - **section_title**: el título de la sección en la que encontró la publicación  
	 - **members_only**: una bandera que especifica si el artículo es para miembros de Medium
	 - **author_name**: nombre del autor del artículo  
	 - **author_handle**: identificador del autor del artículo  
	 - **site_name**: si el artículo se publicó en un sitio, este contiene el nombre de dicho sitio  
	 - **site_slug**: si el artículo se publicó en un sitio, este contiene el identificador de dicho sitio  

Para escribir toda esta información en los archivos, utilicé el módulo `csv` siempre confiable, en particular, la clase `DictWriter` que permite el uso de diccionarios cuando se llama al método `writerow`:

```python
import csv

EMAILS_FILE_HEADERS = ["id", "date", "to", "from", "subject"]
emails = [{"id": 1, "date": datetime.now(), 
           "to": "cosme@fulanito.com", "from": "noreply@medium.com",
	   "subject": "Don't miss this!"}]

with open("mails.csv", "w") as writable:
	writer = csv.DictWriter(writable, fieldnames=EMAILS_FILE_HEADERS)
	writer.writeheader()
	for email in emails:
		writer.writerow(email)
```  

En realidad, lo que hago es un poco más complicado, ya que quiero agregar filas al conjunto de datos de forma incremental; por lo tanto, el archivo debe abrirse con `"a"` como modo de archivo, no con `"w"` y es necesario tener especial cuidado para no escribir los encabezados en el medio del archivo tampoco. Puedes verificar toda la implementación de las funciones de escritura en [este fragmento de código](https://github.com/fferegrino/medium-collector/blob/v0.0.0/medium_collector/download/writer.py).

## Cargando la información a S3   

Pero, ¿de qué sirven estos datos si van a vivir guardados en mi computadora? Para superar este problema, pensé en cargar los datos en un *bucket* de S3, para que estén disponibles para descargarlo en cualquier lugar que lo necesite. Para acceder a cualquier recurso de AWS, mi herramienta favorita es el paquete `boto3` que es una verdadera navaja suiza para AWS.

Supongamos que desea cargar el archivo `medium_data/mails.csv` en el bucket `"medium_bucket"` y que se llame `"mails.csv"`, el siguiente código será suficiente:  

```python
import boto3

bucket = "my_super_cool_bucket"
client = boto3.client(  
	"s3",
	aws_access_key_id=config("ACCESS_KEY"),
	aws_secret_access_key=config("SECRET_KEY"),  
	region_name="eu-west-2",  
)

client.upload_file("medium_data/mails.csv", bucket, "mails.csv")
```

Pero no quiero seguir cargando el mismo archivo una y otra vez si no hay necesidad de hacerlo. Para lograr esto, es posible verificar si el archivo existe en nuestro *bucket* y resumir su contenido con un hash `md5` generado por el propio AWS. Entonces es solo una cuestión de comparar dicho hash con el hash del archivo local y si son iguales, no subo el archivo:

```python
import hashlib

def get_file_hash(file_path):   
	hash_md5 = hashlib.md5()  
	with open(file_path, "rb") as readable:  
		for chunk in iter(lambda: readable.read(4096), b""):  
			hash_md5.update(chunk)  
	return hash_md5.hexdigest()

head = client.head_object(Bucket=bucket, Key="mails.csv")
md5_signature = get_file_hash("medium_data/mails.csv")
if "ETag" in head and literal_eval(head["ETag"]) == md5_signature:
	# The file already exists, do nothing
else:
	client.upload_file("medium_data/mails.csv", bucket, "mails.csv")
```

El método al que se debe llamar para obtener información para un objeto es `head_object`. Por cierto, toda la lógica y la implementación están en [este archivo](https://github.com/fferegrino/medium-collector/blob/v0.0.0/medium_collector/s3.py) si quieres verlo.

*Algunas consideraciones*: para este ejemplo, consideré que el *depósito* ya existe, sin embargo, si no existe, también es posible usar *boto* para crearlo. Como dije, *boto* es un monstruo de biblioteca que debes considerar si trabajas con AWS. También estoy usando las credenciales directamente en la llamada a la creación del cliente para *s3*, pero hay varias formas de manejar la autenticación con *boto*, consulta la documentación para obtener más información.  

## Cargando los datos a Kaggle  

Hasta ahora, todo va muy bien, pero el conjunto de datos todavía está disponible para mí, y aunque podría hacer público mi *bucket* de s3, hay algunos problemas con esto:  

 - No quiero que mi factura de S3 suba por el cielo si alguien con intenciones maliciosas se apodera de mi s3  
 - Quiero que la gente use los datos, y con una url de s3 no es muy fácil de descubrir o promocionar el *dataset*   

Pero hay un buen lugar para almacenar conjuntos de datos, es gratuito y, al mismo tiempo, indexa los datos y los hace visibles al público en generar. Estoy hablando de Kaggle.

Kaggle hace que sea relativamente fácil interactuar con algunos de los recursos que ofrece el sitio web a través de su paquete [kaggle-api](https://github.com/Kaggle/kaggle-api). En general, promueven la interacción a través de la línea de comando, sin embargo, también es posible utilizar la API programática que alimenta el *cli*. Para cargar una nueva versión de mi conjunto de datos tuve que hacer algo como esto:

```python
from kaggle import api

def upload_to_kaggle(data_folder, message):
	api.dataset_create_version(data_folder, message, quiet=True)
	
upload_to_kaggle("medium_data", "A new version of this dataset")
```

Y eso es todo. Realmente, es así de fácil. Pero no tan rápido, es decir, si queremos actualizar un conjunto de datos, si estamos creando uno desde cero, primero debemos realizar tres cosas:

 - Crear una carpeta (por ejemplo, `"medium_data"`) donde solo existen los archivos correspondientes a nuestro conjunto de datos
 - Tener un archivo llamado `dataset-metadata.json` en esa carpeta con los metadatos para su conjunto de datos, el aspecto del archivo debe parecerse al siguiente (pero puedes revisar el de mi app [para un ejemplo más concreto](https://github.com/fferegrino/medium-collector/blob/v0.0.0/data/dataset-metadata.json)):

```
{
  "title": "My Awesome Dataset", 
  "id": "cosme_fulanito/my-awesome-dataset", 
  "licenses": [{"name": "CC0-1.0"}]
}
```
 - Ejecutar `kaggle datasets create -p medium_data` en la consola, o usar el método `dataset_create_new` programáticamente.

Realicé esas tareas manualmente, ya que no es como si anduviera creando *datasets* a cada rato, sino que simplemente los estoy tratando de actualizar, tu caso de uso puede ser diferente.  

*Algunas consideraciones*: al igual que AWS, Kaggle también ofrece algunas formas de autenticación con su servicio, utilicé el que más promocionan: un archivo kaggle.json en `~/.kaggle`, pero puede usar algo diferente.

## Pruebas    

Al igual que con cualquier pieza de software que se pondrá en producción y que se ejecutará sin supervisión (sin mencionar que mi factura de s3 depende de que esto funcione correctamente), es bueno tener algunas pruebas solo para asegurarse de que haga lo que programamos. Sin embargo, inicialmente tenía la intención de escribir sobre las pruebas en esta publicación, pero este post ya es suficientemente largo, por lo que publicaré cómo probé la aplicación en una publicación futura, así que asegúrate de seguirme si quieres aprender sobre pruebas, *patching* y *mocks* (incluyéndo pruebas con servicios de AWS).

### ¿Preguntas? ¿comentarios?

No olvides que me puedes dejar un comentario aquí o enviarme un tuit en [@io_exception](https://twitter.com/io_exception), con gusto te ayudo si las cosas no están claras o necesitas más explicación sobre el código. Recuerda que la aplicación completa está [disponible en GitHub](https://github.com/fferegrino/medium-collector/tree/v0.0.0) para que puedas jugar con el código.
