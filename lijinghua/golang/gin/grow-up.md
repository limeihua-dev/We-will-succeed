## 接收前端json对

```go
type LoginAuth struct {
	// binding:"required"修饰的字段，若接收为空值，则报错，是必须字段
	Username string `form:"username" json:"username" uri:"username" xml:"username" binding:"required"`
	Pssword  string `form:"password" json:"password" uri:"password" xml:"password" binding:"required"`
}
	var LoginPayload LoginAuth
	if err := c.ShouldBindJSON(&LoginPayload); err != nil {
		c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		return
	}
```

