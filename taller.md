# Normalizacion base de datos clientes,empleados...etc

-- creacion de la base de datos

CREATE DATABASE vtaszfs; 
USE vtaszfs;

-- Tabla Clientes
CREATE TABLE Clientes (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre VARCHAR(100),
email VARCHAR(100) UNIQUE
);

-- Tabla UbicacionCliente
CREATE TABLE UbicacionCliente (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre VARCHAR(100),
email VARCHAR(100) UNIQUE
);

-- Tabla Empleados
CREATE TABLE Empleados (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre VARCHAR(100),
puesto VARCHAR(100),
salario DECIMAL(10,2),
fecha_contratacion DATE 
);

-- Tabla Proveedores
CREATE TABLE Proveedores (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre VARCHAR(100),
contacto VARCHAR(100),
telefono VARCHAR(20),
direccion VARCHAR(255)
);

-- Tabla TiposProductos
CREATE TABLE TiposProductos (
id INT PRIMARY KEY AUTO_INCREMENT,
tipo_nombre VARCHAR(100),
descripcion TEXT
);

-- Tabla Productos
CREATE TABLE Productos (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre VARCHAR(100),
precio DECIMAL(10,2),
proveedor_id INT,
tipo_id INT,
FOREIGN KEY (preveedor_id) REFERENCES Proveedores(id),
FOREIGN KEY (tipo_id) REFERENCES TiposProductos(id)
);

-- Tabla pedidos
CREATE TABLE Pedidos (
id INT PRIMARY KEY AUTO_INCREMENT,
cliente_id INT,
fecha DATE,
total DECIMAL(10,2),
FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);

-- Tabla DetallesPedido
CREATE TABLE DetallesPedido (
id INT PRIMARY KEY AUTO_INCREMENT,
pedido_id INT,
producto_id INT,
cantidad INT,
precio DECIMAL(10,2),
FOREIGN KEY (pedido_id) REFERENCES Pedidos(id),
FOREIGN KEY (producto_id) REFERENCES Productos(id)
);


# 1.NORMALIZACION

## 1. Crear una tabla HistorialPedidos que almacene cambios en los pedidos.

CREATE TABLE HistorialPedidos (
id INT PRIMARY KEY AUTO_INCREMENT,
pedido_id INT,
estado VARCHAR(50),
total DECIMAL(10,2),
fecha_modificada TIMESTAMP,
FOREIGN KEY (pedido_id) REFERENCES Pedidos(id)
);

## 2. Evaluar la tabla Clientes para eliminar datos redundantes y normalizar hasta 3NF.

### -- Tabla Clientes
CREATE TABLE Clientes (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre VARCHAR(100),
FOREIGN KEY (email_id) REFERENCES EmailCliente(id)
);

### -- Tabla UbicacionCliente
CREATE TABLE UbicacionCliente (
id INT PRIMARY KEY AUTO_INCREMENT,
cliente_id INT,
FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);

CREATE TABLE DireccionCliente(
id INT PRIMARY KEY AUTO_INCREMENT,
cliente_id INT,
pais VARCHAR(255),
ciudad VARCHAR(255),
direccion VARCHAR(255)
FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);

CREATE TABLE EmailCliente (
id INT PRIMARY KEY AUTO_INCREMENT,
email VARCHAR(100),
cliente_id INT,
FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);

## 3. Separar la tabla Empleados en una tabla de DatosEmpleados y otra para Puestos .

CREATE TABLE DatosEmpleados (
id INT PRIMARY KEY AUTO_INCREMENT,
puesto_id INT,
nombre VARCHAR(100),
fecha_contratacion DATE,
FOREIGN KEY (puesto_id) REFERENCES Puestos(id)
);

CREATE TABLE Puestos (
id INT PRIMARY KEY AUTO_INCREMENT,
puesto VARCHAR(50),
salario DECIMAL(10,2)
);

## 4. Revisar la relación Clientes y UbicacionCliente para evitar duplicación de datos.


CREATE TABLE Ubicacion (
id INT PRIMARY KEY AUTO_INCREMENT,
direccion VARCHAR(100),
ciudad VARCHAR(100),
pais VARCHAR(100)
);

### -- Tabla Clientes
CREATE TABLE Clientes (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre VARCHAR(100),
ubicacion_id INT,
FOREIGN KEY (ubicacion_id) REFERENCES Ubicacion(id)
);


CREATE TABLE EmailCliente (
id INT PRIMARY KEY AUTO_INCREMENT,
email VARCHAR(100),
cliente_id INT,
FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);


## 5. Normalizar Proveedores para tener ContactoProveedores en otra tabla.

-- Tabla Proveedores
CREATE TABLE Proveedores (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre VARCHAR(100),
ubicacion_id INT,
FOREIGN KEY (ubicacion_id) REFERENCES Ubicacion(id)
);

CREATE TABLE ContactoProveedores (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre_contacto VARCHAR(100),
telefono VARCHAR(20),
proveedor_id INT,
FOREIGN KEY (proveedor_id) REFERENCES Proveedores(id) 
);


