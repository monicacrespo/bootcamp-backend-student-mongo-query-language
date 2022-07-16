# Challenge

* We would like to show the top 5 apartments more expensive in Spain, with the following fields:
  * Name
  * City.
  * Amenities, but instead of an array, un string with all the amenities

    ```
    use('listingsAndReviews');
    db.listingsAndReviews.aggregate([
    { 
    $match: { 
        "address.country": { $eq: "Spain" },
        property_type: { $eq: "Apartment" }
    }
    }, 
    {
    $sort: {
        price: -1
    }
    },
    { 
    $limit : 5 
    },
    { 
    $project: { 
        _id: 0, 
        name: 1, 
        city: "$address.country",  
        amenities: {
                $reduce: {
                    input: "$amenities",
                    initialValue: "",
                    in: { 
                    $concat: [
                        "$$value",
                        {
                        $cond: {
                            if: { $eq: [ "$$value", "" ] },
                            then: "",
                            else: ", "
                        }
                        }, 
                        "$$this" 
                        ] 
                    }
                }
        }
    } 
    }
    ])
    ```
    ```
    // Result 
    [
    {
        "name": "Room in the center of Barcelona",
        "city": "Spain",
        "amenities": "TV, Wifi, Air conditioning, Kitchen, Smoking allowed, Heating, Washer, Essentials, Shampoo, Hangers"
    },
    {
        "name": "Ático exclusivo Barcelona",
        "city": "Spain",
        "amenities": "TV, Internet, Wifi, Air conditioning, Pool, Kitchen, Elevator, Family/kid friendly, Washer, Dryer, Essentials, Shampoo, Hangers, Hair dryer, Iron, Laptop friendly workspace, Hot water, Long term stays allowed, Host greets you, Paid parking on premises"
    },
    {
        "name": "1405 - LUXURY BEACH TERRACE APARTME",
        "city": "Spain",
        "amenities": "TV, Internet, Wifi, Air conditioning, Wheelchair accessible, Pool, Kitchen, Smoking allowed, Gym, Elevator, Buzzer/wireless intercom, Heating, Family/kid friendly, Suitable for events, Washer, Dryer, Essentials"
    },
    {
        "name": "Casa Batlló views! Central apartment¦",
        "city": "Spain",
        "amenities": "TV, Cable TV, Internet, Wifi, Air conditioning, Kitchen, Elevator, Buzzer/wireless intercom, Family/kid friendly, Washer, Smoke detector, Carbon monoxide detector, Essentials, Shampoo, 24-hour check-in, Hangers, Hair dryer, Iron, Laptop friendly workspace, Self check-in, Building staff, Hot water, Bed linens, Microwave, Coffee maker, Refrigerator, Dishes and silverware, Cooking basics, Oven, Stove"
    },
    {
        "name": "2714 - AB Girona Apartment 41 - Stylish Apartment for up to 7 guests in Eixample with a Balcony",
        "city": "Spain",
        "amenities": "TV, Internet, Wifi, Air conditioning, Wheelchair accessible, Kitchen, Free parking on premises, Elevator, Heating, Washer, Safety card, Essentials, Shampoo, 24-hour check-in, Hangers, Hair dryer, Iron, Hot water, Microwave, Coffee maker, Refrigerator, Dishes and silverware, Cooking basics, Oven, Patio or balcony, Luggage dropoff allowed"
    }
    ]
    ```