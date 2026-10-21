# CLAUDE.md — Perfil de Clientes (perfil-clientes)

Las reglas generales de trabajo están en el repo privado `lives-instrucciones` (CLAUDE.md). Este archivo solo describe esta herramienta.

## Qué hace
Perfil comercial de cada cliente (EFI, THY, PAK, VINCE y los demás de `pc_clientes`): pedidos, telas, segundas, facturación y despachos, plan de ventas y archivos. Contiene facturación y precios de cada cliente.
- `index.html` — **perfil** de solo lectura. Inicio con tarjetas por cliente (`#SIGLA` abre uno directo). KPIs: prendas pedidas del año (con variación contra el mismo corte del año anterior), promedio por pedido (pedido = grupo + PO), segundas, facturación y precio, despacho a tiempo (prendas con marca de retraso ÷ prendas con marca, solo si hay facturas) y cierre proyectado del plan. Pestañas:
  - Ficha (datos del cliente, comparativo por año en tarjetas y "Frente a los demás clientes" con puesto y media).
  - Pedidos (línea anual por mes, una por año; promedio por tipo de pedido: Producción, Reposición, Muestra y Stock/reserva; detalle con regla 500).
  - Telas (ranking y variación).
  - Segundas (de Calidad Manufactura y de Calidad Textil).
  - Facturación y despachos (solo si hay facturas): caídas por grupo, despacho a tiempo, mix de tela.
  - Plan de ventas (solo si hay plan; barras apiladas por mes, que son comparación por categoría).
  - Archivos (con "Descargar").
  - Imprimir (A4 horizontal, todas las pestañas) y Excel LIVES.
- `carga.html` — **carga de archivos** por cliente. Reconoce el tipo por su **contenido**, no por el nombre (.xlsx, .xlsm, .xls o .csv):
  - **Matriz de facturas**: hoja con "name fabric", "# de prendas" y "grupos". Las filas "PENDIENTE" son programa (sirven para medir caídas).
  - **Reporte del sistema** (FacturaExpRpt): tipo 'M' = muestra.
  - **Plan de ventas**: hojas con "PLAN DE VENTAS" (USD o PRENDAS); vale para todos los clientes.
  - Cualquier otro archivo se guarda como adjunto.
  - Solo toma las filas cuyo grupo empieza con el prefijo del cliente elegido; si el archivo es de otro cliente avisa en cuál subirlo. Informa cuántas filas son nuevas y cuántas ya estaban (no se vuelven a contar).

## Base de datos (Supabase lives-datos)
- Una sola puerta: `pc_api(p_accion, p)`.
  - Leer: `permiso`, `clientes`, `perfil`, `archivos`.
  - Registrar: `subir_facturas`, `subir_plan`, `adjunto`.
- Tablas propias: `pc_clientes`, `pc_cargas`, `pc_facturas`, `pc_plan`, `pc_datos`; función `pc_tipo_pedido`. Lee además `ops` (pedidos), `segundas_detalle` y `ctex_seg_det`. Al 21/10/2026: 4 cargas, 166 facturas y 387 filas de plan.
- Archivos: bucket privado `perfil-clientes` (`<SIGLA o TODOS>/<AAAAMMDD>-<HHMMSS>_<nombre>`). El original se sube primero y luego se registra; si falla el registro puede quedar un archivo suelto.

## Quién la usa
No aparece en el registro de entradas del portal de los últimos 60 días (al 21/10/2026).

## Quién puede registrar
- Lo decide la base: `pc_puede_ver` y `pc_puede_subir`. `permiso` devuelve ambos; sin permiso de subir, la carga queda deshabilitada.
- A Sistemas: esas dos funciones son las piezas que reemplazan con su seguridad; las facturas y el plan deberían salir directo de sus sistemas.

## Pendientes vistos al escribir esto (21/10/2026)
- Los mensajes de falta de permiso nombran a una persona con texto fijo: deben pasar a "Datos e IA".
- La tabla de archivos heredados menciona "tablero VINCE" (origen antiguo).
- Time and Action usa los mismos clientes (`pc_clientes`).
