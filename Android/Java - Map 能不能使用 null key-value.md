# Java - Map 能不能使用 null key/value?

`Map` 接口本身的文档说了可能会抛出 NPE，但最终还是取决于具体的实现类。

### key = null ?

很不幸，能不能保存 null key 取决于具体的类型，而不取决于 `Map` 接口。一句话答案：

> HashMap、LinkedHashMap、IdentityHashMap 能使用 null key，ConcurrentHashMap、ConcurrentSkipListMap、TreeMap、HashTable 不能使用 null key。

HashMap 相关的几个非线程安全类型都可以使用 null key，其他的都不行，不行的意思是，执行 put(null, value) 方法的时候直接抛 NPE。

### value = null ?

同样，是否能使用 null value 也取决于具体的类型。一句话答案：

> HashMap、LinkedHashMap、TreeMap、IdentityHashMap 能使用 null value，Hashtable、ConcurrentHashMap、ConcurrentSkipListMap 不能使用  null value。

同样，不能使用的含义是 put(key, null) 时抛出 NPE。

### 推论

因此使用 Map 接口的时候：

- 正常运行的 `Map.put(key, value)`  可能由于 `key == null` 或者 `value == null`，导致不符合预期的行为。
- 并不能保证 `Map.put(null, value)` 能正常运行。
- 并不能保证 `Map.put(key, null)` 能正常运行。
- `Map.get(key) == null` 不能用来判断 `Map` 是否含有 `key`，应该用 `Map.containsKey(key)` 来判断。

### 实践原则

- 不要利用可以用 null 作为 key/value 的这个特性，一律视为不能为 null。
- 使用 Map 的 put/get 方法前要检查 null。
- 换成 Kotlin :)

### 代码验证

```java
public class TryMapNullKeyValue {
    public static void main(String[] args) {
        tryMapNullKeyValue(new HashMap<>());
        tryMapNullKeyValue(new LinkedHashMap<>());
        tryMapNullKeyValue(new TreeMap<>());
        tryMapNullKeyValue(new IdentityHashMap<>());
        tryMapNullKeyValue(new Hashtable<>());
        tryMapNullKeyValue(new ConcurrentHashMap<>());
        tryMapNullKeyValue(new ConcurrentSkipListMap<>());
    }

    private static void tryMapNullKeyValue(final Map<String, String> map) {
        System.out.println("try map null key/value for " + map.getClass());

        print("put(\"null\", null)", () -> {
            map.put("null", null);
            return "OK";
        });
        print("put(null, null)", () -> {
            map.put(null, null);
            return "OK";
        });

        print("get(null)=", () -> map.get(null));
        print("get(\"null\")=", () -> map.get("null"));
        print("containsKey(null)=", () -> map.containsKey(null));
        print("containsKey(\"null\")=", () -> map.containsKey("null"));

        System.out.println();
    }

    private static void print(String message, Callable<Object> callable) {
        try {
            System.out.println(message);
            System.out.println("    -> " + callable.call());
        } catch (NullPointerException e) {
            System.out.println("    -> NPE");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

运行结果

```
try map null key/value for class java.util.HashMap
put("null", null)
    -> OK
put(null, null)
    -> OK
get(null)=
    -> null
get("null")=
    -> null
containsKey(null)=
    -> true
containsKey("null")=
    -> true

try map null key/value for class java.util.LinkedHashMap
put("null", null)
    -> OK
put(null, null)
    -> OK
get(null)=
    -> null
get("null")=
    -> null
containsKey(null)=
    -> true
containsKey("null")=
    -> true

try map null key/value for class java.util.TreeMap
put("null", null)
    -> OK
put(null, null)
    -> NPE
get(null)=
    -> NPE
get("null")=
    -> null
containsKey(null)=
    -> NPE
containsKey("null")=
    -> true

try map null key/value for class java.util.IdentityHashMap
put("null", null)
    -> OK
put(null, null)
    -> OK
get(null)=
    -> null
get("null")=
    -> null
containsKey(null)=
    -> true
containsKey("null")=
    -> true

try map null key/value for class java.util.Hashtable
put("null", null)
    -> NPE
put(null, null)
    -> NPE
get(null)=
    -> NPE
get("null")=
    -> null
containsKey(null)=
    -> NPE
containsKey("null")=
    -> false

try map null key/value for class java.util.concurrent.ConcurrentHashMap
put("null", null)
    -> NPE
put(null, null)
    -> NPE
get(null)=
    -> NPE
get("null")=
    -> null
containsKey(null)=
    -> NPE
containsKey("null")=
    -> false

try map null key/value for class java.util.concurrent.ConcurrentSkipListMap
put("null", null)
    -> NPE
put(null, null)
    -> NPE
get(null)=
    -> NPE
get("null")=
    -> null
containsKey(null)=
    -> NPE
containsKey("null")=
    -> false
```

