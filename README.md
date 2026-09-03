Actúa como un Desarrollador/Ingeniero Senior en Azure. Necesito tu ayuda para implementar un PoC de exclusión de Defender for Servers a nivel de recurso mediante Tags en Azure Policy.

Contexto del proyecto:
- Entorno de prueba: Suscripción Azure NPR-3505-MDE.
- Objetivo: Evitar que el plan de Microsoft Defender for Servers (Standard) se aplique a máquinas virtuales de tipo Workstation / VDI (ej. Windows 11), cambiando su pricing tier a "Free" de forma individualizada.
- Mecanismo: Usar la Azure Policy built-in con ID: 080fedce-9d4a-4d07-abf0-9f036afbc9c8 ("Configure Azure Defender for Servers to be disabled for resources (resource level) with the selected tag").

Necesito que trabajemos paso a paso en los siguientes puntos técnicos:

1. Asignación de la Policy vía Azure CLI / PowerShell:
   - Proporcióname los comandos exactos para asignar la política 080fedce-9d4a-4d07-abf0-9f036afbc9c8 en la suscripción NPR-3505-MDE.
   - Pasa los parámetros `inclusionTagName` (usaremos temporalmente "workstation") e `inclusionTagValues` (usaremos ["true"]).

2. Etiquetado y Despliegue de VM de Prueba:
   - Comandos para crear o etiquetar una VM existente en Azure CLI usando el par "workstation=true".

3. Evaluación de Cumplimiento y Verificación:
   - Comando para forzar la evaluación de la política (`az policy state trigger-scan`).
   - Consulta CLI/REST API para verificar si el estado de `Microsoft.Security/pricings` para esa VM específica cambió exitosamente a "Free".

4. Propuesta de Automatización de Tagging:
   - Dame una estructura básica de un script (PowerShell o Azure CLI) que filtre VMs por su propiedad de SO/imagen (Windows 11) y les aplique el tag de forma automática.

Por favor, dame primero el punto 1 y 2 con los comandos limpios y listos para ejecutar.
