<h1 align="center">
  Amazon Kinesis Video Streams CPP Producer, GStreamer Plugin and JNI
  <br>
</h1>

<h4 align="center"> Amazon Kinesis Video Streams | Secure Video Ingestion for Analysis &amp; Storage </h4>

<p align="center">
  <a href="https://travis-ci.com/awslabs/amazon-kinesis-video-streams-producer-sdk-cpp"> <img src="https://travis-ci.com/awslabs/amazon-kinesis-video-streams-producer-sdk-cpp.svg?branch=master" alt="Build Status"> </a>
  <a href="https://codecov.io/gh/awslabs/amazon-kinesis-video-streams-producer-sdk-cpp"> <img src="https://codecov.io/gh/awslabs/amazon-kinesis-video-streams-producer-sdk-cpp/branch/master/graph/badge.svg" alt="Coverage Status"> </a>
</p>

<p align="center">
  <a href="#key-features">Key Features</a> •
  <a href="#build">Build</a> •
  <a href="#run">Run</a> •
  <a href="#documentation">Documentation</a> •
  <a href="#related">Related</a> •
  <a href="#license">License</a>
</p>

## Key Features
* C++ SDK
* GStreamer Plugin (kvssink)
* JNI

Amazon Kinesis Video Streams Producer SDK for C/C++ makes it easy to build an on-device application that securely connects to a video stream, and reliably publishes video and other media data to Kinesis Video Streams. It takes care of all the underlying tasks required to package the frames and fragments generated by the device's media pipeline. The SDK also hand
les stream creation, token rotation for secure and uninterrupted streaming, processing acknowledgements returned by Kinesis Video Streams, and other tasks.

## Build
### Download
To download run the following command:

`git clone --recursive https://github.com/awslabs/amazon-kinesis-video-streams-producer-sdk-cpp.git`

Note: If you miss running git clone with --recursive, run `git submodule update --init` in the amazon-kinesis-video-streams-producer-sdk-cpp/open-source directory
You will also need to install `pkg-config`, `automake-1.16` and `CMake` and a build enviroment. If you are build the GStreamer plugin you will also need to install it locally.

Refer to the [FAQ](#FAQ) for platform specific instructions.

### Configure
Create a build directory in the newly checked out repository, and execute CMake from it.

`mkdir -p amazon-kinesis-video-streams-producer-sdk-cpp/build; cd amazon-kinesis-video-streams-producer-sdk-cpp/build; cmake .. `

GStreamer and JNI is NOT built by default, if you wish to build both you MUST execute `cmake .. -DBUILD_GSTREAMER_PLUGIN=ON -DBUILD_JNI=TRUE`

By default we download all the libraries from GitHub and build them locally, so should require nothing to be installed ahead of time.
If you do wish to link to existing libraries you can use the following flags to customize your build.

#### Cross-Compilation
If you wish to cross-compile `CC` and `CXX` are respected when building the library and all its dependencies. See our [.travis.yml](.travis.yml) for an example of this. Every commit is cross compiled to ensure that it continues to work.


#### CMake Arguments
You can pass the following options to `cmake ..`.

* `-DBUILD_GSTREAMER_PLUGIN` -- Build kvssink GStreamer plugin
* `-DBUILD_JNI` -- Build C++ wrapper for JNI to expose the functionality to Java/Android
* `-DBUILD_DEPENDENCIES` -- Build depending libraries from source
* `-DBUILD_TEST=TRUE` -- Build unit/integration tests, may be useful for confirm support for your device. `./tst/webrtc_client_test`
* `-DCODE_COVERAGE` --  Enable coverage reporting
* `-DCOMPILER_WARNINGS` -- Enable all compiler warnings
* `-DADDRESS_SANITIZER` -- Build with AddressSanitizer
* `-DMEMORY_SANITIZER` --  Build with MemorySanitizer
* `-DTHREAD_SANITIZER` -- Build with ThreadSanitizer
* `-DUNDEFINED_BEHAVIOR_SANITIZER` Build with UndefinedBehaviorSanitizer
* `-DALIGNED_MEMORY_MODEL` Build for aligned memory model only devices. Default is OFF.

### Build
To build the library run make in the build directory you executed CMake.

```
make
```

In your build directory you will now have shared objects for all the targets you have selected

## Run
### GStreamer Plugin (kvssink)

#### Loading Element
The GStreamer plugin will be in your `build` directory, to load this plugin it will need to be in your `GST_PLUGIN_PATH`. Do this you can run

```
export GST_PLUGIN_PATH=`pwd`/build
```

Now if you execute `gst-inspect-1.0 kvssink` you should get information on the plugin like

```text
Factory Details:
  Rank                     primary + 10 (266)
  Long-name                KVS Sink
  Klass                    Sink/Video/Network
  Description              GStreamer AWS KVS plugin
  Author                   AWS KVS <kinesis-video-support@amazon.com>

Plugin Details:
  Name                     kvssink
  Description              GStreamer AWS KVS plugin
  Filename                 /Users/seaduboi/workspaces/amazon-kinesis-video-streams-producer-sdk-cpp/build/libgstkvssink.so
  Version                  1.0
  License                  Proprietary
  Source module            kvssinkpackage
  Binary package           GStreamer
  Origin URL               http://gstreamer.net
```

If the build failed, or `GST_PLUGIN_PATH` is not proprely set you will get output like

```text
No such element or plugin 'kvssink'
```


#### Using Element
The kvssink element has the following required parameters:

* `stream-name` -- The name of the destination Kinesis video stream.
* `storage-size` -- The storage size of the device in kilobytes. For information about configuring device storage, see StorageInfo.
* `access-key` -- The AWS access key that is used to access Kinesis Video Streams. You must provide either this parameter or credential-path.
* `secret-key` -- The AWS secret key that is used to access Kinesis Video Streams. You must provide either this parameter or credential-path.
* `credential-path` -- A path to a file containing your credentials for accessing Kinesis Video Streams. For example credential files, see Sample Static Credential and Sample Rotating Credential. For more information on rotating credentials, see Managing Access Keys for IAM Users. You must provide either this parameter or access-key and secret-key.


For examples of common use cases you can look at [Example: Kinesis Video Streams Producer SDK GStreamer Plugin](https://docs.aws.amazon.com/kinesisvideostreams/latest/dg/examples-gstreamer-plugin.html)

## FAQ
* Is CPP-SDK and GStreamer supported on Mac/Windows/Linux (Supported Platforms)
Yes! We have FAQs and platform specific instructions for [Windows](docs/windows.md), [MacOS](docs/macos.md) and [Linux](docs/linux.md)

## Related
* [What Is Amazon Kinesis Video Streams](https://docs.aws.amazon.com/kinesisvideostreams/latest/dg/what-is-kinesis-video.html)
* [C SDK](https://github.com/awslabs/amazon-kinesis-video-streams-producer-c)
* [Example: Kinesis Video Streams Producer SDK GStreamer Plugin](https://docs.aws.amazon.com/kinesisvideostreams/latest/dg/examples-gstreamer-plugin.html)

## License

This library is licensed under the Apache 2.0 License.
