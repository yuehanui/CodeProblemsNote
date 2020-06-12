# CodeProblemsNote

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
>Iterate the array and use HashMap to store each element. Specifically, store each value in the array as the key and store its index as the value into the Hashmap.
Since to put an item into the Hashmap takes O(1) time, and to check if the Hashmap conatins a specific value also takes O(1) time. The whole process will take O(n) time.


Algorithm:
>Iterate through the array, for each element, check if its "compementary number" ( the number add up to the target) is alrady in the Hapshmap. If it is true, the result is found. Otherwise, Put the index of the element into the array, use the value of the element as key.

Java Code: 

```
puclic int[] twoSum(int[] nums, int target) {
	Map<Integer, Integer> theMap = new HashMap<>();
	for( int i = 0; i < nuims.length; i++) {
 		if (theMap.containsKey( target - nums[i] )) {
 			int[] result = {theMap.get(target-nums[i], i};
 			return result;
 		}
 	}
 	return null;
}
```
Thoughts:

>Why use the index of the element as value, the value of the element as key when putting it into the HashMap, instead of the other way?
The key of this algorithm is:
>1. Use HashMap.containsKey() to check if it contains the complementary number.
>2. Use HashMap.get() to get the index of the complementary number

>If we store the data in the other way. The first step could be replaced using HashMap.containsValue(), which is fine. However, the second part would be a problem since there is no HashMap.getValue(), or at least there is no efficent way to implement HashMap.containsValue(). The efficiency of hashMap comes from the hush function which works on the key instead of the value. 


 










