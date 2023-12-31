# My Frida Script Tips

### 1. Find String

Find where a specific string is set in android app

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

### 2. Find Button

Find where a "OnClickListener" is set for a button in android app

#### Find by text

```javascript
const Button = Java.use("android.widget.Button");
Button.setOnClickListener.implementation = function (listener) {
    try {
        const btn = Java.cast(this, Java.use("android.widget.Button"));
        const text = btn.getText().toString();
        if (text === "Click me") {
            // will show the location
            console.log(Java.use("android.util.Log").getStackTraceString(Java.use("java.lang.ClassCastException").$new()));
        }
    } catch (error) {
        
    }
    this.setOnClickListener(listener);
};
```

#### Find by ID

Use script below and click to button to get it's "ID\_INT"

```javascript
const Button = Java.use("android.widget.Button");
const context = Java.use("android.app.ActivityThread").currentApplication().getApplicationContext();
const packageName = context.getPackageName();
const resources = context.getResources();

Button.setOnClickListener.implementation = function (listener) {
    const clone = Java.retain(listener);
    let text = "";
    try {
        const btn = Java.cast(this, Java.use("android.widget.Button"));
        text = btn.getText().toString();
    } catch {
        text = "";
    }
    Java.registerClass({
        name: "com.example.CustomListener",
        implements: [clone],
        methods: {
            onClick: function (view) {
                const buttonXMLId = view.toString().split("app:")[1].replace("}", "").split("/")[1];
                const buttonIntId = resources.getIdentifier(buttonXMLId, "id", packageName);
                console.log(`Button ${text ? '"' + text + '"' : ""} with {XML_ID = ${buttonXMLId}} and {ID_INT = ${buttonIntId}} was clicked`);
                clone.onClick(view);
            },
        },
    });
    const customListenerClass = Java.use("com.example.CustomListener");
    const customListenerInstance = customListenerClass.$new();
    this.setOnClickListener(customListenerInstance);
};

```

use ID\_INT above to get result

```javascript
const Button = Java.use("android.widget.Button");

Button.setOnClickListener.implementation = function (listener) {
    try {
        const INT_ID = 2131230818; // change it
        if (this.getId() === INT_ID) {
            // get location here
            console.log(Java.use("android.util.Log").getStackTraceString(Java.use("java.lang.ClassCastException").$new()));
        }
    } catch (error) {}
    this.setOnClickListener(listener);
};

```

### 3. Get list of methods defined inside a class

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