## 6. Crear una tabla de Telefonos para almacenar múltiples números por cliente.


CREATE TABLE Telefonos (
id INT PRIMARY KEY AUTO_INCREMENT,
cliente_id INT,
numero VARCHAR(20),
tipo_numero ENUM('fijo', 'movil', 'trabajo', 'otro'),
FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);

## 7. Transformar TiposProductos en una relación categórica jerárquica.

-- Tabla TiposProductos
CREATE TABLE TiposProductos (
id INT PRIMARY KEY AUTO_INCREMENT,
tipo_nombre VARCHAR(100),
tipo_id INT,
descripcion TEXT,
FOREIGN KEY (tipo_id) REFERENCES TiposProductos(id)
);


## 8. Normalizar Pedidos y DetallesPedido para evitar inconsistencias de precios.

### -- Tabla pedidos
CREATE TABLE Pedidos (
id INT PRIMARY KEY AUTO_INCREMENT,
cliente_id INT,
fecha DATE,
total DECIMAL(10,2),
FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);

### -- Tabla DetallesPedido
CREATE TABLE DetallesPedido (
id INT PRIMARY KEY AUTO_INCREMENT,
pedido_id INT,
producto_id INT,
cantidad INT,
precio DECIMAL(10,2),
FOREIGN KEY (pedido_id) REFERENCES Pedidos(id),
FOREIGN KEY (producto_id) REFERENCES Productos(id)
);

CREATE TRIGGER actualizar_total_pedido
AFTER INSERT ON DetallesPedidos
FOR EACH ROW
BEGIN
 UPDATE PedidoS
 SET total = total + (NEW.precio * NEW.cantidad)
 WHERE id = NEW.pedido_id
END;


## 9. Usar una relación de muchos a muchos para Empleados y Proveedores .


CREATE TABLE Empleados_Proveedores (
id INT PRIMARY KEY AUTO_INCREMENT,
empleados_id INT,
proveedores_id INT,
FOREIGN KEY (empleados_id) REFERENCES DatosEmpleados(id),
FOREIGN KEY (proveedor_id) REFERENCES Proveedores(id) 
);



## 10. Convertir la tabla UbicacionCliente en una relación genérica de Ubicaciones .


CREATE TABLE Ubicacion (
id INT PRIMARY KEY AUTO_INCREMENT,
direccion VARCHAR(100),
ciudad VARCHAR(100),
pais VARCHAR(100)
);

### -- Tabla Clientes
CREATE TABLE Clientes (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre VARCHAR(100),
ubicacion_id INT,
FOREIGN KEY (ubicacion_id) REFERENCES Ubicacion(id)
);

### -- Tabla Proveedores
CREATE TABLE Proveedores (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre VARCHAR(100),
ubicacion_id INT,
FOREIGN KEY (ubicacion_id) REFERENCES Ubicacion(id)
);

------------------------------------------------------
 RESULTADO FINAL CON LA TABLA TOTALMENTE NORMALIZADA.
------------------------------------------------------
CREATE DATABASE vtaszfs; 
USE vtaszfs;



CREATE TABLE IF NOT EXISTS Ubicacion (
id INT PRIMARY KEY AUTO_INCREMENT,
direccion VARCHAR(100),
ciudad VARCHAR(100),
pais VARCHAR(100)
)ENGINE = INNODB;

-- Tabla Clientes
CREATE TABLE IF NOT EXISTS Clientes (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre VARCHAR(100),
ubicacion_id INT,
CONSTRAINT fk_ubicacion_id_clientes FOREIGN KEY (ubicacion_id) REFERENCES Ubicacion(id)
)ENGINE = INNODB;


CREATE TABLE IF NOT EXISTS EmailCliente (
id INT PRIMARY KEY AUTO_INCREMENT,
email VARCHAR(100),
cliente_id INT,
CONSTRAINT fk_cliente_id_email FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
)ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS Telefonos (
id INT PRIMARY KEY AUTO_INCREMENT,
cliente_id INT,
numero VARCHAR(20),
tipo_numero ENUM('fijo', 'movil', 'trabajo', 'otro'),
CONSTRAINT fk_cliente_id_telefonos FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
)ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS Puestos (
id INT PRIMARY KEY AUTO_INCREMENT,
puesto VARCHAR(50),
salario DECIMAL(10,2)
)ENGINE = INNODB;


CREATE TABLE IF NOT EXISTS DatosEmpleados (
id INT PRIMARY KEY AUTO_INCREMENT,
puesto_id INT,
nombre VARCHAR(100),
fecha_contratacion DATE,
CONSTRAINT fk_puesto_id FOREIGN KEY (puesto_id) REFERENCES Puestos(id)
)ENGINE = INNODB;


