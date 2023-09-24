<!--

@license Apache-2.0

Copyright (c) 2021 The Stdlib Authors.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

-->


<details>
  <summary>
    About stdlib...
  </summary>
  <p>We believe in a future in which the web is a preferred environment for numerical computation. To help realize this future, we've built stdlib. stdlib is a standard library, with an emphasis on numerical and scientific computation, written in JavaScript (and C) for execution in browsers and in Node.js.</p>
  <p>The library is fully decomposable, being architected in such a way that you can swap out and mix and match APIs and functionality to cater to your exact preferences and use cases.</p>
  <p>When you use stdlib, you can be absolutely certain that you are using the most thorough, rigorous, well-written, studied, documented, tested, measured, and high-quality code out there.</p>
  <p>To join us in bringing numerical computing to the web, get started by checking us out on <a href="https://github.com/stdlib-js/stdlib">GitHub</a>, and please consider <a href="https://opencollective.com/stdlib">financially supporting stdlib</a>. We greatly appreciate your continued support!</p>
</details>

# Function Object

[![NPM version][npm-image]][npm-url] [![Build Status][test-image]][test-url] [![Coverage Status][coverage-image]][coverage-url] <!-- [![dependencies][dependencies-image]][dependencies-url] -->

> C APIs for creating and managing ndarray function objects.

<!-- Section to include introductory text. Make sure to keep an empty line after the intro `section` element and another before the `/section` close. -->

<section class="intro">

</section>

<!-- /.intro -->

<!-- Package usage documentation. -->

<section class="installation">

## Installation

```bash
npm install @stdlib/ndarray-base-function-object
```

Alternatively,

-   To load the package in a website via a `script` tag without installation and bundlers, use the [ES Module][es-module] available on the [`esm` branch][esm-url].
-   If you are using Deno, visit the [`deno` branch][deno-url].
-   For use in Observable, or in browser/node environments, use the [Universal Module Definition (UMD)][umd] build available on the [`umd` branch][umd-url].

The [branches.md][branches-url] file summarizes the available branches and displays a diagram illustrating their relationships.

</section>

<section class="usage">

## Usage

```javascript
var headerDir = require( '@stdlib/ndarray-base-function-object' );
```

#### headerDir

Absolute file path for the directory containing header files for C APIs.

```javascript
var dir = headerDir;
// returns <string>
```

</section>

<!-- /.usage -->

<!-- Package usage notes. Make sure to keep an empty line after the `section` element and another before the `/section` close. -->

<section class="notes">

</section>

<!-- /.notes -->

<!-- Package usage examples. -->

<section class="examples">

## Examples

```javascript
var headerDir = require( '@stdlib/ndarray-base-function-object' );

console.log( headerDir );
```

</section>

<!-- /.examples -->

<!-- C interface documentation. -->

* * *

<section class="c">

## C APIs

<!-- Section to include introductory text. Make sure to keep an empty line after the intro `section` element and another before the `/section` close. -->

<section class="intro">

</section>

<!-- /.intro -->

<!-- C usage documentation. -->

<section class="usage">

### Usage

```c
#include "stdlib/ndarray/base/function_object.h"
```

#### ndarrayFunctionObject

Structure for grouping ndarray function information.

```c
struct ndarrayFunctionObject {
    // ndarray function name:
    const char *name;

    // Number of input ndarrays:
    int32_t nin;

    // Number of output ndarrays:
    int32_t nout;

    // Total number of ndarray arguments (nin + nout):
    int32_t narrays;

    // Array containing ndarray functions:
    ndarrayFcn *functions;

    // Number of ndarray functions:
    int32_t nfunctions;

    // Array of type "numbers" (as enumerated elsewhere), where the total number of types equals `narrays * nfunctions` and where each set of `narrays` consecutive types (non-overlapping) corresponds to the set of ndarray argument types for a corresponding ndarray function:
    int32_t *types;

    // Array of void pointers corresponding to the "data" (e.g., callbacks) which should be passed to a respective ndarray function (note: the number of pointers should match the number of ndarray functions):
    void **data;
};
```

