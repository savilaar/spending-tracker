# Gastos Tracker

Una aplicación web simple para rastrear gastos personales usando reconocimiento de voz.

## Cómo usar

1. Abre `index.html` en tu navegador móvil (Chrome o Safari recomendados)
2. Permite el acceso al micrófono cuando se solicite

## Características

### 📱 Grabar Gastos
- Presiona el botón del micrófono
- Di tu gasto, por ejemplo: "Carne 5000 efectivo"
- La app automáticamente detectará:
  - **Monto**: 5000
  - **Método de pago**: Efectivo
  - **Categoría**: Supermercado (basado en "carne")
  - **Original**: carne (guardado como referencia)
- Revisa la información y presiona "Guardar Gasto"

### 📊 Historial
- Ve todos tus gastos ordenados por fecha
- Exporta a CSV para análisis en Excel/Google Sheets

### ⚙️ Ajustes
Personaliza la app según tus necesidades:

1. **Métodos de Pago**: Agrega tus métodos (Efectivo, Débito, Crédito, etc.)
2. **Categorías**: Define categorías personalizadas (Supermercado, Transporte, etc.)
3. **Mapeo de Palabras Clave**: Asocia palabras con categorías
   - Ejemplo: "carne" → "Supermercado"
   - Ejemplo: "colectivo" → "Transporte"

## Valores por Defecto

### Métodos de Pago
- Efectivo
- Débito
- Crédito
- Transferencia

### Categorías
- Supermercado
- Transporte
- Comida
- Servicios
- Entretenimiento
- Otros

### Mapeos Incluidos
- carne, pan, verdura, fruta → Supermercado
- colectivo, taxi, uber → Transporte
- luz, agua, gas, internet → Servicios

## Ejemplos de Uso

- "Pan 500 efectivo"
- "Colectivo 150 débito"
- "Carne 8000 crédito"
- "Netflix 3000 débito"

## Almacenamiento

Todos los datos se guardan localmente en tu dispositivo usando IndexedDB. No se envía información a ningún servidor.

## Compatibilidad

- Chrome/Edge (Android/iOS)
- Safari (iOS)

**Nota**: El reconocimiento de voz puede requerir conexión a Internet en algunos navegadores.
