import requests
import os
from lxml import etree
url = 'http://sc.chinaz.com/jianli/free.html'

save_addr = "简历模板"  # 保存的文件夹
header = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.111 Safari/537.36 Edg/86.0.622.56'
}

#1 .  爬取简历列表页面
res = requests.get(url, headers=header)

# 设置编码:
res.encoding = res.apparent_encoding
# 将返回的结果转html
html = etree.HTML(res.text)

# 提取列表所有数据
div_list = html.xpath('//*[@id="container"]/div')
for div in div_list:
    #href = div.xpath('./p/a/@href')[0]
    href = div.xpath('./a/@href')[0]
    title = div.xpath('./p/a/text()')[0] 
    print(href,title)
    # 请求详细页面
    detil_url =requests.get(href)
    # 获取详细页面下载链接
    detil_html = etree.HTML(detil_url.text)
    download_url = detil_html.xpath('//div[@class="clearfix mt20 downlist"]/ul/li[1]/a/@href')[0]##https://downsc.chinaz.net/Files/DownLoad/jianli/202306/zjianli1763.rar
    #请求文件下载地址 
    download_res = requests.get(url=download_url)
    # 确定保存名称和路径
    file_name = f'{title}.{download_url.split(".")[-1]}'
    # 确定保存的文件夹是否存在保存到本地
    if not os.path.exists(save_addr):
                os.makedirs(save_addr)
    with open(os.path.join(save_addr,file_name),'wb') as f:
                f.write(download_res.content)
                print(f'{file_name}保存好了')
