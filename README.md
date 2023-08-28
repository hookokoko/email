email
=====

Fork From [jordan-wright/email](https://github.com/jordan-wright/email)

现有的go-mail实现连接池 有一些问题
1. 没有Stat()统计方法
2. 没有最大空闲连接的参数
3. 创建连接之后，没有空闲到一定时间自动关闭的功能，即创建之后一直占着连接资源，即使没有发送邮件的请求

参考[go-redis](https://github.com/redis/go-redis/blob/master/internal/pool/pool.go)的连接池实现了新的实现


### Email for humans
The ```email``` package is designed to be simple to use, but flexible enough so as not to be restrictive. The goal is to provide an *email interface for humans*.

The ```email``` package currently supports the following:
*  From, To, Bcc, and Cc fields
*  Email addresses in both "test@example.com" and "First Last &lt;test@example.com&gt;" format
*  Text and HTML Message Body
*  Attachments
*  Read Receipts
*  Custom headers
*  More to come!

### Installation
```go get github.com/jordan-wright/email```

*Note: Version > 1 of this library requires Go v1.5 or above.*

*If you need compatibility with previous Go versions, you can use the previous package at gopkg.in/jordan-wright/email.v1*

### Examples
#### Sending email using Gmail
```go
e := email.NewEmail()
e.From = "Jordan Wright <test@gmail.com>"
e.To = []string{"test@example.com"}
e.Bcc = []string{"test_bcc@example.com"}
e.Cc = []string{"test_cc@example.com"}
e.Subject = "Awesome Subject"
e.Text = []byte("Text Body is, of course, supported!")
e.HTML = []byte("<h1>Fancy HTML is supported, too!</h1>")
e.Send("smtp.gmail.com:587", smtp.PlainAuth("", "test@gmail.com", "password123", "smtp.gmail.com"))
```

#### Another Method for Creating an Email
You can also create an email directly by creating a struct as follows:
```go
e := &email.Email {
	To: []string{"test@example.com"},
	From: "Jordan Wright <test@gmail.com>",
	Subject: "Awesome Subject",
	Text: []byte("Text Body is, of course, supported!"),
	HTML: []byte("<h1>Fancy HTML is supported, too!</h1>"),
	Headers: textproto.MIMEHeader{},
}
```

#### Creating an Email From an io.Reader
You can also create an email from any type that implements the ```io.Reader``` interface by using ```email.NewEmailFromReader```.

#### Attaching a File
```go
e := NewEmail()
e.AttachFile("test.txt")
```

#### A Pool of Reusable Connections
```go
(var ch <-chan *email.Email)
p := email.NewPool(
	"smtp.gmail.com:587",
	4,
	smtp.PlainAuth("", "test@gmail.com", "password123", "smtp.gmail.com"),
)
for i := 0; i < 4; i++ {
	go func() {
		for e := range ch {
			p.Send(e, 10 * time.Second)
		}
	}()
}
```

### Documentation
[http://godoc.org/github.com/jordan-wright/email](http://godoc.org/github.com/jordan-wright/email)

### Other Sources
Sections inspired by the handy [gophermail](https://github.com/jpoehls/gophermail) project.

### Contributors
I'd like to thank all the [contributors and maintainers](https://github.com/jordan-wright/email/graphs/contributors) of this package.

A special thanks goes out to Jed Denlea [jeddenlea](https://github.com/jeddenlea) for his numerous contributions and optimizations.
