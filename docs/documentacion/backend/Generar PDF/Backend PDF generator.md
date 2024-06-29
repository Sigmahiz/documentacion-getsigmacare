# Backend Guia de PDF Service

## Requerimientos
- Se necesita tener instalados los paquetes
- Que los archivos en la ruta apps/sigma-lite/src/app/pdf/template tengan permisos de lectura
	- Ejemplo chmod -R 777 apps/sigma-lite/src/app/pdf/template
## Estructura y archivos
- Carpeta con los templates:
	-  apps/sigma-lite/src/app/pdf/template
- Servicio de PDF:
	- apps/sigma-lite/src/app/pdf/pdf.service.ts

## Ejemplo de uso 

### Template  example.html
```
<html>
	<head></head>
	<body>
		<h1> {{name}} {{email}} </h1>
	</body>
</html>
```
### Controller PdfController.ts
```
import {PdfService} from  "../../pdf/pdf.service";//importar el service

import { 
	Controller, 
	Get, 
	Res // necesario para implementar el response en formato HTML
} from  '@nestjs/common';

@Controller('pdf')
export class PdfController {
  constructor(private pdfService: PdfService) {} //inicializar en el constructor

  @Get('generate') //metodo http
  async generatePdf() { // funcion que invoca por callback
    const template = 'example'; //definir el template example.html
    const data = {  // definir data en un objeto (clave valor)
      name: 'John Doe',
      email: 'john.doe@example.com',
    };
	// llamar con async/await el metodo y colocar template y data
    const html = await this.pdfService.generateFromTemplate(template, data);
    res //invocar el metodo
    .status(200) //definir un codigo de respuesta
    .header('Content-Type', 'text/html') // establecer un formato de respuesta
    .send(html);//data a ser devuelta en este caso el PDF en html
  }
}
```