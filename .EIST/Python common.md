#python 
# Strings
- string.upper() / lower
- `char.isalpha() ` 
- `ord('Char')` 
- **Copying Data-structures** : *Deep copy* `new_Set = old_Set.copy()` 
- **Copying sets of mutable** : *need deeper copy (May need to import copy)* `new_Set = copy.deep_copy(old_set)` 
>[!info] **String**
>when dealing with a string you can access its individual chars like an *Array* 
>` myChar = myString[0] `
# Dictionary
 - dict = {} initializes as an empty dictionary
 - check contains key: `if element in nodes: `
 - Deletion: `del nodes[KEY]`
 - Iteration: 
	 ``` python
	 for key, value in nodes.items():
		 #DO SOMETHING
	``` 
>[!warning] **Dict key deletion**
> ` del nodes[key] ` raises an error if key not in dictionary
> ` value = nodes.pop(key, None) ` will not throw an error (*and the value doesn't necessarily need to be retrieved*)
# Set
- init: my_set = set() | my_set = {1, 2, 3}
- my_set.add(element)
- my_set.remove(element) -> raises error on non-existent
- my_set.discard(element) -> chill if no element found
- my_set.clear() empty out
- check contains: `if element in my_set: `
- Iterate: `for element in my_set: `
- a. (union| intersection | difference | symmetric_difference)(b)
>[!tip] **Mutable declaration**
> **Dictionary** ` my_dict = {} `
> **Set** ` my_set = set() ` 
> **List** ` my_list = [] ` 
# Files
#python_files #json 
## writing Json
opening a file example: (+ using Json)
 ```python
 with open(undo_log_file, "w") as file:
	for operation_list in undo_operation_list_list:
	# TODO Step 3: Write undo log to corresponding log file.
	file.write(json.dumps(operation_list) + "\n")
```
- **Access types**: 
	- "w" for write over (deletes previous file content)
	- "a" for append writes at EOF
	- "r " for read only
## Reading Json
#python_files  #json 
```python
with open(file_name, 'r') as file:
	for line in file:
	operations = json.loads(line)
	# this is processing UNRELATED
	for op in operations:
		apply_operation(op, store)
```

## files within directories
``` python
os.makedirs(os.path.dirname(log_file), exist_ok=True)
with open(log_file, 'a') as file:
	file.write(json.dumps(operation_list) + "\n")
``` 
the first line is for extracting the **Directories** from the given **Path** `os.path.dirname("path")` 
`os.makedirs(<PathToDirectory>, exist_ok=True)` makes the directories and the `exist_ok` defines whether or not it's OK if they already exist -> don't throw an error

## File creation guide
|Mode|Meaning|File created if not exists?|Raises Error if not exists?|
|---|---|---|---|
|`'r'`|Read|❌ No|✅ Yes, `FileNotFoundError`|
|`'w'`|Write (overwrite)|✅ Yes|❌ No|
|`'a'`|Append (write at end)|✅ Yes|❌ No|
|`'x'`|Create, fail if exists|✅ Yes|❌ No (unless it exists)|
|`'r+'`|Read & write|❌ No|✅ Yes, `FileNotFoundError`|
|`'w+'`|Write & read (overwrite)|✅ Yes|❌ No|
|`'a+'`|Append & read|✅ Yes|❌ No|
|`'x+'`|Create & read, fail if exists|✅ Yes|❌ No (unless it exists)|