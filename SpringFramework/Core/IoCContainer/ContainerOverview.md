# Container Overview

Interface `org.springframework.context.ApplicationContext` đại diện cho Spring IoC container và chịu trách nhiệm cho việc khởi tạo, cấu hình và tập hợp các `Bean`. Container lấy những chỉ dẫn về những đối tượng mà nó khởi tạo, cấu hình, và tập hợp thông qua việc đọc cấu hình `metadata`. Việc cấu hình `metadata` có thể được viết bằng XML, Java annotations, Java code. `metadata` cho phép bạn hiểu được các `Object` hợp thành ứng dụng và sự liên kết giữa những `Object` đó.

Một vài `implementations` (tái định nghĩa, hoặc mở rộng interface)  của interface `AppliacationContext` được cung cấp sẵn bởi `*Spring*`. Trong các ứng dụng độc lập, người ta thường tạo 1 `instance` (thể hiện cụ thể của class) của `ClassPathXmlApplicationContext` hoặc `FileSystemXmlApplicationContext`. Trong khi XML được xem như một định dạng truyền thống để định nghĩa các cấu hình trong `metadata`, ngoài ra có thể sử dụng thêm `Java annotations` hoặc `Java code` như là một định dạng của `metadata` bằng cách thực hiện một số cài đặt nhỏ bằng XML trong `metadata` để cho phép hỗ trợ thêm các định dạng trên.

Trong hầu hết các kịch bản ứng dụng, người dùng rõ ràng không cần phải khải tạo 1 hoặc nhiều `instances` của Spring IoC container. Ví dụ như, trong một kịch bản ứng dụng web, chỉ cần một số dòng code mẫu XML được mô tả trong `web.xml` cũng có thể đủ để chương trình hoạt động ([Convenient ApplicationContext Instantiation for Web Applications](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#context-create))

Sơ đồ sau đây cho thấy 1 cái tổng quát về cách hoạt động của Spring. Các `classes` của ứng dụng được kết hợp với các cấu hình của `metadata`, để sau khi `ApplicationContext` được tạo và khởi tạo, chúng ta có một ứng dụng được cấu hình đầu đủ và có khả năng hoạt động được.

![The Spring IoC container](../../imgs/container-magic.png)

The Spring IoC container

## 1. Cấu hình Metadata

Theo truyền thống, `metadata` được cấu bình bằng XML (được sử dụng trong phần hướng dẫn này) để diễn tả các khái niệm và tính năng của Spring IoC container.

Để biết thêm thông tin về việc sử dụng các định dạng khác:

 * [Annotation-base configurationg](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-annotation-config): Spring 2.5 giới thiệu việc cho phép cấu hình `metadata` dự trên các `annotation`.
 * [Java-based Configuration](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-java): Bắt đầu với Spring 3.0, chúng ta có thể định nghĩa các `bean` bên ngoài cho các `class` của ứng dụng bằng code Java thay cho XML. Để sử dụng các tính năng mới, xem thêm [@Configuration](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html), [@Bean](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Bean.html), [@Import](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Import.html) và [@DependOn](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/DependsOn.html)

 Cấu hình Spring bao gồm ít nhất một và thường nhiều hơn một `bean` mà `container` phải quản lý. `Metadata` được cấu hình bằng định dạng XML thì các `bean` được định nghĩa bằng tag `<bean />` trong tag `<beans />`. Còn đối với bằng cấu hình bằng Java code sử dụng `@Bean` annotation trên các phương thức nằm trong các `class` có `@Configuration` annotation.

 Các định nghĩa `bean` tương ứng với các đố tượng thực tế tạo nên ứng dụng. Thông thường, chúng ta định nghĩa `service layer objects`, đối tượng truy cập dữ liệu (Data access Ojects: DAOs), các đối tượng hạ tầng như `Hibernate`, `SessionFactories`v.v... Chúng ta thường không cấu hình `fine-granied domain object`(các đối tượng domain được chia nhỏ) trong `container`, bởi vì nó thường là nhiệm vụ của DAOs và business logic để tạo và tải các `domain object`. Tuy nhiên, chúng ta có thể sử dụng tích hợp AspectsJ với Spring để cấu hình các đối tượng đã được tạo bên ngoài sự quản lý, điều khiển của IoC container. Xem thêm [Using AspectJ to dependency-inject domain objects with Spring](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#aop-atconfigurable)

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

## Khởi tạo container

Đường dẫn vị trí hoặc đường dẫn được cung cấp cho hàm dựng của `ApplicationContext` là chuỗi tài nguyên (resource string) cho phép `container` tải các cấu hình của `metadata` từ nhiều nguồn như hệ thống tập tin cục bộ, Java `CLASSPATH` va2 nhiều nơi khác.

```Java
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
```

> Lớp trừa tượng `[Resource](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#resources)` cung cấp một cơ chế thuận tiện để đọc `InputStream` từ các vị trí được xác định tong cú pháp URI. Cụ thể, các đường dẫn `Resource` được sử dụng để xây dựng `applications contexts`, [được mô tả ở Application Contexts and Resource Paths](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#resources-app-ctx)

Ví dụ theo sau thể hiện file cấu hình `(services.xml)` của `service layer objects`

```java
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

```java
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

Ở ví dụ trước, `service layer` bao gồm lớp `PetStoreServiceImpl` và 2 `data access object` của kiểu `JpaAccountDao` và `JpaItemDao` (dựa trên JPA Object-Relationl Mapping standard).Phần tử `property` `name` tương quan đến thuộc tính `name` của `JavaBean` và phần tử `ref` tương quan đến tên của một định nghĩa `bean` khác. Sự liên kết giữa các phần tử `id` và `ref` thể hiện sự phụ thuộc giữa các đối tượng cộng tác. Chi tiết về cấu hình cho các `[object's dependencies](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-dependencies)`