-- Tabla Proveedores
CREATE TABLE IF NOT EXISTS Proveedores (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre VARCHAR(100),
ubicacion_id INT,
CONSTRAINT fk_ubicacion_id_proveedores FOREIGN KEY (ubicacion_id) REFERENCES Ubicacion(id)
)ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS ContactoProveedores (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre_contacto VARCHAR(100),
telefono VARCHAR(20),
proveedor_id INT,
CONSTRAINT fk_proveedor_id_contacto FOREIGN KEY (proveedor_id) REFERENCES Proveedores(id) 
)ENGINE = INNODB;


-- Tabla TiposProductos
CREATE TABLE IF NOT EXISTS TiposProductos (
id INT PRIMARY KEY AUTO_INCREMENT,
tipo_nombre VARCHAR(100),
tipo_id INT,
descripcion TEXT,
CONSTRAINT fk_tipo_id_seccion FOREIGN KEY (tipo_id) REFERENCES TiposProductos(id)
)ENGINE = INNODB;

-- Tabla Productos
CREATE TABLE IF NOT EXISTS Productos (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre VARCHAR(100),
precio DECIMAL(10,2),
proveedor_id INT,
tipo_id INT,
CONSTRAINT fk_proveedor_id_productos FOREIGN KEY (proveedor_id) REFERENCES Proveedores(id),
CONSTRAINT fk_tipo_id_productos FOREIGN KEY (tipo_id) REFERENCES TiposProductos(id)
)ENGINE = INNODB;

-- Tabla pedidos
CREATE TABLE IF NOT EXISTS Pedidos (
id INT PRIMARY KEY AUTO_INCREMENT,
cliente_id INT,
fecha DATE,
total DECIMAL(10,2),
empleado_id INT,
CONSTRAINT fk_cliente_id_pedidos FOREIGN KEY (cliente_id) REFERENCES Clientes(id),
CONSTRAINT fk_empleado_id FOREIGN KEY (empleado_id) REFERENCES DatosEmpleados(id)
)ENGINE = INNODB;

-- Tabla DetallesPedido
CREATE TABLE IF NOT EXISTS DetallesPedido (
id INT PRIMARY KEY AUTO_INCREMENT,
pedido_id INT,
producto_id INT,
cantidad INT,
precio DECIMAL(10,2),
CONSTRAINT fk_pedido_id_detalles FOREIGN KEY (pedido_id) REFERENCES Pedidos(id),
CONSTRAINT fk_producto_id FOREIGN KEY (producto_id) REFERENCES Productos(id)
)ENGINE = INNODB;
DELIMITER //

CREATE TRIGGER actualizar_total_pedido
AFTER INSERT ON DetallesPedido
FOR EACH ROW
BEGIN
 UPDATE Pedidos
 SET total = total + (NEW.precio * NEW.cantidad)
 WHERE id = NEW.pedido_id;
END;
//
DELIMITER ;

CREATE TABLE IF NOT EXISTS HistorialPedidos (
id INT PRIMARY KEY AUTO_INCREMENT,
pedido_id INT,
estado VARCHAR(50),
total DECIMAL(10,2),
fecha_modificada TIMESTAMP,
CONSTRAINT fk_pedido_id_historial FOREIGN KEY (pedido_id) REFERENCES Pedidos(id)
)ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS Empleados_Proveedores (
id INT PRIMARY KEY AUTO_INCREMENT,
empleados_id INT,
proveedor_id INT,
CONSTRAINT fk_empleados_id_relacion FOREIGN KEY (empleados_id) REFERENCES DatosEmpleados(id),
CONSTRAINT fk_proveedor_id_relacion FOREIGN KEY (proveedor_id) REFERENCES Proveedores(id) 
)ENGINE = INNODB;

==========================================
       Datos con los que trabajar
==========================================

### --Ubicacion--

INSERT INTO Ubicacion (id,direccion,ciudad,pais) VALUES 
(1,'Calle 1', 'Bogotá', 'Colombia'),
(2,'Calle 2', 'Medellín', 'Colombia'),
(3,'Calle 3', 'Cali', 'Colombia'),
(4,'Av. 4', 'Barranquilla', 'Colombia'),
(5,'Av. 5', 'Cartagena', 'Colombia');


### --Puestos--

INSERT INTO Clientes (id,nombre,ubicacion_id) VALUES 
(1,'juan gonzales',1),
(2,'luis herrera',2),
(3,'pedro palacios',3),
(4,'hector martines',4),
(5,'andres lopez',5);

INSERT INTO Clientes (id,nombre,ubicacion_id) VALUES 
(6,'martines',NULL),
(7,'lopez',NULL);
--EmailClientes--

INSERT INTO EmailCliente (id,email,cliente_id) VALUES
(1,'juan@gmail.com',1),
(2,'luis@gmail.com',2),
(3,'pedro@gmail.com',3),
(4,'hector@gmail.com',4),
(5,'andres@gmail.com',5);

### --Telefonos--

