# Mapogram QGIS Containerized

## Build GIS Server Image:
```bash
docker build -f Dockerfile -t qgis-server ./ 
```

## Setup Containers for QGIS service

- Create Docker Network
```bash
docker network create qgis
```

- Create QGIS server container
```bash
docker run -d  --name qgis-server --net=qgis --hostname=qgis-server \    
              -v $(pwd)/data:/data:ro -p 5555:5555 \                             
              -e "QGIS_PROJECT_FILE=/data/Mapogram-FIRMS.qgz" \
              qgis-server
```

- Create Web server for handling request 
```bash
docker run -d --name nginx --net=qgis --hostname=nginx \     
              -v $(pwd)/nginx.conf:/etc/nginx/conf.d/default.conf:ro -p 8080:80 \
              nginx:1.13
```

## Sample Request for testing
```
http://localhost:8080/qgis-server/?SERVICE=WMS&VERSION=1.3.0&REQUEST=GetPrint&CRS=EPSG:3857&FORMAT=png&TEMPLATE=MapogramView&map0:LAYERS=Google%20Satellite%20Hybrid,MODIS%2024h%20Confidence%20(80-95),MODIS%2024h%20Confidence%20(%3E=%2095)&map0:OPACITIES=255,255,255,255&map0:ROTATION=0&map0:SCALE=43137592&map0:GRID_INTERVAL_X=0&map0:GRID_INTERVAL_Y=0&map0:EXTENT=8571182,-3773207,17443267,2519429
```