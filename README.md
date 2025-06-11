# Prisma ORM

An **ORM** (Object-Relational Mapping) is a technique or a tool that allows developers to interact with a database using an object-oriented approach, rather than writing SQL queries. For example: **Prisma**, **Drizzle**, **Sequelize** (JavaScript), etc.

## Benefits of using Prisma

- **Productivity**: Reduces repeating code (**DRY – Do Not Repeat Yourself**).  
- **Portability**: Makes database switching easier.  
- **Security**: Helps prevent SQL injection.  
- **Maintenance**: Simplifies database schema changes.


## Installing Prisma

We install Prisma as an NPM package as a development dependency since we don’t need it in production.  
Simply run the following command:

```bash 
npm install prisma -D
```
OR

```bash 
npm install prisma --save-dev
```

## Set Up prisma
Setting up prisma, run the following commands on your terminal and replace the 'DATAABASE' with the database name you are using. Eg. **postgresql**

```bash
npx prisma init --datasource-provider DATABASE
```

The above command does 2 things:
- **Creates a directory** named prisma that includes the schema.prisma file, where database models and connection settings are defined.
- **Generates a .env file** at the project root to manage environment variables, including the database connection string.

The .env file provides a DATABASE_URL template, for easy configuration by filling in details like: username, password, host, port and database name.

```bash
DATABASE_URL="postgresql://username:yourpassword@localhost:5432/mydb?schema=public"
```

```bash 
DATABASE_URL="postgresql://postgres:@2025Dev@localhost:5432/booksdata"
```

## Field types in prisma
1. A String represents any sequence of text characters, from single words to lengthy paragraphs.

1. A Int represents standard whole numbers for counting and indexing.

1. A BigInt represents much larger whole numbers, extending far beyond the limits of regular integers (large numerical values that exceed standard integer capacity)

1. A Float represents decimal numbers rounding.

1. A Decimal represents precise decimal numbers without any loss of accuracy.

1. A Boolean represents binary logic values, storing either true or false states.

1. A DateTime represents specific points in time, including both date, time and timestamps.

1. A Bytes represents raw binary data in its original form which can store any kind of binary content, from images and documents to encrypted data.

1. A JSONrepresents structured data objects that can contain nested properties, arrays, and various data types within a single field, providing flexibility for complex data structures.

1. A UUID represents universally unique identifiers

## Field Attributes
1. @id is used to mark the primary key

1. @default sets a default value eg createdAt DateTime @default(now())

1. @unique ensures that a field has unique values across all rows in a table.

1. @updatedAt automatically updates a field to the current timestamp whenever the record is updated eg updatedAt DateTime @updatedAt.

1. @map maps the field to a column with a different name in the database for example firstName String @map("first_name")

1. @relation defines relationships between models. It specifies foreign keys and the related models eg author User @relation(fields: [authorId], references: [id])

1. @@iddefines a composite primary key using multiple fields eg @@id([field1, field2])

1. @@unique ensures a unique constraint on a combination of multiple fields. eg @@unique([username, emailAddress])

1. @@map maps the model to a table with a different name in the database.

## Creating a Module 
 ```bash 
model Book {
  id Int @id @default(autoincrement())
  title String @map("book_title")
  author String @map("book_author")
  publishedYear Int @map("published_year")
  pages Int @map("total_pages")
  @@map("books_table")
}

```

## Prisma Migration
- Migrations are versioned files that describe changes to your database schema.

To run a migration, we execute the following command and change 'MIGRATION-NAME' to desired name.

```bash 
npx prisma migrate dev --name MIGRATION-NAME
```

## CRUD (create, read, update, and delete) in Prisma
CRUD stands for Create, Read, Update, and Delete, representing the four fundamental operations performed on data in persistent storage, particularly in databases.

### Create a single record using create()
this creates a single instance of a book object in the example below.
```bash 
import { PrismaClient } from '@prisma/client';
const client = new PrismaClient();

const createBook = async () => {
  const newBook = await client.book.create({
    data: {
      title: "Roughing It!",
      author: "Mark Twain",
      publishedYear: 1999,
      pages: 990,
    },
  });
  console.log(newBook);
};

createBook();
```

### Create multiple records using createMany()
this creates a two instance of a book object in the example below.
```bash 
import { PrismaClient } from "@prisma/client";
const client = new PrismaClient();

const createBooks = async () => {
  const newBooks = await client.book.createMany({
    data: [
      {
        title: "River Flowing",
        author: "david mulwa",
        publishedYear: 2016,
        pages: 320,
      },
      {
        title: "Escaping the matrix",
        author: "Onyango Tate",
        publishedYear: 2025,
        pages: 464,
      },
    ],
  });
  console.log(newBooks);
};

createBooks();
```

## Get all records using findMany()
This displays all record in the book model.
``` bash 
import { PrismaClient } from "@prisma/client";
const client = new PrismaClient();

const getBooks = async () => {
  const books = await client.book.findMany();
  console.log(books);
};

getBooks();
```

## Applying the AND operator to select only records that match multiple criterias
The AND operator is used together with where clause to filter and retrieve only records that match multiple criteria simultaneously.
``` bash 
import { PrismaClient } from "@prisma/client";
const client = new PrismaClient();

const getBooks = async () => {
  const books = await client.book.findMany({
    where: {
      AND: [
        { publishedYear: { gt: 2000 } },
        { pages: { gt: 300 } },
      ],
    },
  });
  console.log(books);
};

getBooks();
```

## comparison operators in Prisma
### Numeric filter
- equals: matches exactly eg { age: { equals: 25 } }.

- not: matches 'not equal to the specified value' eg { age: { not: 25 } }

- lt: less than eg { age: { lt: 18 } }.

- lte: less than or equal to eg { age: { lte: 18 } }.

### String filters
- equals: matches exactly (case-sensitive) eg { name: { equals: "John" } }

- contains: matches if the string contains the specified substring eg { name: { contains: "John" } }

- startsWith: matches if the string starts with the specified string eg { name: { startsWith: "Jo" } }.

## Update a single record using update()
Here we modify a single record in the database by specifying a **where clause** (locates the specific record to update) and a **data object** (defines the changes to apply)

```bash 
import { PrismaClient } from "@prisma/client";
const client = new PrismaClient();

const updateBook = async () => {
  const updatedBook = await client.book.update({
    where: { id: 2 }, 
    data: { title: "River and the source" },
  });
  console.log(updatedBook);
};

updateBook();
```

## Delete a single record using delete()
delete() is used to remove a single record from a database while specifying a **where condition** to locate the record.

```bash
import { PrismaClient } from "@prisma/client";
const client = new PrismaClient();

const deleteBook = async () => {
  const deletedBook = await client.book.delete({
    where: { id: 1 },
  });
  console.log(deletedBook);
};

deleteBook();
```

## Relationships
Relationships define how different models (tables) are connected within a database.

Prisma uses a schema to define these relationships making it easier to work with relational databases.

### Types of Relationships in Prisma
1. One-to-One (1-1)
-  means that one record in a table is associated to only one record in another table.

2. One-to-Many (1-n)
-  means that one record in a table can be associated with multiple other records in another table.

3. Many-to-Many (m-n)
- means that multiple records in one table can be associated to multiple records in another table.