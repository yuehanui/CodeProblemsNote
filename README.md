# Code Problems Note

[TOC]

## Count: 22

## Array

### Two Sum

Problem:
>Given an array of integers, return indices of the two numbers such that they add up to a specific target.
>Each input have exactly one solution, and you may not use the same element twice


Example:
>Given nums = [2, 7, 11, 15], target = 9,
>     
>Because nums[0] + nums[1] = 2 + 7 = 9,       
>return [0, 1].

思路：      
>Iterate the array and use HashMap to store each element. Specifically, store each value in the array as the key and store its index as the value into the HashMap.
Since to put an item into the HashMap takes O(1) time, and to check if the HashMap contains a specific value also takes O(1) time. The whole process will take O(n) time.


Algorithm:
>Iterate through the array, for each element, check if its "complementary number" ( the number add up to the target) is already in the HashMap. If it is true, the result is found. Otherwise, put the  index of the element in the array using the value of the element as key.

Java Code:

```java
public int[] twoSum(int[] nums, int target) {
	Map<Integer, Integer> theMap = new HashMap<>();
	for( int i = 0; i < nums.length; i++) {
 		if (theMap.containsKey( target - nums[i] )) {
 			int[] result = {theMap.get(target-nums[i], i};
 			return result;
 		}
    theMap.put(nums[i],i);
 	}
 	return null;
}
```
Thoughts:

>Why use the index of the element as value, the value of the element as key when putting it into the HashMap, instead of the other way?
The key of this algorithm is:
>1. Use HashMap.containsKey() to check if it contains the complementary number.
>2. Use HashMap.get() to get the index of the complementary number
>
>If we store the data in the other way. The first step could be replaced using HashMap.containsValue(), which is fine. However, the second part would be a problem since there is no HashMap.getValue(), or at least there is no efficient way to implement HashMap.containsValue(). The efficiency of hashMap comes from the hush function which works on the key instead of the value.





### Longest Substring Without Repeating Characters.

Problem:
> Given a string, find the length of the longest substring without repeating characters

#### Solution 1:
思路：
> Initialize a HashSet to store the characters.
Use a sliding window[```i```, ```j```] to representing the substring between index ```i``` and index ```j```(inclusively).
Set ```i```, ```j``` and ```ans``` to 0
Iterate through the string and for each character in the string:
>> if the character is found in the HashSet:
>>> remove character at index ```i``` from the HashSet
>>> ```i``` = ```i``` + 1
>>
>> else:
>>>add the characters to the HashSet
>>> ```j``` = ```j``` + 1
>>>set ```ans``` to max(```ans```, ```j```-```i```);

>return ```ans```

Java Code:
```java
public int lengthOfLongestSubstring(String s) {
        Set<Character> chars = new HashSet<>();
        int i = 0, j = 0, ans = 0;
        int length = s.length();
        while (j < length){
            if (chars.contains(s.charAt(j))){
                chars.remove(s.charAt(i));
                i++;
            } else {
                chars.add(s.charAt(j));
                j ++;
                ans = Math.max(ans, j-i);
            }
        }
        return ans;
    }
```

Complexity Analysis:
> In the worst case, that is, all characters in the string are the same, both the "if" condition and "else" condition will run n times(n = length of string). So the whole while loop will run 2n times.
So the time complexity = O(2n) = O(n)
>
> In the worst case, that is, all characters in the string are unique, every characters in the string will be added to the HashSet. So the length of the string n will be a upper bound for space complexity.
If the length of the string is larger than the number of total unique characters of the specific charset, the space complexity will be upper bounded by the number of total unique characters m.
So the space complexity = O(min(n,m))

#### Solution 2
思路
> Optimized from solution 1. Instead of using a HashSet to store the characters in solution 1, use a HashMap to store the characters as key and their index as value. Then when iterating the string, if the characters is found in the HashMap, we can get its index where the character is found and move the ```i``` cursor to the character right after it. This will give a better time complexity than solution 1.

Java Code:
```java
        Map<Character, Integer> chars = new HashMap<>();
        int i = 0,j = 0, ans = 0;
        int length = s.length();
        while (j < length) {
            if (chars.containsKey(s.charAt(j))) {
                i = Math.max(i, chars.get(s.charAt(j))+1);

            }
            chars.put(s.charAt(j),j);
            j++;
            ans = Math.max(ans, j-i);
        }
        return ans;
    }
```

Complexity Analysis
> In the worst case, which is all characters in the string are the same, the while loop will run n times(n = length of string).
> So the time complexity = O(n)
> The space complexity is the same as solution 1, O(min(m,n));

### Find index in sorted array

Problem:

>Given a sorted array of integers, return the index of the given key. Return ```-1``` if not found.

思路：

> Use divide and Conquer strategy.
>
> Since the array is sorted, we can start by comparing the search key with the integer at the middle of the array. If the search key is equal to the middle key, we find it and we can return the index of the middle key. Else, if the search key is smaller than the middle key of the array. We search it using the same strategy in the left half of the array. Otherwise, we search it in the right half in the array.

Java Code:

```java
static int binSearch(int[] a, int key) {
    int left = 0, right = a.length-1;
    while (left <= right) {
      int mid = (left + right) / 2;
      if (a[mid] == key) return mid;
      else if (key < a[mid]) right = mid -1;
      else left = mid+1;
    }
    return -1;
  }
```

Complexity Analysis:

> This algorithm uses divide and conquer algorithm. The while loop will runs ```logN``` times. So the time complexity is O(logN)
>
> The space complexity is O(1) since fixed amount of space, specifically, ```left```,```right``` and```mid```, is used.





### Find Maximum in Sliding Window

Problem:

> Given a large array of integers and a window of size w, find the current maximum value in the window as the window slides through the array.

Example:

![](img/image1.png)

思路：

> Use a deque data structure to approach the problem. A Deque is a double head queue, which you could add and remove item from its both end in O(1) time.
>
> The following algorithm will maintain a deque that always has the index of the largest number in the current sliding window at it head.
>
> Iterate through the array, for each iteration,
>
> 1. first check if the last item stored in the deque is smaller than the current number. Keep removing items from the end of the deque until its last item is larger than the current number.
>
> 2. Then check if the item at the head of the deque are still in the sliding windows. Remove the item at the head if it is no longer in the sliding windows.
>
> > (Notice that for each number, there are two possible way to get out of the deque. If the number is the largest number of the previous sliding window, and it is larger than the current number, and it is also at the leftest position of the sliding windows, then it will be removed from the head of the deque since it will no longer be in the sliding window at this iteration. Otherwise, it will be removed from the end of the deque when a number larger than it enters)
>
> 3. Then add the current number to the end of the deque.

Java Code:

``` java
public static ArrayDeque<Integer> findMaxSlidingWindow(int[] arr, int windowSize) {
    ArrayDeque<Integer> result = new ArrayDeque<>();
    ArrayDeque<Integer> window = new ArrayDeque<>();

    if (arr.length ==0 || arr.length<windowSize) return result;

    for (int i = 0; i < windowSize; i++){
      while (!window.isEmpty() && arr[i] > arr[window.peekLast()]){
        window.removeLast();
      }
      window.addLast(i);
    }
    result.add(arr[window.peekFirst()]);
    for (int i = windowSize; i < arr.length; i++){
      while (!window.isEmpty() && arr[i] > arr[window.peekLast()]) {
        window.removeLast();
      }
      if (!window.isEmpty() && window.peekFirst() <= i-windowSize){
        window.removeFirst();
      }
      window.addLast(i);
      result.add(arr[window.peekFirst()]);
    }
    return result;
  }
```

> Complexity Analysis
>

> The array is iterated once. Each item in the array entered the deque once. So the time complexity is O(n)
>
> The item in the deque will not exceed the size of the sliding windows ```w```, so the memory complexity is O(m)



