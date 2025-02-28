# 📌 WhatsApp por Categoría en Jumpseller

## **📌 Introducción**
Este código permite mostrar botones de contacto por WhatsApp y Teléfono en la página de productos de una tienda en Jumpseller. La asignación de los números de contacto se realiza según la categoría del producto, con opciones de prioridad y asignación automática de asesores comerciales.

## **📌 Requisitos Previos**
1. **Acceso a la edición de Liquid y JSON en Jumpseller.**
2. **Tener configuradas las categorías en la tienda.**
3. (Opcional) **Google Tag Manager**, si se desea medir los clics en los botones.

## **📌 Archivos y su Función**
### `Json_whatsapp_category.json`
- Define las opciones configurables en el panel de administración de Jumpseller.
- Permite asignar categorías y números de contacto dinámicamente.

### `product-whatsapp-category-random.liquid`
- Renderiza los botones de WhatsApp y Teléfono en la página de productos.
- Asigna los números según la categoría del producto.
- Selecciona asesores comerciales de forma aleatoria.
- Implementa un `fallback` en caso de que el producto no tenga una categoría asignada.

---

## **📌 Configuración en `product-whatsapp-category-random.json`**
Cada opción dentro del JSON permite personalizar los números de contacto y categorías. Aquí está una descripción de las opciones más importantes:

| **Opción** | **Descripción** |
|------------|---------------|
| `product_whatsapp_priority_category` | Categoría prioritaria que tiene preferencia sobre otras. |
| `product_whatsapp_priority_number` | Número de WhatsApp para la categoría prioritaria. |
| `product_phone_priority_number` | Número de teléfono para la categoría prioritaria. |
| `product_whatsapp_priority_text` | Texto del botón para la categoría prioritaria. |
| `product_whatsapp_commercial_category` | Categoría donde los asesores se asignan de forma aleatoria. |
| `product_whatsapp_commercial_number_1` y `_2` | Números de WhatsApp para los asesores comerciales. |
| `product_phone_commercial_number_1` y `_2` | Números de Teléfono para los asesores comerciales. |
| `product_whatsapp_categories_count` | Número total de categorías asignadas manualmente. |
| `product_whatsapp_category_X` | Categoría asignada manualmente (1,2,3...). |
| `product_whatsapp_number_X` | Número de WhatsApp para la categoría manual. |
| `product_phone_number_X` | Número de teléfono para la categoría manual. |

### **🔹 Agregar Más Categorías**
Para agregar una nueva categoría manualmente:
1. **Aumentar el valor de `product_whatsapp_categories_count` en `product-whatsapp-category-random.json`.**
2. **Agregar nuevas opciones en el JSON:**
```json
"product_whatsapp_category_4": {
  "name": "Categoría 4",
  "type": "category"
},
"product_whatsapp_number_4": {
  "name": "Número WhatsApp Categoría 4",
  "type": "input",
  "default": "573027147698"
},
"product_phone_number_4": {
  "name": "Número Teléfono Categoría 4",
  "type": "input",
  "default": "3027147698"
}
```

---

## **📌 Implementación en `product-whatsapp-category-random.liquid`**
El código Liquid funciona de la siguiente manera:

1. **Cuenta cuántas categorías han sido configuradas en `product-whatsapp-category-random.json`.**
2. **Recorre todas las categorías del producto** para encontrar una coincidencia.
3. **Si encuentra una categoría coincidente, asigna el número de WhatsApp y Teléfono correspondiente.**
4. **Si no encuentra coincidencia, asigna los asesores comerciales de forma aleatoria (en caso de categoría comercial).**
5. **Si no hay coincidencia con ninguna categoría, usa un `fallback` con los datos comerciales.**

### **🔹 Cómo Funciona el `for` Dinámico**
El código recorre todas las categorías asignadas dinámicamente con:
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
    {% assign tracking_class = "category-" | append: category_value | replace: ' ', '-' %}
    {% break %}
  {% endif %}
{% endfor %}
```

### **🔹 Fallback en Caso de No Encontrar Categoría**
Si no hay coincidencia con ninguna categoría, el código asigna los datos comerciales:
```liquid
{% unless category_matched %}
  {% assign whatsapp_data = options.product_whatsapp_commercial_number_1 | append: "," | append: options.product_phone_commercial_number_1 %}
  {% assign tracking_class = "category-comercial" %}
{% endunless %}
```

---

## **📌 Medición de Clics con Google Tag Manager (Opcional)**
Para medir los clics en los botones de WhatsApp y Teléfono:
1. **Google Tag Manager debe estar instalado en la tienda Jumpseller.**
2. **Cada botón tiene clases CSS específicas para el seguimiento:**
```html
<a class="track-whatsapp category-1">...</a>
<a class="track-phone category-1">...</a>
```
3. **En Google Tag Manager, crear una Variable `Click Classes` y configurar activadores.**
4. **Crear eventos en Google Analytics para registrar los clics.**

---

## **📌 Casos de Uso y Posibles Errores**
| **Problema** | **Solución** |
|-------------|--------------|
| Los botones no aparecen | Verificar que el producto tenga categorías asignadas. |
| Los números no son los correctos | Revisar `product-whatsapp-category-random.json` y confirmar los valores asignados. |
| El fallback no se activa correctamente | Confirmar que `category_count` esté bien definido. |

---

## **📌 Conclusión y Próximos Pasos**
Este sistema permite manejar los botones de WhatsApp de manera dinámica en Jumpseller. En futuras versiones se podrían agregar nuevas funcionalidades como **integración con CRM** o **mensajes personalizados según el producto.**

📌 **Para cualquier actualización, se recomienda revisar `product-whatsapp-category-random.json` antes de modificar el código Liquid.**

🚀 **Listo para usar en Jumpseller!** 🎯

