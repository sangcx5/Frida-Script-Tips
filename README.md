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