### Search Rotated Array

Problem:

> Search a given number in a sorted array that has been rotated by some arbitrary number.

Example:

![image2](./img/image2.png)

思路：

> Solve it  using a  binary search algorithm with some modification. At least one half of the array is always sorted. If the number n lies within the sorted half of the array, then the problem is basically a binary search problem. Otherwise, split the unsored half of the array evenly and see if the number lies in the sorted half. Keep checking until it lies in the sorted half then do a traditional binary search.

Java Code:

```java
static int binarySearchRotated(int[] arr,int key) {
    int start = 0, end = arr.length-1;
    int sortedSide; //indicate which half is sorted. 0 for left, 1 for right
    while(true){
      int mid = (start + end ) / 2;
      // see which side is sorted
      if (arr[mid] < arr[end]) sortedSide = 1;
      else sortedSide = 0;

      if (sortedSide == 0) { // left side is sorted
        if (key >= arr[start] && key <= arr[mid]){ //lies in the sorted half
          end = mid;	
          break;
        } else { // lies in the unsosrted half
          start = mid +1;
        }
      } else { // right side is sorted
        if (key >= arr[mid] && key <= arr[end]){//lies in the sorted half
          start = mid;
          break;
        } else{ // lies in the unsosrted half
          end = mid -1;
        }
      }
    }

    while (start <= end){  //regular binary search
      int mid = (start + end) /2;
      if (key == arr[mid]){
        return mid;
      } else if (key > arr[mid]){
        start = mid + 1;
      } else {
        end = mid -1;
      }
    }
    return -1;
  }
```

Complexity Analysis:

> The algorithm is based on binary search which cut the problem in half each iteration. So the time complexity is O(logN)
>
> The algorithm uses fixed extra space, so the space complexity is O(1)



### 1-bit & 2-bit character

Problem:

> We have two special characters. The first character can be represented by one bit ```0```, the second character can be represented by two bits ( ```10``` or ```11```).
>
> Now given a string represented by several bits. Return whether the last character must be a 1-bit character or not. The given string  will always ends with a ```0```.

Example:

> Input: [1, 0, 0]        Output: True
>
> Input: [1, 1, 1, 0]    Output: False

#### Solution 1:

思路:

> Iterate through the string. For each number ,
>
> >  if it is```1```, the character is represents has 2 bits, which is the ```1``` itself and the number follows it. So we can increase the pointer by 2.
> >
> > Othwise, the character is represented by 1 bits,which is the ```0``` itself. So we incease the pointer by 1.
>
> If the pointer reaches the index of the last bit, then the last bit is by itself, so the answer is True. Otherwise, the answer is False since the last bit is the second bit of a character.

Java Code:

```java
public boolean isOneBitCharacter(int[] bits) {
  int p = 0;
  while (p< bits.length-1){
    p += (1 + bits[p]);
  }
  return p == bits.length-1;
}
```

Complexity Analysis:

> The string is iterated once so the time complexity is O(n) which n is the length of bits.
>
> The space complexity is O(1), the space used by ```p```, the pointer

#### Solution 2:

思路:

> A number 0 is always an end of a character. Find the second-last ```0``` in the string, then count the ```1```s  bewteen it and the last bit (which is also a ```0```). If there are even number of  ```1```s, then the last bit is by it self and the answer True. Otherwise, the last bit is the second bit of a two bit character, and the answer is False.

Java Code:

```java
public boolean isOneBitCharacter(int[] bits) {
  int p = bits.length-2;
  while (p >= 0 && bits[p]>0) p --;
  return (bits.length - p) % 2 == 0;
}
```

Complexity Analysis:

> The time complexity is O(n).
>
> The space complexity is O(1). (The space of ```p```, the pointer.)



### Find the Smallest Common Number

Problem:

> Given three positive integer arrays which  are sorted in ascendening order.
>
> Find the Smallest number that is  common in all three arrays. Return -1 if the smallest common number is not found.

Example:

> ![image3](./img/image3.png)

思路：

> Use three pointers. Each pointer points at the first digit of each array. If the numbers three pointers pointing at are equal, return the number. Otherwise, compare three numbers and move the pointer that is pointing at the smallest number among three to the right by one step.

Java Code:

```java
static Integer findLeastCommonNumber(int[] arr1, int[] arr2, int[] arr3) {
    //TODO: Write - Your - Code
    int p1 = 0, p2 = 0, p3 = 0;
    while (p1 < arr1.length && p2 < arr2.length && p3 < arr3.length){
      if (arr1[p1] == arr2[p2] && arr1[p1] == arr3[p3]){
        return arr1[p1];
      }
      if (arr1[p1] < arr2[p2]){
        if (arr1[p1] < arr3[p3]) p1 ++;
        else p3 ++;
      } else p2 ++;
    }
    return -1;
  }
```

Complexity Analysis:

> The time complexity is O(n) since each array are iterated once.
>
> The space complexity is O(1) since the otter space used (three pointers) is fixed.



### Rotate an Array by N element

Problem:

> Given a array of integers, rotate the array by  N elements where N is an integer.
>
> For positive N, perform right rotate
>
> For negative N, perform left rotate

Example:

![image4](./img/image4.png)

思路:

> If ```N``` is larger than the size of the array, it equals to ```N``` mod ```size of the array```
>
> For negative N which should perform left rotation, the action can be replace by performing right rotation by ```N``` mod ```size of the array``` element;
>
> However, the ```%``` function of Java doesn't perform in common sense for negative number. For example ```-2``` % ```10``` would result in ```-2``` and ```-14``` % ```10``` would result in ```-4```. So to make it perform correctly for negative number. We can add the ```size of the array``` to the result for negative ```N```.
>
> Once we get the simplified ```N```, we rotate the entire array. Then rotate the array from index ```0``` to index ```n-```. Lastly, rotate the array from index ```n``` to the end of the array. 

Java Code:

```java
static void rotateArray(List<Integer> arr, int n) {
		int rotate = n % arr.size();
  	if (rotate < 0) rotate += arr.size();
  	reverseArray(arr,0,arr.size()-1);
    reverseArray(arr, 0, rotate-1);
    reverseArray(arr,rotate, arr.size()-1);
  }
static void reverseArray(List <Integer> arr, int left, int right){
	while (left < right){
		int temp = arr.get(left);
    arr.set(left,arr.get(right));
    arr.set(right,temp);
    left ++;
    right --;
	}
}
```

	### Find low / high Index of  a Key in a Sorted Array

Problem:

> Given a sorted array, find the lowest and the highest index of the given key. Return -1 if the key is not found in the array.

思路：

> Use binary search to approach the problem. For lowest index, modifiy the binary search so :
>
> While ```left index``` <= ```right index```:
>
> > > if the middle item < ```key```,
> >
> > > > ```left index``` = ```middle index``` + 1
> >
> > > else (which is middle item >= ```key``` ),
> >
> > > > ``` right index``` = ```middle index``` -1;
>
> > if it smaller than ```size of the array``` and ```arr[left]``` equals to ```key```
>
> > > return ```left index```
>
> > Else,
>
> > > return -1
>
> Notice than unlike the original binary search, this modified binary search always runs to the end (```left index``` > ```right index```). 
>
> And it always run through a state that ```left index``` equals to```right index```.
>
> In this specific state,  if the current item is equal to or larger than the ```key```,  ```right index``` will be moved which doesn't effect the result since ```left index`` is the one to be returned. 
>
> And if the current item is smaller than ```key```, which means the possible solution is next to the current item at the right side, ```left index``` will increase by one so it point to the possible answer. 
>
> In both case, ```left index``` will end at the possible solution and ```right index``` will end at the left side next to the possible answer.
>
> It is only a possble answer since
>
> 1. Maybe the key isn't in the array at all, then ```left index``` will end up at the smallest number that is larger than ```key```.
> 2. In a specific case of the previous situation, all numbers in the array is smaller than ```key```, ```left index``` will end up at right side next to the end of the array, which result in ```indexOutOfBound Error```, so we should also check for this special case.

