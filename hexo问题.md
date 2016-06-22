# 安装
npm install -g hexo
hexo init blog
## 安装
hexo-cli
hexo-server(不能启动服务)

# deploy设置
deploy:
  type: git
  repository: git@github.com:wuzhi3078/wuzhi3078.github.com.io.git
  '#例如我的：repository: git@github.com:zhongbaitu/zhongbaitu.github.com.git
  branch: master

deploy的type改成git，然后运行下npm install hexo-deployer-git --save

# 图片的问题
npm install https://github.com/CodeFalling/hexo-asset-image --save
1 图片放到 _posts的与文件同名目录下
2 站点配置开启 ：post_asset_folder: true
3 图片用 ![IMAGE](UnityPocketGame/500.jpg)
4 vscode 可以ctrl+shift+v 预览rd文件。

# 原生主题使用google字体加载慢











