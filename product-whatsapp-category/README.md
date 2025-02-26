# Implementación de Botones de Contacto por Categoría en Jumpseller

## Introducción
Este documento describe cómo implementar botones de contacto por WhatsApp y teléfono en Jumpseller, basándose en la categoría de los productos. El código permite asignar números específicos a cada categoría y una categoría prioritaria que tendrá su propio número y texto de botón personalizado.

## Funcionalidad
- **Categoría Prioritaria:** Si un producto pertenece a esta categoría, se mostrará su contacto específico.
- **Categorías Generales:** Si el producto no tiene categoría prioritaria, se mostrará el contacto de la primera categoría coincidente.
- **Escalabilidad:** Se pueden agregar más categorías fácilmente.
- **Flexibilidad:** Se puede eliminar la categoría prioritaria si no es necesaria.

---

## Configuración JSON
El siguiente código define las opciones configurables en Jumpseller para asignar números de contacto a cada categoría.

```json
{
  "WhatsApp_Categorias": {
    "icon": "chat",
    "options": {
      "product_whatsapp_priority_category": {
        "name": "Categoría Prioritaria",
        "type": "category",
        "default": "SOLICITAR PRESUPUESTO",
        "value_type": "permalink"
      },
      "product_whatsapp_priority_number": {
        "name": "Número WhatsApp Categoría Prioritaria",
        "type": "input",
        "default": "573104265599"
      },
      "product_phone_priority_number": {
        "name": "Número Teléfono Categoría Prioritaria",
        "type": "input",
        "default": "3104265599"
      },
      "product_whatsapp_priority_text": {
        "name": "Texto del botón para Categoría Prioritaria",
        "type": "input",
        "default": "Solicitar presupuesto por WhatsApp"
      },
      "product_whatsapp_category_1": {
        "name": "Categoría 1",
        "type": "category",
        "default": "Impresoras 3D",
        "value_type": "permalink"
      },
      "product_whatsapp_number_1": {
        "name": "Número WhatsApp Categoría 1",
        "type": "input",
        "default": "573028301499"
      },
      "product_phone_number_1": {
        "name": "Número Teléfono Categoría 1",
        "type": "input",
        "default": "3028301499"
      },
      "product_whatsapp_category_2": {
        "name": "Categoría 2",
        "type": "category",
        "default": "Escáner 3D",
        "value_type": "permalink"
      },
      "product_whatsapp_number_2": {
        "name": "Número WhatsApp Categoría 2",
        "type": "input",
        "default": "573239815754"
      },
      "product_phone_number_2": {
        "name": "Número Teléfono Categoría 2",
        "type": "input",
        "default": "3239815754"
      }
    }
  }
}
```

---

## Código Liquid
Este código se encarga de mostrar los botones de contacto en función de la categoría del producto.

```liquid
{% assign priority_category = options.product_whatsapp_priority_category %}
{% assign priority_whatsapp = options.product_whatsapp_priority_number %}
{% assign priority_phone = options.product_phone_priority_number %}
{% assign priority_text = options.product_whatsapp_priority_text %}

{% assign whatsapp_data = "" %}
{% assign button_text = "Comprar por WhatsApp" %}

{% for product_category in product.categories %}
  {% assign product_category_permalink = product_category.url | split: "/" | last %}
  
  {% if product_category_permalink == priority_category %}
    {% assign whatsapp_data = priority_whatsapp | append: "," | append: priority_phone %}
    {% assign button_text = priority_text %}
    {% break %}
  {% endif %}
{% endfor %}

{% if whatsapp_data == "" %}
  {% for i in (1..3) %}
    {% assign category_key = "product_whatsapp_category_" | append: i %}
    {% assign whatsapp_key = "product_whatsapp_number_" | append: i %}
    {% assign phone_key = "product_phone_number_" | append: i %}
    
    {% assign category_permalink = options[category_key] %}
    {% assign whatsapp_number = options[whatsapp_key] %}
    {% assign phone_number = options[phone_key] %}
    
    {% for product_category in product.categories %}
      {% assign product_category_permalink = product_category.url | split: "/" | last %}
      
      {% if product_category_permalink == category_permalink %}
        {% assign whatsapp_data = whatsapp_number | append: "," | append: phone_number %}
        {% break %}
      {% endif %}
    {% endfor %}
    {% if whatsapp_data != "" %}
      {% break %}
    {% endif %}
  {% endfor %}
{% endif %}

{% if whatsapp_data != "" %}
  {% assign whatsapp_details = whatsapp_data | split: "," %}
  <div class="product-btn-comprar-what">
    <a href="https://api.whatsapp.com/send?phone={{ whatsapp_details[0] }}&text={% t 'Hola, me gustaría comprar el siguiente producto: ' %} {{ product.name }} | {{ store.base_url }}{{ product.url }}" target="_blank">
      {{ button_text }} <i class="fab fa-whatsapp"></i>
    </a>
  </div>
  <div class="product-btn-comprar-tel">
    <a href="tel:{{ whatsapp_details[1] }}">
      <i class="fas fa-phone"></i> Llamar {{ whatsapp_details[1] }}
    </a>
  </div>
{% endif %}
```

