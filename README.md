[![npm version](https://badge.fury.io/js/route-node.svg)](http://badge.fury.io/js/route-node)
[![Build Status](https://travis-ci.org/troch/route-node.svg?branch=master)](https://travis-ci.org/troch/route-node)
[![Coverage Status](https://coveralls.io/repos/troch/route-node/badge.svg?branch=master)](https://coveralls.io/r/troch/route-node?branch=master)


# route-node

A package to create a tree (trie) of named routes. It is similar to [routington](https://www.npmjs.com/package/routington) except that nodes are not added by splitting path by segment ("/"). Instead the tree is built with the supplied nodes, meaning each node is a valid route.


## Install

    $ npm install route-node --save

## Usage

Building your route tree:

```javascript
import rootNode from 'route-node';

// Create nodes
const usersNode = new RouteNode('users', '/users', [
    new RouteNode('list', '/list'),
    new RouteNode('view', '/view/:id')
]);

// You can also use plain objects
const ordersNode = new RouteNode('orders', '/orders', [
    {name: 'pending',   path: '/pending'},
    {name: 'completed', path: '/completed'},
    {name: 'view',      path: '/view/:id'}
]);

// Creating a top node
const rootNode = new RouteNode('', '', [
    ordersNode,
    usersNode
]);

// Add nodes programmatically
rootNode.add(new RouteNode('home', '/home'));
```
You can chain constructor with `add` and `addNode` functions, making the example above shorter:

```javascript
const rootNode = new RouteNode()
    .addNode('users',            '/users'))
    .addNode('users.view',       '/view/:id')
    .addNode('users.list',       '/list')
    .addNode('orders',           '/orders')
    .addNode('orders.pending',   '/pending')
    .addNode('orders.completed', '/completed')
    .addNode('orders.view',      '/view/:id')
```

And then build paths, or match your paths against your tree:

```javascript

rootNode.getPath('users.view');                // => "/users/view/:id"
rootNode.buildPath('users.view', {id: 1});     // => "/users/view/1"

rootNode.matchPath('/users/view/1');           // => {name: "users.view", params: {id: "1"}}
```

Trailing slash can be optional:

```javascript
rootNode.matchPath('/users/view/1');           // => {name: "users.view", params: {id: "1"}}
rootNode.matchPath('/users/view/1/');          // => null

rootNode.matchPath('/users/view/1/', { trailingSlash: true });
// => {name: "users.view", params: {id: "1"}}
```

Query parameters are optional, however a match will fail if the URL contains non-expected query parameters. This can be prevented by setting `strictQueryParams` to false.

## Callbacks

When adding routes (with contructor or `.add`), you can pass a callback which will be executed for each route added successfully to the tree.

## Related packages

- [routington](https://www.npmjs.com/package/routington)

## Based on

- [parth-parser](https://www.npmjs.com/package/path-parser) for parsing, matching and building paths.
