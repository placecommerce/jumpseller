**Documentación: Implementación de Multi WhatsApp en Jumpseller**

## **Introducción**

Esta documentación explica cómo implementar y configurar **Multi WhatsApp** en un tema de Jumpseller utilizando `options.json` y Liquid. Con esta implementación, los asesores de ventas o soporte pueden ser administrados desde el editor del tema sin modificar el código.

---

## 1. Configuración en `options.json`

En el archivo `options.json`, agregamos las opciones para definir los asesores y sus números de contacto.

```json
{
  "Chats": {
    "icon": "chat",
    "options": {
      "multi_whatsapp_heading": {
        "name": "\ud83d\udd39 Configuración: Multi WhatsApp",
        "type": "heading",
        "default": true
      },
      "multi_whatsapp_asesor_1": {
        "name": "Nombre Asesor 1",
        "type": "input",
        "default": "Asesor 1 Bogotá - Sede Normandía"
      },
      "multi_whatsapp_asesor_1_number": {
        "name": "Número WhatsApp Asesor 1",
        "type": "input",
        "default": "573028301499"
      },
      "multi_whatsapp_asesor_2": {
        "name": "Nombre Asesor 2",
        "type": "input",
        "default": "Asesor 2 Bogotá - Sede Ricaurte"
      },
      "multi_whatsapp_asesor_2_number": {
        "name": "Número WhatsApp Asesor 2",
        "type": "input",
        "default": "573107779621"
      },
      "multi_whatsapp_asesor_3": {
        "name": "Nombre Asesor 3",
        "type": "input",
        "default": "Asesor Medellín - Sede Belén"
      },
      "multi_whatsapp_asesor_3_number": {
        "name": "Número WhatsApp Asesor 3",
        "type": "input",
        "default": "573237070353"
      }
    }
  }
}
```

### **Explicación del JSON:**

- \`\`: Sección para agrupar los campos relacionados con Multi WhatsApp.
- **Cada asesor tiene dos entradas:**
  - `multi_whatsapp_asesor_X`: Nombre del asesor.
  - `multi_whatsapp_asesor_X_number`: Número de WhatsApp.
- **Valores predeterminados:** Definidos para una configuración inicial, pero pueden ser cambiados desde el editor del tema.

---

## **2. Implementación en Liquid**

Este código genera dinámicamente los botones de WhatsApp para los asesores configurados en `options.json`.

```liquid
<div class='sticky-chat'>
  <div class='chat-content'>
    <div class='chat-header'>
      <svg viewbox='0 0 32 32'><path d='...' ></path></svg>
      <div class='title'>Escríbenos ahora! <span>¿Con cuál de nuestras áreas quieres hablar?</span></div>
    </div>
    <div class='chat-text'>
      
      {% assign asesores = "1,2,3" | split: "," %}
      {% for asesor in asesores %}
        {% assign name_key = "multi_whatsapp_asesor_" | append: asesor %}
        {% assign number_key = "multi_whatsapp_asesor_" | append: asesor | append: "_number" %}
        
        {% if options[name_key] != "" and options[number_key] != "" %}
          <a class='chat-button contiene btn-wtp-{{ asesor }}' href="https://api.whatsapp.com/send?phone={{ options[number_key] }}&text={{ 'Hola Arrowti3D, quiero más información de sus productos!' | t }}" rel='nofollow noreferrer' target='_blank'>
            <span>{{ options[name_key] }}</span>
            <svg viewBox='0 0 32 32'><path class='cls-1' style="fill: #4dc247;" d='...'></path></svg>
          </a>
        {% endif %}
      {% endfor %}

    </div>
  </div>
</div>
```

### **Explicación del código Liquid:**

1. \`\`: Lista de asesores disponibles. Solo cambia esta línea si se agregan más asesores en `options.json`.
2. \`\`: Itera sobre los asesores disponibles.
3. `** y **`: Construyen dinámicamente las claves para obtener los valores desde `options.json`.
4. \`\`: Solo muestra el asesor si tiene nombre y número.
5. **Generación automática de enlaces de WhatsApp**, evitando código repetitivo.

---

## **3. ¿Cómo agregar otro asesor?**

### ✅ Paso 1: Agregar en `options.json`

```json
"multi_whatsapp_asesor_4": {
  "name": "Nombre Asesor 4",
  "type": "input",
  "default": "Asesor Cali - Sede Norte"
},
"multi_whatsapp_asesor_4_number": {
  "name": "Número WhatsApp Asesor 4",
  "type": "input",
  "default": "573001234567"
}
```

### ✅ **Paso 2: Modificar la lista en Liquid**

```liquid
{% assign asesores = "1,2,3,4" | split: "," %}
```

**¡Listo!** 🎉 

---

## **4. Beneficios de esta implementación**

✅ **Fácil mantenimiento:** Solo modificas `options.json` y la lista en Liquid.\
✅ **Código más limpio:** Menos repetición gracias a la generación dinámica.\
✅ **Mayor flexibilidad:** Se pueden agregar más asesores sin modificar el código base.

---
