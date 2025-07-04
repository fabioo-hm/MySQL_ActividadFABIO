# MYSQL Ejercicio

## 1. Creación de la base de datos:

```sql
CREATE DATABASE ejercicioFAHM;
USE ejercicioFAHM;
```

## 2. Normalización y creación de tablas:

```sql
CREATE TABLE IF NOT EXISTS Puestos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    puesto VARCHAR(50) NOT NULL
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS DatosEmpleados (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100) NOT NULL,
    salario DECIMAL(10,2) NOT NULL,
    puesto_id INT NOT NULL,
    CONSTRAINT FK_puesto_id FOREIGN KEY (puesto_id) REFERENCES Puestos(id),
    fecha_contrato DATE NOT NULL
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS Paises (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre_país VARCHAR(60) UNIQUE
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS Regiones (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre_región VARCHAR(60) UNIQUE,
    país_id INT NOT NULL
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS Código_postal (
    id INT PRIMARY KEY AUTO_INCREMENT,
    descripción VARCHAR(10) UNIQUE
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS Ciudades (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre_ciudad VARCHAR(60) UNIQUE,
    región_id INT NOT NULL,
    código_id INT NOT NULL
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS Ubicaciones (
    id INT PRIMARY KEY AUTO_INCREMENT,
    dirección VARCHAR(255) NOT NULL ,
    ciudad_id INT NOT NULL,
    CONSTRAINT FK_ciudad_id FOREIGN KEY (ciudad_id) REFERENCES Ciudades(id)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS TipoContacto (
    id INT PRIMARY KEY AUTO_INCREMENT,
    tipo VARCHAR(50) NOT NULL
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS TelefonoClientes (
    id INT PRIMARY KEY AUTO_INCREMENT,
    descripcion VARCHAR(100) NOT NULL,
    tipo_id INT NULL,
    CONSTRAINT FK_tipotelC_id FOREIGN KEY (tipo_id) REFERENCES TipoContacto(id)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS EmailClientes (
    id INT PRIMARY KEY AUTO_INCREMENT,
    descripcion VARCHAR(100) NOT NULL,
    tipo_id INT NOT NULL,
    CONSTRAINT FK_tipoEmailC_id FOREIGN KEY (tipo_id) REFERENCES TipoContacto(id)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS ContactoProveedores (
    id INT PRIMARY KEY AUTO_INCREMENT,
    descripcion VARCHAR(100) NOT NULL,
    tipo_id INT,
    CONSTRAINT FK_tipotel_id FOREIGN KEY (tipo_id) REFERENCES TipoContacto(id)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS Tipos_productos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    tipo_nombre VARCHAR(100) NOT NULL,
    descripción TEXT NOT NULL,
    padre_id INT NULL,
    CONSTRAINT FK_padre_id FOREIGN KEY (padre_id) REFERENCES Tipos_productos(id)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS Proveedores ( 
    id INT PRIMARY KEY AUTO_INCREMENT, 
    nombre VARCHAR(100) NOT NULL, 
    contactoproveedores_id INT NOT NULL, 
    ubicacion_id INT, 
    CONSTRAINT FK_contactoproveedores_id FOREIGN KEY (contactoproveedores_id) REFERENCES ContactoProveedores(id),
    CONSTRAINT FK_ubicacionprovee_id FOREIGN KEY (ubicacion_id) REFERENCES Ubicaciones(id)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS EmpleadosProveedores (
    empleados_id INT NOT NULL,
    proveedores_id INT NOT NULL,
    PRIMARY KEY (empleados_id, proveedores_id),
    FOREIGN KEY (empleados_id) REFERENCES DatosEmpleados(id), FOREIGN KEY (proveedores_id) REFERENCES Proveedores(id)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS Productos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100) NOT NULL,
    precio DECIMAL(10,2) NOT NULL,
    proveedor_id INT NOT NULL,
    tipoproducto_id INT NULL,
    CONSTRAINT FK_proveedor_id FOREIGN KEY (proveedor_id) REFERENCES EmpleadosProveedores(proveedores_id),
    CONSTRAINT FK_tipoproducto_id FOREIGN KEY (tipoproducto_id) REFERENCES Tipos_productos(id)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS Clientes (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100) NOT NULL,
    emailclientes_id INT NOT NULL,
    CONSTRAINT FK_emailclientes_id FOREIGN KEY (emailclientes_id) REFERENCES EmailClientes(id),
    teléfonosclientes_id INT NOT NULL,
    CONSTRAINT FK_teléfonosclientes_id FOREIGN KEY (teléfonosclientes_id) REFERENCES TelefonoClientes(id),
    ubicación_id INT,
    CONSTRAINT FK_ubicación_id FOREIGN KEY (ubicación_id) REFERENCES Ubicaciones(id)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS Pedidos(
    id INT PRIMARY KEY AUTO_INCREMENT,
    cliente_id INT NOT NULL,
    CONSTRAINT FK_cliente_id FOREIGN KEY (cliente_id) REFERENCES Clientes(id),
    fecha DATE NOT NULL,
    total DECIMAL(10,2) NOT NULL
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS PedidosEmpleados ( 
    empleado_id INT NOT NULL, 
    pedido_id INT, 
    PRIMARY KEY (empleado_id, pedido_id), 
    CONSTRAINT FK_pe_empleado FOREIGN KEY (empleado_id) REFERENCES DatosEmpleados(id), 
    CONSTRAINT FK_pe_pedido FOREIGN KEY (pedido_id) REFERENCES Pedidos(id)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS HistorialPedidos(
    id INT PRIMARY KEY AUTO_INCREMENT,
    pedido_id INT NOT NULL,
    CONSTRAINT FK_pedido_id FOREIGN KEY (pedido_id) REFERENCES Pedidos(id),
    fecha_modificación DATE NOT NULL,
    estado_anterior VARCHAR(50) NOT NULL,
    estado_actual VARCHAR(50) NOT NULL,
    detalle TEXT NOT NULL
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS DetallesPedidos(
    id INT PRIMARY KEY AUTO_INCREMENT,
    pedido_id INT NOT NULL,
    CONSTRAINT FK_pedidoo_id FOREIGN KEY (pedido_id) REFERENCES Pedidos(id),
    producto_id INT NOT NULL,
    CONSTRAINT FK_productos_id FOREIGN KEY (producto_id) REFERENCES Productos(id),
    cantidad INT NOT NULL,
    precio DECIMAL(10,2) NOT NULL
) ENGINE = INNODB;

ALTER TABLE DatosEmpleados ADD COLUMN ciudad_id INT,
ADD CONSTRAINT FK_city_empleado FOREIGN KEY (ciudad_id) REFERENCES Ciudades(id);
```

