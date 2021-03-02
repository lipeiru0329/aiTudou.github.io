---
title: sort
date: 2020-07-02 18:54:31
tags:
---

## Sort

### Merge Sort:

```
func sortArray(nums []int) []int {
    
    if len(nums) <= 1 {
        return nums
    }
    
    mid := len(nums) / 2
    ans := merge(sortArray(nums[:mid]), sortArray(nums[mid:]))
    // fmt.Println(ans)
    return ans
}

func merge(num1 []int, num2 []int) []int {
    
    if len(num1) == 0 {
        return num2
    }
    
    if len(num2) == 0 {
        return num1
    }
    
    ans := []int{}
    
    i := 0
    j := 0
    
    for i < len(num1) && j < len(num2) {
        if num1[i] < num2[j] {
            ans = append(ans, num1[i])
            i++
        } else {
            ans = append(ans, num2[j])
            j++
        }
    }
    if i < len(num1) {
        ans = append(ans, num1[i:]...)
    }
    
    if j < len(num2) {
        ans = append(ans, num2[j:]...)
    }
    return ans
}
```

