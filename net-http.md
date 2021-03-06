# package http
```go
import "net/http"
```
> http 包提供了 HTTP 客户端和服务端的实现
>> github.com/drone/routes 路由包

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
const DefaultMaxHeaderBytes = 1 << 20 //1M
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

## Example
```go
package main

import (
	"fmt"
	"net/http"
)

func main() {
	fmt.Println(http.CanonicalHeaderKey("accept-encoding"))
}
```

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

## Example
```go
package main

import (
	"fmt"
	"net/http"
)

func main() {
	fmt.Println(http.StatusText(200))
	fmt.Println(http.StatusText(404))
}
```


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
    // 很少有客户端和服务端支持
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
func NewRequest(method, urlStr string, body io.Reader)(*Request, error)
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
> ProtoAtLeast 报告该请求使用的 HTTP 协议版本至少是 major.minor

## func (*Request)UserAgent
```go
func (r *Request)UserAgent()string
```
> UserAgent 返回请求中的客户端用户代理信息(请求的 User-Agen 头)

## func (*Request)Referer
```go
func (r *Request)Rferer()string
```
> Referer 返回请求中的访问来路信息。(请求的 Referer 头)也可以从 Header["Referer"]中获取

## func AddCookie
```go
func (r *Request)AddCookie(c *Cookie)
```
> AddCookie 向请求中添加一个 cookie。AddCookie 不会添加超过一个 Cookie 头字段。这表示所有的 cookie 都写在同一行，用分号分隔（cookie内部用逗号分隔属性）。

## func (*Request)Write
``` go
func (r *Request)Write(w io.Writer)error
```
> Write 方法以有线格式将 HTTP/1.1 请求写入 w (用于将请求写入下层 TCPConn 等)。如果存在Body，ContentLength字段<= 0且TransferEncoding字段未显式设置为["identity"]，Write方法会显式添加"Transfer-Encoding: chunked"到请求的头域。Body字段会在发送完请求后关闭。

## func (*Request)WriteProxy
```go
func (r *Request) WriteProxy(w io.Writer) error
```
> WriteProxy 类似 Write 但会将请求以HTTP代理期望的格式发送。

## func (*Request)Cookies
```go
func (r *Request)Cookies()[]*Cookie
```
> Cookies 解析并返回请求的 Cookie 头设置的 cookie

## func (*Request)Cookie
```go
func (r *Request)Cookie(name string)(*Cookie, error)
```
> Cookie 返回请求中名为 name 的 cookie，如果未找到 cookie 会返回 nil, ErrNoCookie

## func (*Request)ParseForm
```go
func (r *Request)ParseForm()error
```
> ParseForm 解析 URL 中的查询字符串，并将解析结果更新到 r.Form 字段。对于POST或PUT请求，ParseForm还会将body当作表单解析，并将结果既更新到r.PostForm也更新到r.Form。解析结果中，POST或PUT请求主体要优先于URL查询字符串（同名变量，主体的值在查询字符串的值前面）。如果请求的主体的大小没有被MaxBytesReader函数设定限制，其大小默认限制为开头10MB。ParseMultipartForm会自动调用ParseForm。重复调用本方法是无意义的。

## func (*Request)ParseMultipartForm
```go
func (*Request)ParseMultiparForm(maxMemory int64)error
```
> ParseMultipartForm将请求的主体作为multipart/form-data解析。请求的整个主体都会被解析，得到的文件记录最多maxMemery字节保存在内存，其余部分保存在硬盘的temp文件里。如果必要，ParseMultipartForm会自行调用ParseForm。重复调用本方法是无意义的。

## func (*Request)FormValue
```go
func (r *Request)FormValue(key string)string
```
> FormValue 返回 key 为键查询 r.Form 字段得到结果 []string 切片的第一个值。POST 和 PUT 主体中的同名参数优先于 URL 查询字符串。如果必要会隐式调用 ParseMultipartForm 和 ParseForm

## func (*Request)PostFormValue
```go
func (r *Request)PostFormValue(key string)string
```
> PostFormValue 返回 key 为键查询 r.PostForm 字段得到结果 []string 切片的第一个值。如果必要会隐式调用 ParseMultipartForm 和 ParseForm

## func (*Request)FormFile
```go
func (r *Request)FormFile(key string)(multipart.File, *multipar.FileHeader, error)
```
> FormFile 返回以 key 为键查询 r.MultipartForm 字段得到结果中第一个文件和它的信息。如果必要，本函数会隐式调用ParseMultipartForm和ParseForm。查询失败会返回ErrMissingFile错误。

