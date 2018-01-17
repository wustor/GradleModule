
## exclude配置

其实我的博客里面忽略了一种情况，就是dependencies中有需要exclude的情况，多谢[Vanish](https://github.com/Vanish136)提出了这个问题，如果单纯的去遍历一个map是不可以的，比如说下面Glide的配置情况

 

       compile("com.github.bumptech.glide:glide:4.3.1") {
       exclude(group: 'com.android.support', module: 'support-v4')
        exclude(group: 'com.android.support', module: 'appcompat-v7')
        exclude(group: 'com.squareup.okhttp3', module: 'okhttp3')
    }

对于这种情况的话，需要额外定义一个关于exclude的map,因为可能多个依赖需要exclude，下面就用Glide举个栗子：

###  config.gradle

map的key是compile的依赖，然后value是一个数组，因为有可能需要剔除多个重复依赖，所以用数组来表示

    excludes = ["com.github.bumptech.glide:glide:4.3.1":
                        [
                                'com.android.support' : 'support-v4',
                                'com.android.support' : 'appcompat-v7',
                                'com.squareup.okhttp3': 'okhttp3']]

### build.gradle

```
excludes.each { entry ->
        compile(entry.key) {
            excludes.value.each { childEntry ->
                exclude(group: childEntry.key, module: childEntry.value)
            }
        }
    }
```

然后在build.gradle中拿到这个map，然后进行遍历就好，代码已上传，有需要的可以下载进行查看。
