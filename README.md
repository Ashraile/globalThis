# globalThis polyfill
Find globalThis in any environment without polluting the namespace


```javascript


   ;
    (function(GLOBAL, CONTEXT, undefined) {

        GLOBAL.TestingGlobalThis = GLOBAL;
        console.log(TestingGlobalThis); // window, node, etc

    })(
        (function() {
            if (typeof globalThis === 'object' && globalThis.globalThis === globalThis) { return globalThis; }    
            try {
                Object.defineProperty(Object.prototype, '__magic__', {
                    get: function() {
                        return this;
                    },
                    configurable: true
                });

                __magic__.globalThis = __magic__; 
                // The previous line should have made `globalThis` a globally available variable, but it fails in Internet Explorer 10 and older.
                // Detect this failure and fall back.

                var __global__ = (typeof globalThis === 'undefined') ? getThis() : globalThis; // cache our `globalThis` locally to avoid polluting the namespace
                
                delete __magic__.globalThis;       // clean up `globalThis` from global namespace (IE notwithstanding) as we're not strictly polyfilling it here.
                delete Object.prototype.__magic__; // clean up `__magic__` from `Object.prototype`.

                return __global__;                 // console.log(__global__, typeof globalThis === 'undefined'); // globalThis, true

            } catch (e) {
                return getThis(); // In IE8, Object.defineProperty only works on DOM objects. If we hit this code path, assume `window` exists.
            }

            function getThis() {
                // if we're here it's almost certainly Internet Explorer so check for a window.
                if (typeof window !== 'undefined' && window && window.window === window && typeof window.document !== 'undefined') {
                    return window; // if this much effort was taken to simulate a browser environment, assume they know what they're doing
                } else {
                    try {
                        return this;
                    } catch (e) {

                    }
                }
            };

        })()
    );
```
