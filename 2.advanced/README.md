# Advanced
* We would like to know the average price of renting on airbnb in Spain

    ```
    use('listingsAndReviews');
    db.listingsAndReviews.aggregate([
    { $match: { "address.country": { $eq: "Spain" } } },  
    {
        $group: {
        _id: { country: "$address.country" },
        averagePrice: { $avg: "$price" }
        }
    }
    ])
    ```

    ```
    // Result
    [
    {
        "_id": {
        "country": "Spain"
        },
        "averagePrice": {
            "$numberDecimal": "100.8262243285939968404423380726698"
        }
    }
    ]
    ```
* We would like to know the average price of renting on airbnb grouped by country

```
use('listingsAndReviews');
  db.listingsAndReviews.aggregate([    
    {
      $group: {
        _id: { country: "$address.country" },
        averagePrice: { $avg: "$price" }
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
      "country": "Portugal"
    },
    "averagePrice": {
      "$numberDecimal": "69.18198198198198198198198198198198"
    }
  },
  {
    "_id": {
      "country": "Spain"
    },
    "averagePrice": {
      "$numberDecimal": "100.8262243285939968404423380726698"
    }
  },
  {
    "_id": {
      "country": "United States"
    },
    "averagePrice": {
      "$numberDecimal": "185.7692307692307692307692307692308"
    }
  },
  {
    "_id": {
      "country": "Brazil"
    },
    "averagePrice": {
      "$numberDecimal": "525.3465346534653465346534653465347"
    }
  },
  {
    "_id": {
      "country": "Hong Kong"
    },
    "averagePrice": {
      "$numberDecimal": "773.7866666666666666666666666666667"
    }
  },
  ...
]
```

* We would like to know the average price of renting on airbnb grouped by country and number of rooms

```
use('listingsAndReviews');
  db.listingsAndReviews.aggregate([    
    {
      $group: {
        _id: {            
            country: "$address.country",
            bedrooms: "$bedrooms"
        },
        averagePrice: { $avg: "$price" }
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
    "averagePrice": {
      "$numberDecimal": "70.00"
    }
  },
  {
    "_id": {
      "country": "Australia",
      "bedrooms": 0
    },
    "averagePrice": {
      "$numberDecimal": "125.6222222222222222222222222222222"
    }
  },
  {
    "_id": {
      "country": "Australia",
      "bedrooms": 1
    },
    "averagePrice": {
      "$numberDecimal": "114.7041420118343195266272189349112"
    }
  },
  {
    "_id": {
      "country": "Australia",
      "bedrooms": 2
    },
    "averagePrice": {
      "$numberDecimal": "218.0757575757575757575757575757576"
    }
  },
  {
    "_id": {
      "country": "Australia",
      "bedrooms": 3
    },
    "averagePrice": {
      "$numberDecimal": "393.7391304347826086956521739130435"
    }
  },
  {
    "_id": {
      "country": "Australia",
      "bedrooms": 4
    },
    "averagePrice": {
      "$numberDecimal": "555.6470588235294117647058823529412"
    }
  },
  {
    "_id": {
      "country": "Australia",
      "bedrooms": 5
    },
    "averagePrice": {
      "$numberDecimal": "732.9090909090909090909090909090909"
    }
  },
  {
    "_id": {
      "country": "Australia",
      "bedrooms": 6
    },
    "averagePrice": {
      "$numberDecimal": "814.00"
    }
  },
  {
    "_id": {
      "country": "Australia",
      "bedrooms": 7
    },
    "averagePrice": {
      "$numberDecimal": "689.00"
    }
  },
  {
    "_id": {
      "country": "Brazil",
      "bedrooms": 0
    },
    "averagePrice": {
      "$numberDecimal": "295.1428571428571428571428571428571"
    }
  },
  {
    "_id": {
      "country": "Brazil",
      "bedrooms": 1
    },
    "averagePrice": {
      "$numberDecimal": "270.9339622641509433962264150943396"
    }
  },
  ...
]
```