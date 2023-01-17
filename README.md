# KMA TTV CTF
## Phpri Phprai
### Description
Khi truy cập vào trang web BTC cung cấp ta nhận được source code xử lý như sau:
![image](https://user-images.githubusercontent.com/86275419/212816476-1d4e082e-635f-4f75-8733-ac0a472ba16d.png)

Bài này yêu cầu ta phải truyền các cặp `Key=Value` lên URL để vượt qua các câu lệnh kiểm tra điều kiện `IF` để lấy Flag. Ta phải vượt qua tổng cộng 5 câu lệnh `IF` để lấy được Flag hoàn chỉnh.

### Solution
1. Tại vòng IF thứ nhất:

![image](https://user-images.githubusercontent.com/86275419/212819298-86ce4140-d9b4-4610-ad15-1b0ca9f092db.png)

Ta sẽ phải truyền 2 cặp `Key=Value` lên URL sao cho 2 Value phải khác nhau, nhưng khi Hash 2 value qua hàm `md5()` phải cho kết quả so sánh `==` giống nhau. 
Mình đã search trên mạng với từ khóa `php hash collision` và tìm được [bài viết này](https://news.ycombinator.com/item?id=9484757)

Cặp Key-Value 1: `1=QNKCDZO` 

Cặp Key-Value 2: `2=240610708`

URL lúc này: `47.254.251.2:8889/?1=QNKCDZO&2=240610708`

Ta lấy được Flag đầu tiên: `KCSC{B0_u_Bu_S4`

2. Tại vòng IF thứ hai:

![image](https://user-images.githubusercontent.com/86275419/212825259-abd99b01-b14d-497a-927e-594277f3049e.png)

Ta sẽ phải truyền 1 cặp `key&value` sao cho khi so sánh với giá trị của biến `$$flag` qua hàm `strcmp()` sẽ phải giống nhau. Hiện tại mình để ý thấy biến $$flag không thấy được khởi tạo trong source code nên mình sẽ thử không truyền value lên url

Cặp Key-Value 3: `3=`

URL lúc này: `47.254.251.2:8889/?1=QNKCDZO&2=240610708&3=`

Ta lấy được phần Flag thứ hai: `C_8usssssss_htt`

3. Tại vòng IF thứ ba:

![image](https://user-images.githubusercontent.com/86275419/212826228-e1dcead0-1803-4c13-a284-45b9cd5126e2.png)

Ta sẽ phải truyền một giá trị lên url sao cho giá trị này khi so sánh bằng toán tử `==` phải giống chuỗi '1.4e5' và khi so sánh bằng toán tử `!==` phải khác chuỗi '1.4e5'

Toán tử  `==` khi so sánh 2 giá trị sẽ chuyển một chuỗi ra kiểu số để so sánh mà không xét đến kiểu dữ liệu là gì 

Toán tử `!==` sẽ so sánh 2 chuỗi phải có giá trị giống nhau và kiểu dữ liệu cũng phải giống nhau

--> Ta sẽ đổi `1.4e5` ra số sẽ được `140000`. Ta sẽ truyền `140000` lên URL

Cặp Key-Value 3: `4=140000`

URL lúc này: `47.254.251.2:8889/?1=QNKCDZO&2=240610708&3=&4=140000`

Ta lấy được phần Flag thứ ba: `ps://www.youtub`

4. Tại vòng IF thứ tư

![image](https://user-images.githubusercontent.com/86275419/212827465-59dfcb61-f67e-43f3-a7c7-48805ad6d259.png)

Như đã giải thích bên trên với toán tử `==` và `!==`

Ta sẽ truyền: `%2069` lên URL (%20 là encode url của dấu cách)

+ Khi so sánh `$str5 == 69`: $str5 là kiểu chuỗi sẽ được convert sang kiểu số nên sẽ cho giá trị boolean True
+ Khi so sánh `$str5 !== '69'` và `$str5 !==69`: $str5 sẽ giữ nguyên kiểu dữ liệu (do đó có dấu cách) nên khi so sánh với '69' sẽ cho giá trị boolean True
+ Khi `$str5` qua hàm `trim()` sẽ loại bỏ dấu cách, khi qua hàm strlen() sẽ có giá trị bằng 2 do đó giá trị boolean khi so sánh ở đây cũng là True

URL lúc này: `47.254.251.2:8889/?1=QNKCDZO&2=240610708&3=&4=140000&5=%2069`

Ta lấy được phần Flag thứ tư: `e.com/watch?v=x`

5. Tại vòng IF cuối

![image](https://user-images.githubusercontent.com/86275419/212829856-a973aa20-9b84-4871-a11f-0a4108245c9c.png)

Ở đây ta thấy bài sử dụng hàm `preg_replace()` để replace chuỗi của `$str1` (KaCeEtCe) thành `''` trong chuỗi `$str6` (chuỗi ta nhập vào)

Để vượt qua hàm này đơn giản là ta sẽ truyền: `KaCeKaCeEtCeEtCe`
+ Vì khi replace, hàm này sẽ replace `KaCeEtCe`, tại chuỗi ta truyền vào sẽ replace KaCe`KaCeEtCe`EtCe, do đó sau khi replace chuỗi ta nhập vào thì lúc này còn `KaCeEtCe`

URL lúc này: `47.254.251.2:8889/?1=QNKCDZO&2=240610708&3=&4=140000&5=%2069&6=KaCeKaCeEtCeEtCe`

Ta lấy được phần Flag cuối: `QtC3F8fH6g}`

Flag: `KCSC{B0_u_Bu_S4C_8usssssss_https://www.youtube.com/watch?v=xQtC3F8fH6g}`