## 3. Inserts:

```sql
INSERT INTO Puestos(id,puesto) VALUES 
(1,'Gerente'),
(2,'Vendedor'),
(3,'Administrador');

INSERT INTO Paises(id,nombre_país) VALUES
(1,'Colombia');

INSERT INTO Regiones(id,nombre_región,país_id) VALUES
(1,'Antioquia',1),
(2,'Santander',1);

INSERT INTO Código_postal(id,descripción) VALUES
(1,'050001'),
(2,'680001');

INSERT INTO Ciudades(id,nombre_ciudad,región_id,código_id) VALUES
(1,'Medellín',1,1),
(2,'Bucaramanga',2,2);

INSERT INTO DatosEmpleados(id,nombre,salario,puesto_id,fecha_contrato, ciudad_id) VALUES
(1, 'Andrés Suárez', 3500000.00, 1, '2024-05-01',1),
(2, 'Miguel Rojas', 2000000.00, 2, '2022-01-15',2),
(3, 'Fabio Hernández', 2700000.00, 1, '2025-03-20',1);

INSERT INTO Ubicaciones(id,dirección,ciudad_id) VALUES
(1,'Carrera 25 #26-08', 2), 
(2,'Av. Siempre Viva 742', 1),
(3,'Calle 60 #9-143',2),
(4,'Tv. 93 #34-99',2),
(5,'Cra. 25a #1a Sur 45',1);

INSERT INTO TipoContacto(id,tipo) VALUES
(1,'Corporativo'),
(2,'Personal');

INSERT INTO TelefonoClientes(id,descripcion,tipo_id) VALUES
(1,'3158325271',2),
(2,'3167695142',2),
(3,'6387724',1),
(4,'3159170124',2),
(5,'3124532799',2);

INSERT INTO EmailClientes(id,descripcion,tipo_id) VALUES
(1,'juanchitomanolargo@gmail.com',2),
(2,'internautas2005@gmail.com',1),
(3,'empresajuarez@gmail.com',1),
(4,'josefer2007@gmail.com',2),
(5,'simonalcachofa@gmail.com',2);

INSERT INTO ContactoProveedores(id,descripcion,tipo_id) VALUES
(1,'3134601033',1),
(2,'3138430142',1);

INSERT INTO Tipos_productos(id,tipo_nombre,descripción,padre_id) VALUES
(1, 'Electrónica', 'Productos electrónicos',NULL), 
(2, 'Accesorios', 'Accesorios tecnológicos', 1);

INSERT INTO Proveedores(id,nombre,contactoproveedores_id,ubicacion_id) VALUES
(1, 'Alkatronix',1,4),
(2, 'Electronix',2,5);

INSERT INTO EmpleadosProveedores(empleados_id,proveedores_id) VALUES
(1,1),
(2,2);

INSERT INTO Productos(id,nombre,precio,proveedor_id,tipoproducto_id) VALUES
(1,'Laptop Dell',1200.00,1,1), 
(2,'Cable HDMI',15.00,2,2), 
(3,'Mouse inalámbrico',25.00,1,2), 
(4,'Teclado mecánico',85.00,2,2), 
(5,'Webcam HD',50.00,1,2),
(6,'Monitores QLED',300.00,1,1),
(7,'Audífonos Gamers',35.00,1,2),
(8,'USBs',10.00,2,2),
(9,'Luces Led',30.00,1,2),
(10,'Micróno UHQ',250.00,1,2);

INSERT INTO Clientes(id,nombre,emailclientes_id,teléfonosclientes_id,ubicación_id) VALUES
(1,'Juan López',1,1,1), 
(2,'María Gómez',2,2,2),
(3,'Juan Alcachofa',3,3,3),
(4,'José Fernández',4,4,4),
(5,'Simón Rubiano',5,5,NULL);

INSERT INTO Pedidos(id,cliente_id,fecha,total) VALUES
(1,1,'2025-06-01',1215000.00), 
(2,2,'2025-06-05',10000000.00),
(3,4,'2025-06-19',3000000.00);

INSERT INTO PedidosEmpleados(empleado_id, pedido_id) VALUES
(1,1),
(3,2);

INSERT INTO HistorialPedidos(id,pedido_id,fecha_modificación,estado_anterior,estado_actual,detalle) VALUES
(1,1,'2024-06-01','en proceso','verificado','el cliente recibió su pedido'),
(2,2,'2024-06-05','en proceso','cancelado','el cliente canceló su pedido');

INSERT INTO DetallesPedidos(id,pedido_id,producto_id,cantidad,precio) VALUES
(1,1,1,1,1200.00), 
(2,1,2,1,15.00), 
(3,2,2,2,30.00), 
(4,2,3,1,25.00), 
(5,2,5,1, 45.00);
```

