## 第一步
Win + R 输入 eventvwr.msc 进入[事件查看器]
## 第二步

- 打开 Windows 日志
- 点击安全
- 点击筛选当前日志
- 点击xml
- 勾选手动编辑查询
## 第三步
将下列查询条件复制替换

``` xml
<QueryList>
  <Query Id="0" Path="Security">
    <Select Path="Security">
*[EventData[Data[@Name='LogonType']='2']]
and
*[System[(EventID='4624')]]
</Select>
  </Query>
</QueryList>
```