![[Pasted image 20260812162705.png]]![[Pasted image 20260812164813.png]]

```
@Mapper //在运行的时候，会自动生成这个接口的实现类对象（通过代理的方式），然后将这个对象交给IOC容器进行处理  
public interface UserMapper {  
  
    @Select("SELECT * from user")  
    public List<User> list();  
}
```

![[Pasted image 20260812172931.png]]![[Pasted image 20260812201326.png]]![[Pasted image 20260812201623.png]]![[Pasted image 20260812202218.png]]![[Pasted image 20260812202319.png]]![[Pasted image 20260812202534.png]]![[Pasted image 20260812202619.png]]![[Pasted image 20260812202918.png]]![[Pasted image 20260812203129.png]]