## func (*Request)MultipartReader
```go
func (r *Request)MultiparReader()(*multipart.Reader, error)
```
> 如果请求是 multipart/form-data POST 请求， MultipartReader 返回一个 multipart.Reader 接口，否则返回 nil 和一个错误。使用本函数代替ParseMultipartForm，可以将r.Body作为流处理。

## type Response
```go
type Response struct {
    Status string // 例如"200 OK"
    StatusCode	// 如 200
    Proto	// 如 "HTTP/1.0"
    ProtoMajor int
    ProtoMinor int
    // Header 保管头域的键值对
    // 使用本函数代替ParseMultipartForm，可以将r.Body作为流处理。
    // 被本结构体中的其他字段复制保管的头（如ContentLength）会从Header中删掉。
    Header Header
    // Body 代表回复的主体
    // Client 类型和 Transpor 类型会保证 Body 字段总是非 nil 的，即使回复没有主体或主体长度为0。
    // 关闭主体是调用者的责任
    // 如果服务端采用"chunked"传输编码发送的回复，Body字段会自动进行解码。
    Body io.ReadCloser
    // ContentLength 记录相关内容长度
    // 其值为-1表示长度未知（采用chunked传输编码）
    // 除非对应的Request.Method是"HEAD"，其值>=0表示可以从Body读取的字节数
    ContentLength int64
    // TransferEncoding按从最外到最里的顺序列出传输编码，空切片表示"identity"编码。
    TransferEncoding []string
    // Close 记录头域是否指定应在读取完主体后关闭连接
    // 该值是给客户端的建议，Response.Write方法的ReadResponse函数都不会关闭连接。
    Close bool
    // Trailer字段保存和头域相同格式的trailer键值对，和Header字段相同类型
    Trailer Header
    // Request是用来获取此回复的请求
    // Request的Body字段是nil（因为已经被用掉了）
    // 这个字段是被Client类型发出请求并获得回复后填充的
    Request *Request
    // TLS包含接收到该回复的TLS连接的信息。 对未加密的回复，本字段为nil。
    // 返回的指针是被（同一TLS连接接收到的）回复共享的，不应被修改。
    TLS *tls.ConnectionState
}
```
> Response 代表一个 HTTP 请求的回复

## func ReadResponse
```go
func ReadResponse(r *bufio.Reader, req *Request)(*Response, error)
```
> ReadResponse 从 r 读取并返回一个 HTTP 回复。req 参数是可选的，指定该回复对应的请求(即是对该请求的回复)。如果是nil，将假设请求是GET请求。客户端必须在结束resp.Body的读取后关闭它。读取完毕并关闭后，客户端可以检查resp.Trailer字段获取回复的trailer的键值对。（本函数主要用在客户端从下层获取回复）

## func (*Response)ProtoAlLeast
```go
func (r *Response)ProtoAtLeast(major, minor int)bool
```
> ProtoAtLeast 报告该回复使用的 HTTP 协议版本至少是 major.minor

## func (*Response)Cookies
```go
func (r *Response)Cookies()[]*Cookie
> Cookies 解析并返回该回复中的Set-Cookie头设置的cookie。
```

## func (*Response)Location
```go
func (r *Response)Location()(*url.Url, error)
```
> Location 返回该回复的 Location 头设置的 URL。相对地址的重定向会相对于该回复对应的请求来确定绝对地址。如果回复中没有 Location 头，会返回 nil, ErrNoLocation

## func (*Response)Write
```go
func (r *Response)Write(w io.Writer)error
```
> Write以有线格式将回复写入w（用于将回复写入下层TCPConn等）。Body字段在发送完回复后会被关闭。

## type ResponseWriter
```go
type ResponseWriter interface {
	// Header返回一个Header类型值，该值会被WriteHeader方法发送。
    // 在调用WriteHeader或Write方法后再改变该对象是没有意义的。
    Header() Header
    // WriteHeader该方法发送HTTP回复的头域和状态码。
    // 如果没有被显式调用，第一次调用Write时会触发隐式调用WriteHeader(http.StatusOK)
    // WriterHeader的显式调用主要用于发送错误码。
    WriteHeader(int)
    // Write向连接中写入作为HTTP的一部分回复的数据。
    // 如果被调用时还未调用WriteHeader，本方法会先调用WriteHeader(http.StatusOK)
    // 如果Header中没有"Content-Type"键，
    // 本方法会使用包函数DetectContentType检查数据的前512字节，将返回值作为该键的值。
    Write([]byte) (int, error)
}
```
> ResponseWriter接口被HTTP处理器用于构造HTTP回复。