#### ndarrayFcn

Function pointer type for an ndarray function.

```c
typedef int8_t (*ndarrayFcn)( struct ndarray *arrays[], void *data );
```

An `ndarrayFcn` function should accept the following arguments:

-   **arrays**: `[in] struct ndarray**` array containing pointers to input and output ndarrays.
-   **data**: `[in] void*` function data (e.g., a callback).

An `ndarrayFcn` function should return a status code (with `0` indicating success).

<!-- lint disable maximum-heading-length -->

#### stdlib_ndarray_function_allocate( _name, nin, nout, _functions, nfunctions, _types, _data\[] )

Returns a pointer to a dynamically allocated ndarray function object.

```c
#include "stdlib/ndarray/base/unary.h"
#include "stdlib/ndarray/dtypes.h"
#include <stdlib.h>
#include <stdio.h>

// Define the function(s) we want to apply to ndarrays:
double scale( const double x ) {
    return x * 10.0;
}

// Define a function name:
const char name[] = "unary_ndarray_function";

// Define a list of ndarray functions (in this case, as the function to be applied accepts doubles, we only use ndarray functions which handle doubles as function arguments and, for the purposes of this example, we assume that the output ndarray is always a double-precision floating-point number array):
ndarrayFcn functions[] = {
    stdlib_ndarray_d_d,
    stdlib_ndarray_f_f_as_d_d,
    stdlib_ndarray_u_d_as_d_d,
    stdlib_ndarray_i_d_as_d_d,
    stdlib_ndarray_t_d_as_d_d,
    stdlib_ndarray_k_d_as_d_d,
    stdlib_ndarray_b_d_as_d_d,
    stdlib_ndarray_s_d_as_d_d
};

// Define the **ndarray** argument types for each ndarray function:
int32_t types[] = {
    STDLIB_NDARRAY_FLOAT64, STDLIB_NDARRAY_FLOAT64,
    STDLIB_NDARRAY_FLOAT32, STDLIB_NDARRAY_FLOAT64,
    STDLIB_NDARRAY_UINT32, STDLIB_NDARRAY_FLOAT64,
    STDLIB_NDARRAY_INT32, STDLIB_NDARRAY_FLOAT64,
    STDLIB_NDARRAY_UINT16, STDLIB_NDARRAY_FLOAT64,
    STDLIB_NDARRAY_INT16, STDLIB_NDARRAY_FLOAT64,
    STDLIB_NDARRAY_UINT8, STDLIB_NDARRAY_FLOAT64,
    STDLIB_NDARRAY_INT8, STDLIB_NDARRAY_FLOAT64
};

// Define a list of ndarray function "data" (in this case, callbacks):
void *data[] = {
    (void *)scale,
    (void *)scale,
    (void *)scale,
    (void *)scale,
    (void *)scale,
    (void *)scale,
    (void *)scale,
    (void *)scale
};

// Create a new ndarray function object:
struct ndarrayFunctionObject *obj = stdlib_ndarray_function_allocate( name, 1, 1, functions, 8, types, data );
if ( obj == NULL ) {
    fprintf( stderr, "Error allocating memory.\n" );
    exit( 1 );
}

// Free allocated memory:
stdlib_ndarray_function_free( obj );
```

The function accepts the following arguments:

-   **name**: `[in] char*` ndarray function name.
-   **nin**: `[in] int32_t` number of input ndarrays.
-   **nout**: `[in] int32_t` number of output ndarrays.
-   **functions**: `[in] ndarrayFcn*` array containing ndarray functions.
-   **nfunctions**: `[in] int32_t` number of ndarray functions.
-   **types**: `[in] int32_t*` array of type "numbers", where the total number of types equals `(nin+nout)*nfunctions` and where each set of `nin+nout` consecutive types (non-overlapping) corresponds to the set of ndarray argument types for a corresponding ndarray function.
-   **data**: `[in] void*` array of void pointers corresponding to the "data" (e.g., callbacks) which should be passed to a respective ndarray function.