## 4. Joins:

```sql
SELECT pe.id, pe.fecha AS FechaPedido, pe.total AS TotalPedido ,
cl.nombre AS Cliente
FROM Pedidos AS pe
INNER JOIN Clientes AS cl ON cl.id = pe.cliente_id;

SELECT pro.id, pro.nombre AS Productos,
prov.nombre
FROM Productos AS pro
INNER JOIN Proveedores AS prov ON pro.proveedor_id = prov.id;

SELECT pe.id AS Pedido,
cl.nombre AS Cliente,
ubi.dirección AS Dirección,
ct.nombre_ciudad AS Ciudad,
re.nombre_región AS Departamento,
p.nombre_país AS País
FROM Pedidos AS pe
LEFT JOIN Clientes AS cl ON cl.id = pe.cliente_id
LEFT JOIN Ubicaciones AS ubi ON ubi.id = cl.ubicación_id
LEFT JOIN Ciudades AS ct ON ct.id = ubi.ciudad_id
LEFT JOIN Regiones AS re ON re.id = ct.región_id
LEFT JOIN Paises AS p ON p.id = re.país_id;

SELECT de.nombre AS Empleado,
pe.pedido_id AS Pedido
FROM DatosEmpleados AS de
LEFT JOIN PedidosEmpleados pe ON de.id = pe.empleado_id;

SELECT tp.tipo_nombre AS TipoProducto,
pro.id, pro.nombre AS Producto
FROM Productos pro
INNER JOIN Tipos_productos tp ON tp.id = pro.tipoproducto_id;

SELECT cl.nombre AS Cliente,
COUNT(pe.id) AS TotalPedidos
FROM Clientes AS cl
LEFT JOIN Pedidos pe ON cl.id = pe.cliente_id
GROUP BY cl.id, cl.nombre;

SELECT pe.pedido_id AS Pedido, p.fecha AS Fecha,
de.nombre AS Empleado
FROM PedidosEmpleados pe
JOIN Pedidos AS p ON p.id = pe.pedido_id
JOIN DatosEmpleados de ON de.id = pe.empleado_id;

SELECT pro.nombre AS Producto
FROM DetallesPedidos de
RIGHT JOIN Productos pro ON pro.id = de.producto_id
WHERE de.id IS NULL;

SELECT cl.nombre AS Cliente,
COUNT(pe.id) AS TotalPedidos,
ubi.dirección AS Dirección,
ct.nombre_ciudad AS Ciudad,
re.nombre_región AS Departamento,
pa.nombre_país AS País
FROM Clientes cl
LEFT JOIN Pedidos pe ON pe.cliente_id = cl.id
JOIN Ubicaciones ubi ON ubi.id = cl.ubicación_id
JOIN Ciudades ct ON ct.id = ubi.ciudad_id
JOIN Regiones re ON re.id = ct.región_id
JOIN Paises pa ON pa.id = re.país_id
GROUP BY cl.id;

SELECT prov.nombre AS Proveedor,
pro.nombre AS Producto,
tp.tipo_nombre AS TipoProducto
FROM Productos pro
JOIN Proveedores prov ON prov.id = pro.proveedor_id
JOIN Tipos_productos tp ON tp.id = pro.tipoproducto_id;
```

