# Data - Structure

## Sorting:

### MergeSort
- Merge sort is basically based on divide and concur.
- If there are N elements than Best, Average and Worst case complexity is Nlog(N).
- Why Nlog(N) ?
	- log(N) is for divide. This is because, there will be log(n) maximum devision before merging.
	- Ex: N=8, log(8)= 3
	- 8/2=4
	- 4/2=2
	- 2/2=1
	- as can see in above case, there are 3 total division we executed to satisfy the base condition of recursion which is divide until Left is not equal to the Right of array.
- Post Division or Left and Right partition merge logic will execute and that is O(n) for each lever Left and Right sorted array.
- Ex: If there are 2 sorted array or list and we are merging it will be O(n) complexity.
- As merging will execute for each level Left and Right so overall complexity will be Nlog(N).

- Base condition: Divide until leftIndex==rightIndex
- mid = (rightIndex-leftIndex)/2.
- callLeft(array, left, mid);
- callRight(array, mid+1, right);
- merge(array, left, mid+1, right);
	 