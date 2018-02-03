# package http
```go
import "net/http"
```
> http 包提供了 HTTP 客户端和服务端的实现

```go
const (
    StatusContinue           = 100
    StatusSwitchingProtocols = 101
    StatusOK                   = 200
    StatusCreated              = 201
    StatusAccepted             = 202
    StatusNonAuthoritativeInfo = 203
    StatusNoContent            = 204
    StatusResetContent         = 205
    StatusPartialContent       = 206
    StatusMultipleChoices   = 300
    StatusMovedPermanently  = 301
    StatusFound             = 302
    StatusSeeOther          = 303
    StatusNotModified       = 304
    StatusUseProxy          = 305
    StatusTemporaryRedirect = 307
    StatusBadRequest                   = 400
    StatusUnauthorized                 = 401
    StatusPaymentRequired              = 402
    StatusForbidden                    = 403
    StatusNotFound                     = 404
    StatusMethodNotAllowed             = 405
    StatusNotAcceptable                = 406
    StatusProxyAuthRequired            = 407
    StatusRequestTimeout               = 408
    StatusConflict                     = 409
    StatusGone                         = 410
    StatusLengthRequired               = 411
    StatusPreconditionFailed           = 412
    StatusRequestEntityTooLarge        = 413
    StatusRequestURITooLong            = 414
    StatusUnsupportedMediaType         = 415
    StatusRequestedRangeNotSatisfiable = 416
    StatusExpectationFailed            = 417
    StatusTeapot                       = 418
    StatusInternalServerError     = 500
    StatusNotImplemented          = 501
    StatusBadGateway              = 502
    StatusServiceUnavailable      = 503
    StatusGatewayTimeout          = 504
    StatusHTTPVersionNotSupported = 505
)
```

```go
const DefaultMaxHeaderBytes = 1 << 20 1M
```
> DefaultMaxHeaderBytes 是HTTP 请求的头域最大允许长度。可以通过设置Server.MaxHeaderBytes 字段来覆盖

```go
const DefaultMaxIdleConnsPerHost = 2
```
> DefaultMaxIdleConnsPerHost 是 Transport 的 MaxIdleConnsPerHost 默认值

```go
const TimeFormat = "Mon, 02 Jan 2006 15:04:05 GMT"
```
> TimeFormat 是当解析或生产 HTTP 头域中的时间， 用与 time.Parse 或 time.Promat 函数的时间格式。这种格式类似 time.RFC1123 但强制采用 GMT 时区

```go
var (
    ErrHeaderTooLong        = &ProtocolError{"header too long"}
    ErrShortBody            = &ProtocolError{"entity body too short"}
    ErrNotSupported         = &ProtocolError{"feature not supported"}
    ErrUnexpectedTrailer    = &ProtocolError{"trailer header without chunked transfer encoding"}
    ErrMissingContentLength = &ProtocolError{"missing ContentLength in HEAD response"}
    ErrNotMultipart         = &ProtocolError{"request Content-Type isn't multipart/form-data"}
    ErrMissingBoundary      = &ProtocolError{"no multipart boundary param in Content-Type"}
)
```
> HTTP 请求的解析错误。

```go
var (
    ErrWriteAfterFlush = errors.New("Conn.Write called after Flush")
    ErrBodyNotAllowed  = errors.New("http: request method or response status code does not allow body")
    ErrHijacked        = errors.New("Conn has been hijacked")
    ErrContentLength   = errors.New("Conn.Write wrote more than the declared Content-Length")
)
```
> 会被HTTP服务端返回的错误

```go
var DefaultClient = &Client{}
```
> DefaultClient 是用于包 Get、Head 和 Post 的默认 Client。

```go
var DefaultServeMux = NewServeMux()
```
> DefaultServeMux 是用于 Serve 的默认 ServeMux。

```go
var ErrBodyReadAferClose = errors.New("http: invalid Read on closed Body")
```
> 在 Resques 或 Response 的 Body 字段已经关闭后，试图从中读取时，就会返回 ErrBodyReadAfterClose。这个错误一般发生在： HTTP 处理器中调用完 ResponseWriter 接口的 WriteHeader 或 Write 后从请求中读取数据的时候。

