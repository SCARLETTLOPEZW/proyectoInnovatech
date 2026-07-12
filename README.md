1. EC2 BACKEND — 98.94.39.79

Primero entra a la instancia servidor app, donde tienes Ventas, Despachos y la base de datos.

Ver contenedores activos
docker ps


Luego:

docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}"


<img width="956" height="142" alt="image" src="https://github.com/user-attachments/assets/a73b2fc8-09ca-4d94-b27f-71e2df429075" />


Ver política de reinicio de los contenedores

Copia y pega:

docker inspect backend_ventas \
--format 'BACKEND VENTAS -> RestartPolicy: {{.HostConfig.RestartPolicy.Name}}'

<img width="736" height="87" alt="image" src="https://github.com/user-attachments/assets/9083b84e-8238-4276-9b89-d62323be8288" />

Después:

docker inspect backend_despachos \
--format 'BACKEND DESPACHOS -> RestartPolicy: {{.HostConfig.RestartPolicy.Name}}'

<img width="752" height="92" alt="image" src="https://github.com/user-attachments/assets/55760e75-03b6-45b7-8e99-5a974ad17825" />

Para la base de datos:

docker inspect base_datos_proyectos \
--format 'BASE DE DATOS -> RestartPolicy: {{.HostConfig.RestartPolicy.Name}}'

<img width="708" height="80" alt="image" src="https://github.com/user-attachments/assets/f929f2cd-2920-47f1-83c4-2f66dafec666" />

Si no recuerdas el nombre exacto de la base de datos, usa:

docker ps --format "{{.Names}}"

<img width="552" height="100" alt="image" src="https://github.com/user-attachments/assets/44486e17-0597-4636-b950-e2b05d4b8bbf" />



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


<img width="557" height="617" alt="image" src="https://github.com/user-attachments/assets/781b0a0d-1910-4df9-9785-a5746baf7e6e" />


3. EC2 BACKEND — mostrar Dockerfile Despachos

Sin salir de ~/proyecto, ejecuta:

echo "=========================================="
echo "DOCKERFILE BACKEND DESPACHOS"
echo "=========================================="
cat back-Despachos_SpringBoot/Springboot-API-REST-DESPACHO/Dockerfile


<img width="958" height="507" alt="image" src="https://github.com/user-attachments/assets/4aae0c61-c8f9-4e23-8225-64e091b659d6" />


ejecuta:

find . -type f -name "Dockerfile" -print


<img width="752" height="163" alt="image" src="https://github.com/user-attachments/assets/0398ba88-54f9-4f3d-919c-a77d08e781d4" />


4. EC2 BACKEND — mostrar .dockerignore

Ejecuta:

find . -type f -name ".dockerignore" -print
<img width="736" height="200" alt="image" src="https://github.com/user-attachments/assets/275234b6-b168-4be0-8702-4ee079d1d8d3" />


Después puedes mostrar todos los .dockerignore encontrados con:

find . -type f -name ".dockerignore" -exec sh -c 'echo "===== $1 ====="; cat "$1"; echo' _ {} \;


[ec2-user@ip-10-0-3-2 proyecto]$ find . -type f -name ".dockerignore" -exec sh -c 'echo "===== $1 ====="; cat "$1"; echo' _ {} \;
===== ./back-Despachos_SpringBoot/Springboot-API-REST-DESPACHO/.dockerignore =====
# Ignora archivos de configuración locales
.env
*.iml
.idea/
*.log

# Ignora dependencias locales
target/
.mvn/
.settings/

# Ignora control de versiones y sistema de build
.git/
.gitignore
docker-compose.override.yml
../../Docker-compose.yml

# Ignora archivos temporales
*.swp
*~
===== ./back-Ventas_SpringBoot/Springboot-API-REST/.dockerignore =====
# Ignora archivos de configuración locales
.env
*.iml
.idea/
*.log

# Ignora dependencias locales
target/
.mvn/
.settings/

# Ignora control de versiones y sistema de build
.git/
.gitignore
docker-compose.override.yml
../../back-Despachos_SpringBoot/Dockerfile

# Ignora archivos temporales
*.swp
*~
===== ./front_despacho/.dockerignore =====
node_modules
dist
.git
.gitignore
.env
.env.local
*.log
README.md



5. EC2 BACKEND — evidencia de APIs funcionando

Ejecuta:

echo "=========================================="
echo "API VENTAS"
echo "=========================================="
curl -i http://localhost:8082/api/v1/ventas
<img width="937" height="317" alt="image" src="https://github.com/user-attachments/assets/28d6abba-bff8-48ca-9fa1-e4ad65611f96" />


Luego:

echo "=========================================="
echo "API DESPACHOS"
echo "=========================================="
curl -i http://localhost:8081/api/v1/despachos


Debe aparecer:

HTTP/1.1 200


<img width="953" height="646" alt="image" src="https://github.com/user-attachments/assets/306cdded-29aa-4676-bd4d-555690431a55" />


6. EC2 BACKEND — logs sin errores recientes

Ejecuta:

echo "=========================================="
echo "ERRORES BACKEND VENTAS"
echo "=========================================="

docker logs --since 15m backend_ventas 2>&1 | grep -E "ERROR|Exception|Caused by" || echo "SIN ERRORES RECIENTES"
<img width="947" height="567" alt="image" src="https://github.com/user-attachments/assets/8e9e697f-7d17-4ee3-9ffa-725fae4670d3" />