INSERT INTO Telefonos (id,cliente_id, numero, tipo_numero) VALUES
(1,1,'3011111111','movil'),
(2,2,'3022222222','trabajo'),
(3,3,'3033333333','fijo'),
(4,4,'3044444444','otro'),
(5,5,'3055555555','movil');

### --Puestos--

INSERT INTO Puestos (id,puesto,salario) VALUES
(1,'Cajero', 1500.00),
(2,'Gerente', 3500.00),
(3,'Asistente', 1800.00),
(4,'Jefe de compras', 2800.00),
(5,'Supervisor', 2200.00);

### --Datos empleados--

INSERT INTO DatosEmpleados (id,puesto_id, nombre, fecha_contratacion) VALUES
(1,1, 'Mario Mendoza', '2021-03-01'),
(2,2, 'Lucía Cárdenas', '2020-07-15'),
(3,3, 'Tomás Álvarez', '2022-01-10'),
(4,4, 'Daniela López', '2023-07-21'),
(5,5, 'Esteban Nieto', '2024-02-20');


### --Proveedores--

INSERT INTO Proveedores (id,nombre,ubicacion_id) VALUES
(1,'Proveedor A', 1),
(2,'Proveedor B', 2),
(3,'Proveedor C', 3),
(4,'Proveedor D', 4),
(5,'Proveedor E', 5);

### --ContactosProveedores--

INSERT INTO ContactoProveedores (id,nombre_contacto, telefono, proveedor_id) VALUES
(1,'Ana Contacto', '3111111111', 1),
(2,'Luis Contacto', '3122222222', 2),
(3,'Carlos Contacto', '3133333333', 3),
(4,'Diana Contacto', '3144444444', 4),
(5,'Marcos Contacto', '3155555555', 5);

### --TiposProductos--

INSERT INTO TiposProductos (id,tipo_nombre, tipo_id, descripcion) VALUES
(1,'Tecnología', NULL, 'Productos tecnológicos'),
(2,'Electrodomésticos', NULL, 'Aparatos eléctricos'),
(3,'Audio', 1, 'Productos de sonido'),
(4,'Computadores', 1, 'Laptops y PCs'),
(5,'Cocina', 2, 'Para uso en cocina');


### --Productos--

INSERT INTO Productos (id,nombre, precio, proveedor_id, tipo_id) VALUES
(1,'Producto1', 78.84, 1, 5),
(2,'Producto2', 54.12, 2, 4),
(3,'Producto3', 57.73, 3, 3),
(4,'Producto4', 53.81, 4, 2),
(5,'Producto5', 85.56, 5, 1);

INSERT INTO Productos (id,nombre, precio, proveedor_id, tipo_id) VALUES
(11,'Producto10', 78.84, 1, 5),
(12,'Producto20', 54.12, 1, 4),
(13,'Producto30', 57.73, 1, 3),
(14,'Producto40', 53.81, 1, 2),
(15,'Producto50', 85.56, 1, 1),
(16,'Producto60', 85.56, 1, 5);

INSERT INTO Productos (id,nombre, precio, proveedor_id, tipo_id) VALUES
(6,'Producto6', 78.84, 5, 5),
(7,'Producto7', 54.12, 4, 4),
(8,'Producto8', 57.73, 3, 3),
(9,'Producto9', 53.81, 2, 2),
(10,'Producto10', 85.56, 1, 1);

### --pedidos--
INSERT INTO Pedidos (id,cliente_id,fecha,total,empleado_id) VALUES
(1,1,'2025-01-01',120.99,1),
(2,2,'2025-02-02',170.99,2),
(3,3,'2025-03-03',190.99,3),
(4,4,'2025-04-04',820.99,4),
(5,5,'2025-05-05',180.99,5);

### --DetallesPedidos

INSERT INTO DetallesPedido (id,pedido_id,producto_id,cantidad,precio) VALUES
(1,1,1,10,'70.99'),
(2,2,2,15,'80.99'),
(3,3,3,20,'95.99'),
(4,4,4,25,'99.99'),
(5,5,5,30,'98.99');



# 2.JOINS

## 1. Obtener la lista de todos los pedidos con los nombres de clientes usando INNER JOIN .

SELECT p.id AS pedido_id, c.nombre AS cliente, p.fecha, p.total
FROM Pedidos as p
INNER JOIN Clientes AS c on p.cliente_id = c.id;

## 2.Listar los productos y proveedores que los suministran con INNER JOIN

SELECT pr.nombre AS producto, p.nombre AS proveedor 
FROM Productos AS pr
INNER JOIN Proveedores AS p ON pr.proveedor_id = p.id; 

## 3. Mostrar los pedidos y las ubicaciones de los clientes con LEFT JOIN .

SELECT p.id AS pedido_id, p.fecha, c.nombre AS cliente, u.direccion, u.ciudad, u.pais
FROM Pedidos AS p
LEFT JOIN Clientes AS c ON p.cliente_id = c.id
LEFT JOIN Ubicacion AS u ON c.ubicacion_id = u.id;