```go
var ErrHandlerTimeout = errors.New("http: Handler timeout")
```
> 在处理器超时以后调用 ResponseWriter 接口的 Write 方法，就会返回 ErrHandlerTimeout。

```go
var ErrLineTooLong = errors.New("header line too long")
```

```go
var ErrMissingFile = errors.New("http: no such file")
```
> 当请求中没有提供给 FormFile 函数的文件字段名，或者该字段名不是文件字段时，该函数就会返回 ErrMissingFile

```go
var ErrNoCookie = errors.New("http: named cookie not present")
var ErrNoLocation = errors.New("http: no Location header in response")
```

## type ProtocolError
```go
type ProtocalError struct {
	ErrorString string
}
```
> HTTP 请求解析错误。

## func (*ProtocalError)Error 
```go
func (err *ProtocalError)Error()string
```

## func CanonicalHeaderKey
```go
func CanonicalHeaderKey(s string)string
```
> CanonicalHeaderKey 函数返回头域(表示为 Header 类型)的键 s 的规划化格式。规划化过程中让单词首字母和 ‘-’ 后的第一个字母大写，其余字母小写。例如，"accept-encoding"规范化为"Accept-Encoding"。

## func DetectContentType
```go
func DetectContentType(data []byte)string
```
> DetectContentType 函数实现了 http://mimesniff.spec.whatwg.org/ 描述的算法，用于确定数据的 Content-Type。函数勇士返回一个合法的 MIME 类型；如果它不能确定数据的类型，将返回 “application/octet-stream”。它最多检查数据的前 512 字节

## func ParseHTTPVersion
```go
func ParseHTTPVersion(vers string)(major, minor int, ok bool)
```
> ParseHTTPVersion 解析 HTTP 版本字符串。如 “HTTP/1.0” 返回 (1,0,true)

## func ParseTime
```go
func ParseTime(text string)(t time.Time, err error)
```
> ParseTime 用3种格式 TimeFormat, time.RFC850和time.ANSIC尝试解析一个时间头的值（如Date: header）。

## func StatusText
```go
func StatusText(code int)string
```
> StatusText 返回 HTTP 状态码 code 对应的文本，如220对应"OK"。如果 code 是未知的状态码，会返回""。

## type ConnState
```go
type ConnState int
```
> ConnState 代表一个客户端到服务端的链接的状态。本类型用于可选的 Server.ConnState 回调函数。

```go
const (
    // StateNew代表一个新的连接，将要立刻发送请求。
    // 连接从这个状态开始，然后转变为StateAlive或StateClosed。
    StateNew ConnState = iota
    // StateActive代表一个已经读取了请求数据1到多个字节的连接。
    // 用于StateAlive的Server.ConnState回调函数在将连接交付给处理器之前被触发，
    // 等到请求被处理完后，Server.ConnState回调函数再次被触发。
    // 在请求被处理后，连接状态改变为StateClosed、StateHijacked或StateIdle。
    StateActive
    // StateIdle代表一个已经处理完了请求、处在闲置状态、等待新请求的连接。
    // 连接状态可以从StateIdle改变为StateActive或StateClosed。
    StateIdle
    // 代表一个被劫持的连接。这是一个终止状态，不会转变为StateClosed。
    StateHijacked
    // StateClosed代表一个关闭的连接。
    // 这是一个终止状态。被劫持的连接不会转变为StateClosed。
    StateClosed
)
```

## func (ConnState)String 
```go
func (c ConnState)String()string
```

## type Header
```go
type Header map[string][]string
```
> Header 代表 HTTP 头域的键值对

## func (Header)Get
```go
func (h Header)Get(key string)string
```
> Get 返回键对应的第一个值，如果键不存在会返回“”，如果获取该键对应的值切片，请直接用规范格式的键访问 map

## （Header)Set
```go
func (h Header)Set(key, value string)
```
> Set 添加键值对到 h，如果键已存在则会用只有新值一个元素的切片取代旧值切片。

## func (Header)Add
```go
func (h Header)Add(key, value string)
```
> Add 添加键值对到 h,如果已存在则会将新的值附加到旧的切片后面

