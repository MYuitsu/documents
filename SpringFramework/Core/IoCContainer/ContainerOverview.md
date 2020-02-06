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

 Các định nghĩa `bean` tương ứng với các đố tượng thực tế tạo nên ứng dụng. Thông thường, chúng ta định nghĩa `service layer objects`, đối tượng truy cập dữ liệu (Data access Ojects: DAOs), các đối tượng hạ tầng như `Hibernate`, `SessionFactories`v.v... Chúng ta thường không cấu hình `fine-granied domain object`(các đối tượng domain được chia nhỏ) trong `container`, bởi vì nó thường là nhiệm vụ của DAOs và business logic để tạo và tải các `domain object`.