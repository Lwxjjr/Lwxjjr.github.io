+++
date = '2024-10-23T19:59:04+08:00'
draft = true
title = 'UseRedigo'
categories = 'Redis'
+++
操作redigo前，需要下载第三方包
```cli
go get github.com/gomoudle/redigo/redis
```
## 1. 使用Args接口操作批量操作
```go
type Args []interface{}
// types Args，Args的作用可以组织redis一系列参数保证它们以正确的顺序和格式传递给redis命令

func (args Args) Add(value ...interface{}) Args

func (args Args) AddFlat(v interface{}) Args
/* 
将v的值扁平化添加到 args 的结果里面，
其实就是将复合数据结构（struct, slices...）中的元素一一添加到 args 这个参数列表中
*/
```
官方文档里面的example有hmset的使用
```go
// 连接redis
c, err := dial()
if err != nil {
	fmt.Println(err)
	return
}
defer c.Close()

// 初始化构造实际场景下的hash结构体
var p1 struct {
	Title  string `redis:"title"`
	Author string `redis:"author"`
	Body   string `redis:"body"`
}

p1.Title = "Example"
p1.Author = "Gary"
p1.Body = "Hello"

if _, err := c.Do("HMSET", redis.Args{}.Add("id1").AddFlat(&p1)...); err != nil {
	fmt.Println(err)
	return
}

m := map[string]string{
	"title":  "Example2",
	"author": "Steve",
	"body":   "Map",
}

if _, err := c.Do("HMSET", redis.Args{}.Add("id2").AddFlat(m)...); err != nil {
	fmt.Println(err)
	return
}

for _, id := range []string{"id1", "id2"} {

	v, err := redis.Values(c.Do("HGETALL", id))
	if err != nil {
		fmt.Println(err)
		return
	}

	if err := redis.ScanStruct(v, &p2); err != nil {
		fmt.Println(err)
		return
	}

	fmt.Printf("%+v\n", p2)
}


-----------------------------------------------------------------------------------------
Output:

{Title:Example Author:Gary Body:Hello}
{Title:Example2 Author:Steve Body:Map}
```
# 2.que
22222

# 3.sss
11111