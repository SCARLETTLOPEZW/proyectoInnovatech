1. EC2 BACKEND — 98.94.39.79

Primero entra a la instancia servidor app, donde tienes Ventas, Despachos y la base de datos.

Ver contenedores activos
docker ps


Luego:

docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}"


📸 Saca captura de este resultado.

Ver política de reinicio de los contenedores

Copia y pega:

docker inspect backend_ventas \
--format 'BACKEND VENTAS -> RestartPolicy: {{.HostConfig.RestartPolicy.Name}}'


Después:

docker inspect backend_despachos \
--format 'BACKEND DESPACHOS -> RestartPolicy: {{.HostConfig.RestartPolicy.Name}}'


Para la base de datos:

docker inspect base_datos_proyectos \
--format 'BASE DE DATOS -> RestartPolicy: {{.HostConfig.RestartPolicy.Name}}'


Si no recuerdas el nombre exacto de la base de datos, usa:

docker ps --format "{{.Names}}"


📸 Saca captura de los resultados.

Idealmente debe aparecer:

BACKEND VENTAS -> RestartPolicy: always
BACKEND DESPACHOS -> RestartPolicy: always
BASE DE DATOS -> RestartPolicy: always

2. EC2 BACKEND — mostrar Dockerfile Ventas

Ve al proyecto:

cd ~/proyecto


Confirma la ruta:

pwd


Luego:

echo "=========================================="
echo "DOCKERFILE BACKEND VENTAS"
echo "=========================================="
cat back-Ventas_SpringBoot/Springboot-API-REST/Dockerfile


📸 Saca captura completa.

3. EC2 BACKEND — mostrar Dockerfile Despachos

Sin salir de ~/proyecto, ejecuta:

echo "=========================================="
echo "DOCKERFILE BACKEND DESPACHOS"
echo "=========================================="
cat back-Despachos_SpringBoot/Springboot-API-REST-DESPACHO/Dockerfile


📸 Saca captura completa.

Si te aparece:

No such file or directory


ejecuta:

find . -type f -name "Dockerfile" -print


y me mandas el resultado.

4. EC2 BACKEND — mostrar .dockerignore

Ejecuta:

find . -type f -name ".dockerignore" -print


Después puedes mostrar todos los .dockerignore encontrados con:

find . -type f -name ".dockerignore" -exec sh -c 'echo "===== $1 ====="; cat "$1"; echo' _ {} \;


📸 Saca captura.

Si no aparece ningún archivo, significa que todavía no tienes .dockerignore en los backends.

5. EC2 BACKEND — evidencia de APIs funcionando

Ejecuta:

echo "=========================================="
echo "API VENTAS"
echo "=========================================="
curl -i http://localhost:8082/api/v1/ventas


Luego:

echo "=========================================="
echo "API DESPACHOS"
echo "=========================================="
curl -i http://localhost:8081/api/v1/despachos


Debe aparecer:

HTTP/1.1 200


📸 Puedes sacar una sola captura con ambas APIs.

6. EC2 BACKEND — logs sin errores recientes

Ejecuta:

echo "=========================================="
echo "ERRORES BACKEND VENTAS"
echo "=========================================="

docker logs --since 15m backend_ventas 2>&1 | grep -E "ERROR|Exception|Caused by" || echo "SIN ERRORES RECIENTES"


Después:

echo "=========================================="
echo "ERRORES BACKEND DESPACHOS"
echo "=========================================="

docker logs --since 15m backend_despachos 2>&1 | grep -E "ERROR|Exception|Caused by" || echo "SIN ERRORES RECIENTES"


📸 Saca captura si aparece SIN ERRORES RECIENTES en ambos.

7. EC2 FRONTEND — 54.165.12.94

Ahora entra a la instancia del frontend.

Ejecuta:

docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}"


Luego:

docker inspect frontend_despacho \
--format 'FRONTEND DESPACHO -> RestartPolicy: {{.HostConfig.RestartPolicy.Name}}'


Debe aparecer:

FRONTEND DESPACHO -> RestartPolicy: always


📸 Saca captura.

8. EC2 FRONTEND — Dockerfile multietapa

Ve al proyecto:

cd ~/proyectoInnovatech/front_despacho


Confirma:

pwd


Ahora:

echo "=========================================="
echo "DOCKERFILE FRONTEND"
echo "=========================================="
cat Dockerfile


📸 Saca captura completa.

Deberíamos ver algo parecido a:

FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

RUN npm run build

FROM nginx:alpine-slim

COPY --from=builder /app/dist /usr/share/nginx/html


Eso demuestra la estrategia multietapa.

9. EC2 FRONTEND — .dockerignore

Ejecuta:

echo "=========================================="
echo "DOCKERIGNORE FRONTEND"
echo "=========================================="

cat .dockerignore


Si dice:

No such file or directory


créalo con:

cat > .dockerignore <<'EOF'
node_modules
dist
.git
.github
.env
.env.*
npm-debug.log*
Dockerfile*
README.md
EOF


Comprueba:

cat .dockerignore


📸 Saca captura.

10. EC2 FRONTEND — evidencia de Nginx y APIs

Ejecuta:

docker logs frontend_despacho --tail 50


Para mostrar solo las APIs:

docker logs frontend_despacho 2>&1 | grep -E "ventas-api|despachos-api" | tail -20


Idealmente aparecerá:

GET /ventas-api/api/v1/ventas HTTP/1.1" 200
GET /despachos-api/api/v1/despachos HTTP/1.1" 200


📸 Saca captura.

11. AWS CLI — evidencia de autenticación

En la EC2 backend ejecuta:

aws sts get-caller-identity


📸 Saca captura, pero para el informe puedes tapar parcialmente el Account ID.

Después:

aws eks list-clusters --region us-east-1


En tu caso debería mostrar:

{
    "clusters": []
}


Esto demuestra que no existe un clúster EKS creado en la cuenta del laboratorio.

12. Evidencia de la restricción IAM de AWS Academy

Ejecuta:

aws ecr describe-repositories \
--region us-east-1 \
--query 'repositories[*].[repositoryName,repositoryUri]' \
--output table


En tu laboratorio ya sabemos que aparece:

AccessDeniedException


y:

explicit deny in an identity-based policy


📸 Saca captura.

No necesitas repetir create-route ni modificar la VPC otra vez.

Haz ahora estos 4 primero

En la EC2 Backend, copia este bloque completo:

echo "========== CONTENEDORES =========="
docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}"

echo
echo "========== RESTART POLICY =========="

docker inspect backend_ventas \
--format 'BACKEND VENTAS -> {{.HostConfig.RestartPolicy.Name}}'

docker inspect backend_despachos \
--format 'BACKEND DESPACHOS -> {{.HostConfig.RestartPolicy.Name}}'

echo
echo "========== API VENTAS =========="
curl -s -o /dev/null -w "HTTP VENTAS: %{http_code}\n" \
http://localhost:8082/api/v1/ventas

echo
echo "========== API DESPACHOS =========="
curl -s -o /dev/null -w "HTTP DESPACHOS: %{http_code}\n" \
http://localhost:8081/api/v1/despachos
