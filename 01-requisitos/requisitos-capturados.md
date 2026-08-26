# Narración del cliente — Restaurante "Al paso"

## Título del caso
Gestión de mesas, pedidos, pagos y reservas — Restaurante "AL paso"

## Narración del cliente

"Soy el administrador del restaurante Al paso. Necesitamos un sistema para controlar
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

No todos los clientes que comen en el restaurante están registrados — solo guardamos los
datos de los clientes que hacen reservas o que piden que se les emita algún comprobante con
sus datos. El mesero que atiende cada pedido también queremos saber quién es, para poder
evaluar el desempeño del personal."

## Suposiciones

- Se identifica al cliente por nombre y teléfono o email; el registro de cliente es
  opcional para consumo directo en el restaurante, pero obligatorio para hacer una
  reserva.
- Un pedido pertenece a una sola mesa y es atendido por un solo mesero, pero puede recibir
  varios pagos (división de cuenta).
- El precio de cada producto dentro de un pedido se "congela" al momento de agregarlo,
  para no verse afectado por cambios posteriores del menú.
- La validación de que una mesa no tenga dos reservas con horarios solapados se resuelve
  a nivel de aplicación, no se modela como una entidad aparte.

## Entidades iniciales detectadas (sustantivos relevantes)

Cliente, Mesa, Empleado (mesero), Categoría, Producto (plato/bebida), Pedido,
Pago, Reserva.

## Reglas y restricciones extra

- El número de mesa es único.
- La capacidad de una mesa debe ser mayor a 0.
- El estado de una mesa es uno de: disponible, ocupada, reservada.
- El estado de un pedido es uno de: abierto, en preparación, servido, cerrado.
- El método de pago es uno de: efectivo, tarjeta, QR.
- El estado de una reserva es uno de: confirmada, cancelada, atendida.
