# Basic
## Restore backup

Restore an airbnb database. You can find it in this link: https://drive.google.com/drive/folders/1gAtZZdrBKiKioJSZwnShXskaKk6H_gCJ?usp=sharing

You could follow the following instructions: https://www.lemoncode.tv/curso/docker-y-mongodb/leccion/restaurando-backup-mongodb

You can find a sample in this link: https://www.mongodb.com/docs/atlas/sample-data/sample-airbnb/

Please, remember to check whether there are any previous backups in opt/app that you would need to delete.

## General
In this database you could find a lot of apartments and their reviews, obtained from  webscrapping.

Question: If you were using a real site, what potential issues you foresee in the way the information is stored?

Storing large documents in your database can lead to excessive RAM and bandwidth usage. MongoDB keeps frequently accessed data, referred to as the working set, in RAM. When the working set grows beyond the RAM allotment, performance is degraded as data must be retrieved from disk instead.

If your most frequent queries are for documents that contain much more information than you need for that query, consider restructuring your schema with smaller documents using references to additional collections. By breaking up your data into more collections and using smaller documents for frequently accessed data, you reduce the overall size of the working set and improve performance.

## Queries
### Basic
* Get how many apartments there are in Spain.
```
use('listingsAndReviews');
db.listingsAndReviews.countDocuments({ "address.country": { $eq: "Spain" } } );
```

```
// Result
633
```

* Get the first 10 records sorted by price. The fields that need to be displayed are: name, beds, price, government_area

```
use('listingsAndReviews');
db.listingsAndReviews.find(
    { "address.country": { $eq: "Spain" } }, 
    { _id: 0, name: 1, beds: 1, price: 1, government_area: "$address.government_area" } 
    ).sort({price: 1, _id: 1}).limit(10)
```

```
// Result
[
  {
    "name": "Cómoda Habitación L'Eixample, Gracia",
    "beds": 2,
    "price": {
      "$numberDecimal": "10.00"
    },
    "government_area": "la Nova Esquerra de l'Eixample"
  },
  ...
  {
    "name": "Bright room in sagrada familia",
    "beds": 2,
    "price": {
      "$numberDecimal": "15.00"
    },
    "government_area": "la Sagrada Família"
  }
```
Since the _id field is always guaranteed to contain exclusively unique values, the returned sort order will always be the same across multiple executions of the same sort.

### Filters

* We want to comfortably travel, we are four people and we want four beds and two toilets.

```
use('listingsAndReviews');
db.listingsAndReviews.find({ 
$and: [
      {"beds": {$eq: 4}},
      {"bathrooms": {$eq: 2}}
]
},{ _id: 0, name: 1, beds: 1, bathrooms: 1, price: 1}
);
```

* Besides, we like the apartment comes with WIFI

```
use('listingsAndReviews');
db.listingsAndReviews.find({ 
$and: [
      {"beds": {$eq: 4}},
      {"bathrooms": {$eq: 2}},
      { amenities: "Wifi" }
]
},{ _id: 0, name: 1, beds: 1, bathrooms: 1, amenities: 1, price: 1}
);
```

```
// Result
[
  {
    "name": "Waterfront, Enchanted Lake Home 2/1",
    "beds": 4,
    "bathrooms": {
      "$numberDecimal": "2.0"
    },
    "amenities": [
      "TV",
      "Internet",
      "Wifi",
      "Air conditioning",
      "Wheelchair accessible",
      "Kitchen",
      "Free parking on premises",
      "Free street parking",
      "Family/kid friendly",
      "Washer",
      "Dryer",
      "Smoke detector",
      "Carbon monoxide detector",
      "Essentials",
      "24-hour check-in",
      "Hangers",
      "Hair dryer",
      "Iron",
      "Self check-in",
      "Keypad",
      "Private entrance",
      "Hot water",
      "Bed linens",
      "Extra pillows and blankets",
      "Microwave",
      "Coffee maker",
      "Refrigerator",
      "Stove",
      "Patio or balcony",
      "Long term stays allowed"
    ],
    "price": {
      "$numberDecimal": "189.00"
    }
  },
  ...
]
```

* Besides, a friend with a dog has joined, so, we would need an apartment with Pets Allowed

```
use('listingsAndReviews');
db.listingsAndReviews.find({ 
$and: [
      {"beds": {$eq: 4}},
      {"bathrooms": {$eq: 2}},
      {"amenities": { $all: ["Wifi", "Pets allowed"] } }
]
},{ _id: 0, name: 1, beds: 1, bathrooms: 1, amenities: 1, price: 1}
);
```

