# MongoDB basics
This is a repository where I tried to learn how to use mongodb with node.js. I documented my finding and listed command memos below.

# MongoDB commands

##### `mongod`
Starts running MongoDB

---

##### `mongo`
Enters in the MongoDB shell

---

## Within the MongoDB shell

##### `exit`
Exits the MongoDB shell

---

##### `help`
Lists commands you can use

---

##### `show dbs`
Shows the database names

---

##### `show collections`
Shows the collections in the current database

---

##### `show users`
Shows the users in the current database

---

##### `show profile`
Shows the most recent system.profile entries with time >= 1ms

---

##### `show logs`
Shows the accessible logger names

---

##### `show log [name]`
Prints out the last segment of log in memory, 'global' is default

---

##### `use [yourDbName]`
Switches to `[yourDbName]`. If not existing, it will create it for you

---

##### `db.[yourDbName].insert([yourPredefinedObject])`
Inserts `[yourPredefinedObject]` in `[yourDbName]`

---

##### `db.[yourDbName].find()`
Returns everything matching the [objectMatchingPattern].
Uses the [filterPattern] to filter what to return if given.
If no argument is given, find lists everything that was saved in `[yourDbName]`

```javascript
// Find everything
db.test.find();

// Find with a pattern
db.test.find({ "gender": "Female" });

// Find with a pattern and filter the results
db.test.find(
	{ "gender": "Female" },
	{ "name": 1, "_id": 0 } // Returns all female user-names without the ObjectId
);

// You can use some comparators to find based on given value
db.test.find({ "gender": "Female", "age": { $gte: 20, $lte: 28 } });
// $gt  = Greater than [value]              | $lt  = Less than [value]
// $gte = Greater than or equal to [value]  | $lte = Less than or equal to [value]
// $in  = One of the given [values]         | $nin = Not one of the given [values]
// $ne  = Not equal to [value]              |
// ——
// These also work with Date objects

// You can also use the OR operator
db.test.find({ $or: [
	{ "gender": "Female" },
	{ "age": { $gte: 28, $lte: 58 } }
]});
// Searches whether for women or for people between 28 and 58. Why-soever.

// You can also negate comparators using $not
db.test.find(
	{ "gender": "Female", { $not: { "age": { $gte: 28, $lte: 58 } } } }
);
// Searches for women that are not aged between 28 and 58. Why-soever.

// You can use regular expressions to find stuff
db.test.find({ "name": /^D.*$/ } );
// Searches for anyone who's name starts with the letter D

// You can use $all to find documents having an array property containing all of the given values
db.test.find({ "cards": { $all: [ "ace of diamonds", "ace of spades" ] } } );

// You can use .[index] to look at a particular item of an array property of a document
db.test.find({ "cards.0": "ace of spades" );
// Returns users having the "ace of spades" as first card

// You can use .[key] to look at a particular key of a sub-document of a document
db.test.find({ "info.phone": "123456" );
```

---

##### prefix `[...].limit([max])`
Limits the result to a maximum of [max]

---

##### prefix `[...].skip([resultsToSkip])`
Skips [resultsToSkip] results

---

##### prefix `[...].sort([sortPattern])`
Sorts the results using the [sortPattern].

```javascript
db.test.find(...).sort(
	{ "name": 1 } // 1 means ascending and 0 descending
);
```

---

##### prefix `[...].pretty()`
Logs the result of any command in a formatted way

---

##### `db.[yourDbName].update([yourObjectMatcher], [yourModifiedObject])`
Looks for the entry matching `[yourObjectMatcher]` and updates it with `[yourModifiedObject]`

```javascript
// 1 to 1 replacement
db.testdb.update(
	{ "name": "John Smith" },
	{ "name": "Johnny Smith" }
);

// 1 to 1 replacement for all matching document at the same time
db.testdb.update(
	{ "name": "John Smith" },
	{ $set: { "name": "Johnny Smith" } },
	{ multi: true }
);

// If i want to add informations while updating I need to use the $set
db.testdb.update(
	{ "name": "John Smith" },
	{ $set: { "name": "Johnny Smith", "age": 45 } }
);

// If i want to increment a value I need to use the $inc
db.testdb.update(
	{ "name": "John Smith" },
	{ $inc: { "age": 1 } } // Increments age of 1. Use negative values for decrementing
);

// If i want to multiply a value I need to use the $mul
db.testdb.update(
	{ "name": "John Smith" },
	{ $mul: { "height": 2 } } // Multiplies height by 2
);

// If i want to add a document to a key of type array I need to use the $push
// The key is added in case it is not existing
db.testdb.update(
	{ "name": "John Smith" },
	{ $push: { "sisters": { "name": "Anna Smith" } } }
);

// If i want to $push more than one item at the same time, I need to use each
db.testdb.update(
	{ "name": "John Smith" },
	{ $push: { "twoBiggerSisters": {
		$each: sistersArray.sortByAge(),
		$slice: -2 // Limits the amount of pushed items
	}}}
);

// If i want to $push an item to an array property of my document, I need to use $addToSet
db.testdb.update(
	{ "name": "John Smith" },
	{ $addToSet: { "sisters": { "name": "Anna Smith" } } }
);

// If i want to $pop an item of an array property of my document, I need to use $pop
db.testdb.update(
	{ "name": "John Smith" },
	{ $pop: { "sisters": 1 }} // Use negative value to pop from the beginning
);

// If i want to remove an item of an array property of my document, I need to use $pull
db.testdb.update(
	{ "name": "John Smith" },
	{ $pull: { "sisters": { "name", "Anna Smith" } }}
);
```

---

##### `db.[yourDbName].drop([query])`
Drops content out of [yourDbName]. Drops everything if no arguments are given.

---

##### `db.cloneDatabase([host])`
Clones the database from the [host] host into the actual MongoDB

---

##### `db.copyDatabase([fromdb], [todb], [fromhost])`
Clones a specific database [fromdb] from the host [fromhost] into a specific [todb] database of the actual MongoDB

---

##### `load([scriptFile])`
Loads and executes the [scriptFile] into the MongoDB shell context

---

#### .mongorc.js
Javascript file that will be executed at every mongo-shell start

---

## Storable data types
- null
- Arrays
- boolean
- Objects
- Date
- Regular expressions
- Strings
- ObjectIds (12 bytes ids)
- Numbers

## Sources of informations
- [MongoDB Official Documentation](https://docs.mongodb.com/)
- [MongoDB Tutorial](https://youtu.be/xDCKcNBFsuI?list=PLGLfVvz_LVvSpxyVx5XcprEgvhJ1BzruD) by [Derek Banas](https://www.youtube.com/channel/UCwRXb5dUK4cvsHbx-rGzSgw) @newthinktank

## License
- [WTFPL](https://github.com/vogelino/nodejs-mongodb-basics/blob/master/LICENSE) - [WTFPL website](http://www.wtfpl.net/)
