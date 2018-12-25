
#### [原文](https://developer.android.com/topic/performance/vitals/launch-time)

# 应用启动时间


## App 的启动机制

App 有三种启动方式，分别是 Cold start、Warm start 和 Hot start，其中 Cold start 是将 App 从零开始（App 进程不存在）启动，而 Warm start 和 Hot start 都是将 App 从后台拉起到前台的过程。

- Cold start：自从设备启动后还未拉起过 App 或者 App 被系统杀死；

Cold start 方式下，系统会拉起





应用从什么时候算开始启动，什么时候算启动完毕？







