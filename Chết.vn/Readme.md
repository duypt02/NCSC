## Đây là bài luyện tập đầu tiên trong quá trình Training của mình tại KCSC
## Link challenge: https://xn--cht-8jz.vn/

# Challenge 1
Khi vào Challenge sẽ hiện tra 1 trang web trống
![image](https://user-images.githubusercontent.com/86275419/218961726-15d7920e-5c7f-4bee-a1c6-28bdd893117a.png)

View-source ta sẽ có Flag

![image](https://user-images.githubusercontent.com/86275419/218961907-d2c3d9e0-93b7-439a-a869-f24bce6cbe3b.png)

### Flag: `flag: FLAG{ddf1ea89-cd89-4da3-98bc-08028ac4dc7e}`

# Challenge 2
Khi vào Challenge author sẽ cho ta đoạn code PHP xử lý phần BE
![image](https://user-images.githubusercontent.com/86275419/218962362-9dbfaa82-aa61-456e-86ff-6fe19fe7ac7c.png)

Để ý thấy rằng Flag đã được mã hóa lần lượt qua các hàm: base64_encode(hex2bin(strrev(bin2hex(str_rot13(FLAG)))))

### Solution

Đề bài đã cho ta sẵn Flag sau khi được mã hóa, do đó ta chỉ cần giải mã ngược lại là sẽ có Flag

Code decrypt:

![image](https://user-images.githubusercontent.com/86275419/218963928-9fa958d2-62e4-4a9b-800b-132df4e10bf4.png)

Ta có Flag:

![image](https://user-images.githubusercontent.com/86275419/218964113-5502a690-96b7-4dd0-bb29-0b17890932ce.png)

### Flag: `FLAG{c564ca8b-5c94-4446-aba4-955148676bfc}`
# Challenge 3
Khi vào Challenge author sẽ cho ta đoạn code PHP xử lý phần BE

![image](https://user-images.githubusercontent.com/86275419/218966966-1b77245d-e651-4441-91b1-aecb0defdace.png)

Mục tiêu của bài này là ta sẽ phải truyền giá trị lên parameter `number` sao cho giá trị đó phải !== '1337' và khi convert sang kiểu int (hàm `intval()`) phải === 1337
+ Toán tử `===` sẽ so khớp 2 giá trị phải cùng kiểu và cùng giá trị
+ intval(value, base), với base = 0 sẽ mặc định chuyển value sang hệ cơ số ứng với định dạng của value (nếu value bắt đầu với 0x thì sẽ chuyển sang hệ hexa, bắt đâu bằng 0 thì sang hệ 8, còn lại sẽ chuyển sang hệ 10)

### Solution

Payload: `?number=%201337`

+ %20 là dấu cách, ở vòng IF đầu tiên `$number === '1337'` do toán từ `===` so khớp phải cùng kiểu và giá trị nên khi `1337` === ` 1337` sẽ trả về giá trị False
+ Taị vòng IF thứ 2, do khi qua hàm `intval()` sẽ convert `' 1337'` thành `1337` do đó khi so sánh với `1337` sẽ trả về giá trị True

![image](https://user-images.githubusercontent.com/86275419/218972129-196bbe73-d254-4d3f-b129-8991255d6b16.png)

### Flag: `FLAG{554382f3-960e-4859-9c79-c64ecd4445e7}`

# Challenge 4

![image](https://user-images.githubusercontent.com/86275419/218972576-ae24b013-133b-4ca3-b37d-942b3cf31045.png)

Mục đích cảu bài này là ta sẽ phải truyền lên 2 parameter `0` và `1` giá trị sao cho khi so sánh chúng phải khác nhau, nhưng khi được băm ra (qua hàm md5()) chúng phải giống nhau

### Solution

Payload: `?0[]=1&1[]`

+ Tại vòng IF so khớp đầu tiên ($_GET[0] != $_GET[1]), do khi mình truyền vào parameter 0 một mảng có 1 giá trị là 1, còn parameter 1 mình truyền một mảng không có giá trị (nhưng mảng vẫn có 1 giá trị là `""`) do đó khi so khớp 2 mảng với nhau sẽ trả về giá trị False
+ Tại vòng IF so khớp thứ 2 (md5($_GET[0]) === md5($_GET[1])) do md5() không băm được mảng nên sẽ trả về giá trị NULL, do đó khi so khớp NULL === NULL sẽ trả về giá trị True

![image](https://user-images.githubusercontent.com/86275419/218979769-94e3679d-352e-4f3b-b03d-47fe792b59a5.png)

### Flag:` FLAG{82104890-f270-4d27-b4e4-638a940ffdcf}`

# Challenge 5

![image](https://user-images.githubusercontent.com/86275419/218981764-2fec68ab-9342-4618-95ce-e654e56049aa.png)

Trước khi làm bài này mình không biết hàm `simplexml_load_string()` là gì và mình đã lên [đây](https://www.php.net/manual/en/function.simplexml-load-string.php) tìm hiểu

+ Hàm này sẽ convert một đoạn XML thành 1 Object

Trong đoạn code xử lý thì file_get_contents() sẽ có nhiệm vụ đọc 1 file XML và đưa vào hàm simplexml_load_string() convert thành đối tượng sau đó tham chiếu tới các thẻ trong đối tượng đó để lấy dữ liệu, trong bài sẽ tham chiếu lần lượt `f->l->a->g`. Sau đó lấy dữ liệu trong thẻ `g` để lấy giá trị, nếu giá trị là `flagggggggggggggg` thì sẽ trả về Flag

### Solution

Để ý thấy hàm file_get_contents() đọc file từ dữ liệu mà ta truyền vào, do đó ta có thể sử dụng kỹ thuật RFI

Ta sẽ tạo một file XML gồm các thẻ lồng nhau lần lượt từ thẻ `f->l->a->g` trên pastebin (hoặc có thể dùng ngrok):

![image](https://user-images.githubusercontent.com/86275419/218984891-1e28d170-efd2-41c1-ac88-ea27a1dc05df.png)

Sau đó ta sẽ truyền url tới file ta vừa tạo trên pastebin để file_get_contents() đọc dữ liệu trên đó:

![image](https://user-images.githubusercontent.com/86275419/218985388-022e44ba-433c-4147-8264-5758b7a678fe.png)

### Flag: `FLAG{19ee9d17-7f23-4c03-b702-4276246ccdb2}`

# Challenge 6

![image](https://user-images.githubusercontent.com/86275419/218985540-c8d6917e-5f25-4bb7-96b9-12424e7fc8d1.png)

Mục đích của bài này là ta sẽ phải truyền lên parameter `number` giá trị mà không được chứa chữ số, nhưng khi convert sang int bằng hàm intval() thì phải trả về cho ta giá trị số != 0

### Solution

Mình đã tìm hiểu hàm intval() trên trang [này](https://www.php.net/manual/en/function.intval.php) và có ý tưởng là truyền 1 mảng lên đó, vì khi intval() convert một mảng rỗng thì sẽ trả về giá trị 0 (False), còn nếu convert mảng có phần tử thì sẽ có giá trị là 1(True)

Payload: `?number[]`

+ Vì khi ta truyền 1 mảng lên URL mặc dù không truyền phần từ thì mảng vẫn sẽ có 1 phần từ là `""`, đó đó khi qua hàm `intval()` sẽ convert thành 1 (True)

![image](https://user-images.githubusercontent.com/86275419/218987599-a5e469f8-b3c3-434a-80d8-8a970a3d9b1e.png)

### Flag: `FLAG{2352ca3b-c94e-450b-b69a-1938cab26571}`

# Challenge 7

![image](https://user-images.githubusercontent.com/86275419/218988194-883bdcbb-69b1-4c0f-a592-968e49b19d09.png)

Bài này thoạt nhìn qua thì ta thấy rằng phải truyền lên parameter `u` một mảng gồm 2 phần tử `admin` và `lord` nhưng khi lấy phần tử đầu trong mảng (admin) ra so sánh với `admin` lại không được giống nhau

### Solution 

Sau khi copy code về local để test thì mình phát hiện ra rằng không được tin những gì mình thấy trên website

![image](https://user-images.githubusercontent.com/86275419/218989400-8fc59e7d-302b-43e0-8ff0-88b6dba612e2.png)

Thật ra mình sẽ phải truyền vào 2 mảng chứ không phải 1 mảng

Việc đầu tiên mình cần làm là xem parameter đang bị lỗi định dạng có mã hex là gì (để mình có thể truyền lên URL), ở đây mình sẽ sử dụng CURL với XXD để làm việc này:

![image](https://user-images.githubusercontent.com/86275419/218990962-26711100-ce36-4f58-96c1-ce16f10453ab.png)

3 bytes bị lỗi có mã hex là: e2, 81, a0

Payload: `?u[]=&u%e2%81%a0[]=admin&u%e2%81%a0[]=lord`

![image](https://user-images.githubusercontent.com/86275419/218991934-d71a6888-d5a2-4615-bdf2-253525dd6ae3.png)

FLAG{07af425c-fc46-4689-aaf6-5512e4271f63}

### Flag: `FLAG{07af425c-fc46-4689-aaf6-5512e4271f63}`

# Challenge 8
![image](https://user-images.githubusercontent.com/86275419/218992425-f932f807-5093-488b-ae10-ce6d30e152d8.png)

Yêu cầu của bài này là ta sẽ truyền lên parameter `u` giá trị mà không được chứa `admin` trong đí (vì khi ta truyền 1 chuỗi có `admin` thì nó sẽ in ra `no no no` mà không xử lý tiếp xuống dưới)

### Solution

Để ý thấy rằng bài này có một hàm urldecode() đặt bên dưới vòng IF so sánh đâu tiên (`if(preg_match('/admin/',$_GET['u']))`), do đó ta sẽ nghĩ tới việc encode 2 lần chữ a (vì lần 1 mặc định là browser sẽ decode). 

Payload: `?u=%2561dmin`

+ %25 = %
+ %61 = a

Tại vòng IF so khớp đầu tiên, $_GET['u'] = %61dmin do đó sẽ vượt qua được hàm preg_match()
Sau khi qua hàm urldecode() thì %61dmin = admin, do đó sẽ match với vòng IF thứ hai

![image](https://user-images.githubusercontent.com/86275419/218995135-df57aaba-4611-4a97-8301-f20101ebf90f.png)

### Flag: `FLAG{0406eb88-39ce-4dcc-bace-b058a7e57dd0}`

# Challenge 9

![image](https://user-images.githubusercontent.com/86275419/218997568-a908fd11-c513-4b6a-8018-3b6499aff7ac.png)

Vơi bài này ta sẽ phải truyền vào một chuỗi `give_me_the_flag` lên parameter `say` sao cho có thể bypass được hàm  preg_replace() bên trên

`preg_replace('/^(.*)flag(.*)$/', 'waf', $_GET['say'])`:
+ Hàm này sẽ kiểm tra nếu trong chuỗi ta truyền vào mà match với regex ở vị trí đầu thì sẽ thay thế cả chuỗi ta truyền vài thành waf

### Solution

Mình sẽ sử dụng [regex 101](https://regex101.com/) để nghiên cứu chuỗi regex

![image](https://user-images.githubusercontent.com/86275419/218999076-cdbb1dd2-58e6-44bc-b721-79c8d0bf38fb.png)

Sau một hồi đọc kết quả mà regex 101 đưa ra thì mình thấy rằng dấu `.` không bao gồm kí tự xuống dòng `\n` do đó sau một hồi test mình đã thử thêm ký tự %0a(\n) lên url

![image](https://user-images.githubusercontent.com/86275419/218999809-130aba4f-6a6a-4b56-bc2c-7a632f05918c.png)

Payload: `?say=%0agive_me_the_flag`

### Flag: `FLAG{62e0d117-93af-4e36-957c-3841d1ae7100}`
