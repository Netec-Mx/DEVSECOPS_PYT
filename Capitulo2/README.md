# Práctica 2. Análisis de seguridad y códificación de un API Rest Python

## 📝 Descripción de la práctica:

En este laboratorio se espera que el alumno pueda códificar un API REST con Python usando las buenas prácticas de seguridad y pueda realizar análisis de código para buscar vulnerabilidades de código usando snyk y sonarqube.

## 🎯 Objetivos:
Al finalizar la práctica, serás capaz de:
- Desarrollar una API RESTful aplicando buenas prácticas en el diseño de código con Python 3.13. 
- Analizar tu código con Snyk para identificar vulnerabilidades.
- Evaluar la calidad y seguridad de tu código con SonarQube.

## 🕒 Duración aproximada:
- 60 minutos.

## 🔍 Objetivo visual:
![diagrama](../images/2/diagrama.png)

---

**[⬅️ Atrás](https://netec-mx.github.io/DEVSECOPS_PYT/Capitulo1/)** | **[🗂️ Lista general](https://netec-mx.github.io/DEVSECOPS_PYT/)** | **[Siguiente ➡️](https://netec-mx.github.io/DEVSECOPS_PYT/Capitulo3/)**

---

## Instrucciones:
> ⚠️ ***IMPORTANTE:** Antes de iniciar el laboratorio es necesario tener una cuenta de **gmail** y una cuenta **github** para el registro de las herramientas.*

Este laboratorio está dividido en las siguientes secciones:

- **[Códificar Microservicio Client](#códifica-microservicio-client-return)**
- **[Análisis de seguridad de código con SNYK](#análisis-de-seguridad-de-código-snyk-return)**
- **[Análisis de seguridad de código con SONARQUBE](#análisis-de-seguridad-de-código-con-sonarqube-return)**

### Códifica Microservicio Client [return](#instrucciones)
Paso 1. Crea una carpeta en el escritorio, a la cual debes asignarle el nombre de **microserviceclient**.

Paso 2. Abre la carpeta en **Visual Studio Code**.

Paso 3. Crea los siguientes elementos:

![alt text](../images/2/1.png)

Paso 3.1. Añade el siguiente contenido en el archivo **requirements.txt**.

```properties
flask==3.1.0
sqlalchemy==2.0.41
mysql-connector-python==9.2.0
```

Paso 4. En el paquete **entities**, añade dos archivos:

1. **_\_init__.py**: Este archivo sólo se agrega para que Python reconozca la carpeta **entities** como un paquete.
2. **model.py**: Se define el modelo que usarás para tu microservicios.

#### **model.py**

```python
from sqlalchemy import Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base

Base=declarative_base()

class Client(Base):
    __tablename__="clients"
    id= Column(Integer, primary_key=True, autoincrement=True)
    name=Column(String(50))
    email=Column(String(150))
    address=Column(String(200))
```

Paso 5. En la carpeta **database**, añade los siguientes archivos:

- **_\_init__.py**: Necesario para indicar que una carpeta es un paquete.
- **configuration.py**: En este, se definen las variables de ambientes que debes usar en el proyecto. 
- **db_session.py**: En este archivo se definen los objetos que te ayudarán a crear la sesión local de base de datos.

#### **configuration.py**
```python
import os

name_db=os.getenv("NAME_DB","appdb")
user_db=os.getenv("USER_DB","root")
password_db=os.getenv("PASSWORD_DB","1234")
ip_db=os.getenv("IP_DB","localhost")
port_db=os.getenv("PORT_DB","3306")

class Config:
    SQLALCHEMY_DATABASE_URI=f"mysql+mysqlconnector://{user_db}:{password_db}@{ip_db}:{port_db}/{name_db}"
    SQLALCHEMY_TRACK_MODIFICATIONS=False
```

#### **db_session.py**
```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from .configuration import Config

engine = create_engine(Config.SQLALCHEMY_DATABASE_URI)
SessionLocal = sessionmaker(bind=engine)
```

Paso 6. Posteriormente, dentro de la carpeta **dao** añade los siguientes archivos:

- **_\_init__.py**: Necesario para indicar que la carpeta es un paquete.
- **use_cases.py**: En este archivo se indican las operaciones del CRUD que se realizarán a la base de datos. 

#### **use_cases.py**
```python
from entities.model import Client
from database.db_session import SessionLocal

def insert_client(client:Client)->bool:
    with SessionLocal() as session:
        if client:
            session.add(client)
            session.commit()
            return True

def select_all()->list:
    with SessionLocal() as session:
        all=session.query(Client).all()
        return all

def update_client(client:Client)->bool:
    with SessionLocal() as session:
        result=session.query(Client).filter(Client.id == client.id).update({
            Client.name:client.name,
            Client.address:client.address,
            Client.email:client.email
        })
        session.commit()
        return result>0


def delete_client(id:int)->bool:
    with SessionLocal() as session:
        result = session.query(Client).filter(Client.id == id).delete()
        session.commit()
        return result>0
```

Paso 7. Luego, en la carpeta **controller**, añade los siguientes archivos: 

- **_\_init__.py**: Necesario para indicar que la carpeta es un paquete.
- **routes.py**: En este archivo, se indica las rutas que tendrá tu microservicio empleando HTTP protocol. 

#### **routes.py**
```python
from flask import Blueprint, request, jsonify
from dao import use_cases
from entities.model import Client

client_bp=Blueprint("client_bp", __name__)
request_mapping="/client"

@client_bp.route(request_mapping, methods=["POST"])
def insert():
    data= request.get_json()
    if not data:
        return {"message":"bad request"},400
    client=Client(**data)
    if use_cases.insert_client(client):
        return {"message":"inserted"},201
    else:
        return {"message":"server error"}, 500


@client_bp.route(request_mapping, methods=["GET"])
def select_all():
    data=use_cases.select_all()
    if data:
        json=[{"id":tmp.id, "name":tmp.name, "email":tmp.email, "address":tmp.address} for tmp in data]
        return jsonify(json), 200
    else:
        return {"message":"internal error"}, 500

@client_bp.route(request_mapping, methods=["PUT"])
def update():
    data=request.get_json()
    if data and data["id"]:
        client=Client(**data)
        if use_cases.update_client(client):
            return {"message":"updated"}, 200
    else:
        return {"message":"bad request"}, 400
    return {"message":"internal error"}, 500
    

@client_bp.route(request_mapping, methods=["DELETE"])
def delete():
    id=request.args["id"]
    if id:
        if use_cases.delete_client(id):
            return {"message":"deleted"}, 200
    else:
        return {"massage":"bad request"}, 400
    return {"message":"server error"}, 500
```


Paso 8. Añade el siguiente contenido en el archivo **app.py**.

#### **app.py**
```python
from flask import Flask
from database.db_session import engine
from entities.model import Base
from controller.routes import client_bp


app= Flask(__name__)

#create tables
Base.metadata.create_all(engine)

# registry routes
app.register_blueprint(client_bp)

if __name__=="__main__":
    app.run(debug=True, port=8082)
```


### Análisis de seguridad de código SNYK [return](#instrucciones)

Paso 1. Abre el siguiente enlace **[https://app.snyk.io/login](https://app.snyk.io/login)**, e iniciar sesión utilizando tu cuenta de **Google**.

![alt text](../images/2/3.png)

Paso 2. Después, instala en **Visual Studio Code** la extensión  **Snyk Security**.

![alt text](../images/2/4.png)

Paso 3. En las opciones de **SNYK**, usar la opción **Enable Synk Code and start analyzing**.

![alt text](../images/2/5.png)

> ⚠️ ***IMPORTANTE:** Al sincronizar tu proyecto a **snyk**, te solicitará iniciar sesión con la cuenta que creaste en los puntos anteriores.*

Paso 4. En la plataforma **Snyk**, activa el análisis de código. 

![alt text](../images/2/6.png)

Paso 5. Ahora, reescanea el proyecto completo y esperar el análisis de seguridad.

![alt text](../images/2/7.png)

Paso 6. Analiza las recomendaciones de seguridad que te entrega la herramienta.

### Análisis de seguridad de código con SONARQUBE [return](#instrucciones)

Paso 1. Abre el siguiente enlace: [https://sonarcloud.io/login](https://sonarcloud.io/login) e inicia sesión usando tu cuenta de **GitHub**.

![alt text](../images/2/8.png)

Paso 2. Al iniciar tu cuenta de **Sonar Cloud**, añade un nuevo proyecto. 

![alt text](../images/2/9.png)

Paso 3. Selecciona la opción **Create a project manually**.

![alt text](../images/2/10.png)

Paso 4. Crea una nueva organización bajo el nombre **netec**.

Paso 5. Añade un proyecto **Analyze projects** con las siguientes opciones:

![alt text](../images/2/11.png)

Paso 6. Set up project con las siguientes opciones:

![alt text](../images/2/12.png)

Paso 7. En **Visual Studio Code**, instala la extensión **SonarQube for IDE**.

![alt text](../images/2/13.png)

Paso 8. En las opciones de la extensión, configura **Add SonarQube Cloud Connection**.

Paso 9. Pulsa **Generate token**, en esta opción te solicitará iniciar sesión con tu cuenta de **SonarQube**. Finalmente, selecciona la **Organización**.

![alt text](../images/2/14.png)

Paso 10. Ahora debemos enlazar el proyecto creado en **sonarcloud** con el proyecto que tenemos con **visual studio code** 

![alt text](../images/2/15.png)

Paso 11. Espera un poco a que finalice de analizar todos los archivos de **Microservice Client**.

Paso 12. En **Security HOSTSPOSTS**, podrás observar el reporte de posibles problemas de seguridad:

![alt text](../images/2/16.png)

Paso 13. Si visitas cada archivo de tu proyecto, verás que **Sonarqube** realiza recomendaciones de código limpio y te permite mejorarlo. 

![alt text](../images/2/17.png)

Paso 14. Analiza los resultados

## Resultado esperado:

Al final de la práctica, podrás observar los resultados del análisis de dos herramientas orientadas a la seguridad del código y a su mejora continua. 

![alt text](../images/2/18.png)

---

**[⬅️ Atrás](https://netec-mx.github.io/DEVSECOPS_PYT/Capitulo1/)** | **[🗂️ Lista general](https://netec-mx.github.io/DEVSECOPS_PYT/)** | **[Siguiente ➡️](https://netec-mx.github.io/DEVSECOPS_PYT/Capitulo3/)**

---