## 5. Consultas Simples:

```sql
SELECT pro.id, pro.nombre AS Producto, pro.precio AS Precio
FROM Productos pro
WHERE precio > 50.00;

SELECT cl.id, cl.nombre AS Nombre,
ct.nombre_ciudad
FROM Clientes cl
JOIN Ubicaciones ubi ON ubi.id = cl.ubicación_id
JOIN Ciudades ct ON ct.id = ubi.ciudad_id
WHERE nombre_ciudad = 'Bucaramanga';

SELECT de.nombre AS Empleado,
de.fecha_contrato AS FechaContratación
FROM DatosEmpleados de
WHERE fecha_contrato >= DATE_SUB(CURDATE(), INTERVAL 2 YEAR);

SELECT prov.nombre AS Proveedor, COUNT(p.id) AS Productos
FROM Proveedores prov
JOIN Productos p ON prov.id = p.proveedor_id
GROUP BY prov.id HAVING COUNT(p.id) > 5;

SELECT cl.nombre AS Cliente
FROM Clientes cl
WHERE ubicación_id IS NULL;

SELECT cl.nombre,
SUM(p.total) AS TotalVentas
FROM Clientes cl
JOIN Pedidos p ON cl.id = p.cliente_id
GROUP BY cl.id;

SELECT AVG(salario) AS SalarioPromedio
FROM DatosEmpleados;

SELECT tp.id, tp.tipo_nombre AS TipoProducto
FROM Tipos_productos tp;

SELECT pro.id, pro.nombre AS Producto, pro.precio AS Precio
FROM Productos pro
ORDER BY precio DESC
LIMIT 3;

SELECT cl.id, cl.nombre AS Cliente,
COUNT(pe.id) AS TotalPedidos
FROM Clientes cl
JOIN Pedidos pe ON pe.cliente_id = cl.id
GROUP BY cl.id
ORDER BY TotalPedidos DESC
LIMIT 1;
```

