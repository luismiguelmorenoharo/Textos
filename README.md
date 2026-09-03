### **1. Investigación y Definición del Tag (Fase 2 - Diseño / Concepción)**

#### **¿A quién contactar?**

En la transcripción con Steve se mencionan explícitamente dos contactos para el tema de infraestructura/tags:

* **El equipo de Azure Infra / Atlantik.**
* Para la creación/coordinación previa con VDI/Citrix, Steve mencionó a **Brahim** (para ver si se pueden etiquetar las VMs al momento de la creación).

#### **¿Cómo preparar la propuesta para llegar con la idea clara?**

Para no llegar con las manos vacías, prepara un documento corto o correo de 1 página con esta estructura técnica basada estrictamente en la política de Azure:

1. **El objetivo técnico:** Explicar que la política built-in de Defender for Servers (`080fedce-9d4a-4d07-abf0-9f036afbc9c8`) requiere evaluar una clave (`inclusionTagName`) y uno o varios valores (`inclusionTagValues`) para deshabilitar la protección a nivel de recurso.
2. **Propuesta de Clave/Valor:**
* **Nombre de la etiqueta (*Tag Name*):** Propón `Environment` o `Workload` o `OsType` (Steve sugirió `workstation`).
* **Valor de la etiqueta (*Tag Value*):** Propón `Workstation` o `Windows11`.


3. **Preguntas puntuales para el equipo de Infra:**
* *"¿Existe ya una convención de Tags oficial en la empresa para identificar el tipo de SO o Workstation?"*
* *"¿Quién es el dueño del gobierno de etiquetas en las suscripciones de Azure?"*



---

### **2. Implementación de la Azure Policy de Exclusión (Fase 3 - PoC / Despliegue)**

#### **¿Qué hacer si el Director/Contacto no responde sobre la VM Windows 11?**

1. **Escala suavemente vía Steve / Philippe:** No envíes un cuarto mensaje directo. En el Grooming o por chat a Steve/Philippe, menciona: *"Tengo bloqueada la creación de la VM Windows 11 por catálogo Terraform. Le escribí a [Nombre del Director], pero está ocupado. ¿Me pueden indicar con qué persona de su equipo puedo coordinar para no interrumpirlo a él?"*
2. **Usa el Portal o az CLI temporalmente en `NPR-3505-MDE`:** Si tienes permisos de creación de recursos en tu suscripción de pruebas `NPR-3505-MDE`, no necesitas esperar a Terraform ni al catálogo *golden* para hacer la PoC. Puedes crear una VM Windows 11 directamente por el portal de Azure o mediante `az vm create`.

#### **¿Cómo funciona exactamente un Tag y la Azure Policy?**

* **¿Qué es un Tag?** Es un par Clave:Valor asignado a un recurso de Azure (ej. `OsType : Workstation`). Se puede ver en la sección **Tags** del recurso en el Portal, agregarlo en la plantilla/código Terraform (`tags = { OsType = "Workstation" }`), o mediante Azure CLI (`az tag update`).
* **¿Cómo funciona la Policy `080fedce-9d4a-4d07-abf0-9f036afbc9c8`?**
* Configuras la política pasándole dos parámetros: `inclusionTagName` (ej. `OsType`) e `inclusionTagValues` (ej. `["Workstation"]`).
* La política evalúa las VMs de la suscripción. Si la VM **tiene ese Tag**, la política ejecuta una acción de tipo `DeployIfNotExists` que pone el plan de Defender for Servers en **Free** (deshabilitado) únicamente para ese recurso.



#### **¿Puedo validar con una VM que NO sea Windows 11 por el momento?**

**Sí, absolutamente.**

* **Técnicamente:** La política `080fedce-9d4a-4d07-abf0-9f036afbc9c8` solo revisa si el **Tag** está presente en el recurso; no valida el sistema operativo internamente.
* **Declaración de Steve:** Steve te pidió probar si Defender Server se instala o no según el Tag. Puedes desplegar cualquier VM disponible en tu catálogo, asignarle el Tag, verificar que la Policy la ponga en *Free*, y luego repetir la validación cuando tengas la VM Windows 11 final.

---

### **3. Estrategia de Tagueo Automático (Fase 3)**

#### **¿Qué es el mecanismo / script de tagueo automático?**

Significa que no debes taguear manualmente cada VM que se cree en el futuro. Existen dos opciones técnicas estándar en Azure:

1. **Opción A (Azure Policy de Tagging Automático):** Una política de tipo `Modify` o `Require Tag` que detecta la creación de la VM y le aplica la etiqueta según el campo de la oferta de la imagen (`Microsoft.Compute/virtualMachines/storageProfile.imageReference.offer`).
2. **Opción B (Script de Automatización / PowerShell / CLI):** Un script que recorre las VMs en las suscripciones, consulta la propiedad de la imagen/SO y ejecuta `az tag update` para aplicar el Tag a las VMs existentes.

---

### **Desglose Estructurado de Tareas (Sin Asunciones)**

| # | Tarea / Subtarea | Acción Concreta | Herramienta / Método |
| --- | --- | --- | --- |
| **2.1** | **Propuesta de Tagging** | Redactar propuesta de `TagName` y `TagValue` basada en el parámetro que requiere la Policy `080fedce-9d4a-4d07-abf0-9f036afbc9c8`. | Documento corto / Correo |
| **2.2** | **Alineación con Infra/Brahim** | Validar la convención con Azure Infra / Atlantik / Brahim. | Reunión / Chat |
| **3.1** | **PoC de Policy en `NPR-3505-MDE**` | Asignar la Policy `080fedce-9d4a-4d07-abf0-9f036afbc9c8` en tu suscripción definiendo los parámetros del Tag. | Portal Azure / `az policy assignment` |
| **3.2** | **Despliegue de VM de Prueba** | Crear una VM disponible (o Windows 11 vía portal/CLI) con el Tag asignado. | Portal / `az vm create` / Terraform |
| **3.3** | **Validación de Exclusión** | Ejecutar escaneo de cumplimiento (`az policy state trigger-scan`) y verificar que el plan de Defender pase a *Free*. | Portal Defender for Cloud |
| **3.4** | **Diseño del Tagueo Automático** | Evaluar la opción de Azure Policy (tipo `Modify`) vs Script para aplicar el Tag masivamente según el SO/oferta de la VM. | Azure Policy / Script CLI |
