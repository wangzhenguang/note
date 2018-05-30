

### gradle 添加全局配置脚本
<user_home>/.gradle/init.d

gradle会执行init.d下以.gradle为拓展名的所有初始化脚本。

配合gradle生命周期回调

```
gradle.projectsLoad{Gradle gradle ->

   
}

```

### gradle 生命周期

初始化  --> 项目执行前（hook)  -->  配置阶段 --> task 图生成(hook) -->执行阶段  --> 项目构建完成(hook)


```groovy

gradle.beforeProject{project->
    //项目执行前
} 

gradle.taskGraph.whenReady{TaskExecutionGraph taskExecutionGraph ->
    
   //task 图生成
    
}

gradle.buildFinished{result-> 
    //项目构建完成
}

```