---
layout: post
current: post
cover:  assets/detrasdelavis/002.png
navigation: True
title: Probando nuestro código con pytest
date: 2020-05-12 10:00:00
tags: [blog, python, pytest]
class: post-template
subclass: 'post tag-blog'
author: io_exception
---

#### Aviso  
A lo largo de este post estaré probando las funciones del código que escribí para el post sobre la [generación automática de datasets](https://tacosdedatos.com/generacion-automatica-datasets); sin embargo no es necesario que leas ese post primero, pero seguramente te ayudará a ponerle más contexto al código que aquí se presenta.

## ¿Qué es *pytest*?  

*pytest* es un *framework* para Python que ofrece la recolección automática de los *tests*, aserciones simples, soporte para *fixtures*, *debugeo* y mucho más... no te preocupes si algunas de estas palabras no te hacen mucho sentido; intentaré aclararlos más adelante a lo largo de este post.

Por cierto, *pytest* no es el único *framework* disponible; también está *nose*, *doctest*, *testify*... pero *pytest* es el que uso y de el que conozco más.

Para obtener *pytest* lo puedes descargar desde PyPI con tu gestor de paquetes de elección:

```shell
pip install pytest
```

## Escribiendo nuestros *tests*

Para escribir las pruebas es necesario escribir funciones que comiencen con el prefijo `test_`. Es necesario que las llamemos así ya que al momento de ejecutar *pytest* debemos especificar un directorio raíz, a partir de este directorio *pytest* leerá todos los archivos buscando funciones que comiencen con `test_`. Por ejemplo, si miras el [repositorio de *medium-collector*](https://github.com/fferegrino/medium-collector), verás que todos los *tests* están contenidos dentro de un folder apropiadamente llamado *tests*. Para ejecutar todas las pruebas, lo que tenemos que hacer es ejecutar *pytest* con esta carpeta como argumento:

```shell
pytest tests/
```

## Parametrizando nuestras pruebas  
Comencemos por escribir un test sencillo: una sola entrada, una sola salida. y sin llamadas a servicios externos. Me refiero a una función que toma una cadena codificada (como esta: `=?UTF-8?B?VGhlcmXigJlz?= more to the story`) y regresa otra cadena (como esta: `There’s more to the story`), en este caso estoy hablando sobre la función [`get_subject` method](https://github.com/fferegrino/medium-collector/blob/v0.0.0/medium_collector/download/parser.py#L12):  

```python
def get_subject(subject):
    subject_parts = []
    subjects = email.header.decode_header(subject)
    for content, encoding in subjects:
        try:
            subject_parts.append(content.decode(encoding or "utf8"))
        except:
            subject_parts.append(content)
    return "".join(subject_parts)
```  

Para escribir una prueba unitaria es tan "simple" como hacer esto:  

```python
def test_get_subject():
    expected = "There's more to the story"
    actual = get_subject("=?UTF-8?B?VGhlcmXigJlz?= more to the story")
    assert expected == actual
```

Sin embargo, esta funcion necesita ser probada con el caso en donde toda la cadena está codificada, o el caso en donde no lo está. Para cubrir estos casos tendríamos que escribir métodos como `test_get_subject_all_encoded` y `test_get_subject_none_encoded`, pero eso sería una duplicación absurda de código, para solucionar este problema de **probar el mismo código con múltiples valores de entrada** podemos hacer uso de la **parametrización** usando el decorador `@pytest.mark.parametrize`:

```python
import pytest

@pytest.mark.parametrize(
    ["input_subject", "expected"],
    [
        # Input 1
        (
            "=?UTF-8?B?V2hlbiBhICQxMDAsMDAwIFNhbGFyeSBJc27igJl0IEVub3VnaCB8IEFkYW0gUGFyc29ucyBpbiBNYWtpbmcgb2YgYSBNaWxsaW8=?= =?UTF-8?B?bmFpcmU=?=",
            "When a $100,000 Salary Isn’t Enough | Adam Parsons in Making of a Millionaire",
        ),
        # Input 2
        (
            "=?UTF-8?B?VGhlcmXigJlz?= more to the story", 
            "There’s more to the story"
        ),
        # Input 3
        (
            "7 Things Rich People Advise But Never Do | David O. in The Startup",
            "7 Things Rich People Advise But Never Do | David O. in The Startup",
        ),
    ],
)
def test_get_subject(input_subject, expected):
    actual = get_subject(input_subject)
    assert actual == expected
```

El código anterior le indica a *pytest* que ejecute la prueba `test_get_subject` tres veces, cada una reemplazando `input_subject` y `expected` con sus valores correspondientes especificados en el segundo argumento de `parametrize`. 

## Fixtures  
En algunas ocasiones tal vez tengamos **pruebas que comiencen desde cierto estado**, este estado puede ser tener datos en una base de datos, tener archivos en alguna carpeta, o tal vez simplemente tener el objeto correcto como entrada a la función; es ahí donde las *fixtures* son útiles.

Por ejemplo, en el repositorio de *medium-colletor* hay una función llamada `parse_mail` que, como el nombre lo sugiere, podemos usar para extraer información de un objeto de la clase `email.message.Message`. Esta es una versión simplificada de la implementación del método:  

```python
def parse_mail(email_message):
    html = get_html(email_message)
    mail_info = {
        "id": email_message["Message-ID"],
        "to": email_message["To"],
        "from": email_message["From"],
        "subject": get_subject(email_message["Subject"]),
        "date": email_message["Date"],
    }
    return mail_info, html
```  

Para probar esta función necesitamos un objeto de la clase `Message`, pero en realidad no quiero tener que conectarme a nuestro servidor de email cada vez que ejecutemos la prueba; este es el escenario perfecto para usar una *fixture*. Para definir una, tenemos que isar algo como el siguiente código:  

```python  
@pytest.fixture
def dummy_mail():
    msg = MIMEMultipart("alternative")
    msg["Subject"] = "Link"
    msg["From"] = "you@this.com"
    msg["To"] = "me@that.com"
    msg["Message-ID"] = "123"
    msg["Date"] = datetime.now().strftime("%a, %d %b %Y %H:%M:%S +0000 (UTC)")
    
    text = "Hi!"
    html = f"<html><head></head><body><p>{text}<br></body></html>"
    msg.attach(MIMEText(text, "plain"))
    msg.attach(MIMEText(html, "html"))

    return msg
``` 

Lo primero que hay que notar es que `@pytest.fixture` es usado como decorador de... ¿¡una función!? Sí, así es, una *fixture* no es nada más que una función cuyo valor de retorno debe ser el valor que queremos que esa *fixture* tenga. En este caso, el valor de nuestra *fixture* será un objeto de la clase `MIMEMultipart` que hereda de `Message` que es justo lo que queremos.  

Ahora, para usar nuestra *fixture* llamada `dummy_mail` en nuestra prueba es suficiente con pasarla como argumento en nuestra función de prueba:

```python
def test_parse_mail(dummy_mail):
    expected_mail_info = {
        "id": "ad841b37bd4b9b5403b575432f67f5ed2d68ed40",
        "to": "a4747a50dad63531704f5ab32509bb0c60b7350f",
        "from": "you@this.com",
        "subject": "Link",
        "date": ANY,
    }
    mail_info, decoded = parse_mail(dummy_mail)

    assert mail_info == expected_mail_info
    assert decoded == "<html><head></head><body><p>Hi!<br></body></html>"
```

Cuando ejecutamos *pytest*, este tratará de resolverlas antes de que se ejecute cualquier prueba que las use, y una ves que estas estén listas, los métodos de prueba reciben los valores especificados en cada método asociado. Este mecanismo permite algunos otros usos interesantes de los que hablaré más adelante.  

### Una característica extra de las *fixtures*  
Las *fixtures* de *pytest* son geniales, y otro de sus usos es cuando queremos reutilizar el mismo fragmento de código en dos o más funciones de prueba. Imagina que necesitamos usar un objeto de la clase `Message` para dos pruebas. Podríamos haber declarado una variable global, digamos `MESSAGE = MIMEMultipart("alternative")` y después usarla en nuestros métodos así:

```python
def test_parse_mail_1():
    parse_mail(MESSAGE)
    # ...
    
def test_parse_mail_2():
    parse_mail(MESSAGE)
    # ...
```

Pero en este caso, ambos tests estarían usando la misma variable, `MESSAGE` lo que significa que cualquier cambio hecho por `test_parse_mail_1` afectaría el `MESSAGE` que `test_parse_mail_2` recibe, esto rompe el propósito de las pruebas unitarias, ya que nuestros *tests* no estarían aislados. Sin embargo, cuando usamos *fixtures*, cada función de prueba recibe una copia nueva de lo que sea que regrese nuestra función marcada con `@pytest.fixture`, haciendo fácil y sencillo usarlas una y otra vez.


## Patching  
Sin lugar a dudas, algunas partes de nuestro código dependerán de librerías de terceros o a servicios externos que no queremos ejecutar o contactar cuando ejecutamos nuestras pruebas. Ya sea porque la librería que estamos usando consume muchos recursos o es un sistema productivo que no debería ser tocado durante las pruebas, aquí es cuando el **patching** brilla por su utilidad; este nos ayuda a **reemplazar el comportamiento (o valores de retorno) de una llamada a una función** con lo que nosotros dispongamos.

Imagina que la función `get_html` contiene código que es muy *"costoso"* ejecutar, y no lo queremos que este código se ejecute cada vez que llamamos al test `test_parse_mail`, entonces podemos *parcharlo* (tengo que decir que el *patching* no es una funcionalidad de *pytest* si no que viene con Python por default).

Hay dos formas de *"parchar"* nuestro código: una de ellas es haciendo uso de la instrucción `with`, pasando el nombre completo de la función que queremos *"parchar"*. Un test que aplica un *patch* a la función `get_html` dentro de `parse_email` se vería así:  

```python
from unittest.mock import patch

def test_parse_mail(dummy_mail):
    expected_mail_info = {
        "id": "ad841b37bd4b9b5403b575432f67f5ed2d68ed40",
        # ...
    }
    
    with patch("medium_collector.download.parser.get_html", 
        return_value="Hello") as patched:
        mail_info, decoded = parse_mail(dummy_mail)

    patched.assert_called_once()
    assert mail_info == expected_mail_info
    assert decoded == "Hello"
```  

En el fragmento anterior estamos *"parchando"* la función y estableciendo el valor de `"Hello"` como su valor de retorno con `return_value`. Esto significa que `"Hello"` será regresado cada vez que la función es ejecutada. Ahora que la función original no es realmente ejecutada, es importante que nos cercioremos que nuestro código está llamando a esta función, para esto podemos utilizar el método `assert_called_once` para verificar que lo hemos llamado.  

### Los peligros del *patching*  
El *parcheo* podría parecer una solución fácil para evitar conectarse con servicios externos o llamadas a funciones costosas. Pero debes tener en cuenta que cuando *parchamos* algo, estamos asumiendo muchas cosas sobre el código que estamos *parchando*, estas asunciones son:

 - Sabemos el comportamiento esperado del código que estamos *parchando*, es decir, sabemos sus valores de retorno y bajo que circunstancias falla.
 - Puedes, con completa seguridad, regresar un objeto que se comporte como el valor que originalmente sería retornado por la función real.

Cuando aplicas un parche a una función, toma en cuenta que esta puede retornar un valor "complejo" que sea difícil de reproducir, y que *"parcharlo"* mal resultaría en tu código siendo probado ante un escenario que nunca ocurrirá en la vida real. Para evitar esto, tal vez tengas que examinar muy a detalla cuales son los valores de retorno de lo que estás *"parchando"* con el fin de hacerlo correctamente.

Otro problema muy común con el *patching* es que en nos podemos dejar llevar y terminar *"parchando"* todo... lo que, a final de cuentas nos pone en la situación de estar probando nuestro código en escenarios poco realistas. Si en algún momento te encuentras haciendo esto, es mejor que te detengas y reconsideres si las pruebas unitarias son la mejor solución para probar tu código... tal vez las pruebas de integración sean una mejor solución para tu problema de *testing*.

## *Fixtures* avanzadas  
Como lo mencioné anteriormente, la forma en la que *pytest* resuelve las *fixtures* puede ser usada para darle a nuestro código más flexibilidad. En el repositorio de *medium-collector* hay una función que carga algunos archivos a un *bucket* de S3 usando la librería *boto*, esta es la función `upload_files`, que se ve más o menos así:  

```python
def upload_files(file, bucket):
    client = boto3.client(
        "s3",
        aws_access_key_id=config("ACCESS_KEY"),
        aws_secret_access_key=config("SECRET_KEY"),
        region_name="eu-west-2",
    )
    client.upload_file(str(file), bucket, file.name)2
```

Claro que no quero estar conectándome a AWS cada vez que ejecuto las pruebas; aquí es donde la librería *moto* aparece para rescatarme. En palabras de sus creadores: *"Moto es una librería que permite que tus tests fácilmente finjan comunicarse con servicios de AWS"*. La forma en la que ellos sugieren usarla es a través de un manejador de contexto:

```python
def test_my_model_save():
    with mock_s3():
        conn = boto3.resource('s3', region_name='us-east-1')
```

Para probar nuestra función debemos cumplir dos condiciones antes de llamar a `upload_files`:

 1. Imitar S3; no queremos estar conectándonos a AWS en nuestras pruebas unitarias,
 2. Tener un *bucket* que ya exista; nuestro código asume que uno ya existe

Para lograr estas dos cosas con un *fixture* podemos hacer algo como esto:

```python
@pytest.fixture
def bucket():
   return "my_special_bucket"

@pytest.fixture
def mock_storage(bucket):
    @contextmanager
    def inner(create_bucket=True):
        with mock_s3():
            conn = boto3.client("s3", region_name="eu-east-1")
            if create_bucket:
                conn.create_bucket(Bucket=bucket)
            yield
    return inner
```

Esta *fixture* es, en realidad, una función (`inner`) que gracias al decorador `contextmanager` podemos llamar con la instrucción `with`. En términos de la implementación puedes ver que estamos usando `mock_s3` como lo recomiendan los desarrolladores de *moto*, dentro de este contexto creamos un cliente de *boto3*, luego, dependiendo de un parámetro pasado a nuestra función `inner` decidimos si creamos o no la *bucket*; por último, y como este se trata de un manejador de contexto usamos la instrucción `yield` que le indicará a *pytest* que nuestra *fixture* está lista para ser usada.  

Ah, no sé si te diste cuenta, pero `mock_storage` toma como argumento otra *fixture* (`bucket` en este caso). Esa es otra de las excelentes características de *pytest*: permite crear ciertas dependencias entre nuestras *fixtures*, y esta es resuleta antes de que se ejecuten nuestras pruebas.

Ahora sí, estamos listos a probar nuestra función `upload_files` con esta función:

```python
def test_upload_files(bucket, mock_storage):
    with mock_storage(create_bucket=True):
        upload_files(files_path)
        client = boto3.client("s3", region_name="eu-east-1")
        contents = client.list_objects(Bucket=bucket)["Contents"]
    assert len(contents) == 1
```

## ¡Practica!
Me hubiera encantado preparar un notebook o cualquier otra forma de entorno interactivo que pudieras usar para jugar y experimentar un poco con los *tests*, pero creo que para este tema, es mejor que te ensucies las manos con un poco de código verdadero. Te invito a que descargues el código de la aplicación [*medium-collector* repo](https://github.com/fferegrino/medium-collector) y ejecutes los test por ti mism@.  

## Más allá de las pruebas unitarias  
A pesar de que *pytest* es fabuloso para realizar pruebas unitarias, nada nos detiene de usarlo para otras pruebas, aún más más complejas; hablo de pruebas de integración o tal vez hasta de *end-to-end*. Con herramientas como *Docker*, *localstack* y otras más, es posible crear un poderos *framework* de pruebas para todos tus proyectos de Python. En un post futuro voy a hablar de cómo es que se puede utilizar todo el poder de estas herramientas para crear un test de *end-to-end*, así que asegúrate de seguir el blog y de seguirme en Twitter en [@io_exception](https://twitter.com/io_exception).
