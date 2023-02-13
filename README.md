# PRÁCTICA FINAL LIBERANDO PRODUCTOS :bell:

## Tabla de contenidos :file_folder:

- [Objetivo de la práctica](#Objetivo-de-la-práctica)
- [Requisitos previos](#Requisitos-previos)
- [Clonado del repositorio](#clonado-del-repositorio)
- [Proyecto inicial](#proyecto-inicial)
    - [Software necesario](#software-necesario)
- [Ejecución de servidor](#ejecución-de-servidor)
    - [Ejecución directa con Python](#ejecución-directa-con-python)
    - [Ejecución a través de un contenedor Docker](#Ejecución-a-través-de-un-contenedor-Docker)
- [Comprobación de endpoints de servidor y métricas](#Comprobación-de-endpoints-de-servidor-y-métricas)
- [Tests](#Tests)
- [Practica a realizar](Practica-a-realizar)
    - [Table of Contents generated with DocToc](#tabla-de-contenidos)

## Objetivo de la práctica :books:

El objetivo es mejorar un proyecto creado previamente para ponerlo en producción, a través de la adicción de una serie de mejoras.

## Requisitos previos 	:memo:

- Cuenta de GitHub
- Minikube
- Kubectl
- Helm
- Docker
- Docker-compose
- Cuenta de Dockerhub

## Clonado del repositorio :blue_book:
Para el clonado del repositorio solo es necesario realizarlo de la siguiente manera:

- git clone git@github.com:KeepCodingCloudDevops6/Liberando-productos-Crisgarcia.git o bien,
- git clone https://github.com/KeepCodingCloudDevops6/Liberando-productos-Crisgarcia.git



## Proyecto inicial :round_pushpin:

El proyecto inicial es un servidor que realiza lo siguiente:

- Utiliza [FastAPI](https://fastapi.tiangolo.com/) para levantar un servidor en el puerto `8081` e implementa inicialmente dos endpoints:
 - `/`: Devuelve en formato `JSON` como respuesta `{"msg":"Hello World"}` y un status code 200.
  - `/health`: Devuelve en formato `JSON` como respuesta `{"health": "ok"}` y un status code 200.

- Se han implementado tests unitarios para el servidor [FastAPI](https://fastapi.tiangolo.com/)

- Utiliza [prometheus-client](https://github.com/prometheus/client_python) para arrancar un servidor de métricas en el puerto `8000` y poder registrar métricas, siendo inicialmente las siguientes:
  - `Counter('server_requests_total', 'Total number of requests to this webserver')`: Contador que se incrementará cada vez que se haga una llamada a alguno de los endpoints implementados por el servidor (inicialmente `/` y `/health`)
  - `Counter('healthcheck_requests_total', 'Total number of requests to healthcheck')`: Contador que se incrementará cada vez que se haga una llamada al endpoint `/health`.
  - `Counter('main_requests_total', 'Total number of requests to main endpoint')`: Contador que se incrementará cada vez que se haga una llamada al endpoint `/`.

## Software necesario :cd:

Es necesario disponer del siguiente software:

- `Python` en versión `3.8.5` o superior, disponible para los diferentes sistemas operativos en la [página oficial de descargas](https://www.python.org/downloads/release/python-385/)

- `virtualenv` para poder instalar las librerías necesarias de Python, se puede instalar a través del siguiente comando:

    ```sh
    pip3 install virtualenv
    ```

    En caso de estar utilizando Linux y el comando anterior diera fallos, se debe ejecutar el siguiente comando:

    ```sh
    sudo apt-get update && sudo apt-get install -y python3.8-venv
    ```

- `Docker` para poder arrancar el servidor implementado a través de un contenedor Docker, es posible descargarlo a [través de su página oficial](https://docs.docker.com/get-docker/).

## Ejecución de servidor :desktop_computer:

### Ejecución directa con Python

1. Instalación de un virtualenv, **realizarlo solo en caso de no haberlo realizado previamente**:
   1. Obtener la versión actual de Python instalada para crear posteriormente un virtualenv:

        ```sh
        python3 --version
        ```

        El comando anterior mostrará algo como lo mostrado a continuación:

        ```sh
            Python 3.8.13
        ```

   2. Crear de virtualenv en la raíz del directorio para poder instalar las librerías necesarias:

       - En caso de en el comando anterior haber obtenido `Python 3.8.*`

            ```sh
            python3.8 -m venv venv
            ```

       - En caso de en el comando anterior haber obtenido `Python 3.9.*`:

           ```sh
           python3.9 -m venv venv
           ```

2. Activar el virtualenv creado en el directorio `venv` en el paso anterior:

     ```sh
     source venv/bin/activate
     ```

3. Instalar las librerías necesarias de Python, recogidas en el fichero `requirements.txt`, **solo en caso de no haber realizado este paso previamente**. Es posible instalarlas a través del siguiente comando:

    ```sh
    pip3 install -r requirements.txt
    ```

4. Ejecución del código para arrancar el servidor:

    ```sh
    python3 src/app.py
    ```

5. La ejecución del comando anterior debería mostrar algo como lo siguiente:

    ```sh
    [2022-04-16 09:44:22 +0000] [1] [INFO] Running on http://0.0.0.0:8081 (CTRL + C to quit)
    ```

### Ejecución a través de un contenedor Docker

1. Crear una imagen Docker con el código necesario para arrancar el servidor:

    ```sh
    docker build -t simple-server:0.0.1 .
    ```

2. Arrancar la imagen construida en el paso anterior mapeando los puertos utilizados por el servidor de FastAPI y el cliente de prometheus:

    ```sh
    docker run -d -p 8000:8000 -p 8081:8081 --name simple-server simple-server:0.0.1
    ```

3. Obtener los logs del contenedor creado en el paso anterior:

    ```sh
    docker logs -f simple-server
    ```

4. La ejecución del comando anterior debería mostrar algo como lo siguiente:

    ```sh
    [2022-04-16 09:44:22 +0000] [1] [INFO] Running on http://0.0.0.0:8081 (CTRL + C to quit)
    ```

## Comprobación de endpoints de servidor y métricas

Una vez arrancado el servidor, utilizando cualquier de las formas expuestas en los apartados anteriores, es posible probar las funcionalidades implementadas por el servidor:

- Comprobación de servidor FastAPI, a través de llamadas a los diferentes endpoints:

  - Realizar una petición al endpoint `/`

      ```sh
      curl -X 'GET' \
      'http://0.0.0.0:8081/' \
      -H 'accept: application/json'
      ```

      Debería devolver la siguiente respuesta:

      ```json
      {"message":"Hello World"}
      ```

  - Realizar una petición al endpoint `/health`

      ```sh
      curl -X 'GET' \
      'http://0.0.0.0:8081/health' \
      -H 'accept: application/json' -v
      ```

      Debería devolver la siguiente respuesta.

      ```json
      {"health": "ok"}
      ```

- Comprobación de registro de métricas, si se accede a la URL `http://0.0.0.0:8000` se podrán ver todas las métricas con los valores actuales en ese momento:

  - Realizar varias llamadas al endpoint `/` y ver como el contador utilizado para registrar las llamadas a ese endpoint, `main_requests_total` ha aumentado, se debería ver algo como lo mostrado a continuación:

    ```sh
    # TYPE main_requests_total counter
    main_requests_total 4.0
    ```

  - Realizar varias llamadas al endpoint `/health` y ver como el contador utilizado para registrar las llamadas a ese endpoint, `healthcheck_requests_total` ha aumentado, se debería ver algo como lo mostrado a continuación:

    ```sh
    # TYPE healthcheck_requests_total counter
    healthcheck_requests_total 26.0
    ```

  - También se ha credo un contador para el número total de llamadas al servidor `server_requests_total`, por lo que este valor debería ser la suma de los dos anteriores, tal y como se puede ver a continuación:

    ```sh
    # TYPE server_requests_total counter
    server_requests_total 30.0
    ```

## Tests

Se ha implementado tests unitarios para probar el servidor FastAPI, estos están disponibles en el archivo `src/tests/app_test.py`.

Es posible ejecutar los tests de diferentes formas:

- Ejecución de todos los tests:

    ```sh
    pytest
    ```

- Ejecución de todos los tests y mostrar cobertura:

    ```sh
    pytest --cov
    ```

- Ejecución de todos los tests y generación de report de cobertura:

    ```sh
    pytest --cov --cov-report=html
    ```


Con todos estos pasos ya tendríamos nuestro proyecto inicial.

A continuación se explicará la práctica a realizar:

## Practica a realizar :checkered_flag:

A partir del ejemplo inicial descrito en los apartados anteriores, es necesario realizar una serie de mejoras:

Los requerimientos son los siguientes:

- Añadir por lo menos un nuevo endpoint a los existentes `/` y `/health`, un ejemplo sería `/bye` que devolvería `{"msg": "Bye Bye"}`, para ello será necesario añadirlo en el fichero [src/application/app.py](./src/application/app.py)

```
@app.get("/bye")
    async def read_bye():
        """Implement bye check endpoint"""
        # Increment counter used for register the total number of calls in the webserver
        REQUESTS.inc()
        # Increment counter used for register the total number of calls in the main endpoint
        BYE_ENDPOINT_REQUESTS.inc()
        return {"msg": "bye bye"}
``` 

- Creación de tests unitarios para el nuevo endpoint añadido, para ello será necesario modificar el [fichero de tests](./src/tests/app_test.py)

```
@pytest.mark.asyncio
    async def read_bye_test(self):
        """Tests the health check endpoint"""
        response = client.get("bye")

        assert response.status_code == 200
        assert response.json() == {"msg": "bye bye"}
```

![Endpoints](https://github.com/KeepCodingCloudDevops6/Liberando-productos-Crisgarcia/blob/main/Capturas/Endpoints.png)
- Opcionalmente creación de helm chart para desplegar la aplicación en Kubernetes, se dispone de un ejemplo de ello en el laboratorio realizado en la clase 3

- Creación de pipelines de CI/CD en cualquier plataforma (Github Actions, Jenkins, etc) que cuenten por lo menos con las siguientes fases:

  - Testing: tests unitarios con cobertura. Deberemos incluir en el [test.yaml](./.github/workflows/test.yaml)

  ```
    - name: Run unit-test and coverage
        run: pytest -m "not integtest" --cov
  ```
  Para que esto pueda funcionar sin problemas, deberemos subirlo a nuestro Github y en el apartado de `Actions`, ver si se nos ejecuta todo perfectamente. 

  ![unit-test and coverage](https://github.com/KeepCodingCloudDevops6/Liberando-productos-Crisgarcia/blob/main/Capturas/Prueba%20unitaria%20con%20cobertura.png)

  - Build & Push: creación de imagen docker y push de la misma a cualquier registry válido que utilice alguna estrategia de release para los tags de las vistas en clase, se recomienda GHCR ya incluido en los repositorios de Github. Se dispone de un [release.yaml](./.github/workflows/release.yaml) en el que deberemos incluir:

    Aquí deberemos cambiar el usuario y el nombre de nuestra imagen para que pueda funcionar.

  ```
  - name: Build to DockerHub
        run: docker build -t crisgarcia/liberando-productos-crisgarcia:latest .

      - name: Push to DockerHub
        run: docker push crisgarcia/liberando-productos-crisgarcia:latest
  ```
 Para que esto pueda funcionar sin problemas, deberemos subirlo a nuestro github y en el apartado de `Actions`, ver si se nos ejecuta todo perfectamente.

 ![Build&Push](https://github.com/KeepCodingCloudDevops6/Liberando-productos-Crisgarcia/blob/main/Capturas/Build%26Push.png)

- Configuración de monitorización y alertas:

  - Configurar monitorización mediante prometheus en los nuevos endpoints añadidos, por lo menos con la siguiente configuración:
    - Contador cada vez que se pasa por el/los nuevo/s endpoint/s, tal y como se ha realizado para los endpoints implementados inicialmente.

  - Desplegar prometheus a través de Kubernetes mediante minikube y configurar alert-manager para por lo menos las siguientes alarmas, tal y como se ha realizado en el laboratorio del día 3 mediante el chart `kube-prometheus-stack`:
    - Uso de CPU de un contenedor mayor al del límite configurado, se puede utilizar como base el ejemplo utilizado en el laboratorio 3 para mandar alarmas cuando el contenedor de la aplicación `fast-api` consumía más del asignado mediante request.

  - Las alarmas configuradas deberán tener severity high o critical.

  - Crear canal en slack `<nombreAlumno>-prometheus-alarms` y configurar webhook entrante para envío de alertas con alert manager.

  - Alert manager estará configurado para lo siguiente:
    - Mandar un mensaje a Slack en el canal configurado en el paso anterior con las alertas con label "severity" y "critical".
    - Deberán enviarse tanto alarmas como recuperación de las mismas.
    - Habrá una plantilla configurada para el envío de alarmas.

    Para poder comprobar si esta parte funciona se recomienda realizar una prueba de estres, como la realizada en el laboratorio 3 a partir del paso 8.

    ![Alarma Slack](https://github.com/KeepCodingCloudDevops6/Liberando-productos-Crisgarcia/blob/main/Capturas/Alarma%20Slack.png)

  - Creación de un dashboard de Grafana, con por lo menos lo siguiente:
    - Número de llamadas a los endpoints.
    - Número de veces que la aplicación ha arrancado.

![Grafana](https://github.com/KeepCodingCloudDevops6/Liberando-productos-Crisgarcia/blob/main/Capturas/Grafana.png)

```Para poder ejecutar todas estas mejoras deberemos seguir los siguientes pasos:```

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Tabla de contenidos**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Monitoring-Autoscaling](#monitoring-autoscaling)
  - [Objetivo](#objetivo)
  - [Software necesario](#software-necesario)
  - [Prerrequisitos](#prerequisitos)
  - [Escenarios de prueba](#escenarios-de-prueba)
    - [Despliegue de aplicación simple-fast-api](#despliegue-de-aplicación-simple-fast-api)
    - [Autoescalado con KEDA](#autoescalado-con-keda)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Configuración de monitorización y alertas.

## Objetivo

El objetivo de este laboratorio es realizar mejoras de monitorización mediante el stack de Prometheus, utilizando el chart kube-prometheus-stack para ello.

## Software necesario

- [minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [helm](https://helm.sh/docs/intro/install/)

## Prerrequisitos

1. Crear un cluster de Kubernetes que utilice la versión `v1.21.1` utilizando minikube para ello a través de un nuevo perfil llamado `monitoring-demo`:

    ```sh
    minikube start --kubernetes-version='v1.21.1' \
        --memory=4096 \
        -p monitoring-demo
    ```

2. Crear un espacio de trabajo de Slack utilizando la [página oficial](https://slack.com/get-started#/createnew)

3. Crear un [slack webhook](https://api.slack.com/messaging/webhooks) y conectarlo con un canal que se debe crear, para realizar el envío de las alarmas por parte de AlertManager que se utilizará posteriormente. Para ello desde la aplicación de Slack se deben realizar los siguientes pasos:

    1. Crear un canal para el envío de alarmas, haciendo click en el botón con el símbolo `+` en el panel lateral izquierdo de la sección `Canales`, aparecerá un desplegable con dos opciones, donde se deberá hacer click en la opción `Crear un canal`.


    2. Introducir el nombre para el canal que se utilizará para el envío de notificaciones relacionadas con alarmas, en este caso  `<nombreAlumno>-prometheus-alarms`, añadiendo opcionalmente una descripción para el propósito del canal. 

    3. Una vez introducidos los datos hacer click en el botón `Crear` de color verde.

    4. Hacer click en la sección de la esquina superior izquierda con el nombre `Más`, pulsando este botón, saldrá un menú desplegable sobre el que es necesario pulsar en el botón `Aplicaciones`, tal y como se puede ver en la siguiente captura.


    5. Una vez en la sección de Aplicaciones de Slack es necesario buscar la aplicación `Incoming WebHooks`, introduciendo para ello ese nombre en el cuadro de búsqueda superior. En la lista de resultados dentro de la sección `Aplicaciones disponibles` debería aparecer una con el nombre `Incoming WebHooks`.

    6. Será necesario hacer click sobre el botón `Abrir` que aparece justo debajo de la aplicación mencionada en el paso anterior, una vez hecho esto se abrirá una página en el navegador web para añadir esta aplicación al espacio de Slack que se ha creado como parte del laboratorio Actual.

        Es necesario pulsar hacer click sobre el botón `Añadir a Slack` para poder disponer de esta aplicación en el espacio de trabajo de Slack.

    7. Será necesario configurar la aplicación `Incoming WebHooks` en el espacio de trabajo, seleccionando para ello el canal de Slack al que enviar notificaciones, escogiendo el canal creado para el envío de notificaciones en los pasos anteriores en el menú desplegable `Publicar en el canal`.

        Una vez seleccionado el canal, será necesario pulsar sobre el botón `Añadir integración con Webhooks entrantes`.

    8. Una vez realizado el paso anterior se mostrará una guía de configuración y utilización de la aplicación, de ello interesa que se almacene el valor que se muestra para el campo `URL de Webhook`, ya que se utilizará posteriormente para el envío de notificaciones de alarmas. 


4. Modificar el fichero `custom_values_prometheus.yaml` añadiendo el canal al que se enviarán las alarmas, así como la URL del webhook configurado en el paso anterior:

    ```yaml
    . . .
    alertmanager:
        config:
            global:
            resolve_timeout: 5m
            route:
            group_by: ['job']
            group_wait: 30s
            group_interval: 5m
            repeat_interval: 12h
            receiver: 'slack'
            routes:
            - match:
                alertname: Watchdog
                receiver: 'null'
            # This inhibt rule is a hack from: https://stackoverflow.com/questions/54806336/how-to-silence-prometheus-alertmanager-using-config-files/54814033#54814033
            inhibit_rules:
            - target_match_re:
                alertname: '.+Overcommit'
                source_match:
                alertname: 'Watchdog'
                equal: ['prometheus']
            receivers:
            - name: 'null'
            - name: 'slack'
            slack_configs:
            - api_url: 'https://hooks.slack.com/services/YOUR_SLACK_WEBHOOK_HERE' # <--- AÑADIR EN ESTA LÍNEA EL WEBHOOK CREADO
                send_resolved: true
                channel: '#notif-channel' # <--- AÑADIR EN ESTA LÍNEA EL CANAL
    . . .
    ```

    > **Solamente es necesario modificar las líneas mostradas, el resto se deben dejar como están en el fichero**

5. Añadir el repositorio de helm `prometheus-community` para poder desplegar el chart `kube-prometheus-stack`:

    ```sh
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm repo update
    ```

6. Desplegar el chart `kube-prometheus-stack` del repositorio de helm añadido en el paso anterior con los valores configurados en el archivo `custom_values_prometheus.yaml` en el namespace `monitoring`:

    ```sh
    helm -n monitoring upgrade --install prometheus prometheus-community/kube-prometheus-stack -f custom_values_prometheus.yaml --create-namespace --wait --version 34.1.1
    ```

7. Realizar split de la terminal o crear una nueva pestaña y ver como se están creando pod en el namespace `monitoring` utilizado para desplegar el stack de prometheus:

    ```sh
    kubectl -n monitoring get po -w
    ```

8. Añadir el repositorio de helm de bitnami para poder desplegar el chart de mongodb empaquetado por esta compañía:

    ```sh
    helm repo add bitnami https://charts.bitnami.com/bitnami
    helm repo update
    ```

9. Instalar el addond `metrics-server` en el perfil utilizado actualmente de minikube:

    ```sh
    minikube addons enable metrics-server -p monitoring-demo
    ```

## Escenarios de prueba

### Despliegue de aplicación simple-fast-api

1. Se ha creado un helm chart en la carpeta `fast-api-webapp`, en la cual se han realizado modificaciones  para disponer de métricas mediante prometheus. Para desplegarlo es necesario realizar los siguientes pasos:

    1. Descargar las dependencias necesarias, siendo en este caso el chart de mongodb

        ```sh
        helm dep up fast-api-webapp
        ```

    2. Desplegar el helm chart:

        ```sh
        helm -n fast-api upgrade my-app --wait --install --create-namespace fast-api-webapp
        ```

    3. Hacer split de la terminal o crear una nueva pestaña en la misma y observar como se crean los pods en el namespace `fast-api` donde se ha desplegado el web server:

        ```sh
        kubectl -n fast-api get po -w
        ```

    4. Hacer de nuevo split de la terminal o crear una nueva pestaña y obtener los logs del container `wait-mongo` del deployment `my-app-fast-api-webapp` en el namespace `fast-api`, observar como está utilizando ese contenedor para esperar a que MongoDB esté listo:

        ```sh
        kubectl -n fast-api logs -f deployment/my-app-fast-api-webapp -c wait-mongo
        ```

        Una vez se obtenga el mensaje de conexión exitosa a mongo, siendo algo como lo mostrado a continuación, indicará que empezará el contenedor:

        ```sh
        Connecting to my-app-mongodb:27017 (10.101.242.27:27017)
        HTTP/1.0 200 OK
        Connection: close
        Content-Type: text/plain
        Content-Length: 85
        ```

    5. Obtener los logs del contenedor `fast-api-webapp` del deployment `my-app-fast-api-webapp` en el namespace `fast-api`, observar como está arrancando el servidor fast-api:

        ```sh
        kubectl -n fast-api logs -f deployment/my-app-fast-api-webapp -c fast-api-webapp
        ```

        Debería obtenerse un resultado similar al siguiente:

        ```sh
        [2022-11-09 11:28:12 +0000] [1] [INFO] Running on http://0.0.0.0:8081 (CTRL + C to quit)
        ```

2. Abrir una nueva pestaña en la terminal y realizar un port-forward del puerto `http-web` del servicio de Grafana al puerto 3000 de la máquina:

    ```sh
    kubectl -n monitoring port-forward svc/prometheus-grafana 3000:http-web
    ```

3. Abrir otra pestaña en la terminal y realizar un port-forward del servicio de Prometheus al puerto 9090 de la máquina:

    ```sh
    kubectl -n monitoring port-forward svc/prometheus-kube-prometheus-prometheus 9090:9090
    ```

4. Abrir una nueva pestaña en la terminal y realizar un port-forward al `Service` creado para nuestro servidor:

    ```sh
    kubectl -n fast-api port-forward svc/my-app-fast-api-webapp 8081:8081
    ```

5. Acceder a la dirección `http://localhost:3000` en el navegador para acceder a Grafana, las credenciales por defecto son `admin` para el usuario y `prom-operator` para la contraseña.

    Cuando estemos dentro de Grafana, deberemos de irnos a los cuatro cuadrados que se ven en la izquierda de la pantalla, y meternos en `Browse`.

    Cuando estemos dentro de `Browse`, a nuestra derecha, encontraremos el botón de `import` al que deberemos dar y subiremos el archivo `custom_dashboard.json` que tenemos en nuestro repositorio.

    Por último, cuando cargue el archivo, Grafana nos enseñará todos los números de llamadas a los endpoint arriba especificados en la última hora.

6. Acceder a la dirección `http://localhost:9090` para acceder al Prometheus, **por defecto no se necesita autenticación**.

7. Empezar a realizar diferentes peticiones al servidor de fastapi, es posible ver los endpoints disponibles y realizar peticiones a los mismos a través de la URL `http://localhost:8081/docs` utilizando swagger

8. Acceder al dashboard creado para observar las peticiones al servidor a través de la URL `http://localhost:3000/dashboards`, seleccionando una vez en ella la opción Import y en el siguiente paso seleccionar **Upload JSON File** y seleccionar el archivo presente en esta carpeta llamado `custom_dashboard.json`.
Por último, cuando cargue el archivo, Grafana nos enseñará todos los numeros de llamadas a los endpoint arriba especificados en la última hora.

![Creación dashboard Grafana](https://github.com/KeepCodingCloudDevops6/Liberando-productos-Crisgarcia/blob/main/Capturas/Creaci%C3%B3n%20dashboard%20Grafana.png)

9. Obtener el pod creado en el paso 1 para poder lanzar posteriormente un comando de prueba de estrés, así como seleccionarlo en el menú desplegable del panel de Grafana:

   ```sh
   export POD_NAME=$(kubectl get pods --namespace fast-api -l "app.kubernetes.io/name=fast-api-webapp,app.kubernetes.io/instance=my-app" -o jsonpath="{.items[0].metadata.name}")
   echo $POD_NAME
   ```

   Se debería obtener un resultado similar al siguiente:

   ```sh
   my-app-fast-api-webapp-585bf945cc-lvvpv
   ```

10. Utilizar el resultado obtenido en el paso anterior para seleccionar en el dashboard creado de Grafana para seleccionar el pod del que obtener información, seleccionando este a través del menú desplegable de nombre `pod`.

11. Acceder mediante una shell interactiva al contenedor `fast-api-webapp` del pod obtenido en el paso anterior:

    ```sh
    kubectl -n fast-api exec --stdin --tty $POD_NAME -c fast-api-webapp -- /bin/sh
    ```

12. Dentro de la shell en la que se ha accedido en el paso anterior, instalar y utilizar los siguientes comandos para descargar un proyecto de github que realizará ```pruebas de estrés```:

    1. Instalar los binarios necesarios en el pod

        ```sh
        apk update && apk add git go
        ```

    2. Descargar el repositorio de github y acceder a la carpeta de este, donde se realizará la compilación del proyecto:

        ```sh
        git clone https://github.com/jaeg/NodeWrecker.git
        cd NodeWrecker
        go build -o extress main.go
        ```

    3. Ejecución del binario obtenido de la compilación del paso anterior que realizará una prueba de estrés dentro del pod:

        ```sh
        ./extress -abuse-memory -escalate -max-duration 10000000
        ```

13. Abrir una nueva pestaña en la terminal y ver como evoluciona el HPA creado para la aplicación web:

    ```sh
    kubectl -n fast-api get hpa -w
    ```

14. Se debería recibir una notificación como la siguiente en el canal de Slack configurado para el envío de notificaciones sobre alarmas:

    ```sh
    [FIRING:1] Monitoring Event Notification
    Alert: fastApiConsumingMoreThanRequest - critical
    Description: Pod my-app-fast-api-webapp-585bf945cc-lvvpv is consuming more than requested
    Graph: :gráfico_con_tendencia_ascendente: Runbook: <|:cuaderno_de_espiral:>
    Details:
    • alertname: fastApiConsumingMoreThanRequest
    • pod: my-app-fast-api-webapp-585bf945cc-lvvpv
    • prometheus: monitoring/prometheus-kube-prometheus-prometheus
    • severity: critical
    ```


15. El Horizontal Pod Autoscaler escalará el número de pods para mitigar este pico y, por lo tanto, pasado un tiempo debería recibirse una notificación de que la alarma se ha mitigado.


16. Para parar el cluster de minikube creado para esta parte del laboratorio:

    ```sh
    minikube -p monitoring-demo stop
    ```


:woman: Cristina Garcia Rodriguez 

:incoming_envelope: crisgarciarodriguez04@gmail.com
