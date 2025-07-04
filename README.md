# 🛠️ Taller MySQL

Este taller está diseñado para profundizar en el manejo y optimización de bases de datos **MySQL**.  
A través de ejercicios prácticos, se explorarán temas avanzados para reforzar el conocimiento en:

- Normalización  
- JOINs  
- Consultas complejas  
- Subconsultas  
- Procedimientos almacenados  
- Funciones definidas por el usuario  
- Triggers  

---

## 📋 Requisitos Previos

- Conocimiento básico de SQL y MySQL  
- MySQL instalado y configurado en tu máquina

---

## 🎯 Objetivos del Taller

Al finalizar este taller, el participante será capaz de:

1. Diseñar bases de datos optimizadas mediante técnicas de normalización.
2. Realizar consultas avanzadas en múltiples tablas.
3. Utilizar subconsultas para consultas complejas.
4. Crear y ejecutar procedimientos almacenados y funciones definidas por el usuario.
5. Implementar triggers para automatizar operaciones en la base de datos.

---

## 🧱 Base de Datos – Creación Inicial

```sql
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
  cliente_id INT,
  direccion VARCHAR(255),
  ciudad VARCHAR(100),
  estado VARCHAR(50),
  codigo_postal VARCHAR(10),
  pais VARCHAR(50),
  FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);

-- Tabla Empleados
CREATE TABLE Empleados (
  id INT PRIMARY KEY AUTO_INCREMENT,
  nombre VARCHAR(100),
  puesto VARCHAR(50),
  salario DECIMAL(10, 2),
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
  precio DECIMAL(10, 2),
  proveedor_id INT,
  tipo_id INT,
  FOREIGN KEY (proveedor_id) REFERENCES Proveedores(id),
  FOREIGN KEY (tipo_id) REFERENCES TiposProductos(id)
);

-- Tabla Pedidos
CREATE TABLE Pedidos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  cliente_id INT,
  fecha DATE,
  total DECIMAL(10, 2),
  FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);

-- Tabla DetallesPedido
CREATE TABLE DetallesPedido (
  id INT PRIMARY KEY AUTO_INCREMENT,
  pedido_id INT,
  producto_id INT,
  cantidad INT,
  precio DECIMAL(10, 2),
  FOREIGN KEY (pedido_id) REFERENCES Pedidos(id),
  FOREIGN KEY (producto_id) REFERENCES Productos(id)
);