## 4. Consultar los empleados que han registrado pedidos, incluyendo empleados sin pedidos
( LEFT JOIN ).

SELECT d.id AS empleados_id, d.nombre AS empleado, d.fecha_contratacion, p.id AS pedido_id, p.fecha AS fecha_pedido 
FROM DatosEmpleados AS d
LEFT JOIN Pedidos AS p ON d.id = p.empleado_id;

## 5. Obtener el tipo de producto y los productos asociados con INNER JOIN .

SELECT p.nombre AS producto, p.precio, t.tipo_nombre AS tipo, t.descripcion
FROM Productos AS p
INNER JOIN TiposProductos AS t ON p.tipo_id = t.id;

## 6. Listar todos los clientes y el número de pedidos realizados con COUNT y GROUP BY .

SELECT c.nombre AS cliente, COUNT(p.id) AS total_pedidos
FROM Clientes AS c
LEFT JOIN Pedidos AS p ON c.id = p.cliente_id
GROUP BY c.id, c.nombre;

## 7. Combinar Pedidos y Empleados para mostrar qué empleados gestionaron pedidos
específicos.

SELECT p.id AS pedido_id, p.fecha AS fecha_pedido, d.nombre AS empleado
FROM Pedidos AS p
INNER JOIN DatosEmpleados AS d ON p.empleado_id = d.id;

## 8. Mostrar productos que no han sido pedidos ( RIGHT JOIN ).

SELECT p.id AS productos_id, p.nombre AS producto
FROM DetallesPedido AS d
RIGHT JOIN Productos AS p ON d.producto_id = p.id
WHERE d.id IS NULL;

## 9. Mostrar el total de pedidos y ubicación de clientes usando múltiples JOIN .

SELECT c.nombre AS cliente, u.ciudad, u.pais, SUM(p.total) AS total_pedidos
FROM Pedidos AS p
INNER JOIN Clientes AS c ON p.cliente_id = c.id
INNER JOIN Ubicacion AS u ON c.ubicacion_id = u.id
GROUP BY c.id, c.nombre, u.ciudad, u.pais;

## 10. Unir Proveedores , Productos , y TiposProductos para un listado completo de inventario.

SELECT p.nombre AS producto, p.precio, prov.nombre AS proveedor, tp.tipo_nombre AS tipo, tp.descripcion
FROM Productos AS p
INNER JOIN Proveedores AS prov ON p.proveedor_id = prov.id
INNER JOIN TiposProductos as tp ON p.tipo_id = tp.id;


# 3. Consultas Simples

## 1. Seleccionar todos los productos con precio mayor a $50

SELECT p.nombre AS nombre, p.precio AS precio
FROM Productos AS p
WHERE p.precio > 50;

## 2. Consultar clientes registrados en una ciudad específica.

SELECT c.id AS cliente_id, c.nombre, u.ciudad AS ciudad
FROM Clientes AS c
INNER JOIN Ubicacion AS u ON c.ubicacion_id = u.id
WHERE u.ciudad LIKE '%Cali%';

## 3. Mostrar empleados contratados en los últimos 2 años.

SELECT d.id AS epledos_id, d.nombre AS nombre, d.fecha_contratacion AS fecha_contratacion
FROM DatosEmpleados AS d
WHERE d.fecha_contratacion >= DATE_SUB(CURDATE(), INTERVAL 2 YEAR);

## 4. Seleccionar proveedores que suministran más de 5 productos.

SELECT p.proveedor_id, prov.nombre AS proveedor, COUNT(*)  AS total_productos
FROM Productos AS p
INNER JOIN Proveedores AS prov ON p.proveedor_id =prov.id
GROUP BY p.proveedor_id, prov.nombre
HAVING COUNT(*) > 5;

## 5. Listar clientes que no tienen dirección registrada en UbicacionCliente .

SELECT c.id, c.nombre
FROM Clientes AS c
LEFT JOIN Ubicacion AS u ON c.ubicacion_id = u.id
WHERE c.ubicacion_id IS NULL;

## 6. Calcular el total de ventas por cada cliente.

SELECT c.id AS cliente_id, c.nombre AS cliente, SUM(p.total) AS total_ventas
FROM Clientes AS c
INNER JOIN Pedidos AS p ON c.id = p.cliente_id 
GROUP BY c.id, c.nombre;

## 7. Mostrar el salario promedio de los empleados.

SELECT AVG(salario) as salario_promedio
FROM Puestos;

## 8. Consultar el tipo de productos disponibles en TiposProductos .

SELECT id, tipo_nombre, descripcion
FROM TiposProductos;

## 9. Seleccionar los 3 productos más caros.

SELECT nombre, precio
FROM Productos
ORDER BY precio DESC LIMIT 3;

## 10. Consultar el cliente con el mayor número de pedidos.

SELECT c.id AS cliente_id, c.nombre, COUNT(p.id) AS total_pedidos
FROM Clientes AS c
INNER JOIN Pedidos AS p ON c.id = p.cliente_id
GROUP BY c.id, c.nombre
ORDER BY total_pedidos DESC LIMIT 1;