Java Code:

```java
static int findLowIndex(List<Integer> arr, int key) {
    if (arr.size() == 0){
      return -1;
    }
    int left =0, right = arr.size()-1;
    int mid;
    
    while (left <= right) {
      mid = (left + right) /2;
      if (arr.get(mid) < key) left = mid +1;
      else right = mid -1;
    }
    if (left < arr.size() && arr.get(left) == key) return left;
    else return -1;
  }
  
  static int findHighIndex(List<Integer> arr, int key) {
    if (arr.size() == 0){
      return -1;
    }
    int left =0, right = arr.size()-1;
    int mid;
    
    while (left < right) {
      mid = (left + right) /2;
      if (arr.get(mid) > key) right = mid -1;
      else left = mid +1;
    }
    if (right >= 0 && arr.get(right) == key) return right;
    else return -1;
  }
```

Complexity Analysis:

> The time complexity is O(logn) and the space Complexity is O(1)

### Move All Zeros to the Beginning of the Array

Problem:

> Given an array, move all zero in it to the beginning of the array while maintaining the order of other numbers.

思路:

> Itertate through the array from the end to the beginning. Use a counter to store the number of zeros encounters. For each number that is ```0```, increase ```zeros counter``` by 1. Otherwise, copy the number to the slot that is ```zeros conter``` steps away from it on the right side. Then set the first ````zeros conter``` postions as ```0```

Java Code:

```java
static void moveZerosToLeft(int[] A) {
  int zeros = 0;
  for(int i = A.length-1; i >= 0; i--){
    if (A[i] == 0 ) zeros ++;
    else A[i+zeros] = A[i];
  } 
  for(int i = 0; i < zeros; i++){
    A[i] = 0;
  }
}
```

Complexity Analysis:

> The time complexity is O(n) 
>
> The space complexity is O(1)



### Stock Buy Sell to Maximize Profit

Problem:

> Given a list of daily stock price, return the buy and sell price to maximize profit. The stock must be bought before sold and it cannot be bought and sold in the same day.

Example:

> Yellow marks buying and green marks selling

![iamge5](./img/iamge5.png)

思路:

> Use Kadane's algorithm, which is a dynamic programming mindset.
>
> General idea: the``` miaximum profit of selling at day n``` is either
>
> 1. (```the maximum profit of selling at day n-1``` -  ```price of day n-1``` + ```price of day n```) 
>
>    or
>
> 2. (```the profit of buying at day n-1 and selling at day n```)
>
> So we can start by initialize the buying at ```day 0``` and selling at ```day 1``` , which are the first possible day to buy and sell. then we iterate through the array, for each day, calculate the ```maximum profit of selling at that day``` using the mindset described above. Then if the profit is greater than the ```global maximum profit```, update the ```global maximum profit```.
>
> One thing to notice is that this specific problem requires us to return the buying price and selling price instead of the maximum profit. So we will use two variable to store thoes values.

Java Code:

```java
public static Tuple findBuySellStockPrices(int[] array) {
    if (array.length == 0){
      return null;
    }
    int globalMaxProfit = Integer.MIN_VALUE;
    int localMaxProfit = array[1] - array[0];
    int buy = array[0], sell = array[1];
    for(int i=2; i < array.length; i++){
      int a = localMaxProfit + array[i] - array[i-1] ;
      int b = array[i] - array[i-1];
      localMaxProfit = Math.max(a,b);

      if (localMaxProfit > globalMaxProfit){
        globalMaxProfit = localMaxProfit;
        sell = array[i];
        if (b > a) {
          buy = array[i-1];
        }
      }
    }
    Tuple<Integer, Integer> result = new Tuple<Integer, 			
  																				Integer>(buy,sell);
    return result;
  }
```

Complexity Analysis:

> The time complexity is O(n) since we use a single for loop to iterate throuth the array.
>
> The spcae complexity is O(1) since the outter space used is fixed.



### Merge an Array with Overlapping Intervals

Problem:

> Given a list of iterval pairs which each interval has a start timestamp and a end timestamp. The input list is sorted by starting timestamp. Return a new list with overlapping ivervals merged.

Example:

![image6](./img/image6.png)

思路:

> Keep two variable ```start ``` and ```end``` to indicate the start timestamp and end stimestamp of a interval. Start by initalize ```start``` to the start timestamp of the first interval and ```end``` to the end timestamp of the first interval. 
>
> Iterate through the array, for each item, 
>
> > if it is overlapping with the interval that ```start``` and ```end```  indicates currently, update ```end``` to merge it in.
> >
> > Else, add the interval (```start```, ```end```) to the ouput list then update ```start``` and ```end``` to the start timestamp and end timestamp of the current interval.
>
> After the iteration is done, add the interval (```start```, ```end```) to the output list again.

Java Code

```java
static ArrayList<Pair> mergeIntervals(ArrayList<Pair> v) {
	if(v == null || v.size() == 0) {
      return null;
    }
  ArrayList<Pair> result = new ArrayList<Pair>();
  int start = v.get(0).first, end = v.get(0).second;

  for (int i = 1; i < v.size();i++){
    if (v.get(i).first <= end){
      end = Math.max(end,v.get(i).second);
    } else {
      result.add(new Pair(start,end));
      start = v.get(i).first;
      end = v.get(i).second;
    }
  }  
  result.add(new Pair(start,end));
  return result;
  }
```

Complexity Analysis:

> The time complexity is O(n) since we use a single for loop to iterate through the list.
>
> The space complexity is O(n) since in worst case, every item in the input list will be add into the output list.



### Sort an Array using QuickSort Algorithm

Problem:

> Given an array, sort it using QuickSort Algorhtim

思路：

> Review of two partition schemes of Quicksort
>
> 1. Lomuto's partition
>
> > ```pseudocode
> > Partition(A, lo, hi)
> > 	pivot = A[hi]
> > 	i = lo
> > 	for (j = 0; j< hi-1; j++)
> > 		if (A[j] < pivot)
> > 			swap A[i] and A[j]
> > 			i++
> > 	swap A[hi] and A[i]
> > 	return i
> > 		
> > ```
> >
> > **`i`的移动逻辑**
> >
> > > `i` 从向右移动，遇到小于`pivot`的数则继续移动，遇到大于`pivot`的数则会保持指向该数，并等待`j`找到比`pivot`小的数来跟他交换，交换后`i`向右移动一格。
> >
> > 
> >
> > **`j`的移动逻辑**
> >
> > > `j`从左向右移动，遇到比`pivot`大的数则跳过，遇到比`pivot`小的数则把该数与`i`指向的数交换。
> >
> > 
> >
> > **算法中只判断`j`指向的数比`pivot`大，并没有判断`i`指向的数字是否比`pivot`小，什么操作？**
> >
> > > 分两个阶段，初始化阶段和正式阶段
> > >
> > > 在遇到第一个大于`pivot`的数之前，`i`和`j`会保持指向同一个数并会进行交换（并不会带来实质改变）。遇到第一个大于`pivot`的数后`i`停下，而`j`继续前进，此时他们才分开。
> > >
> > > 当`i`找到第一个比`pivot`大的数后，它就不再会指向比`pivot`小的数字。这是因为他在和`j`执行交换后指向的下一个数一定是大于`pivot`的（如果该数比`pivot`小，则`j`会在经过它的时候把它和`i`指向的数进行交换。交换后`i`也会移动，等同于`i`带着它指向的大于`pivot`的数进行移动。所以只有当`i`指向的数后面至少有一个大于`pivot`的数时，`i`才能在移动后指向另一个数。）
> > >
> > >  所以可以这么理解，`i`从左到右开始找比`pivot`小的数，找到后就盯着它。`j`则从左到右寻找比`pivot`小的数，找到就把它就与`i`指向的数进行交换。交换发生时，`j`一定是送出去一个比`pivot`小的数，但`i`并不一定指向一个比`pivot`大的数。但当`i`指向的数不比`pivot`大时，`i`和`j`必定是指向同一个数字，所以此时的交换是不会造成结果的。
> >
> > 
> >
> > **如何知道loop结束时`i`就是停留在数列的大小分界点上？**
> >
> > >  在loop完成时，`j`遍历完array，`i`则指向从左到右第一个比`pivot`大的数字。这是因为`i`见到比`pivot`小的数字会直接路过，而见到比`pivot`大的数会盯着等到换到一个比`pivot`小的数后才继续前进，所以`i`在身后留下的永远是比`pivot`小的数。
> >
> > 
>
> 1. Hoare's partition
>
> > ```pseudocode
> > Partition(A, lo, hi)
> > 	pivot = A[lo]
> > 	i = lo, j = hi
> > 	while (true)
> > 		while A[i] < pivot
> > 			i ++
> > 		while A[j] > pivot
> > 			j --
> > 		if i >= j 
> > 			return j
> > 		else
> > 			swap A[i] and A[j]
> > ```
>
> Hoare's partition is generally more efficient since it does less comparisons.
>
> Both partitions is get O(n^2) time complexity in worst case (already sorted array). This can be avoided by using random pivot or by randomly permute the array before sorting.
>
> The general idea of quicksort is to divide & conquer. 

Java Code:

```java
static void quickSort(int[] arr) {
  if (arr.length != 0) quickSortHelper(arr,0,arr.length-1);
  return;
}
static void quickSortHelper(int[] arr, int lo, int hi){
  if (lo < hi){
    int split = partition(arr,lo,hi);
    quickSortHelper(arr,lo,split);
    quickSortHelper(arr, split+1, hi);
  }
  return;
}

