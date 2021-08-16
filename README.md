# IIR新生訓練Bioinformatic_HW
學號：NE6101115 
姓名：崔元淇
## 作業前置
### * 資料統計(計算 genomic 數量與種類)
```
order_0_dict = {'a' : S.count('a'), 'c' : S.count('c'), 't' : S.count('t'), 'g' : S.count('g')}

for i in range(100000):
    if i > 0:
        order_1_dict[S[i-1] + S[i]] += 1
    if i > 1:
        order_2_dict[S[i-2] + S[i-1] + S[i]] += 1
```
## 第一題
### 方法
* order 0 : 

    意義 ： genomic獨立出現在此序列機率
    
    方法 ： 各 genomic 數量 * log ( genomic 在此序列之比例) 相加
* order 1, 2 : 

    意義 ： 考慮每個genomic及前1~2個genomic出現在此序列的機率
    
    方法 ： 建立transition matrix
* order 1 ：

![](https://i.imgur.com/lYoQWy0.png)



* order 2 ：

![](https://i.imgur.com/8XKzkqo.png)


### 結果
* 各 order 結果：

![](https://i.imgur.com/Xxdcsad.png)



## 第二題
### 方法
* from hmmlearn import hmm 使用 MultinomialHMM
* 調整參數以達到較好結果 (n_iter, n_states)
### 結果 
```
s = s2 = []
for i in S:
    for j in range(len(dict_gen)):
        if i == [k for k in dict_gen.keys()][j]:
            s.append(j)
s= np.array(s).reshape((-1, 1))

for i in S2:
    for j in range(len(order_0_dict)):
        if i == [k for k in order_0_dict.keys()][j]:
            s2.append(j)
s2= np.array(s2).reshape((-1, 1))

n_states = 3
model = hmm.MultinomialHMM(n_components = n_states, n_iter = 1000, tol = 0.01)
model.fit(s)
print('prob(100001~200000) :', model.score(s) / log(2))
print('prob(200001~302400) :', model.score(s2) / log(2))
```

* 利用訓練好的狀態，判斷相同資料集 [ 200001 ~ 302400 ] 的資料：

![](https://i.imgur.com/UNBXBbG.png)