## type Flusher
```go
type Flusher interface {
	// Flush 将缓冲中的所有数据发送到客户端
	Flush()
}
```
> HTTP处理器ResponseWriter接口参数的下层如果实现了Flusher接口，可以让HTTP处理器将缓冲中的数据发送到客户端。
>
> 注意：即使ResponseWriter接口的下层支持Flush方法，如果客户端是通过HTTP代理连接的，缓冲中的数据也可能直到回复完毕才被传输到客户端。

## type CloseNotifier
```go
type CloseNotifier interface {
	// CloseNotify 返回一个通道，该通道会在客户端连接丢失时接收到唯一的值
	CloseNotify() <-chan bool
}
```
> HTTP处理器ResponseWriter接口参数的下层如果实现了CloseNotifier接口，可以让用户检测下层的连接是否停止。如果客户端在回复准备好之前关闭了连接，该机制可以用于取消服务端耗时较长的操作。

## type Hijacker
## type RoundTripper
## type Transport
## func (*Transport) RegisterProtocol
## func (*Transport) RoundTrip
## func (*Transport) CloseIdleConnections
## func (*Transport) CancelRequest

## type Client
```go
type Client struct {
	// Transport指定执行独立、单次HTTP请求的机制。
    // 如果Transport为nil，则使用DefaultTransport。
    Transport RoundTripper
    // CheckRedirect指定处理重定向的策略。
    // 如果CheckRedirect不为nil，客户端会在执行重定向之前调用本函数字段。
    // 参数req和via是将要执行的请求和已经执行的请求（切片，越新的请求越靠后）。
    // 如果CheckRedirect返回一个错误，本类型的Get方法不会发送请求req，
    // 而是返回之前得到的最后一个回复和该错误。（包装进url.Error类型里）
    //
    // 如果CheckRedirect为nil，会采用默认策略：连续10此请求后停止。
    CheckRedirect func(req *Request, via []*Request) error
    // Jar指定cookie管理器。
    // 如果Jar为nil，请求中不会发送cookie，回复中的cookie会被忽略。
    Jar CookieJar
    // Timeout指定本类型的值执行请求的时间限制。
    // 该超时限制包括连接时间、重定向和读取回复主体的时间。
    // 计时器会在Head、Get、Post或Do方法返回后继续运作并在超时后中断回复主体的读取。
    //
    // Timeout为零值表示不设置超时。
    //
    // Client实例的Transport字段必须支持CancelRequest方法，
    // 否则Client会在试图用Head、Get、Post或Do方法执行请求时返回错误。
    // 本类型的Transport字段默认值（DefaultTransport）支持CancelRequest方法。
    Timeout time.Duration
}
```
> Client 类型代表 HTTP 客户端。它的零值（DefaultClient）是一个可用的使用 DefaultTransport 的客户端。Client 的 Transport 字段一般会含有内部状态（缓存TCP连接），因此 Client 类型值应尽量被重用而不是每次需要都创建新的。Client 类型值可以安全的被多个 g o程同时使用。Client 类型的层次比 RoundTripper 接口（如Transport）高，还会管理 HTTP 的 cookie 和重定向等细节。

## func (*Client)Do
```go
func (c *client)Do(req *Request)(resp *Response, err error)
```
> Do方法发送请求，返回HTTP回复。它会遵守客户端c设置的策略（如重定向、cookie、认证）。如果客户端的策略（如重定向）返回错误或存在HTTP协议错误时，本方法将返回该错误；如果回应的状态码不是2xx，本方法并不会返回错误。如果返回值err为nil，resp.Body总是非nil的，调用者应该在读取完resp.Body后关闭它。如果返回值resp的主体未关闭，c下层的RoundTripper接口（一般为Transport类型）可能无法重用resp主体下层保持的TCP连接去执行之后的请求。请求的主体，如果非nil，会在执行后被c.Transport关闭，即使出现错误。一般应使用Get、Post或PostForm方法代替Do方法。