static int partition(int[] arr, int lo, int hi){
  
  int pivot = arr[lo];   //this line will be different if use randomize pivot to avoit O(n^2) worst case time complexity  
  int i = lo, j = hi;
  while (true){
    while (arr[i] < pivot){
      i ++;
    }
    while (arr[j] > pivot){
      j --;
    }
    if (i >= j) return j;

    int temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
  }
}
```



Complexity Analysis:

> The average time complexity is O(n logn), the worst case time complexity is O(n^2), which is rare and can be avoid by using the methods mentioned above.
>
> The space complexity is O(logn) since it recursively called function and the complexity decrease in logarithmic order

## Linked List

### Add Two Numbers

Problem:

> You are Given two non-empty linked lists representing two non-negative integer. The digits are stored in revere order and each node contains a single digit. Add the two numbers and return it as a linked list.

Example:

>Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
> Output: 7 -> 0 -> 8
> Explanation: 342 + 465 = 807.

​	思路:

> Simply sum two numbers digit by digit starting from the least-significant digit, which is the heads of the linked lists. For each addition on two digits, if the summation exceed 10, for example the summation is 12,1 will be carried to the more significant digit and the current digit will be set to 2. Just as you do the kindergarten math.

Java Code:

```java
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
	ListNode head = new ListNode(0);
	ListNode list1 = l1, list2 = l2, currNode = head;
	int carry = 0;

	while (list1 != null || list2 != null) {
		int x = (list1 != null) ? list1.val : 0;
		int y = (list2 != null) ? list2.val : 0;
		int sum = x + y + carry;
		carry = sum / 10;
		int digit = sum % 10;
		currNode.next = new ListNode(digit);
		currNode = currNode.next;
		if (list1 != null) list1 = list1.next;
		if (list2 != null) list2 = list2.next;
	}
	if (carry > 0) {
		currNode.next = new ListNode(carry);
	}
	return head.next;
}
```

Complexity Analysis:

> The time complexity is O(n) sice we use a single while loop to approach the problem.
>
> The space complexity is is O(n).

### Reverse a Singly Linked List

Problem:

> Given a singly linked list, return the pointer to the head of the reversed linked list.

思路: 

> Iterate through the linked list. For each node, change the its ```next``` arribute to point to the node that originally before it.
>
> One thing to notice is that for a node in singly liked list, the ```next``` arribute is the only reference to its next node. If the ```next``` arribute is modified without storing it somewhere else, the reference to the next node will be perminently lost. So we need to store the reference to the next node in a variable before modify the ```next``` attribute.

Java Code:

```java
public static Node reverseLinkedList(Node head){
  if (head == null || head.next == null) return head;
  
  Node ListToDo = head.next;
  Node ReversedList = head;
  ReversedList.next = null;
  
  while (ListToDo != null){
    Node temp = ListToDo;
    ListToDo = ListToDo.next;
    temp.next = ReversedList;
    ReversedList = temp;
  }
  return ReversedList;
}
```

Complexity Analysis:

> The time complexity is O(n) since we use a single whle loop to iterate through the linked list.
>
> The space complexity is O(1) since fixed number of variables are used to solve the problem.

### Delete All Occurrences of a Given Key in a Linked List

Problem: 

>  Given a linked list and a key, delete all the nodes with the given key in the linked list.

Example:

![image7](./img/image7.png)

思路：

> The general idea is if a node is the key, connect its previous node to its next node, which will remove this node from the linked list.
>
> One thing to notice is that the head node doesn't has a 'previous node'. So if the head node is the key, we will set its next node as head instead.

Java Code:

```java
public static LinkedListNode deleteNode(LinkedListNode head, int key) 
{
  while (head.data == key){
    head = head.next;
  }
  LinkedListNode pointer = head;
  while(pointer.next != null){
    if (pointer.next.data == key){
      pointer.next = pointer.next.next;
    } else {
      pointer = pointer.next;
    }
  }
  return head;
}
```

Complexify Analysis:

> The time complexity is O(n)
>
> The space complexity is O(1)



### Sort Linked List Using Insertion Sort

Problem:

> Given a linked list, return a sorted linked list using insertion sort

思路:

> Maintain two linked list. One is the original list and the other one is a output list, an initially empty list.. Iterate through the originally list, for each node, compare it with the nodes of the output list and insert it at the position that  its previous node is smaller than it and the next node is larger than it. 

Java Code:

```java
public static LinkedListNode insertionSort(LinkedListNode head) {
 	LinkedListNode newHead = new LinkedListNode(Integer.MIN_VALUE);
  LinkedListNode pointer = head; 
  while (pointer != null){
    LinkedListNode newPointer = newHead;
    while (newPointer.next != null && newPointer.next.data < pointer.data){
    newPointer = newPointer.next;
	}
    LinkedListNode temp= newPointer.next;
    newPointer.next = new LinkedListNode(pointer.data);
    newPointer.next.next = temp;
    
    pointer = pointer.next;
  }
  return newHead.next;
}
```

Complexity Analysis:

> The time complexity is O(n^2), which is the typical time complexity of insertion sort algorithm. 
>
> The space complexity is O(1).



### Intersection point of Two Lists

Problem:

> Given two linked list that may or may not intersect, return the point of intersection if they intersect. Or return null if they don't.

Example:

![image8](./img/image8.png)

思路:

> If two linked list intersect at some point, they have the same remaining length at that point. The essential reason is that they in fact point to the same memory address when they intersect. 
>
> So for two linked lists with length ```m``` and `n`, respectively, the maximum possible length of  the intersection part is MAX(`m`, `n`). Assume `m` > `n`, then the first possbile intersection point for n is its first node. And the first possbile intersection point for m is its (`m`-`n`+1)th node. 
>
> So we can use two pinter. One points to the (`m`-`n`+1)th node of the longer list. The other one points to the first node of the shorter list. Then we start to move two pointers simultaneously while comparing them. Once two pointer are equal, we can be sure that the node they point to and its following nodes  are identical.

Java Code:

```java
public static LinkedListNode intersect(LinkedListNode head1, LinkedListNode head2) {
  int length1 = getLLLength(head1);
  int length2 = getLLLength(head2);
  LinkedListNode pointerLong, pointerShort;
  
  if (length1 > length2){
    pointerLong = head1;
    pointerShort = head2;
  } else {
    pointerLong = head2;
    pointerShort = head1;
  }
  for (int i = 0; i < Math.abs(length1-length2); i++){
    pointerLong = pointerLong.next;
  }
  while ( pointerLong != null && pointerLong != pointerShort) {
    pointerLong = pointerLong.next;
    pointerShort = pointerShort.next;
  }
  return pointerLong;
} 
public static int getLLLength(LinkedListNode head){
  int length = 0;
  while (head != null){
    length += 1;
    head = head.next;
  }
  return length;
}
```

Complexity Analysis:

> The time complexity is O(m+n), linear time complexity
>
> The space complexity is O(1)



### Find n'th Node from the End of a Linked List

Problem:

> Given a linked list, return the n'th node from the end. Return null if  n is out of bound

思路:

> Use to pointers that are n nodes away. Move two pointers simultaneously. When one pointer reach the end, the other pointer will be at the n'th node from the end.

Java Code:

```java
public static LinkedListNode findNthFromLast(LinkedListNode head, int n) {
  if (head == null || n < 1) return null;
  LinkedListNode tail = head;
  while(n>0){
    tail = tail.next;
    if (tail == null) return null; //n is out-of bound
    n--;
  }
  while(tail != null){
    tail = tail.next;
    head = head.next;
  }
  return head;
  }
