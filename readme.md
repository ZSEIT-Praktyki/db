# Database for Auction Website

**Disclamer: This is not a final version, changes may include: structure of a database, contents of tables, and more. Those changes will be made when problems appear in backend development.**


## Table of Contents:

1. **[Disclamer about diagram](#Disclamer-about-diagram)**
2. **[Diagram](#Diagram)**
3. **[Explanation of unclear concepts and solutions](#Explanation-of-unclear-concepts-and-solutions)**
    1. [Categories and subcategories](#Categories-and-subcategories)
    2. [User and user addresses](#User-and-user-addresses)
        1. [Redundant data](##Redundant-data)
        2. [Password attribute data type](##Password-attribute-data-type)
        3. [Phone numbers in database](##Phone-numbers-in-database)
        4. [Activated attribute](##Activated-attribute)
        5. [Postal codes in database](##Postal-codes-in-database)
        6. [Way of saving states](##Way-of-saving-states)
    3. [Reviews](#Reviews)
        1. [Relationships](##Relationships)
        2. [Rating](##Rating)
    4. [Orders](#Orders)
        1. [Identifying a seller](##Identifying-a-seller)
    5. [Listing](#Listing)
        1. [Listing's images](#Listing's-images)

# Disclamer about diagram

Diagram is made with draw.io, in case you don't want to use this, here you have a screenshot of this diagram. **Note: It is possible, that a structure of this database has changed, but this file has not. If you want to be 100% sure with having the newest diagram you should use [this diagram](./db-concept.drawio).**

# Diagram

![img](./db-concept.png)

# Explanation of unclear concepts and solutions

Some design concepts might be unclear for people not involved in process of conceptual work. This section explains most of problematic solution contained in the project of a database.

# Categories and subcategories

Every listing have asigned subcategory. Because of that listing inherits a category from subcategory. These entites are organised in a hierarchy.

### Below is a diagram explaining connections and hierarchy between listings, categories and subcategories:

![img](./readme-assets/categories.png)

# User and user addresses

Here are explained concepts used in this database that we think can cause problems without proper explaination.

## Redundant data

It is not hard to think, that in table `Users` is unnecessary data about owner of an account (`owners_name`, `owners_surname` and `owners_phone`). This information can be accessed from saved address in `user_addresses` table. **We did not think, that such data should be saved as an address, since addresses are changed relatively frequent. That is why name, surname and phone of a owner of each account is saved in `Users` table.**

## Password attribute data type

In `Users` table there is a `password` attribute with data type `BINARY(60)`. Reason for usage of this data type is simple. For hashing we are using [bcrypt](https://en.wikipedia.org/wiki/Bcrypt) and this data type is recommended.

## Phone numbers in database

Phone numbers need to be saved in format like this `+XXYYYYYYYYY`, where `XX` is a country code and `ZZZZZZZZZ` is a phone number.

## Activated attribute

For account to work it needs to be activated. That is a security measure preventing bots from creating huge quantities of accounts. Activation is done by verifing an email. Activated accounts have value `TRUE` in `activated` attribute.

## Postal codes in database

Postal codes are saved without "-" character, so postal codes should be saved in this format: `XXYYY`, instead of this: `XX-YYY`.

## Way of saving states

We were thinking about 2 solutions to this problem:

- Enum datatype
- Extra table containing all states which are accessed by using foreign key

We have chosen the second option, because it does not limit us with number of states possible to add.

# Reviews

Here is an explanation of inner workings of review system when it comes to data keeping.

## Relationships

`Review` table connects two users, where the entity connecting them is a review. One has a role of an issuer. Issuer makes a review and assigns it to a recipient. We may say that it is a relation to itself. **There is a danger of this solution. When implementation of this feature is made badly a user may be able to make reviews for himself. That is why it is important to properly secure an endpoint.**

## Rating

Reviews can be positive and negative. That's why `rating` attribute is a BOOL type. TRUE -> Review is positive, FALSE -> Review is negative.

# Orders

Table `orders` contains every order made on this website. Order is connected with a listing and a buyer.

## Identifying a seller

In table `orders` there is no seller_id. To reach a seller you need to look into a listing.

# Listing

Listing has a relationships with subcategory and user which is a seller.

## Listing's images

Images are stored in a seprate table called `lisitng_img`. To access photos we highly suggest running a second query.