```
// Result
[
  {
    "name": "Penthouse. Terrace.",
    "beds": 4,
    "bathrooms": {
      "$numberDecimal": "2.0"
    },
    "amenities": [
      "TV",
      "Internet",
      "Wifi",
      "Air conditioning",
      "Wheelchair accessible",
      "Kitchen",
      "Paid parking off premises",
      "Pets allowed",
      "Elevator",
      "Buzzer/wireless intercom",
      "Heating",
      "Family/kid friendly",
      "Washer",
      "Dryer",
      "Smoke detector",
      "Carbon monoxide detector",
      "Fire extinguisher",
      "Essentials",
      "Shampoo",
      "24-hour check-in",
      "Hangers",
      "Hair dryer",
      "Iron",
      "Laptop friendly workspace",
      "High chair",
      "Children’s books and toys",
      "Crib",
      "Pack ’n Play/travel crib",
      "Hot water",
      "Microwave",
      "Coffee maker",
      "Refrigerator",
      "Dishwasher",
      "Dishes and silverware",
      "Cooking basics",
      "Oven",
      "Stove",
      "Long term stays allowed",
      "Host greets you",
      "Paid parking on premises"
    ],
    "price": {
      "$numberDecimal": "87.00"
    }
  },
  ...
]
```

### Logical Query Operators
* We are between going to Barcelona or Portugal, but we want the price to be cheap (50 $), and a good review scores rating.

```
use('listingsAndReviews');
db.listingsAndReviews.find({ 
$and: [
      {"price": { $eq: 50 } },
      {"review_scores.review_scores_rating": {$gt: 9}},      
      {
       $or: [
            { "address.market": { $eq: "Barcelona" } },
            { "address.country": { $eq: "Portugal" } }            
            ]
      }
]
},
{ _id: 0, name: 1, "address.country": 1, "address.market": 1, price: 1, review_score_rating: "$review_scores.review_scores_rating"}
);
```

```
// Result
[
  {
    "name": "B. Arts IV",
    "price": {
      "$numberDecimal": "50.00"
    },
    "address": {
      "market": "Porto",
      "country": "Portugal"
    },
    "review_score_rating": 100
  },
  {
    "name": "The Porto Concierge - White Martin",
    "price": {
      "$numberDecimal": "50.00"
    },
    "address": {
      "market": "Porto",
      "country": "Portugal"
    },
    "review_score_rating": 90
  },
  ...
]
```

### $group (aggregation)

* Get the apartments in Spain with the following fields:
  * Name
  * City (we dont want to display an object, only the string with the city)
  * Price 

```
use('listingsAndReviews');
db.listingsAndReviews.aggregate([  
  { $match: { "address.country": { $eq: "Spain" } } },
  { $project: { _id: 0, name: 1, city: "$address.market", price: 1} }
])
```

```
// Result
[
  {
    "name": "Nice room in Barcelona Center",
    "price": {
      "$numberDecimal": "50.00"
    },
    "city": "Barcelona"
  },
  {
    "name": "Cozy bedroom Sagrada Familia",
    "price": {
      "$numberDecimal": "20.00"
    },
    "city": "Barcelona"
  },
  ...
]
```

* Get how many listings are available per country
```
use('listingsAndReviews');
db.listingsAndReviews.aggregate([
  {
    $unwind: "$address.country"
  },
  {
    $group: {
      _id: { country: "$address.country" },
      countNumberOfDocumentsForCountry: { $count: {} }
      }
  },
  {
    $sort: {
      _id: 1
    }
  }
])
```
```
// Result
[
  {
    "_id": {
      "country": "Australia"
    },
    "countNumberOfDocumentsForCountry": 610
  },
  {
    "_id": {
      "country": "Brazil"
    },
    "countNumberOfDocumentsForCountry": 606
  },
  {
    "_id": {
      "country": "Canada"
    },
    "countNumberOfDocumentsForCountry": 649
  },
  {
    "_id": {
      "country": "China"
    },
    "countNumberOfDocumentsForCountry": 19
  },
  {
    "_id": {
      "country": "Hong Kong"
    },
    "countNumberOfDocumentsForCountry": 600
  },
  {
    "_id": {
      "country": "Portugal"
    },
    "countNumberOfDocumentsForCountry": 555
  },
  {
    "_id": {
      "country": "Spain"
    },
    "countNumberOfDocumentsForCountry": 633
  },
  {
    "_id": {
      "country": "Turkey"
    },
    "countNumberOfDocumentsForCountry": 661
  },
  {
    "_id": {
      "country": "United States"
    },
    "countNumberOfDocumentsForCountry": 1222
  }
]
```