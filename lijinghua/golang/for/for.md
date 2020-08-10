# **1、for循环的用法**

## **（1）常规用法**

```go
func main() {
    slice := []int{1, 2, 3, 4, 5, 6}
    //方式1
    for i := 0; i < len(slice); i++ {
        if i < 10 {
            slice = append(slice, 0)
        }
        fmt.Printf("%v ", slice[i]) //1 2 3 4 5 6 0 0 0 0 0 0 0 0 0 0
    }
    fmt.Println()
    //方式2
    slice = slice[:6]
    n := len(slice)
    for i := 0; i < n; i++ {
        fmt.Printf("%v ", slice[i]) //1 2 3 4 5 6
    }
    fmt.Println()
}
```

方式1每次循环都会执行len函数，适用于动态循环（循环出口是动态的）；

方式2适用于固定循环（一般为固定循环次数），固定循环不推荐使用方式1（如果循环出口是一个耗时的操作，会影响效率）；　

## **（2）死循环**

```go
func main() {
    slice := []int{1, 2, 3, 4, 5, 6}
    for {
        slice = append(slice, 0)
        if len(slice) > 10 {
            break
        }
    }
    fmt.Println(slice) //[1 2 3 4 5 6 0 0 0 0 0]
}
```

死循环一般用于监听操作，或者循环出口不唯一，需配合break结束循环。

## **（3）for...range**

```go
func main() {
    slice := []int{1, 2, 3, 4, 5, 6}
    for i := range slice {
        fmt.Printf("%d ", i) //0 1 2 3 4 5
    }
    fmt.Println()
    for i, v := range slice {
        fmt.Printf("slice[%d]=%d ", i, v)
        //slice[0]=1 slice[1]=2 slice[2]=3 slice[3]=4 slice[4]=5 slice[5]=6
    }
}
```

for...range支持迭代 数组、切片、字符串、map、只读channel或可读可写channel；

for...range迭代类型不同返回值不同，数组、切片、字符串返回下标和值，map返回键和值，channel返回值；　　

# **2、for与for range在遍历字符串时的差别**

```go
func main() {
    str := "abcd笃志弘毅"
    n := len(str)
    for i := 0; i < n; i++ {
        fmt.Printf("%c", str[i]) //abcdç¬å¿å¼æ¯
    }
    fmt.Println()
    for i, v := range str {
        fmt.Printf("str[%d]=%c", i, v)
        //str[0]=astr[1]=bstr[2]=cstr[3]=dstr[4]=笃str[7]=志str[10]=弘str[13]=毅
    }
}
```

for遍历字符串时是按字节（byte）进行遍历，超出一个字节的编码字符会出现乱码；

for...range遍历字符串时是按字符（rune）进行遍历；　　

# **3、for...range注意点**

## **（1）迭代切片时相当于遍历切片副本，原始切片的append或再切片不影响整个迭代**

```go
func main() {
    slice := []int{1, 2, 3, 4, 5, 6}
    for i, v := range slice {
        //迭代切片相当于temp:=slice[:]，遍历temp切片
        //slice的append或再切片slice[1:2]不影响整个迭代
        slice = append(slice, 0)
        slice = slice[0:len(slice)]
        fmt.Printf("slice[%d]=%d ", i, v)
        //slice[0]=1 slice[1]=2 slice[2]=3 slice[3]=4 slice[4]=5 slice[5]=6
    }
    fmt.Println("\n", slice) // [1 2 3 4 5 6 0 0 0 0 0 0]
}
```

## **（2）值类型的迭代修改不影响原始数据，指针类型的迭代修改会影响原始数据**

```go
func main() {
    //切片
    slice := []int{1, 2, 3, 4, 5, 6}
    for _, v := range slice {
        v += v
    }
    fmt.Println(slice) //[1 2 3 4 5 6]
    //map
    m := map[int]*int{}
    m[1] = new(int)
    m[2] = new(int)
    *m[1] = 1
    *m[2] = 2
    fmt.Println(*m[1], *m[2]) //1 2
    for _, v := range m {
        *v++
    }
    fmt.Println(*m[1], *m[2]) //2 3
}
```

##  （3）for...range迭代map时**

》访问是随机的，遍历结果可能不一样；

```go
func main() {
    m := map[int]int{0: 0, 1: 1, 2: 2, 3: 3, 4: 4}
    for k, v := range m {
        fmt.Printf("%v %v,", k, v) //2 2,3 3,4 4,0 0,1 1,
    }
}
```

》可以正常进行删除操作，因为迭代使用的是拷贝；

```go
func main() {
    m := map[int]*int{}
    m[1] = new(int)
    m[2] = new(int)
    *m[1] = 1
    *m[2] = 2
    fmt.Println(*m[1], *m[2]) //1 2
    for k, v := range m {
        delete(m, k)
        *v++
        fmt.Printf("%v ", *v) //2 3
    }
}
```

可以正常进行添加操作，由于map底层是哈希表，哈希表的元素不具备顺序性，所以新添加的元素可能不会出现在后续的迭代中；

```go
func main() {
    m := map[int]string{1: "一", 2: "二"}
    for k, v := range m {
        m[k+2] = "测试"
        fmt.Println(k, v)
    }
    fmt.Println(m)
}
```