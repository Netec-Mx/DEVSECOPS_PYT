# 4. Gestión de Secretos con Azure Keyvault del Microservicio Cliente

Es necesario proteger los datos de la base de datos y no almacenarlos en el microservicios cómo método de seguridad, para realizar esto usaremos **Azure Key Vault** para la protección de la información. 


## Objetivos
- Configurar un registro de aplicación en el directorio activo de azure. 

- Crear y configurar un **Azure Key Vault**

- Conectar nuestro **Microservicio Cliente** a Azure Key Vault para obtener la información de la base de datos. 


---

<div style="width: 400px;">
        <table width="50%">
            <tr>
                <td style="text-align: center;">
                    <a href="../Capitulo3/"><img src="../images/anterior.png" width="40px"></a>
                    <br>anterior
                </td>
                <td style="text-align: center;">
                   <a href="../README.md">Lista Laboratorios</a>
                </td>
<td style="text-align: center;">
                    <a href="../Capitulo5/"><img src="../images/siguiente.png" width="40px"></a>
                    <br>siguiente
                </td>
            </tr>
        </table>
</div>

---

## Diagrama

![diagrama](../images/4/diagrama.png)

## Instrucciones
Este lab esta separado en las siguientes secciones:

- **[App Registration en Azure](#app-registration-en-azure-return)**
- **[Configuración Azure Key Vault](#configuración-azure-key-vaultreturn)**

- **[Configuración Microservicio Cliente](#configuración-microservicio-cliente-return)**


## App Registration en Azure [return](#instrucciones)
> **IMPORTANTE:** Para esta sección necesitaremos una cuenta de **Microsoft Azure** para la creación y configuración de los recursos, la cuenta debe de tener el rol de **Application Administrator** y los permisos para asignar nuevos roles a recursos de Azure. 

1. Abrir el portal de azure **https://portal.azure.com/**

2. Iniciar sesión con tu cuenta de Azure

3. En la barra de busqueda del portal de azure escribe **Microsoft Entra ID**

![alt text](../images/4/1.png)

4. En **Microsoft Entra ID** busca **App Registrations** en la sección de **Manage**

![alt text](../images/4/2.png)

5. Crearemos un nuevo registro de aplicación en **+ New registration** 

![alt text](../images/4/3.png)

6. Añadiremos los siguientes datos al registro de la aplicación. 

- **Name:** claseapp**tusiniciales**
- **Supported account types:**  Accounts in this organizational directory only

- **Redirect URI**: Web

![alt text](../images/4/4.png)

7. Al crear el registro de la aplicación en la pestaña de **Overview** vamos a obtener los siguientes datos (almacenar estos datos en un **notepad**): 

- **Application (client) ID**
- **Directory (tenant) ID**

![alt text](../images/4/5.png)

8. Generaremos un secreto navegando en **Manage->Certificates & Secrets**

![alt text](../images/4/6.png)

9. El secreto tendrá la siguiente configuración:

![alt text](../images/4/7.png)

10. Al generar el secreto nos generará un valor **si no copiamos este valor va a desaparecer y no lo podremos recuperar**

![alt text](../images/4/8.png)

11. Almacenar el secreto en el notepad donde tenemos el client id y tenant id. 

![alt text](../images/4/9.png)



## Configuración Azure Key Vault[return](#instrucciones)
1. Abrir el portal de azure **https://portal.azure.com**

2. En la barra de busqueda escribiremos **Resource Groups**

![alt text](../images/4/10.png)

3. Si no tenemos un grupo de recursos crearemos uno

4. En el grupo de recursos crearemos un nuevo recurso **+Create**

![alt text](../images/4/11.png)


5. Buscaremos **Key Vault**

![alt text](../images/4/12.png)

6. La configuración del key vault, usaremos la siguiente: 

- **Subscription:** *suscripción que puedas usar para crear recursos*

- **Resource Group:** *Grupo de recursos disponible (si no hay crear uno nuevo)*

- **Key vault name:** *keyvault**tusiniciales***

- **Region:** East US

- **Pricing tier:** Standard

- **todas las demás opciones se dejan por default pulsar review + create**

![alt text](../images/4/13.png)

7. Abrir el **Azure Key Vault** creado y abrir la opción **Access control IAM** (añadiremos 2 roles)
- **Rol 1: KeyVault Adminstrator**: Este rol se le agrega a nuestra cuenta de Azure, esto permite que podamos administrar la herramienta y agregar información.

- **Rol 2: Key Vault Secrets User**: Este rol se le agrega a nuestra aplicación registrada en azure y permite que nuestra app pueda obtener los secretos de **Azure Key Vault**. 


![alt text](../images/4/14.png)

8. En la opción de **Overview** de azure key vault vamos a copiar **Vault URI** y lo almacenaremos en nuestro notepad

![alt text](../images/4/15.png)

9. Ahora nos vamos a la sección de **Objects->Secrets**

![alt text](../images/4/16.png)

10. Generaremos 2 secretos 
- **db-user**: root
- **db-password**: 1234 

> **NOTA:** Estos valores cambiarán conforme cambie la base de datos. 

![alt text](../images/4/17.png)


11. Validamos que los 2 secretos se hayan creado. 

![alt text](../images/4/18.png)

12. Fin configuración **Azure Key Vault** antes de pasar a la siguiente sección asegurate que en tu notepad tengas la siguiente información:

![alt text](../images/4/19.png)


## Configuración Microservicio Cliente [return](#instrucciones)

1. En el archivo de requirements agregar: 
 
- **azure-identity==1.23.0**
- **azure-keyvault-secret==4.9.0**

![alt text](../images/4/20.png)

2. Instalar los paquetes usando el siguiente comando (asegurate de ejecutar el comando estando en la misma carpeta donde se encuentra el archivo **requirements.txt**): 


```bash
pip install -r requirements.txt
```


3. Instalar el paquete **python-dotenv** para usar variables de ambiente (**Este paquete sólo se usa en el ambiente de desarrollo NO ES NECESARIO AGREGARLO AL ARCHIVO requirements.txt**).

```bash
pip install python-dotenv
```

4. En la raíz de nuestro proyecto crearemos un archivo llamado **.env** donde añadiremos los datos que obtuvimos de Azure con variables de ambiente:

```properties
AZURE_TENANT_ID=<tenant_id>
AZURE_CLIENT_ID=<client_id>
AZURE_CLIENT_SECRET=<app_secret>
```


5. En la carpeta **database** crearemos un archivo que llamaremos **secrets.py** donde añadiremos el siguiente código:

**secrets.py**
```python
import os
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

#delete in production
from dotenv import load_dotenv
load_dotenv()


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

6. En el archivo **configuration.py** que se encuentra en la carpeta **database** lo módificamos con el siguiente código:

**configuration.py**
```python
import os
from .secrets import get_secret


name_db=os.getenv("NAME_DB","appdb")
user_db=get_secret("db-user")
password_db=get_secret("db-password")
ip_db=os.getenv("IP_DB","localhost")
port_db=os.getenv("PORT_DB","3306")

class Config:
    SQLALCHEMY_DATABASE_URI=f"mysql+mysqlconnector://{user_db}:{password_db}@{ip_db}:{port_db}/{name_db}"
    SQLALCHEMY_TRACK_MODIFICATIONS=False

```

7. Ejecutar la aplicación de python. 


## Resultado Esperado 
Si hemos llegado hasta aqui el alumno debería de poder ejecutar las operaciones del microservicio sin ningún problema. 

![alt text](../images/4/21.png)

