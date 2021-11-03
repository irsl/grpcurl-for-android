# gRPCurl for Android

[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that lets you interact with gRPC servers. It's basically curl for gRPC servers.

And this is a pre-compiled binary version of it for the Android platform.

The build process was inspired by this article:

https://www.sajalkayan.com/post/go-android-binary.html


## How to build

You need a recent golang version (Go 1.16 or above).

You need gomobile: https://pkg.go.dev/golang.org/x/mobile/cmd/gomobile

```
go install golang.org/x/mobile/cmd/gomobile@latest
```

You need the Android NDK:

```
mkdir /ndk
cd /ndk
wget https://dl.google.com/android/repository/android-ndk-r23b-linux.zip
unzip android-ndk-r23b-linux.zip
```

Then fetch grpcurl:

```
mkdir ~/grpcurl
cd ~/grpcurl
git clone https://github.com/fullstorydev/grpcurl.git .
```

Build it normally first (especially to fetch dependencies - remove the indent() call from the source code):

```
go build -o grpcurl ./cmd/grpcurl/grpcurl.go
```
 
And then we can finally crosscompile:

```
GOMOBILE=~/gopath/pkg/gomobile GOOS=android GOARCH=arm CC=/ndk/android-ndk-r23b/toolchains/llvm/prebuilt/linux-x86_64/bin/armv7a-linux-androideabi23-clang CXX=/ndk/android-ndk-r23b/toolchains/llvm/prebuilt/linux-x86_64/bin/armv7a-linux-androideabi23-clang++ CGO_ENABLED=1 GOARM=7 go build -p=8 -pkgdir=$GOMOBILE/pkg_android_arm -tags="" -ldflags="-extldflags=-pie" -o ./grpcurl-arm -x ./cmd/grpcurl/grpcurl.go
```

And also for x86_64:

```
GOMOBILE=~/gopath/pkg/gomobile GOOS=android GOARCH=amd64  CC=/ndk/android-ndk-r23b/toolchains/llvm/prebuilt/linux-x86_64/bin/x86_64-linux-android23-clang CXX=/ndk/android-ndk-r23b/toolchains/llvm/prebuilt/linux-x86_64/bin/x86_64-linux-android23-clang++ CGO_ENABLED=1 GOARM=7 go build -p=8 -pkgdir=$GOMOBILE/pkg_android_arm -tags="" -ldflags="-extldflags=-pie" -o ./grpcurl-amd64 -x ./cmd/grpcurl/grpcurl.go
```