Después:

echo "=========================================="
echo "ERRORES BACKEND DESPACHOS"
echo "=========================================="

docker logs --since 15m backend_despachos 2>&1 | grep -E "ERROR|Exception|Caused by" || echo "SIN ERRORES RECIENTES"


<img width="941" height="440" alt="image" src="https://github.com/user-attachments/assets/55032db0-bb4b-43f8-b20c-e7dfa7fb2bb2" />


7. EC2 FRONTEND — 54.165.12.94

Ahora entra a la instancia del frontend.

Ejecuta:

docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}"


Luego:

docker inspect frontend_despacho \
--format 'FRONTEND DESPACHO -> RestartPolicy: {{.HostConfig.RestartPolicy.Name}}'
<img width="1917" height="717" alt="image" src="https://github.com/user-attachments/assets/bdc6c712-56a8-4c9d-8730-baea9a2df1f0" />


Debe aparecer:

FRONTEND DESPACHO -> RestartPolicy: always


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


<img width="940" height="593" alt="image" src="https://github.com/user-attachments/assets/5b691e53-960f-48b4-b4fe-83c13f7d5984" />





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


<img width="961" height="588" alt="image" src="https://github.com/user-attachments/assets/b5a3a7aa-176f-4cb3-a7c7-c41cd9430df5" />
<img width="728" height="550" alt="image" src="https://github.com/user-attachments/assets/85d312e1-7400-4aa6-8402-be60e6ddb57a" />
<img width="928" height="387" alt="image" src="https://github.com/user-attachments/assets/bd4104b5-5a30-484c-a1e7-8521eb90b3c9" />


10. EC2 FRONTEND — evidencia de Nginx y APIs

Ejecuta:

docker logs frontend_despacho --tail 50
<img width="1406" height="755" alt="image" src="https://github.com/user-attachments/assets/e814cf5c-fa3d-4ecd-bb3f-0403268cea9a" />


Para mostrar solo las APIs:

docker logs frontend_despacho 2>&1 | grep -E "ventas-api|despachos-api" | tail -20


Idealmente aparecerá:

GET /ventas-api/api/v1/ventas HTTP/1.1" 200
GET /despachos-api/api/v1/despachos HTTP/1.1" 200

<img width="940" height="917" alt="Captura de pantalla 2026-07-11 184444" src="https://github.com/user-attachments/assets/5765cd49-ad4b-4f35-849f-c0913f2364e7" />
<img width="937" height="917" alt="Captura de pantalla 2026-07-11 184459" src="https://github.com/user-attachments/assets/e5ac42ca-855c-46c6-a85e-2d7a0088b3df" />



11. AWS CLI — evidencia de autenticación

En la EC2 backend ejecuta:

aws sts get-caller-identity




Después:

<img width="856" height="171" alt="Captura de pantalla 2026-07-11 210640" src="https://github.com/user-attachments/assets/8611dd0a-54ba-47a8-940a-3c77cec7cbe4" />En tu caso debería mostrar:

{
    "clusters": []
}
<img width="790" height="102" alt="Captura de pantalla 2026-07-11 210802" src="https://github.com/user-attachments/assets/bf420521-6d0c-476c-b1d1-57e7b8b729dc" />


Esto demuestra que no existe un clúster EKS creado en la cuenta del laboratorio.

12. Evidencia de la restricción IAM de AWS Academy

Ejecuta:

aws ecr describe-repositories \
--region us-east-1 \
--query 'repositories[*].[repositoryName,repositoryUri]' \
--output table


En tu laboratorio ya sabemos que aparece:

AccessDeniedException

<img width="795" height="132" alt="image" src="https://github.com/user-attachments/assets/482b2d8b-2a0e-48e8-aaf2-34b7939c615e" />
<img width="1398" height="552" alt="image" src="https://github.com/user-attachments/assets/1bfec143-f2cc-4d4a-9eeb-630c5a87da95" />


y:

explicit deny in an identity-based policy


<img width="1390" height="430" alt="image" src="https://github.com/user-attachments/assets/2e6848fe-94ed-4698-8081-bf937444a2d5" />
<img width="951" height="375" alt="image" src="https://github.com/user-attachments/assets/c456cb03-ca73-4af4-b42a-3b1bc61a8080" />


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
<img width="952" height="633" alt="image" src="https://github.com/user-attachments/assets/5405324a-eea3-4a18-87f8-7da47604032a" />

<img width="366" height="213" alt="Captura de pantalla 2026-07-11 184719" src="https://github.com/user-attachments/assets/f256b458-43e0-477e-bbf0-77bce7777d2f" />
<img width="1896" height="922" alt="Captura de pantalla 2026-07-11 190543" src="https://github.com/user-attachments/assets/452a52e9-d8aa-47bb-ab0a-fffa0f4c1a33" />
<img width="1902" height="1025" alt="Captura de pantalla 2026-07-11 190031" src="https://github.com/user-attachments/assets/eb212a07-b9e7-4388-bc7a-d2320dd2395b" />
<img width="1916" height="1062" alt="Captura de pantalla 2026-07-11 190008" src="https://github.com/user-attachments/assets/d140e60c-df55-4562-aede-fa9ab324c786" />
<img width="1490" height="355" alt="Captura de pantalla 2026-07-11 190004" src="https://github.com/user-attachments/assets/33ae25c2-70ab-4b96-94d6-15cbfc89ab32" />

