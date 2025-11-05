# pytest

## 简单数据驱动

接口测试用例.xls：

名称  接口URL                请求方式      URL接口参数            JSON参数

登录  https://.../login      POST         {"application":"web"}   {"username":"xiaoming","pwd":"12346"}

```
test_data=xfile.read("接口测试用例.xls").excel_to_dict(sheet=1)

//@pytest.mark.paramtrize 会循环使用test_exceute函数读取每行的数据，循环次数就是行数，即test_data的列长度。每次循环就是把test_data的行数据赋值给case_info

//eval()：根据字符串的格式，自动把字符串变成对应的数据结构

//接口之间有关联性：向关联接口赋值
dic = {}
@pytest.mark.paramtrize("case_info",test__data)
def test_exceute(case_info):
  resp1=requests.request(url=case_info["接口URL"]，
    method=case_info["请求方式"]，
    params=eval(case_info["URL接口参数"])，
    data=eval(case_info["JSON参数"])，
    )
   print(resp1)
   if case_info["提取参数"]:
      lst=jsonpath.jsonpath(resp1.json(),"$.."+case_info["提取参数"])
      dict[case_info["提取参数"]]=lst[0]
```