```c
struct ndarrayFunctionObject * stdlib_ndarray_function_allocate( const char *name, int32_t nin, int32_t nout, ndarrayFcn *functions, int32_t nfunctions, int32_t *types, void *data[] )
```

The function returns a pointer to a dynamically allocated ndarray function or, if unable to allocate memory, a null pointer. The **user** is responsible for freeing the allocated memory.

#### stdlib_ndarray_function_free( \*obj )

Frees an ndarray function object's allocated memory.

```c
#include "stdlib/ndarray/base/unary.h"
#include "stdlib/ndarray/dtypes.h"
#include <stdlib.h>
#include <stdio.h>

// Define the function(s) we want to apply to ndarrays:
double scale( const double x ) {
    return x * 10.0;
}

// Define a function name:
const char name[] = "unary_ndarray_function";

// Define a list of ndarray functions:
ndarrayFcn functions[] = {
    stdlib_ndarray_d_d
};

// Define the **ndarray** argument types for each ndarray function:
int32_t types[] = {
    STDLIB_NDARRAY_FLOAT64, STDLIB_NDARRAY_FLOAT64
};

// Define a list of ndarray function "data" (in this case, callbacks):
void *data[] = {
    (void *)scale
};

// Create a new ndarray function object:
struct ndarrayFunctionObject *obj = stdlib_ndarray_function_allocate( name, 1, 1, functions, 1, types, data );
if ( obj == NULL ) {
    fprintf( stderr, "Error allocating memory.\n" );
    exit( 1 );
}

// ...

// Free allocated memory:
stdlib_ndarray_function_free( obj );
```

The function accepts the following arguments:

-   **obj**: `[in] ndarrayFunctionObject*` ndarray function object.

```c
void stdlib_ndarray_function_free( struct ndarrayFunctionObject *obj )
```

#### stdlib_ndarray_function_dispatch_index_of( _obj, _types )

Returns the first index of a function whose signature satisfies a provided list of array types.

```c
#include "stdlib/ndarray/base/unary.h"
#include "stdlib/ndarray/dtypes.h"
#include <stdlib.h>
#include <stdio.h>

// Define the function(s) we want to apply to ndarrays:
double scale( const double x ) {
    return x * 10.0;
}

// ...

// Define a function name:
const char name[] = "unary_ndarray_function";

// Define a list of ndarray functions:
ndarrayFcn functions[] = {
    stdlib_ndarray_d_d,
    stdlib_ndarray_f_f_as_d_d
};

// Define the **ndarray** argument types for each ndarray function:
int32_t types[] = {
    STDLIB_NDARRAY_FLOAT64, STDLIB_NDARRAY_FLOAT64,
    STDLIB_NDARRAY_FLOAT32, STDLIB_NDARRAY_FLOAT64
};

// Define a list of ndarray function "data" (in this case, callbacks):
void *data[] = {
    (void *)scale,
    (void *)scale
};

// Create a new ndarray function object:
struct ndarrayFunctionObject *obj = stdlib_ndarray_function_allocate( name, 1, 1, functions, 2, types, data );
if ( obj == NULL ) {
    fprintf( stderr, "Error allocating memory.\n" );
    exit( 1 );
}

// ...

// Define a list of types on which to dispatch:
int32_t itypes[] = {
    STDLIB_NDARRAY_FLOAT32, STDLIB_NDARRAY_FLOAT64
};

// Find a function satisfying the list of types:
int64_t idx = stdlib_ndarray_function_dispatch_index_of( obj, itypes );
if ( idx < 0 ) {
    fprintf( stderr, "Unable to find function.\n" );
    exit( 1 );
}

// ...

// Free allocated memory:
stdlib_ndarray_function_free( obj );
```

