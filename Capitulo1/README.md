# Práctica 1. Modelado de amenazas con Microsoft Threat Modeling Tool

## 📝 Descripción de la práctica:

En este laboratorio, vas a implementar un *modelo de amenazas para una aplicación orientada a microservicios*. Durante el proceso, observarás las posibles amenazas y explorarás estrategias para mitigarlas de forma efectiva. 

## 🎯 Objetivos:
Al finalizar la práctica, serás capaz de:
- Comprender la solicitud de la aplicación.
- Utilizar **Microsoft Threat Modeling Tool** para identificar posibles amenazas en la arquitectura. 
- Elaborar un informe sobre las posibles amenazas detectadas. 

## 🔍 Objetivo visual:

En esta práctica, se espera que analices la estructura de la aplicación que se presenta a continuación: 

![diagrama](../images/1/diagrama.png)

---

**[Lista general 🗂️](https://netec-mx.github.io/DEVSECOPS_PYT/)** | **[Siguiente ➡️](https://netec-mx.github.io/DEVSECOPS_PYT/Capitulo2/)**

---
## Instrucciones:

Antes de comenzar, lee con atención la siguiente información sobre la aplicación propuesta en el diagrama anterior. 

> ⚠️ ***IMPORTANTE:** Muestra cómo interactúan los componentes de una arquitectura de microservicios, con especial atención al flujo de autenticación. Los elementos clave son: el **Servidor de Descubrimiento**, un **Cliente de Microservicio**, una base de datos **MySQL**, un **Gateway** y un **Proveedor de Identidad**.*
*El flujo comienza cuando el cliente solicita un token al Proveedor de Identidad, que responde con el token, permitiendo al cliente interactuar con los microservicios a través del Gateway.*

Una vez que comprendas cómo funciona la aplicación, deberás realizar el modelado para identificar las posibles amenazas representadas en el diagrama anterior. 

Paso 1. Para iniciar, abre **Microsoft Threat Modeling Tool**.

![alt text](../images/1/1.png)

Paso 2. Selecciona **Create A Model**.

![alt text](../images/1/2.png)

Paso 3. Con esto, se abrirá un área de trabajo con todas las herramientas necesarias para crear un modelo. 

![alt text](../images/1/3.png)

Paso 4. Define el siguiente diagrama con los elementos que se muestran a continuación. 

![alt text](../images/1/4.png)

Paso 5. Los elementos utilizados para el diagrama son los siguientes: 

- **Web Application**
- **Identity Server**
- **Browser**
- **Generic Trust Line Boundary**
- **Database**
- **Request**
- **Response**

Paso 6. Posteriormente, guarda el modelo con el nombre **microservices-model**.

Paso 7. Luego, genera el reporte; para ello, da clic en: **Reports ➡️ Create Full Report**.

![alt text](../images/1/5.png)

Paso 8. Guarda el reporte en el escritorio con el nombre **reporte**.

Paso 9. Después, analiza el reporte generado.

![alt text](../images/1/6.png)

Paso 10. Realiza una lista que identifique en qué casos del ciclo de vida del software se implementa cada vulnerabilidad. 

- **Requirements (Requisitos):**

    - Se identifican los requisitos de seguridad.
    - Se determina el nivel de riesgo aceptable.

- **Design (Diseño):**

    - Aquí se realiza el modelado de amenazas.
    - Se identifican amenazas, mitigaciones y se documentan.

- **Implementation (Implementación):**

    - Se codifican las mitigaciones.
    - Se aplican prácticas seguras de desarrollo (como: validación de entradas, cifrado, entre otras).

- **Verification (Verificación / Pruebas):**

    - Pruebas de seguridad: análisis estático, dinámico, pruebas de penetración, etc.

- **Release (Liberación):**

    - Revisión final de seguridad.
    - Se valida que todos los riesgos fueron tratados adecuadamente.

- **Response (Respuesta / Mantenimiento):**

    - Planes de respuesta a incidentes y correcciones post-lanzamiento.

## Resultado esperado:

![alt text](../images/1/7.png)

---

**[Lista general 🗂️](https://netec-mx.github.io/DEVSECOPS_PYT/)** | **[Siguiente ➡️](https://netec-mx.github.io/DEVSECOPS_PYT/Capitulo2/)**

---