## func (*Client)Head
```go
func (c *Client)Head(url string)(resp *Response, err error)
```
> Head 向指定的 URL 发出一个 HEAD 请求，如果回应状态码如 301 ~ 303、307，Head 会在调用 c.CheckRedirect 后执行重定向

## func (*Client)Get
```go
func (c *Client)Get(url string)(resp *Response, err error)
```
> Get 向指定的 URL 发出一个 GET 请求，如果回应状态码如 301 ~ 303、307，Head 会在调用 c.CheckRedirect 后执行重定向。如果 c.CheckRedirect 执行失败或存在 HTTP 协议错误时，本方法将返回该错误；如果回应的状态码不是 2XX,本方法并不会返回错误。吐过返回值 err 为 nil，resp.Body 总是非 nil 的，调用者应该在读取完 resp.Body 后关闭它。

## func (*Client)Post
```go
func (c *Client)Post(url string, bodyType string, body io.Reader)(resp *Response, err error)
```
> Post 向指定的 URL 发出一个 POST 请求。bodyType 为 POST 数据的类型，body 为 POST 数据，作为请求的主体。如果参数 body 实现了 io.Closer 接口，它会在发送请求后关闭。调用者有责任在读取完返回值 resp 的主体后关闭它。

## func (*Client)PostForm
```go
func (c *Client)PostForm(url string, data url.Values)(resp *Response, err error)
```
> PostForm 向指定的 URL 发送一个 POST 请求，url.Values 类型的 data 会被编码为请求的主体。 POST 数据的类型一般会设为 "application/x-www-form-urlencoded"。 如果返回值 err 为 nil，resp.Body 总是非 nil 的，调用者应该在读取完 resp.Body 后关闭它。

## type Handler
```go
type Handler interface {
	ServeHTTP(ResponseWriter, *Request)
}
```
> 实现了 Handler 接口的对象可以注册到 HTTP 服务端，为特定的路径及其子树树提供服务。ServeHTTP 应该将回复的头域和数据写入 ResponseWriter 接口然后返回。返回标志着该请求已经结束，HTTP 服务端可以转移向该连接上的下一个请求

## func NotFoundHandler
```go
func NotFoundHandler()Handler
```
> NotFoundHandler 返回一个简单的请求处理器，该处理器会对每个请求都回复 “404 page not found”

## func RedirectHandler
```go
func RedirectHandler(url string, code int)Handler
```
> RedirectHandler 返回一个请求处理器，该处理器会对每个请求都使用状态码 code 重定向到网址 url

## func TimeoutHandler
```go
func TimeoutHandler(h Handler, dt time.Duration, msg string)Handler
```
> TimeoutHandler 返回一个采用指定时间限制的请求处理器。返回的 Handler 会调用 h.ServeHTTP 去处理每个请求，但如果某一次调用耗时超过了时间限制，该处理器会回复请求状态码 503 Service Unavailable, 并将 msg 作为回复的主体(如果msg为空字符串，将发送一个合理的默认信息)。超时后 h 对它的 ResponseWriter 接口参数的鞋服操作会返回 ErrHanderTimeout

## func StripPrefix
```go
func StripPrefix(prefix string, h Handler)Handler
```
> StriPrefic 返回一个处理器，该处理器会将请求的 URL.Path 字段中给定前缀 prefix 去除后再交由 h 处理。StripPrefix 会向 URL.Path 字段中没有给定前缀的请求回复 404 page not found

## type HandlerFunc
```go
type HandlerFunc func(ResponseWriter, *Request)
```
> HandlerFunc 是一个适配器，通过类型转换让我们可以将普通的函数作为 HTTP 处理器使用。如果 f 是一个具有适当签名的函数，HandlerFunc(f) 通过调用 f 实现了 Handler 接口

## func (HandlerFunc)serveHTTP
```go
func (f HandlerFunc)ServeHTTP(w ResponseWriter, r *Request)
```
> ServeHTTP 方法会调用 f(w,r)

## type ServeMux
```go
type ServerMux struct {
	//
}
```
> ServeMux 类型是 HTTP 请求的多路转接器。回将每个请求的 URL 与一个注册模式的列表进行匹配，并调用 和 URL 最匹配的模式的处理器。
>
> 模式固定的、由根尅是的路径，或由根开始的子树。较长的模式优先于较短的模式。
>
> 注意，因为以斜杠结尾的模式代表一个由根开始的子树，模式"/"会匹配所有的未被其他注册的模式匹配的路径，而不仅仅是路径"/"。
>
> ServeMux 还会注意到请求的 URL 路劲的无害化，将任何路径中包含"."或".."元素的请求重定向到等价的没有这两种元素的URL。（参见path.Clean函数）

