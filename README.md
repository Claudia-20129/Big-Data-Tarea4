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

