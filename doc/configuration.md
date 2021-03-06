[Documentation index](index.md)

# Configuring Noder

## How to configure Noder

* Noder can be configured by setting the configuration in the script tag which loads it.
It will be the configuration of the default context.
For example:

```html
<script type="text/javascript" src="noder.js">
{
	// put the configuration of the default context here
}
</script>
```

* It is also possible to create a new context with a specific configuration.
For example, supposing ``noder`` is the global variable exposed by Noder (corresponding to the default context),
the following code creates a new context with the specified configuration:

```js
var newNoder = noder.createContext({
	// put the configuration here
});
```

Then, ``newNoder`` is a new context, completely independent from ``noder``.

## Configuration options

Noder supports the following configuration options:

```js
{
  varName: 'noder', // Name of the global variable exposed by Noder
  // The default value for the default context in a browser is "noder".
  // There is no default for other contexts than the default one.
  // If it is null or contains an empty string, Noder does not expose any global variable.

  scriptsType: 'noder', // Type of scripts to execute as CommonJS modules.
  // The default is to use the same value as varName.
  // If it contains an empty string, Noder does not look for scripts to execute as modules.

  main: "lib/myMainModule", // Main module to load when starting
  // If this parameter is defined in the configuration object of the default context,
  // it overrides the value defined after the question mark in the Noder script tag.
  // A module loaded this way is considered the main module, available through require.main.

  onstart : function (mainModule) {
    // Function to be executed when the main module has been loaded.
    // The reference to the exported object is given as a parameter.
  },

  resolver: {
    "default" : {
      // Default configuration for the resolver.
      // This object describes the tree of modules.
      // An object value means that a directory with that name exists.
      // A string value means a redirection to some other file/directory
      // The special '.' key specifies the file to use when requiring the directory.

      "underscore" : {}, // require.resolve("underscore") will return "underscore/index.js"
      "uglify-js" : {
        "." : "uglify-js.js" // require.resolve("uglify-js") will return "uglify-js/uglify-js.js"
      },
      "markdown" : {
        "lib" : {
          "." : "markdown.js" // require.resolve("markdown/lib") will return "markdown/lib/markdown.js"
        },
        "." : "lib", // require.resolve("markdown") will also return "markdown/lib/markdown.js"
      }
      // require.resolve("otherLib") will return "otherLib.js"
      // require.resolve("otherLib/myFile") will return "otherLib/myFile.js"
    }
  },

  packaging: {
    baseUrl: "", // Base URL which is prepended to every path when requesting files to the server.
    // It can be either absolute or relative to the current page location.
    requestConfig: {
      // Options passed to the noder-js/request module when requesting files to the server.
      // Here are some useful properties:
      sync: false, // whether requests are synchronous (true) or asynchronous (false, default value).
      headers: {} // map of http headers and their values
    },
    packagesMap: {
      // This section specifies how files are packaged.
      // Any file not included in a package is processed in unpackaged mode.
      "markdown" : {
        // Include all files in the markdown folder (including files in sub-folders):
        "**" : "markdown.js"
      },
      "underscore" : {
        // Include all files in the underscore folder (not including files in sub-folders):
        "*" : "myPackage1.js",
        "exception.js" : "myPackage2.js" // exception.js is not in myPackage1.js but in myPackage2.js
      },
      "myFolder" : {
        "a.js" : "myPackage1.js",
        "b.js" : "myPackage1.js",
        "c.js" : "myPackage2.js"
      }
    }
  }
}
```
