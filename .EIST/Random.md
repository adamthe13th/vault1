>[!bug] **DO NOT FORGET NULL CHECKS & OPTIONAL PRESENT CHECKS**

# Binary Search Tree
#bst 
```java
public Tree searchTree(int index){
	if(this.getIndex() == index){return this;}
	//not this search forward
	//left
	if(index < this.getIndex()){
		if(this.leftChild==null){
			return null;
		} else {
			return this.leftChild.searchTree(index);
		}
	}
	//right
	if(index > this.getIndex()){
		if(this.rightchild==null){
			return null;
		} else {
			return this.rightChild.searchTree(index);	
		}
	}
}
``` 

