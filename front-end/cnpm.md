## 1.临时使用
`npm --registry https://registry.npm.taobao.org install express`

## 2.持久使用
`npm config set registry https://registry.npm.taobao.org`

配置后可通过下面方式来验证是否成功 
`npm config get registry`

## 3.通过cnpm使用
`npm install -g cnpm --registry=https://registry.npm.taobao.org`
