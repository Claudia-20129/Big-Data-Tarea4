# Big-Data-Tarea4
Código de las consultas en  MongoDB y HBase


# Código de las consultas en  MongoDB y HBase

# MongoDB
1. Diseño de la Base de Datos
Caso de Uso: Almacenamiento de datos de una red social.
Vamos a crear una base de datos para almacenar información de usuarios, publicaciones y comentarios.

Nombre de la base de Datos: Social_Network
Esquema de la base de datos:
•	Colección users: Contendrá información de los usuarios.
{
  "_id": "ObjectId",
  "username": "string",
  "email": "string",
  "created_at": "date",
  "profile": {
    "name": "string",
    "bio": "string",
    "age": "number"
  }
}

Colección posts: Contendrá publicaciones de los usuarios.
{
  "_id": "ObjectId",
  "user_id": "ObjectId",
  "content": "string",
  "created_at": "date",
  "tags": ["string"],
  "likes": "number"
}


Colección comments: Almacena los comentarios de las publicaciones.
{
  "_id": "ObjectId",
  "post_id": "ObjectId",
  "user_id": "ObjectId",
  "comment": "string",
  "created_at": "date"
}

2. Implementación en MongoDB
•	Creación de la Base de Datos y Colecciones
Ejecutar en la consola de MongoDB
Este código generara datos de prueba automáticamente

use social_network; // Cambia el nombre de la base si es necesario

// Generar 100 usuarios
for (let i = 1; i <= 100; i++) {
  db.users.insertOne({
    username: `user${i}`,
    email: `user${i}@example.com`,
    created_at: new Date(),
    profile: {
      name: `User ${i}`,
      bio: `This is user ${i}'s bio.`,
      age: Math.floor(Math.random() * 40) + 20 // Edad entre 20 y 60
    }
  });
}

// Generar 100 publicaciones
for (let i = 1; i <= 100; i++) {
  db.posts.insertOne({
    user_id: db.users.find().limit(1).skip(Math.floor(Math.random() * 100)).toArray()[0]._id,
    content: `This is post ${i} content.`,
    created_at: new Date(),
    tags: ["tag" + (Math.floor(Math.random() * 10) + 1)],
    likes: Math.floor(Math.random() * 100) // Likes entre 0 y 99
  });
}

// Generar 100 comentarios
for (let i = 1; i <= 100; i++) {
  db.comments.insertOne({
    post_id: db.posts.find().limit(1).skip(Math.floor(Math.random() * 100)).toArray()[0]._id,
    user_id: db.users.find().limit(1).skip(Math.floor(Math.random() * 100)).toArray()[0]._id,
    comment: `This is comment ${i}`,
    created_at: new Date()
  });
}

print("Datos de prueba insertados correctamente.");


Validación de Datos
Después de ejecutar el código, vamos a validar que los datos se hayan insertado correctamente con estas consultas:
// Contar los documentos insertados
db.users.countDocuments();    // Debe ser 100
db.posts.countDocuments();    // Debe ser 100
db.comments.countDocuments(); // Debe ser 100

•	Operaciones
1.	Consultas básicas (inserción, selección, actualización y eliminación de documentos).
Inserción:
db.users.insertOne({
  username: "newuser",
  email: "newuser@example.com",
  created_at: new Date(),
  profile: { name: "New User", bio: "Loves databases", age: 25 }
});

Selección:
db.users.find({ "profile.age": { $gt: 20 } });

Actualización:
db.posts.updateOne(
  { content: "Hello World!" },
  { $set: { likes: 20 } }
);


Eliminación de Documentos:
db.comments.deleteOne({ comment: "I agree!" });

2.	 Consultas con filtros y operadores. 
Buscar publicaciones con más de 10 "likes":
db.posts.find({ likes: { $gt: 10 } });

Filtrar usuarios con nombre que empiece con "U":
db.users.find({ "profile.name": { $regex: "^U" } });

3.	Consultas de agregación para calcular estadísticas (contar, sumar, promediar, etc.).
Contar el número de publicaciones:
db.posts.aggregate([{ $count: "total_posts" }]);

Sumar el total de "likes" de todas las publicaciones:
db.posts.aggregate([{ $group: { _id: null, total_likes: { $sum: "$likes" } } }]);

Calcular el promedio de edad de los usuarios:
db.users.aggregate([{ $group: { _id: null, average_age: { $avg: "$profile.age" } } }]);


# HBase
Ingresamos a la máquina virtual configurada con hadoop
Usuario: vboxuser  
Password: bigdata
 
Ejecutamos Putty para conectarnos por SSH a la máquina virtual utilizando la IP local
 
Ingresamos nuevamente 
Usuario: vboxuser  
Password: bigdata
 
Descargamos Hbase 
wget https://downloads.apache.org/hbase/2.6.1/hbase-2.6.1-bin.tar.gz
 
Extraemos el archivo y movemos la carpeta 
tar xzf hbase-2.6.1-bin.tar.gz
 
