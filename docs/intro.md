# Introducción al Tutorial

Descubramos  **Docusaurus en menos de 5 minutos**.

## Comenzando

Comienza  **creando un nuevo sitio**.

O  **prueba Docusaurus inmediatamente**  con  **[docusaurus.new](https://docusaurus.new/)**.

### Lo que necesitarás

-   [Node.js](https://nodejs.org/en/download/)  versión 18.0 o superior:
    -   Cuando instales Node.js, se recomienda que marques todas las casillas relacionadas con las dependencias.

## Generar un nuevo sitio

Genera un nuevo sitio de Docusaurus usando la  **plantilla clásica**.

La plantilla clásica se agregará automáticamente a tu proyecto después de ejecutar el siguiente comando:

bash

Copiar

```
npm init docusaurus@latest my-website classic

```

Puedes escribir este comando en el símbolo del sistema, PowerShell, Terminal o cualquier otra terminal integrada de tu editor de código.

El comando también instala todas las dependencias necesarias para ejecutar Docusaurus.

## Inicia tu sitio

Ejecuta el servidor de desarrollo:

bash

Copiar

```
cd my-website
npm run start

```

El comando  `cd`  cambia el directorio con el que estás trabajando. Para trabajar con tu nuevo sitio de Docusaurus, deberás navegar hasta allí en la terminal.

El comando  `npm run start`  construye tu sitio web localmente y lo sirve a través de un servidor de desarrollo, listo para que lo veas en  [http://localhost:3000/](http://localhost:3000/).

Abre  `docs/intro.md`  (esta página) y edita algunas líneas: el sitio  **se recarga automáticamente**  y muestra tus cambios.