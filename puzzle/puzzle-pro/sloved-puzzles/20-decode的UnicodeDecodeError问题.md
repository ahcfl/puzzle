response = requests.get(url, headers=headers)
text = response.content.decode('GBK') 
pycharm则会出现一下错误提示：
UnicodeDecodeError: 'gbk' codec can't decode byte 0xd0 in position 11315: illegal multibyte sequence
大意为：UnicodeDecodeError: ‘gbk’ codec不能解码位置为11315的字节0xd0:非法的多字节序列
出现原因：应该是被解码的文本中含有不规范的字符，gbk不能解码。
解决办法为：

text = response.content.decode('GBK','ignore')
response_content_text = response_content_data.decode('GBK', 'ignore')

str转bytes叫encode，bytes转str叫decode。

sudo /etc/init.d/networking restart