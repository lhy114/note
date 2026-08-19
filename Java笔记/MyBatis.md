## 定义
![[Pasted image 20260812162705.png]]![[Pasted image 20260812164813.png]]

```
@Mapper //在运行的时候，会自动生成这个接口的实现类对象（通过代理的方式），然后将这个对象交给IOC容器进行处理  
public interface UserMapper {  
  
    @Select("SELECT * from user")  
    public List<User> list();  
}
```

![[Pasted image 20260812172931.png]]
## 基础用法

![[Pasted image 20260812201326.png]]

![[Pasted image 20260812201623.png]]![[Pasted image 20260812202218.png]]![[Pasted image 20260812202319.png]]![[Pasted image 20260812202534.png]]![[Pasted image 20260812202619.png]]![[Pasted image 20260812202918.png]]![[Pasted image 20260812203129.png]]

## XML文件
![[Pasted image 20260814102448.png]]

## 动态SQL
![[Pasted image 20260814104103.png]]
### if
![[Pasted image 20260814110414.png]]
![[Pasted image 20260814112447.png]]

### foreach
![[Pasted image 20260814113400.png]]
## sql&include
![[Pasted image 20260814113812.png]]


# MyBatis Plus
