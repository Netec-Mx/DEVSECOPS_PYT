# Práctica 7. Configuración y uso de Checkov para el escaneo de una plantilla de Terraform 

## 📝 Planteamiento de la práctica:
En este laboratorio exploraremos la integración continua utilizando GitHub Actions y el almacenamiento de nuestra imagen Docker en Docker Hub.

## 🎯 Objetivos:
Al finalizar la práctica, serás capaz de:
- Instalar Checkov.
- Configurar una plantilla de Terraform para tu microservicio cliente.
- Buscar vulnerabilidades usando Checkov.

## 🕒 Duración aproximada:
- 40 minutos.

## 🔍 Objetivo visual:

![diagrama](../images/7/diagrama.png)

---

**[⬅️ Atrás](https://netec-mx.github.io/DEVSECOPS_PYT/Capitulo6/)** | **[🗂️ Lista general](https://netec-mx.github.io/DEVSECOPS_PYT/)** | **[Siguiente ➡️](https://netec-mx.github.io/DEVSECOPS_PYT/Capitulo8/)**

---

## Instrucciones:
Este laboratorio está dividido en las siguientes secciones:

- **[Instalación checkov](#instalación-checkov-return)**

- **[Crear plantilla terraform](#crear-plantilla-terraform-return)**

- **[Análisis de vulnerabilidades de terraform](#análisis-de-vulnerabilidades-de-terraform-return)**

## Instalación checkov [return](#instrucciones)
> ⚠️ ***IMPORTANTE:** Para realizar esta sección, es necesario tener instalado Python. Si aún no lo tienes, puedes descargarlo desde **[aquí](https://www.python.org/downloads/)**.*

Paso 1. Abre una terminal y ejecuta el siguiente comando: 

```bash
pip install checkov
```

![alt text](../images/7/1.png)

Paso 2. Valida la instalación de Checkov usando el siguiente comando: 

```bash
pip list
```

![alt text](../images/7/2.png)

## Crear plantilla terraform [return](#instrucciones)

Paso 1. Abre **Visual Studio Code**.
Paso 2. Crea una carpeta llamada **terraform**.
Paso 3. Dentro de la carpeta, crea un archivo llamado **main.tf**.
Paso 4. En el archivo **main.tf**, añade el siguiente contenido: 

```json
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.100"
    }
  }
}

provider "azurerm" {
  features {}
  tenant_id       = "<tenant id>"
  client_id       = "<client  id>"
  client_secret   = "<client secret>"
  subscription_id = "<subscription id>"
  
}


resource "azurerm_container_group" "container" {
  name                = "microserviceenvf"
  location            = "eastus"
  resource_group_name = "RGRepositorios"
  os_type             = "Linux"

  container {
    name   = "microservicio-cliente"
    image  = "docker.io/eddszombie/devsecopspy:microserviceclient"
    cpu    = 2
    memory = 2

    ports {
      port     = 8082
      protocol = "TCP"
    }

    environment_variables = {
        NAME_DB="<name db>"
        IP_DB="<ip db>"
        PORT_DB="<port db>"
        AZURE_TENANT_ID="<tenant id>"
        AZURE_CLIENT_ID="<client id>"
        AZURE_CLIENT_SECRET="<app secret>"
        KEYVAULT_URI="<vault uri>/"
    }
  }

  image_registry_credential {
  server   = "index.docker.io"
  username = "<docker user name>"
  password = "<docker password>"
}


  ip_address_type = "Public"
  dns_name_label  = "microservicio-cliente-vefed" 
}

```

El archivo de Terraform anterior realiza las siguientes acciones:

- Definición de proveedor requerido:
        - Se indica que se usará el proveedor de Azure (azurerm).

- Configuración del proveedor Azure:
        - Se establece la conexión a una suscripción de Azure, especificando su ID y habilitando las configuraciones necesarias.

- Creación de un grupo de contenedores:
        - Se configura el servicio Azure Container Instance, donde se desplegará uno o varios contenedores.

- Definición del contenedor:
        - Se especifica un contenedor que utilizará una imagen almacenada en Docker Hub, asignándole CPU, memoria y configuraciones de red.

- Exposición de un puerto:
        - Se habilita el acceso al contenedor a través del puerto 8082 utilizando el protocolo TCP.

- Asignación de variables de entorno:
        - Se definen variables internas que el contenedor usará para su configuración, como el perfil de ejecución, la base de datos y las credenciales.

- Configuración de credenciales para el registro de imágenes:
        - Se proporcionan un usuario y una contraseña para que Azure pueda descargar la imagen del contenedor desde Docker Hub.

- Asignación de IP pública y DNS:
        - Se indica que el contenedor contará con una dirección IP pública accesible desde internet, y se define un nombre DNS para facilitar su acceso.

## Análisis de vulnerabilidades de terraform [return](#instrucciones)
Paso 1. Abre una terminal en la ruta donde se encuentra el archivo de Terraform, ya que ahí se realizará el escaneo de vulnerabilidades. 

Paso 2. Ejecuta el siguiente comando:

```bash
checkov -d .
```
![alt](../images/7/3.png)


Paso 3. Analiza todas las recomendaciones de seguridad que te proporciona Checkov. 

Paso 4. Para proteger las variables de entorno y solucionar la mayoría de los errores, crea un archivo llamado **variables.tf** y añade el siguiente contenido:

```json
variable "client_id_app"{
    description = "id cliente app registry"
    type = string
    sensitive = true
}

variable "tenant_id_app" {
  description = "tenant id Azure"
  type = string
  sensitive = true
}

variable "app_secret_app"{
    description = "secret app registry"
    type = string
    sensitive = true
}


variable "client_id"{
    description = "id cliente terraform app"
    type = string
    sensitive = true
}

variable "tenant_id" {
  description = "tenant id terraform app"
  type = string
  sensitive = true
}

variable "app_secret"{
    description = "terraform app"
    type = string
    sensitive = true
}

variable "dockerhub_username" {
  description = "username docker"
  type = string
  sensitive = false
}

variable "dockerhub_password" {
  description = "docker password"
  type = string
  sensitive = true
}

```

Paso 5. Ahora crea un archivo llamado **terraform.tfvars** y añade el siguiente contenido:

```json
client_id="<client id terraform app>"
app_secret="<app secret terraform app>"
tenant_id="<tenant id terraform app>"
client_id_app="<client id app python>"
app_secret_app="<app secret app python>"
tenant_id_app="<tenant id app python>"
dockerhub_username="<docker hub username>"
dockerhub_password="<docker hub password>"
```


Paso 6. Escanea nuevamente tu plantilla de Terraform con el siguiente comando:

```bash
checkov -d . 
```

Paso 7. Al realizar el nuevo escaneo, el error relacionado con el **hardcoding** de datos en la plantilla debería desaparecer. 

> 💡 ***Nota:** Es posible que aparezcan otras sugerencias para mejorar la seguridad de tu infraestructura como código.*

## Resultado esperado: [Instrucciones](#instrucciones)

Al final, observarás una pantalla con unas sugerencias como la siguiente imagen:

![alt text](../images/7/4.png)

---

**[⬅️ Atrás](https://netec-mx.github.io/DEVSECOPS_PYT/Capitulo6/)** | **[🗂️ Lista general](https://netec-mx.github.io/DEVSECOPS_PYT/)** | **[Siguiente ➡️](https://netec-mx.github.io/DEVSECOPS_PYT/Capitulo8/)**

---