The function accepts the following arguments:

-   **obj**: `[in] ndarrayFunctionObject*` ndarray function object.
-   **types**: `[in] int32_t*` list of array types on which to dispatch.

```c
int64_t stdlib_ndarray_function_dispatch_index_of( const struct ndarrayFunctionObject *obj, const int32_t *types )
```

If a function is found, the function returns the index of the function, and the function returns `-1` if unable to find a function.

</section>

<!-- /.usage -->

<!-- C API usage notes. Make sure to keep an empty line after the `section` element and another before the `/section` close. -->

<section class="notes">

</section>

<!-- /.notes -->

<!-- C API usage examples. -->

<section class="examples">

</section>

<!-- /.examples -->

</section>

<!-- /.c -->

<!-- Section to include cited references. If references are included, add a horizontal rule *before* the section. Make sure to keep an empty line after the `section` element and another before the `/section` close. -->

<section class="references">

</section>

<!-- /.references -->

<!-- Section for related `stdlib` packages. Do not manually edit this section, as it is automatically populated. -->

<section class="related">

</section>

<!-- /.related -->

<!-- Section for all links. Make sure to keep an empty line after the `section` element and another before the `/section` close. -->


<section class="main-repo" >

* * *

## Notice

This package is part of [stdlib][stdlib], a standard library for JavaScript and Node.js, with an emphasis on numerical and scientific computing. The library provides a collection of robust, high performance libraries for mathematics, statistics, streams, utilities, and more.

For more information on the project, filing bug reports and feature requests, and guidance on how to develop [stdlib][stdlib], see the main project [repository][stdlib].

#### Community

[![Chat][chat-image]][chat-url]

---

## License

See [LICENSE][stdlib-license].


## Copyright

Copyright &copy; 2016-2023. The Stdlib [Authors][stdlib-authors].

</section>

<!-- /.stdlib -->

<!-- Section for all links. Make sure to keep an empty line after the `section` element and another before the `/section` close. -->

<section class="links">

[npm-image]: http://img.shields.io/npm/v/@stdlib/ndarray-base-function-object.svg
[npm-url]: https://npmjs.org/package/@stdlib/ndarray-base-function-object

[test-image]: https://github.com/stdlib-js/ndarray-base-function-object/actions/workflows/test.yml/badge.svg?branch=v0.1.0
[test-url]: https://github.com/stdlib-js/ndarray-base-function-object/actions/workflows/test.yml?query=branch:v0.1.0

[coverage-image]: https://img.shields.io/codecov/c/github/stdlib-js/ndarray-base-function-object/main.svg
[coverage-url]: https://codecov.io/github/stdlib-js/ndarray-base-function-object?branch=main

<!--

[dependencies-image]: https://img.shields.io/david/stdlib-js/ndarray-base-function-object.svg
[dependencies-url]: https://david-dm.org/stdlib-js/ndarray-base-function-object/main

-->

[chat-image]: https://img.shields.io/gitter/room/stdlib-js/stdlib.svg
[chat-url]: https://app.gitter.im/#/room/#stdlib-js_stdlib:gitter.im

[stdlib]: https://github.com/stdlib-js/stdlib

[stdlib-authors]: https://github.com/stdlib-js/stdlib/graphs/contributors

[umd]: https://github.com/umdjs/umd
[es-module]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules

[deno-url]: https://github.com/stdlib-js/ndarray-base-function-object/tree/deno
[umd-url]: https://github.com/stdlib-js/ndarray-base-function-object/tree/umd
[esm-url]: https://github.com/stdlib-js/ndarray-base-function-object/tree/esm
[branches-url]: https://github.com/stdlib-js/ndarray-base-function-object/blob/main/branches.md

[stdlib-license]: https://raw.githubusercontent.com/stdlib-js/ndarray-base-function-object/main/LICENSE

</section>

<!-- /.links -->
