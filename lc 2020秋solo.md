[TOC]

### 2020秋leetcode个人赛

#### 第一题 速算机器人

<img src="../../../TyporaImage/1-1600060185966.png" alt="1" style="zoom:67%;" />

标签：模拟，暴力

直接上代码：

```java
class Solution {
    public int calculate(String s) {
        // if(s.length() == 0) return 0;
        int x = 1;
        int y = 0;
        for(int i = 0; i < s.length(); ++i){
            if(s.charAt(i) == 'A'){
                x = 2 * x + y;
            }else{
                y = 2 * y + x;
            }
        }
        return x + y;
    }
}
```

#### 第二题 早餐组合

<img src="../../../TyporaImage/2-1600061074875.png" alt="2" style="zoom:67%;" />

<img src="../../../TyporaImage/3-1600061127495.png" alt="3" style="zoom:67%;" />

标签：二分查找

思路：

1. 暴力算法，因为数据量太大，会超时。
2. 如何优化？ 有两个数组，如果两个数组分开排列组合再去判断时间复杂度太多。我们对其中一个数组进行循环。（这里有一个优化，就是当前值已经大于k了，那么就没必要循环了）
3. 我们在对另外一个数组进行二分查找，k值变成k - staple[i], 能够进行快速查找。

代码：

```java
class Solution {
    public int breakfastNumber(int[] staple, int[] drinks, int x) {
        Arrays.sort(staple);
        Arrays.sort(drinks);
        long count = 0;
        for(int i = 0 ; i < staple.length; i++){
            if(staple[i] >= x) break;
            int low = 0; 
            int high = drinks.length - 1;
            int res = x - staple[i];
            int mid = 0;
            while(low <= high){
                mid =low + (high - low) / 2;
                if(drinks[mid] < res){
                   low = mid + 1;
                }else if(drinks[mid] > res){ 
                    high = mid - 1;
                }else{
                    low++; //本来应该返回mid，但是防止有相同元素，所以对low++
                }
            }
            count = count + low; 
        }
        return (int)(count % 1000000007); //(int)le9 + 7
    }
}
```

二分查找板子：

```java
while(low <= high){
    mid =low + (high - low) / 2;
    if(drinks[mid] < res){
        low = mid + 1;
    }else if(drinks[mid] > res){ 
        high = mid - 1;
    }else{
        return mid;
    }
}
//or another way
while(low < high){
    mid = (high + low) / 2;
    if(drinks[mid] < res){
        low = mid + 1;
    }else if(drinks[mid] > res){ 
        high = mid;
    }else{
        return mid;
    }
}
```

#### 第三题  秋叶收藏集

<img src="../../../TyporaImage/4-1600062542564.png" alt="4" style="zoom:67%;" />

<img src="../../../TyporaImage/5-1600062553486.png" alt="5" style="zoom:67%;" />

##### 思路1：

   标签：动态规划， 贪心算法

1. 需要三个状态：
   - dp\[i][0]：截止到第i个树叶，使树叶排列处于全红的状态 需要修改的次数
   2. dp\[i][1]：截止到第i个树叶，使树叶排列处于「红、黄」的状态 需要修改的次数
   - dp\[i][2]：截止到第i个树叶，使树叶排列处于「红、黄、红」的状态 需要修改的次数.
2. 图像：

<img src="../../../TyporaImage/1599904648-QCZjmT-image.png" alt="image.png" style="zoom:150%;" />

3. 动态方程 ：
   - dp\[i][0] = dp\[i-1][0] + ( leaves[i]\=='r'  ?  0 : 1)
   - dp\[i][1] = min(dp\[i-1][0], dp\[i-1][1]) + ( leaves[i]\=='y' ? 0 : 1)
   - dp\[i][2] = min(dp\[i-1][1],  dp\[i-1][2])+(leaves[i] =='r' ? 0 : 1)

代码：

```java
public int minimumOperations(String leaves) {
    // dp[0]表示使树叶排列处于全红的状态 需要修改的次数
    // dp[1]表示树叶排列处于「红、黄」的状态 需要修改的次数
    // dp[2]表示树叶排列处于「红、黄、红」的状态 需要修改的次数
    int[] dp = new int[3];
    // 截止到第一个树叶
    dp[0] = (leaves.charAt(0)=='r'?0:1);

    // 截止到第二个树叶
    dp[1] = dp[0]+(leaves.charAt(1)=='y'?0:1);
    dp[0] += (leaves.charAt(1)=='r'?0:1);

    //截止到第三个树叶
    dp[2] = dp[1]+(leaves.charAt(2)=='r'?0:1);
    dp[1] = Math.min(dp[1], dp[0])+(leaves.charAt(2)=='y'?0:1);
    dp[0] = dp[0]+(leaves.charAt(2)=='r'?0:1);

    for(int i=3;i<leaves.length();i++) {
        dp[2] = Math.min(dp[2], dp[1])+(leaves.charAt(i)=='r'?0:1);
        dp[1] = Math.min(dp[1], dp[0])+(leaves.charAt(i)=='y'?0:1);
        dp[0] = dp[0]+(leaves.charAt(i)=='r'?0:1);
    }
    return dp[2];
}
```

##### 思路2 :

标签：数组前缀和

思路：

1. 用sum\[x] 表示`[0, x)`内红叶的数量，假设整理后红叶的区间为 `[0, i)` 和 `[j, n)`, 那么黄叶区间为 `[i, j)`.
2. 对于左右两个区间, 操作次数为区间长度减去红叶的数量, 对于中间的区间, 操作次数就是红叶的数量.
3. 需要操作的总次数为` (i - sum[i]) + (n - j - sum[n] + sum[j]) + (sum[j] - sum[i])`, 整理后得到` n - sum[n] + (i - 2 * sum[i]) - (j - 2 * sum[j])`, 约束条件为 `0 < i < j < n`. 为了让操作数最少, 我们希望 j 确定时` i - 2 * sum[i]` 最小.
4. 用 `min[x] `记录` [1, x]` 区间内的` i - 2 * sum[i] `的最小值, 将 j 从 n - 1 遍历到 2,`min[j - 1] ` 即为当前最小的` i - 2 * sum[i]`.


代码：

```java 
class Solution {
    public int minimumOperations(String leaves) {
        int n = leaves.length();
        char[] array = leaves.toCharArray();
        int[] sum = new int[n + 1];
        for (int i = 0; i < n; i++)
            sum[i + 1] = sum[i] + (array[i] == 'r' ? 1 : 0);
        int[] min = new int[n + 1];
        int currentMin = Integer.MAX_VALUE;
        //最左边和最右边一定要是r红叶
        for (int i = 1; i < n - 1; i++) {
            currentMin = Math.min(currentMin, i - 2 * sum[i]);
            min[i] = currentMin;
        }
        int result = Integer.MAX_VALUE;
        for (int j = n - 1; j > 1; j--)
            result = Math.min(result, n - sum[n] + min[j - 1] - j + 2 * sum[j]);
        return result;
    }
}
```

#### 第四题 快速公交







