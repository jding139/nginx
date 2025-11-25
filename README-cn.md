# 支持商密算法的Nginx
通过tassl支持国密协议CNTLS的nginx.

我在搭建符合商密标准平台过程中，发现很难找到合适的开源系统让我轻松的搭建商密平台。终于发现江南天安(https://www.tass.com.cn/)提供的开源库，而且还有针对Nginx的改造版本。
不过他们提供的修改是基于nginx 1.16版本，我这里参考其修改，一直到了 nginx 1.28.0上。

切换到 stable-1.28-sm分支，下载代码，然后按照下面的步骤编译即可得到支持商密算法的Nginx 1.28.
 
 注：TASSL最新版本https://github.com/jntass/TASSL-1.1.1 (此版本可配合原生nginx搭建国密SSL web server和反向代理；如需实现国密SSL前向代理，请使用此仓库内经过定制的nginx)

## 如何使nginx调用tassl实现国密ssl协议？
具体方法如下：
1.	下载https://github.com/jntass/TASSL-1.1.1 编译并安装。
2.	下载stable-1.28-sm分支的代码进行编译。

      ./configure --with-http_ssl_module --with-stream --with-stream_ssl_module --with-openssl=/usr/local/tassl --prefix=/usr/local/nginx
      注：--with-openssl后跟TASSL源码路径

      make
      make install

3.	配置nginx。

     配置nginx.conf证书部分：
     ssl_certificate      /usr/local/tassl/tassl_demo/cert/certs/SS.crt;    #/*签名证书*/
     ssl_certificate_key  /usr/local/tassl/tassl_demo/cert/certs/SS.key;     #/*签名私钥*/
     ssl_enc_certificate      /usr/local/tassl/tassl_demo/cert/certs/SE.crt;     #/*加密证书*/
     ssl_enc_certificate_key  /usr/local/tassl/tassl_demo/cert/certs/SE.key;     #/*加密私钥*/
      注意：签名证书的证书用途需有数据签名功能，加密证书的证书用途需有数据加密功能。如果功能不正确，会导致握手失败。调用tassl_demo/cert/中的脚本生成的证书已经具备相应功能,可以用来测试。

#下载国密浏览器进行测试
虽然有很多支持商密的浏览器，比方说360国密浏览器等等，但是最终都需要收费。
我推荐使用免费的零信浏览器来验证商密功能：https://www.zotrus.com/browser/
零信除了提供免费浏览器，它还是一个证书提供机构，可以在它的系统中申请免费的内部测试证书 https://cersign.com/intranet-ssl-certificates.html；正式证书则可以在https://www.zotrus.com/identity/index.html 购买。
