# My Frida Script

### 1. Search String

Search where a specific string is set in android

```javascript
let TextView = Java.use("android.widget.TextView");
TextView.setText.overload("java.lang.CharSequence").implementation = function (text) {
    if (!text) return this.setText(text);
    if (text.toString() === "Hello what's up") {
        // This log will show where is "Hello what's up" is set
        console.log(Java.use("android.util.Log").getStackTraceString(Java.use("java.lang.Exception").$new()));
    }
    return this.setText(text);
};
```

Or try other "overload" if no result

```
.overload('int')
.overload('int', 'android.widget.TextView$BufferType')
.overload('java.lang.CharSequence', 'android.widget.TextView$BufferType')
.overload('[C', 'int', 'int')
.overload('java.lang.CharSequence', 'android.widget.TextView$BufferType', 'boolean', 'int')
```

### 2. Get list of methods defined inside a class

```javascript
function listOfMethods(className) {
    // for ex: className = com.sangcx.lab.MainActivity
    console.log("\n======== List of methods of " + className + "========");
    let targetClass = Java.use(className);
    let methods = targetClass.class.getDeclaredMethods();
    for (let i = 0; i < methods.length; i++) {
        console.log(methods[i].toString());
    }
    console.log("========" + className + "========\n\n");
}
```
