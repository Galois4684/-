共**289**条记录。部分内容：
```
{'电影名': '大话西游之月光宝盒', '年份': '1995', '地区': ['中国香港', '中国大陆'], '类型': ['喜剧', '爱情', '奇幻', '古装'], '导演': ['刘镇伟'], '演员': ['周星驰', '吴孟达'], '评分': '9.0'}
```

百度云:

链接：https://pan.baidu.com/s/1yT6XqhdN6HDEZ1N-qfn0Lw?pwd=4684 
提取码：4684 
--来自百度网盘超级会员V3的分享



数据爬取部分代码:
```
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import time
import pickle

options = Options()
# options.add_argument("--headless")
# browser = webdriver.Chrome(options=options)
browser = webdriver.Chrome()

wait = WebDriverWait(browser, 10)  # 设置一个最长等待时间为10秒的WebDriverWait对象

browser.get('https://movie.douban.com/explore')

# 等待第一个元素可点击，然后点击
place = wait.until(EC.element_to_be_clickable((By.XPATH, '//*[@id="app"]/div/div[1]/div/div[1]/div[2]/div/div[1]')))
place.click()

# 等待第二个元素可点击，然后点击
chinese = wait.until(EC.element_to_be_clickable((By.XPATH, '//*[@id="app"]/div/div[1]/div/div[1]/div[2]/div/div[2]/div/div/ul/li[2]/span')))
chinese.click()

# 等待第三个元素可点击，然后点击
rank = wait.until(EC.element_to_be_clickable((By.XPATH, '//*[@id="app"]/div/div[1]/div/div[1]/div[5]/div')))
rank.click()

# 等待第四个元素可点击，然后点击
highscore = wait.until(EC.element_to_be_clickable((By.XPATH, '//*[@id="app"]/div/div[1]/div/div[1]/div[5]/div[2]/div/ul/li[4]/span')))
highscore.click()

for i in range(35):
    time.sleep(1)
    showmore = wait.until(EC.element_to_be_clickable((By.XPATH, '//*[@id="app"]/div/div[2]/div/button')))
    showmore.click()

time.sleep(1)
movies = browser.find_element(By.CLASS_NAME, 'explore-list')
movies = movies.find_elements(By.TAG_NAME, 'li')

data = []

# 根据提取到的text内容分割筛选
for item in movies:
    time.sleep(1)
    info = item.text.split('/')
    try :
        _ = info[0].split('\n')
        info[0] = _[0]
        info.append(_[1])
        _ = info[-2].split('\n')
        info[-2] = _[0]
        info.append(_[1])
    except:
        continue
        print(info)

    if len(info) <= 6:
        continue
    try :
        data.append({'电影名': info[0].strip(),
                     '年份': info[5].strip(),
                     '地区': [item for item in info[1].split(' ') if item != ''],
                     '类型': [item for item in info[2].split(' ') if item != ''],
                     '导演': [item for item in info[3].split(' ') if item != ''],
                     '演员': [item for item in info[4].split(' ') if item != ''],
                     '评分': info[6].strip()})
    except:
        print(info)

# 保存数据
with open('movie_datas.pkl', 'wb', encoding='utf-8') as tf:
    pickle.dump(data, tf)

# 关闭浏览器
browser.quit()
```

