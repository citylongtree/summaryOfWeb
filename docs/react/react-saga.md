# 理解 redux-saga 关键词
>fork: 创建一个新的进程或者线程，（并发）发送请求。  
>call: 发送 API 请求。 
>put: 发送对应的 dispatch， 触发对应的 action。  
>takeEvery: 监听对应的 action；每一次 dispatch 都会触发。
>takeLatest: 监听对应的 action；只会触发最后一次 dispatch 。
>all: 同时并发多个 action，没有顺序