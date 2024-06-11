## docker-compose.yml
```shell
version: '3'

services:
  grafana:
    image: grafana/grafana
    ports:
      - 3010:3000
    volumes:
      - grafana-data:/var/lib/grafana
    environment:
      - GF_SECURITY_ADMIN_USER=admin
      - GF_SECURITY_ADMIN_PASSWORD=password

volumes:
  grafana-data:
```