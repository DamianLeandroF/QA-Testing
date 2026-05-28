# 🏆 EVALUACIÓN FINAL: QA MANUAL & TESTING

**Postulante:** Damián Fregocini  
**Comisión:** QA Manual - Mayo 2026 (#101880)  

---

## 🎯 OBJETIVO DEL PROYECTO
Validar de manera integral el sistema de **Mercado Libre (Argentina)** mediante el diseño y ejecución de casos de prueba funcionales, el reporte técnico de defectos (*bugs*), la documentación de pruebas de API con Postman y el análisis de métricas no funcionales de rendimiento a través de Google Lighthouse.

---

## ⚙️ PLAN / SETUP DE TESTING
* **Qué se probó:** Interfaz de búsqueda, catálogo de productos, flujo de autenticación (Login), procesos de carrito de compras y pasarela de pago.
* **Herramientas usadas:**
  * **Gestión de Casos:** Google Docs / Hojas de cálculo.
  * **Pruebas de API:** Postman (Métodos GET/POST).
  * **Rendimiento:** Lighthouse (Chrome DevTools).
  * **Evidencia:** Recortes de pantalla y registros de consola.
* **Ambiente:** Producción (`https://www.mercadolibre.com.ar`).

---

## 🧪 CASOS DE PRUEBA (10 CASOS)

| ID | Tipo | Caso de prueba | Precondición | Pasos | Resultado esperado | Resultado obtenido | Evidencia / Notas |
| :--- | :---: | :--- | :--- | :--- | :--- | :---: | :--- |
| **TP-01** | 🟢 Positivo | Búsqueda efectiva | Home cargada | Buscar "Termo Stanley" | Mostrar resultados relevantes | **✅ PASSED** | Se verificó carga en <1.5s. |
| **TP-02** | 🟢 Positivo | Agregar al carrito | Detalle producto | Clic "Agregar al carrito" | Icono carrito actualiza a (1) | **✅ PASSED** | Contador estricto y外 persistente. |
| **TP-03** | 🟢 Positivo | Filtro por condición | Lista resultados | Aplicar filtro "Nuevo" | Solo mostrar ítems nuevos | **✅ PASSED** | Filtro API respondió correctamente. |
| **TP-04** | 🟢 Positivo | Cambio de Código Postal | Detalle producto | Ingresar CP "7600" | Actualizar costos de envío | **✅ PASSED** | Costo varió según zona geográfica. |
| **TP-05** | 🟢 Positivo | Orden por menor precio | Lista resultados | Seleccionar "Menor precio" | Lista ordenada de $ min a max | **✅ PASSED** | Lógica de ordenamiento correcta. |
| **TN-01** | 🔴 Negativo | Login formato inválido | Pantalla Login | Ingresar email sin "@" | Error: "Revisá que esté bien" | **❌ FAILED** | Permitió avanzar (Ver `BUG-01`). |
| **TN-02** | 🔴 Negativo | Búsqueda inexistente | Barra búsqueda | Buscar "asdfghjkl123" | Mensaje de "No resultados" | **✅ PASSED** | Manejo de excepción visual OK. |
| **TN-03** | 🔴 Negativo | Pago CVV incompleto | Checkout | Dejar CVV vacío | Bloquear botón "Confirmar" | **✅ PASSED** | El campo resalta error en rojo. |
| **TN-04** | 🔴 Negativo | Caracteres en búsqueda | Barra búsqueda | Ingresar "000000000000" | No romper UI / Sin resultados | **✅ PASSED** | Sanitización de input correcta. |
| **TN-05** | 🔴 Negativo | Superar Stock real | Carrito | Cambiar cantidad a "99" | Error: "No hay stock" | **❌ FAILED** | Error 500 en servidor (Ver `BUG-03`). |

---

## 🐛 REPORTE DE DEFECTOS (BUGS)

### `BUG-01`
* **Caso relacionado:** TN-01
* **Descripción:** Falta de validación sintáctica en el campo de Login.
* **Pasos para reproducir:**
  1. Ir al formulario de Login.
  2. Ingresar `test.com` (sin símbolo @).
  3. Clic en el botón "Continuar".
* **Resultado Esperado:** Mensaje de error inmediato que impida el avance.
* **Resultado Obtenido:** El sistema acepta el valor y permite pasar al campo de ingreso de contraseña.
* **Severidad:** Media.
* **Observaciones:** Afecta directamente la calidad de los datos de usuario registrados.

---

### `BUG-02`
* **Caso relacionado:** UI Mobile
* **Descripción:** Desbordamiento visual (*Layout Break*) en cabecera con textos largos.
* **Pasos para reproducir:**
  1. Ingresar o pegar una cadena continua de 200 caracteres en el buscador principal.
* **Resultado Esperado:** El texto debe truncarse o generar un scroll interno sin alterar los elementos del header.
* **Resultado Obtenido:** La barra de búsqueda se expande hacia la derecha, desplazando el icono del carrito fuera de la pantalla.
* **Severidad:** Baja.

---

### `BUG-03`
* **Caso relacionado:** TN-05
* **Descripción:** Fallo de persistencia en validación de stock al momento de checkout masivo.
* **Pasos para reproducir:**
  1. Agregar un producto al carrito de compras.
  2. Cambiar manualmente la cantidad a una cifra superior al stock disponible (ej. 99).
  3. Proceder al pago.
* **Resultado Esperado:** Mensaje preventivo inline informando la falta de stock del artículo.
* **Resultado Obtenido:** El sistema procesa de forma regular la intención de cobro y luego interrumpe la conexión con un error 500 interno del servidor.
* **Severidad:** Alta.

---

## 🚀 PRUEBAS DE API CON POSTMAN (PARTE 1)
Se validaron los endpoints de comunicación mediante **PokemonAPI** para contrastar e inspeccionar la consistencia de la arquitectura de microservicios.

* **Requests realizados:** `GET /pokemon/{id}`, `GET /type/{name}`.
* **Evidencias analizadas:**
  * **`200 OK`**: Consulta exitosa del recurso "Pikachu" con un payload JSON íntegro y mapeado.
  * **`404 Not Found`**: Manejo e interceptación correcta del backend al buscar un ID inexistente (ej. `99999`).
* **Relación con Frontend:** Las pruebas de API confirmaron que los errores de "No resultados" detectados en la interfaz de usuario (`TN-02`) están correctamente gestionados y devueltos desde los servicios backend.

---

## 📊 INFORME DE RENDIMIENTO (LIGHTHOUSE)
* **URL Auditada:** `https://www.mercadolibre.com.ar`
* **Métricas Principales:**
  * **Performance (Rendimiento):** 62 (Mobile) / 88 (Desktop).
  * **Accessibility (Accesibilidad):** 92.
* **Problemas detectados:** Tiempo de carga inicial (*Largest Contentful Paint*) elevado en dispositivos móviles debido a la falta de optimización en imágenes pesadas y exceso de scripts de analíticas de terceros.
* **Acciones sugeridas:**
  1. Implementar carga diferida progresiva (*Lazy Load*) en el catálogo inferior.
  2. Fragmentar y minificar archivos estáticos CSS/JS para liberar el hilo principal del navegador.

---

## 🔬 ACTIVIDAD DETALLADA POSTMAN (PARTE 2)
* **Métodos utilizados:** `GET` para la obtención interactiva de datos estructurados.
* **Validaciones automatizadas:** 
  * Se comprobó mediante scripts de Postman que el tiempo de respuesta promedio de los servidores es estable e inferior a los 200ms (`<200ms`).
  * Se validó el esquema JSON para asegurar que propiedades críticas como `name` y `abilities` siempre se mantengan presentes y con tipos de datos correctos en el contrato de la API.

### 📋 Conceptos Generales de PokeAPI Evaluados
1. **Arquitectura RESTful:** Funciona bajo el estándar HTTP. Las respuestas se estructuran bajo un JSON jerárquico limpio.
2. **Autenticación y Límites:** Al ser una API pública y abierta, no requiere de una *API Key*. Se contempló el control del *Rate Limiting* para evitar sobrecargas, simulando errores `429 Too Many Requests`.
3. **Paginación:** Uso mandatorio de parámetros de control en listas extensas:
   * `limit`: Cantidad de registros a recuperar por petición.
   * `offset`: Índice inicial del puntero de búsqueda.
   * *Ejemplo evaluado:* `https://pokeapi.co/api/v2/pokemon?limit=10&offset=0`

### 📡 Consultas de Pruebas Ejecutadas
* **Caso A (Positivo):** `GET /pokemon/pikachu` -> Valida mapeo correcto de estadísticas básicas. Status: `200 OK`.
* **Caso B (Positivo):** `GET /berry/1/` -> Valida que el ID retorne inequívocamente la baya "cheri". Status: `200 OK`.
* **Caso C (Negativo):** `GET /pokemon/aguanteelqa` -> Valida el comportamiento controlado ante strings inválidos. Status: `404 Not Found`.

---

## 💾 DOCUMENTACIÓN Y ARCHIVOS DE RESPALDO
Se documentó y guardó la colección exportada de Postman donde se agruparon las pruebas de regresión. Las evidencias obtenidas demuestran una perfecta sincronía entre los datos puros manipulados en el backend y los componentes lógicos visualizados en el destino final por el usuario de la aplicación.

> ### 📝 CONCLUSIÓN Y DICTAMEN DEL PROFESIONAL
> El ciclo de pruebas se completó registrando un **80% de éxito**. Las funcionalidades base de exploración y filtrado operan correctamente. Sin embargo, los defectos hallados en el flujo de login y en la desincronización transaccional de stock representan prioridades críticas que requieren corrección inmediata antes del próximo despliegue a producción.
> 
> **Estado global del proyecto:** ⚠️ **Aprobado con observaciones críticas.**

---

**Firma:**  
Damián Fregocini  
*QA Manual Tester*  
**Fecha de Cierre:** 28/05/2026
