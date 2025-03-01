# Explicación del Funcionamiento del Código

Este código en Liquid genera dinámicamente botones de contacto de WhatsApp y teléfono en función de la categoría del producto en la tienda de Jumpseller. Se configura mediante un archivo JSON que define las categorías, números de contacto y opciones personalizadas.

---

## 1. Configuración en `product_whatsapp_category_random.json`

El archivo JSON define las opciones configurables en Jumpseller:

- **Categoría prioritaria** con su número de WhatsApp y teléfono.
- **Categoría comercial** que distribuye aleatoriamente entre asesores comerciales.
- **Otras categorías dinámicas** (hasta 3 por defecto), cada una con su propio número de WhatsApp y teléfono.

Ejemplo de configuración:

```json
{
  "product_whatsapp_priority_category": "Categoría VIP",
  "product_whatsapp_priority_number": "573104265599",
  "product_phone_priority_number": "3104265599",
  "product_whatsapp_priority_text": "Solicitar presupuesto por WhatsApp",
  "product_whatsapp_commercial_category": "Categoría Comercial",
  "product_whatsapp_commercial_number_1": "573028301499",
  "product_phone_commercial_number_1": "3028301499"
}
```

---

## 2. Funcionamiento del Código Liquid (`.liquid`)

El código recorre las categorías del producto para encontrar coincidencias y asignar el número de WhatsApp adecuado.

### Paso a paso del código:

1. **Inicialización de variables**: Se definen variables vacías para almacenar los datos de WhatsApp y teléfono.
   ```liquid
   {% assign whatsapp_data = "" %}
   {% assign button_text = "Comprar por WhatsApp" %}
   {% assign category_matched = false %}
   {% assign tracking_class = "category-default" %}
   ```

2. **Selección aleatoria de asesores comerciales con `localStorage`**: Se verifica si ya hay un asesor guardado en `localStorage`, de lo contrario, se selecciona uno aleatoriamente.
   ```javascript
   document.addEventListener("DOMContentLoaded", function() {
       let asesores = ["{{ options.product_whatsapp_commercial_number_1 }}", "{{ options.product_whatsapp_commercial_number_2 }}"];
       let asesorSeleccionado = localStorage.getItem("asesor_comercial");
       
       if (!asesorSeleccionado || isNaN(parseInt(asesorSeleccionado))) {
           asesorSeleccionado = Math.floor(Math.random() * asesores.length);
           localStorage.setItem("asesor_comercial", asesorSeleccionado);
       } else {
           asesorSeleccionado = parseInt(asesorSeleccionado);
       }
   });
   ```

3. **Verificación de categoría prioritaria**: Si el producto pertenece a esta categoría, se asignan sus datos de contacto.
   ```liquid
   {% if category_permalink == options.product_whatsapp_priority_category %}
     {% assign whatsapp_data = options.product_whatsapp_priority_number | append: "," | append: options.product_phone_priority_number %}
     {% assign button_text = options.product_whatsapp_priority_text %}
     {% assign category_matched = true %}
     {% assign tracking_class = "category-prioritaria" %}
   {% endif %}
   ```

4. **Verificación de categoría comercial**: Si el producto está en esta categoría, se asignan los datos del asesor comercial seleccionado en `localStorage`.
   ```liquid
   {% if category_permalink == options.product_whatsapp_commercial_category %}
     {% assign category_matched = true %}
     {% assign tracking_class = "category-comercial" %}
   {% endif %}
   ```

5. **Búsqueda en las categorías dinámicas**: Se recorren hasta `product_whatsapp_categories_count` categorías configuradas en el JSON.
   ```liquid
   {% for i in (1..category_count) %}
     {% capture category_key %}product_whatsapp_category_{{ i }}{% endcapture %}
     {% capture whatsapp_key %}product_whatsapp_number_{{ i }}{% endcapture %}
     {% capture phone_key %}product_phone_number_{{ i }}{% endcapture %}

     {% assign category_value = options[category_key] %}
     {% assign whatsapp_value = options[whatsapp_key] %}
     {% assign phone_value = options[phone_key] %}

     {% if category_permalink == category_value %}
       {% assign whatsapp_data = whatsapp_value | append: "," | append: phone_value %}
       {% assign category_matched = true %}
     {% endif %}
   {% endfor %}
   ```

6. **Asignación por defecto**: Si no hay coincidencia con ninguna categoría, se usa el primer asesor comercial.
   ```liquid
   {% unless category_matched %}
     {% assign whatsapp_data = options.product_whatsapp_commercial_number_1 | append: "," | append: options.product_phone_commercial_number_1 %}
   {% endunless %}
   ```

7. **Generación de los botones de WhatsApp y Teléfono**: Se crean enlaces con los números de contacto.
   ```liquid
   <a class="track-whatsapp" href="https://api.whatsapp.com/send?phone={{ whatsapp_details[0] }}">
     {{ button_text }} <i class="fab fa-whatsapp"></i>
   </a>
   <a class="track-phone" href="tel:{{ whatsapp_details[1] }}">
     <i class="fas fa-phone"></i> Llamar {{ whatsapp_details[1] }}
   </a>
   ```

---

## 3. Mejoras Implementadas

### 3.1. Manejo de `localStorage`
- Se agregó verificación para evitar errores cuando `localStorage` está deshabilitado.
- Se maneja `QuotaExceededError` en caso de que el almacenamiento esté lleno.
- Se validan los datos antes de leerlos de `localStorage`.

### 3.2. Soporte para Múltiples Productos en una Misma Página
- Se reemplazaron los `id` por `class` en los botones de WhatsApp y Teléfono.
- Se usó `querySelectorAll()` en lugar de `getElementById()` para evitar conflictos.
- Se agregaron atributos `data` para manejar cada producto de forma independiente.

### 3.3. Opción para Ocultar el Botón de Llamada sin Afectar la Lógica
- Se agregó la opción de ocultar el botón de llamada con CSS.

---

## 4. Conclusión

Este código optimizado permite mostrar botones de WhatsApp y Teléfono dinámicos según la categoría del producto en Jumpseller, con mejoras que garantizan estabilidad y compatibilidad con escenarios avanzados, como páginas con múltiples productos.

Si necesitas personalizarlo aún más, puedes modificar las opciones en el JSON o ajustar las clases CSS y el JavaScript según tus necesidades. 🚀

