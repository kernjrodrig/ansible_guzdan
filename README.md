# Playbooks Ansible para Gestión de Certificados Ingress en OpenShift

Este repositorio contiene playbooks de Ansible para automatizar la extracción, verificación, respaldo y reemplazo de certificados TLS utilizados por el Ingress Controller en clústeres OpenShift 4.x.

## Estructura de los archivos principales

- [`certificate/login-get-certifi.yml`](certificate/login-get-certifi.yml):  
  Playbook principal. Procesa todos los clústeres definidos en `vars.yml`, extrayendo y verificando el certificado TLS de Ingress. Si el certificado está próximo a expirar, ejecuta el reemplazo automático.

- [`certificate/vars.yml`](certificate/vars.yml):  
  Variables de configuración de los clústeres, incluyendo endpoint, token, nombre del secreto, archivos de certificados, etc.

- [`certificate/extract_cert.yml`](certificate/extract_cert.yml):  
  Tareas para extraer el secreto TLS, guardar el certificado en un archivo temporal y calcular los días restantes hasta su expiración.

- [`certificate/replace.yml`](certificate/replace.yml):  
  Tareas para reemplazar el certificado TLS en el secreto, respaldar el Ingress Controller y actualizarlo para usar el nuevo secreto.

- Certificados de ejemplo:  
  - `ingress-10-cert.pem`, `ingress-10-key.pem`, `ingress-15.crt`, `ingress-15.key`, `ingress-61-cert.pem`, `ingress-61-key.pem`, etc.

## ¿Qué hace el flujo principal?

1. **Extrae el secreto TLS** del Ingress Controller de cada clúster.
2. **Verifica la fecha de expiración** del certificado.
3. **Si faltan menos de 60 días para expirar**, ejecuta el reemplazo:
   - Crea/actualiza el secreto TLS con los nuevos archivos de certificado y llave.
   - Respalda la configuración actual del Ingress Controller.
   - Actualiza el Ingress Controller para usar el nuevo secreto.
   - Muestra el dominio del clúster para verificación.

## Ejecución

1. **Edita [`certificate/vars.yml`](certificate/vars.yml)**  
   Agrega tus clústeres, tokens y rutas de certificados/llaves.

2. **Ejecuta el playbook principal:**
   ```sh
   ansible-playbook certificate/login-get-certifi.yml
   ```

3. **Revisa los mensajes de salida** para verificar si se realizó el reemplazo y el estado de los certificados.

## Requisitos

- Ansible 2.9+
- Colecciones:
  - `kubernetes.core`
  - `community.crypto`
- Acceso API a los clústeres OpenShift y permisos para gestionar secretos e IngressController.

## Notas

- Los certificados y llaves pueden estar en formato `.pem` o `.crt`/`.key`, siempre que el contenido sea válido.
- Los backups del Ingress Controller se guardan en archivos YAML para recuperación manual si es necesario.
- Puedes personalizar los umbrales de expiración y rutas de archivos en los archivos de variables.

---

**Autor:**  
Equipo de Automatización Guzdan Service by:Javo  
Abril 2025