```

Complexity Analysis:

> The time complexity is O(n)
>
> The space complexity is O(1)



### Swap N'th Node with Head

Problem:

> Given a head of a singly linked list and an integer N. Swap the N'th node of the linked list with the Head;

 思路：

> First thing to notice is that simply swap the value in the node isn't necessary since the data type sotred in the linked list isn't always interger. Simply swaping the value will involve different implementation according to its data type. So what we should do is swaping the nodes.
>
> The basic idea is to change the N'th node's next field so it points to the 2nd node. Then change the N-1'th node's next field so it points to the original head. And also change the original head's next field so it points to the N'th node's original next node. 

Java Code:

```java
static LinkedListNode swapNthNode(LinkedListNode head, int n) {
  if (head == null || n < 2) return head;

  LinkedListNode pointer = head;
  LinkedListNode temp = head.next;
  for (int i = 0; i < n-2; i++){
    pointer = pointer.next;
    if (pointer== null) return null;
  }
  LinkedListNode newHead = pointer.next;
  pointer.next = head;
  head.next = newHead.next;
  newHead.next = temp;
  return newHead;
}
```

Complexity Analysis:

> The time complexity is O(n).
>
> The space complexity is O(1).



### Merge Two Sorted Linked List

Problem:

> Given two sorted linked list, merge them so that the resulting linked list is also sorted.

思路:

> Use to pointer, one for each linked list. The pointers initially point to the first nodes of the lists. Compare two number and add the node with smaller number to the return list, then move the pointer of it to its next node. When one of  the pointer is null (which indicate one of the list is all in the return list now), linked the node of another pointer to the return list.

Java Code:

```java
public static LinkedListNode mergeSorted(LinkedListNode head1, LinkedListNode head2) {    
  if (head1 == null) return head2;
  if (head2 == null) return head1;

  LinkedListNode newHead;
  if (head1.data > head2.data){
    newHead = head2;
    head2 = head2.next;
  }
  else {
    newHead = head1;
    head1 = head1.next;
  }

  LinkedListNode pointer = newHead;
  while(head1 != null && head2 != null){
    if (head2.data > head1.data){
      pointer.next = head1;
      head1 = head1.next;
    } else {
      pointer.next = head2;
      head2 = head2.next;
    }
    pointer = pointer.next;
  }
  if (head1 != null) pointer.next = head1;
  if (head2 != null) pointer.next = head2;

  return newHead;
}
```

Complexity Analysis:

> The time complexity is O(m+n)
>
> The space complexity is O(m+n)



### Sort a Linked List Using Merge Sort

Problem:

> Given a Linked List of integer, sort it in ascending order using merge sort, and return the new head of the linked list.

思路:

> The tricky part is spliting the linked list into two halves. For an array, we can simply use index to split it. But for a linked list, we need to find another way to split it. 
>
> Use the fast / slow pointers approach. Let the fast pointer moves two step in each iteration and the slow pointer moves one step. So when the fast pointer reach the end of the linked list, the slow pointer will be at the middle of the linked list.
>
> After spliting the list into two part, we recursively called the mergsort function so the problem is trasformed into small subproblem. The smallest subproblem will be a list with one or no node, which is already sorted. Then it work from the buttom up, the problem at each level is to merge two sorted linkedlist. We can use two pointer, one for each linked list, and keep adding the smaller node to the new node while moving the pointer.

Java Code:

```java

 public static LinkedListNode mergeSort(LinkedListNode head) {
  if (head == null || head.next == null) return head;
  Pair<LinkedListNode,LinkedListNode> firstSecond= new Pair<>(null,null);

  split(head,firstSecond);

  firstSecond.first =mergeSort(firstSecond.first);
  firstSecond.second =mergeSort(firstSecond.second);

  return mergeSortedLists(firstSecond.first,firstSecond.second);
}
public static void split(LinkedListNode head, Pair<LinkedListNode,LinkedListNode> firstSecond){
  if (head == null) return;
  if (head.next == null){
    firstSecond.first = head;
    firstSecond.second = null;
    return;
  }
  LinkedListNode slow = head, fast = head.next;
  while (fast != null){
    fast = fast.next;
    if (fast!= null){
      fast = fast.next;
      slow = slow.next;
    }
  }
  firstSecond.first = head;
  firstSecond.second = slow.next;
  slow.next = null;
}

public static LinkedListNode mergeSortedLists(LinkedListNode first,LinkedListNode second){
  if (first == null) return second;
  if (second == null) return first;

  LinkedListNode newHead;
  if (first.data <= second.data){
    newHead = first;
    first = first.next;
  } else {
    newHead = second;
    second = second.next;
  }
  LinkedListNode pointer = newHead;
  while (first != null && second != null){
    if (first.data <= second.data){
      pointer.next = first;
      first = first.next;
    } else {
      pointer.next = second;
      second = second.next;
    }
    pointer = pointer.next;
  }

  if (first == null) {
    pointer.next = second;
  } else if (second == null){
    pointer.next = first;
  }
  return newHead;
}
```

Complexity Analysis:

> The time complexity is O(logN)
>
> The space complexity is O(logN)



### Reverse Even Nodes in a Linked List

Problem:

> Given a linked list, reverese the nodes at even indeces.

![image9](./img/image9.png)

Thought:

> Extract the nodes with even indices into a new list. While extracting  even nodes, push from the head of the new list. Then merge two lists alternatively.

Java Code:

```java
public static LinkedListNode reverseEvenNodes(LinkedListNode head) {
    if (head == null || head.next == null) return head;
    
    // extract the nodes at even indices into a new list.
    LinkedListNode even = null, pointer = head;
    while (pointer != null && pointer.next != null){
      LinkedListNode temp = even;
      even = pointer.next;

      if(pointer.next.next != null){
        pointer.next = pointer.next.next;
      } else{ // reach end of list
        pointer.next = null;  
      }
      pointer = pointer.next;
      even.next = temp;
    }
      // merge two lists
      pointer = head;
      while(pointer != null && even != null){
        LinkedListNode temp = pointer.next;
        pointer.next = even;
        even = even.next;
        pointer.next.next = temp;
        pointer = pointer.next.next;
      }
      return head;
    }
