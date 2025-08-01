#UML

|Relationship|Symbol →|Java Meaning|Example|
|---|---|---|---|
|Association|→ (solid)|Has-a (uses or holds reference)|`Person → Car`|
|Aggregation|◇─→ (hollow diamond)|Whole-part, non-owning|`Team ◇─→ Player`|
|Composition|◆─→ (filled diamond)|Whole-part, owning|`House ◆─→ Room`|
|Inheritance|▻─ (hollow triangle)|Extends|`Dog ▻─ Animal`|
|Implementation|▻─ with `<<interface>>`|Implements|`Dog ▻─ <<Pet>>`|
|Dependency|← - - -|Uses temporarily (e.g., parameter or return)|`Client ← - - - Logger`|

- **Owning** the object is created inside the owner (new) so destroying the owner destroys the owned
- **non-Owning** the part is created outside and passed as a reference to the Whole meaning deleting the Whole doesn't destroy the part
-