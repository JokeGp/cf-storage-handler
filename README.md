# Cloud Function – Storage Upload Logger (GCP)

## 📌 Descripción
Este proyecto implementa una **Cloud Function de segunda generación en Google Cloud Platform**
que se activa automáticamente cuando un archivo es subido a un bucket de **Cloud Storage**.

La función extrae metadatos del archivo y los registra en **Cloud Logging**, simulando
un escenario real de automatización y observabilidad en un entorno productivo.

---

## 🏗️ Arquitectura

Flujo de trabajo:

1. Usuario sube un archivo a Cloud Storage
2. Eventarc detecta el evento `object.finalized`
3. Se activa la Cloud Function
4. Se validan los datos del evento
5. Se registran los metadatos en Cloud Logging

```text
[User]
   |
   v
[Cloud Storage Bucket]
   |
   v
[Eventarc Trigger]
   |
   v
[Cloud Function (Node.js)]
   |
   v
[Cloud Logging]

🔐 Seguridad y Control de Accesos

Se utiliza una Service Account dedicada

Principio de mínimo privilegio

La Cloud Function NO usa credenciales de usuario

Accesos controlados mediante IAM

Roles asignados:

roles/storage.objectViewer

roles/logging.logWriter

roles/eventarc.eventReceiver

🗂️ Configuración del Bucket

Bucket: turing-evaluacion-gcp-files

Reglas de ciclo de vida (Lifecycle):

Eliminación automática después de X días

Acceso restringido por IAM

⚙️ Cloud Function

Runtime: Node.js 20

Región: us-central1

Trigger: Cloud Storage (object.finalized)

Memoria: 256 MB

Timeout: 60s

Metadatos capturados:

Nombre del archivo

Bucket

Tamaño

Tipo MIME

Fecha de creación

Event ID

🧪 Pruebas Unitarias

Se implementaron pruebas unitarias básicas sin frameworks externos
para validar:

✔ Flujo exitoso
✔ Manejo de errores por payload inválido

Ejecutar pruebas:

npm test


Salida esperada:

Logs del flujo exitoso

Error controlado para eventos inválidos

🚀 Despliegue
gcloud functions deploy onFileUpload \
  --gen2 \
  --runtime=nodejs20 \
  --region=us-central1 \
  --source=. \
  --entry-point=onFileUpload \
  --trigger-bucket=turing-evaluacion-gcp-files \
  --service-account=cf-storage-sa@turing-evaluacion-gcp.iam.gserviceaccount.com

📊 Observabilidad

Los logs pueden consultarse con:

gcloud functions logs read onFileUpload --gen2 --region=us-central1

🧠 Decisiones Técnicas

Separación de la lógica para permitir pruebas unitarias

Logging estructurado para facilitar análisis en Cloud Logging

Validación temprana del payload

Uso de Service Account dedicada

Cloud Functions Gen2 para mejor escalabilidad

📎 Autor

José Enrique Guerrero Pérez
Proyecto de evaluación – GCP