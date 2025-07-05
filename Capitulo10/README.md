# Práctica 10. Monitoreo de Microservicio Cliente con Grafana y Prometheus
## 📝 Planteamiento de la práctica:
En este laboratorio se espera que puedas implementar una arquitectura de microservicios en Docker, incluyendo el monitoreo. 

## 🎯 Objetivos:
Al finalizar la práctica, serás capaz de:
- Usar Docker Compose para crear una arquitectura.
- Validar la configuración de Prometheus.
- Crear dashboard en Grafana.

## 🕒 Duración aproximada:
- 60 minutos.

## 🔍 Objetivo visual:
![diagrama](../images/10/diagrama.png)

---

**[⬅️ Atrás](https://netec-mx.github.io/DEVSECOPS_PYT/Capitulo9/)** | **[🗂️ Lista general](https://netec-mx.github.io/DEVSECOPS_PYT/)**

---

## Instrucciones:

Este laboratorio está dividido en las siguientes secciones:

- **[Configurar Grafana y Prometheus](#configurar-grafana-y-prometheus-return)**

- **[Crear Dashboard en Grafana](#crear-dashboard-en-grafana-return)**

## Configurar Grafana y Prometheus [return](#instrucciones)

Paso 1. Crea una carpeta llamada **grafana y prometheus** en el Escritorio.

Paso 2. Abre la carpeta con **Visual Studio Code**.

Paso 3. Agrega los siguientes archivos dentro de la carpeta:

![alt text](../images/10/1.png)

Paso 4. En el archivo **dashboard.json**, pega el siguiente contenido: 

```json
{
  "__inputs": [
    {
      "name": "DS_PROMETHEUS",
      "label": "prometheus",
      "description": "",
      "type": "datasource",
      "pluginId": "prometheus",
      "pluginName": "Prometheus"
    }
  ],
  "__elements": {},
  "__requires": [
    {
      "type": "panel",
      "id": "bargauge",
      "name": "Bar gauge",
      "version": ""
    },
    {
      "type": "grafana",
      "id": "grafana",
      "name": "Grafana",
      "version": "11.4.0"
    },
    {
      "type": "datasource",
      "id": "prometheus",
      "name": "Prometheus",
      "version": "1.0.0"
    },
    {
      "type": "panel",
      "id": "stat",
      "name": "Stat",
      "version": ""
    },
    {
      "type": "panel",
      "id": "timeseries",
      "name": "Time series",
      "version": ""
    }
  ],
  "annotations": {
    "list": [
      {
        "builtIn": 1,
        "datasource": {
          "type": "grafana",
          "uid": "-- Grafana --"
        },
        "enable": true,
        "hide": true,
        "iconColor": "rgba(0, 211, 255, 1)",
        "name": "Annotations & Alerts",
        "type": "dashboard"
      }
    ]
  },
  "editable": true,
  "fiscalYearStartMonth": 0,
  "graphTooltip": 0,
  "id": null,
  "links": [],
  "panels": [
    {
      "datasource": {
        "type": "prometheus",
        "uid": "${DS_PROMETHEUS}"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic-by-name"
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 5,
        "y": 0
      },
      "id": 2,
      "options": {
        "displayMode": "lcd",
        "legend": {
          "calcs": [],
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": false
        },
        "maxVizHeight": 300,
        "minVizHeight": 16,
        "minVizWidth": 8,
        "namePlacement": "auto",
        "orientation": "horizontal",
        "reduceOptions": {
          "calcs": [
            "lastNotNull"
          ],
          "fields": "",
          "values": false
        },
        "showUnfilled": true,
        "sizing": "auto",
        "valueMode": "text"
      },
      "pluginVersion": "11.4.0",
      "targets": [
        {
          "editorMode": "code",
          "expr": "flask_app_requests_total",
          "legendFormat": "{{method}}",
          "range": true,
          "refId": "A",
          "datasource": {
            "type": "prometheus",
            "uid": "${DS_PROMETHEUS}"
          }
        }
      ],
      "title": "Panel Title",
      "type": "bargauge"
    },
    {
      "datasource": {
        "type": "prometheus",
        "uid": "${DS_PROMETHEUS}"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "thresholds"
          },
          "mappings": [
            {
              "options": {
                "0": {
                  "color": "red",
                  "index": 1,
                  "text": "DOWN"
                },
                "1": {
                  "color": "green",
                  "index": 0,
                  "text": "UP"
                }
              },
              "type": "value"
            },
            {
              "options": {
                "match": "null+nan",
                "result": {
                  "color": "red",
                  "index": 2,
                  "text": "DOWN"
                }
              },
              "type": "special"
            }
          ],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 9,
        "w": 9,
        "x": 2,
        "y": 8
      },
      "id": 1,
      "options": {
        "colorMode": "value",
        "graphMode": "area",
        "justifyMode": "auto",
        "orientation": "auto",
        "percentChangeColorMode": "standard",
        "reduceOptions": {
          "calcs": [
            "lastNotNull"
          ],
          "fields": "",
          "values": false
        },
        "showPercentChange": false,
        "textMode": "auto",
        "wideLayout": true
      },
      "pluginVersion": "11.4.0",
      "targets": [
        {
          "datasource": {
            "type": "prometheus",
            "uid": "${DS_PROMETHEUS}"
          },
          "editorMode": "code",
          "expr": "up {job=\"microservicecliente\"}",
          "legendFormat": "__auto",
          "range": true,
          "refId": "A"
        }
      ],
      "title": "Panel Title",
      "type": "stat"
    },
    {
      "datasource": {
        "type": "prometheus",
        "uid": "${DS_PROMETHEUS}"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "fixedColor": "yellow",
            "mode": "fixed"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "barWidthFactor": 0.6,
            "drawStyle": "line",
            "fillOpacity": 0,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 9,
        "w": 9,
        "x": 11,
        "y": 8
      },
      "id": 3,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": true
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "pluginVersion": "11.4.0",
      "targets": [
        {
          "editorMode": "code",
          "expr": "rate(flask_app_request_latency_seconds_sum[10m]) \r\n/\r\nrate(flask_app_request_latency_seconds_count[10m])",
          "legendFormat": "__auto",
          "range": true,
          "refId": "A",
          "datasource": {
            "type": "prometheus",
            "uid": "${DS_PROMETHEUS}"
          }
        }
      ],
      "title": "Latencia últimos10 minutos",
      "type": "timeseries"
    }
  ],
  "refresh": "5s",
  "schemaVersion": 40,
  "tags": [],
  "templating": {
    "list": []
  },
  "time": {
    "from": "now-6h",
    "to": "now"
  },
  "timepicker": {},
  "timezone": "browser",
  "title": "New dashboard",
  "uid": "fenw8dihslon4d",
  "version": 4,
  "weekStart": ""
}
```

Paso 5. En el archivo **db.env**, añade el siguiente contenido: 

```properties
MYSQL_DATABASE=datadb
MYSQL_ROOT_PASSWORD=1234
```

Paso 6. De igual forma, en el archivo **docker-compose.yaml**, añade el siguiente contenido: 

```yaml
services:

  prometheus:
    container_name: prometheus
    image: edgardovefe/pgclase:prometheus
    volumes:
      - './prometheus.yaml:/etc/prometheus/prometheus.yml'
    depends_on:
      microclient:
        condition: service_healthy
    ports:
      - 9090:9090
    networks:
      - microservicenetwork
    
  grafana:
    container_name: grafana
    image: edgardovefe/pgclase:grafana
    ports:
      - 3000:3000
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=pass
    volumes:
      - grafana-storage:/var/lib/grafana
    networks:
      - microservicenetwork


  mysqlserver:
    container_name: mysqlserver
    image: "mysql:8.0"
    env_file:
      - db.env
    networks:
      - microservicenetwork
    healthcheck:
      test: mysqladmin ping -uroot -p${MYSQL_ROOT_PASSWORD} -hlocalhost
  
  microclient:
    container_name: microclient
    image: edgardovefe/devsecopspy:microservicegrafana
    env_file:
      - micro-client.env
    networks:
      - microservicenetwork
    ports:
      - 8082:8082
    depends_on:
      mysqlserver:
        condition: service_healthy
    healthcheck:
      test: curl -f http://localhost:8082/client

volumes:
  grafana-storage:

networks:
  microservicenetwork:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.32.0/24
          gateway: 192.168.32.1
```

Paso 7. Por otro lado, en el archivo **micro-client.env**, añade el siguiente contenido: 

```properties
NAME_DB=datadb
IP_DB=mysqlserver
PORT_DB=3306
AZURE_TENANT_ID=<tenant_id>
AZURE_CLIENT_ID=<client_id>
AZURE_CLIENT_SECRET=<app_secret>
KEYVAULT_URI=<uri_key_vault>
```
> ⚠️ _**IMPORTANTE:** Sustituye los valores por la información del **app registry de Azure**._

Paso 8. Además en el archivo **prometheus.yaml**, añade el siguiente contenido:

```yaml
global:
  scrape_interval: 5s

scrape_configs:
  - job_name: 'microservicecliente'
    metrics_path: '/metrics'
    static_configs:
      - targets: ['microclient:8082']
```

Paso 9. Después, abre una terminal en **Visual Studio Code**. 

Paso 10. Ejecuta el siguiente comando (asegúrate de estar en la misma ruta donde se encuentra el archivo **docker-compose.yaml**):

```bash
docker-compose up -d
```

> 💡 ***Nota:** Este comando iniciará toda la infraestructura, incluyendo tu microservicio cliente, Grafana y Prometheus. Puede tardar unos segundos.*

Paso 11. Verifica que la salida del comando sea similar a la siguiente: 

![alt text](../images/10/2.png)

Paso 12. Asegúrate de que los contenedores estén en ejecución con el siguiente comando:

```bash
docker ps
```

![alt text](../images/10/3.png)

> ⚠️ _**IMPORTANTE:** Deben de ser 4 contenedores iniciados._

## Crear Dashboard en Grafana [return](#instrucciones)

Paso 1. Abre un navegador web y escribe la siguiente dirección **`http://localhost:3000`**.

![alt text](../images/10/4.png)

Paso 2. Ingresa las credenciales cuando se te soliciten:

- User: **admin**
- Password: **pass**

Paso 3. Una vez dentro de Grafana, busca la opción **Connections ➡️Add new Connection** en el menú lateral.

![alt text](../images/10/5.png)

Paso 4. Busca **prometheus**.

![alt text](../images/10/6.png)

Paso 5. Selecciona **Add new data source**.

Paso 6. Crea el data source con la siguiente información:

- Name: **prometheus**
- Prometheus server URL: **http://prometheus:9090**
- Deja todo lo demás con la configuración por defecto.

![alt text](../images/10/7.png)

Paso 7. Guarda el data source desplazándote hasta el final de la página de configuración de Prometheus y haciendo clic en **Save & test**.

![alt text](../images/10/8.png)

Paso 8. Ve al menú lateral de Grafana y selecciona **Dashboards**.

![alt text](../images/10/9.png)

Paso 9. En la sección **Dashboards**, seleccion **New ➡️ Import**.

![alt text](../images/10/10.png)

Paso 10. Selecciona **Upload dashboard JSON file** y elige el archivo `dashboard.json` que guardaste en la carpeta **prometheus y grafana**. 

![alt text](../images/10/11.png)

Paso 11. Seleccionamos el data source llamado **prometheus**.

Paso 12. Si seguiste correctamente todos los pasos, deberías ver el siguiente dashboard:

![alt text](../images/10/12.png)

## Resultado esperado:

Si has llegado hasta aquí, significa que ya implementaste correctamente un dashboard de monitoreo para tu microservicio cliente.

![alt text](../images/10/12.png)

---

**[⬅️ Atrás](https://netec-mx.github.io/DEVSECOPS_PYT/Capitulo9/)** | **[🗂️ Lista general](https://netec-mx.github.io/DEVSECOPS_PYT/)**

---
