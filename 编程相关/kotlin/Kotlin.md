

[TOC]


#### 匿名object

- 匿名object只可以用在本地和私有作用域中声明的类型

```
val o = object {
    val count =0;
    val sum = 0;
}

//函数内使用
fun test(){
    
    val o = object {
        val count =50;
        val sum =0;
    }
    
    for(i in 0..o.count){
        o.sum += i;
    }
    
}


private fun test(val v1,val v2){
    
    return object{
        var x =v1;
        var y = v2;
    }
}

```
#### 密封类

```

sealed class Expression
class Unit :Expression()
data class Const(val number:Double):Expression()
data class Sum(val el:Expression,val e2:Expression):Expression()
data class Multiply(val el:Expression,val e2:Expression):Expression()
object NaN :Expression()



fun eval(expr : Expression):Double = when(expr){
    
    is Unit -> 1.0
    is Const -> expr.number
    is Sum -> eval(expr.e1) + eval(expr.e2)
    is Myltiply -> eval(expr.el) * eval(expr.e2)
    NaN -> Double.NaN
}
```
#### 数据类解构
```
val u = User("name",100)
val (name ,age) = u;

```