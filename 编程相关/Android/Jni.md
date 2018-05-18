

[TOC]


### JNIEnv类型和jobject类型

#### JNIEnv类型
---
JNIEnv类型实际上代表java环境，通过JNIEnv*指针就可以对java端的代码进行操作。
JNIEnv类中的常用函数
- NewObject:创建java类中的对象。
- NewString :创建java类中的String对象
- New<Type>Array:创建类型为type的数组对象
- Get<Type>Field:获取类型为type的字段
- set<Type>Field:设置类型为type的字段值
- GetStatic<Type>Field:获取类型为type的静态字段
- SetStatic<Type>Field:设置类型为type的静态字段
- Call<Type>Method:调用返回类型为type的方法
- CallStatic<Type>Method:调用返回类型为type的静态方法
-

#### jobject参数 obj
---
如果native方法不是static，obj就代表native方法的类实例
如果native方法是static，obj就代表native方法的类的class对象实例



#### java类型和native中类型映射关系
---
|java类型|本地类型 | jni定义的别名|
|--|---|----|
| int | long | jint/jsize|
long |_int64 | jlong
byte | signed char | jbyte
boolean | unsigned char | jboolean 
char | unsigined short | jchar 
short | short | jshort
float |　float | jfloat|
double  | double | jdouble
Object | _jobject* | jobject


#### jclass 类型
---
jclass类型表示java中的Class类

- jclass FindClass(const char* clsName) : 通过类的名称获取jclass。（jclass str = env->FindClass("java/lang/String")
- jclass GetObjectClass（jobject obj):通过对象实例来获取jclass，相当于java中的getClass方法
- jclass GetSuperClass（jclass obj)：通过jclass获取父类的jclass对象

#### native中访问java层代码
---
本地代码中访问java代码时，需要取得java属性/方法的jfieldID/jmethodID才能操作。

GetFieldID(jclass clazz,const char* name ,const char* sign)

GetMethodID(jclass clazz,const char* name,const char* sign)