# 4. Consultas Multitabla

## 1. Listar todos los pedidos y el cliente asociado.

SELECT p.id AS pedido_id, p.fecha, p.total, c.nombre AS cliente
FROM Pedidos AS p
INNER JOIN Clientes AS c ON p.cliente_id = c.id;

## 2. Mostrar la ubicación de cada cliente en sus pedidos.

SELECT p.id AS pedido_id, c.nombre AS cliente, U.direccion, u.ciudad, u.pais
FROM Pedidos AS p
INNER JOIN Clientes AS c ON p.cliente_id = c.id
INNER JOIN Ubicacion AS u ON c.ubicacion_id = u.id;

## 3. Listar productos junto con el proveedor y tipo de producto.

SELECT p.nombre AS producto, p.precio,prov.nombre AS proveedor, tp.tipo_nombre AS tipo, tp.descripcion
FROM Productos AS p
INNER JOIN Proveedores AS prov ON p.proveedor_id = prov.id
INNER JOIN TiposProductos AS tp ON p.tipo_id = tp.id;

## 4. Consultar todos los empleados que gestionan pedidos de clientes en una ciudad específica.

SELECT d.id AS emplead_id, d.nombre AS empleado, u.ciudad AS ciudad_cliente
FROM DatosEmpleados AS d
INNER JOIN Pedidos AS p ON d.id = p.empleado_id
INNER JOIN Clientes AS c ON p.cliente_id = c.id
INNER JOIN Ubicacion AS u ON c.ubicacion_id = u.id
WHERE u.ciudad LIKE '%Cali%';

## 5. Consultar los 5 productos más vendidos.

SELECT p.id AS producto_id, p.nombre AS producto, SUM(d.cantidad) AS total_vendido
FROM Productos AS p
INNER JOIN DetallesPedido AS d ON p.id = d.producto_id
GROUP BY p.id, p.nombre
ORDER BY total_vendido DESC LIMIT 5;

## 6. Obtener la cantidad total de pedidos por cliente y ciudad.

SELECT c.nombre AS cliente, u.ciudad AS ciudad, COUNT(p.id) AS total_pedidos
FROM Clientes AS c
INNER JOIN Ubicacion AS u ON c.ubicacion_id = u.id
LEFT JOIN Pedidos AS p ON c.id = p.cliente_id
GROUP BY c.id, c.nombre, u.ciudad;


## 7. Listar clientes y proveedores en la misma ciudad.

SELECT c.nombre AS cliente, u1.ciudad AS ciudad, pr.nombre AS proveedor
FROM Clientes AS c
INNER JOIN Ubicacion AS u1 ON c.ubicacion_id = u1.id
INNER JOIN Proveedores AS pr
INNER JOIN Ubicacion AS u2 ON pr.ubicacion_id = u2.id
WHERE u1.ciudad = u2.ciudad;

## 8. Mostrar el total de ventas agrupado por tipo de producto.

SELECT tp.tipo_nombre AS tipo_producto, SUM(dp.precio * dp.cantidad) AS total_ventas
FROM DetallesPedido AS dp
INNER JOIN Productos AS p ON dp.producto_id = p.id
INNER JOIN TiposProductos AS tp ON p.tipo_id = tp.id
GROUP BY tp.tipo_nombre;

## 9. Listar empleados que gestionan pedidos de productos de un proveedor específico.

SELECT e.nombre AS empleado, p.nombre AS producto, pr.nombre AS proveedor
FROM Pedidos AS pe
INNER JOIN DatosEmpleados AS e ON pe.empleado_id = e.id
INNER JOIN DetallesPedido AS dp ON pe.id = dp.pedido_id
INNER JOIN Productos AS p ON dp.producto_id = p.id
INNER JOIN Proveedores AS pr ON p.proveedor_id = pr.id
WHERE pr.nombre = 'Proveedor A'
GROUP BY e.nombre, p.nombre, pr.nombre;

## 10. Obtener el ingreso total de cada proveedor a partir de los productos vendidos.

SELECT pr.nombre AS proveedor, SUM(dp.precio * dp.cantidad) AS ingreso_total
FROM Productos AS p
INNER JOIN Proveedores AS pr ON p.proveedor_id = pr.id
INNER JOIN DetallesPedido AS dp ON p.id = dp.producto_id
GROUP BY pr.nombre
ORDER BY ingreso_total DESC;

# subconsultas

## 1. Consultar el producto más caro en cada categoría.

SELECT p1.nombre, p1.precio, tp.tipo_nombre
FROM productos AS p1
JOIN tiposproductos AS tp ON p1.tipo_id = tp.id
WHERE p1.precio = (
       SELECT MAX(p2.precio)
       FROM productos AS p2
       WHERE p2.tipo_id = p1.tipo_id
);

## 2. Encontrar el cliente con mayor total en pedidos.