## func NewServeMux
```go
func NewserveMux()*ServeMux
```
> NewServeMux 创建并返回一个新的 *ServeMux

## func (*ServeMux)Handle
```go
func (mux *ServeMux)Handle(pattern string, handler Handler)
```
> Handle 注册 HTTP 处理器 handler 和应对的模式 pattern。如果该模式已经注册有一个处理器，Handle 会 panic

## func (*ServeMux)HandleFunc 
```go
func (mux *ServeMux)HandleFunc(pattern string, handler func(ResponseWriter, *Request))
```
> HandleFunc 注册一个处理器函数 handler 和对应的模式 pattern

## func (*ServeMux)Handler
```go
func (mux *ServeMux)Handler(r *Request)(h Handler, pattern string)
```
> Handler 根据 r.Method、r.Host 和 r.URL.Path 等数据，返回将用于处理该请求的 HTTP 处理器。总是返回一个非 nil 的处理器。如果路径不是它的规范格式，将返回内建的用于从定向到等价的规范路径的处理器。
>
> Handler 也会返回匹配该请求的已注册模式；在內建重定向处理器的情况下，pattern 会在从定向后进行匹配。如果还有已注册模式可以应用于该请求，本方法将返回一个內建的 "404 page not found" 处理器和一个字符串模式。

## func (*ServeMux)ServeHTTP
```go
func (mux *ServeMux)ServeHTTP(w ResponseWriter, r *Request)
```
> ServeHTTP 将请求派遣到与请求的 URL 醉匹配的模式对应的处理器

## type Server
```go
type Server struct {
	Addr string	// 监听的 TCP 地址，如果为空字符串会使用 “:http”
	Handler Handler	// 调用的处理器，如果为 nil 会调用 htpp.DefaultServeMux
	ReadTimeout time.Duration	// 请求的读取操作在超时前的最大持续时间
	WriteTimeout time.Duration	// 回复的写入操作在超时前的最大持续时间
	MaxHeaderBytes int	// 请求的头域最大长度，如为 0 则用 DefaultMaxHeaderBytes
	TLSConfig *tls.Config	// 可选的 TLS 配置，用于 ListenAndServeTLS 方法
	// TLSNextProto (可选)指定一个函数来在一个 NPN 型协议升级出现时接管 TLS 连接的所有权
	// 映射的键为商谈的协议名；映射的值为函数，该海曙的 Handler 参数应处理 HTTP 请求
	// 并且初始化 Handler.ServeHTTP 的 *Request 参数的 TLS 和 RemoteAddr 字段（如果未设置）。
    // 连接在函数返回时会自动关闭。
	TLSNextProto map[string]func(*Server, *tls.Conn, Handler)
	// ConnState字段指定一个可选的回调函数，该函数会在一个与客户端的连接改变状态时被调用。
	ConnState func(net.Conn, ConnState)
	// ErrorLog指定一个可选的日志记录器，用于记录接收连接时的错误和处理器不正常的行为。
    // 如果本字段为nil，日志会通过log包的标准日志记录器写入os.Stderr。
	ErrorLog *log.Logger
	//
}
```
> Server 类型定义了运行 HTTP 服务端的参数。Server 的零值是合法的配置。

## func (*Server)SerKeepAlivesEnabled
```go
func (s *Server)SerKeepAlivesEnabled(v bool)
```
> SetKeepAlivesEnabled 控制是否允许 HTTP限制连接重用(keep-alive)功能。默认该功能总是被启用的。只有资源非常紧张的环境或服务端在关闭进程中时，才应该关闭该功能。

## func (*Server)serve 
```go
func (srv *Server)Serve(l net.Listener)error
```
> Serve 会接手监听器 l net.Lostener 收到的每一个连接，并为每一个连接创建一个姓的服务 go 程。该 go 程会读取请求，然后调用 srv.Handler 回复请求。

## func (*Server)ListenAndServe 
```go
func (srv *Server)ListenAndServe()error
```
> ListenAndServe 监听 srv.Addr 指定的 TCP 地址，并且会调用 Serve 方法接收到的链接。如果 srv.Addr 为空字符串，会使用":http"

