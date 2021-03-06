## TRUSTOCEAN SSL SDK
由TrustOcean Limited专门为Python用户开发的TrustOcean API SDK, 用于对接数字证书接口，支持数字证书申请、吊销、验证、续期等操作。

<span> <img src="https://trustocean.com/wp-content/themes/twentytwelve/img/logo-red.svg" height = "29px" alt="环智中诚" align=center /></span>
<span>【<a href="https://github.com/londry/TRUSTOCEAN-SDK-PYTHON/wiki" target="_blank">WIKI页面</a>】</span>
<span>【<a href="https://certs-trustocean.doc.coding.io/" target="_blank">API说明文档</a>】</span>
<span>【<a href="https://www.trustocean.com" target="_blank">环智中诚官网</a>】</span>
<span>【<a href="https://wpa1.qq.com/PFVEyXqF?_type=wpa&qidian=true" target="_blank">在线客服</a>】</span>


### 安装步骤
1.确保Python版本正确：
- Python >= 3.6

2.安装依赖库
```python
pip install requests
pip install pyOpenSSL
pip install pem
```

3.安装此SDK
```python
pip install trustocean_sdk
```

### 开始使用
在开始使用此API SDK库之前，请确保您已经拥有了环智中诚的账户，且保证您的账户已经具备了API服务的访问权限。
- 如果还没有账户？[访问此注册账户](https://console.trustocean.com/)
- 还没有API访问权限？[访问此申请通用的API权限](https://trustocean.com/partner-program)

#### 获取API登陆信息并添加白名单
- API USERNAME就是您的环智中诚账户邮箱地址/Tier2 API的用户名
- API PASSWORD/Token密码,如果您已经生成过，请继续使用之前生成的。如果还没有生成，可以通过 [访问此页面创建](https://console.trustocean.com/partner/api-setting) ，特别注意，当您生成了新的API-Token之后，之前的API-Token将会失效。
- 同时需要将您用于请求API的电脑/服务器的公网IPv4地址添加到 [此页面的API白名单](https://console.trustocean.com/partner/api-setting) 一栏，才可确保能正常和接口交互。

#### 在您的项目代码中使用此SDK
引入SDK库中的API客户端到代码
```python
from trustocean_sdk import client as TrustOceanClient
```
创建API客户端实例
```python
api_username = '你的环智中诚账户邮箱地址'
api_token = '你在环智中诚API页面创建的API-Token'

apiClient = TrustOceanClient.APIClient(api_username, api_token)
```
检查是否可以正常访问API服务
```python
result = apiClient.check_service_status()
if result['status'] == 'success':
    print('connected')
else:
    print('connection error')
    print(result)
```
##### 生成用于创建SSL订单的CSR代码和KEY私钥
此SDK提供了一系列工具来帮助您更加轻松的创建SSL证书订单，在开始使用这些工具之前，您需要先导入工具库到您的代码中
```python
from trustocean_sdk import utils as TrustOceanUtils
```
创建RSA签名的CSR代码和KEY私钥，用于申请常用的RSA2048位加密的SSL证书
```python
x509Utils = TrustOceanUtils.X509Utils()
new_key_pair = x509Utils.generate_csr(TrustOceanUtils.X509Utils.SIGN_TYPE_RSA, 'trustocean.com')
print(key_pair['csr_code']) # 文本格式的CSR代码内容
print(key_pair['key_code']) # 文本格式的私钥KEY代码内容
```
创建ECC签名的CSR代码和KEY私钥，用于申请更高安全系数的ECC加密的SSL证书
```python
x509Utils = TrustOceanUtils.X509Utils()
new_key_pair = x509Utils.generate_csr(TrustOceanUtils.X509Utils.SIGN_TYPE_ECC, 'trustocean.com')
print(new_key_pair['csr_code']) # 文本格式的CSR代码内容
print(new_key_pair['key_code']) # 文本格式的私钥KEY代码内容
```
##### 获取API账户可订购的产品信息
此接口主要用于获取产品信息，产品信息中的多个信息将用于后续创建证书订单的接口，因此您需要将您的产品信息缓存在本地以备用，或根据自己的业务逻辑进行存储。
```python
products = apiClient.get_product_list()
print(products['products'])
```
响应中的`products`值为`pid->[productinfo]`格式的参数，对应每款产品信息，产品信息中最主要的参数包括
```text
pid: 产品的pid,用于订购下单
name: 产品的名称
class: 产品的验证类型 DV/OV/EV
multidomain: 是否支持保护多条域名 off/on
wildcard: 是否支持保护通配符域名 off/on
ipv4: 是否支持保护公网IPv4地址 off/on
brand: 产品的品牌
chineseName: 产品的中文名称备注
periods: 产品支持的订购周期，下单时可以对照 trustocean_sdk.client.ProductPeriodHelper 中定义的常量进行传入
```

更多使用方法和说明，请查看此项目的WIKI页面，[>>点此进入WIKI页面](https://github.com/londry/TRUSTOCEAN-SDK-PYTHON/wiki)
