
1. [Cấu hình metadata](#confiMetadata)
2. Phần 2
    2.1 [Khởi tạo Container](#instantiatingContainer)
    2.2 [Soạn siêu dữ liệu cấu hình dựa trên XML](#compos)
    2.3 [DSL định nghĩa Groovy Bean](#confGroovy)
3. [Sử dụng Container](#usingContainer)
# Container Overview

Interface `org.springframework.context.ApplicationContext` đại diện cho Spring IoC container và chịu trách nhiệm cho việc khởi tạo, cấu hình và tập hợp các `Bean`. Container lấy những chỉ dẫn về những đối tượng mà nó khởi tạo, cấu hình, và tập hợp thông qua việc đọc cấu hình `metadata`. Việc cấu hình `metadata` có thể được viết bằng XML, Java annotations, Java code. `metadata` cho phép bạn hiểu được các `Object` hợp thành ứng dụng và sự liên kết giữa những `Object` đó.

Một vài `implementations` (tái định nghĩa, hoặc mở rộng interface)  của interface `AppliacationContext` được cung cấp sẵn bởi `*Spring*`. Trong các ứng dụng độc lập, người ta thường tạo 1 `instance` (thể hiện cụ thể của class) của `ClassPathXmlApplicationContext` hoặc `FileSystemXmlApplicationContext`. Trong khi XML được xem như một định dạng truyền thống để định nghĩa các cấu hình trong `metadata`, ngoài ra có thể sử dụng thêm `Java annotations` hoặc `Java code` như là một định dạng của `metadata` bằng cách thực hiện một số cài đặt nhỏ bằng XML trong `metadata` để cho phép hỗ trợ thêm các định dạng trên.

Trong hầu hết các kịch bản ứng dụng, người dùng rõ ràng không cần phải khải tạo 1 hoặc nhiều `instances` của Spring IoC container. Ví dụ như, trong một kịch bản ứng dụng web, chỉ cần một số dòng code mẫu XML được mô tả trong `web.xml` cũng có thể đủ để chương trình hoạt động ([Convenient ApplicationContext Instantiation for Web Applications](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#context-create))

Sơ đồ sau đây cho thấy 1 cái tổng quát về cách hoạt động của Spring. Các `classes` của ứng dụng được kết hợp với các cấu hình của `metadata`, để sau khi `ApplicationContext` được tạo và khởi tạo, chúng ta có một ứng dụng được cấu hình đầu đủ và có khả năng hoạt động được.

![The Spring IoC container](../../imgs/container-magic.png)

The Spring IoC container

## 1. Cấu hình Metadata <a name="confiMetadata"></a>

Theo truyền thống, `metadata` được cấu bình bằng XML (được sử dụng trong phần hướng dẫn này) để diễn tả các khái niệm và tính năng của Spring IoC container.

Để biết thêm thông tin về việc sử dụng các định dạng khác:

 * [Annotation-base configurationg](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-annotation-config): Spring 2.5 giới thiệu việc cho phép cấu hình `metadata` dự trên các `annotation`.
 * [Java-based Configuration](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-java): Bắt đầu với Spring 3.0, chúng ta có thể định nghĩa các `bean` bên ngoài cho các `class` của ứng dụng bằng code Java thay cho XML. Để sử dụng các tính năng mới, xem thêm [@Configuration](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html), [@Bean](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Bean.html), [@Import](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Import.html) và [@DependOn](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/DependsOn.html)

 Cấu hình Spring bao gồm ít nhất một và thường nhiều hơn một `bean` mà `container` phải quản lý. `Metadata` được cấu hình bằng định dạng XML thì các `bean` được định nghĩa bằng tag `<bean />` trong tag `<beans />`. Còn đối với bằng cấu hình bằng Java code sử dụng `@Bean` annotation trên các phương thức nằm trong các `class` có `@Configuration` annotation.

 Các định nghĩa `bean` tương ứng với các đối tượng thực tế tạo nên ứng dụng. Thông thường, chúng ta định nghĩa `service layer objects`, đối tượng truy cập dữ liệu (Data access Ojects: DAOs), các đối tượng hạ tầng như `Hibernate`, `SessionFactories`v.v... Chúng ta thường không cấu hình `fine-granied domain object`(các đối tượng domain được chia nhỏ) trong `container`, bởi vì nó thường là nhiệm vụ của DAOs và business logic để tạo và tải các `domain object`. Tuy nhiên, chúng ta có thể sử dụng tích hợp AspectsJ với Spring để cấu hình các đối tượng đã được tạo bên ngoài sự quản lý, điều khiển của IoC container. Xem thêm [Using AspectJ to dependency-inject domain objects with Spring](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#aop-atconfigurable)

 Bên dưới đây là ví dụ thể hiện cấu trúc cơ bản của cấu hình `metadata` theo định dạng XML.
 ```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="..." class="...">  
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <bean id="..." class="...">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions go here -->

</beans>
 ```

 1. Thuộc tính `id` là một chuỗi để định danh các `bean` tự định nghĩa.
 2. Thuộc tính `class` định nghĩa loại của các `bean` và sử dụng `classname` đủ điều kiện (là classname chỉ định rõ đối tượng, package của đối tượng đó)

 Giá trị của thuộc tính `id` đề cập đến các `collaborating objects`(đối tượng cộng tác). XML để tham chiếu đến các `collaborating objects` không được hiển thị trong ví dụ này. Xem thêm [Dependencies](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-dependencies).

## 2.
## 2.1. Khởi tạo container <a name="instantiatingContainer"></a>

Đường dẫn vị trí hoặc đường dẫn được cung cấp cho hàm dựng của `ApplicationContext` là chuỗi tài nguyên (resource string) cho phép `container` tải các cấu hình của `metadata` từ nhiều nguồn như hệ thống tập tin cục bộ, Java `CLASSPATH` và nhiều nơi khác.

```Java
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
```

> Lớp trừu tượng [Resource](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#resources) cung cấp một cơ chế thuận tiện để đọc `InputStream` từ các vị trí được xác định trong cú pháp URI. Cụ thể, các đường dẫn `Resource` được sử dụng để xây dựng `applications contexts`, được mô tả ở [Application Contexts and Resource Paths](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#resources-app-ctx)

Ví dụ theo sau thể hiện file cấu hình `(services.xml)` của `service layer objects`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- services -->

    <bean id="petStore" class="org.springframework.samples.jpetstore.services.PetStoreServiceImpl">
        <property name="accountDao" ref="accountDao"/>
        <property name="itemDao" ref="itemDao"/>
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions for services go here -->

</beans>
```

Ví dụ bên dưới thể hiện `daos.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="accountDao"
        class="org.springframework.samples.jpetstore.dao.jpa.JpaAccountDao">
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <bean id="itemDao" class="org.springframework.samples.jpetstore.dao.jpa.JpaItemDao">
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions for data access objects go here -->

</beans>
```

Ở ví dụ trước, `service layer` bao gồm lớp `PetStoreServiceImpl` và 2 `data access object` của kiểu `JpaAccountDao` và `JpaItemDao` (dựa trên JPA Object-Relationl Mapping standard).Phần tử `property` `name` tương quan đến thuộc tính `name` của `JavaBean` và phần tử `ref` tương quan đến tên của một định nghĩa `bean` khác. Sự liên kết giữa các phần tử `id` và `ref` thể hiện sự phụ thuộc giữa các đối tượng cộng tác. Chi tiết về cấu hình cho các [object's dependencies](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-dependencies).

### 2.2. Soạn siêu dữ liệu cấu hình dựa trên XML<a name="compos"></a>
Có thể hữu ích khi có các định nghĩa bean trải rộng trên nhiều tệp XML. Thông thường, mỗi tệp cấu hình XML riêng lẻ đại diện cho một lớp hoặc module logic trong kiến trúc của bạn.

Bạn có thể sử dụng hàm tạo ngữ cảnh ứng dụng *(the application context contructor)* để tải các định nghĩa bean từ tất cả các đoạn XML này. Hàm tạo này lấy nhiều vị trí tài nguyên *(`Resource` locations)*, như đã được chỉ ra trong [phần trước](#instantiatingContainer).
Hoặc, sử dụng một hoặc nhiều lần <import /> để tải định nghĩa bean từ tệp hoặc tệp khác, như sau:
```xml
<beans>
    <import resource="services.xml"/>
    <import resource="resources/messageSource.xml"/>
    <import resource="/resources/themeSource.xml"/>

    <bean id="bean1" class="..."/>
    <bean id="bean2" class="..."/>
</beans>
```
Trong ví dụ trên, bean được định nghĩa bên ngoài và được load từ 3 files: `services.xml`, `messageSource.xml` và `themeSource.xml`. Tất cả các đường dẫn vị trí đều liên quan đến tệp định nghĩa khi nhập nên `services.xml` phải nằm trong cùng thư mục hoặc đường dẫn với tệp đang nhập, trong khi `messageSource.xml` và `themeSource.xml` phải nằm trong thư mục resources - cùng thư mục với tệp đang nhập. 
Nội dung của các tệp đang được nhập, bao gồm `phần tử <bean /> cấp cao nhất`, phải là các định nghĩa bean XML hợp lệ, theo Lược đồ Spring.
***Chú ý***: có `/` trước `resources` hay `không có` đều không khác nhau, tuy nhiên tốt nhất là không nên dùng.  

>Có thể, nhưng không nên, để tham chiếu các tệp trong thư mục mẹ bằng cách sử dụng đường dẫn tương đối, ví dụ: `"../"`. Làm như vậy sẽ tạo ra sự phụ thuộc vào một tệp nằm ngoài ứng dụng hiện tại. Cụ thể, tài liệu tham khảo này không được khuyến nghị cho `classpath:` URL (ví dụ: `classpath: ../ services.xml`), trong đó quy trình giải quyết thời gian chạy chọn gốc đường dẫn lớp class gần nhất và sau đó nhìn vào thư mục mẹ của nó. Thay đổi cấu hình classpath có thể dẫn đến việc lựa chọn một thư mục không như ý.

>Bạn luôn có thể sử dụng các đường dẫn fully qualified thay vì các đường dẫn tương đối : ví dụ: `file: C: /config/service.xml` hoặc `classpath: /config/service.xml`. Tuy nhiên, lưu ý rằng bạn đang ghép cấu hình ứng dụng của bạn với các vị trí tuyệt đối cụ thể. Nói chung, tốt hơn là giữ một hướng dẫn cho các vị trí tuyệt đối như vậy - ví dụ: thông qua các trình giữ chỗ "$ {...}" được giải quyết theo các thuộc tính hệ thống JVM khi chạy.

### 2.3. DSL định nghĩa Groovy Bean <a name="confGroovy">
Là một ví dụ khác cho configuration metadata bên ngoài, các định nghĩa bean cũng có thể được thể hiện trong Spring’s Groovy Bean Definition DSL, như được biết từ Grails framework. Thông thường, cấu hình như vậy nằm trong tệp ".groovy" với cấu trúc được hiển thị trong ví dụ sau:
```groovy
beans {
    dataSource(BasicDataSource) {
        driverClassName = "org.hsqldb.jdbcDriver"
        url = "jdbc:hsqldb:mem:grailsDB"
        username = "sa"
        password = ""
        settings = [mynew:"setting"]
    }
    sessionFactory(SessionFactory) {
        dataSource = dataSource
    }
    myService(MyService) {
        nestedBean = { AnotherBean bean ->
            dataSource = dataSource
        }
    }
}
```
Kiểu cấu hình này phần lớn tương đương định nghĩa bean bằng XML, thậm chí còn hỗ trợ Spring’s XML configuration namespaces. Nó cũng cho phép nhập các tệp XML định nghĩa bean thông qua lệnh `importBeans`.

## 3. Sử dụng Container <a name="usingContainer">
`ApplicationContext` là giao diện cho advanced factory có khả năng duy trì sổ đăng ký của các bean khác nhau và các phụ thuộc của chúng. Bằng các sử dụng phương thức `T getBean(String name, Class<T> requiredType)`, có thể lấy lại các thể hiện - (retrieve instance) của bean.

`ApplicationContext` cho phép đọc và truy cập các định nghĩa bean, như ví dụ sau:
```java
// create and configure beans
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");

// retrieve configured instance
PetStoreService service = context.getBean("petStore", PetStoreService.class);

// use configured instance
List<String> userList = service.getUsernameList();
```

với cấu hình bằng file groovy, bootstrapping<sup>1</sup> trông rất giống. Nó có một lớp triển khai ngữ cảnh khác nhau, nhận biết Groovy (nhưng cũng hiểu các định nghĩa bean XML). Ví sụ sau đây cho thấy cấu hình Groovy:
```java
ApplicationContext context = new GenericGroovyApplicationContext("services.groovy", "daos.groovy");
```
Biến thể linh hoạt nhất là `GenericApplicationContext` kết hợp với các reader delegates - ví dụ với `XmlBeanDefinitionReader` cho XML file, như ví dụ sau:
```java
GenericApplicationContext context = new GenericApplicationContext();
new XmlBeanDefinitionReader(context).loadBeanDefinitions("services.xml", "daos.xml");
context.refresh();
``` 
Cũng có thể dùng `GroovyBeanDefinitionReader` cho Groovy file, như ví dụ sau:
```java
GenericApplicationContext context = new GenericApplicationContext();
new GroovyBeanDefinitionReader(context).loadBeanDefinitions("services.groovy", "daos.groovy");
context.refresh();
```
Bạn có thể pha trộn và kết hợp các reader delegates như vậy trên cùng `ApplicationContext`, dọc dịnh nghĩa bean từ các nguồn đa dạng.

Bạn có thề sử dụng `getBean` để lấy trường hợp của các bean. Interface `ApplicationContext` có một vài phương thức để retrieving bean, nhưng tốt nhất không nên dùng chúng.
Thật vậy, mã ứng dụng của bạn hoàn toàn không có lệnh gọi phương thức getBean() và do đó hoàn toàn không phụ thuộc vào API Spring. Ví dụ, tích hợp Spring với các khung web cung cấp nội dung phụ thuộc cho các thành phần khung web khác nhau như: controllers and JSF-managed beans, cho phép bạn khai báo một phụ thuộc vào một bean cụ thể thông qua metadata 
(như chú thích tự động(autowiring annotation)).

---------------------------
<sup>1</sup>: Xem trên bài viết Wikipedia về [bootstrapping](https://en.wikipedia.org/wiki/Bootstrapping) .
Có một phần và các liên kết giải thích ý nghĩa của nó trong Máy tính . Nó có bốn cách sử dụng khác nhau trong lĩnh vực này.
Dưới đây là một số trích dẫn, nhưng để giải thích sâu hơn và ý nghĩa thay thế, hãy tham khảo các liên kết ở trên.
>"... là một kỹ thuật trong đó một chương trình máy tính đơn giản kích hoạt một hệ thống chương trình phức tạp hơn."
"Một cách sử dụng khác của thuật ngữ bootstrapping là sử dụng trình biên dịch để tự biên dịch, bằng cách trước tiên viết một phần nhỏ của trình biên dịch ngôn ngữ lập trình mới bằng ngôn ngữ hiện có để biên dịch thêm các chương trình của trình biên dịch mới được viết bằng ngôn ngữ mới."