## func (*Server)ListenAndServeTLS
```go
func (srv *Server)ListenAndServeTLS(certFile, keyFile string)error
```
> ListenAndServeTLS监听srv.Addr确定的TCP地址，并且会调用Serve方法处理接收到的连接。必须提供证书文件和对应的私钥文件。如果证书是由权威机构签发的，certFile参数必须是顺序串联的服务端证书和CA证书。如果srv.Addr为空字符串，会使用":https"。

## type File
```go
type File interface {
	io.Closer
	io.Reader
	Readdir(count int)([]os.FileInfo, error)
	Seek(offset int64, whence int)(int64, error)
	Stat()(os.FileInfo, error)
}
```
> File 是被 FileSystem 接口的 Open 方法返回的接口类型，可以被 FileServer 等函数用于文件访问服务。该接口的方法的行为应该和 *os.File 类型的同名方法相同

## type FileSystem
```go
type FileSystem interface {
	Open(name string)(File, error)
}
```
> FileSystem 接口实现了对一系列命名文件的访问。文件路径的分隔符为"/"不管足迹操作系统的惯例如何

## type Dir
```go
type Dir string
```
> Dir 使用限制到指定目录树的本地文件系统实现了 http.fileSystem 接口。空 Dir 被视为 "."

## func (Dir)Open
```go
func (d Dir)Open(name string)(File, error)
```

## fnnc NewFileTransport
```go
func NewFileTransport(fs FileSystem)RoundTripper
```
> NewFileTransport返回一个RoundTripper接口，使用FileSystem接口fs提供文件访问服务。 返回的RoundTripper接口会忽略接收的请求的URL主机及其他绝大多数属性。NewFileTransport函数的典型使用情况是给Transport类型的值注册"file"协议

## func FileServer
```go
func FileServer（root FileSystem)Handler
```
> FileServer 返回一个使用 FileSystem 接口 root 提供文件访问服务的 HTTP 处理器。要使用操作系统的 FileSystem 接口实现，可使用 http.Dir

## func ProxyURL
```go
func ProxyURL(fixedURL *url.URL)func(*Request)(*url.URL, error)
```
> ProxyURL 返回一个代理函数(用于 Transport 类型)，该函数总是返回同一个 URL

## func ProxyFromEnvironment
```go
func ProxyFromEnvironment(req *Request)(*url.URL, error)
```
> ProxyFromEnvironment 使用环境变量 $HTTP_PROXY 和 $NO_PROXY (或$http_proxy和$no_proxy)的配置返回用于req的代理。如果代理环境不合法将返回错误；如果环境未设定代理或者给定的request不应使用代理时，将返回(nil, nil)；如果req.URL.Host字段是"localhost"（可以有端口号，也可以没有），也会返回(nil, nil)。

## func SetCookie 
```go
func SetCookie(w ResponseWriter, cookie *Cookie)
```
> SetCookie 在 w 的头域中添加Set-Cookie头，该HTTP头的值为cookie。

## func Redirect 
```go
func Redirect(w ResponseWriter, r *Request, urlStr string, code int)
```
> Redirect回复请求一个重定向地址urlStr和状态码code。该重定向地址可以是相对于请求r的相对地址

## func NotFound 
```go
func NotFound(w ResponseWriter, r *Request)
```
> NotFound 回复请求 404 状态(not fount)

## func Error
```go
func Error(w ResponseWriter, errror string, code int)
```
> Error使用指定的错误信息和状态码回复请求，将数据写入w。错误信息必须是明文。

## func ServeContent
```go
func ServeContent(w ResponseWriter, req *Request, name string, modtime time.Time, content io.ReadSeeker)
```
> ServeContent使用提供的ReadSeeker的内容回复请求。ServeContent比起io.Copy函数的主要优点，是可以处理范围类请求（只要一部分内容）、设置MIME类型，处理If-Modified-Since请求。
> 
> 如果未设定回复的Content-Type头，本函数首先会尝试从name的文件扩展名推断数据类型；如果失败，会用读取content的第1块数据并提供给DetectContentType推断类型；之后会设置Content-Type头。参数name不会用于别的地方，甚至于它可以是空字符串，也永远不会发送到回复里。
>
> 如果modtime不是Time零值，函数会在回复的头域里设置Last-Modified头。如果请求的头域包含If-Modified-Since头，本函数会使用modtime参数来确定是否应该发送内容。如果调用者设置了w的ETag头，ServeContent会使用它处理包含If-Range头和If-None-Match头的请求。参数content的Seek方法必须有效：函数使用Seek来确定它的大小。注意：本包File接口和*os.File类型都实现了io.ReadSeeker接口。

