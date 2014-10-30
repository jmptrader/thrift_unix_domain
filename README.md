thrift_unix_domain
==================

thrift golang unix domain socket

reference by https://github.com/apache/thrift/tree/master/lib/go

Using Thrift Unix Domain with Go

go get git.apache.org/thrift.git/lib/go/thrift
go get github.com/Wang/thrift_unix_domain

Server:
func main() {
    transportFactory := thrift.NewTFramedTransportFactory(thrift.NewTTransportFactory())
    protocolFactory := thrift.NewTBinaryProtocolFactoryDefault()
    
    serverTransport, err := thrift_unix_domain.NewTServerUnixDomain("/tmp/thrift.sock")
    if err != nil {
        fmt.Println("Error!", err)
        os.Exit(1)
    }
    handler := &Handler{} //your thrift struct
    processor := thriftMsg.NewThriftMsgProcessor(handler) //your thrift function
    server := thrift.NewTSimpleServer4(processor, serverTransport, transportFactory, protocolFactory)
    fmt.Println("thrift server in", "/tmp/thrift.sock")
    server.Serve()
}

Client:
func main() {
    transportFactory := thrift.NewTFramedTransportFactory(thrift.NewTTransportFactory())
    protocolFactory := thrift.NewTBinaryProtocolFactoryDefault()
    transport, err := thrift_unix_domain.NewTUnixDomain("/tmp/thrift.sock")
    if err != nil {
        fmt.Fprintln(os.Stderr, "error resolving address:", err)
        os.Exit(1)
    }
    useTransport := transportFactory.GetTransport(transport)
    client := thriftMsg.NewThriftMsgClientFactory(useTransport, protocolFactory) //you thrift function
    if err := transport.Open(); err != nil {
        fmt.Fprintln(os.Stderr, "Error opening socket to /tmp/thrift.sock", " ", err)
        os.Exit(1)
    }
    defer transport.Close()

}