SELECT c.nombre
FROM clientes AS c
WHERE c.id =(
       SELECT cliente_id
       FROM pedidos
       GROUP BY cliente_id
       ORDER BY SUM(total) DESC LIMIT 1
);

## 3. Listar empleados que ganan más que el salario promedio.

SELECT de.nombre, p.puesto, p.salario
FROM datosempleados AS de
JOIN puestos AS p ON de.puesto_id = p.id
WHERE p.salario > (
       SELECT AVG(salario)
       FROM puestos
);

## 4. Consultar productos que han sido pedidos más de 5 veces.

SELECT p.nombre
FROM productos AS p
WHERE p.id IN (
       SELECT producto_id
       FROM detallespedido
       GROUP BY producto_id
       HAVING SUM(cantidad) > 5
);

## 5. Listar pedidos cuyo total es mayor al promedio de todos los pedidos.

SELECT p.id, p.cliente_id,p.total, p.fecha
FROM pedidos AS p
WHERE p.total > (
       SELECT AVG(total)
       FROM pedidos
);


## 6. Seleccionar los 3 proveedores con más productos

SELECT nombre, cantidad
FROM (
       SELECT pr.nombre, COUNT(p.id) AS cantidad  
       FROM proveedores AS pr
       JOIN productos AS p ON pr.id = p.proveedor_id
       GROUP BY pr.id
) AS sub
ORDER BY cantidad DESC LIMIT 3;
## 7. Consultar productos con precio superior al promedio en su tipo.
  
  SELECT p1.nombre, p1.precio, tp.tipo_nombre
  FROM productos AS p1
  JOIN tiposproductos AS tp ON p1.tipo_id = tp.id
  WHERE p1.precio > (
       SELECT AVG(p2.precio)
       FROM productos AS p2
       WHERE p2.tipo_id = p1.tipo_id
  ); 

  ## 8. Mostrar clientes que han realizado más pedidos que la media.

SELECT c.nombre, COUNT(p.id) AS cantidad_pedidos
FROM clientes AS c
JOIN pedidos AS p ON c.id = p.cliente_id
GROUP BY c.id
HAVING COUNT(p.id) > (
       SELECT AVG(cantidad)
       FROM (
              SELECT COUNT(*) AS cantidad
              FROM pedidos
              GROUP BY cliente_id
       ) AS sub
);

## 9. Encontrar productos cuyo precio es mayor que el promedio de todos los productos.

 SELECT nombre, precio
 FROM productos
 WHERE precio > (
       SELECT AVG(precio)
       FROM productos
 );

 ## 10. Mostrar empleados cuyo salario es menor al promedio del departamento.

SELECT de.nombre, p1.puesto, p1.salario
FROM datosempleados AS de
JOIN puestos AS p1 ON de.puesto_id = p1.id
WHERE p1.salario < (
       SELECT AVG(p2.salario)
       FROM puestos AS p2 
       WHERE p2.id = de.puesto_id
);

# Procedimientos Almacenados

## 1. Crear un procedimiento para actualizar el precio de todos los productos de un proveedor.

DELIMITER //

CREATE PROCEDURE ActualizarPrecioProveedor(
    IN id_proveedor INT,
    IN porcentaje DECIMAL(5,2)
)
BEGIN
    UPDATE productos
    SET precio = precio * (1 + porcentaje / 100)
    WHERE proveedor_id = id_proveedor;
END;
//

DELIMITER ;

## 2. Un procedimiento que devuelva la dirección de un cliente por ID.

DELIMITER //

CREATE PROCEDURE ObtenerDireccionCliente(
    IN id_cliente INT
)
BEGIN
    DECLARE MESSAGE_TEXT VARCHAR(100);
    IF EXISTS (
        SELECT 1 FROM clientes WHERE id = id_cliente
    )THEN
        SELECT u.direccion, u.ciudad, u.pais
        FROM clientes AS c
        JOIN ubicacion AS u ON c.ubicacion_id = u.id
        WHERE c.id = id_cliente;
    ELSE
        SELECT 'Cliente no encontrado.' AS mensaje;
    END IF;
END;
//
DELIMITER ;


## 3. Crear un procedimiento que registre un pedido nuevo y sus detalles.

DELIMITER //

CREATE PROCEDURE RegistrarPedido(
    IN id_cliente INT,
    IN id_empleado INT,
    IN id_producto INT,
    IN cantidad INT,
    IN precio_unitario DECIMAL(10,2)
)
BEGIN
    DECLARE nuevo_pedido_id INT;
    DECLARE MESSAGE_TEXT VARCHAR(100);
    IF NOT EXISTS(SELECT 1 FROM clientes WHERE id = id_cliente) THEN
        SET MESSAGE_TEXT = 'Cliente no existe.';
    ELSEIF NOT EXISTS (SELECT 1 FROM productos WHERE id = id_producto) THEN
         SET MESSAGE_TEXT = 'Producto no existe.';
    ELSE

        INSERT INTO pedidos (cliente_id, fecha, total, empleado_id)
        VALUES (id_cliente, CURDATE(), cantidad * precio_unitario, id_empleado);

        SET nuevo_pedido_id = LAST_INSERT_ID();

        INSERT INTO detallespedido (pedido_id, producto_id, cantidad, precio)
        VALUES (nuevo_pedido_id, id_producto, cantidad, precio_unitario);

        SELECT'alistado con exito' AS Mensaje;;
    END IF;