## func ServeFile
```go
func ServeFile(w ResponseWriter, r *Request, name string)
```
> ServeFile 回复请求 name 指定的文件或者目录的内容。

## func MaxBytesReader
```go
func MaxBytesReader(w ResponseWriter, r io.ReadCloser, n int64)io.ReadCloser 
```
> MaxBytesReader类似io.LimitReader，但它是用来限制接收到的请求的Body的大小的。不同于io.LimitReader，本函数返回一个ReadCloser，返回值的Read方法在读取的数据超过大小限制时会返回非EOF错误，其Close方法会关闭下层的io.ReadCloser接口r。MaxBytesReader预防客户端因为意外或者蓄意发送的“大”请求，以避免尺寸过大的请求浪费服务端资源。

## func Head
```go
func Head(url string)(resp *Response, error)
```
> Head向指定的URL发出一个HEAD请求，如果回应的状态码 301 ~ 303 307，Head会在调用c.CheckRedirect后执行重定向， Head是对包变量DefaultClient的Head方法的包装。

## func Get
```go
func Get(url string)(resp *Response, err error)
```
> Get 向指定的 URL 发送一个 GET 请求，如果如果回应的状态码 301 ~ 303 307，Get 会调用 c.CheckRedirect 后执行重定向。如果c.CheckRedirect执行失败或存在HTTP协议错误时，本方法将返回该错误；如果回应的状态码不是2xx，本方法并不会返回错误。如果返回值err为nil，resp.Body总是非nil的，调用者应该在读取完resp.Body后关闭它。Get是对包变量DefaultClient的Get方法的包装。

## func Post
```go
func Post(url string, bodyType string, body io.Reader)(resp *Response, err error)
```
> Post向指定的URL发出一个POST请求。bodyType为POST数据的类型， body为POST数据，作为请求的主体。如果参数body实现了io.Closer接口，它会在发送请求后被关闭。调用者有责任在读取完返回值resp的主体后关闭它。Post是对包变量DefaultClient的Post方法的包装。

## func PostForm
```go
func PostForm(url string, data url.Values)(resp *Response, err error)
```
> PostForm 向指定的 URL 发送一个 POST 请求，url.Vaules 类型的 data 会被编码为请求的主体。如果返回值 err 为 nil, resp.Body 总是非 nil 的，调用者应该在读取网 resp.Body 后关闭它。PostForm 是对包变量 DefaultClient 的 PostForm 方法包装

## func Handle
```go
func Handle(pattern string, handler Handler)
```
> Handle 注册 HTTP 处理器 handler 和对应的模式 pattern (注册到 DefaultServeMux)。如果该模式已经注册有一个处理器，Handle 会 panic。ServeMux 的文档解释了模式的匹配机制

## func HandleFunc
```go
func handleFunc(pattern string, handler func(ResponseWriter, *Request))
```
> HandleFunc 注册一个处理器函数 handler 和对应的模式 pattern（注册到DefaultServeMux）。ServeMux 的文档解释了模式的匹配机制。

## func Serve
```go
func Serve(l net.Listener, handler Handler)error
```
> Serve 会接手监听器 l net.Lostener 收到的每一个连接，并为每一个连接创建一个新的服务 go 程。该 go 程会读取请求，然后调用 handler 回复请求。handler 参数一般会设为 nil,此时会使用 DefaultServeMux

## func ListenAndServe
```go
func ListenAdnServe(addr string, handler Handler)error
```
> ListenAndServe 监听 TCP 地址 addr，并且会使用 handler 参数调用 Serve 函数处理接收到的链接。handler 参数一般会设为 nil, 此时会使用 DefaultServeMux

## func ListenAndServeTLS
```go
func ListenAndServeTLS(addr string, certFile string, keyFile string, handler Handler)error
```
> ListenAndServeTLS函数和ListenAndServe函数的行为基本一致，除了它期望HTTPS连接之外。此外，必须提供证书文件和对应的私钥文件。如果证书是由权威机构签发的，certFile参数必须是顺序串联的服务端证书和CA证书。如果srv.Addr为空字符串，会使用":https"。
