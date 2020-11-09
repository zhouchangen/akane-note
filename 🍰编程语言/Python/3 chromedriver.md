# chromedriver



使用示例：

```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
import time

# 环境地址
URL = "http://baidu.com"
# 用户名
USERNAME = "admin"
# 密码
PASSWORD = "123456"

def start(driver):
    # 浏览器最大化
    driver.maximize_window()

    # 访问环境地址
    driver.get(URL)
    time.sleep(1)


    # 填写账号和密码
    usernameElement = driver.find_element_by_id("username")
    usernameElement.send_keys(USERNAME)
    passwordElement = driver.find_element_by_id("password")
    passwordElement.send_keys(PASSWORD)
    time.sleep(2)

    # 登录
    loginElement = driver.find_element_by_id("sign")
    # 点击
    loginElement.click() 
    time.sleep(5)

     # 刷新浏览器
    driver.refresh()

if __name__ == "__main__":
    driver = webdriver.Chrome("./driver/chromedriver.exe")
    start(driver)
    driver.close

```

注：需下载chromedriver.exe