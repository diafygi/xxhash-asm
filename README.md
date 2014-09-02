xxHash-asm.js - Javascript port of xxHash
========================

**NOTE: THIS LIBRARY CURRENTLY DOES NOT WORK. DO NOT USE!**

The is an [emscripten](https://github.com/kripken/emscripten) javascript build of
[xxHash](https://code.google.com/p/xxhash/), which is a super fast hashing library.

I also added a barebones wrapper so you don't have to mess with all the memory
allocation and emscripten Module.ccall nonsense.

###Demo

https://diafygi.github.io/xxhash-asm/demo.html

###How to use

1. Include the library.

```html
<script src="xxhash-asm.js"></script>
```

2. Call the `xxHash()` function, which returns an integer for the hash.

```javascript
//accepts strings
console.log(
    xxHash("Hello World!")
);

//accepts typed arrays
console.log(
    xxHash(new Uint8Array([1,2,3,4]))
);

//can be seeded
console.log(
    xxHash("Seed me!", 12345)
);
```

###How to build yourself

1. `emcc -O2 -s EXPORTED_FUNCTIONS="['_XXH32']" c/xxhash.c -o xxhash-asm.js`
2. Add the following wrapper function to the beginning of `xxhash-asm.js`

```javascript
function xxHash(arr, seed){
    //convert string input to typed array
    if(typeof(arr) === "string"){
        var tmp_arr = new Uint32Array(arr.length);
        for(var i = 0; i < arr.length; i++){
            tmp_arr[i] = arr[i].charCodeAt();
        }
        arr = tmp_arr;
    }
    //run xxhash array
    var len = arr.length * arr.BYTES_PER_ELEMENT;
    var buf = Module._malloc(len);
    Module.HEAPU8.set(arr, buf);
    var result = Module.ccall('XXH32', 'number', ['number', 'number', 'number'], [buf, len, seed || 0]);
    Module._free(buf);
    return result;
}
```

###License

This code is released under the [BSD New](http://opensource.org/licenses/BSD-3-Clause)
license, which is the same as the original xxHash library.