sudo mv hbase-2.6.1 /usr/local/hbase
Si llega a solicitar contraseña: bigdata 
 
Configuramos las Variables de Entorno 
Editamos el archivo ~/.bashrc con nano: 
nano ~/.bashrc
 
Colocamos las líneas siguientes al final del archivo.  
export HBASE_HOME=/usr/local/hbase 
export PATH=$PATH:$HBASE_HOME/bin
copiar las líneas y damos Crtl+O enter y luego Crtl+X para salir 
 

Cargamos las variables de entorno: 
Cargue la configuración anterior en entorno actual 
source ~/.bashrc
 

Configuramos HBase 
Editamos el archivo hbase-site.xml para configurarlo en modo local, añadiendo las siguientes 
propiedades: 
sudo nano /usr/local/hbase/conf/hbase-site.xml
 

Vamos a agregar la siguiente configuración al final del archivo y antes de la etiqueta de cierre 
</configuration> 
<property> 
<name>hbase.rootdir</name> 
<value>file:///usr/local/hbase/data</value> 
</property> 
<property> 
<name>hbase.zookeeper.property.dataDir</name> 
<value>/usr/local/hbase/zookeeper</value> 
</property> 
<property> 
<name>hbase.cluster.distributed</name> 
<value>false</value> 
</property>
Después de copiar las líneas, damos Crtl+O enter y luego Crtl+X para salir
 
Vamos a editar el archivo hbase-env.sh  
sudo nano /usr/local/hbase/conf/hbase-env.sh
 
Ahora agregamos la línea siguiente al final del archivo.  
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
Y de nuevo damos Crtl+O enter y luego Crtl+X para salir
 

Iniciamos HBase 
Iniciamos el servicio de HBase: 
start-hbase.sh
 

Verificamos que HBase está corriendo: 
hbase Shell
Este proceso puede demorar algunos minutos
 
 
Verificamos el status del servidor 
Status
 
Digitamos exit para salir del Shell de Hbase
 

Instalación de Python y Dependencias 
Usaremos Python con happybase, una librería que permite interactuar con HBase desde 
Python 
pip install happybase
 
Al final verificamos que la instalación fue satisfactoria
 
 

Ejecutamos el servidor thrift 
hbase-daemon.sh start thrift
 

Verificación de la Instalación 
Probamos la conexión desde Python, para esto creamos un archivo de prueba test_hbase.py: 
nano test_hbase.py
 
Copiamos las siguientes líneas y de nuevo damos Crtl+O enter y luego Crtl+X para salir
import happybase 
try: 
# Intentar establecer conexión 
connection = happybase.Connection('localhost') 
# Listar las tablas existentes 
print("Tablas existentes:", connection.tables()) 
print("Conexión exitosa a HBase") 
connection.close() 
except Exception as e: 
print("Error al conectar:", str(e))
 
Ejecutamos el código: 
python3 test_hbase.py
 

DATASET 
Descargamos el dataset a utilizar y lo cargamos a la tabla de Hbase, puede ser de un 
repositorio publico como datos abiertos (datos.gov.co), UCI Machine Learning Repository, 
Awesome Public Datasets, en GitHub, etc. 
Como ejemplo utlizaremos un dataset de datos de carros usados 
wget http://45.65.233.139/data/Car_details_v3.csv --no-check-certificate
 
Creamos un archive .py donde nos conectaremos a Hbase, crearemos una tabla con sus 
respectivas familias y haremos algunas operaciones y consultas 
nano consultas.py
 

Copiamos el siguiente código 
import happybase 
import pandas as pd 
from datetime import datetime 
 
