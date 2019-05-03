# quantitative-investment-learning
使用Python进行量化投资的学习报告


**Python量化投资学习报告**

`CatsJuice` 编辑于 `2019-4-26`

上一次更新： `2019-05-03 15:45`

**CONTENTS:**





- [1. **数据抓取**](#1-数据抓取)
    - [1.1. **通过第三方数据平台直接调用api**](#11-通过第三方数据平台直接调用api)
        - [1.1.1. **TuShare(挖地兔)**](#111-tushare挖地兔)
        - [1.1.2. **其他平台**](#112-其他平台)
    - [1.2. **使用爬虫抓取**](#12-使用爬虫抓取)
        - [1.2.1. **新浪财经**](#121-新浪财经)
        - [1.2.2. **网易财经**](#122-网易财经)
        - [1.2.3. **东方财富**](#123-东方财富)
- [2. **热点获取**](#2-热点获取)
    - [2.1. **东方财富**](#21-东方财富)
- [3. **数据分析**](#3-数据分析)
    - [3.1. **换手率分析**](#31-换手率分析)
    - [3.2. **CCI指标**](#32-cci指标)
        - [3.2.1. **概念**](#321-概念)
        - [3.2.2. **指标用法**](#322-指标用法)
        - [3.2.3. **公式**](#323-公式)
        - [3.2.4. **程序设计**](#324-程序设计)
        - [3.2.5. **参数说明**](#325-参数说明)
        - [3.2.6. **测试结果**](#326-测试结果)
    - [3.3. **《胡立阳股票投资100招》** 由“价量关系”来为个股打分数](#33-胡立阳股票投资100招-由价量关系来为个股打分数)
        - [3.3.1. **概念**](#331-概念)
        - [3.3.2. **程序设计**](#332-程序设计)
        - [3.3.3. **结果**](#333-结果)
        - [3.3.4. **Source Code**](#334-source-code)







# 1. **数据抓取**

## 1.1. **通过第三方数据平台直接调用api**
### 1.1.1. **TuShare(挖地兔)**

**A. 概述**

> **Tushare**是一个免费、开源的python财经数据接口包。主要实现对股票等金融数据从**数据采集**、**清洗加工** 到**数据存储**的过程，能够为金融分析人员提供快速、整洁、和多样的便于分析的数据，为他们在数据获取方面极大地减轻工作量，使他们更加专注于策略和模型的研究与实现上。

接口文档地址：[http://tushare.org/](http://tushare.org/) 

ToShare Pro: [https://tushare.pro/](https://tushare.pro/)

**B. 基本使用**

**使用前提：**

- 安装Python
- 安装Pandas, lxml

**下载安装：**

- 方式1：`pip install tushare`
- 方式2：访问[https://pypi.python.org/pypi/Tushare/](https://pypi.python.org/pypi/Tushare/)下载安装

**使用：**
以[历史行情](http://tushare.org/trading.html#id2)接口为例， 如下调用：
```
import tushare as ts

ts.get_hist_data('600848')      #一次性获取全部日k线数据
```
**结果显示：**

![历史行情接结果返回](https://catsjuice.cn/index/src/markdown/stock/201904262245.png "历史行情结果")

> `Tushare`返回的绝大部分的数据格式都是`pandas DataFrame`类型，非常便于用`pandas` / `NumPy` / `Matplotlib`进行数据分析和可视化

### 1.1.2. **其他平台**

**Win.d**

官网: [https://www.wind.com.cn/Default.html](https://www.wind.com.cn/Default.html)
>中国市场的精准金融数据服务供应商，为量化投资与各类金融业务系统提供准确、及时、完整的落地数据，内容涵盖
股票、债券、基金、衍生品、指数、宏观行业等各类金融市场数据，助您运筹帷幄，决胜千里


**优矿**

官网: [https://uqer.io/](https://uqer.io/)
> 提供各类资产的财务、因子、主题、宏观行业特色大数据，以及量化场景下的PIT数据，保障量化过程不引入未来数据。
> 股票、期货、指数、场内外基金等多资产多策略回测。丰富的衍生工具，保证多因子策略、事件驱动等快速实现。

## 1.2. **使用爬虫抓取**


手动写爬虫进行抓取， 首先需要确定数据来源， 较为主流的财经数据平台有**新浪财经**， **东方财富**， **网易财经**等。 接下来针对若干平台， 分析抓取的过程以及可能遇到的问题；

### 1.2.1. **新浪财经**

**获取所有股票代码**（这里只考虑沪深A股）

新浪财经的数据地址为[http://vip.stock.finance.sina.com.cn/q/go.php/vIR_CustomSearch/index.phtml](http://vip.stock.finance.sina.com.cn/q/go.php/vIR_CustomSearch/index.phtml), 通过切换页面可以发现， 地址栏的url的参数发生了变化，变化的规律为：`p=n`, `n`为当前页码， 而股票代码和股票名称， 通过`F12`打开开发者工具, 表单中的值均可定位到， 应该可以通过爬虫抓取所有股票的基本信息， 通过新浪财经抓取股票的代码我这里没有写， 暂先略过；

**获取日线数据**

在新浪财经数据中心， 我并没有找到**交易数据**的表格， 要查看某只股票的**日线** / **分线**只能通过点击某只股票进到[详情页](https://finance.sina.com.cn/realstock/company/sh601789/nc.shtml)(而url中的股票代码对应着不同的股票)， 而新浪财经的数据并非以表格的形式展示， 而是通过图表展示， 鼠标移动时通过`JavaScript`更新日期及当前股票的数据信息；那么直接抓取网页是无法获取到数据的， 现在的思路是分析js文件， 找到鼠标移动时监听到的事件， 查明js是如何更新数据的， js的数据从哪里提取，以此来抓取信息;

首先， 通过页面元素审查可以发现， 分时线是通过`HTML5`的`canvas`绘制的， 在Sources找相关的js文件， 可以找到`paintSth.js`文件， 由于在鼠标移动时会更新页面元素， 所以可以直接在文件中查找`mousemove`， 找到了相关代码如下:

```
C = this.interactCanvas,
...
...
...
C.addEventListener("mousemove", o),
```
可以看到Canvas加了一个mousemove的监听器， 执行`o`, 再查找`o()`, 可以找到如下代码：
```
function o() {
    if (!c) {
        var t = document.createElement("canvas");
        c = t.getContext("2d")
    }
    return c
}
```
这里`c`又是一个未知量， 所以应该继续检索`c`的信息， 由于关联的js文件较多， 这种做法过于费时费力， 爬取新浪财经的交易数据应该不是明智的选择；

**换种思路**， 既然可以浏览， 那么使用`selenium`就有可能， `selenium`可以进行自动化测试， 让鼠标在固定位置移动， 同时抓取更新的信息， 这种做法是可行的， 我也尝试着做了， 由于这一方法过于不实用， 源码略；

**这种做法局限性太大**， 首先， **效率过低**， 这是很致命的一个缺陷， 除此之外， 由于移动导致的像素不同， 可能会出现数据遗漏或重复；最后， 新浪财经默认只显示一定日期的交易数据， 要查看更早的需要手动拖动进度条， 这就使得`selenium`的操作误差更大；

综上所述， 在新浪抓取交易数据是挺不容易的事情；

**获取财务数据**

新浪的财务数据在[http://vip.stock.finance.sina.com.cn/q/go.php/vFinanceAnalyze/kind/profit/index.phtml?p=1](http://vip.stock.finance.sina.com.cn/q/go.php/vFinanceAnalyze/kind/profit/index.phtml?p=2), 这应该是爬虫抓取中喜闻乐见的格式了，换页不需要通过ajax， 所以抓取的时候只需要设定好抓取的总页数， 循环抓取页面再解析即可， 接下来即可直接进行代码的编写（未亲自验证）

### 1.2.2. **网易财经**

**获取所有股票代码**（这里只考虑沪深A股）
网易财经的所有沪深A股数据位于[http://quotes.money.163.com/old/#query=EQA&DataType=HS_RANK&sort=PERCENT&order=desc&count=24&page=0](http://quotes.money.163.com/old/#query=EQA&DataType=HS_RANK&sort=PERCENT&order=desc&count=24&page=0), 从`url`来看， 换页通过url传参即可改变了， 但是实际操作可以发现， 点击换页时`url`中的`page`并不会改变， 但改变`url`中的`page`参数， 当前页面序号会改变；但这不意味着可以像[1.2.1. **新浪财经**](#121-新浪财经)中爬取新浪财经一样， 枚举url的page参数来爬取所有信息；因为股票数据是异步加载的， 直接抓取无法获取到值；

对于网易财经，由于点击换页时页面的url没有更新，所以应该是使用了`Ajax`或`Js`来更新数据， 通过`F12`调起开发者工具， 在`Network`选型卡中， 筛选`XHR`, 每当点击换页时， 就会有新的`XHR`， 分析这些`XHR`的url可以发现，只有`page`值在改变：

![XHR URL](https://catsjuice.cn/index/src/markdown/stock/201904271217.jpg)

直接复制`Request URL`并使用浏览器访问， 可以得到`json`格式的数据， 但是可以看到中文通过`Unicode`编码了， 在获取后， 可以通过`s.decode('unicode_escape')`来解码；接下来就是对`json`解析并提取需要的信息了， json格式如下：

![网易财经股票JSON格式](https://catsjuice.cn/index/src/markdown/stock/201904271328.png)

在`list`中有`[0]`到`[23]`共24条数据， 对应请求中的参数`count=24`， 关于字段名的解释， 以下为我的分析：

No. | key_name | meaning
:--:| :--: | :--:
1 | ANNOUNMT | 公告信息，并非必须，对应在页面中有公告标签的股票才有这个字段
2 | CODE | 股票代码
3 | FIVE_MINUTE | 5分钟涨跌额
4 | HIGH | 最高
5 | HS | 换手率(不带%)
6 | LB | 量比
7 | LOW | 最低
8 | MCAP | 流通市值
9 | MFRATIO | list, 包含2个值`MFRATIO2`:净利润， `MFRATIO10`: 主营收
10 | MFSUM | 每股收益
11 | NAME | 名称
12 | NO | 网易财经中的编号
13 | OPEN | 今日开盘
14 | PE | 市盈率
15 | PERCENT | 涨跌幅
16 | PRICE | 价格
17 | TCAP | 总市值
18 | TURNOVER | 成交额
19 | VOLUME | 成交量
20 | WB | 委比


&emsp;&emsp;所以可以直接抓取这个url来获取相关的数据， 更有趣的是， 请求参数中有个`count`参数， 决定了数据的数量， 所以我尝试将`count`设置成全部数量, 查看网易财经沪深A股， 网易的编号最后一只为`3607 `， 所以如下请求：
```
http://quotes.money.163.com/hs/service/diyrank.php?host=http%3A%2F%2Fquotes.money.163.com%2Fhs%2Fservice%2Fdiyrank.php&page=0&query=STYPE%3AEQA&fields=NO%2CSYMBOL%2CNAME%2CPRICE%2CPERCENT%2CUPDOWN%2CFIVE_MINUTE%2COPEN%2CYESTCLOSE%2CHIGH%2CLOW%2CVOLUME%2CTURNOVER%2CHS%2CLB%2CWB%2CZF%2CPE%2CMCAP%2CTCAP%2CMFSUM%2CMFRATIO.MFRATIO2%2CMFRATIO.MFRATIO10%2CSNAME%2CCODE%2CANNOUNMT%2CUVSNEWS&sort=PERCENT&order=desc&count=3607&type=query
```
即可返回所有`json`格式的数据， 然后再进行解析， 并写入文件， 完整代码如下：

```
import urllib
import json
import csv
from tqdm import tqdm

# 下载器
class Downloader(object):
    def __init__(self, url):
        self.url = url

    def download(self):
        html_content = urllib.request.urlopen(self.url).read()
        html_content = html_content.decode("utf-8")
        return html_content

# 调度器
class Controller(object):

    def __init__(self):
        self.downloader = None
        self.parser = None
        self.saver = None

    def get_data(self):
        url = "http://quotes.money.163.com/hs/service/diyrank.php?host=http%3A%2F%2Fquotes.money.163.com%2Fhs%2Fservice%2Fdiyrank.php&page=0&query=STYPE%3AEQA&fields=NO%2CSYMBOL%2CNAME%2CPRICE%2CPERCENT%2CUPDOWN%2CFIVE_MINUTE%2COPEN%2CYESTCLOSE%2CHIGH%2CLOW%2CVOLUME%2CTURNOVER%2CHS%2CLB%2CWB%2CZF%2CPE%2CMCAP%2CTCAP%2CMFSUM%2CMFRATIO.MFRATIO2%2CMFRATIO.MFRATIO10%2CSNAME%2CCODE%2CANNOUNMT%2CUVSNEWS&sort=PERCENT&order=desc&count=3607&type=query"
        html_content = urllib.request.urlopen(url).read()
        # 这时候解码可能导致json解析错误!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
        # html_content = html_content.decode("unicode_escape")
        # 当数量大于2995会报错， json解析失败， 原因是编号为2996的股票， 在公告中嵌套了引号导致json解析失败
        data = json.loads(html_content)

        self.saver = Saver(data=data)
        self.saver.save()

class Saver(object):

    def __init__(self, data):
        self.data = data
        self.prifix = 'F:\\files\\sharesDatas\\code_list\\'         # 存放目录

    def save(self):
        # 新建文件, 写入文件头
        file_header = ["代码", "名称", '流通市值', '每股收益', '总市值']
        csv_file = open(self.prifix+'沪深A股.csv', 'w', newline='')
        writer = csv.writer(csv_file)
        writer.writerow(file_header)

        list = self.data['list']
        sum = len(list)
        for i in tqdm(range(0, sum)):
            item = list[i]

            # 处理股票代码， 去掉网易财经的0/1前缀， 并使其在Excel中显示正常（加`）
            code = str(item['CODE'])
            code = code[1:7] if len(code) == 7 else code[:]
            code = "`" + code

            row = [code, item['NAME'], item['MCAP'], item['MFSUM'], item['TCAP']]
            csv_file = open(self.prifix + '沪深A股.csv', 'a', newline='')      # 追加
            writer = csv.writer(csv_file)
            writer.writerow(row)


if __name__ == '__main__':
    controller = Controller()
    controller.get_data()
```


**获取日线数据**
网易财经的日线交易数据可在[http://quotes.money.163.com/trade/lsjysj_601318.html#06f01](http://quotes.money.163.com/trade/lsjysj_601318.html#06f01)查看， 需要将`url`中的`601318`替换成相应的股票代码， 在这个页面没有换页按钮， 仅显示若干条数据， 但是在数据表的右上角有个`下载数据`的链接， 点击后， 需要勾选需要下载的字段， 点击下载后会下载一个`code.csv`文件， 所以要做的就是抓取下载的真实`url`， 按`F12`打开开发者工具， 点击下载按钮后， 在控制台看到如下提示：

![网易财经下载数据控制台捕获](https://catsjuice.cn/index/src/markdown/stock/201904271817.png)
其中已经包含了请求的真实地址， 即：

```
http://quotes.money.163.com/service/chddata.html?code=0601318&start=20070301&end=20190426&fields=TCLOSE;HIGH;LOW;TOPEN;LCLOSE;CHG;PCHG;TURNOVER;VOTURNOVER;VATURNOVER;TCAP;MCAP"
```
而想要搞清楚url的参数， 可以转到`submit`的文件， 这里即`b.667271.min.js:1`, 这是一个压缩的js文件， 格式化后， 在最后可以找到如下关键代码：
```
submit: function() {
    var e = n.value;
    if (e) e = e.replace(/-/g, "");
    var a = i.value;
    if (a) a = a.replace(/-/g, "");
    var o = t.elem.getElementsByTagName("input"),
    r = [];
    for (var d = 0; d < o.length; d++) {
        if (o[d].type == "checkbox" && o[d].checked) {
            r.push(o[d].value)
        }
    }
    var c = "/service/chddata.html?code=" + window["STOCKCODE"];
    e && /^\d{8}$/.test(e) && (c += "&start=" + e);
    a && /^\d{8}$/.test(a) && (c += "&end=" + a);
    r.length && (c += "&fields=" + r.join(";"));
    location.href = c
}
```
能够清楚地看到url的拼接过程， 具体参数如下：
编号 | 参数 | 解释 | 规范
:--: | :--: | :--: | :--:
1 | start | 起始日期 | 年月日直接拼接，如`20190427`，不足2位补0
2 | end | 截止日期 | 年月日直接拼接，如`20190427`，不足2位补0
3 | fields | 即需要下载的字段 | 使用`;`分割，如：`TCLOSE;HIGH;LOW;TOPEN;LCLOSE;CHG;PCHG;TURNOVER;VOTURNOVER;VATURNOVER;TCAP;MCAP` 具体意义不作详述
4 | code | 股票代码 | 关于股票代码， 网易财经的股票代码在传参时， 如果是以`6`开头的股票， 需要在前面加`0`, 而以`0`和`3`开头的股票需要在前面加`1`,如：`1000333`, `1300001`, `0601318`
接下来是做抓取， 抓取就是根据已经获取的**股票代码**， 枚举股票代码并下载对应的日线数据， 仅需注意每次循环最好使用`time.sleep(random.random()*2)`， 否则可能因操作频繁被拒绝访问；下面是部分源代码（注： 这里源代码中的股票代码是实时获取的， 抓取的是**股城网**, 因为只需要抓取股票代码即可）:
```
import random
import time

from stock.classes.Downloader import Downloader
from stock.classes.Parser import Parser
from stock.classes.Saver import Saver

# 控制器
class Controller(object):

    # 构造函数
    def __init__(self, url, kline_filepath, codelist_filepath, date):
        '''
        :param url: 股票基本信息url（ 股城网>行情>沪深A股 ）
        :param kline_filepath:  日线数据文件保存路径
        :param codelist_filepath:  股票代码文件保存路径
        :param date:  查询截止日期
        '''
        self.url = url
        self.kline_filepath = kline_filepath
        self.codelist_filepath = codelist_filepath
        self.date = date
        self.downloader = None  # 下载器实例
        self.parser = None      # 解析器实例
        self.saver = Saver()    # 存储器实例

    # 执行函数
    def start(self):
        page = 1
        all_code = []
        while page <= 181:
            print("当前为第%s页" % page)
            time.sleep(random.random()*2)
            self.downloader = Downloader(self.url % page)
            page += 1
            html_content = self.downloader.download()
            self.parser = Parser(html_content)
            codeList = self.parser.parseSharesCode()
            for code in codeList:
                all_code.append(code)
            # print(codeList)
            self.saver.saveKlineToCSV(codeList=codeList, filepath=self.kline_filepath, date=self.date)
        self.save(codelist=all_code)

    def save(self,codelist):
        self.saver.saveCodeListToCSV(codeList=codelist, filepath=self.codelist_filepath)  # 保存代码到csv
        # self.saver.saveKlineToMySQL(filepath=self.kline_filepath)                         # 保存所有股票的信息到数据库


# 程序入口
if __name__ == '__main__':
    url = 'https://hq.gucheng.com/HSinfo.html?en_hq_type_code=&sort_field_name=px_change_rate&sort_type=desc&page=%s'   # 股城网>行情>沪深A股
    kline_filepath = 'F:\\files\\sharesDatas\\dayline\\'                  # 定义数据文件保存路径
    codelist_filepath = 'F:\\files\\sharesDatas\\code_list\\'             # 定义数据文件保存路径
    controller = Controller(url=url, kline_filepath=kline_filepath, codelist_filepath=codelist_filepath, date='20190428')
    controller.start()
```
项目地址: [https://github.com/CatsJuice/netease-stock-day-line](https://github.com/CatsJuice/netease-stock-day-line))

`或者:`
```
git clone https://github.com/CatsJuice/netease-stock-day-line.git
``` 

**网易财经财务数据**

网页url为[http://quotes.money.163.com/f10/zycwzb_601318.html#01c01](http://quotes.money.163.com/f10/zycwzb_601318.html#01c01)， 可以看到同交易日线数据一样， 这里有一个下载数据的按钮， 对应`盈利能力`， `偿还能力`等， 而这里直接通过开发者工具审查元素， 即可看到超链接的`href`：`/service/zycwzb_601318.html?type=report&part=ylnl`， 很快， 就能拿到完整的`url`， 然后操作同网易日线数据， 项目地址同样位于[https://github.com/CatsJuice/netease-stock-day-line](https://github.com/CatsJuice/netease-stock-day-line))




### 1.2.3. **东方财富**

**股票信息列表**

因为没找到合适的页面， 实时资金流向排行[http://data.eastmoney.com/zjlx/detail.html](http://data.eastmoney.com/zjlx/detail.html)爬取所有沪深A股代码， 这里不作详述；

**交易数据, 财务数据**

东方财富的交易数据/财务数据我也尝试过使用爬虫爬取， 但是还是要走不少弯路的， 而且可能最后还没成功， 首先， 东方财富的数据页面和其他平台一样， 股票代码在url中， 如[http://data.eastmoney.com/bbsj/yjbb/600175.html](http://data.eastmoney.com/bbsj/yjbb/600175.html)， 直接爬取， 或者使用开发者工具定位页面元素会发现：


![东方财富页面元素审查](https://catsjuice.cn/index/src/markdown/stock/201904271950.png "东方财富页面元素审查")

对应的数据是乱码的， 而如果继续挖掘其js文件， 是可以找到有加密的函数的，如下图所示：

![东方财富js加密](https://catsjuice.cn/index/src/markdown/stock/201904271953.png "东方财富js加密")

在这个名字特别明显直白的`js`文件`load_table_data_pc.js?201606021831`中, 可以看到加密， 解密的方法， 这也使得爬取成为可能， 但是太大费周章暂不考虑。




# 2. **热点获取**

## 2.1. **东方财富**

这里我抓取的是东方财富的**国内经济要闻**， 页面地址位于[http://finance.eastmoney.com/news/cgnjj.html](http://finance.eastmoney.com/news/cgnjj.html)

这一页面的新闻列表不是通过Ajax异步加载的， 可以直接抓取， 而页码由url中`cgnjj_x`(x为页码)决定，抓取过程较为简单；这里统计的思路是， 将所有的要闻简述拼接成字符串（以`;`分隔并写入txt作备份）， 然后利用`jieba`库分词并统计词频， 项目地址：[https://github.com/CatsJuice/eastmoney-yaowen-keyword](https://github.com/CatsJuice/eastmoney-yaowen-keyword)

也可以直接`clone`至本地
```
git clone https://github.com/CatsJuice/eastmoney-yaowen-keyword.git
```

# 3. **数据分析**

在证券投资中， 有很多技术层面的投资策略， 如各种公式， 通过策略可以筛选出股票， 但并不意味着一定能盈利， 而量化投资可以验证这一策略的可靠性， 接下来就是我对若干策略的验证；

## 3.1. **换手率分析**

分析**连续处于低换手率**的股票， 脱离低换手率后， 出现**连续处于高换手率**， 判断2个时期的**收盘价**均价， 分析满足这一特征的股票的价格是否会上涨；

程序设计的思路如下
- 迭代日线数据文件
- 判断是否是连续高换手率
- 判断是否在连续高换手率后出现连续低换手率
- 结果展示

**注:** 因为数据文件是按日期的倒序排序的， 所以分析迭代时， 先判断是否出现连续高换手率

核心代码如下：
```
high = []
low = []
for row in arr:
    rate = row[10]
    if rate == "None":
        high = []
        low = []
        continue
    if rate == 0:
        if len(high) >= self.min_days and len(low) >= self.min_days:
            dic = {'high': high, 'low': low}
            self.res.append(dic)
            high = []
            low = []
        continue
    rate = float(rate)
    # 4. 判断是否是 高 换手率
    if rate >= self.border_rate:   
        # 4.3 判断是否是: 连续高 ->连续低 -> 结束连续低
        if len(low) >= self.min_days:
            # 符合条件， 写入
            dic = {'high': high, 'low': low}
            self.res.append(dic)
            high = []
            low = []
        elif len(low) > 0:      # 连续低中有值
            # 不满足连续 低， 重置
            high = []
            low = []
        high.append(row)
    elif len(high) < self.min_days:     # 是低换手率， 判断是否前面是连续高换手率
        # 4.1 前面不是连续的高换手率， 重置高换手率数组
        high = []
    else:
        # 4.2 前面是连续高换手率， 地换手率写入
        low.append(row)
    
    # 判断日期是否已达到最后
    if row[0] <= self.end_date:
        # 判断是否满足条件
        if len(high) >= self.min_days and len(low) >= self.min_days:
            dic = {'high': high, 'low': low}
            self.res.append(dic)
            high = []
            low = []
        break
```

简易流程图如下：

![流程图](https://catsjuice.cn/index/src/markdown/stock/mind201905012008.jpg "换手率程序设计流程图")

[点此查看流程图原图](https://catsjuice.cn/index/src/markdown/stock/mind201905012008_origin.png)

程序可调整参数如下：

No | param | type | meaning | demo
:--:|:--:|:--: |:--: |:--:
1 | `file_path_prefix` | `str` | 日线数据目录前缀 | `'F:\\files\\sharesDatas\\kline\\'`
2 | `min_days` |  `int` | 最小连续天数 | `10`
3 | `border_rate` | `float` | 换手率高低边界 | `2`
4 | `end_date` | `str` | 统计的最早日期 | `'2018-12-28'`

程序运行结果截图如下：201905012052.png

![screenshot](https://catsjuice.cn/index/src/markdown/stock/201905012052.png)

完整项目地址：[https://github.com/CatsJuice/low-switch-hand-rate](https://github.com/CatsJuice/low-switch-hand-rate)

或者：
```
git clone https://github.com/CatsJuice/low-switch-hand-rate.git
```

## 3.2. **CCI指标**

### 3.2.1. **概念**

> 顺势指标又叫CCI指标，CCI指标是美国股市技术分析 家唐纳德·蓝伯特(Donald Lambert)于20世纪80年代提出的，专门测量股价、外汇或者贵金属交易是否已超出常态分布范围。属于超买超卖类指标中较特殊的一种。波动于正无穷大和负无穷大之间。但是，又不需要以0为中轴线，这一点也和波动于正无穷大和负无穷大的指标不同。

### 3.2.2. **指标用法**

> `1`. 当`CCI`指标曲线在`+100`线～`-100`线的常态区间里运行时,`CCI`指标参考意义不大，可以用KDJ等其它技术指标进行研判。
> 
> `2`. 当`CCI`指标曲线从上向下突破`+100`线而重新进入常态区间时，表明市场价格的上涨阶段可能结束，将进入一个比较长时间的震荡整理阶段，应及时平多做空。
>
> `3`. 当`CCI`指标曲线从上向下突破`-100`线而进入另一个非常态区间（超卖区）时，表明市场价格的弱势状态已经形成，将进入一个比较长的寻底过程，可以持有空单等待更高利润。如果`CCI`指标曲线在超卖区运行了相当长的一段时间后开始掉头向上，表明价格的短期底部初步探明，可以少量建仓。`CCI`指标曲线在超卖区运行的时间越长，确认短期的底部的准确度越高。
>
> `4`. `CCI`指标曲线从下向上突破`-100`线而重新进入常态区间时，表明市场价格的探底阶段可能结束，有可能进入一个盘整阶段，可以逢低少量做多。
>
> `5`. `CCI`指标曲线从下向上突破`+100`线而进入非常态区间(超买区)时，表明市场价格已经脱离常态而进入强势状态，如果伴随较大的市场交投，应及时介入成功率将很大。
>
> `6`. `CCI`指标曲线从下向上突破`+100`线而进入非常态区间(超买区)后，只要`CCI`指标曲线一直朝上运行，表明价格依然保持强势可以继续持有待涨。但是，如果在远离`+100`线的地方开始掉头向下时，则表明市场价格的强势状态将可能难以维持，涨势可能转弱，应考虑卖出。如果前期的短期涨幅过高同时价格回落时交投活跃，则应该果断逢高卖出或做空。

### 3.2.3. **公式**

关于公式有两种：

**公式一：**
```
TYP:=(HIGH+LOW+CLOSE)/3;
CCI:(TYP-MA(TYP,N))/(0.015*AVEDEV(TYP,N));
```
该公式来自`通达信`， 含义如下：
```
TYP赋值:(最高价+最低价+收盘价)/3
输出CCI:(TYP-TYP的N日简单移动平均)/(0.015*TYP的N日平均绝对偏差)
```
**公式二：**

![cci公式](https://catsjuice.cn/index/src/markdown/stock/201905022239.png "cci")

该公式摘录于[https://www.joinquant.com/view/community/detail/219](https://www.joinquant.com/view/community/detail/219)

程序中我采用的是**公式二**， 生成的CCI单独写入`.csv`文件

### 3.2.4. **程序设计**

**总共设计了4个类如下：**

- `CCIAnalyze(object)` &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp;//&ensp;&ensp;CCI分析类
    - `analyze_all(self)` &emsp;&emsp;&emsp;&emsp;//&ensp;&ensp;分析所有股票
    - `analyze_one(self)` &emsp;&emsp;&emsp;&emsp;//&ensp;&ensp;分析一只股票
    - `test_buy(self)` &emsp;&emsp;&emsp;&emsp;&emsp;&ensp;//&ensp;&ensp;测试购买（策略一：在下文说明）
    - `test_buy_2(self)` &emsp;&emsp;&emsp;&emsp;&ensp;//&ensp;&ensp;测试购买（策略二：在下文说明）
- `CCICalculate(object)` &emsp;&emsp;&emsp;&emsp;&emsp;//&ensp;&ensp;根据[3.2.3. **公式**](#323-公式)中的公式二计算CCI
- `CCICalculate_2(object)`&emsp;&emsp;&emsp;&emsp;//&ensp;&ensp;根据[3.2.3. **公式**](#323-公式)中的公式一计算CCI
- `BuyInfo(object)`&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;//&ensp;&ensp;购买辅助类


### 3.2.5. **参数说明**

id | param | type | default | mean | demo | necessary
:--: | :--: | :--: | :--: | :--: |:--: | :--:
1 | `file_path_prefix` | `str` | `None` | 日线数据文件目录前缀 | `'F:\\files\\sharesDatas\\kline\\'` | `True`
2 | `cci_path_prefix` | `str` | `None` | CCI数据将要保存的目录前缀 | `'F:\\files\\sharesDatas\\cci\\'` | `True`
3 | `code` | `str` / `int` | `None` | 股票代码  | `000001` | `False`
4 | `end_date` | `str` | `'0000-00-00'` | 最早的日期（截止日期） | `'2009-01-01'` | `False`

> 项目地址： [https://github.com/CatsJuice/stock-cci](https://github.com/CatsJuice/stock-cci)

或者`clone`:
```
git clone https://github.com/CatsJuice/stock-cci
```

### 3.2.6. **测试结果**

在进行测试购买时， 采用的策略有2种，如下：

**策略一：**

对应`CCIAnalyze(object)`中的`test_buy(self)`方法， 当`CCI`向下突破`-100`时， 后一交易日**买入**， 等到`CCI`向上突破`100`时， 后一交易日**卖出**(由于计算出当日的CCI， 当日已不可**买入/卖出**，所以计算后一交易日**买入/卖出**)

**策略二：**

对应`CCIAnalyze(object)`中的`test_buy_2(self)`方法, 当`CCI`向下突破`-100`时， 继续观察， 等到向下达到第一个峰值时， 后一交易日**买入**，等到`CCI`向上突破`100`时， 继续观察， 等到向上达到第一个峰值时， 后一交易日**卖出**（和策略一一样， 计算出`CCI`后只能后一日买入， 而策略二不同的是， 要判断达到第一个峰值， 必须确定峰值后一日CCI降低， 所以测试时， 购买的是峰值的后`第2个`交易日）

经过若干次测试，分别使用的数据是`2019-01-01 ~ 2019-04-26`, `2018-01-01 ~ 2019-04-26`, `2017-01-01 ~ 2019-04-26`, 测试的结果, 购买策略一的收益率大概为`56%`, 可见这个指标有一定依据， 但是盈利的几率不够高; 而策略二的收益率大概为`57%`, 较策略一高一点， 但是依旧无法将其作为购买的唯一指标， 以下是某次运行结果的部分截图：

![CCI指标测试截图](https://catsjuice.cn/index/src/markdown/stock/201905031521.png "CCI指标测试截图")

## 3.3. **《胡立阳股票投资100招》** 由“价量关系”来为个股打分数

### 3.3.1. **概念**

胡立阳根据股票的`价量关系`对股票进行打分（第21招）， 而其打分的依据如下：

> **当日个股表现：**
> 
>（1）价涨量增 `+2` 分
>
>（2）价涨量缩 `+1` 分
>
>（3）价跌量增 `-2` 分
>
>（4）价跌量缩 `-1` 分
>
> 每日累计评分。你只要连续计算一个星期,以最高分或者是评分稳定增加的作为你投资的第一选择,因为那只个股具备了“价量配合”的上涨条件


### 3.3.2. **程序设计**

根据以上打分标准， 不难计算每只股票某日的得分， 然后将所有得分排序即可， 这样能够得到某一天的所有股票打分， 而需要验证每只股票是否盈利， 这里我简单地判断后一天收盘价是否比当天高，所以能够得出每只股票是否盈利， 计算出当天得分前`n`只股票的盈利与否， 再用 盈利股票的只数 / n 即当天的盈利率；

以此类推`m`天可得出m天这一策略的盈利几率

程序流程图如下：

![StockScore_2.py简易流程图](https://catsjuice.cn/index/src/markdown/stock/mind201904281842.jpg "StockScore_2.py简易流程图")

[点击此处查看流程图原图](https://catsjuice.cn/index/src/markdown/stock/mind201904281842.jpeg)


参数详情如下表

id | param | type | mean | demo
:--: | :--: | :--: | :--: | :--:
1 | `prefix` | str | 网易财经日线数据文件前缀 | `'F:\\files\\sharesDatas\\kline\\' `
2 | `date_now` |str| 最新数据的第一个日期, 对应爬取的最新数据表第一行的日期 | `'2019-04-26'` ; Format( `yyyy-mm-dd` )
3 | `days` | int | 加分的天数 | `5`
4 | `calculate_days` | int | 要统计的天数 | `20`
5 | `best_num` | int | 选出的最佳的天数 | `10`

### 3.3.3. **结果**

![胡立阳打分标准盈利率](https://catsjuice.cn/index/src/markdown/stock/201904281250.png "胡立阳打分标准盈利率")


> **键值说明:**
> 
> `date`: 统计的日期
>
> `rate_low`: 打分最低的盈利几率
>
> `rate_high`: 打分最高的盈利几率

在分别统计了每个分数的股票的盈利率， 和排名靠前的股票的盈利率来看， 胡立阳的打分欠缺科学性， 盈利率不稳定， 不建议做参考；

### 3.3.4. **Source Code**

项目地址： [https://github.com/CatsJuice/stock-price-num-score](https://github.com/CatsJuice/stock-price-num-score)

直接`clone`:
```
git clone https://github.com/CatsJuice/stock-price-num-score.git
```