## func (Header)Del
```go
func (h Header)Del(key string)
```
> Del 删除键值对

## func (Header)Write
```go
func (h Header)Write(w io.Writer)error
```
> Write 以有线格式将头域写入 w

## func (Header)WriteSubset
```go
func (h Header)WriteSubset(w io.Writer, exclude map[string]bool)error
```
> WriteSubset 以有线格式将头域写入 w。当 exclude 不为 nil 时，如果 h 的键值对的键在 exclude 中存在且对应值为真，该键值对就不会被写入 w

## type Cookie 
```go
type Cookie struct {
    Name       string
    Value      string
    Path       string
    Domain     string
    Expires    time.Time
    RawExpires string
    // MaxAge=0表示未设置Max-Age属性
    // MaxAge<0表示立刻删除该cookie，等价于"Max-Age: 0"
    // MaxAge>0表示存在Max-Age属性，单位是秒
    MaxAge   int
    Secure   bool
    HttpOnly bool
    Raw      string
    Unparsed []string // 未解析的“属性-值”对的原始文本
}
```
> Cookie 代表一个出现在 HTTP 回复的头域中 Set-Cookie 头的值里或者 HTTP 请求的头域中 Cookie 头的值里的 HTTP cookie

## func (*Cookie)String
```go
func (c *Cookie)String()string
```
> String 返回该 cookie 的序列化结果。如果只设置了 Name 和 Value 字段，序列化结果可用于 HTTP 请求的 Cookie 头或者 HTTP 回复的 Set-Cookie 头；吐过设置了其他字段，序列化结果只能用于 HTTP 回复的 Set-Cookie 头；如果设置了其他字段，序列化结果只能用于 HTTP 回复的 Set-Cookie 头。

## type CookieJar
```go
type CookieJar interface {
	// SetCookies管理从u的回复中收到的cookie
    // 根据其策略和实现，它可以选择是否存储cookie
    SetCookies(u *url.URL, cookies []*Cookie)
    // Cookies返回发送请求到u时应使用的cookie
    // 本方法有责任遵守RFC 6265规定的标准cookie限制
    Cookies(u *url.URL) []*Cookie
}
```
> CookieJar 管理 cookie 的存储和在 HTTP 请求中的使用。CookieJar 的实现必须能安全的被多个 go 程同时使用。是 CookieJar 的实现

