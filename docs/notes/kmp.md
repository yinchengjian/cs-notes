## kmp就是求子串位置

子序列可以不连续

子串连续

kmp解决str2在str1中包含，返回str2在str1中的位置，

最长前缀和最长后缀的匹配程度

在str2开始之前就已经知道str2对应的next数组，然后进行匹配，每次匹配不到就要将str2的下标移到该下标所对应的next数组的位置去

![1548208546967](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1548208546967.png)

# Manacher算法

一个字符串中找到最长回文子串

![1548225045514](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1548225045514.png)



![1548225162599](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1548225162599.png)

先找到最长回文串