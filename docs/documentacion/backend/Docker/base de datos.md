## docker-compose.yml
```shell
version: '3.8'
services:
  db:
    image: postgres:16
    restart: always
    ports:
      - '5432:5432'
    environment:
      POSTGRES_USER: johndoe
      POSTGRES_PASSWORD: randompassword
      POSTGRES_DB: mydb
    volumes:
      - postgres-data:/var/lib/postgresql/data

volumes:
  postgres-data:
```