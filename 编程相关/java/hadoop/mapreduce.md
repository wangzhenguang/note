


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

#### 自定义输出对象

1. 实现org.apache.hadoop.io.Writable 接口
2. 重写write/read方法
```
  @Override
    public void write(DataOutput dataOutput) throws IOException {
        dataOutput.writeLong(long);
        dataOutput.writeInt(int);
        ...
    }
    
    
    @Override
    public void readFields(DataInput dataInput) throws IOException {
        long= dataInput.readLong();
        int = dataInput.readInt();
    }

```

#### mapper

#### reducer

#### partitioner

#### GroupingComparator

#### FileOutputFormat

