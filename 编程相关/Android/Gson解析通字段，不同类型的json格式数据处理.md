[TOC]
###定义一个JsonDeserializer<T>
```
public class BeanDeserializer implements JsonDeserializer<Bean>{
 public Bean deserialize(JsonElement jsonElement , Typetype,JsonDeserializationContext context)throws JsonParseException{
    //这里jsonElement就是你当前bean的json格式, 获取相应的字段,判断即可操作
    //例如 我这里判断是否是数组还是对象, 如果是数组就弄为空, 其他正常赋值
    if(jsonelement.isJsonArray()){
        bean = null;
    }else {
       bean = new Gson().fromJson(jsonElement, Bean.class);                             
          return bean;
   }    
}
```
###解析当前对象时

```        
//使用当前构建类注册
 Gson gson =  new GsonBuilder().registerTypeadapter(Bean.class,new BeanDeserializer()).create();
//接下来该干嘛干嘛...
```