## 6. Consultas Multitablas:

```sql
SELECT pe.id AS IdPedido,
cl.nombre AS Cliente
FROM Pedidos pe
JOIN Clientes cl ON cl.id = pe.cliente_id;

SELECT pe.id AS IdPedido,
cl.nombre AS Cliente,
ubi.dirección AS Dirección,
ct.nombre_ciudad AS Ciudad,
re.nombre_región AS Departamento,
pa.nombre_país AS País
FROM Pedidos pe
JOIN Clientes cl ON cl.id = pe.cliente_id
JOIN Ubicaciones ubi ON ubi.id = cl.ubicación_id
JOIN Ciudades ct ON ct.id = ubi.ciudad_id
JOIN Regiones re ON re.id = ct.región_id
JOIN Paises pa ON pa.id = re.país_id;

SELECT pro.id, pro.nombre AS Producto,
tp.tipo_nombre AS TiposProductos,
prov.nombre AS Proveedor
FROM Productos pro
JOIN Tipos_productos tp ON tp.id = pro.tipoproducto_id
JOIN Proveedores prov ON prov.id = pro.proveedor_id;

SELECT DISTINCT de.nombre AS Empleado,
ct.nombre_ciudad AS Ciudad
FROM PedidosEmpleados pe
JOIN DatosEmpleados de ON pe.empleado_id = de.id
JOIN Pedidos p ON pe.pedido_id = p.id
JOIN Clientes cl ON p.cliente_id = cl.id
JOIN Ubicaciones ubi ON cl.ubicación_id = ubi.id
JOIN Ciudades ct ON ubi.ciudad_id = ct.id
WHERE ct.nombre_ciudad = 'Medellín';

SELECT pro.id, pro.nombre AS Producto,
SUM(dp.cantidad) AS Total
FROM DetallesPedidos dp
JOIN Productos pro ON dp.producto_id = pro.id
GROUP BY pro.id
ORDER BY Total DESC
LIMIT 5;

SELECT cl.nombre AS Cliente,
ct.nombre_ciudad AS Ciudad,
COUNT(pe.id) AS TotalPedidos
FROM Clientes cl
JOIN Pedidos pe ON cl.id = pe.cliente_id
JOIN Ubicaciones ubi ON ubi.id = cl.ubicación_id
JOIN Ciudades ct ON ct.id = ubi.ciudad_id
GROUP BY cl.id, ct.nombre_ciudad;

SELECT DISTINCT cl.nombre AS Cliente,
prov.nombre AS Proveedor,
ct.nombre_ciudad AS Ciudad
FROM Clientes cl
JOIN Ubicaciones ubi ON cl.ubicación_id = ubi.id
JOIN Ciudades ct ON ubi.ciudad_id = ct.id
JOIN Ubicaciones ubi2 ON ct.id = ubi2.ciudad_id
JOIN Proveedores prov ON prov.ubicacion_id = ubi2.id;

SELECT tp.tipo_nombre AS TiposProductos,
SUM(dp.precio * dp.cantidad) AS TotalVentas
FROM DetallesPedidos dp
JOIN Productos p ON p.id = dp.producto_id
JOIN Tipos_productos tp ON tp.id = p.tipoproducto_id
GROUP BY tp.id;

SELECT DISTINCT de.nombre AS Empleado,
prov.nombre AS Proveedor
FROM PedidosEmpleados pe
JOIN DatosEmpleados de ON de.id = pe.empleado_id
JOIN Pedidos p ON p.id = pe.pedido_id
JOIN DetallesPedidos dp ON dp.pedido_id = p.id
JOIN Productos pro ON pro.id = dp.producto_id
JOIN Proveedores prov ON prov.id = pro.proveedor_id
WHERE prov.id = 2;

SELECT prov.nombre AS Proveedor,
SUM(dp.precio * dp.cantidad) AS IngresoTotal
FROM Proveedores prov
JOIN Productos pro ON prov.id = pro.proveedor_id
JOIN DetallesPedidos dp ON pro.id = dp.producto_id
GROUP BY prov.id;
```
## 7. Subconsultas:
```sql
SELECT tp.tipo_nombre AS categoria, p.nombre AS producto, p.precio
FROM Productos p
JOIN Tipos_productos tp ON p.tipoproducto_id = tp.id
WHERE (p.tipoproducto_id, p.precio) IN (
    SELECT tipoproducto_id, MAX(precio)
    FROM Productos
    GROUP BY tipoproducto_id
);

SELECT cl.nombre AS Cliente, sub.cantidad_pedidos
FROM Clientes cl
JOIN (
    SELECT cliente_id, COUNT(pe.id) AS cantidad_pedidos
    FROM Pedidos pe
    GROUP BY cliente_id
    ORDER BY cantidad_pedidos DESC
    LIMIT 1
) sub ON cl.id = sub.cliente_id;

SELECT em.nombre AS Nombre_Empleado, em.salarios AS Salario
FROM DatosEmpleados em
WHERE em.salario > (
    SELECT AVG(em.salario)
    FROM DatosEmpleados em
);

SELECT p.nombre AS Producto
FROM Productos p
WHERE p.id IN (
    SELECT dp.producto_id
    FROM DetallesPedidos dp
    GROUP BY dp.producto_id
    HAVING SUM(dp.cantidad) > 5 
);

SELECT pe.id AS Pedido
FROM Pedidos pe
WHERE pe.total > (
    SELECT AVG(pe.total)
    FROM Pedidos pe
);

SELECT p.nombre AS Proveedor
FROM Proveedores p
JOIN (
    SELECT ep.proveedores_id
    FROM EmpleadosProveedores ep
    JOIN Productos pro ON ep.proveedores_id = pro.proveedor_id
    GROUP BY ep.proveedores_id
    ORDER BY COUNT(pro.id) ASC
    LIMIT 3) AS top_pro ON p.id = top_pro.proveedores_id;

SELECT tp.tipo_nombre AS Categoria, p.nombre AS Producto, p.precio
FROM Productos p
JOIN Tipos_productos tp ON p.tipoproducto_id = tp.id
WHERE p.precio > (
    SELECT AVG(pro.precio)
    FROM Productos pro
    WHERE pro.tipoproducto_id = p.tipoproducto_id
);

SELECT c.nombre, COUNT(p.id) AS total_pedidos
FROM Clientes c
JOIN Pedidos p ON c.id = p.cliente_id
GROUP BY c.id, c.nombre
HAVING COUNT(p.id) > (
    SELECT AVG(pedidos_por_cliente)
    FROM (
        SELECT COUNT(pe.id) AS pedidos_por_cliente
        FROM Pedidos pe
        GROUP BY cliente_id
    ) AS sub
);

SELECT pro.nombre AS Producto
FROM Productos pro
WHERE pro.precio > (
    SELECT AVG(p2.precio)
    FROM Productos p2
);

SELECT e.nombre AS Empleado, e.salario, r.nombre_región
FROM DatosEmpleados e
JOIN Ciudades c ON e.ciudad_id = c.id
JOIN Regiones r ON c.región_id = r.id
WHERE e.salario < (
    SELECT AVG(e2.salario)
    FROM DatosEmpleados e2
    JOIN Ciudades c2 ON e2.ciudad_id = c2.id
    JOIN Regiones r2 ON c2.región_id = r2.id
    WHERE r2.id = r.id
);
```
## 8. Procedimientos Almacenados:
```sql
DELIMITER $$

CREATE PROCEDURE actualizar_precios_proveedor(
    IN proveedorID INT,
    IN porcentaje DECIMAL(5,2)
)
BEGIN
    UPDATE Productos
    SET precio = precio * (1 + porcentaje / 100)
    WHERE proveedor_id = proveedorID;
END$$

DELIMITER ;

DELIMITER $$

CREATE PROCEDURE obtener_direccion_cliente(
    IN clienteID INT
)
BEGIN
    SELECT u.dirección
    FROM Clientes c
    JOIN Ubicaciones u ON c.ubicación_id = u.id
    WHERE c.id = clienteID;
END$$

DELIMITER ;

DELIMITER $$

CREATE PROCEDURE registrar_pedido(
    IN clienteID INT,
    IN fechaPedido DATE,
    IN totalPedido DECIMAL(10,2),
    IN productoID INT,
    IN cantidad INT,
    IN precioUnidad DECIMAL(10,2)
)
BEGIN
    DECLARE nuevoPedidoID INT;

    INSERT INTO Pedidos (cliente_id, fecha, total)
    VALUES (clienteID, fechaPedido, totalPedido);

    SET nuevoPedidoID = LAST_INSERT_ID();

    INSERT INTO DetallesPedidos (pedido_id, producto_id, cantidad, precio)
    VALUES (nuevoPedidoID, productoID, cantidad, precioUnidad);
END$$

DELIMITER ;

DELIMITER $$

CREATE PROCEDURE total_ventas_cliente(
    IN clienteID INT
)
BEGIN
    SELECT SUM(total) AS total_ventas
    FROM Pedidos
    WHERE cliente_id = clienteID;
END$$

DELIMITER ;

DELIMITER $$

CREATE PROCEDURE empleados_por_puesto(
    IN puestoID INT
)
BEGIN
    SELECT id, nombre, salario
    FROM DatosEmpleados
    WHERE puesto_id = puestoID;
END$$

DELIMITER ;

DELIMITER $$

CREATE PROCEDURE actualizar_salario_por_puesto(
    IN puestoID INT,
    IN porcentaje DECIMAL(5,2)
)
BEGIN
    UPDATE DatosEmpleados
    SET salario = salario * (1 + porcentaje / 100)
    WHERE puesto_id = puestoID;
END$$

DELIMITER ;

DELIMITER $$

CREATE PROCEDURE pedidos_entre_fechas(
    IN fechaInicio DATE,
    IN fechaFin DATE
)
BEGIN
    SELECT *
    FROM Pedidos
    WHERE fecha BETWEEN fechaInicio AND fechaFin;
END$$

DELIMITER ;

DELIMITER $$

CREATE PROCEDURE aplicar_descuento_categoria(
    IN categoriaID INT,
    IN porcentaje DECIMAL(5,2)
)
BEGIN
    UPDATE Productos
    SET precio = precio * (1 - porcentaje / 100)
    WHERE tipoproducto_id = categoriaID;
END$$

DELIMITER ;

DELIMITER $$

CREATE PROCEDURE proveedores_por_tipo_producto(
    IN tipoID INT
)
BEGIN
    SELECT DISTINCT p.id, p.nombre
    FROM Proveedores p
    JOIN EmpleadosProveedores ep ON p.id = ep.proveedores_id
    JOIN Productos pr ON ep.proveedores_id = pr.proveedor_id
    WHERE pr.tipoproducto_id = tipoID;
END$$

DELIMITER ;

DELIMITER $$

CREATE PROCEDURE pedido_mayor_valor()
BEGIN
    SELECT *
    FROM Pedidos
    ORDER BY total DESC
    LIMIT 1;
END$$

DELIMITER ;
```
### 8.1. Calls:
```sql
CALL actualizar_precios_proveedor(1, 10);
CALL obtener_direccion_cliente(3);
CALL registrar_pedido(2, '2025-07-04', 150.00, 10, 2, 75.00);
CALL total_ventas_cliente(2);
CALL empleados_por_puesto(1);
CALL actualizar_salario_por_puesto(1, 5);
CALL pedidos_entre_fechas('2025-01-01', '2025-06-30');
CALL aplicar_descuento_categoria(2, 20);
CALL proveedores_por_tipo_producto(2);
CALL pedido_mayor_valor();
```