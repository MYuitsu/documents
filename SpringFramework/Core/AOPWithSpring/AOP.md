# Aspect Oriented Programming (AOP)

1. Đặt vấn đề 

Khi thực hiện một chức năng trong phần mềm ngoài chức năng chính ra thì còn nhiều phần phụ như viết log, quản lý transaction, xử lý exception v.v... Trên thực tế có rất nhiều hàm chức năng mà lập trình viên phải thực hiện lập đi lập lại các công việc phụ đó, làm cho code liên kết quá chặt vào nhau, duplicate, rất khó sửa đổi hay thêm mới logic.

```java
Logger logger = Logger.getLogger(...);
TransactionManager tm = getTransactionManager();
public void addAccount(Account account) {
    logger.info("Creating (" + account + ") Account");
    try {
        tm.beginTransaction();
        db.add(account);
        tm.commit();
    } catch (Exception) {
        tm.rollback();
        logger.error("Account creation failed");
    }
}
```
![](../imgs/aop-flow-addAccount-example.png)

2. Khái niệm của AOP

Là kỹ thuật lập trình nhằm phân tách chương trình thành các moudule riêng rẽ, phân biệt, không phụ thuộc nhau. Nhưng khi hoạt động sẽ kết hợp các moudule lại với nhau để thực hiện các chức năng. 

Khi sửa chữa chỉ cần điều chỉnh 1 moudule cụ thể.





(Tham khảo)[https://gpcoder.com/5112-gioi-thieu-aspect-oriented-programming-aop/]