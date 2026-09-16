# FACE2 --- MongoDB CRUD Practice Log

**Date:** 15 September 2026\
**Database:** `PCEA24CY037`\
**Collection:** `products`\
**Environment:** MongoDB Atlas + `mongosh`

## 1. Product Data Creation

Created product documents using a JavaScript `for` loop that inserts 50
products into `db.products`.

``` javascript
for (let i = 1; i <= 50; i++) {
    db.products.insertOne({
        productId: i,
        productName: "Product " + i,
        category:
            i % 5 === 0 ? "Laptop" :
            i % 5 === 1 ? "Mobile" :
            i % 5 === 2 ? "Headphones" :
            i % 5 === 3 ? "Keyboard" :
                          "Mouse",

        brand:
            i % 3 === 0 ? "Dell" :
            i % 3 === 1 ? "Samsung" :
                          "HP",

        price: 1000 + (i * 500),
        stock: 10 + i,
        rating: 3 + ((i % 3) * 0.5),
        inStock: i % 4 !== 0,

        tags: [
            "electronics",
            i % 2 === 0 ? "featured" : "new"
        ],

        seller: {
            sellerId: 1000 + i,
            sellerName: "Seller " + i
        },

        createdAt: new Date()
    });
}
```

## 2. Updating Product 20 Price

Used `updateMany()` to change the price of all documents having
`productId: 20`.

``` javascript
db.products.updateMany(
    { productId: 20 },
    { $set: { price: 17000 } }
)
```

Result:

``` text
acknowledged: true
matchedCount: 2
modifiedCount: 2
```

This showed that two documents had `productId: 20`.

## 3. Updating Product 20 Seller Name

### First attempt --- incorrect

``` javascript
db.products.upadteOne(
    { productId: 20 },
    { $set: { "seller,sellername": "Aditya store" } }
)
```

Error:

``` text
TypeError: db.products.upadteOne is not a function
```

Problems: - `upadteOne` was misspelled. Correct spelling is
`updateOne`. - Nested field syntax was incorrect. A dot (`.`), not a
comma (`,`), is used between nested field names.

### Correct command

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $set: { "seller.sellerName": "Aditya Store" } }
)
```

Result:

``` text
acknowledged: true
matchedCount: 1
modifiedCount: 1
```

Verification:

``` javascript
db.products.find({ productId: 20 })
```

The first Product 20 document now contains:

``` javascript
seller: {
    sellerId: 1020,
    sellerName: "Aditya Store"
}
```

The second Product 20 document still contains:

``` javascript
seller: {
    sellerId: 1020,
    sellerName: "Seller 20"
}
```

This demonstrates the difference between `updateOne()` and
`updateMany()`.

## 4. Increasing Product 20 Stock

Used `$inc` to increase the stock of one Product 20 document by 20.

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $inc: { stock: 20 } }
)
```

Result:

``` text
acknowledged: true
matchedCount: 1
modifiedCount: 1
```

The selected Product 20 document changed from:

``` text
stock: 30
```

to:

``` text
stock: 50
```

## 5. Checking Product 20

Correct command:

``` javascript
db.products.find({ productId: 20 })
```

The final visible state included:

``` javascript
{
    productId: 20,
    productName: "Product 20",
    category: "Laptop",
    brand: "HP",
    price: 17000,
    stock: 50,
    inStock: false,
    tags: ["electronics", "featured"],
    seller: {
        sellerId: 1020,
        sellerName: "Aditya Store"
    }
}
```

A second document with the same `productId: 20` remained with:

``` text
stock: 30
sellerName: "Seller 20"
```

## 6. Removing the Rating Field

### First attempt --- incorrect

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $unset: Prating: "" }
})
```

This produced a syntax error because the `$unset` object was not written
correctly.

### Correct command

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $unset: { rating: "" } }
)
```

Result:

``` text
acknowledged: true
matchedCount: 1
modifiedCount: 1
```

The `rating` field was removed from the first matching Product 20
document.

## 7. Another Incorrect Find Command

Incorrect:

``` javascript
db.products.find({ productId: 20 })
```

The attempted command in the session accidentally contained an extra
closing `}`:

``` javascript
db.products.find({ productId: 20 }})
```

This caused:

``` text
SyntaxError: Unexpected token
```

Correct command:

``` javascript
db.products.find({ productId: 20 })
```

## 8. Incorrect Direct Database Syntax

An incorrect command was also attempted:

``` javascript
db.productId : 20 })
```

This is not a valid MongoDB query.

Correct way to search the `products` collection:

``` javascript
db.products.find({ productId: 20 })
```

## 9. MongoDB Operators Practiced

### `$set`

Used to create or modify a field.

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $set: { price: 17000 } }
)
```

### `$inc`

Used to increase or decrease a numeric field.

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $inc: { stock: 20 } }
)
```

### `$unset`

Used to remove a field.

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $unset: { rating: "" } }
)
```

### Nested field update

Used dot notation:

``` javascript
"seller.sellerName"
```

Example:

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $set: { "seller.sellerName": "Aditya Store" } }
)
```

## 10. Important Lessons Learned

1.  MongoDB method names must be spelled correctly:
    -   `updateOne()` --- correct
    -   `upadteOne()` --- incorrect
2.  Nested fields use dot notation:
    -   Correct: `"seller.sellerName"`
    -   Incorrect: `"seller,sellername"`
3.  `$set` needs a field and a value:

``` javascript
{ $set: { field: value } }
```

4.  `$inc` changes numeric values:

``` javascript
{ $inc: { stock: 20 } }
```

5.  `$unset` removes a field:

``` javascript
{ $unset: { rating: "" } }
```

6.  `updateOne()` updates only the first matching document.

7.  `updateMany()` updates all matching documents.

8.  Always verify an update using `find()` or `findOne()`.

## 11. Useful Verification Commands

Count documents:

``` javascript
db.products.countDocuments()
```

Find Product 20:

``` javascript
db.products.find({ productId: 20 })
```

Find one Product 20:

``` javascript
db.products.findOne({ productId: 20 })
```

Show all products:

``` javascript
db.products.find()
```

## 12. Today's Practical Summary

Today's MongoDB practical work covered:

-   Creating product documents
-   Working with MongoDB Atlas
-   Using `mongosh`
-   `find()`
-   `findOne()`
-   `updateOne()`
-   `updateMany()`
-   `$set`
-   `$inc`
-   `$unset`
-   Nested document fields
-   Dot notation
-   Checking update results
-   Understanding syntax errors
-   Understanding `matchedCount` and `modifiedCount`

**Project/database:** E-Commerce `products` collection\
**Practice document:** `FACE2`
