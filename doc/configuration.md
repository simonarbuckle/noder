[Documentation index](index.md)

# Configuring Noder

## How to configure Noder

* Noder can be configured by setting the configuration in the script tag which loads it.
It will be the configuration of the default context.
For example:

```html
<script type="noder.js">
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

  main: "lib/myMainModule", // Main module to load when starting (with an optional method to call).
  // If this parameter is defined in the configuration object of the default context,
  // it overrides the value defined after the question mark in the Noder script tag.
  // A module loaded this way is considered the main module, available through require.main.

  onstart : function (mainModule) {
    // Function to be executed when the main module has been loaded (and the optional method called).
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
    },
    "profiles": {
      // Profiles allow different resolver configurations to be applied to different modules.
      "q0.6" : {
        "q": "q-0.6.js" // in this profile, require.resolve("q") will return "q-0.6.js"
      },
      "q0.8" : {
        "q": "q-0.8.js" // in this profile, require.resolve("q") will return "q-0.8.js"
      },
      "underscore1.2" : {
        // It is possible to override a parameter from the default configuration.
        // In this profile, require.resolve("underscore") will return "underscore-1.2/index.js":
        "underscore" : "underscore-1.2",
        "underscore-1.2": {}
      }
    },
    "activations": {
      // This section specifies when profiles are enabled.
      "a" : "q0.8", // from module "a", (or anything inside, e.g. "a/b.js" or "a/d/e.js"),
      // profile q0.8 will be enabled
      "b" : {
        "*" : "q0.6", // from module "b", with the exception of "b/c" and "b/d",
        // profile q0.6 will be enabled
        "c" : "underscore1.2", // from module "b/c", profile underscore1.2 will be enabled
        "d" : "q0.6,underscore1.2" // from module "b/d", profiles q0.6 and underscore1.2 will be enabled
      }
    }
  },

  packaging: {
    baseUrl: "", // Base URL which is prepended to every path when requesting files to the server.
    // It can be either absolute or relative to the current page location.
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