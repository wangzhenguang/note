


Mapper接口泛型参数
---

KEYIN: mr框架所读入的起始偏移量。
VALUEIN:mr框架读到的一行文本内容

KEYOUT:逻辑处理完成输出的数据key
VALUEOUT: 逻辑处理完成输出的value


hadoop中有自己的序列化接口，所以不直接使用java中的Long,String等

String = org.apache.hadoop.io.Text
Long = LongWritable
Integer = IntWritable