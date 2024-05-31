# [easy] Insomnia   
 --- 
[Hack The Box](https://app.hackthebox.com/challenges/Insomnia)    
 --- 
目標的首頁如圖   
![image.png](files\image_y.png)    
1. 嘗試登入 admin → User not found   
2. 嘗試登入 administrator → User not found   
3. 能點的超連結都點了之後，決定從註冊一個帳號開始   
    1. 註冊 administrator → Username already exists   
    2. 這邊就讓我覺得蠻奇怪的，明明我嘗試登入 administrator 時，他回傳user not found   
![image.png](files\image_f.png)    
   
登入自己註冊的user之後，會跳轉到這個畫面，但是也沒有更多的資訊了   
![image.png](files\image.png)    
去討論串找提示：仔細閱讀source code   
從這段程式碼可以看出來只要成功用administrator的身分登入就會拿到flag
但又回到怎麼用administrator的身分登入的問題   
![image.png](files\image_n.png)    
- 嘗試Brute force破解密碼 → 速度超慢，flag已經找到了卻都還沒破解密碼   
- 註冊” administrator” → 成功註冊但是沒有成功偽裝成”administrator”   
- 嘗試username跟password都不輸入任何字元 → User not found   
    - 這個有點奇怪，因為根據程式碼，應該要 return “Please provide username and password” 這串 error msg才對   
    - 選擇把注意力放在實作跟邏輯有落差的code segment   
   
回去看code想了很久，才發現圖中標示[1]的地方有syntax error，正確的語法應該是
`if (count($json\_data) != 2)`   
這意味著程式碼並不會真的檢查到我們其實沒有提供同時username+password
而且程式碼註記[2]的地方透露出，程式碼會用這筆json\_data去查詢是否存在該資料，也沒有任何的防呆機制（例如沒有要求哪些column一定要被檢查到，而是只要有input對應的data就return出來）   
但是在登入頁面試著登入administrator卻不提供密碼，這樣的結果是user not found   
![image.png](files\image_a.png)    
輸入並攔截我剛才註冊的user，想到就算我不輸入password，JSON還是會夾帶 `password` 這個key發出request   
![image.png](files\image_e.png)    
所以直接刪掉 `password`   
![image.png](files\image_x.png)    
這樣就成功在不知道密碼的情況下登入administrator了   
![image.png](files\image_1.png)    
```
 # Flag
 HTB{I_just_want_to_sleep_a_little_bit!!!!!}


```
