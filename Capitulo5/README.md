# Práctica 5. Añadir seguridad en CI/CD para el Microservicio Cliente

## 📝 Planteamiento de la práctica:
Para automatizar la construcción, las pruebas y la liberación del proyecto, es necesario crear un flujo de CI/CD usando GitHub Actions. Aunque este flujo puede incluir varias etapas, nos enfocaremos principalmente en las pruebas de seguridad del proyecto. 

## 🎯 Objetivos:
Al finalizar la práctica, serás capaz de:
- Configurar un repositorio en GitHub.
- Sincronizar tu microservicio cliente con el repositorio remoto.
- Crear un CI/CD con pruebas de seguridad para validar automaticamente el código del microservicio. 

## 🕒 Duración aproximada:
- 60 minutos.

## 🔍 Objetivo visual:
![diagrama](../images/5/diagrama.png)

> **IMPORTANTE:** Antes de comenzar este laboratorio necesitas tener una cuenta de **GitHub gratuita**, y una cuenta de **Docker hub**, en el caso de no tenerlas es necesario los siguientes enlaces **[Github Free account](https://github.com/)** y **[Docker hub Free account](https://hub.docker.com/)**

---

**[⬅️ Atrás](https://netec-mx.github.io/DEVSECOPS_PYT/Capitulo4/)** | **[🗂️ Lista general](https://netec-mx.github.io/DEVSECOPS_PYT/)** | **[Siguiente ➡️](https://netec-mx.github.io/DEVSECOPS_PYT/Capitulo6/)**

---

## Instrucciones:
Este laboratorio está dividido en las siguientes secciones:
- **[Configuración repositorio en GitHub](#configuración-repositorio-en-github-return)**

- **[Configuración Microservicio Cliente](#configuración-microservicio-cliente-return)**

- **[Crear GitHub Action](#crear-github-action-return)**

- **[Validar GitHub Action](#validar-github-action-return)**

## Configuración repositorio en GitHub [return](#instrucciones)

Paso 1. Crear un nuevo repositorio en GitHub con los siguientes datos: 
- **repository name:** repomicroservice

- **Public:** checked

- **Las demás opciones por default**

![alt text](../images/5/1.png)

Paso 2.  Al crear el repositorio nos mostrará algunos comandos importantes, vamos a copiar el comando que comience con **git remote add origin url_repositorio** y activamos **HTTPS**

![alt text](../images/5/2.png)

Paso 3. Fin configuración repositorio remoto. 

## Configuración Microservicio cliente [return](#instrucciones)

> **IMPORTANTE:** Para esta sección es fundamental tener instalado **git** en el caso de no tenerlo, descargarlo de la siguiente ruta **[git download](https://git-scm.com/downloads)**

Paso 1. Abrir el microservicio cliente en **Visual Studio Code** 

Paso 2. Modificar el archivo **secrets.py** que se encuentra en la carpeta **database** y comentar la carga de las variables de ambiente:

**secrets.py**
```python
import os
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

#delete in production
#from dotenv import load_dotenv
#load_dotenv()


key_vault_uri=os.getenv("KEYVAULT_URI","https://keyvaultenvf.vault.azure.net/")

#auth azure
credential=DefaultAzureCredential()

#create keyvault client
client=SecretClient(vault_url=key_vault_uri, credential=credential)

def get_secret(name_secret:str)->str:
    try:
        return client.get_secret(name=name_secret).value
    except Exception as ex:
        print(f"error get secret {ex}")
```

Paso 3. Crear un archivo que llamaremos **.dockerignore** en la raíz del proyecto, donde agregaremos lo siguiente:

**.dockerignore**
```properties
.sonarlint
.vscode
.env
Dockerfile
__pycache__/
*.pyc
cicd.yaml
```

Paso 4. Crearemos un archivo que llamaremos **Dockerfile** con el siguiente contenido: 

```Dockerfile
# Dockerfile
FROM python:3.13-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt \
    && pip install gunicorn
COPY . .
EXPOSE 8082
CMD ["gunicorn", "--bind", "0.0.0.0:8082", "app:app"]
```

Paso 5. Ahora agregaremos un nuevo archivo en la raíz del proyecto con el nombre **.gitignore** donde agregaremos el siguiente contenido: 

**.gitignore**
```properties
.env
.vscode
.sonarlint
__pycache__/
*.pyc
```

Paso 6. Abrimos una terminal en la carpeta donde se encuentra nuestro proyecto y ejecutamos los siguientes comandos:

```bash
git init
```

```bash
git add .
```

```bash
git commit -m "clase"
```

```bash
git remote add origin <URI_repository_git>
```

```bash
git push --set-upstream origin master
```

> **IMPORTANTE:** Este último comando nos pedirá iniciar sesión en nuestra cuenta de GitHub

Paso 7. Al visitar nuestro repositorio en GitHub podremos observar que nuestro código de python ya se encuentra cargado en **GitHub**

![alt text](../images/5/3.png)

## Crear GitHub Action [return](#instrucciones)

Paso 1. En el repositorio de **GitHub** donde se encuentra tu **Microservicio cliente**, ve a la sección **Actions**.

![alt text](../images/5/9.png)

Paso 2. Selecciona la opción **set up a workflow yourself**.

![alt text](../images/5/10.png)

Paso 3. Añade el siguiente flujo YAML para configurar el CI/CD.

```yaml
name: CI/CD Microservice Python

on:
  push:
    branches:
      - master

jobs:
  build-test-scan:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repo
        uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.13'

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt


      - name: Safety security scan
        run: |
          pip install safety
          safety check --full-report

      - name: Snyk vulnerability scan
        uses: snyk/actions/python@master
        with:
          command: monitor
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}

      - name: Build Docker image
        run: docker build -t ${{ secrets.DOCKER_USERNAME }}/devsecopspy:${{ github.sha }} .
        working-directory: .

      - name: Log in to Docker Hub
        uses: docker/login-action@v3.3.0
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Push Docker image to Docker Hub
        run: |
          docker tag ${{ secrets.DOCKER_USERNAME }}/devsecopspy:${{ github.sha }} ${{ secrets.DOCKER_USERNAME }}/devsecopspy:microserviceclient
          docker push ${{ secrets.DOCKER_USERNAME }}/devsecopspy:microserviceclient

```

Paso 4. Realizar el **commit** de los cambios.
Paso 5. Agrega los siguientes **Secretos** en el repositorio.

![alt text](../images/5/11.png)

## Validar GitHub Action [return](#instrucciones)

Paso 1. Dirígete a la sección de **Actions** en tu repositorio.

Paso 2. Verifica que tu **action** se haya ejecutado correctamente.

![alt text](../images/5/12.png)

Paso 3. Abre tu cuenta de **Docker Hub** y valida que se haya creado el repositorio con tu imagen, lista para ser usada en un despliegue. 

![alt text](../images/5/13.png)

## Resultado esperado: [Instrucciones](#instrucciones)

Al finalizar la ejecución del flujo, se espera que puedas ver la imagen generada en tu repositorio de **GitHub**

![alt text](../images/5/13.png)

---

**[⬅️ Atrás](https://netec-mx.github.io/DEVSECOPS_PYT/Capitulo4/)** | **[🗂️ Lista general](https://netec-mx.github.io/DEVSECOPS_PYT/)** | **[Siguiente ➡️](https://netec-mx.github.io/DEVSECOPS_PYT/Capitulo6/)**

---
