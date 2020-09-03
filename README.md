# protoc-gen-go-grpc-mock
`protoc-gen-go-grpc-mock` is a protobuf plugin that autogenerates gRPC services mocks in Go using the `github.com/stretchr/testify/mock` package

## Requirements

In order to work, `protoc-gen-grpc-go` requires:

- [protoc]()
- [protoc-gen-go]()
- [protoc-gen-go-grpc]() **Note**: minimum version 1.33!

## Installation

```sh
go get -u github.com/nefixestrada/protoc-gen-go-grpc-mock
```

## Usage

### Compilation

```sh
protoc \
    --go_out=. \
    --go_opt=paths=source_relative \
    --go-grpc_out=. \
    --go-grpc_opt=paths=source_relative \
    --go-grpc-mock_out=. \
    --go-grpc-mock_opt=paths=source_relative \
pkg/proto/hyper.proto
```

### Testing

```go
func TestMock(t *testing.T) {
	assert := assert.New(t)
	require := require.New(t)

	service, client, err := proto.NewHyperServiceMock()
	if err != nil {
		require.NoError(err)
	}
	defer service.Stop()

	service.On(proto.DesktopList, mock.Anything, &proto.DesktopListRequest{}).Return((*proto.DesktopListResponse)(nil), status.Error(codes.Internal, "custom error! :D"))

	list, err := client.DesktopList(context.Background(), &proto.DesktopListRequest{})

	assert.EqualError(err, status.Error(codes.Internal, "custom error! :D").Error())
	assert.Nil(list)
}
```

## Missing

- Streaming Methods
