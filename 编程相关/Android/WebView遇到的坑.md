###  webView 放大 缩小的按钮 会导致崩溃



![image](C:\Users\m\Desktop\weizhi\android\webView遇到的坑.md_files\5388b28d-e9ae-436b-b823-5db284b2091c.png)

  https://code.google.com/p/android/issues/detail?id=15694 http://stackoverflow.com/questions/5267639/how-to-safely-turn-webview-zooming-on-and-off-as-neede
zoom controller有个默认的渐隐动画，只要在渐隐动画进行中，退出activity就会出现crash.

http://stackoverflow.com/questions/4908794/webview-throws-receiver-not-registered-android-widget-zoombuttonscontroller

来源：  http://blog.csdn.net/liu857279611/article/details/52069637