**签名格式对照表**
| 类型 | 相应的签名| 
|---|--------------|
boolean | Z 
byte | B
char | C
short | S
int | I 
long | J
float | F
double | D
void | V
object | L用/分割包完整类名（Ljava/lang/String)
Array | [签名 [I [Ljava/lang/String
Method | (参数类型签名...)返回值类型签名


### JNIEnv类型中方法的使用
---
```
public class Hello{
    public int property;
    public int function(int foo,Date date , int[] arr){
        System.out.println("function");
        return 0
    }
    
    public native void test();
    
}

//传入的类型是jobject 表示不是静态方法
JNIEXPORT void Java_Hello_test(JNIEnv* env,jobject obj){
    
    //通过jobject获取jclass
    jclass hello_clazzz = env ->GetObjectClass(obj);
    //获取jclass里 签名I 属性名 property的字段
    jfieldId fieldId_prop = env->GetFieldId(hello_clazz,"property","I");
    // hello_clazz中方法名为function 参数为int data int[] 返回值为int的方法
    jmethodId methodId_func = env->GetMethodId(hello_clazz,"function","(ILJava/util/Data;[I)I")
    
    env->CallIntMethod(obj,methodId_func,0L,null,null)
    
}


```

调用示例方法的三种形式：
```
Call<Type>Mehtod(jobject obj,jmethodId id,....)

//java中的字符是Unicode双字节，c++是单字节，需要加L
env -> CallBooleanMethod(obj,id_function,10L,3.4,L'a') 

Call<Type>Method(jobject obj,jmethodId id,va_list lst) //这种不常使用



Call<Type>Method(jobject obj,jmethodId id,jvalue* v)
//调用示例
jvalue* args = new jvalue[3];
args[0].i =10L  
args[1].d = 3.44
args[2].c = L'a'

env - CallBooleanMethod(obj,id_function,args)


```

- c++中调用java中的方法

```
//java 代码
public double max(double d,double d2){
    return d>d2 ? d :d2;
}

//jni  方法签名格式 ：(参数类型签名...)返回值类型签名

JNIEXPORT void JNICALL Java_com_jni_demo_JNIDemo_sayHello(JNIEnv* env,jobject obj){
    jclass clazz = env->GetObjectClass(obj); //获取class对象
    jmethodID id_max = env->GetMethodID(clazz,"max","(DD)D");//获取方法id
    //调用方法
    jdouble value = env->CallDoubleMethod(obj,id_max,1.2,1.1);
    count<<value<<endl;
}


```


#### java和c++中的动态机制

```
class Facther{
    public:
        void function(){
            count<<"father.func"<<endl;
        }
}
class Child:public Facther{
    public:
        void function(){
            count<<"child.func"<<endl;
        }
}

Facther* p = new Childe()；
p->function();

```
上面代码执行的是父类中的function，如果想执行子类的function方法，需要将父类的function定义成virtual虚函数（virtual void function() )


**java中所有方法都是虚拟的，所以总是调用子类的方法，因此CallNonVirtual<Type>Mehtod这个方法可以帮助调用父类的方法**


- 示例代码
```
pbulic class Father{
    public void function(){
        System.out.println("facther:function")
    }
}

public class Child extends Father{
    
    public void function(){
        System.out.println("child:function")
    }
}

public void main(String[] args){
    public Facther father = new Child();
    
    System.loadLibrary("JNIDemo");
    JNIDemo jniDemo = new JNIDemo();
    jniDemo.function()
}

// jni代码
#include<iostream.h>
#include "com_jni_JNIDemo.h"

JNIEXPORT void JNICALL Java_com_jni_JNIDemo_function(JNIEnv* env,jobject obj){
    
    // 获取class
    jclass clazz = env->GetObjectClass(obj);
    //获取字段id
    jfieldId id_father = env-GetObjectFieldID(obj,"fachter","Lcom/jni/demo/Father");
    //获取方法id
    jmethodID id_father_function = env-GetMethodID(clazz,"function","()V");
    
    env->CallVoidMethod(father,id_father_function) //调用父类的方法 但是会执行子类的
    
    env->CallNonvirtualVoidMethod(father,clazz_father,id_father_function);
    
}

```
### jni中创建对象及字符串

#### native中创建java对象
---
- 第一种
jobject NewObject(jclass clazz,jmethodID methodId,...);
```
jclass clazz_date = env->FindClass("java/utils/Date");
jmethodID mid_date = env->GetMethodID(clazz_date,"<init>","()V");
//创建Date对象
jobject now = env->NewObject(clazz_date,mid_date);
//获取date中要调用的方法id，调用date的方法
jmethodId mid_date_getTime = env->GetMethodID(clazz_date,"getTime","()J");

jlong time = env->CallLongMethod(now,mid_date_getTime);

```

#### native中操作java字符串

JNIEnv中的c++方法
```
//获取字符串长度
jsize GetStringLength(jstring j_msg)


//将jstring 对象拷贝到const jchar* 指针字符串
// 拷贝java字符串并以Utf-8编码传入jstr
GetStringRegion(jstring jstr_s,jsize start,jsize len,jchar* jstr)
// utf-16
GetStringUTFRegion(jstring j_msg,jsize start,jsize len,char* jstr)

// 字符串指针转换成字符串对象jstring
jobject NewString(const jchar* jstr,int size)

//将jstring对象转成const jchar*字符串指针 返回一个utf-16的宽字符串
const jchar* GetStringChars(jstring j_msg,jboolean* copied) // copied是否进行拷贝

//返回utf-8的字符串
const char* GetStringUTFChars(jstring str,jboolean* copied)


ReleaseStringChars(jstring j_msg,const jchar* jstr) 

ReleaseStringUTFChar(jstring jstr,const char* str)

//将jstring对象转化成const jchar*字符串指针
const jchar* GetStringCritical（jstring j_msg,jboolean* copied);

ReleaseStringCritical(jstring j_msg,const jchar* jstr)

/**
    GetStringCritical/ReleaseStringCritical之间是一个关健区，在这个关键区域之间不能调用
    jni的其他函数，否则将造成关键区代码执行期间垃圾回收器停止运作，任何触发垃圾回收器的线程也会暂停。
*/

```



> java中，使用的字符串Sting对象是Unicode(UTF-16)码，即每个字符不论是中文还是英文还是符号，一个字符总是占两个字节。通过JNI接口可以将java的字符串转到c/c++ 中的宽字符串(wchart_t*)或者传回一个UTF-8的字符串（char*）到c/c++;反过来，c/c++可以通过一个宽字符串，或一个utf-8编码的字符串来创建一个java端的String对象。


示例: 将java字符串反转，java再输出
```
public class JNIDemo{
    
    public native void callCppFunction();
    
    public String msg = "hello";
    static{
         System.loadLibrary("JNIDemo")
    }
    
    public static void main(String[] args){
       JNIDemo jniDemo = new JNIDemo();
       jniDemo.callCppFunction();
       System.out.println(jniDemo.msg);
    }
}

// c++代码
#include<iostream>
#include"com_jni_demo_JNIDemo.h"
#include"windowns.h"
#include<string>
#include<algorithm>
using namespace std;

JNIEXPORT void JNICALL Java_com_jni_demo_JNIDemo_callCppFunction(JNIEnv* env,jobject obj){
    
    jfieldID fid_msg = env->GetFieldID(env->GetObjectClass(obj),"msg","Ljava/lang/string;");
    
    jstring j_msg =(jstring)env->GetObjectField(obj,fid_msg);
    
    const jchar* jstr = env->GetStringChars(j_msg,NULL);
    
    wstring wstr( (const wchar_t*)jstr );
    //释放指针
    env->ReleaseStringChars(j_msg,jstr)
    
    //第二种
    const jchar* jstr =env->GetStringCritical(j_msg,NULL);
    wstring wstr( (const wchart_*)jstr) //转换成c++的字符串
    //释放指针
    env->ReleaseStringCritical(j_msg,jstr);
    
    //第三种
    jsize len =env->getStringLength(j_msg);
    jchar* jstr = new jchar[len+1]
    jstr[len]=L'\0' //c++中字符串以\0结尾，不然会输出意想不到的字符
    
    env->GetStringRegion(j_msg,0,len,jstr);
    //转换成宽字符串
    wstring wstr( (const wchar_t*)jstr);
    //释放指针
    delete[] jstr;
    
    //反转字符串
    reverser(wstr.begin(),wstr.end());// 接受的参数是wstring
    //获取反转后的字符串
    jstring j_new_str = env->NewString( (const jchar*)wstr.c_str(),(jnit)wstr.size());
    
    env->SetObjectField(obj.fild_msg,j_new_str);
}

```
#### c++中操作java数组

```
public class JNIDemo{
    int[] arrays = { 1,2,3,4,5}
    
    Father[] objectArrs = {new Father(),new Father()};
    
    public native void callCppFunction();
    
    public static void main(String[] args){
        System.loadLibrary("JNIDemo");
        JNIDemo jniDemo = new JNIDemo();
        jniDemo.callCppFunction();
    }
}

// c++代码
JNIEXPROT void JNICALL Java_com_jni_demo_JNIDemo_callCppFunction(JNIEnv* env ,jobject jobj){
    
    //获取java中的数组属性id
    jfieldID fid_arrays = env->GetFieldID(env->GetObjectClass(obj),"arrays",'[I' );
    //通过id获取数组对象
    jintArray jint_arr = (jniArray)env->GetObjectFiled(obj,fid_array);
    
    //获取到数组的指针
    jint* int_arr = env->GetIntArrayElements(jint_arr,NULL);
    //获取数组的长度
    jsize len = env->GetArrayLength(jint_arr);
    
    
    //操作数组
    jintArray jint_arr_temp = env->NewIntArray(len);
    jint* int_arr_temp = env->GetIntArrayElements（jint_arr_temp,NULL);
    //赋值数组
    int_arr_temp[0] = int_arr[j];
    
    //第二种
    jint* buffer = new jint[len];
    //拷贝数组
    env->GetIntArrayRegion（jint_arr,0,3,buffer);
    //设置原来数组的值
    env->SetIntArrayRegion(jint_arr,3,4,buffer);
    
    /**
        mode的取值:
            0:对java的数组进行更新并释放c/c++数组。
            JNI_COMMIT:对java的数组进行更新但不释放c/c++的数组。
            JNI_ABORT:对java的数组不进行更新，释放c/c++的数组。
    */
    env->ReleaseIntArrayElements(jint_arr,int_arr,JNI_ABORT);
    
    //操作对象数组
    jfieldID fid_obj_arrays = env->GetFieldID(env->GetObjectClass(obj),"objArrays","[Lcom/jni/demo/Father;");
    
    jobjectArray jobj_arr = (jobjectArray)env->GetObjectField(obj,fid_obj_arrays);
    
    jobject jobj = env->GetObjectArrayElement(jobj_arr,1);//获取索引1的， father对象
    
    //获取方法id
    jmethodID id_father_function = env->GetMethodID(env->GetObjectClass(jobj),"function","()V");
    env->CallVoidMethod(jobj,id_father_function);
}


```