---

## Explicación del Código Liquid
- **Asignación de variables:** Se extraen las opciones configuradas en `options`.
- **Verificación de la categoría prioritaria:** Si el producto pertenece a la categoría prioritaria, se usan sus datos.
- **Búsqueda en otras categorías:** Si no hay coincidencia con la prioritaria, se busca la primera categoría coincidente en el bucle.
- **Generación de botones:** Si se encuentra una coincidencia, se generan los botones de contacto.

---

## Modificaciones y Personalización
### **Agregar Más Categorías**
- Agregar más entradas en el JSON.
- Aumentar el rango del bucle `{% for i in (1..N) %}` en el código Liquid.

### **Eliminar la Categoría Prioritaria**
- Remover las claves relacionadas en el JSON.
- Eliminar la lógica que busca la categoría prioritaria en el código Liquid.

---

# Explicación del Código Liquid para WhatsApp en Jumpseller

## Introducción
Este documento explica en detalle el funcionamiento del código Liquid utilizado para mostrar botones de contacto de WhatsApp y teléfono en función de la categoría del producto en Jumpseller. Se analizará cómo se asignan los números de contacto, cómo se priorizan las categorías y cómo se pueden modificar las configuraciones.

---

## **1️⃣ Definir las Variables de Configuración**
```liquid
{% assign priority_category = options.product_whatsapp_priority_category %}
{% assign priority_whatsapp = options.product_whatsapp_priority_number %}
{% assign priority_phone = options.product_phone_priority_number %}
{% assign priority_text = options.product_whatsapp_priority_text %}
```
📌 **¿Qué hace?**  
- Obtiene las opciones configuradas en Jumpseller (`options`) y las asigna a variables.
- `priority_category`: La categoría prioritaria seleccionada en la configuración.
- `priority_whatsapp`: Número de WhatsApp para la categoría prioritaria.
- `priority_phone`: Número de teléfono para la categoría prioritaria.
- `priority_text`: Texto personalizado del botón para la categoría prioritaria.

---

## **2️⃣ Inicializar Variables para WhatsApp y Botón**
```liquid
{% assign whatsapp_data = "" %}
{% assign button_text = "Comprar por WhatsApp" %}
```
📌 **¿Qué hace?**  
- `whatsapp_data` almacena el número de WhatsApp y el teléfono de la categoría encontrada.
- `button_text` define el texto predeterminado para el botón de WhatsApp.

---

## **3️⃣ Buscar si el Producto Pertenece a la Categoría Prioritaria**
```liquid
{% for product_category in product.categories %}
  {% assign product_category_permalink = product_category.url | split: "/" | last %}
  
  {% if product_category_permalink == priority_category %}
    {% assign whatsapp_data = priority_whatsapp | append: "," | append: priority_phone %}
    {% assign button_text = priority_text %}
    {% break %}
  {% endif %}
{% endfor %}
```
📌 **¿Qué hace?**  
- Recorre todas las categorías del producto (`product.categories`).
- Obtiene el **permalink** de la categoría (`product_category.url | split: "/" | last`).
- Si la categoría del producto coincide con la categoría prioritaria (`priority_category`), entonces:
  - Guarda el número de WhatsApp y teléfono en `whatsapp_data`.
  - Cambia el texto del botón al definido en `priority_text`.
  - **Usa `{% break %}` para salir del bucle y no seguir buscando más categorías.**

---

