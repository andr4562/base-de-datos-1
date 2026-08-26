# Modelo Conceptual — Entidades y Relaciones

## Entidades y Atributos

### CLIENTE
- `id_cliente` (PK)
- `nombre`
- `apellido`
- `telefono`
- `email`

### MESA
- `id_mesa` (PK)
- `numero`
- `capacidad`
- `ubicacion` (interior / terraza)
- `estado` (disponible / ocupada / reservada)

### EMPLEADO
- `id_empleado` (PK)
- `nombre`
- `apellido`
- `cargo` (mesero / cocinero / cajero / administrador)
- `turno`

### CATEGORIA
- `id_categoria` (PK)
- `nombre`

### PRODUCTO
- `id_producto` (PK)
- `nombre`
- `descripcion`
- `precio`
- `id_categoria` (FK → CATEGORIA)
- `disponible`

### PEDIDO
- `id_pedido` (PK)
- `id_mesa` (FK → MESA)
- `id_empleado` (FK → EMPLEADO)
- `id_cliente` (FK → CLIENTE, opcional)
- `fecha_hora`
- `estado` (abierto / en_preparacion / servido / cerrado)

### PEDIDO_PRODUCTO (entidad asociativa N:M)
- `id_pedido` (PK parcial, FK → PEDIDO)
- `id_producto` (PK parcial, FK → PRODUCTO)
- `cantidad`
- `precio_unitario`
- `notas`

### PAGO
- `id_pago` (PK)
- `id_pedido` (FK → PEDIDO)
- `metodo` (efectivo / tarjeta / qr)
- `monto`
- `fecha_pago`

### RESERVA
- `id_reserva` (PK)
- `id_cliente` (FK → CLIENTE)
- `id_mesa` (FK → MESA)
- `fecha_reserva`
- `hora_reserva`
- `num_personas`
- `estado` (confirmada / cancelada / atendida)

---

## Relaciones y Cardinalidades

**MESA 1 — N PEDIDO**
Una mesa recibe muchos pedidos a lo largo del tiempo; cada pedido se abre en una única mesa.

**EMPLEADO 1 — N PEDIDO**
Un mesero atiende muchos pedidos durante su turno; cada pedido es atendido por un único mesero.

**CLIENTE 1 — N PEDIDO** (opcional)
Un cliente registrado puede generar muchos pedidos; un pedido puede no tener cliente asociado.

**CATEGORIA 1 — N PRODUCTO**
Una categoría agrupa muchos productos; cada producto pertenece a una única categoría.

**PEDIDO N — M PRODUCTO** (a través de PEDIDO_PRODUCTO)
Un pedido puede incluir varios productos y un producto puede aparecer en muchos pedidos. La entidad asociativa guarda cantidad, precio congelado y notas.

**PEDIDO 1 — N PAGO**
Un pedido puede saldarse con varios pagos (división de cuenta); cada pago corresponde a un único pedido.

**CLIENTE 1 — N RESERVA**
Un cliente puede hacer varias reservas en distintas fechas; cada reserva pertenece a un único cliente.

**MESA 1 — N RESERVA**
Una mesa puede tener varias reservas en distintos horarios; cada reserva corresponde a una única mesa.

---

## Restricciones de Negocio Importantes

- No se debe permitir crear una reserva para una mesa que ya tiene otra reserva con fecha y horario solapado.
- El `precio_unitario` en `PEDIDO_PRODUCTO` se copia del precio actual del producto al agregarlo.
- Un pedido no puede cerrarse si la suma de pagos no cubre el total de productos.
- El estado de la mesa cambia a `ocupada` cuando se abre un pedido y vuelve a `disponible` cuando cierra.
