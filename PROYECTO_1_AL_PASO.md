# PROYECTO 1 — AVANCE DE PROYECTO
# Gestión de negocio "Al Paso"

## 1. Narración del cliente — Negocio "Al Paso"

### Título del caso
Gestión de mesas, pedidos, pagos y reservas — Negocio "Al Paso"

### Narración del cliente

"Soy el administrador del negocio Al Paso. Necesitamos un sistema para controlar
las mesas, los pedidos que se toman en cada mesa, los pagos y las reservas. Actualmente
todo se anota en un cuaderno y a veces se pierden comandas o se cobra mal.

Cada mesa tiene un número, una capacidad de personas y está ubicada en el salón interior
o en la terraza. Cuando llegan los clientes, un mesero abre un pedido para esa mesa y va
agregando los platos y bebidas que van pidiendo, con la cantidad de cada uno; a veces el
cliente pide que un plato venga sin algún ingrediente y eso también se anota. Los platos y
bebidas están organizados por categorías (entradas, platos fuertes, bebidas, postres) y cada
uno tiene un precio.

Un mismo pedido puede tener varios platos distintos, y un plato puede aparecer en muchos
pedidos distintos a lo largo del día. El precio que se cobra es el que tenía el plato en el
momento del pedido, aunque después cambiemos el precio del menú.

Cuando el cliente termina de comer, se cierra el pedido y se registra el pago; a veces la
cuenta se divide y se hacen dos o tres pagos para el mismo pedido (por ejemplo, con
tarjeta y en efectivo).

También atendemos reservas: un cliente llama o escribe para reservar una mesa en una
fecha y hora determinada, indicando cuántas personas son. No queremos que se pueda
reservar la misma mesa dos veces en el mismo horario.

No todos los clientes que comen en el negocio están registrados — solo guardamos los
datos de los clientes que hacen reservas o que piden que se les emita algún comprobante con
sus datos. El mesero que atiende cada pedido también queremos saber quién es, para poder
evaluar el desempeño del personal."

### Suposiciones

- Se identifica al cliente por nombre y teléfono o email; el registro de cliente es
  opcional para consumo directo en el negocio, pero obligatorio para hacer una
  reserva.
- Un pedido pertenece a una sola mesa y es atendido por un solo mesero, pero puede recibir
  varios pagos (división de cuenta).
- El precio de cada producto dentro de un pedido se "congela" al momento de agregarlo,
  para no verse afectado por cambios posteriores del menú.
- La validación de que una mesa no tenga dos reservas con horarios solapados se resuelve
  a nivel de aplicación (o con una restricción adicional), no se modela como una entidad
  aparte.

### Entidades iniciales detectadas (sustantivos relevantes)

Cliente, Mesa, Empleado (mesero), Categoría, Producto (plato/bebida), Pedido,
Pago, Reserva.

### Reglas y restricciones extra

- El número de mesa es único.
- La capacidad de una mesa debe ser mayor a 0.
- El estado de una mesa es uno de: disponible, ocupada, reservada.
- El estado de un pedido es uno de: abierto, en preparación, servido, cerrado.
- El método de pago es uno de: efectivo, tarjeta, QR.
- El estado de una reserva es uno de: confirmada, cancelada, atendida.

---

## 2. Modelo conceptual — Entidades, atributos y relaciones

### 2.1 Entidades y atributos

#### CLIENTE
- `id_cliente` 
- `nombre` 
- `apellido` 
- `telefono`
- `email` 

#### MESA
- `id_mesa`
- `numero`
- `capacidad` 
- `ubicacion` 
- `estado` 

#### EMPLEADO
- `id_empleado` 
- `nombre` 
- `apellido` 
- `cargo` 
- `turno` 

#### CATEGORIA
- `id_categoria` 
- `nombre`

#### PRODUCTO
- `id_producto` 
- `nombre` 
- `descripcion` 
- `precio` 
- `id_categoria` 
- `disponible` 

