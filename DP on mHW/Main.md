>[!info] **Solutions Repo**
>[DPMH solutions](https://github.com/JigaoLuo/modern_dbs)

# Cache metrics
**Average memory access time** = Hit time + Miss rate * Miss penalty

>[!info]
>**Increasing cache size or associativity increases its latency**
>both cause more tags to search through, more logic, longer wiring
>**Also consumes more power for the larger array and take longer to activate**


# Matrix Blocking
#block
```Cpp
// travel through blocks
for(int i=0; i<totalSize; i+=sizeOfBlocks){ // result row
	for(int j=0; j<totalSize; j+=sizeOfBlocks){ // result column
		for(int k=0; k<totalSize; k+=sizeOfBlocks){ // source lines
			//travel inside blocks
			for(ic = i; ic< i + blockSize; ic ++){
				for(jc = j; jc< j + blockSize; jc ++){
					for(kc = k; kc< k + blockSize; kc++){
						C[ic][jc] += A[ic][kc] * B[kc][jc];
					}
				}
			}
		}
	}
}
```

# DBMS storage layouts
#store 
aside from the basic Row store (mostly useful in OLTP) there are multiple ways of handling Column store
## Column store
### Fixed length Offset
- Does not require explicit tuple matching: the *n-th* tuple has its attributes in the *n-th* position in the corresponding column *Array* 
- **This does require the values within the same array to have the same size** 
- -> **Thus the *fixed length offset***
### Embedded tuple ID
- The attribute is stored alongside a tuple ID to match -> comes with inherent slowdown when matching tuples
- **Can be *virtual* ass in they are not explicitly stored in memory rather inferred (e.g. based on position or some other calculation)**
### Hybrid approach
#### PAX: Partition Attributes Across
essentially if one page (normal size) is capable of holding 10 tuples with **n** elements each, instead having the tuples (in Row) one after the other. divide the page into n *mini-pages* and each holds an attribute in *column store*.
### Hybrid storage model
1. store new Data in NSM for fast OLTP
2. Migrate data to DSM for more efficient OLAP
>[!tip] 
>**NSM** for N-ary store model: row store
>**DSM** for decomposition storage model: Column store (it decomposes tuples into their column attributes)

>[!note] *Recent research apparently says DSM can be used efficiently for Hybrid workloads*

# Hardware conscious/ oblivious
## conscious
-  better performance *due to fine-tuning to the underlying architecture* (cache, TLB, NUMA etc.)
- Portability?
## oblivious
- efficiency can still be achieved due to modern hardware being able to hide performance losses **Inherent in the multi-layer memory hierarchy**  with *Hyperthreads/ SMT*
- easily portable
- more robust to data skew