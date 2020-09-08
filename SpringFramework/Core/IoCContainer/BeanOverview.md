# Bean Overview
Một Spring IoC container quản lí một hoặc nhiều bean. Các bean được tạo ra bằng cấu hình metadata, cái mà bạn cung cấp cho container (ví dụ như định nghĩa `<bean/>` trong form XML).

Trong chính container, các bean được biểu diễn dưới dạng đối tượng `BeanDefinition`, trong các metadata sau:
- A package-qualified class name: thông thường lớp thực hiện thực tế của bean được định nghĩa.
- Bean yếu tố cấu hình hành vi *(Bean behavioral configuration elements)*, cách hoạt động của bean trong container (scope, lifecycle callbacks, và vân vân).
- Tham chiếu đến bean khác, cái cần thiết để nó thực hiện công việc của nó. Các tham chiếu cũng được gọi là collaborators hoặc defendencies.
- Các cài đặt cấu hình khác để đặt trong đối tượng vừa tạo. Ví dụ, giới hạn kích thước của nhóm hoặc số lượng kết nối để sử dụng trong 1 bean quản lý nhóm kết nối.

Metadata này chuyển thành một tập các thuộc tính tạo nên mỗi định nghĩa bean. Các thuộc tính được mô tả trong bảng sau:

Thuộc tính | Giải thích
-|-
Class | [Khởi tạo bean](#iniBean)
Name | [Tên của bean](#namBean)
Scope | [Phạm vi bean](#scoBean)
Constructor arguments | [Tiêm phụ thuộc (DI)]()
Properties | [Tiêm phụ thuộc (DI)]()
Autowiring mode | [Cộng tác viên tự động *(Autowiring Collaborators)*]()
Lazy initialization mode | [Lazy-initialized Beans]()
Initialization method | [Initialization Callbacks]()
Destruction method | [Destruction Callbacks]()

Ngoài việc dùng các định nghĩa bean có chứa thông tin để tạo 1 bean cụ thể, người dùng có thể tạo bean bên ngoài container bằng cách sử dụng `ApplicationContext`. Điều này được thực hiện bằng cách truy cập `ApplicationContext từ BeanFactory` thông qua phương thức `getBeanFactory()`. Phương thức này trả về BeanFactory `DefaultListableBeanFactory` implementation. `DefaultListableBeanFactory` hỗ trợ việc đăng kí này thông qua 2 phương thức là `registerSingleton(..)` và `registerBeanDefinition(..)`. Tuy nhiên các ứng dụng thông thường đều sử dụng các bean được đăng ký thông qua metadata.

> Bean metadata và các phiên bản singleton được cung cấp thủ công cần phải được đăng ký càng sớm càng tốt để container hoạt động chính xác trong quá trình tự động và các bước hướng nội khác. Trong khi overriding existing metadata và existing singleton chỉ hỗ trợ ở một mức độ nào đó, thì việc đăng ký bean mới trong thời gian chạy không được hỗ trợ chính thức và có thể dẫn đến các ngoại lệ truy cập đồng thời, trạng thái không nhất quán trong container hoặc cả 2.

## Đặt tên bean <a name="namBean"></a>
Mỗi bean đều có 1 hoặc nhiều định danh. Các định danh này phải là duy nhất trong host container. Mỗi bean thưởng chỉ có 1 định danh. Tuy nhiên, nếu nó cần nhiều hơn 1 định danh thì những cái thêm được coi là bí danh.

Trong cấu hình metadata bằng XML, ta dùng thuộc tính `id` và `name` để định danh cho bean. Thuộc tính `id` cho phép chỉ định chính xác 1 id. Thông thường `id` chứa các kí tự và số ('myBean', 'someService', 'yen',...) nhưng cũng có thể sử dụng các kí tự đặt biệt để đặt tên bean. Nếu muốn tạo thêm bí danh cho bean, ta dùng thuộc tính `name`. Cách nhau bằng `,`, `.` hoặc khoảng trắng.

Có thể không đặt `id` hoặc `name` cho bean. Nếu bạn không tạo `id` hoặc `name`, thì container sẽ tự tạo 1 tên duy nhất cho bean đó. Tuy nhiên, nếu muốn tham chiếu tới bean nào đó bằng cách sử dụng `ref` element hoặc tra cứu kiểu `Service Locator`, thì bean đó cần phải được cung cấp `id` hoặc `name`. Các bean không được cung cấp tên thường liên quan đến việc sử dụng [inner bean]() và [autowiring collaborator](). 

**Quy ước đặt tên bean**|
-|
Tên bean bắt dầu bằng chữ cái viết thường và kí tự đều tiên viết in hoa cho chữ cái tiếp theo. Ví dụ như, `accountManager`, `loginController`, `anhDuy`,... .|

> Với việc quét các thành phần trong classpath, Spring tạo tên cho những thành phần không có tên theo quy ước trên. Bản chất của nó là lấy tên lớp simple và biến kí tự ban đầu của nó thành chữ thường. Trong trường hợp đặc biệt, khi có nhiều hơn 1 kí tự viết in hoa thì tên ban đầu được giữ nguyên. Đây là các quy tắc tương tự như được định nghĩa bởi `java.beans.Introspector.decapitalize` (mà Spring sử dụng ở đây). 

### Đặt bí danh bean bên ngoài định nghĩa bean <a name="aliBean"></a>



