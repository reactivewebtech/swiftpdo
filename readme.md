# SwiftPDO 

SwiftPDO is a PHP SQL query builder using PDO. It's a quick and light library featuring a smart join builder, which automatically creates table joins for you.
Original fork from good old FluentPDO library.

## Features
- Easy interface for creating robust queries
- Supports any database compatible with PDO includes the latest MySQL versions 
- Compatible with reserved words of major SQL databases for fast migration from old versions
- Ability to build complex SELECT, INSERT, UPDATE & DELETE queries with little code
- Type hinting for magic methods with code completion in smart IDEs

## Versions

#### Version 1.x

The stable release of SwiftPDO and actively maintained. Officially supports PHP 5.4 to PHP 8.0.
Fully Compatible with MySQL latest versions and "reserved words" issues that, Unlike other similar libraries that only until 5.7.


## Installation

### Composer

The preferred way to install SwiftPDO is via [composer](http://getcomposer.org/).

Add the following line in your `composer.json` file:

	"require": {
		...
		"rwt/SwiftPDO": "^1.1.0"
	}

update your dependencies with `composer update`, and you're done!

### Download Zip

If you prefer not to use composer, download the latest release, create the directory `rwt/SwiftPDO` in your library directory, and drop this repository into it. Finally, add:

```php
require '[lib-dir]/rwt/SwiftPDO/src/Query.php';
```

to the top of your application. **Note:** You will need an autoloader to use SwiftPDO without changing its source code.

## Getting Started

Create a new PDO instance, and pass the instance to SwiftPDO:

```php
$pdo = new PDO('mysql:dbname=swiftdb', 'user', 'password');
$swift = new \rwt\SwiftPDO\Query($pdo);
```

Then, creating queries is quick and easy:

```php
$query = $swift->from('comment')
             ->where('article.published_at > ?', $date)
             ->orderBy('published_at DESC')
             ->limit(5);
```

which would build the query below:

```mysql
SELECT comment.*
FROM comment
LEFT JOIN article ON article.id = comment.article_id
WHERE article.published_at > ?
ORDER BY article.published_at DESC
LIMIT 5
```

To get data from the select, all we do is loop through the returned array:

```php
foreach ($query as $row) {
    echo "$row['title']\n";
}
```

## Using the Smart Join Builder

Let's start with a traditional join, below:

```php
$query = $swift->from('article')
             ->leftJoin('user ON user.id = article.user_id')
             ->select('user.name');
```

That's pretty verbose, and not very smart. If your tables use proper primary and foreign key names, you can shorten the above to:

```php
$query = $swift->from('article')
             ->leftJoin('user')
             ->select('user.name');
```

That's better, but not ideal. However, it would be even easier to **not write any joins**:

```php
$query = $swift->from('article')
             ->select('user.name');
```

Awesome, right? SwiftPDO is able to build the join for you, by you prepending the foreign table name to the requested column.

All three snippets above will create the exact same query:

```mysql
SELECT article.*, user.name 
FROM article 
LEFT JOIN user ON user.id = article.user_id
```

##### Close your connection

Finally, it's always a good idea to free resources as soon as they are done with their duties:
 
 ```php
$swift->close();
```

## CRUD Query Examples

##### SELECT

```php
$query = $swift->from('article')->where('id', 1)->fetch();
$query = $swift->from('user', 1)->fetch(); // shorter version if selecting one row by primary key
```

##### INSERT

```php
$values = array('title' => 'article 1', 'content' => 'content 1');

$query = $swift->insertInto('article')->values($values)->execute();
$query = $swift->insertInto('article', $values)->execute(); // shorter version
```

##### UPDATE

```php
$set = array('published_at' => new SwiftLiteral('NOW()'));

$query = $swift->update('article')->set($set)->where('id', 1)->execute();
$query = $swift->update('article', $set, 1)->execute(); // shorter version if updating one row by primary key
```

##### DELETE

```php
$query = $swift->deleteFrom('article')->where('id', 1)->execute();
$query = $swift->deleteFrom('article', 1)->execute(); // shorter version if deleting one row by primary key
```

***Note**: INSERT, UPDATE and DELETE queries will only run after you call `->execute()`*

## TESTS
- Tests for project at: https://github.com/reactivewebtech/swiftpdo-tests.git

## License

Free for commercial and non-commercial use under the [Apache 2.0](http://www.apache.org/licenses/LICENSE-2.0.html) or [GPL 2.0](http://www.gnu.org/licenses/gpl-2.0.html) licenses.
