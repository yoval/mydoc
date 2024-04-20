# 本地代理

全局

`git config --global http.proxy http://127.0.0.1:7890`

或者

`git config --global http.proxy http://username:password@127.0.0.1:7890`

在该仓库运行去掉`global`为特定仓库配置代理，相应的

`git config --global --unset http.proxy`

或者

`git config --unset http.proxy`

取消代理