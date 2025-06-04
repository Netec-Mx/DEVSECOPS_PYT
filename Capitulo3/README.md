# 3. Analizar dependencias y librerias del Microservicio Cliente usando snyk y safety python.  

En este labatorio se espera que los alumnos puedan revisar la salud de las dependencias de su Microservicio usando **SNYK y safety para python**


## Objetivos
- Usar **SNYK** para escanear el proyecto y buscar vulnerabilidades en sus dependencias. 

- Usar **safety**
para buscar vulnerabilidades en las dependencias del archivo **requirements.txt** 

---

<div style="width: 400px;">
        <table width="50%">
            <tr>
                <td style="text-align: center;">
                    <a href="../Capitulo2/"><img src="../images/anterior.png" width="40px"></a>
                    <br>anterior
                </td>
                <td style="text-align: center;">
                   <a href="../README.md">Lista Laboratorios</a>
                </td>
<td style="text-align: center;">
                    <a href="../Capitulo4/"><img src="../images/siguiente.png" width="40px"></a>
                    <br>siguiente
                </td>
            </tr>
        </table>
</div>

---


## Diagrama

![diagrama](../images/3/diagrama.png)


## Instrucciones
Esta práctica se separa en las siguientes secciones:

- **[Análisis de dependencias usando SNYK](#análisis-de-dependencias-usando-snyk-return)**

- **[Análisis de dependencias usando safety dependency check](#análisis-de-dependencias-usando-owasp-dependency-check-return)**


## Análisis de dependencias usando SNYK [return](#instrucciones)
> **IMPORTANTE:** Se necesita tu cuenta de snyk del laboratorio **Análisis de seguridad y códificación de un API Rest Python Flask** en el caso que no se tenga, es necesario regresar al laboratorio anterior y seguir las instrucciones de cómo obtenerla. 

1. Abrir **Visual Studio Code**
2. Abrir el proyecto del **MicroservicioCliente** 
3. Validar que se tenga instalada la extensión **Snyk Security** 

![alt text](../images/3/1.png)

4. En la barra lateral de VSCode pulsa el icóno de **snyk**

![alt text](../images/3/2.png)

5. Realiza un nuevo escaneo, pero ahora tomaremos especial cuidado en la sección **Open Source Security**

![alt text](../images/3/3.png)


6. Si observamos existen 2 problemas de seguridad en 2 dependencias. Le damos click a cualquiera de las 2  para observar información acerca de este problema. 


![alt text](../images/3/4.png)


7. La herramienta nos alerta que la dependencia de **MySQL connector** tiene un problema 

8. Abrimos la página de snyk para poder observar más sobre este problema. 


![alt text](../images/3/5.png)

> **NOTA:** Este problema de seguridad significa que un atacante se podría conectar a nuestro sistema de archivos usando variaciones de rutas para tratar de obtener información sensible dentro de la aplicación. 

9. Ahora... ¿Debería asustarme esta situación?. La respuesta corta es no, siempre y cuando se tomen precauciones cómo:

- Ocultar la información sensible de nuestra aplicación. 

- Restringir el acceso en nuestra base de datos sólo a las ips necesarias. 

- Usar **VPN o SSH tunnel** para conectarse a recursos externos

- Tener nuestros recursos dentro de una red privada.

10. Por el momento nuestra aplicación no requiere de una modificación a nivel de dependencia. Pero si es importante tomar precauciones. 


## Análisis de dependencias usando safety dependency check [return](#instrucciones)

**safety** es un escáner de vulnerabilidades de dependencia diseñado para mejorar la seguridad de la cadena de suministro de software y permitir el uso seguro de paquetes de Python, desde el desarrollo hasta la implementación.

1. Abrir el proyecto del **Microservicio Cliente** 

2. Abrir una terminal de visual studio code. 

3. Instalar safety usando el siguiente comando:

```bash
pip install safety
```

4. Ahora realizar un escaneo de seguridad usando el comando:

```bash
safety scan
```

5. Al ejecutar el comando anterior necesitará registrarse a la herramienta de safety para tener acceso a la base de datos de vulnerabilidades:

![alt text](../images/3/6.png)

6. Al aceptar el registro nos abrirá una página web pidiendo un registro (*se recomienda usar tu cuenta de gmail*).

![alt text](../images/3/7.png)

7. Al finalizar el registro podemos realizar el escaneo de seguridad usando de nuevo el comando:

```bash
safety scan
```


![alt text](../images/3/8.png)

8. Si podemos observar en el análisis de seguridad nos indica que hay una vulnerabilidad en la dependencia de **flask**. 

![alt text](../images/3/9.png)


9. Si nos vamos a la página de **safety** podemos encontrar más información sobre esta vulnerabilidad. 

![alt text](../images/3/10.png)

10. Al ser una vulnerabilidad con un score de severidad de 7.5 es recomendable solucionarla. La documentación nos dice que usemos la versión 3.1.1. 

11. Abrir el archivo **requirements.txt** y modificar la versión de **flask** a la versión 3.1.1

![alt text](../images/3/11.png)

12. Realiza de nuevo un escaneo usando el comando:

```bash
safety scan
```

![alt text](../images/3/12.png)

> **NOTA:** Observamos que ya no muestra vulnerabilidades al cambiar la versión de flask.




## Resultado esperado [Instrucciones](#instrucciones)

Al finalizar el alumno tendrá 2 análisis de de vulnerabilidades de dependencias en python.

**snyk**
![snyk](../images/3/3.png)

**safety**
![safety](../images/3/8.png)