```

Complexity:

> Time complexity: O(n)
>
> Space complexity O(1)



### Rotate a Linked List by N nodes

Problem

> Given the head of a linked list and an integer 'n', rotate the linked list by 'n'.

###  ![image10](./img/image10.png)

Thought:

> If we use the trivial method, rotating the list n times, the time complexity will be O(mn), which m is the length of the list.
>
> A more efficient way is to perform a ratiation at the (m-n +1)th node. For example, for n = 2, perform the rotation at the ( 5 - 2 +1 ) = 4th node. For n larger than M or negative n, adjust it value so it lies in the area of (0 <= n < m)

Java Code:

```java
public static LinkedListNode rotateList(LinkedListNode head, int n) {
  if (head == null | n == 0) return head;
  int length = getLength(head);
  n = n % length;
  if (n < 0) n += length;
  int rotate = (length - n - 1);

  LinkedListNode pointer  = head;
  int count = 0; 
  while (count < rotate){
    pointer = pointer.next;
    count ++;
  }
  LinkedListNode newHead = pointer.next;
  pointer.next = null;
  LinkedListNode pointer2 = newHead;
  while(pointer2.next != null){
    pointer2 = pointer2.next;
  }
  pointer2.next = head;
  return newHead;
}
public static int getLength (LinkedListNode head){
  int count = 0;
  while (head != null){
    count ++;
    head = head.next;
  }
  return count;
}
```

Complexity Analysis:

> Time complexity: O(m), m is the length of the list
>
> Space complexity: O(1)



### Reverse Alternate K Nodes in a Singly Linked List

Problem:

> Given a linkedlist of interger and an integer ''k', reverse every 'k' element.



Example:

<img src="/Users/Yuehan/Library/Mobile Documents/com~apple~CloudDocs/CodeProblemsNote/img/img11.png" alt="img11" style="zoom: 25%;" />



思路: 

> Within each k element, the problem is a simply "reverse a linked list" problem. The primary challenge of the prolem come to linking two reversed part and dealing with the beginning/end of the linkedlist.
>
> Iterate through the linkedlist, for every reversed k elements fraction, we store its tail and the original head of the next fraction to come. 
>
> The reason we need to keep track with these two node is that:
>
> 1. we need to linked the tail to the next head after reversing 
> 2. The original head of the next fraction is the only thing we get to access that fraction and everyting after it.
>
> Dealing with begining of the linkedlist:
>
> > for the frist fraction, we don't need to link the tail of the previous fraction to it since there is no 'previous fraction'.
>
> Dealing with the end of the linkedlist;
>
> > fort he last fraction, the number of node in that fraction may be less than k, so the judgement to end the loop should be either k nodes are visited or reching the end of the linkedList.



```java
static LinkedListNode reverseKNodes(LinkedListNode head, int k) {
  if (k <=1 || head == null || head.next == null) return head;
  
  LinkedListNode newHead = null;
  LinkedListNode preTail = null;
  
  while (head != null){
    LinkedListNode nextTail = head;
    LinkedListNode pre = preTail;
    int count = k;
    while (head != null && count>0){
      count--;
      LinkedListNode temp = head.next;
      head.next = pre;
      pre = head;
      head =temp;
		}
    if (newHead == null) newHead = pre;
    if (preTail != null) preTail.next = pre;
    preTail = nextTail;
    nextTail.next = head;
  }
  return newHead;
}
```



Complexity Analysis:

> Time Compexity: O(n)
>
> Space Complexity O(1)



### Copy Linked List with Arbitrary Pointer

Problem: 

> Given a special linked list which has not only a common `next` attribute, but also a `arbitrary_pointer` arrtibute. the `arbitrary_pinter` points to a random node in the list. Deep copy the linked list and return it.



Example:

<img src="/Users/Yuehan/Library/Mobile Documents/com~apple~CloudDocs/CodeProblemsNote/img/img12.png" alt="img12" style="zoom: 33%;" />

思路:

> The key mindset to solving this problem is that it is relatively easy to deep copy a common linked list while it is tricky to deep copy the `arbitrary_pointer` field. And the reason behind it is that it is hard to loacte the coressponding node in the new list of a node in the original list. For example, I know that the `arbitrary_pointer` of the node with value 7 points to the node with value 21. But I can't locate the node with value 21 in the new list using that information (If it is a array, we can use index to locate it easily ). Another reason is that the node that one's `arbitrary_pointer` points to may hasn't been created yet.
>
> So it is easier if we leave the tricky part, the `arbitrary_pointer` , after we finished coping other part of the list.
>
> First deep copy the list while handling the `arbitrary_pointer` in this way: simply point it to the same node as the original list does.. While doing so, use a map structure to map the original node to the copied node.
>
> Then iterate through the new list, for each node, get its `arbitrary_pointer` field, locate its corresponding node in the new list using the map. Then update the `arbitrary_pointer` field.



Java Code:

```java
public static LinkedListNode deepCopyArbitraryPointer(LinkedListNode head) {
  	{ if (head == null) {
      return null;
    }
     
    LinkedListNode newHead = new LinkedListNode(0);
    LinkedListNode oldPointer = head;
    LinkedListNode newPointer = newHead;
    Map<LinkedListNode,LinkedListNode> aMap = new HashMap<>();

    // deep copy the linked list with the arbitrary_pointer point to the nodes in the original list.
    while (oldPointer != null){
      newPointer.next = new LinkedListNode(oldPointer.data);
      newPointer = newPointer.next;
      newPointer.arbitraryPointer = oldPointer.arbitraryPointer;
      aMap.put(oldPointer,newPointer);
      oldPointer = oldPointer.next;
    }
		
    // update the arbitrary_pointer using the HashMap
    newPointer = newHead.next;
    while (newPointer != null){
      if (newPointer.arbitraryPointer != null){
      	newPointer.arbitraryPointer = aMap.get(newPointer.arbitraryPointer);
      }
      newPointer = newPointer.next;
    }

    return newHead.next;
  }  
```



Complexity Analysis:

> Time complexity: O(n)
>
> Space complexity: O(n)



## Math & Stat

### Find kth Permutation

Problem:

> Given a set of n elements, find the `k`th permutation.



Example:

<img src="/Users/Yuehan/Library/Mobile Documents/com~apple~CloudDocs/CodeProblemsNote/img/img13.png" alt="img13" style="zoom: 33%;" />

<img src="/Users/Yuehan/Library/Mobile Documents/com~apple~CloudDocs/CodeProblemsNote/img/img14.png" alt="img14" style="zoom:33%;" />

思路：

> If we use the trival approach, which is to genereate all possible permutation and return the `k`th one, the time complexity will be O(n!).
>
> A better approch will be like:
>
> for a set of n elements. The 1st number of a permutation could be {1,n}. And for each of these 1st number, there are (n-1)! premutations. Think about it in this way: 
>
> > Afterthe 1st number is divided, the later part of the permutation is the same as the permution of (n-1) but with (maybe) different number of comsistitution. 



```java
static void findKthPermutation(List<Character> v, int k, StringBuilder result) {
    // base case
    if (v.isEmpty()) return;
    // Recursion
    int count = factorial(v.size()-1);
    int index = (k-1) / count;
    result.append(v.remove(index));
    findKthPermutation(v,k-(index * count), result);
  } 
  static int factorial(int n){
    if (n ==1 || n ==0) return 1;
    return n * factorial(n-1);
  }
```



Complexity Analysis:

> The time complexity is O(n)
>
> The space complexity is O(n)





### Integer Division Without Using * or /

Problem:

> Given two integer `x` and `y`. Return `x` ÷ `y` without using the `/` and `*` operators.



思路：

> The trival approach will be:
>
> > Use a counter to count the number of `y` in `x`. For each iteration, counter increases by one and `x` decreases by `y`. And the loop end when `x` is smaller than `y`.
>
> This approach is very intuative while inefficient.It will takes O(n) time to get the answer.
>
> It is better to approach the problem using the shift operation.
>
> > We will still using the loop. But for each iteration, we left shift the counter and `x`. which will make them increase exponentially. Once `x` becomes larger than or equal to `y`, the loop ends and we use some monor techniques to access each cirenstence and got the answer in O(log n) time.
>
> We can also right shfit `y` instead of left shieft `x` , but calcualtion the squre root is likely to take more time.



Java code:

```java
public static int integerDivide(int x, int y) {
    // handle special cases
    if (y == 0) return -1;
    if (x <= y) return 0;
    if (y == 1) return x;

    int count = 1;
    int temp = y;
    while( x > temp){
      count <<= 1;
      temp <<= 1;
    }
    if (x == temp) return count;

    // if x > temp
    count >>= 1;
    temp >>= 1;
    return count + integerDivide(x-temp, y);
}
```

 

Complexity Analysis:

> Time complexity is O (log n)
>
> Space complexity is O(log n) since the recursive call will comsume memory on stack;



###  Use Rand2() to implement Rand10() 

Problem:

>  Given a function Rand2() that will generate random number between 1 and 2 (inclusive), construct a function Rand10() that generate random number bewtween 1 and 10.

思路：

> Let ```a``` = (```rand2()``` - ```1```) * ```2``` = ```{0,2}```. 
>
> Add ```rand2()``` to ```a``` so we get a random number in ```[1,4]```, which is ```rand4()```
>
> Let ```b``` = (```rand4()``` -```1```) * ```2``` = ```{0,2,4,6}```
>
> Add ```rand2()``` to ```b``` so we get a random number in ```[1,8]```, which is ```rand8()```
>
> Repeat the process again to get a random number in ```[1,16]```, which is ```rand16()```. Minus ```1``` to the result to get random number in ```[0,15]```
>
> If the number generated is larger than ```9```, start again from the begining.
>
> Else, we get a random number in [0,9]. Then add ```1``` to the result to get a random number in [1,10]

Java Code:

```java
public int rand10() {
  while (true){
    int rand4 = (rand2() -1) * 2 + rand2();
    int rand8 = (rand4 -1) * 2 + rand2();
    int rand16 = (rand8 -1) * 2 + rand2();
    int a = rand16 -1;
    if (a <= 9){
      return a + 1;
    }
  }
}
```

Complexity Analysis:

> Time complexity is O(1)
>
> Space complexity is O(1)

Similar Problem:

>  Given a function Rand7() that will generate random number between 1 and 7 (inclusive), construct a function Rand10() that generate random number bewtween 1 and 10.

思路：

> Let ```a``` =  (```Rand7()``` - ```1```) * ```7``` to get random number in {0,7,14,21,28,35,42}
>
> Let ```b``` = ```Rand7()``` to get random number in [1,7]
>
> Let ```c``` == ```a``` +  ```b``` - 1 to get random number in [0,48]
>
> if  ```c```  > 39, start from the begining again. Else, continue. We get random number between[0,39]
>
> Let ```d``` = ```c``` / 4 to get random number in [0,9]
>
> Let ```e``` = ```d``` +1 to get random number in [1,10]

Java Code:

```java
public int rand10() {
  while(true){
    int rand49 = (rand7() - 1) * 7 + rand7();
    int c = rand49 -1;
    if (c <= 39){
      return (c / 4) + 1;
			}
	}
}
```





### Pythagorean Triplets

Problem:

> Given an array of integers, find all pythagorean triplets (a,b,c are pythagorean triplets if a^2 + b^2 = c^2)

思路：

> The intuative approach will takes O(n^3) time. Here is a better approach.
>
> First sort the array ,which will takes at most O(n) time no matter which sorting algorithm you use.
>
> Then we loop through the array and for each iteration, we use an pointer `l` and an pointer `r`. The pointer `l` starts at the first item in the array and the pointer `r` started at the last item in the array. Then we check if arr[l]^2 + arr[r]^2 == arr[i]^2; if that so ,we add these three number to the list. if arr[l]^2 + arr[r]^2 > arr[i]^2, then we decrease `r` by one. if arr[l]^2 + arr[r]^2 < arr[i]^2, we increase `l` by one.



Java Code:

```java
tatic List<int[]> findPythagoreanTriplets(int[] arr) {
    List<int[]> triplets = new ArrayList<int[]>();
    QuickSort(arr,0,arr.length-1);// assume quickSort is already implemented.
    for (int i =0; i < arr.length; i++){
      int l = 0, r = arr.length-1;
      while (l<r){
        if (i != l && i != r){
          int result = arr[l] * arr[l] + arr[r]* arr[r] - arr[i] * arr[i];
          if (result == 0){
            int [] triplet = {arr[l],arr[r],arr[i]};
            triplets.add(triplet);
            r --;
            l ++;
          } else if (result > 0){
            r --;
          } else {
            l ++;
          }
        } 
        else if (i == l) l++;
        else  r --;
      }
    }
    return triplets;
  }
```



Complexity Analysis:

> Time complexity is O(n^2)
>
> Space complexity is oO(n)



### Perfect Squares

Problem:

> Given a positive integer *n*, find the least number of perfect square numbers (for example, `1, 4, 9, 16, ...`) which sum to *n*.

Solution 1:

> Use BFS(广度优先算法). The first possible combination found will be the solution since BFS always find the nearst possible solution. 
>
> The possible combinations are the nuumber in the range [`1`,`sqrt(n)`] since anything larger than `sqrt(n)` will have a square larger than `n`.

Java Code:

```java
public static int numSquares(int n) {
        int a = (int) Math.sqrt(n);
        if (a * a == n) return 1;
        if (n <= 3) return n;

        int[] squares = new int[a];
        Queue<Integer> Q = new ArrayDeque<>();
        Queue<Integer> newQ = new ArrayDeque<>();
        Q.add(n);
        int steps = 1;
        while (!Q.isEmpty()){
            	int num = Q.poll();
          
            for (int i = 1; i <= a; i++) {
                int remain = num - i * i;
              	
              	if (remain < 0){
                  break;
                } else if (remain == 0) {
                    return steps;
                } else {
                    newQ.add(remain);
                } 
            }
            if (Q.isEmpty()){
                steps ++;
                Q = newQ;
                newQ = new ArrayDeque<>();
            }
        }
        return n;
    }
```

Soluiton 2:

> Use Dynamic Programing from the buttom-up. Form a map of solution from `1` to `n`, the solution of smaller number could be used in calculation in larger number. 

Java Code:

```java
public static int numSquares(int n) {
        int a = (int) Math.sqrt(n);
        if (a * a == n) return 1;
        int[] sln = new int[n+1];
        Arrays.fill(sln,n);
        sln[0]=0;
        sln[1]=1;
        
        for(int i = 2; i<=n;i++){
            for (int j = 1; j*j <= i; j++){
                sln[i] = Math.min(sln[i],sln[i-j*j]+1);
            }
        }
    return sln[n];
    }
```



Solution 3: 

> Use DFS(深度优先算法) and Dynamic Programming(动态规划) from the top-down. The first possbile combination found may not be the solution. It will likely end up finding all possbile solution. This may sounds inefficient but the advantage of using DFS is that we can using **Dynamaic Programming** to reduce the complexity. Most of the nodes are repetitive so by caching the result and reuse them, most of the recursions will be avoided.

```java
public static int numSquares(int n) {
        int a = (int) Math.sqrt(n);
        if (a * a == n) return 1;
        
        int[] sln = new int[n+1];
        Arrays.fill(sln,Integer.MAX_VALUE);
        
        sln[0]=0;
        sln[1]=1;
        return numSquaresHelper(n,sln);
    }
    public static int numSquaresHelper(int n,int[] sln){
        if (sln[n] == Integer.MAX_VALUE){
            int MaxPossible = (int)Math.sqrt(n);
            for (int i = 1; i<= MaxPossible;++i){
                sln[n] =Math.min(sln[n],numSquaresHelper(n-i*i,sln)+1);
            }
        }
        return sln[n];
    }
```



### Find Missing Number:

Problem:

> Given an array of positive numbers from 1 to n, such that all numbers from 1 to n are present except one; find the missing number.



Example:

<img src="./img/img15.png" alt="img15" style="zoom:50%;" />

Brainstrom:

> A trival solution would takes O(n^2) time.
>
> A better solution is to sort the array firstly. Sorting the array would takes O(log n) time. We could do better.
>
> Given that all numbers from 1 to n except one is present, we could use the formula that calculate the sumation of numbers from 1 to n:
>
> ​																												(n * (n +1))  / 2



Java Code:

```java
static int findMissing(List<Integer> input) {
    int size = input.size()+1;
    int total = ((size) * (size + 1)) / 2;
    int sum = 0;
  
    for (int i: input) sum += i;

    return total - sum;
  }
```



Complexity Analysis:

> The time complexity is O(n)
>
> The space complexity is O(1)



### All Possible Combinations for a Given Sum

Problem:

> Given a positive interger, `target`, print all the possible combinations for this number using positive integers.



Example:

> if the given input is `4`, these are the possible combinations:
>
> > 1, 1, 1, 1
> >
> > 1, 1, 2
> >
> > 1, 3
> >
> > 2, 2



BrainStorm:

> Use a recursive function. Maintain an array named `currentCombo` to store the combination that is currently checked. Maintain an integer named `CurrentSum` to store the sum of the numvers in `currentCombo`. Maintain an integer `addNum` indicate the current number that will be added to the `currentCombo`
>
> `addNum` is initially `1` and `currentSum` is initially 0;
>
> 
>
> if`currentSum` == `target` , add the combination in `currentCombo` to the result list and return (go back to the previous layer)
>
> Loop Start:
>
> > if `addNum` + `currentCombo` <= `target`, call the function it self with `addNum` set to `1` and `addNum` added to`currentCombo`  and `currentSum`. after the nested function return, increase `addNum` by `1` and continues 
> >
> >  else, return (go back to the previous layer)



```java
  static ArrayList<ArrayList<Integer>> printAllSum(int target) {
      ArrayList<ArrayList<Integer>> returnList = new ArrayList<ArrayList<Integer>>();
      int currentSum = 0;
      ArrayList<Integer> currentCombo = new ArrayList<Integer>(); 
      printSumRec(target, 1,currentSum, currentCombo, returnList);
      return returnList;
  }

  static void printSumRec(int target,int addNum, int currentSum, ArrayList<Integer> currentCombo, ArrayList<ArrayList<Integer>> returnList){
      if (currentSum == target){
        returnList.add((ArrayList)currentCombo.clone());
        return;
      }

      while (addNum < target){
          if (currentSum + addNum <= target){
              currentCombo.add(addNum);
              printSumRec(target, addNum, currentSum + addNum, currentCombo, returnList);
              currentCombo.remove(currentCombo.size()-1);
          } else {
             
              return;
          }
          addNum ++;
      }
      return;
  }
```



Complexity Analysis:

> The time complexity is O(n^2)
>
> The space complexity is O(n)



# Print All Permutations of a String

Problem:

> Implement a method to print all permutations of a given string without duplicates.For instance, all permutations of string “bad” are:

<img src="./img/img16.png" alt="img16" style="zoom: 33%;" />

Brainstorm:

> For any given string with length `n`, there are `n` differnt first character options possible. Since each char of the string could be use as the fisrt char.
>
> Then if the first char is the `k`th char of the string, then there are `n-1` different second character options possible. The second char could be any char in the string except the `k`th char.
>
> So we can write a recursive function in this way. Use a loop to iterate through the string, for each iteration, use that char as the first char of the permutation and call the funtion itself with the remaining part of the string as input.



Java Code:

```java
static List<String> permuteString(String input) {
    List<String> result = new ArrayList<String>();
    String head = "";
    permuteStringRec(head, input, result);
    return result;
  }
//recursive helper function	
static void permuteStringRec(String head,String input, List<String> result){
  if (input.length()==1){
    result.add(head+input);
    return;
  }

  for (int i = 0; i< input.length(); i++){
    String[] swaped = swapChar(input,i);
    permuteStringRec(head+swaped[0],swaped[1],result);
  }
}
static String[] swapChar(String input,int i){
  String head = input.substring(i,i+1);
  String rest = input.substring(0,i) + input.substring(i+1,input.length());
  String[] output = {head,rest};
  return output;
}
```



Complixity Analysis:

> The time complexity is O(n!)
>
> The space complexixty is O(n!)



### Find All Subsets of a Set

Problem:

> Given a set of interger. Find all possible subsets of this set of interger.



Example:

<img src="./img/img17.png" alt="img17" style="zoom:33%;" />

Brainstorm:

> For a set of `n` interger, the total number of subsets is `2^n`. But why?
>
>  Think about it in this way. if we increase the number of interger in the set by 1, how will the number of possible subsets increases? 
>
> Every original subset is still legal. And for each of them, you can add the new number to it to get a legal new subset. So the number of possible subsets will doubled.
>
> So we can have the conlusion that the totoal number of subsets is `2^n`



Java Code:

```java
static void findAllSubsets(List<Integer> v, List<HashSet<Integer>> sets) {
  int subsetCount = (int)Math.pow(2,v.size());
  int setLength = v.size();
  for (int i =0;i<subsetCount; i++){
    HashSet<Integer> aSet= new HashSet<>();
    for (int pos = 0; pos<setLength ; pos++){
      if (getBit(pos,i)==1){
        int digit = v.get(pos);
        aSet.add(digit);
      }
    }
    sets.add(aSet);
  }
}

static int getBit(int position,int num){
  int temp = 1 << position;
  int result = temp & num;
  if (result == 0) return 0;
  else return 1;

}
```



### Is String a Valid Number?

Problem:

> GIven an input string,determine if it makes a vilid number or not



Example:

<img src="./img/img18.png" alt="img18" style="zoom:25%;" />



Brainstorm:

> Use a state machine to approach this problem. The restrictions of a vaild number is as follow:
>
> >No more than one decimal point
> >
> >If start with 0, a decimal point should follow
> >
> >Should end at a decimal point



Java Code:

```java
enum STATE {START,ZEROSTART, BEFORE,AT,AFTER, ERROR};

static boolean isNumberValid(String s) {
  STATE theState = STATE.START;
  for(int i = 0;i<s.length();i++){
    theState = nextState(theState, s.charAt(i));
    if (theState == STATE.ERROR){
      return false;
    }
  }
  if (theState != STATE.AT) return true;
  else return true;
}
static STATE nextState(STATE theState, char ch ){
  switch (theState){
    case START:
      if (ch =='.') return STATE.AT;
      else if (ch <= '9' && ch >= '1') return STATE.BEFORE;
      else if (ch == '0') return STATE.ZEROSTART;
      else return STATE.ERROR;
    case ZEROSTART:
      if (ch=='.') return STATE.AT;
      else return STATE.ERROR;

    case BEFORE:
      if (ch =='.') return STATE.AT;
      else if (ch <= '9' && ch >= '0') return STATE.BEFORE;
      else return STATE.ERROR;
    case AT:
      if (ch <= '9' && ch >= '0') return STATE.AFTER;
      else return STATE.ERROR;
    case AFTER:
      if (ch <= '9' && ch >= '0') return STATE.AFTER;
      else return STATE.ERROR;
    default:
      return STATE.ERROR;

  }
}
```