## **4️⃣ Si No Hay Categoría Prioritaria, Buscar en Otras Categorías**
```liquid
{% if whatsapp_data == "" %}
  {% for i in (1..3) %}
    {% assign category_key = "product_whatsapp_category_" | append: i %}
    {% assign whatsapp_key = "product_whatsapp_number_" | append: i %}
    {% assign phone_key = "product_phone_number_" | append: i %}
    
    {% assign category_permalink = options[category_key] %}
    {% assign whatsapp_number = options[whatsapp_key] %}
    {% assign phone_number = options[phone_key] %}
    
    {% for product_category in product.categories %}
      {% assign product_category_permalink = product_category.url | split: "/" | last %}
      
      {% if product_category_permalink == category_permalink %}
        {% assign whatsapp_data = whatsapp_number | append: "," | append: phone_number %}
        {% break %}
      {% endif %}
    {% endfor %}
    {% if whatsapp_data != "" %}
      {% break %}
    {% endif %}
  {% endfor %}
{% endif %}
```
📌 **¿Qué hace?**  
- Si el producto **no tiene** la categoría prioritaria (`whatsapp_data == ""`), busca en las demás categorías configuradas.
- **Bucle `{% for i in (1..3) %}`**:
  - Genera dinámicamente los nombres de las opciones (`product_whatsapp_category_X`).
  - Obtiene los valores de WhatsApp y teléfono de las opciones configuradas (`options`).
- **Bucle interno `{% for product_category in product.categories %}`**:
  - Compara la categoría del producto con la configuración (`category_permalink`).
  - Si hay coincidencia, asigna los números de contacto y **usa `{% break %}` para salir del bucle.**
- **Si se encuentra una categoría coincidente, se detiene completamente la búsqueda (`{% if whatsapp_data != "" %} {% break %} {% endif %}`).**

---

## **5️⃣ Mostrar los Botones Solo Si Hay Datos de Contacto**
```liquid
{% if whatsapp_data != "" %}
  {% assign whatsapp_details = whatsapp_data | split: "," %}
  <div class="product-btn-comprar-what">
    <a href="https://api.whatsapp.com/send?phone={{ whatsapp_details[0] }}&text={% t 'Hola, me gustaría comprar el siguiente producto: ' %} {{ product.name }} | {{ store.base_url }}{{ product.url }}" target="_blank">
      {{ button_text }} <i class="fab fa-whatsapp"></i>
    </a>
  </div>
  <div class="product-btn-comprar-tel">
    <a href="tel:{{ whatsapp_details[1] }}">
      <i class="fas fa-phone"></i> Llamar {{ whatsapp_details[1] }}
    </a>
  </div>
{% endif %}
```
📌 **¿Qué hace?**  
- Verifica si `whatsapp_data` tiene datos (si se encontró una categoría válida con números de contacto).
- Divide `whatsapp_data` en un array (`whatsapp_details`), donde:
  - `whatsapp_details[0]` = Número de WhatsApp.
  - `whatsapp_details[1]` = Número de teléfono.
- Genera los botones con los datos correspondientes:
  - Botón de WhatsApp con un enlace prellenado (`api.whatsapp.com/send`).
  - Botón de llamada (`tel:`).

---

## **Resumen del Flujo**
1. **Verifica si el producto pertenece a la categoría prioritaria.**
   - Si es así, usa su número de WhatsApp, teléfono y texto personalizado.
2. **Si no, busca en las demás categorías configuradas en `options`.**
   - Se detiene en la primera coincidencia.
3. **Si se encuentra una categoría válida, muestra los botones de contacto.**
4. **Si no hay coincidencias, no se muestra ningún botón.**

---

## **Cómo Modificar el Código**
✔ **Para agregar más categorías**:
- Aumentar el rango en `{% for i in (1..N) %}` en el segundo bucle.
- Agregar más categorías en el JSON.

✔ **Para eliminar la categoría prioritaria**:
- Eliminar la primera parte que verifica `priority_category`.
- Ajustar el segundo bucle para que siempre busque una categoría.

✔ **Para cambiar el texto del botón**:
- Modificar la variable `button_text`.

✔ **Para personalizar el mensaje de WhatsApp**:
- Editar la URL de `api.whatsapp.com/send`, agregando más parámetros.

Con esta documentación, cualquier desarrollador podrá entender y modificar el código fácilmente. 🚀