#### PEDIDO
- `id_pedido` 
- `id_mesa` 
- `id_empleado` 
- `id_cliente` 
- `fecha_hora`
- `estado` 

#### PEDIDO_PRODUCTO (entidad asociativa)
- `id_pedido` 
- `id_producto` 
- `cantidad` 
- `precio_unitario` 
- `notas` 

#### PAGO
- `id_pago` 
- `id_pedido` 
- `metodo` 
- `monto` 
- `fecha_pago` 

#### RESERVA
- `id_reserva` 
- `id_cliente` 
- `id_mesa` 
- `fecha_reserva` 
- `hora_reserva` 
- `num_personas`
- `estado` 

### 2.2 Relaciones y cardinalidades (justificación)

**MESA 1 — N PEDIDO**
Una mesa recibe muchos pedidos a lo largo del tiempo (uno distinto por cada grupo de
clientes que se sienta); cada pedido se abre en una única mesa.

**EMPLEADO 1 — N PEDIDO**
Un mesero atiende muchos pedidos durante su turno; cada pedido es atendido por un único
mesero, lo que permite evaluar el desempeño por empleado.

**CLIENTE 1 — N PEDIDO (opcional)**
Un cliente registrado puede generar muchos pedidos a lo largo de sus visitas; un pedido
puede no tener cliente asociado (consumo directo sin registrar datos).

**CATEGORIA 1 — N PRODUCTO**
Una categoría agrupa muchos productos (varias entradas, varios postres, etc.); cada
producto pertenece a una única categoría.

**PEDIDO N — M PRODUCTO (resuelta con PEDIDO_PRODUCTO)**
Un pedido puede incluir varios productos distintos y un mismo producto puede aparecer en
muchos pedidos diferentes. La entidad asociativa guarda además la cantidad, el precio
congelado al momento del pedido y notas específicas del cliente.

**PEDIDO 1 — N PAGO**
Un pedido puede saldarse con más de un pago (cuenta dividida entre efectivo y tarjeta,
por ejemplo); cada pago corresponde a un único pedido.

**CLIENTE 1 — N RESERVA**
Un cliente puede hacer varias reservas en distintas fechas; cada reserva pertenece a un
único cliente.

**MESA 1 — N RESERVA**
Una mesa puede tener muchas reservas en distintos horarios; cada reserva corresponde a
una única mesa.

### 2.3 Restricciones de negocio importantes

- No se debe permitir crear una reserva para una mesa que ya tiene otra reserva con fecha
  y horario que se solapen (se valida en la capa de aplicación).
- El `precio_unitario` en `PEDIDO_PRODUCTO` se copia del precio del producto al momento de
  agregarlo al pedido, para no verse afectado por cambios posteriores del menú.
- Un pedido no debería marcarse como `cerrado` si la suma de sus pagos no cubre el total
  de sus productos (validación de aplicación).
- El estado de la mesa cambia a `ocupada` cuando se abre un pedido sobre ella y vuelve a
  `disponible` cuando el pedido se cierra.

### 2.4 Representación para el DER

- **Entidades** (rectángulo/tabla): CLIENTE, MESA, EMPLEADO, CATEGORIA, PRODUCTO, PEDIDO,
  PAGO, RESERVA.
- **Entidad asociativa** (N:M con atributos propios): PEDIDO_PRODUCTO.
- **Cardinalidades**: MESA (1)—(N) PEDIDO; EMPLEADO (1)—(N) PEDIDO; CLIENTE (1)—(N) PEDIDO;
  CATEGORIA (1)—(N) PRODUCTO; PEDIDO (N)—(M) PRODUCTO; PEDIDO (1)—(N) PAGO;
  CLIENTE (1)—(N) RESERVA; MESA (1)—(N) RESERVA.

---

## Estado del proyecto

✅ Modelo conceptual completado  
⏳ Diseño de Diagrama Entidad-Relación (DER) — pendiente  
⏳ Implementación de base de datos (SQL) — pendiente
