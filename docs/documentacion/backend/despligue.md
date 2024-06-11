**Para realizar la puesta en producción del backend, se requiere:**

 - Sistema operativo Ubuntu de preferencia
 - Node con la versión LTS (El sistema actualmente con la versión 20)
	 - Para instalar NVM en ubuntu y configurar una versión de node puedes utilizar el siguiente comando:
		 - Descargar e instalar NVM:`curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash`
		 - Luego cargar en el terminal: ```source ~/.bashrc```
		 - Instalar y usar LTS versión:
			 - ```nvm install --lts```
			 - ```nvm use --lts```
	 - Instalar PNPM, ```npm install -g pnpm```
	 - Instalar GIT y clonar el repositorio https://github.com/Sigmahiz/sigma-back.git
	 - Ejecutar `pnpm i` para instalar las dependencias
	 - Instalar pm2, con `pnpm install:pm2` o `npm install -g pm2`
	 - Establecer en tu `.env.{entorno}` la configuración de base de datos y JWT, en el .env.example se puede encontrar más información
	 - Ejecutar `pnpm sigma-lite:migrate:{entorno}` para tener generar los schemas de base datos y generar las tablas en la base datos.
	 - Ejecutar `pnpm sigma-lite:seed:{entorno}`, para generar datos de prueba si es requerido (opcional)
	 - Ejecutar `pnpm sigma-lite:{entorno}` para verificar que no existan errores de compilación
	 - Si el sistema funciona con normalidad ejecutar `pnpm start:sigma-backend` para añadirlo a la instancia de pm2,

