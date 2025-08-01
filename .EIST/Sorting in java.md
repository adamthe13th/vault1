>[!success] **Setup**
>Object class must `implements Comparable<ObjectType> {...}`

**Then** `compareTo()` function needs to be implemented
### example
```java
public int compareTo(Person other) {
        return Integer.compare(this.age, other.age);
    }
```

### Manual
- **>0** this > other
- **<0** this < other
- **== 0** this equals other

> either integrate this logic in the `compareTo()` function
> or create a manual comparator

```java
new Comparator<Person>() {
            @Override
            public int compare(Person p1, Person p2) {
                if (p1.age < p2.age) return -1;
                else if (p1.age > p2.age) return 1;
                else return 0;
            }
``` 