# Bloque principal de ejecución 
try: 
    # 1. Establecer conexión con HBase 
    connection = happybase.Connection('localhost') 
    print("Conexión establecida con HBase")
    # 2. Crear la tabla con las familias de columnas 
    table_name = 'used_cars' 
    families = { 
        'basic': dict(),        # información básica del coche 
        'specs': dict(),        # especificaciones técnicas 
        'sales': dict(),        # información de venta 
        'condition': dict()     # estado del vehículo 
    } 
     # Eliminar la tabla si ya existe 
    if table_name.encode() in connection.tables(): 
        print(f"Eliminando tabla existente - {table_name}") 
        connection.delete_table(table_name, disable=True) 
        # Crear nueva tabla 
    connection.create_table(table_name, families) 
    table = connection.table(table_name) 
    print("Tabla 'used_cars' creada exitosamente") 
    # 3. Cargar datos del CSV 
    car_data = pd.read_csv('Car_details_v3.csv') 
    # Iterar sobre el DataFrame usando el índice 
    for index, row in car_data.iterrows(): 
        # Generar row key basado en el índice 
        row_key = f'car_{index}'.encode() 
        # Organizar los datos en familias de columnas 
        data = { 
            b'basic:name': str(row['name']).encode(), 
            b'basic:year': str(row['year']).encode(), 
            b'basic:transmission': str(row['transmission']).encode(), 
            b'basic:fuel': str(row['fuel']).encode(), 
            b'specs:engine': str(row['engine']).encode(), 
            b'specs:max_power': str(row['max_power']).encode(), 
            b'specs:torque': str(row['torque']).encode(), 
            b'specs:seats': str(row['seats']).encode(), 
            b'specs:mileage': str(row['mileage']).encode(), 
            b'sales:selling_price': str(row['selling_price']).encode(), 
            b'sales:seller_type': str(row['seller_type']).encode(),
            b'condition:km_driven': str(row['km_driven']).encode(), 
            b'condition:owner': str(row['owner']).encode() 
        } 
         table.put(row_key, data) 
      print("Datos cargados exitosamente") 
      # 4. Consultas y Análisis de Datos 
    print("\n=== Todos los coches en la base de datos (primeros 3) ===") 
    count = 0 
    for key, data in table.scan(): 
        if count < 3:  # Limitamos a 3 para el ejemplo 
            print(f"\nCoche ID: {key.decode()}") 
            print(f"Nombre: {data[b'basic:name'].decode()}") 
            print(f"Año: {data[b'basic:year'].decode()}") 
            print(f"Precio: {data[b'sales:selling_price'].decode()}") 
            count += 1 
            # 6. Encontrar coches por rango de precio 
    print("\n=== Coches con precio menor a 50000 ===") 
    for key, data in table.scan(): 
        if int(data[b'sales:selling_price'].decode()) < 50000: 
            print(f"\nCoche ID: {key.decode()}") 
            print(f"Nombre: {data[b'basic:name'].decode()}") 
            print(f"Precio: {data[b'sales:selling_price'].decode()}") 
            # 7. Análisis de propietarios 
    print("\n=== Coches por tipo de propietario ===") 
    owner_stats = {} 
    for key, data in table.scan(): 
        owner = data[b'condition:owner'].decode() 
        owner_stats[owner] = owner_stats.get(owner, 0) + 1 
    
  for owner, count in owner_stats.items(): 
        print(f"{owner}: {count} coches") 
        # 8. Análisis de precios por tipo de combustible 
    print("\n=== Precio promedio por tipo de combustible ===") 
    fuel_prices = {} 
    fuel_counts = {} 
    for key, data in table.scan(): 
        fuel = data[b'basic:fuel'].decode() 
        price = int(data[b'sales:selling_price'].decode()) 
        fuel_prices[fuel] = fuel_prices.get(fuel, 0) + price 
        fuel_counts[fuel] = fuel_counts.get(fuel, 0) + 1 
    for fuel in fuel_prices: 
        avg_price = fuel_prices[fuel] / fuel_counts[fuel] 
        print(f"{fuel}: {avg_price:.2f}") 
        # 9. Top 3 coches con mayor kilometraje 
    print("\n=== Top 3 coches con mayor kilometraje ===") 
    cars_by_km = [] 
    for key, data in table.scan(): 
        cars_by_km.append({ 
            'id': key.decode(), 
            'name': data[b'basic:name'].decode(), 
            'km': int(data[b'condition:km_driven'].decode()), 
            'price': int(data[b'sales:selling_price'].decode()) 
        }) 
     
 for car in sorted(cars_by_km, key=lambda x: x['km'], reverse=True)[:3]: 
        print(f"ID: {car['id']}") 
        print(f"Nombre: {car['name']}") 
        print(f"Kilometraje: {car['km']}") 
        print(f"Precio: {car['price']}\n") 
        # 10. Análisis de precios por tipo de transmisión 
    print("\n=== Precio promedio por tipo de transmisión ===") 
    transmission_prices = {} 
    transmission_counts = {} 
     
  for key, data in table.scan(): 
        trans = data[b'basic:transmission'].decode() 
        price = int(data[b'sales:selling_price'].decode()) 
        transmission_prices[trans] = transmission_prices.get(trans, 0) + price 
        transmission_counts[trans] = transmission_counts.get(trans, 0) + 1 
     
  for trans in transmission_prices: 
        avg_price = transmission_prices[trans] / transmission_counts[trans] 
        print(f"{trans}: {avg_price:.2f}") 
        # 11. Ejemplo de actualización de precio 
    car_to_update = 'car_0' 
    new_price = 460000 
    
table.put(car_to_update.encode(), {b'sales:selling_price': str(new_price).encode()}) 
print(f"\nPrecio actualizado para el coche ID: {car_to_update}") 
except Exception as e: 
print(f"Error: {str(e)}") 
f
 inally: 
# Cerrar la conexión 
connection.close()

Damos Crtl+O enter y luego Crtl+X para salir
 

Instalamos librerías necesarias 
pip install pandas
Nota: Tuve que ejecutar algunos scripts para liberar espacio en el disco duro de mi PC, ya que no había suficiente almacenamiento para ejecutar los ‘pandas’.
 
Ejecutamos el script: 
python3 consultas.py

 
Ahora vamos a monitorear el servidor Hbase ingresando por la URL 
http://ip_maquina_virtual:16010 
Para este caso sería 
http://192.168.0.109:16010


