# bootcamp-backend-student-mongo-query-language

##Restore backup
Restore data set de mongo atlas airbnb.

You can find it in this link: https://drive.google.com/drive/folders/1gAtZZdrBKiKioJSZwnShXskaKk6H_gCJ?usp=sharing

You could follow the following instructions: https://www.lemoncode.tv/curso/docker-y-mongodb/leccion/restaurando-backup-mongodb

Acuérdate de mirar si en opt/app hay contenido de backups previos que tengas que borrar

##General
En este base de datos puedes encontrar un montón de apartamentos y sus reviews, esto está sacado de hacer webscrapping.

Pregunta: Si montaras un sitio real, ¿qué posibles problemas pontenciales le ves a cómo está almacenada la información?

##Queries

* Saca en una consulta cuántos apartamentos hay en España.
```
use('listingsAndReviews');
db.listingsAndReviews.countDocuments({ "address.country": { $eq: "Spain" } } );
```
* Lista los 10 primeros:

Sólo muestra: nombre, camas, precio, government_area
Ordenados por precio.

```
use('listingsAndReviews');
db.listingsAndReviews.aggregate ( 
   [
     { $match: { "address.country": { $eq: "Spain" } } },
     { $sort : { price : 1, _id: 1 } },
     { $limit : 10 },
     { $project : { name:1, beds:1, price:1, "address.government_area":1 } }
   ]
)
```
Since the _id field is always guaranteed to contain exclusively unique values, the returned sort order will always be the same across multiple executions of the same sort.

```
use('listingsAndReviews');
db.listingsAndReviews.find(
    { "address.country": { $eq: "Spain" } }, { name:1, beds:1, price:1, "address.government_area":1 } 
    ).sort({aprice : 1, _id: 1}).limit(10)
```
Result
```
[
  {
    "_id": "20611485",
    "name": "Cómoda Habitación L'Eixample, Gracia",
    "beds": 2,
    "price": {
      "$numberDecimal": "10.00"
    },
    "address": {
      "government_area": "la Nova Esquerra de l'Eixample"
    }
  },
  {
    "_id": "31305846",
    "name": "Private room with sunny terrace of 200m2. 6",
    "beds": 1,
    "price": {
      "$numberDecimal": "10.00"
    },
    "address": {
      "government_area": "la Maternitat i Sant Ramon"
    }
  },
  {
    "_id": "32636126",
    "name": "Near the RAMBLA, the double room at  SEASIDE PORT1",
    "beds": 1,
    "price": {
      "$numberDecimal": "10.00"
    },
    "address": {
      "government_area": "el Raval"
    }
  },
  {
    "_id": "8521963",
    "name": "Habitación privada",
    "beds": 1,
    "price": {
      "$numberDecimal": "12.00"
    },
    "address": {
      "government_area": "la Bordeta"
    }
  },
  {
    "_id": "18121163",
    "name": "Heart of the City-Cozy Room Private Sunny Balcony!",
    "beds": 1,
    "price": {
      "$numberDecimal": "14.00"
    },
    "address": {
      "government_area": "la Vila de Gràcia"
    }
  },
  {
    "_id": "19741944",
    "name": "Habitación individual.",
    "beds": 1,
    "price": {
      "$numberDecimal": "14.00"
    },
    "address": {
      "government_area": "el Fort Pienc"
    }
  },
  {
    "_id": "10716793",
    "name": "DOUBLE ROOM for 1 or 2 ppl",
    "beds": 1,
    "price": {
      "$numberDecimal": "15.00"
    },
    "address": {
      "government_area": "la Sagrada Família"
    }
  },
  {
    "_id": "15320378",
    "name": "Hab. individual en Barcelona / Single room in Bcn",
    "beds": 1,
    "price": {
      "$numberDecimal": "15.00"
    },
    "address": {
      "government_area": "Sants"
    }
  },
  {
    "_id": "15353532",
    "name": "Nice room in artistic apartment, very central",
    "beds": 1,
    "price": {
      "$numberDecimal": "15.00"
    },
    "address": {
      "government_area": "Sant Pere, Santa Caterina i la Ribera"
    }
  },
  {
    "_id": "17573977",
    "name": "Bright room in sagrada familia",
    "beds": 2,
    "price": {
      "$numberDecimal": "15.00"
    },
    "address": {
      "government_area": "la Sagrada Família"
    }
  }
]
```

Filtrando
Queremos viajar cómodos, somos 4 personas y queremos:
4 camas.
Dos cuartos de baño.
// Pega aquí tu consulta
Al requisito anterior, hay que añadir que nos gusta la tecnología queremos que el apartamento tenga wifi.
// Pega aquí tu consulta
Y bueno, un amigo se ha unido que trae un perro, así que a la query anterior tenemos que buscar que permitan mascota Pets Allowed
// Pega aquí tu consulta
Operadores lógicos
Estamos entre ir a Barcelona o a Portugal, los dos destinos nos valen, peeero... queremos que el precio nos salga baratito (50 $), y que tenga buen rating de reviews
// Pega aquí tu consulta
Agregaciones
Queremos mostrar los pisos que hay en España, y los siguiente campos:
Nombre.
De que ciudad (no queremos mostrar un objeto, solo el string con la ciudad)
El precio (no queremos mostrar un objeto, solo el campo de precio)
// Pega aquí tu consulta
Queremos saber cuántos alojamientos hay disponibles por país.
// Pega aquí tu consulta