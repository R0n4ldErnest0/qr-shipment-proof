# QR Shipment Proof 📦📸

[Español](#español) · [English](#english)

🔗 **Página / Page:** https://r0n4ldernest0.github.io/qr-shipment-proof/?order=EJEMPLO-123

> **Resumen.** Una página web mínima para registrar la **prueba de entrega** de
> un pedido. El repartidor escanea un código QR pegado en el paquete, se abre
> esta página con el número de pedido ya cargado, toma una foto con la cámara del
> celular y la envía. La foto llega a un flujo de automatización (n8n), que la
> asocia al pedido. No necesita instalar ninguna app ni iniciar sesión.
>
> **Summary.** A minimal web page to capture **proof of delivery** for an order.
> The courier scans a QR code stuck on the package, this page opens with the order
> number already filled in, they take a photo with the phone camera and submit it.
> The photo goes to an automation workflow (n8n) that links it to the order. No
> app install or login needed.

---

## Español

### Flujo
```
[Etiqueta del paquete con QR]
        │  QR = https://r0n4ldernest0.github.io/qr-shipment-proof/?order=<ID>
        ▼
[Esta página]  muestra el pedido → abre la cámara trasera → "Submit"
        │  POST multipart/form-data { order_id, photo }
        ▼
[Webhook de n8n]  guarda la foto y actualiza el pedido
```

### Cómo funciona
- Un único `index.html` con HTML + JavaScript puro, publicado en **GitHub Pages**.
- Lee el número de pedido del parámetro `?order=` de la URL (`URLSearchParams`).
  Si no viene, muestra `UNKNOWN`.
- `<input type="file" accept="image/*" capture="environment">` abre directamente
  la **cámara trasera** en el celular.
- Al enviar, arma un `FormData` con `order_id` y `photo` y hace `fetch(..., { method: "POST" })`
  al webhook de n8n configurado en el script. Muestra *Uploading…* y luego
  *Uploaded ✅*.

### Generar los QR
Cada QR solo tiene que codificar la URL con el ID del pedido, por ejemplo:
```
https://r0n4ldernest0.github.io/qr-shipment-proof/?order=PED-000123
```
Se puede generar al crear la etiqueta de envío (por ejemplo, con la librería
`qrcode` de Python o desde el mismo flujo de n8n).

### Configuración
Para usar tu propio backend, cambia la URL del `fetch` en `index.html` por tu
webhook. Este debe aceptar `multipart/form-data` con los campos `order_id` (texto)
y `photo` (archivo).

### Limitaciones y próximos pasos
- La URL apunta a un webhook de **pruebas** (`/webhook-test/`) de n8n. Para
  producción hay que cambiarla a la URL de producción del webhook.
- Hoy marca *Uploaded ✅* sin revisar la respuesta del servidor. Convendría
  validar `response.ok` y mostrar un error si falla.
- Ideas: comprimir la foto antes de subirla, capturar la geolocalización y la
  hora como evidencia adicional, y evitar envíos duplicados.

### Licencia
MIT

---

## English

### Flow
```
[Package label with QR]
        │  QR = https://r0n4ldernest0.github.io/qr-shipment-proof/?order=<ID>
        ▼
[This page]  shows the order → opens rear camera → "Submit"
        │  POST multipart/form-data { order_id, photo }
        ▼
[n8n webhook]  stores the photo and updates the order
```

### How it works
- A single `index.html` with plain HTML + JavaScript, published on **GitHub Pages**.
- Reads the order number from the `?order=` URL parameter (`URLSearchParams`).
  If it's missing, it shows `UNKNOWN`.
- `<input type="file" accept="image/*" capture="environment">` opens the phone's
  **rear camera** directly.
- On submit, it builds a `FormData` with `order_id` and `photo` and calls
  `fetch(..., { method: "POST" })` against the n8n webhook set in the script. It
  shows *Uploading…* and then *Uploaded ✅*.

### Generating the QR codes
Each QR only needs to encode the URL with the order ID, for example:
```
https://r0n4ldernest0.github.io/qr-shipment-proof/?order=PED-000123
```
You can generate it when the shipping label is created (for example, with
Python's `qrcode` library or from the n8n workflow itself).

### Configuration
To use your own backend, replace the `fetch` URL in `index.html` with your
webhook. It must accept `multipart/form-data` with an `order_id` field (text) and
a `photo` field (file).

### Limitations and next steps
- The URL points to an n8n **test** webhook (`/webhook-test/`). For production it
  has to be switched to the webhook's production URL.
- It currently shows *Uploaded ✅* without checking the server response. It should
  check `response.ok` and show an error on failure.
- Ideas: compress the photo before uploading, capture geolocation and timestamp
  as extra evidence, and prevent duplicate submissions.

### License
MIT
