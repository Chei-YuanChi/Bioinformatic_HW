# IIR新生訓練Bioinformatic_HW
學號：NE6101115 
姓名：崔元淇
## 作業前置
### * fna檔讀取
1. 耗時

![](https://i.imgur.com/Gk6pHx3.png)

2. 記憶體消耗

![](https://i.imgur.com/PvNR8uD.png)


### * 資料統計(計算 genomic 數量與種類)
```
dict_gen = {'a' : S.count('a') , 'c' : S.count('c') , 't' : S.count('t') , 'g': S.count('g') }
```
## 第一題
### 方法
* order 0 : 各 genomic 數量 * log( genomic 在此序列之比例) 相加
* order 1, 2 : 建立transition matrix
* order 1 ：

![](https://i.imgur.com/twvDtP6.png)
```
total = log(gen_prob[S[0]], 2)
    for i in range(len(S)):
        if i > 0:
            total += log(order_1_TM[S[i-1]][S[i]], 2)
```
// gen_prob 為單一基因出現的機率
* order 2 ：

![](https://i.imgur.com/iZ69eKM.png)
```
    total = 0
    for i in range(len(S)):
        if i == 0 or i == 1:
            total += log(gen_prob[S[i]], 2)
        else:
            total += log(order_2_TM['{}'.format(S[i - 2] + S[i - 1])][S[i]], 2)
```
### 結果 (含時間與記憶體消耗)
* 建 transition matrix 消耗時間與記憶體：

![](https://i.imgur.com/lYsymFg.png)

![](https://i.imgur.com/Nhfnzys.png)
* 各 order 結果：

![](https://i.imgur.com/PBj9nil.png)
![](https://i.imgur.com/YK0wy76.png)


## 第二題
### 方法
* from hmmlearn import hmm 使用 MultinomialHMM
* 調整參數以達到較好結果 (n_iter, n_states)
### 結果 (含時間與記憶體消耗)
```
s = []
for i in S:
    for j in range(len(dict_gen)):
        if i == [k for k in dict_gen.keys()][j]:
            s.append(j)
s= np.array(s).reshape((-1, 1))

n_states = 7
model = hmm.MultinomialHMM(n_components = n_states, n_iter = 1000, tol = 0.01)
model.fit(s)
print('prob(log) :', model.score(s) / log(2))
```
![](https://i.imgur.com/clUnCx4.png)
![](https://i.imgur.com/8iC3ViI.png)
* 最後選擇狀態數量為7，訓練1000次的模型
1.     在訓練過程中狀態數量低於 7 的準確度雖然仍比 ( plain ) Markov model 要好，但 7 是其中最高的，至於高於 7 的情況，準確度雖然有較 7 高一點，但訓練時間過長，整體效率上沒有這麼好。
2.     在訓練不到1000次的模型中，可能因為訓練次數不足導致準確度不高，而高於1000次的模型中則漸漸走向過擬合導致準確度開始走下坡。
* 利用訓練好的狀態，生成隨機序列：
```
seq, output_state = model.sample(n_samples = 100000, random_state=None)
output_seq = ''
for i in seq.reshape((len(seq))):
    output_seq += [j for j in dict_gen.keys()][i]
print('output sequence length :', len(output_seq))
```
結果過長因此用長度帶過

![](https://i.imgur.com/4L4Fak3.png)
![](https://i.imgur.com/84dJJaY.png)
