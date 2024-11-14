# Jenkins Pipeline

Este archivo define un pipeline de Jenkins que automatiza la construcción, pruebas y despliegue de tres módulos diferentes en función de las opciones seleccionadas por el usuario. El pipeline incluye varias etapas para la limpieza del espacio de trabajo, verificación de dependencias, clonación de repositorios, construcción de módulos específicos, ejecución de pruebas unitarias, y el envío de notificaciones por correo electrónico con los resultados del pipeline.

## Parámetros

El pipeline acepta los siguientes parámetros para controlar su flujo:

- **DEPLOY_ENVIRONMENT**: Define el entorno de despliegue (production, staging, development, testing). Esto determinará las variables y configuraciones aplicadas durante el proceso.
  
- **BUILD_MODULE**: Selecciona el módulo que se desea construir en esta ejecución del pipeline. Las opciones disponibles son:
  - Jugar Trivia
  - Encargar Pedido
  - Traducir USQL
  
- **TEST_MODULE**: Define el módulo para el cual se ejecutarán pruebas unitarias. Las opciones son:
  - Entregable 1 - Trivia
  - Entregable 3 - USQL/SQL
  - Otro (si no se desean ejecutar pruebas).

## Etapas del Pipeline

### 1. Clean Workspace
Limpia el espacio de trabajo eliminando los directorios de los entregables existentes y cualquier archivo temporal.

### 2. Check Python and Pip
Verifica que Python y Pip estén instalados correctamente.

### 3. Checkout
Clona los tres repositorios necesarios para los módulos:
- **entregable1final** (Trivia)
- **Entregable2-Pedidos** (Pedidos)
- **entregable3_DSL** (USQL)

### 4. Build Selected Module
Dependiendo de la opción seleccionada en el parámetro **BUILD_MODULE**, construye el módulo correspondiente:
- **Jugar Trivia**: Ejecuta el script principal del juego de trivia.
- **Encargar Pedido**: Llama al job "Build Concurrency Module" (probablemente un job separado en Jenkins).
- **Traducir USQL**: Ejecuta el script principal de traducción de consultas USQL a SQL.

### 5. Install dependencies for Entregable 3 - USQL/SQL
Si se selecciona el módulo de traducción USQL o se elige ejecutar pruebas para este módulo, instala las dependencias necesarias como `ply`, `sqlparse` y `coverage`.

### 6. Unit Tests for Selected Module
Ejecuta las pruebas unitarias para el módulo seleccionado. Las pruebas se ejecutan en los directorios de los entregables correspondientes:
- **entregable1final** para Trivia
- **entregable3_DSL** para USQL.

## Notificaciones

El pipeline incluye un bloque `post` que envía correos electrónicos con los resultados del pipeline:

- **always**: Se envía un correo electrónico con los detalles del resultado del pipeline (ya sea exitoso o fallido).
- **failure**: Si el pipeline falla, se envía un correo de alerta indicando que el pipeline ha fallado.

### Notificaciones por correo:
- **To**: florenciaporras03@gmail.com
- **Subject**: Contiene el nombre completo del pipeline, indicando si fue exitoso o fallido.
- **Body**: Incluye el resultado del pipeline y un enlace con los detalles.

## Requisitos

El pipeline asume que los módulos mencionados están disponibles en los repositorios de GitHub y que los scripts necesarios para su ejecución están ubicados en las rutas correspondientes dentro de los repositorios.

Además para que funcionen las notificaciones por correo, se necesita tener configurado Jenkins.
  En Dashboard > Manage Jenkins > System
  Se tiene que configurar  Extended E-mail Notification:

    - SMTP server: smtp.gmail.com
    - SMTP Port: 465
  
  En advanced:
    Credentials: se tiene que tener creada una credencial con el nombre del mail y seleccionar Use SSL

  Y se tiene que configurar E-mail Notification:
  
    - SMTP server: smtp.gmail.com

  En advancd: 
    - Seleccionar Use SMTP Authentication
    - User Name: en este caso esta configurado para florenciaporras03@gmail.com (pero debes de poner el tuyo)
    - Password: se tiene que usar la App password configurada en el mail de User Name
    - SMTP Port: 465
    - Reply-To Address: mismo mail que User Name

  Y luego se clickea Apply
  









