Node.js follows the CommonJS module system and the builtin `require` function is the easiest way to included files. The basic functionality of `require` is that it reads a javascript file, executes the file, and then proceeds to return the `exports` object. An example module:

    console.log("evaluating example.js");

    no_one_can_see_this = function () {
      console.log("invisible");
    }

    exports.message = "hi";

    exports.say = function () {
      console.log(message);
    }

So if you run `var example = require('./example.js')`, then `example.js` will get evaluated and then `example` be an object equal to:

    {
      message: "hi",
      say: [Function]
    }

If you want to set the exports object to a function or a new object, you have to use the `module.exports` object. So for an example:

    module.exports = function () {
      console.log("hello world")
    }

    require('./example2.js')() //require itself and run the exports object
    
This is an example of a common gotcha: each time you require a file, the `exports` object is cached and reused. An illustration of this point is: 

    node> require('./example.js')
    evaluating example.js
    { message: 'hi', say: [Function] }
    node> require('./example.js')
    { message: 'hi', say: [Function] }
    node> require('./example.js').message = "hey" //set the message to "hey"
    'hey'
    node> require('./example.js') //"reload" the file
    { message: 'hey', say: [Function] } //but the message is still "hey" because it is from the cache

As you can see `example.js` is evaluated the first time `require('./example.js')` is evaluated and the second time the object is returned from a cache. For extra emphasis, it is the *same object* both time.

The rules of where `require` finds the files can be a little complex, but the simple rule of thumb, if the file doesn't start with "./" or "/", then either considered a core module or a dependency in the `node_modules` folder. If the file starts with "./" it is considered a relative file to the file that called `require`. If the file starts with "/", it is considered an absolute path. NOTE: you can omited ".js" and `require` will automatically append it if needed. For more detailed information, http://nodejs.org/docs/v0.4.2/api/modules.html#all_Together...

An extra note, if the "file" passed to require is actually a directory, it will first look for `package.json` in the directory and load the file referenced in the `main` property. Otherwise it will try and load `index.js`.

There is a global variable called `require.paths` that you can modify to change the directories that `require` searches, but generally you should avoid touching it. It changes for all the search paths in your project which can have some unexpected side effects. Also, it may be removed in future releases.