END;
//

DELIMITER ;

## 4. Un procedimiento para calcular el total de ventas de un cliente.

DELIMITER //

CREATE PROCEDURE TotalVentasCliente(
    IN id_cliente INT
)
BEGIN
    IF EXISTS (SELECT 1 FROM pedidos WHERE cliente_id = id_cliente) THEN
        SELECT SUM(total) AS total_ventas
        FROM pedidos
        WHERE cliente_id = id_cliente;
    ELSE
        SELECT 'El cliente no tiene pedidos.' AS mensaje;
    END IF;
END;
//

DELIMITER ;

## 5. Crear un procedimiento para obtener los empleados por puesto.

DELIMITER //

CREATE PROCEDURE EmpleadosPorPuesto(
    IN nombre_puesto VARCHAR(50)
)
BEGIN
    IF EXISTS (SELECT 1 FROM puestos WHERE puesto = nombre_puesto) THEN
        SELECT de.nombre
        FROM datosempleados AS de
        JOIN puestos AS p ON de.puesto_id = p.id
        WHERE p.puesto = nombre_puesto;
    ELSE
        SELECT 'puesto no encontrado' AS Mensaje;
    END IF;
END;
//

DELIMITER ;

## 6. Un procedimiento que actualice el salario de empleados por puesto.

DELIMITER //

CREATE PROCEDURE ActualizarSalarioPorPuesto(
    IN nombre_puesto VARCHAR(50),
    IN nuevo_salario DECIMAL(10,2)
)
BEGIN
    IF nuevo_salario <= 0 THEN
        SELECT 'EL SALARIO NO PUEDE SER NEGATIVO CONSIDERE' AS Mensaje;
    ELSEIF EXISTS (SELECT 1 FROM puestos WHERE puesto = nombre_puesto) THEN
        UPDATE puestos
        SET salario = nuevo_salario
        WHERE puesto = nombre_puesto;
    ELSE
        SELECT 'puesto no encontrado' AS Mensaje;
    END IF;
END;
//

DELIMITER ;

## 7. Crear un procedimiento que liste los pedidos entre dos fechas.

DELIMITER //

CREATE PROCEDURE PedidosEntreFechas(
    IN fecha_inicio DATE,
    IN fecha_fin DATE
)
BEGIN
    IF fecha_inicio > fecha_fin THEN
        SELECT 'La fecah de inicio no puede ser mayor que la fecha final.';
    ELSE
        SELECT *
        FROM pedidos
        WHERE fecha BETWEEN fecha_inicio AND fecha_fin;
    END IF;
END;
//

DELIMITER ;

## 8. Un procedimiento para aplicar un descuento a productos de una categoría.

DELIMITER //

CREATE PROCEDURE AplicarDescuentoCategoria(
    IN nombre_categoria VARCHAR(100),
    IN descuento DECIMAL(5,2)
)
BEGIN
    IF descuento <= 0 OR descuento >= 100 THEN
        SELECT 'el descuento debe estar entre 1 y 99.';
    ELSEIF EXISTS (
        SELECT 1 FROM tiposproductos WHERE tipo_nombre = nombre_categoria;
    ) THEN
        UPDATE productos AS p
        JOIN tiposproductos AS tp ON p.tipo_id = tp.id
        SET p.precio = CASE
            WHEN descuento <= 25 THEN p.precio * (1 - descuento /100)
            ELSE p.precio * 0.70
        END
        WHERE tp.tipo_nombre = nombre_ categoria;
    ELSE 
     SELECT 'CATEGORIA NO ENCONTRADA' AS Mensaje;
     END IF;
END;
//

DELIMITER ;


## 9. Crear un procedimiento que liste todos los proveedores de un tipo de producto.

DELIMITER //

CREATE PROCEDURE ProveedoresPorTipoProducto(
    IN nombre_tipo VARCHAR(100)
)
BEGIN
    SELECT DISTINCT pr.nombre
    FROM proveedores AS pr
    JOIN productos AS p ON pr.id = p.proveedor_id
    JOIN tiposproductos AS tp ON p.tipo_id = tp.id
    WHERE tp.tipo_nombre = nombre_tipo;
END;
//

DELIMITER ;

## 10. Un procedimiento que devuelva el pedido de mayor valor.


DELIMITER //

CREATE PROCEDURE PedidoMayorValor()
BEGIN
    SELECT *
    FROM pedidos
    ORDER BY total DESC
    LIMIT 1;
END;
//

DELIMITER ;