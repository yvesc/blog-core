# blog-core

A Prolog library extracted from the [blog](https://github.com/rla) application.
It is work-in-progress and every piece of its API could change any time.

## Features

 * Document-based in-memory data storage.
 * Generic administration interface.
 * Simple HTTP request routing.
 * Built-in cache busting.

## Dependencies

TODO
 
## Modules

### bc_doc

Provides various predicates for working with documents.

 * `doc_id(+Doc, -Id)`. Extracts ID from the given document.
 
## Admin interface

For a document collection to appear in the admin interface, it must be first
registered into types collection. The document describing display/edit
information has to be inserted.

TODO

This creates descriptor document for documents in the `config` collection.
Meaning of terms is the following:

 * `name(config)` - the name of the collection.
 * `list([ name, value ])` - display in the list view properties `name` and `value`.
    
### Property types

 * `line` - single-line text. Editor `<input type="text">` is used.
 * `multiline` - multi-line text. Editor `<textarea></textarea>` is used.
 * `password` - password with masked editor. Editor `<input type="password">` is used.
 * `boolean` - checkbox. Editor `<input type="checkbox">` is used.
 * `choice` - combobox. Editor `<select></select>` is used.
 
## Provided functionality

TODO

### bc_html

`embed_block(+Slug)//`

Emits HTML for static block. Block contents is added without escaping. Does nothing
when the block is not found.

`embed_post(+Slug)//`

Emits HTML for the given post. Post settings (type, commenting, published)
do not matter. Does nothing when the post is not found.

`meta_headers//`

Emits HTML `<meta>` tags with configured values. When no tags have been configured,
it does nothing.

### bc_bust

Loading this module will install rewrite handler to remove
the request URL path prefix `/t-[0-9]+`.

`bs_bust_token(-Token)`

Retrieves the token for cache busting. Currently it is set to
the server start timestamp.

When serving static files with Nginx, the rewrite rule can be
handled with Nginx's own rewrite rule:

    location ~ ^/t-\d+/(.*)$ {
        access_log off;
        rewrite ^/t-\d+/(.*)$ /$1 last;
    }

## Known issues

Loading `library(http/http_files)` will install some `http_dispatch` handlers
that cause error 500 response for urls like `/css/non-existent.css`.

## Generic top-level module:

    :- use_module(library(bc/bc_daemon)).
    :- bc_daemon('file.docstore').

## Running with REPL

    swipl -s blog.pl -- --port=8001 --fork=false

## Running as a daemon

This uses the [http_unix_daemon](http://www.swi-prolog.org/pldoc/man?section=httpunixdaemon)
module under the hood.

Then starting as daemon takes the following command:

    swipl -s blog.pl -- --port=8001