## type Request 
```go
type Request struct {
	// Method 指定 HTTP 方法 (GET、POST、PUT等)，“”代表 GET
	Method string
	// URL 在服务器端表示被请求的 URI,在客户端表示要访问的 URL
	// 在服务端，URL 字段是解析请求行的 URI (保存在 RequestURI字段)得到的
	// 对于大多数请求来说，除了 Path 和 RawQuery 之外的字段都是空字符串。
	// 
	// 在客户端，URL 的 Host 字段指定了要链接的服务器，
	// 而 Request 的 Host 字段 (可选的) 指定要发送的 HTTP 请求的 Host 头的值。
	URL *url.URL 
	// 接收到的请求的协议版本。本包生产的 Request 总是使用 HTTP/1.1
	Proto string	// "HTTP/1.0"
	ProtoMajor int	// 1
	ProtoMinor int	// 0
	// Header 字段用来表示 HTTP 请求的头域。如果头域(多行键值对格式)为：
	//	accept-encoding: gzip, deflate
    //	Accept-Language: en-us
    //	Connection: keep-alive
    // 则：
    //	Header = map[string][]string{
    //		"Accept-Encoding": {"gzip, deflate"},
    //		"Accept-Language": {"en-us"},
    //		"Connection": {"keep-alive"},
    //	}
	// HTTP 规定头域的键名是大小写敏感的，请求的解析器通过格式化头域的键名来实现这点
	// 在客户端的请求，可能会被自动添加或重写 Header 中的特定的头
	Header Header
	// Body 是请求的主体
	//
	// 在客户端，如果 Body 是 nil 表示该请求没有主体请求 GET 请求
	// Client 的 Transport 字段会负责调用 Body 的 Close 方法
	//
	// 在服务器端，Body 字段总是非 nil 的；但在没有主体时，读取 Body 会立刻返回 EOF
	// Server 会关闭请求的主体，ServeHTTP 处理不需要关闭 Body 字段
	Body io.ReadCloser
	// ContentLength 记录相关内容的长度
	// 如果为 -1 表示长度未知，如果 >= 0 表示可以从 Body 字段读取 ContentLength 字节数据
	// 在客户端，如果 Body 非 nil 而字段为 0，表示不知道 Body 的长度
	ContentLength int64
	// TransferEncoding 按从最外到最里的顺序列出传输编码，空切片表示 “identity” 编码
	// 本字段一般会被忽略，当发送或接受请求时，会自动添加或移除"chunked"传输编码。
	TransferEncoding []string
	// 在服务端指定是否在回复请求后关闭连接
	// 在客户端指定是否在发送请求后关闭连接
	Close bool
	// 在服务端，Host 指定 URL 会在其上寻找资源主机
	// 根据RFC 2616，该值可以是Host头的值，或者URL自身提供的主机名。
    // Host的格式可以是"host:port"。
    //
    // 在客户端，请求的Host字段（可选地）用来重写请求的Host头。
    // 如过该字段为""，Request.Write方法会使用URL字段的Host。
	Host string
	// Form 是解析好的表单数据，包括 URL 字段的 query 参数和 POST 或 PUT 的表单数据
	// 本地端只有在调用 ParseForm 后才有效
	// 客户端会忽略请求中的本字段而使用 Body 替代
	Form url.Values
	// PostForm 是解析好的 POST 或 PUT 的表单数据
	// 本字段只有在调用 ParseForm 后才有效。
	// 在客户端会忽略请求中本字段而使用 Body 替代
	PostForm url.Values 
	// MultipartForm 是解析好的多部件表单，包括上传的文件
	// 只有在调用 ParseMultipartForm 后才有效
	// 在客户端，会忽略请求中的本字段而使用 Body 替代
	MultipartForm *multipart.Form
	// 很好有客户端和服务端支持
	Trailer Header
	// RemoteAddr 允许 HTTP 服务器和其他软件记录该请求的来源地址，一般用于日志
	// 不是 ReadRequest 函数填写的，也没有定义格式
	// HTTP 服务器会在调用处理器之前设置 RemoteAddr 为 “IP:port” 格式的地址
	// 客户端会忽略请求中的 RemoteAddr
	RemoteAddr string
	// RequestURI 是被客户端发送到服务端的请求的请求行中未修改的请求 URI
	// 一般应使用 URI 字段，在客户端设置请求的本字段会导致错误
	RequestURI string
	// TLS 允许 HTTP 服务器和其他软件记录接收到该请求的 TLS 链接的信息
	// 不是 ReadRequest 函数填写的
	// 对弃用了 TLS 的链接，本包的 HTTP 服务器会在调用处理器之前设置 TLS 字段， 否则将设置 TLS 为 nil
	// 客户端会忽略请求中的 TLS 字段
	TLS *tls。ConnectionState
}
```
> Request 类型代表一个服务端接收到的或者客户端发送的 HTTP 请求。Request 各字段的意义和用途在服务端和客户端是不同的。

## func NewRequest
```go
func NewRequest(method, urlStr string,body io.Reader)(*Request, error)
```
> NewRequest 使用指定的方法、网址和可选的主题创建并返回一个新的 *Request。如果 body 参数实现了 io.Closer 接口，Request 返回值的 Body 字段会被设置为 body，并会被 Client 类型的 Do、Post 和 PostForm 方法以及 Transport.RoundTrip 方法关闭。

## func ReadRequest
```go
func ReadRequest(b *bufio.Reader)(req *Request, err error)
```
> ReadRequest 从 b 读取并解析出一个 HTTP 请求。主要用在服务端从下层获取请求

## func (*Request)ProtoAtLeast
```go
func (r *Request)ProtoAtLeast(major, minor int)bool
```
> ProtoAtLeast 报告该请求使用的 HTTP 协议版本至少是 major.mimor

## func (*Request)UserAgent
```go
func (r *Request)UserAgent()string
```
> UserAgent 返回请求中的客户端用户代理信息(请求的 User-Agen 头)



