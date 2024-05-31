---
title: Neo4j Database with Php
slug: my-first-post
description: Learn how to use neo4j with PHP
tags:
  - php-and-neo4j
published_at: 2024-05-31T13:58:46.857Z
last_modified_at: 2024-05-31T13:58:46.857Z
image: /assets/blog/imgg.png
---

# Exploring neo4j-php/Bolt: A PHP Library for Neo4j

![neo4j](/assets/blog/imgg.png)

If you're working with PHP and need to connect to a Neo4j database, the **neo4j-php/Bolt** library is a fantastic tool to consider. This PHP library allows you to connect to a graph database over a TCP socket using the Bolt protocol, making it easier to handle data that’s connected by relationships rather than just tables.

## Why Neo4j?

Before diving into Bolt, let’s understand Neo4j. It’s a graph database engine that excels in handling data with complex relationships. Unlike traditional databases that use tables, Neo4j uses a graph structure, which can be more intuitive and efficient for certain types of data.

If you’re new to Neo4j, check out this [introductory playlist](https://youtube.com/playlist?list=PL9Hl4pk2FsvWdxnoWQiwH8H7J7MBJ69). It’s a great starting point.


## What is Bolt?

Bolt is a Neo4j driver for PHP, designed to provide a robust connection to a Neo4j database. It supports all available versions of the Bolt protocol and stays updated with Neo4j's specifications.

### Key Features

- **Composer Installation**: Easily install via Composer.
- **Comprehensive Documentation**: Clear and detailed README available.
- **SSL Support**: Handles SSL and self-signed certificates.
- **PDO Integration**: Works with PDO via the `pdo-bolt` add-on.
- **PHP Compatibility**: Supports PHP 7, 8, and newer versions.


## Getting Started

To use Bolt, you’ll need a PHP application and a running Neo4j database. Here’s how to set up Neo4j locally using Docker:

```sh
docker run \
    --publish=7474:7474 \
    --publish=7687:7687 \
    --volume=$HOME/neo4j/data:/data \
    neo4j
```

This command does the following:

- Publishes the Neo4j HTTP access port (7474) and the Bolt access port (7687) to your machine.
- Mounts your local directory (`~/neo4j/data`) as the container’s data directory.

## Integrating Bolt with Your PHP Application

To integrate Bolt into your PHP app, you need to create a client to connect to the Neo4j server. Here’s a quick guide:

```php
// Create connection class and specify target host and port.
$conn = new \Bolt\connection\Socket('127.0.0.1', 7687);
// Create a new Bolt instance with the connection object.
$bolt = new \Bolt\Bolt($conn);
// Set the protocol versions (up to 4 versions can be added).
$bolt->setProtocolVersions(5.4);
// Build and get the protocol version instance to create the connection and execute the handshake.
$protocol = $bolt->build();
```

Once you have a client connection, initialize communication with the database:

```php
// Initialize communication with the database.
$response = $protocol->hello()->getResponse();
```

Verify that the `hello` handshake is successful. Next, authenticate (if required):

```php
// Log in to the database.
$response = $protocol->logon(['scheme' => 'basic', 'principal' => 'neo4j', 'credentials' => 'neo4j'])->getResponse();
```

You can then start sending queries and retrieving data:

```php
// Execute a query with parameters and pull records.
$protocol
    ->run('RETURN $a AS num, $b AS str', ['a' => 123, 'b' => 'text'])
    ->pull();

// Fetch responses for the pipelined messages.
foreach ($protocol->getResponses() as $response) {
    // Process each response.
}
```

## Contributing

Bolt is open to contributions. If you find any bugs or want to improve the codebase, feel free to submit issues or pull requests on [GitHub](https://github.com/neo4j-php/Bolt/issues).

With over 5 contributors and a growing user base, there’s a supportive community ready to help and collaborate.

Check out the [Bolt Protocol](https://neo4j.com/docs/bolt/current/) for more technical details.

Happy coding with Neo4j and PHP!