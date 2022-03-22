---
title: Bisq gRPC API Reference

language_tabs:
  - shell: CLI
  - java: Java
  - python: Python

toc_footers:
  - <a href='https://bisq.network'>Bisq Site</a>
  - <a href='https://github.com/bisq-network/bisq'>Bisq Source Code</a>
  - Documentation Powered by <a href='https://github.com/lord/slate'>Slate</a>

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the Bisq gRPC API
---

# Introduction

Welcome to the Bisq gRPC API reference documentation for Bisq Daemon.

You can use this API to access local Bisq daemon API endpoints, which provide a subset of the Bisq Desktop application's
feature set:  check balances, transfer BTC and BSQ, create payment accounts, view offers, create and take offers, and
execute trades.

The Bisq API is based on the gRPC framework, and any supported gRPC language binding can be used to call Bisq API
endpoints. This document provides code examples for language bindings in Java and Python, plus bisq-cli (CLI) command
examples. The code examples are viewable in the dark area to the right, and you can switch between programming language
examples with the tabs on the top right.

The original *.proto files from which the gRPC documentation was generated can be found here:

* [pb.proto](https://github.com/bisq-network/bisq/tree/master/proto/src/main/proto/pb.proto)
* [grpc.proto](https://github.com/bisq-network/bisq/tree/master/proto/src/main/proto/grpc.proto)

This API documentation was created with [Slate](https://github.com/slatedocs/slate).

# Running Example Code

Examples should not be used to make calls to an API daemon connected to the bitcoin mainnet. There is a convenient way
to run a regtest bitcoin-core daemon, a Bisq seed node, an arbitration node, and two regtest API daemons called Alice (
listening on port 9998), and Bob (listening on port 9999). The Bob and Alice daemons will have regtest wallets
containing 10 BTC. Bob's BSQ wallet will also be set up with 1500000 BSQ, Alice's with 1000000 BSQ. These two API
daemons can simulate trading over the local regtest network.

See
the [Bisq API Beta Testing Guide](https://github.com/bisq-network/bisq/blob/master/apitest/docs/api-beta-test-guide.md)
for instructions on how to get the Bisq API test harness up and running.

## CLI Examples

The API CLI is included in Bisq; no protobuf code generation tasks are required to run the CLI examples in this
document.

The only requirements are:

- A running, local API daemon, preferably the test harness described in
  the [Bisq API Beta Testing Guide](https://github.com/bisq-network/bisq/blob/master/apitest/docs/api-beta-test-guide.md)
- A terminal open in the Bisq source project's root directory

## Java Examples

Running Java examples requires:

- A running, local API daemon, preferably the test harness described in
  the [Bisq API Beta Testing Guide](https://github.com/bisq-network/bisq/blob/master/apitest/docs/api-beta-test-guide.md)
- Downloading Bisq protobuf definition files
- Generating protobuf and gRPC service stubs using the the [protoc](https://grpc.io/docs/protoc-installation/) compiler,
  with the [protoc-gen-grpc-java](https://github.com/grpc/grpc-java) plugin.

### Download the Bisq .proto files to your Java project

If your Java source is located in a directory named  `my-api-app/src/main`, open a terminal in your project root
directory (`my-api-app`), and the Bisq .proto files are located in a directory named `my-api-app/src/main/proto`:

    `$ export PROTO_PATH="src/main/proto"`</br>
    `$ curl -o $PROTO_PATH/pb.proto https://raw.githubusercontent.com/bisq-network/bisq/master/proto/src/main/proto/pb.proto`</br>
    `$ curl -o $PROTO_PATH/grpc.proto https://raw.githubusercontent.com/bisq-network/bisq/master/proto/src/main/proto/grpc.proto`

### Generate Bisq API protobuf stubs using Gradle grpc-java plugin (recommended)

You can generate Java API stubs in a Gradle project using the [protoc-gen-grpc-java](https://github.com/grpc/grpc-java)
plugin. Try the [build.gradle](https://github.com/ghubstan/bisq-api-reference/blob/main/java-examples/build.gradle)
file used by the project providing the Java examples for this document; it should work for you.

_Note: You can also generate stubs with [protoc-gen-grpc-java](https://github.com/grpc/grpc-java) in maven projects._

### Generate Bisq API protobuf stubs using grpc-java plugin from terminal

If you prefer to generate the Java protos from a terminal, you can compile
the [protoc gen-java](https://github.com/grpc/grpc-java/blob/master/COMPILING.md) binary from source, or manually
download the [binary](https://repo1.maven.org/maven2/io/grpc/protoc-gen-grpc-java/) to your system `PATH`, and
run `protoc` with the appropriate options:

    `$ protoc --plugin=protoc-gen-grpc-java=$GEN_GRPC_JAVA_BINARY_PATH \`</br>
        `--grpc-java_out=$JAVA_PROTO_OUT_PATH \`</br>
        `--proto_path=$PROTO_PATH \`</br>
          `$PROTO_PATH/*.proto`</br>

_Note:  My attempts to compile the protoc gen-java plugin on my own platform were unsuccessful. You may have better luck
or time to resolve platform specific build issues._

## Python Examples

Running Python examples requires:

- A running, local API daemon, preferably the test harness described in
  the [Bisq API Beta Testing Guide](https://github.com/bisq-network/bisq/blob/master/apitest/docs/api-beta-test-guide.md)
- Downloading Bisq protobuf definition files
- Generating protobuf and gRPC service stubs using the `protoc` compiler, with two additional Python protobuf and grpc
  plugins.

You can download the Bisq protobuf (.proto) files by running:

    `proto-downloader/download-bisq-protos.sh`

You can build Python .proto stubs, install Python example dependencies, and package the examples by running:

    `python-examples/run-setup.sh`
# Authentication

An API password option `--apiPassword=<api-password>` is passed in the daemon's start command.

All API client requests must authenticate to the daemon with the api-password.

* Each CLI command must include the `--password=<api-password>` option.

* API authentication from Java and Python requests are demonstrated in API usage examples.
# Service DisputeAgents
The DisputeAgents service is provided for development only;  it can only be used when running in regtest mode.

## RPC Method RegisterDisputeAgent
### Unary RPC
Register regtest / dev mode dispute agents.  Does not work when running on mainnet.

### gRPC Request: RegisterDisputeAgentRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 dispute_agent_type | string | One of "mediator" or "refundagent".  Development / test arbitrators can only be registered in the UI. 
 registration_key | string | Private developer (only) registration key.

### gRPC Response: RegisterDisputeAgentReply

This Response has no parameters.

# Service GetVersion

## RPC Method GetVersion
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 getversion

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetVersionGrpc;
import bisq.proto.grpc.GetVersionRequest;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetVersion extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = GetVersionGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetVersionRequest.newBuilder().build();
            var reply = stub.getVersion(request);
            out.println(reply.getVersion());
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.GetVersionStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetVersion.with_call(
            bisq_messages.GetVersionRequest(),
            metadata=[('password', api_password)])
        print('Response: ' + response[0].version)
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get the current Bisq version number.


### gRPC Request: GetVersionRequest

This Request has no parameters.

### gRPC Response: GetVersionReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 version | string | The version of the Bisq software release.

# Service Help

## RPC Method GetMethodHelp
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 createoffer --help

```

```java
package bisq.rpccalls;// Help Service is for CLI users.
```
```python
# Help Service is for CLI users.
```
### Unary RPC
Returns a CLI command man page.


### gRPC Request: GetMethodHelpRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 method_name | string | The CLI command name.

### gRPC Response: GetMethodHelpReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 method_help | string | The man page for the CLI command.

# Service Offers
The Offers service provides rpc methods for creating, editing, listing, and cancelling Bisq offers.

## RPC Method CancelOffer
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 canceloffer \
  --offer-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.CancelOfferRequest;
import bisq.proto.grpc.OffersGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class CancelOffer extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = OffersGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = CancelOfferRequest.newBuilder()
                    .setId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                    .build();
            var response = stub.cancelOffer(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.OffersStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.CancelOffer.with_call(
            bisq_messages.CancelOfferRequest(id='ubwhog-fef370c3-3fe7-4ac5-b0d6-8de850916642-184'),
            metadata=[('password', api_password)])
        print('Response: ' + str(response))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Cancel an open offer;  remove it from the offer book.


### gRPC Request: CancelOfferRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 id | string | The canceled offer's unique identifier.

### gRPC Response: CancelOfferReply

This Response has no parameters.

## RPC Method CreateBsqSwapOffer
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 createoffer \
  --swap=true \
  --direction=BUY \
  --currency-code=BSQ \
  --amount=0.50 \
  --min-amount=0.25 \
  --fixed-price=0.00005

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.CreateBsqSwapOfferRequest;
import bisq.proto.grpc.OffersGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class CreateBsqSwapOffer extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = OffersGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = CreateBsqSwapOfferRequest.newBuilder()
                    .setDirection("BUY")    // Buy BTC with BSQ
                    .setAmount(12500000)    // Satoshis
                    .setMinAmount(6250000)  // Satoshis
                    .setPrice("0.00005")    // Price of 1 BSQ in BTC
                    .build();
            var response = stub.createBsqSwapOffer(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.OffersStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.CreateBsqSwapOffer.with_call(
            bisq_messages.CreateBsqSwapOfferRequest(
                direction='SELL',  # Buy BTC with BSQ
                price='0.00005',  # Price of 1 BSQ in BTC
                amount=6250000,  # Satoshis
                min_amount=3125000),  # Optional parameter cannot be 0 Satoshis.
            metadata=[('password', api_password)])
        print('New BSQ swap offer: ' + str(response[0].bsq_swap_offer))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Create a BSQ swap offer.


### gRPC Request: CreateBsqSwapOfferRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 direction | string | The new BSQ swap offer's BUY (BTC) or SELL (BTC) direction. 
 amount | uint64 | The amount of BTC to be traded as a long representing satoshi units. 
 min_amount | uint64 | The minimum amount of BTC to be traded as a long representing satoshi units. 
 price | string | The fixed price of the offer as a string representing BTC units, e.g., "0.00005" or "0.00005000".

### gRPC Response: CreateBsqSwapOfferReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 bsq_swap_offer | [OfferInfo](#offerinfo) | The newly created BSQ swap offer.

## RPC Method CreateOffer
```shell
#!/bin/bash

# Create a fixed-price offer to buy BTC with EUR.
./bisq-cli --password=xyz --port=9998 createoffer \
  --payment-account-id=f3c1ec8b-9761-458d-b13d-9039c6892413 \
  --direction=BUY \
  --currency-code=EUR \
  --amount=0.125 \
  --min-amount=0.0625 \
  --fixed-price=34500 \
  --security-deposit=15.0 \
  --fee-currency=BSQ

# Create a market-price-margin based offer to sell BTC for JPY, at 0.5% above the current market JPY price.
./bisq-cli --password=xyz --port=9998 createoffer \
  --payment-account-id=f3c1ec8b-9761-458d-b13d-9039c6892413 \
  --direction=SELL \
  --currency-code=JPY \
  --amount=0.125 \
  --min-amount=0.0625 \
  --market-price-margin=0.5 \
  --security-deposit=15.0 \
  --fee-currency=BSQ

# Create an offer to swap 0.5 BTC for BSQ, at a price of 0.00005 BTC for 1 BSQ
./bisq-cli --password=xyz --port=9998 createoffer \
  --swap=true \
  --direction=BUY \
  --currency-code=BSQ \
  --amount=0.5 \
  --fixed-price=0.00005

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.CreateOfferRequest;
import bisq.proto.grpc.OffersGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class CreateOffer extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = OffersGrpc.newBlockingStub(channel).withCallCredentials(credentials);

            var fixedPriceOfferRequest = createFixedPriceEurOfferRequest();
            var fixedPriceOfferResponse = stub.createOffer(fixedPriceOfferRequest);
            out.println(fixedPriceOfferResponse);
            waitForOfferPreparation(5_000);

            var marketBasedPriceOfferRequest = createMarketBasedPriceEurOfferRequest();
            var marketBasedPriceOfferResponse = stub.createOffer(marketBasedPriceOfferRequest);
            out.println(marketBasedPriceOfferResponse);
            waitForOfferPreparation(5_000);

            var fixedPriceXmrOfferRequest = createFixedPriceXmrOfferRequest();
            var fixedPriceXmrOfferResponse = stub.createOffer(fixedPriceXmrOfferRequest);
            out.println(fixedPriceXmrOfferResponse);
        } catch (Throwable t) {
            handleError(t);
        }
    }

    private static CreateOfferRequest createFixedPriceEurOfferRequest() {
        return CreateOfferRequest.newBuilder()
                .setCurrencyCode("EUR")
                .setDirection("BUY")                // Buy BTC with EUR
                .setPrice("34500.00")
                .setAmount(12500000)                // Satoshis
                .setMinAmount(6250000)              // Satoshis
                .setBuyerSecurityDepositPct(15.00)  // 15%
                .setPaymentAccountId("f3c1ec8b-9761-458d-b13d-9039c6892413")
                .setMakerFeeCurrencyCode("BTC")     // Pay Bisq trade fee in BTC
                .build();
    }

    private static CreateOfferRequest createMarketBasedPriceEurOfferRequest() {
        return CreateOfferRequest.newBuilder()
                .setCurrencyCode("EUR")
                .setDirection("SELL")               // Sell BTC for EUR
                .setUseMarketBasedPrice(true)
                .setMarketPriceMarginPct(3.25)      // Sell at 3.25% above market BTC price in EUR
                .setAmount(12500000)                // Satoshis
                .setMinAmount(6250000)              // Satoshis
                .setBuyerSecurityDepositPct(15.00)  // 15%
                .setTriggerPrice("0")               // No trigger price
                .setPaymentAccountId("f3c1ec8b-9761-458d-b13d-9039c6892413")
                .setMakerFeeCurrencyCode("BSQ")     // Pay Bisq trade fee in BSQ
                .build();
    }

    private static CreateOfferRequest createFixedPriceXmrOfferRequest() {
        return CreateOfferRequest.newBuilder()
                .setCurrencyCode("XMR")
                .setDirection("BUY")                // Buy BTC with XMR
                .setPrice("0.005")                  // BTC price for 1 XMR
                .setAmount(12500000)                // Satoshis
                .setMinAmount(6250000)              // Satoshis
                .setBuyerSecurityDepositPct(33.00)  // 33%
                .setPaymentAccountId("g3c8ec8b-9aa1-458d-b66d-9039c6892413")
                .setMakerFeeCurrencyCode("BSQ")     // Pay Bisq trade fee in BSQ
                .build();
    }

    private static void waitForOfferPreparation(long ms) {
        try {
            // Wait new offer's preparation and wallet updates before attempting to create another offer.
            Thread.sleep(5_000);
        } catch (InterruptedException ex) {
            // ignored
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
# from getpass import getpass
import time
from builtins import print

import grpc

import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.OffersStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        create_offer_request = fixed_price_usd_request()
        create_offer_response = grpc_service_stub.CreateOffer.with_call(create_offer_request,
                                                                        metadata=[('password', api_password)])
        print('New fixed-price offer: ' + str(create_offer_response[0].offer))
        time.sleep(3)  # Wait for new offer preparation and wallet updates before creating another offer.

        create_offer_request = market_based_price_usd_request()
        create_offer_response = grpc_service_stub.CreateOffer.with_call(create_offer_request,
                                                                        metadata=[('password', api_password)])
        print('New mkt price margin based offer: ' + str(create_offer_response[0].offer))
        time.sleep(3)  # Wait for new offer preparation and wallet updates before creating another offer.

        create_offer_request = fixed_price_xmr_request()
        create_offer_response = grpc_service_stub.CreateOffer.with_call(create_offer_request,
                                                                        metadata=[('password', api_password)])
        print('New XMR offer: ' + str(create_offer_response[0].offer))

    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


def fixed_price_usd_request():
    # Create an offer to buy BTC with USD at a fixed price.
    return bisq_messages.CreateOfferRequest(direction='BUY',
                                            currency_code='USD',
                                            price='40000.00',
                                            amount=12500000,
                                            min_amount=6250000,
                                            buyer_security_deposit_pct=20.00,
                                            payment_account_id='af852e11-f2db-48bd-82f5-123047b41f0c',
                                            maker_fee_currency_code='BSQ')


def market_based_price_usd_request():
    # Create an offer to sell BTC for USD at a moving, market price margin.
    return bisq_messages.CreateOfferRequest(direction='SELL',
                                            currency_code='USD',
                                            use_market_based_price=True,
                                            market_price_margin_pct=2.50,
                                            amount=12500000,
                                            min_amount=6250000,
                                            buyer_security_deposit_pct=20.00,
                                            payment_account_id='af852e11-f2db-48bd-82f5-123047b41f0c',
                                            maker_fee_currency_code='BSQ')


def fixed_price_xmr_request():
    # Create an offer to buy BTC with XMR.
    return bisq_messages.CreateOfferRequest(direction='BUY',  # Buy BTC with XMR
                                            currency_code='XMR',
                                            price='0.005',  # Price of 1 XMR in BTC
                                            amount=12500000,
                                            min_amount=6250000,
                                            buyer_security_deposit_pct=20.00,
                                            payment_account_id='7d52d9b6-e943-4625-a063-f53b09381bf2',
                                            maker_fee_currency_code='BTC')


if __name__ == '__main__':
    main()
```
### Unary RPC
Create a v1 protocol offer.


### gRPC Request: CreateOfferRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 currency_code | string | The new offer's fiat or altcoin currency code. 
 direction | string | The new v1 protocol offer's BUY (BTC) or SELL (BTC) direction. 
 price | string | For fiat offers:  a string representing the rounded, fixed fiat price of the offer, e.g., "45000", not "45000".<br/>For altcoin offers:  a string representing the fixed BTC price of the offer, e.g., "0.00005".<br/> 
 use_market_based_price | bool | Whether the offer price is fixed, or market price margin based. 
 market_price_margin_pct | double | The offer's market price margin as a percentage above or below the current market BTC price, e.g., 2.50 represents 2.5%. 
 amount | uint64 | The amount of BTC to be traded, in satoshis. 
 min_amount | uint64 | The minimum amount of BTC to be traded, in satoshis. 
 buyer_security_deposit_pct | double | A BUY BTC offer maker's security deposit as a percentage of the BTC amount to be traded, e.g., 15.00 represents 15%. 
 trigger_price | string | A market price margin based offer's trigger price is the market BTC price at which the offer is automatically disabled.<br/>Disabled offers are never automatically enabled, they must be manually re-enabled.<br/>A zero value indicates trigger price is not set.  Trigger price does not apply to fixed price offers.<br/> 
 payment_account_id | string | The unique identifier of the payment account used to create the new offer, and send or receive trade payment. 
 maker_fee_currency_code | string | The offer maker's trade fee currency:  BTC or BSQ.

### gRPC Response: CreateOfferReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 offer | [OfferInfo](#offerinfo) | The newly created v1 protocol offer.

## RPC Method EditOffer
```shell
#!/bin/bash

# Warning:  Editing an offer involves removing it from the offer book, then re-publishing
# it with the changes.  This operation takes a few seconds and clients should not try
# to make rapid changes to the same offer;  there must be a delay before each edit request
# for the same offer.

# Disable an offer.
./bisq-cli --password=xyz --port=9998 editoffer \
  --offer-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea \
  --enable=false

# Enable an offer.
./bisq-cli --password=xyz --port=9998 editoffer \
  --offer-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea \
  --enable=true

# Edit the fixed-price, and/or change a market price margin based offer to a fixed-price offer.
./bisq-cli --password=xyz --port=9998 editoffer \
  --offer-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea \
  --fixed-price=35000.5555

# Edit the price margin, and/or change a fixed-price offer to a market price margin based offer.
./bisq-cli --password=xyz --port=9998 editoffer \
  --offer-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea \
  --market-price-margin=0.5

# Set the trigger price on a market price margin based offer.
# Note:  trigger prices do not apply to fixed-price offers.
./bisq-cli --password=xyz --port=9998 editoffer \
  --offer-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea \
  --trigger-price=3960000.0000

# Remove the trigger price from a market price margin based offer.
./bisq-cli --password=xyz --port=9998 editoffer \
  --trigger-price=0

# Change a disabled fixed-price offer to a market price margin based offer, set a trigger price, and enable it.
./bisq-cli --password=xyz --port=9998 editoffer \
  --offer-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea \
  --market-price-margin=0.5 \
  --trigger-price=33000.0000 \
  --enable=true

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.EditOfferRequest;
import bisq.proto.grpc.OffersGrpc;
import io.grpc.ManagedChannelBuilder;

import static bisq.proto.grpc.EditOfferRequest.EditType.*;
import static bisq.proto.grpc.EditOfferRequest.newBuilder;
import static java.lang.System.out;

public class EditOffer extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = OffersGrpc.newBlockingStub(channel).withCallCredentials(credentials);

            var disableOfferRequest = createDisableOfferRequest();
            stub.editOffer(disableOfferRequest);
            out.println("Offer is disabled (removed from peers' offer book).");
            waitForOfferToBeRepublished(2_500);

            var editFixedPriceRequest = createEditFixedPriceRequest();
            stub.editOffer(editFixedPriceRequest);
            out.println("Offer has been re-published with new fixed-price.");
            waitForOfferToBeRepublished(2_500);

            var editFixedPriceAndEnableRequest = createEditFixedPriceAndEnableRequest();
            stub.editOffer(editFixedPriceAndEnableRequest);
            out.println("Offer has been re-published with new fixed-price, and enabled.");
            waitForOfferToBeRepublished(2_500);

            var editPriceMarginRequest = createEditPriceMarginRequest();
            stub.editOffer(editPriceMarginRequest);
            out.println("Offer has been re-published with new price margin.");
            waitForOfferToBeRepublished(2_500);

            var editTriggerPriceRequest = createEditTriggerPriceRequest();
            stub.editOffer(editTriggerPriceRequest);
            out.println("Offer has been re-published with new trigger price.");
        } catch (Throwable t) {
            handleError(t);
        }
    }

    private static EditOfferRequest createDisableOfferRequest() {
        return newBuilder()
                .setId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                .setEditType(ACTIVATION_STATE_ONLY)
                .setEnable(0)   // -1 = ignore this parameter, 0 = disable offer, 1 = enable offer
                .build();
    }

    private static EditOfferRequest createEditFixedPriceRequest() {
        return newBuilder()
                .setId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                .setEditType(FIXED_PRICE_ONLY)
                .setPrice("30000.99")
                .build();
    }

    private static EditOfferRequest createEditFixedPriceAndEnableRequest() {
        return newBuilder()
                .setId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                .setEditType(FIXED_PRICE_AND_ACTIVATION_STATE)
                .setPrice("30000.99")
                .setEnable(1)
                .build();
    }

    private static EditOfferRequest createEditPriceMarginRequest() {
        return newBuilder()
                .setId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                .setEditType(MKT_PRICE_MARGIN_ONLY)
                .setUseMarketBasedPrice(true)
                .setMarketPriceMarginPct(2.00)  // 2.00%
                .build();
    }

    private static EditOfferRequest createEditTriggerPriceRequest() {
        return newBuilder()
                .setId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                .setEditType(TRIGGER_PRICE_ONLY)
                .setTriggerPrice("29000.00")    // Trigger price is disabled when set to "0".
                .build();
    }

    private static void waitForOfferToBeRepublished(long ms) {
        try {
            // Wait for edited offer to be removed from offer-book, edited, and re-published.
            Thread.sleep(ms);
        } catch (InterruptedException ex) {
            // ignored
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
# from getpass import getpass
import time
from builtins import print

import grpc

import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service

EDITED_USD_OFFER_ID = '44736-16df6819-d98b-4f13-87dd-50087c464fac-184'


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.OffersStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        edit_offer_request = disable_offer_request()
        edit_offer_response = grpc_service_stub.EditOffer.with_call(edit_offer_request,
                                                                    metadata=[('password', api_password)])
        print('Offer is disabled.  Rpc response: ' + str(edit_offer_response))
        time.sleep(4)  # Wait for new offer preparation and wallet updates before creating another offer.

        edit_offer_request = enable_offer_request()
        edit_offer_response = grpc_service_stub.EditOffer.with_call(edit_offer_request,
                                                                    metadata=[('password', api_password)])
        print('Offer is enabled.  Rpc response: ' + str(edit_offer_response))
        time.sleep(4)  # Wait for new offer preparation and wallet updates before creating another offer.

        edit_offer_request = edit_fixed_price_request()
        edit_offer_response = grpc_service_stub.EditOffer.with_call(edit_offer_request,
                                                                    metadata=[('password', api_password)])
        print('Offer fixed-price has been changed.  Rpc response: ' + str(edit_offer_response))
        time.sleep(4)  # Wait for new offer preparation and wallet updates before creating another offer.

        edit_offer_request = edit_fixed_price_and_enable_request()
        edit_offer_response = grpc_service_stub.EditOffer.with_call(edit_offer_request,
                                                                    metadata=[('password', api_password)])
        print('Offer fixed-price has been changed, and offer is enabled.  Rpc response: ' + str(edit_offer_response))
        time.sleep(4)  # Wait for new offer preparation and wallet updates before creating another offer.

        # Change the fixed-price offer to a mkt price margin based offer
        edit_offer_request = edit_price_margin_request()
        edit_offer_response = grpc_service_stub.EditOffer.with_call(edit_offer_request,
                                                                    metadata=[('password', api_password)])
        print('Fixed-price offer is not a mkt price margin based offer.  Rpc response: ' + str(edit_offer_response))
        time.sleep(4)  # Wait for new offer preparation and wallet updates before creating another offer.

        # Set the trigger-price on a mkt price margin based offer
        edit_offer_request = edit_trigger_price_request()
        edit_offer_response = grpc_service_stub.EditOffer.with_call(edit_offer_request,
                                                                    metadata=[('password', api_password)])
        print('Offer trigger price is set.  Rpc response: ' + str(edit_offer_response))

    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


def disable_offer_request():
    return bisq_messages.EditOfferRequest(
        id=EDITED_USD_OFFER_ID,
        edit_type=bisq_messages.EditOfferRequest.EditType.ACTIVATION_STATE_ONLY,
        enable=0)  # If enable=-1: ignore enable param, enable=0: disable offer, enable=1: enable offer


def enable_offer_request():
    return bisq_messages.EditOfferRequest(
        id=EDITED_USD_OFFER_ID,
        edit_type=bisq_messages.EditOfferRequest.EditType.ACTIVATION_STATE_ONLY,
        enable=1)  # If enable=-1: ignore enable param, enable=0: disable offer, enable=1: enable offer


def edit_fixed_price_request():
    return bisq_messages.EditOfferRequest(
        id=EDITED_USD_OFFER_ID,
        price='42000.50',
        edit_type=bisq_messages.EditOfferRequest.EditType.FIXED_PRICE_ONLY,
        enable=-1)  # If enable=-1: ignore enable param, enable=0: disable offer, enable=1: enable offer


def edit_fixed_price_and_enable_request():
    return bisq_messages.EditOfferRequest(
        id=EDITED_USD_OFFER_ID,
        price='43000.50',
        edit_type=bisq_messages.EditOfferRequest.EditType.FIXED_PRICE_AND_ACTIVATION_STATE,
        enable=1)  # If enable=-1: ignore enable param, enable=0: disable offer, enable=1: enable offer


def edit_price_margin_request():
    return bisq_messages.EditOfferRequest(
        id=EDITED_USD_OFFER_ID,
        use_market_based_price=True,
        market_price_margin_pct=5.00,
        edit_type=bisq_messages.EditOfferRequest.EditType.MKT_PRICE_MARGIN_ONLY,
        enable=-1)  # If enable=-1: ignore enable param, enable=0: disable offer, enable=1: enable offer


def edit_trigger_price_request():
    return bisq_messages.EditOfferRequest(
        id=EDITED_USD_OFFER_ID,
        trigger_price='40000.0000',
        edit_type=bisq_messages.EditOfferRequest.EditType.TRIGGER_PRICE_ONLY,
        enable=-1)  # If enable=-1: ignore enable param, enable=0: disable offer, enable=1: enable offer


if __name__ == '__main__':
    main()
```
### Unary RPC
Edit an open offer.


### gRPC Request: EditOfferRequest

### Enum: EditType
The EditType determines and constricts what offer details can be modified by the request, simplifying param
validation.  (The CLI need to infer this detail from 'editoffer' command options, other clients do not.)


 Constant | Value | Description 
 ------------- | ------------- | ------------- 
 ACTIVATION_STATE_ONLY | 0 | Edit only the offer's activation state (enabled or disabled). 
 FIXED_PRICE_ONLY | 1 | Edit only the offer's fixed price. 
 FIXED_PRICE_AND_ACTIVATION_STATE | 2 | Edit only the offer's fixed price and activation state. 
 MKT_PRICE_MARGIN_ONLY | 3 | Edit only the offer's market price margin. 
 MKT_PRICE_MARGIN_AND_ACTIVATION_STATE | 4 | Edit only the offer's market price margin and activation state. 
 TRIGGER_PRICE_ONLY | 5 | Edit only the market price margin based offer's trigger price. 
 TRIGGER_PRICE_AND_ACTIVATION_STATE | 6 | Edit only the market price margin based offer's trigger price and activation state. 
 MKT_PRICE_MARGIN_AND_TRIGGER_PRICE | 7 | Edit only the offer's market price margin and trigger price. 
 MKT_PRICE_MARGIN_AND_TRIGGER_PRICE_AND_ACTIVATION_STATE | 8 | Edit only the offer's market price margin, trigger price, and activation state.

Name | Type | Description 
 ------------- | ------------- | ------------- 
 id | string | The edited offer's unique identifier. 
 price | string | For fiat offers:  a string representing the new rounded, fixed fiat price of the offer, e.g., "45000", not "45000".<br/>For altcoin offers:  a string representing the new fixed BTC price of the offer, e.g., "0.00005".<br/> 
 use_market_based_price | bool | Whether the offer price is fixed, or market price margin based. 
 market_price_margin_pct | double | An offer's new market price margin as a percentage above or below the current market BTC price. 
 trigger_price | string | A market price margin based offer's trigger price is the market BTC price at which the offer is automatically disabled.<br/>Disabled offers are never automatically enabled, they must be manually re-enabled.<br/>A zero value indicates trigger price is not set.  Trigger price does not apply to fixed price offers.<br/> 
 enable | sint32 | Whether the offer's activation state should be changed (disable or enable), or left alone.<br/>Send a signed int, not a bool (with default=false).<br/>-1 = do not change activation state<br/>0 = disable<br/>1 = enable<br/> 
 edit_type | EditType | Tell the daemon precisely what is being edited.

### gRPC Response: EditOfferReply

This Response has no parameters.

## RPC Method GetBsqSwapOffer
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 getoffer \
  --offer-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetOfferRequest;
import bisq.proto.grpc.OffersGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetBsqSwapOffer extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = OffersGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetOfferRequest.newBuilder()
                    .setId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                    .build();
            var response = stub.getBsqSwapOffer(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.OffersStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetBsqSwapOffer.with_call(
            bisq_messages.GetOfferRequest(id='VZLGFPV-e8dd2f8c-fc90-4509-8f30-e0bb95815b46-184'),
            metadata=[('password', api_password)])
        print('Response: ' + str(response[0].bsq_swap_offer))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get the available BSQ swap offer with offer-id.


### gRPC Request: GetOfferRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 id | string | The offer's unique identifier.

### gRPC Response: GetBsqSwapOfferReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 bsq_swap_offer | [OfferInfo](#offerinfo) | The returned BSQ swap offer.

## RPC Method GetBsqSwapOffers
```shell
#!/bin/bash
# Get available BSQ swap offers to buy BTC with BSQ.
./bisq-cli --password=xyz --port=9998 getoffers --direction=BUY --currency-code=BSQ
# Get available BSQ swap offers to sell BTC for BSQ.
./bisq-cli --password=xyz --port=9998 getoffers --direction=SELL --currency-code=BSQ

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetBsqSwapOffersRequest;
import bisq.proto.grpc.OffersGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetBsqSwapOffers extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = OffersGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetBsqSwapOffersRequest.newBuilder()
                    .setDirection("SELL")   // Offers to sell (swap) BTC for BSQ.
                    .build();
            var response = stub.getBsqSwapOffers(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.OffersStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetBsqSwapOffers.with_call(
            bisq_messages.GetBsqSwapOffersRequest(direction='SELL'),
            metadata=[('password', api_password)])
        print('Response: ' + str(response[0].bsq_swap_offers))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get all available BSQ swap offers with a BUY (BTC) or SELL (BTC) direction.


### gRPC Request: GetBsqSwapOffersRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 direction | string | The BSQ swap offer's BUY (BTC) or SELL (BTC) direction.

### gRPC Response: GetBsqSwapOffersReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 bsq_swap_offers | [array OfferInfo](#offerinfo) | The returned list of available BSQ swap offers.

## RPC Method GetMyBsqSwapOffer
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 getmyoffer \
  --offer-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetMyOfferRequest;
import bisq.proto.grpc.OffersGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetMyBsqSwapOffer extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = OffersGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetMyOfferRequest.newBuilder()
                    .setId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                    .build();
            var response = stub.getMyBsqSwapOffer(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.OffersStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetMyBsqSwapOffer.with_call(
            bisq_messages.GetMyOfferRequest(id='gzcvxum-63c23b0b-6acd-49ba-a956-55e406994da1-184'),
            metadata=[('password', api_password)])
        print('Response: ' + str(response[0].bsq_swap_offer))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get user's BSQ swap offer with offer-id.


### gRPC Request: GetMyOfferRequest
Deprecated with rpc method GetMyOffer since 27-Dec-2021 (v1.8.0).

Name | Type | Description 
 ------------- | ------------- | ------------- 
 id | string | The offer's unique identifier.

### gRPC Response: GetMyBsqSwapOfferReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 bsq_swap_offer | [OfferInfo](#offerinfo) | The returned BSQ swap offer.

## RPC Method GetMyBsqSwapOffers
```shell
#!/bin/bash
# Get my BSQ swap offers to buy BTC for BSQ.
./bisq-cli --password=xyz --port=9998 getmyoffers --direction=BUY --currency-code=BSQ
# Get my BSQ swap offers to sell BTC for BSQ.
./bisq-cli --password=xyz --port=9998 getmyoffers --direction=SELL --currency-code=BSQ

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetBsqSwapOffersRequest;
import bisq.proto.grpc.OffersGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetMyBsqSwapOffers extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = OffersGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetBsqSwapOffersRequest.newBuilder()
                    .setDirection("BUY")   // Offers to buy (swap) BTC for BSQ.
                    .build();
            var response = stub.getBsqSwapOffers(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.OffersStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetMyBsqSwapOffers.with_call(
            bisq_messages.GetBsqSwapOffersRequest(direction='BUY'),  # My buy BTC for BSQ swap offers
            metadata=[('password', api_password)])
        offers = list(response[0].bsq_swap_offers)
        print('Response: ' + str(offers))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get all user's BSQ swap offers with a BUY (BTC) or SELL (BTC) direction.


### gRPC Request: GetBsqSwapOffersRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 direction | string | The BSQ swap offer's BUY (BTC) or SELL (BTC) direction.

### gRPC Response: GetMyBsqSwapOffersReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 bsq_swap_offers | [array OfferInfo](#offerinfo) | The returned list of user's open BSQ swap offers.

## RPC Method GetMyOffer
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 getmyoffer \
  --offer-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetMyOfferRequest;
import bisq.proto.grpc.OffersGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetMyOffer extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = OffersGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetMyOfferRequest.newBuilder()
                    .setId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                    .build();
            var response = stub.getMyOffer(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.OffersStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetMyOffer.with_call(
            bisq_messages.GetMyOfferRequest(id='QusccrDV-47ae5521-bda1-4f3c-801b-5c193f957df7-184'),
            metadata=[('password', api_password)])
        print('Response: ' + str(response[0].offer))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get my open v1 protocol offer with offer-id.  Deprecated since 27-Dec-2021 (v1.8.0).  Use GetOffer.


### gRPC Request: GetMyOfferRequest
Deprecated with rpc method GetMyOffer since 27-Dec-2021 (v1.8.0).

Name | Type | Description 
 ------------- | ------------- | ------------- 
 id | string | The offer's unique identifier.

### gRPC Response: GetMyOfferReply
Deprecated with rpc method GetMyOffer since 27-Dec-2021 (v1.8.0).

Name | Type | Description 
 ------------- | ------------- | ------------- 
 offer | [OfferInfo](#offerinfo) | The returned v1 protocol offer.

## RPC Method GetMyOffers
```shell
#!/bin/bash
# Get my offers to buy BTC with EUR.
./bisq-cli --password=xyz --port=9998 getmyoffers --direction=BUY --currency-code=EUR
# Get my offers to sell BTC for EUR.
./bisq-cli --password=xyz --port=9998 getmyoffers --direction=SELL --currency-code=EUR

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetMyOffersRequest;
import bisq.proto.grpc.OffersGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetMyOffers extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = OffersGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetMyOffersRequest.newBuilder()
                    .setDirection("BUY")        // Offers to buy BTC
                    .setCurrencyCode("USD")     // with USD
                    .build();
            var response = stub.getMyOffers(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.OffersStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetMyOffers.with_call(
            bisq_messages.GetMyOffersRequest(
                direction='BUY',
                currency_code='USD'),
            metadata=[('password', api_password)])
        offers = list(response[0].offers)
        print('Response: ' + str(offers))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get all user's open v1 protocol offers with a BUY (BTC) or SELL (BTC) direction.


### gRPC Request: GetMyOffersRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 direction | string | The offers' BUY (BTC) or SELL (BTC) direction. 
 currency_code | string | The offer's fiat or altcoin currency code.

### gRPC Response: GetMyOffersReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 offers | [array OfferInfo](#offerinfo) | The returned list of user's open offers.

## RPC Method GetOffer
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 getoffer \
  --offer-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetOfferRequest;
import bisq.proto.grpc.OffersGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetOffer extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = OffersGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetOfferRequest.newBuilder()
                    .setId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                    .build();
            var response = stub.getOffer(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.OffersStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetOffer.with_call(
            bisq_messages.GetOfferRequest(id='VZLGFPV-e8dd2f8c-fc90-4509-8f30-e0bb95815b46-184'),
            metadata=[('password', api_password)])
        print('Response: ' + str(response[0].offer))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get the v1 protocol offer with offer-id.


### gRPC Request: GetOfferRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 id | string | The offer's unique identifier.

### gRPC Response: GetOfferReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 offer | [OfferInfo](#offerinfo) | The returned v1 protocol offer.

## RPC Method GetOfferCategory
```shell
# Used internally by CLI;  there is no user CLI command 'getoffercategory'.

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetOfferCategoryRequest;
import bisq.proto.grpc.OffersGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetOfferCategory extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = OffersGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetOfferCategoryRequest.newBuilder()
                    .setId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                    .build();
            var response = stub.getOfferCategory(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
# from getpass import getpass
from builtins import print

import grpc

import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.OffersStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetOfferCategory.with_call(
            bisq_messages.GetOfferCategoryRequest(
                id='VZLGFPV-e8dd2f8c-fc90-4509-8f30-e0bb95815b46-184',
                is_my_offer=False),
            metadata=[('password', api_password)])
        print('Response: ' + str(response[0].offer_category))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get an offer's category, one of  FIAT, ALTCOIN, or BSQ_SWAP.  This information is needed before an offer
can be taken, and is used by a client to determine what kind of offer to take:  a v1 FIAT or ALTCOIN offer,
or a BSQ swap offer.  V1 and BSQ swap trades are handled differently in the API daemon.


### gRPC Request: GetOfferCategoryRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 id | string | The offer's unique identifier. 
 is_my_offer | bool | Whether the offer was created by the user or not.

### gRPC Response: GetOfferCategoryReply

### Enum: OfferCategory


 Constant | Value | Description 
 ------------- | ------------- | ------------- 
 UNKNOWN | 0 | An invalid offer category probably indicates a software bug. 
 FIAT | 1 | Indicates offer is to BUY or SELL BTC with a fiat currency. 
 ALTCOIN | 2 | Indicates offer is to BUY or SELL BTC with an altcoin. 
 BSQ_SWAP | 3 | Indicates offer is to swap BTC for BSQ.

Name | Type | Description 
 ------------- | ------------- | ------------- 
 offer_category | OfferCategory |

## RPC Method GetOffers
```shell
#!/bin/bash
# Get available offers to buy BTC with JPY.
./bisq-cli --password=xyz --port=9998 getoffers --direction=BUY --currency-code=JPY
# Get available offers to sell BTC for JPY.
./bisq-cli --password=xyz --port=9998 getoffers --direction=SELL --currency-code=JPY

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetOffersRequest;
import bisq.proto.grpc.OffersGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetOffers extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = OffersGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetOffersRequest.newBuilder()
                    .setDirection("SELL")       // Available offers to sell BTC
                    .setCurrencyCode("JPY")     // for JPY
                    .build();
            var response = stub.getOffers(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.OffersStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetOffers.with_call(
            bisq_messages.GetOffersRequest(
                direction='SELL',
                currency_code='USD'),
            metadata=[('password', api_password)])
        offers = list(response[0].offers)
        print('Response: ' + str(offers))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get all available v1 protocol offers with a BUY (BTC) or SELL (BTC) direction.


### gRPC Request: GetOffersRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 direction | string | The offer's BUY (BTC) or SELL (BTC) direction. 
 currency_code | string | The offer's fiat or altcoin currency code.

### gRPC Response: GetOffersReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 offers | [array OfferInfo](#offerinfo) | The returned list of available offers.

# Service PaymentAccounts
The PaymentAccounts service provides rpc methods for creating fiat and crypto currency payment accounts.

## RPC Method CreateCryptoCurrencyPaymentAccount
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 createcryptopaymentacct \
  --account-name="My XMR Payment Account" \
  --currency-code=XMR \
  --address=4AsjtNXChh3Va58czCWHjn9S8ZFnsxggGZoSePauBHmSMr8vY5aBSqrPtQ9Y9M1iwkBHxcuTWXJsJ4NDATQjQJyKBXR7WP7 \
  --trade-instant=false

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.CreateCryptoCurrencyPaymentAccountRequest;
import bisq.proto.grpc.PaymentAccountsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class CreateCryptoCurrencyPaymentAccount extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = PaymentAccountsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var xmrAddress = "4AsjtNXChh3Va58czCWHjn9S8ZFnsxggGZoSePauBHmSMr8vY5aBSqrPtQ9Y9M1iwkBHxcuTWXJsJ4NDATQjQJyKBXR7WP7";
            var request = CreateCryptoCurrencyPaymentAccountRequest.newBuilder()
                    .setAccountName("My Instant XMR Payment Account")
                    .setCurrencyCode("XMR")
                    .setAddress(xmrAddress)
                    .setTradeInstant(true)
                    .build();
            var response = stub.createCryptoCurrencyPaymentAccount(request);
            out.println("New XMR instant payment account: " + response.getPaymentAccount());
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.PaymentAccountsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.CreateCryptoCurrencyPaymentAccount.with_call(
            bisq_messages.CreateCryptoCurrencyPaymentAccountRequest(
                account_name='name',
                currency_code='XMR',
                address='472CJ9TADoeVabhAe6byZQN4yqAFA4morKiyzb8DfLTj4hcQvsXNHxJUNYMw1JDmMALkQ3Bwmyn4aZYST7DzEw9nUeUTKVL',
                trade_instant=False),
            metadata=[('password', api_password)])
        print('Response: ' + str(response[0].payment_account))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Create a crypto currency (altcoin) payment account.


### gRPC Request: CreateCryptoCurrencyPaymentAccountRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 account_name | string | The name of the altcoin payment account.  Uniqueness is not enforced. 
 currency_code | string | The altcoin currency code. 
 address | string | The altcoin receiving address. 
 trade_instant | bool | Whether the altcoin payment account is an instant account or not.

### gRPC Response: CreateCryptoCurrencyPaymentAccountReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 payment_account | [PaymentAccount](#paymentaccount) | The new altcoin payment account.

## RPC Method CreatePaymentAccount
```shell
#!/bin/bash
# Create a swift fiat payment account, providing details in a json form generated by getpaymentacctform.
./bisq-cli --password=xyz --port=9998 createpaymentacct --payment-account-form=swift.json

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.CreatePaymentAccountRequest;
import bisq.proto.grpc.PaymentAccountsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class CreatePaymentAccount extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = PaymentAccountsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            // Fill in the swift payment account json form generated by GetPaymentAccountForm.
            var request = CreatePaymentAccountRequest.newBuilder()
                    .setPaymentAccountForm("/path/to/swift.json")
                    .build();
            var response = stub.createPaymentAccount(request);
            out.println("New swift payment account: " + response.getPaymentAccount());
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.PaymentAccountsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        # Convert a .json form to a string and send it in the request.
        with open('/tmp/sepa-alice.json', 'r') as file:
            json = file.read()
        response = grpc_service_stub.CreatePaymentAccount.with_call(
            bisq_messages.CreatePaymentAccountRequest(payment_account_form=json),
            metadata=[('password', api_password)])
        print('Response: ' + str(response[0].payment_account))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Create a fiat payment account, providing details in a json form generated by rpc method GetPaymentAccountForm.


### gRPC Request: CreatePaymentAccountRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 payment_account_form | string | File path of filled json payment account form.

### gRPC Response: CreatePaymentAccountReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 payment_account | [PaymentAccount](#paymentaccount) | The new payment account.

## RPC Method GetCryptoCurrencyPaymentMethods
```shell
#!/bin/bash
# Not yet supported in CLI.  API currently supports only BSQ, BSQ Swap, and XMR trading.

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetCryptoCurrencyPaymentMethodsRequest;
import bisq.proto.grpc.PaymentAccountsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetCryptoCurrencyPaymentMethods extends BaseJavaExample {
    // Note:  API currently supports only BSQ, BSQ Swap, and XMR trading.
    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = PaymentAccountsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetCryptoCurrencyPaymentMethodsRequest.newBuilder().build();
            var response = stub.getCryptoCurrencyPaymentMethods(request);
            out.println("Response: " + response.getPaymentMethodsList());
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.PaymentAccountsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetCryptoCurrencyPaymentMethods.with_call(
            bisq_messages.GetCryptoCurrencyPaymentMethodsRequest(),
            metadata=[('password', api_password)])
        payment_methods = list(response[0].payment_methods)
        print('Response contains {0} payment methods: '.format(len(payment_methods)))
        for payment_method in payment_methods:
            print('\t{0}'.format(payment_method.id))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get list of all supported Bisq crypto currency (altcoin) payment methods.


### gRPC Request: GetCryptoCurrencyPaymentMethodsRequest

This Request has no parameters.

### gRPC Response: GetCryptoCurrencyPaymentMethodsReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 payment_methods | [array PaymentMethod](#paymentmethod) | Ids of all supported Bisq altcoin payment methods.

## RPC Method GetPaymentAccountForm
```shell
#!/bin/bash
# Get a blank SWIFT payment account form and save the json file in the current working directory.
./bisq-cli --password=xyz --port=9998 getpaymentacctform --payment-method-id=SWIFT

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetPaymentAccountFormRequest;
import bisq.proto.grpc.PaymentAccountsGrpc;
import io.grpc.ManagedChannelBuilder;

import java.io.BufferedWriter;
import java.io.File;
import java.io.FileWriter;

import static java.lang.System.out;

public class GetPaymentAccountForm extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = PaymentAccountsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetPaymentAccountFormRequest.newBuilder()
                    .setPaymentMethodId("SWIFT")
                    .build();
            var response = stub.getPaymentAccountForm(request);
            var paymentAccountFormJson = response.getPaymentAccountFormJson();
            File jsonFile = new File("/tmp/blank-swift-account-form.json");
            BufferedWriter writer = new BufferedWriter(new FileWriter(jsonFile));
            writer.write(paymentAccountFormJson);
            writer.close();
            out.println("Swift payment account saved to " + jsonFile.getAbsolutePath());
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
import os
import tempfile
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.PaymentAccountsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetPaymentAccountForm.with_call(
            bisq_messages.GetPaymentAccountFormRequest(payment_method_id='SWIFT'),
            metadata=[('password', api_password)])
        json_string = response[0].payment_account_form_json
        print('Response: ' + json_string)
        # The client should save this json string to file, manually fill in the form
        # fields, then use it as shown in the create_payment_account.py example.
        json_filename = os.sep.join([tempfile.gettempdir(), 'swift-account-form.json'])
        print('Write response to json file:  {0}'.format(json_filename))
        json_file = open(json_filename, "w")
        num_written_chars = json_file.write(json_string)
        print('Wrote {0} characters to {1}'.format(num_written_chars, json_file.name))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get a json template file for a supported Bisq payment method.  Fill in the form and call rpc method CreatePaymentAccount.


### gRPC Request: GetPaymentAccountFormRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 payment_method_id | string | Payment method id determining content of the requested payment account form.

### gRPC Response: GetPaymentAccountFormReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 payment_account_form_json | string | An empty payment account json form to be filled out and passed to rpc method CreatePaymentAccount.

## RPC Method GetPaymentAccounts
```shell
#!/bin/bash
# Get list of all saved payment accounts, including altcoin accounts.
./bisq-cli --password=xyz --port=9998 getpaymentaccts

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetPaymentAccountsRequest;
import bisq.proto.grpc.PaymentAccountsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetPaymentAccounts extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = PaymentAccountsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetPaymentAccountsRequest.newBuilder().build();
            var response = stub.getPaymentAccounts(request);
            out.println("Response: " + response.getPaymentAccountsList());
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.PaymentAccountsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetPaymentAccounts.with_call(
            bisq_messages.GetPaymentAccountsRequest(),
            metadata=[('password', api_password)])
        payment_accounts = list(response[0].payment_accounts)
        print('Response: {0} payment accounts'.format(len(payment_accounts)))
        print('\t\t{0:<40} {1:<24} {2:<20} {3:<8}'.format('ID', 'Name', 'Payment Method', 'Trade Currency'))
        for payment_account in payment_accounts:
            print('\t\t{0:<40} {1:<24} {2:<20} {3:<8}'
                  .format(payment_account.id,
                          payment_account.account_name,
                          payment_account.payment_method.id,
                          payment_account.selected_trade_currency.code))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get list of all saved fiat payment accounts.


### gRPC Request: GetPaymentAccountsRequest

This Request has no parameters.

### gRPC Response: GetPaymentAccountsReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 payment_accounts | [array PaymentAccount](#paymentaccount) | All user's saved payment accounts.

## RPC Method GetPaymentMethods
```shell
#!/bin/bash
# Get the ids of all supported Bisq payment methods.
./bisq-cli --password=xyz --port=9998 getpaymentmethods

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetPaymentMethodsRequest;
import bisq.proto.grpc.PaymentAccountsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetPaymentMethods extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = PaymentAccountsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetPaymentMethodsRequest.newBuilder().build();
            var response = stub.getPaymentMethods(request);
            out.println("Response: " + response.getPaymentMethodsList());
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.PaymentAccountsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetPaymentMethods.with_call(
            bisq_messages.GetPaymentMethodsRequest(),
            metadata=[('password', api_password)])
        payment_methods = list(response[0].payment_methods)
        print('Response: {0} payment methods'.format(len(payment_methods)))
        for payment_method in payment_methods:
            print('\t\t{0}'.format(payment_method.id))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get list of all supported Bisq payment methods.


### gRPC Request: GetPaymentMethodsRequest

This Request has no parameters.

### gRPC Response: GetPaymentMethodsReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 payment_methods | [array PaymentMethod](#paymentmethod) | Ids of all supported Bisq fiat payment methods.

# Service Price

## RPC Method GetMarketPrice
```shell
#!/bin/bash
# Get most recently available market price of XMR in BTC.
./bisq-cli --password=xyz --port=9998 getbtcprice --currency-code=XMR

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.MarketPriceRequest;
import bisq.proto.grpc.PriceGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetMarketPrice extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = PriceGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = MarketPriceRequest.newBuilder()
                    .setCurrencyCode("XMR")
                    .build();
            var response = stub.getMarketPrice(request);
            out.println("Most recently available XMR market price: " + response.getPrice());
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.PriceStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetMarketPrice.with_call(
            bisq_messages.MarketPriceRequest(currency_code='USD'),
            metadata=[('password', api_password)])
        print('Response: ' + str(response))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get the current market price for a crypto currency.


### gRPC Request: MarketPriceRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 currency_code | string | The three letter currency code.

### gRPC Response: MarketPriceReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 price | double | The most recently available market price.

# Service ShutdownServer

## RPC Method Stop
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 stop

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.ShutdownServerGrpc;
import bisq.proto.grpc.StopRequest;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class Stop extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = ShutdownServerGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = StopRequest.newBuilder().build();
            stub.stop(request);
            out.println("Daemon is shutting down.");
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.ShutdownServerStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.Stop.with_call(bisq_messages.StopRequest(),
                                                    metadata=[('password', api_password)])
        print('Response: ' + str(response))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Shut down a local Bisq daemon.


### gRPC Request: StopRequest

This Request has no parameters.

### gRPC Response: StopReply

This Response has no parameters.

# Service Trades
The Trades service provides rpc methods for taking, executing, and listing trades.

## RPC Method CloseTrade
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 closetrade --trade-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.CloseTradeRequest;
import bisq.proto.grpc.TradesGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class CloseTrade extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = TradesGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = CloseTradeRequest.newBuilder()
                    .setTradeId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                    .build();
            stub.closeTrade(request);
            out.println("Open trade is closed.");
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.TradesStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.CloseTrade.with_call(
            bisq_messages.CloseTradeRequest(trade_id='83e8b2e2-51b6-4f39-a748-3ebd29c22aea'),
            metadata=[('password', api_password)])
        print('Response: ' + str(response))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Close a completed trade;  move it to trade history.


### gRPC Request: CloseTradeRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 trade_id | string | The unique identifier of the trade.

### gRPC Response: CloseTradeReply

This Response has no parameters.

## RPC Method ConfirmPaymentReceived
```shell
#!/bin/bash
# Send message to BTC buyer that payment has been received.
./bisq-cli --password=xyz --port=9998 confirmpaymentreceived \
  --trade-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.ConfirmPaymentReceivedRequest;
import bisq.proto.grpc.TradesGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class ConfirmPaymentReceived extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = TradesGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = ConfirmPaymentReceivedRequest.newBuilder()
                    .setTradeId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                    .build();
            stub.confirmPaymentReceived(request);
            out.println("Payment receipt confirmation message has been sent to BTC buyer.");
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.TradesStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.ConfirmPaymentReceived.with_call(
            bisq_messages.ConfirmPaymentReceivedRequest(trade_id='83e8b2e2-51b6-4f39-a748-3ebd29c22aea'),
            metadata=[('password', api_password)])
        print('Response: ' + str(response))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Send a 'payment received' message to a trading peer (the BTC buyer).


### gRPC Request: ConfirmPaymentReceivedRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 trade_id | string | The unique identifier of the open trade.

### gRPC Response: ConfirmPaymentReceivedReply

This Response has no parameters.

## RPC Method ConfirmPaymentStarted
```shell
#!/bin/bash
# Send message to BTC seller that payment has been sent.
./bisq-cli --password=xyz --port=9998 confirmpaymentstarted \
  --trade-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.ConfirmPaymentStartedRequest;
import bisq.proto.grpc.TradesGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class ConfirmPaymentStarted extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = TradesGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = ConfirmPaymentStartedRequest.newBuilder()
                    .setTradeId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                    .build();
            stub.confirmPaymentStarted(request);
            out.println("Payment started message has been sent to BTC seller.");
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.TradesStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.ConfirmPaymentStarted.with_call(
            bisq_messages.ConfirmPaymentStartedRequest(trade_id='83e8b2e2-51b6-4f39-a748-3ebd29c22aea'),
            metadata=[('password', api_password)])
        print('Response: ' + str(response))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Send a 'payment started' message to a trading peer (the BTC seller).


### gRPC Request: ConfirmPaymentStartedRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 trade_id | string | The unique identifier of the open trade.

### gRPC Response: ConfirmPaymentStartedReply

This Response has no parameters.

## RPC Method FailTrade
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 failtrade --trade-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.FailTradeRequest;
import bisq.proto.grpc.TradesGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class FailTrade extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = TradesGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = FailTradeRequest.newBuilder()
                    .setTradeId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                    .build();
            stub.failTrade(request);
            out.println("Open trade has been moved to failed trades list.");
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.TradesStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.FailTrade.with_call(
            bisq_messages.FailTradeRequest(trade_id='HDCXKUR-1cfb39e9-68b9-4772-8ae0-abceb8339c90-184'),
            metadata=[('password', api_password)])
        print('Response: ' + str(response))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Fail an open trade.


### gRPC Request: FailTradeRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 trade_id | string | The unique identifier of the trade.

### gRPC Response: FailTradeReply

This Response has no parameters.

## RPC Method GetTrade
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 gettrade --trade-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetTradeRequest;
import bisq.proto.grpc.TradesGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetTrade extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = TradesGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetTradeRequest.newBuilder()
                    .setTradeId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                    .build();
            var response = stub.getTrade(request);
            out.println("Trade: " + response.getTrade());
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.TradesStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetTrade.with_call(
            bisq_messages.GetTradeRequest(trade_id='87533-abc12dcd-b12f-499d-8594-e6ee39630d50-184'),
            metadata=[('password', api_password)])
        print('Response: ' + str(response[0].trade))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get a currently open trade.


### gRPC Request: GetTradeRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 trade_id | string | The unique identifier of the trade.

### gRPC Response: GetTradeReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 trade | [TradeInfo](#tradeinfo) | The unique identifier of the trade.

## RPC Method GetTrades
```shell
#!/bin/bash
# Get currently open trades.
./bisq-cli --password=xyz --port=9998 gettrades
./bisq-cli --password=xyz --port=9998 gettrades --category=open

# Get completed trades.
./bisq-cli --password=xyz --port=9998 gettrades --category=closed

# Get failed trades.
./bisq-cli --password=xyz --port=9998 gettrades --category=failed

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetTradesRequest;
import bisq.proto.grpc.TradesGrpc;
import io.grpc.ManagedChannelBuilder;

import static bisq.proto.grpc.GetTradesRequest.Category.CLOSED;
import static java.lang.System.out;

public class GetTrades extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = TradesGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetTradesRequest.newBuilder()
                    .setCategory(CLOSED) // Or currently OPEN, or FAILED
                    .build();
            var response = stub.getTrades(request);
            out.println("Open trades: " + response.getTradesList());
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.TradesStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetTrades.with_call(
            bisq_messages.GetTradesRequest(
                category=bisq_messages.GetTradesRequest.Category.OPEN),
            metadata=[('password', api_password)])
        trades = list(response[0].trades)
        print('Response: ' + str(trades))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get currently open, or historical trades (closed or failed).


### gRPC Request: GetTradesRequest

### Enum: Category
Rpc method GetTrades parameter determining what category of trade list is is being requested.

 Constant | Value | Description 
 ------------- | ------------- | ------------- 
 OPEN | 0 | Get all currently open trades. 
 CLOSED | 1 | Get all completed trades. 
 FAILED | 2 | Get all failed trades.

Name | Type | Description 
 ------------- | ------------- | ------------- 
 category | Category |

### gRPC Response: GetTradesReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 trades | [array TradeInfo](#tradeinfo) | All trades for GetTradesRequest.Category.

## RPC Method TakeOffer
```shell
#!/bin/bash

# Take a BSQ swap offer.
./bisq-cli --password=xyz --port=9998 takeoffer --offer-id=8368b2e2-anb6-4ty9-ab09-3ebdk34f2aea

# Take an offer that is not a BSQ swap offer.
# The payment-account-id param is required, the fee-currency param is optional.
./bisq-cli --password=xyz --port=9998 takeoffer \
    --offer-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea \
    --payment-account-id=fe20cdbd-22be-4b8a-a4b6-d2608ff09d6e \
    --fee-currency=BTC

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetOfferCategoryRequest;
import bisq.proto.grpc.OffersGrpc;
import bisq.proto.grpc.TakeOfferRequest;
import bisq.proto.grpc.TradesGrpc;
import io.grpc.ManagedChannelBuilder;

import static bisq.proto.grpc.GetOfferCategoryReply.OfferCategory.BSQ_SWAP;
import static java.lang.System.err;
import static java.lang.System.out;

public class TakeOffer extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var offersStub = OffersGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var tradesStub = TradesGrpc.newBlockingStub(channel).withCallCredentials(credentials);

            // We need to send our payment account id, and the taker fee currency code if offer is
            // not a BSQ swap offer.  Find out by calling GetOfferCategory before taking the offer.
            var getOfferCategoryRequest = GetOfferCategoryRequest.newBuilder()
                    .setId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                    .build();
            var offerCategory = offersStub.getOfferCategory(getOfferCategoryRequest);
            // Create a takeoffer request builder with just the offerId parameter.
            var takeOfferRequestBuilder = TakeOfferRequest.newBuilder()
                    .setOfferId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea");
            //  If offer is not a BSQ swap offer, add the paymentAccountId and takerFeeCurrencyCode parameters.
            if (!offerCategory.equals(BSQ_SWAP))
                takeOfferRequestBuilder
                        .setPaymentAccountId("f3c1ec8b-9761-458d-b13d-9039c6892413")
                        .setTakerFeeCurrencyCode("BSQ");

            var takeOfferRequest = takeOfferRequestBuilder.build();
            var takeOfferResponse = tradesStub.takeOffer(takeOfferRequest);
            if (takeOfferResponse.hasFailureReason())
                err.println("Take offer failure reason: " + takeOfferResponse.getFailureReason());
            else
                out.println("New trade: " + takeOfferResponse.getTrade());
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
# from getpass import getpass
from builtins import print

import grpc

import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9999')
    grpc_offers_service_stub = bisq_service.OffersStub(grpc_channel)
    grpc_trades_service_stub = bisq_service.TradesStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        # We need to send our payment account id and an (optional) taker fee currency code if offer
        # is not a BSQ swap offer.  Find out by calling GetOfferCategory before taking the offer.
        get_offer_category_response = grpc_offers_service_stub.GetOfferCategory.with_call(
            bisq_messages.GetOfferCategoryRequest(id='4940749-73a2e9c3-d5b9-440a-a05d-9feb8e8805f0-182'),
            metadata=[('password', api_password)])
        offer_category = get_offer_category_response[0].offer_category
        is_bsq_swap = offer_category == bisq_messages.GetOfferCategoryReply.BSQ_SWAP
        take_offer_request = bisq_messages.TakeOfferRequest(offer_id='4940749-73a2e9c3-d5b9-440a-a05d-9feb8e8805f0-182')
        if not is_bsq_swap:
            take_offer_request.payment_account_id = '44838060-ddb5-4fa4-8b34-c128a655316e'
            take_offer_request.taker_fee_currency_code = 'BSQ'
        response = grpc_trades_service_stub.TakeOffer.with_call(
            take_offer_request,
            metadata=[('password', api_password)])
        print('Response: ' + str(response))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Take an open offer.


### gRPC Request: TakeOfferRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 offer_id | string | The unique identifier of the offer being taken. 
 payment_account_id | string | The unique identifier of the payment account used to take offer.. 
 taker_fee_currency_code | string | The code of the currency (BSQ or BTC) used to pay the taker's Bisq trade fee.

### gRPC Response: TakeOfferReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 trade | [TradeInfo](#tradeinfo) | The new trade. 
 failure_reason | [AvailabilityResultWithDescription](#availabilityresultwithdescription) | The reason the offer could not be taken.

## RPC Method UnFailTrade
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 unfailtrade --trade-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.TradesGrpc;
import bisq.proto.grpc.UnFailTradeRequest;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class UnFailTrade extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = TradesGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = UnFailTradeRequest.newBuilder()
                    .setTradeId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                    .build();
            stub.unFailTrade(request);
            out.println("Failed trade has been moved to open trades list.");
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
```
### Unary RPC
Unfail a failed trade.


### gRPC Request: UnFailTradeRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 trade_id | string | The unique identifier of the trade.

### gRPC Response: UnFailTradeReply

This Response has no parameters.

## RPC Method WithdrawFunds
```shell
#!/bin/bash
# Withdraw BTC trade proceeds to external bitcoin wallet.
./bisq-cli --password=xyz --port=9998 withdrawfunds \
  --trade-id=83e8b2e2-51b6-4f39-a748-3ebd29c22aea \
  --address=bcrt1qqau7ad7lf8xx08mnxl709h6cdv4ma9u3ace5k2 \
  --memo="Optional memo saved with transaction in Bisq wallet."

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.TradesGrpc;
import bisq.proto.grpc.WithdrawFundsRequest;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class WithdrawFunds extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = TradesGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = WithdrawFundsRequest.newBuilder()
                    .setTradeId("83e8b2e2-51b6-4f39-a748-3ebd29c22aea")
                    .setAddress("bcrt1qqau7ad7lf8xx08mnxl709h6cdv4ma9u3ace5k2")
                    .setMemo("Optional memo saved with transaction in Bisq wallet.")
                    .build();
            stub.withdrawFunds(request);
            out.println("BTC trade proceeds have been sent to external bitcoin wallet.");
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.TradesStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.WithdrawFunds.with_call(
            bisq_messages.WithdrawFundsRequest(trade_id='83e8b2e2-51b6-4f39-a748-3ebd29c22aea'),
            metadata=[('password', api_password)])
        print('Response: ' + str(response))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Withdraw trade proceeds to an external bitcoin wallet address.


### gRPC Request: WithdrawFundsRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 trade_id | string | The unique identifier of the trade. 
 address | string | The receiver's bitcoin wallet address. 
 memo | string | An optional memo saved with the sent btc transaction.

### gRPC Response: WithdrawFundsReply

This Response has no parameters.

# Service Wallets
The Wallets service provides rpc methods for basic wallet operations such as checking balances,
 sending BTC or BSQ to external wallets, checking transaction fee rates, setting or unsetting
 an encryption password on a a wallet, and unlocking / locking an encrypted wallet.

## RPC Method GetAddressBalance
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 getaddressbalance --address=bcrt1qygvsqmyt8jyhtp7l3zwqm7s7v3nar6vkc2luz3

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetAddressBalanceRequest;
import bisq.proto.grpc.WalletsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetAddressBalance extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = WalletsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetAddressBalanceRequest.newBuilder()
                    .setAddress("mwLYmweQf2dCgAqQCb3qU2UbxBycVBi2PW")
                    .build();
            var response = stub.getAddressBalance(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.WalletsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetAddressBalance.with_call(
            bisq_messages.GetAddressBalanceRequest(address='mwLYmweQf2dCgAqQCb3qU2UbxBycVBi2PW'),
            metadata=[('password', api_password)])
        address_balance_info = response[0].address_balance_info
        print('Address = {0}\nAvailable Balance = {1} sats\nUnused? {2}\nNum confirmations of most recent tx = {3}'
              .format(address_balance_info.address,
                      address_balance_info.balance,
                      address_balance_info.is_address_unused,
                      address_balance_info.num_confirmations))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get BTC balance for a wallet address.


### gRPC Request: GetAddressBalanceRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 address | string | The BTC wallet address being queried.

### gRPC Response: GetAddressBalanceReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 address_balance_info | [AddressBalanceInfo](#addressbalanceinfo) | The BTC wallet address with its balance summary.

## RPC Method GetBalances
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 getbalance
./bisq-cli --password=xyz --port=9998 getbalance --currency-code=BSQ
./bisq-cli --password=xyz --port=9998 getbalance --currency-code=BTC

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetBalancesRequest;
import bisq.proto.grpc.WalletsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetBalances extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = WalletsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetBalancesRequest.newBuilder().build();
            var response = stub.getBalances(request);
            out.println("BSQ " + response.getBalances().getBsq());
            out.println("BTC " + response.getBalances().getBtc());
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.WalletsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetBalances.with_call(
            bisq_messages.GetBalancesRequest(),
            metadata=[('password', api_password)])
        print('BTC Balances: ' + str(response[0].balances.bsq))
        print('BSQ Balances: ' + str(response[0].balances.btc))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get the Bisq wallet's current BSQ and BTC balances.


### gRPC Request: GetBalancesRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 currency_code | string | The Bisq wallet currency (BSQ or BTC) for the balances request.

### gRPC Response: GetBalancesReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 balances | [BalancesInfo](#balancesinfo) | The summary of Bisq wallet's BSQ and BTC balances.

## RPC Method GetFundingAddresses
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 getfundingaddresses

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetFundingAddressesRequest;
import bisq.proto.grpc.WalletsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetFundingAddresses extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = WalletsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetFundingAddressesRequest.newBuilder().build();
            var response = stub.getFundingAddresses(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.WalletsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetFundingAddresses.with_call(
            bisq_messages.GetFundingAddressesRequest(),
            metadata=[('password', api_password)])
        funding_addresses = list(response[0].address_balance_info)
        print('Response contains {0} funding addresses:'.format(len(funding_addresses)))
        for address_balance_info in funding_addresses:
            print('Address = {0}  Available Balance = {1} sats  Unused? {2}  Num confirmations of most recent tx = {3}'
                  .format(address_balance_info.address,
                          address_balance_info.balance,
                          address_balance_info.is_address_unused,
                          address_balance_info.num_confirmations))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get all bitcoin receiving addresses in the Bisq BTC wallet.


### gRPC Request: GetFundingAddressesRequest

This Request has no parameters.

### gRPC Response: GetFundingAddressesReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 address_balance_info | [array AddressBalanceInfo](#addressbalanceinfo) | The list of BTC wallet addresses with their balances.

## RPC Method GetTransaction
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 gettransaction \
  --transaction-id=fef206f2ada53e70fd8430d130e43bc3994ce075d50ac1f4fda8182c40ef6bdd

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetTransactionRequest;
import bisq.proto.grpc.WalletsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetTransaction extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = WalletsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetTransactionRequest.newBuilder()
                    .setTxId("fef206f2ada53e70fd8430d130e43bc3994ce075d50ac1f4fda8182c40ef6bdd")
                    .build();
            var response = stub.getTransaction(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.WalletsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetTransaction.with_call(
            bisq_messages.GetTransactionRequest(
                tx_id='907cf2b9ec2970653a9d7b5384b729037bfdf213d3fa38797704a7adb4c7217e'),
            metadata=[('password', api_password)])
        print('Response: ' + str(response[0].tx_info))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get a bitcoin transaction summary.


### gRPC Request: GetTransactionRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 tx_id | string |

### gRPC Response: GetTransactionReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 tx_info | [TxInfo](#txinfo) | The summary of a bitcoin transaction.

## RPC Method GetTxFeeRate
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 gettxfeerate

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetTxFeeRateRequest;
import bisq.proto.grpc.WalletsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetTxFeeRate extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = WalletsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetTxFeeRateRequest.newBuilder().build();
            var response = stub.getTxFeeRate(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.WalletsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetTxFeeRate.with_call(
            bisq_messages.GetTxFeeRateRequest(),
            metadata=[('password', api_password)])
        print('Response: ' + str(response[0].tx_fee_rate_info))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get the Bisq network's most recently available bitcoin miner transaction fee rate, or custom fee rate if set.


### gRPC Request: GetTxFeeRateRequest

This Request has no parameters.

### gRPC Response: GetTxFeeRateReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 tx_fee_rate_info | [TxFeeRateInfo](#txfeerateinfo) | The summary of the most recently available bitcoin transaction fee rates.

## RPC Method GetUnusedBsqAddress
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 getunusedbsqaddress

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.GetUnusedBsqAddressRequest;
import bisq.proto.grpc.WalletsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class GetUnusedBsqAddress extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = WalletsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = GetUnusedBsqAddressRequest.newBuilder().build();
            var response = stub.getUnusedBsqAddress(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.WalletsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.GetUnusedBsqAddress.with_call(
            bisq_messages.GetUnusedBsqAddressRequest(),
            metadata=[('password', api_password)])
        print('Response: ' + str(response[0].address))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Get an unused BSQ wallet address.


### gRPC Request: GetUnusedBsqAddressRequest

This Request has no parameters.

### gRPC Response: GetUnusedBsqAddressReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 address | string | The BSQ wallet's unused address.

## RPC Method LockWallet
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 lockwallet

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.LockWalletRequest;
import bisq.proto.grpc.WalletsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class LockWallet extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = WalletsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = LockWalletRequest.newBuilder().build();
            stub.lockWallet(request);
            out.println("Wallet is locked.");
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.WalletsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.LockWallet.with_call(
            bisq_messages.LockWalletRequest(),
            metadata=[('password', api_password)])
        print('Response: ' + str(response))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Lock an encrypted Bisq wallet before the UnlockWallet rpc method's timeout period has expired.


### gRPC Request: LockWalletRequest

This Request has no parameters.

### gRPC Response: LockWalletReply

This Response has no parameters.

## RPC Method RemoveWalletPassword
```shell
#!/bin/bash
# Note: CLI command option parser expects a --wallet-password option, to differentiate it from an api daemon password.
./bisq-cli --password=xyz --port=9998 removewalletpassword --wallet-password="abc"

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.RemoveWalletPasswordRequest;
import bisq.proto.grpc.WalletsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class RemoveWalletPassword extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = WalletsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = RemoveWalletPasswordRequest.newBuilder().setPassword("abc").build();
            stub.removeWalletPassword(request);
            out.println("Wallet encryption password is removed.");
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.WalletsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.RemoveWalletPassword.with_call(
            bisq_messages.RemoveWalletPasswordRequest(password='abc'),
            metadata=[('password', api_password)])
        print('Response: ' + str(response))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Remove the encryption password from the Bisq wallet.


### gRPC Request: RemoveWalletPasswordRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 password | string | The Bisq wallet's current encryption password.

### gRPC Response: RemoveWalletPasswordReply

This Response has no parameters.

## RPC Method SendBsq
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 sendbsq \
  --address=Bbcrt1q9elrmtxtzpwt25zq2pmeeu6qk8029w404ad0xn \
  --amount=1000.10

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.SendBsqRequest;
import bisq.proto.grpc.WalletsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class SendBsq extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = WalletsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = SendBsqRequest.newBuilder()
                    .setAddress("Bbcrt1q9elrmtxtzpwt25zq2pmeeu6qk8029w404ad0xn")
                    .setAmount("50.50")
                    .setTxFeeRate("50")
                    .build();
            var response = stub.sendBsq(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.WalletsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.SendBsq.with_call(
            bisq_messages.SendBsqRequest(
                address='Bbcrt1q9elrmtxtzpwt25zq2pmeeu6qk8029w404ad0xn',
                amount='10.00',
                tx_fee_rate='10'),
            metadata=[('password', api_password)])
        print('Response: ' + str(response[0].tx_info))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Send an amount of BSQ to an external address.


### gRPC Request: SendBsqRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 address | string | The external BSQ wallet address. 
 amount | string | The amount being sent to the external BSQ wallet address, as a string in "#######,##" format. 
 tx_fee_rate | string | An optional bitcoin miner transaction fee rate, in sats/byte.  If not defined, Bisq will revert<br/>to the custom transaction fee rate preference, if set, else the common Bisq network fee rate.<br/>

### gRPC Response: SendBsqReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 tx_info | [TxInfo](#txinfo) | The summary of a bitcoin transaction.  (BSQ is a colored coin, and transacted on the bitcoin blockchain.)

## RPC Method SendBtc
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 sendbtc \
  --address=bcrt1qqau7ad7lf8xx08mnxl709h6cdv4ma9u3ace5k2 \
  --amount=0.006 \
  --tx-fee-rate=20

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.SendBtcRequest;
import bisq.proto.grpc.WalletsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class SendBtc extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = WalletsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = SendBtcRequest.newBuilder()
                    .setAddress("bcrt1qqau7ad7lf8xx08mnxl709h6cdv4ma9u3ace5k2")
                    .setAmount("0.005")
                    .setTxFeeRate("50")
                    .setMemo("Optional memo.")
                    .build();
            var response = stub.sendBtc(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.WalletsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.SendBtc.with_call(
            bisq_messages.SendBtcRequest(
                address='bcrt1qr3tjm77z3qzkf4kstj9v3yw9ewhjqjefz3c48y',
                amount='0.006',
                tx_fee_rate='15',
                memo='Optional memo saved with transaction in Bisq wallet.'),
            metadata=[('password', api_password)])
        print('Response: ' + str(response[0].tx_info))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Send an amount of BTC to an external address.


### gRPC Request: SendBtcRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 address | string | The external bitcoin address. 
 amount | string | The amount of BTC to send to the external address, as a string in "##.########" (BTC unit) format. 
 tx_fee_rate | string | An optional bitcoin miner transaction fee rate, in sats/byte.  If not defined, Bisq will revert<br/>to the custom transaction fee rate preference, if set, else the common Bisq network fee rate.<br/> 
 memo | string | An optional memo associated with the bitcoin transaction.

### gRPC Response: SendBtcReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 tx_info | [TxInfo](#txinfo) | The summary of a bitcoin transaction.

## RPC Method SetTxFeeRatePreference
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 settxfeerate --tx-fee-rate=50

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.SetTxFeeRatePreferenceRequest;
import bisq.proto.grpc.WalletsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class SetTxFeeRatePreference extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = WalletsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = SetTxFeeRatePreferenceRequest.newBuilder()
                    .setTxFeeRatePreference(25)
                    .build();
            var response = stub.setTxFeeRatePreference(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
# from getpass import getpass
from builtins import print

import grpc

import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.WalletsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.SetTxFeeRatePreference.with_call(
            bisq_messages.SetTxFeeRatePreferenceRequest(tx_fee_rate_preference=20),
            metadata=[('password', api_password)])
        print('Response: ' + str(response[0].tx_fee_rate_info))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Set the Bisq daemon's custom bitcoin miner transaction fee rate, in sats/byte..


### gRPC Request: SetTxFeeRatePreferenceRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 tx_fee_rate_preference | uint64 |

### gRPC Response: SetTxFeeRatePreferenceReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 tx_fee_rate_info | [TxFeeRateInfo](#txfeerateinfo) | The summary of the most recently available bitcoin transaction fee rates.

## RPC Method SetWalletPassword
```shell
#!/bin/bash
# Note: CLI command option parser expects a --wallet-password option, to differentiate it from an api daemon password.
./bisq-cli --password=xyz --port=9998 setwalletpassword --wallet-password="abc"

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.SetWalletPasswordRequest;
import bisq.proto.grpc.WalletsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class SetWalletPassword extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = WalletsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = SetWalletPasswordRequest.newBuilder().setPassword("abc").build();
            stub.setWalletPassword(request);
            out.println("Wallet encryption password is set.");
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.WalletsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.SetWalletPassword.with_call(
            bisq_messages.SetWalletPasswordRequest(password='abc'),
            metadata=[('password', api_password)])
        print('Response: ' + str(response))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Set the Bisq wallet's encryption password.


### gRPC Request: SetWalletPasswordRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 password | string | The new password for encrypting an unencrypted Bisq wallet. 
 new_password | string | The new password for encrypting an already encrypted Bisq wallet (a password override).

### gRPC Response: SetWalletPasswordReply

This Response has no parameters.

## RPC Method UnlockWallet
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 unlockwallet --wallet-password=abc --timeout=30

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.UnlockWalletRequest;
import bisq.proto.grpc.WalletsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class UnlockWallet extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = WalletsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = UnlockWalletRequest.newBuilder()
                    .setPassword("abc")
                    .setTimeout(120)
                    .build();
            stub.unlockWallet(request);
            out.println("Wallet is unlocked.");
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.WalletsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.UnlockWallet.with_call(
            bisq_messages.UnlockWalletRequest(
                password='abc',
                timeout=30),
            metadata=[('password', api_password)])
        print('Response: ' + str(response))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Unlock a Bisq encrypted wallet before calling wallet sensitive rpc methods: CreateOffer, TakeOffer, GetBalances,
etc., for a timeout period in seconds.  An unlocked wallet will automatically lock itself after the timeout
period has expired, or a LockWallet request has been made, whichever is first.  An unlocked wallet's timeout
setting can be overridden by subsequent UnlockWallet calls.


### gRPC Request: UnlockWalletRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 password | string | The Bisq wallet's encryption password. 
 timeout | uint64 | The Bisq wallet's unlock time period, in seconds.

### gRPC Response: UnlockWalletReply

This Response has no parameters.

## RPC Method UnsetTxFeeRatePreference
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 unsettxfeerate

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.UnsetTxFeeRatePreferenceRequest;
import bisq.proto.grpc.WalletsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class UnsetTxFeeRatePreference extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = WalletsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = UnsetTxFeeRatePreferenceRequest.newBuilder().build();
            var response = stub.unsetTxFeeRatePreference(request);
            out.println(response);
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.WalletsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.UnsetTxFeeRatePreference.with_call(
            bisq_messages.UnsetTxFeeRatePreferenceRequest(),
            metadata=[('password', api_password)])
        print('Response: ' + str(response[0].tx_fee_rate_info))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Remove the custom bitcoin miner transaction fee rate;  revert to the Bisq network's bitcoin miner transaction fee rate.


### gRPC Request: UnsetTxFeeRatePreferenceRequest

This Request has no parameters.

### gRPC Response: UnsetTxFeeRatePreferenceReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 tx_fee_rate_info | [TxFeeRateInfo](#txfeerateinfo) | The summary of the most recently available bitcoin transaction fee rates.

## RPC Method VerifyBsqSentToAddress
```shell
#!/bin/bash
./bisq-cli --password=xyz --port=9998 verifybsqsenttoaddress \
  --address=Bbcrt1q9elrmtxtzpwt25zq2pmeeu6qk8029w404ad0xn \
  --amount="50.50"

```

```java
package bisq.rpccalls;

import bisq.proto.grpc.VerifyBsqSentToAddressRequest;
import bisq.proto.grpc.WalletsGrpc;
import io.grpc.ManagedChannelBuilder;

import static java.lang.System.out;

public class VerifyBsqSentToAddress extends BaseJavaExample {

    public static void main(String[] args) {
        try {
            var channel = ManagedChannelBuilder.forAddress("localhost", 9998).usePlaintext().build();
            addChannelShutdownHook(channel);
            var credentials = buildCallCredentials(getApiPassword());
            var stub = WalletsGrpc.newBlockingStub(channel).withCallCredentials(credentials);
            var request = VerifyBsqSentToAddressRequest.newBuilder()
                    .setAddress("Bbcrt1q9elrmtxtzpwt25zq2pmeeu6qk8029w404ad0xn")
                    .setAmount("50.50")
                    .build();
            var response = stub.verifyBsqSentToAddress(request);
            out.println("Address did receive amount of BSQ: " + response.getIsAmountReceived());
        } catch (Throwable t) {
            handleError(t);
        }
    }
}

//////////////////
// BaseJavaExample
//////////////////

package bisq.rpccalls;

import io.grpc.CallCredentials;
import io.grpc.ManagedChannel;
import io.grpc.Metadata;
import io.grpc.StatusRuntimeException;

import java.util.Scanner;
import java.util.concurrent.Executor;

import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;
import static io.grpc.Status.UNAUTHENTICATED;
import static java.lang.System.*;
import static java.util.concurrent.TimeUnit.SECONDS;

public class BaseJavaExample {

    static void addChannelShutdownHook(ManagedChannel channel) {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                channel.shutdown().awaitTermination(1, SECONDS);
            } catch (InterruptedException ex) {
                throw new IllegalStateException("Error shutting down gRPC channel.", ex);
            }
        }));
    }

    static String getApiPassword() {
        Scanner scanner = new Scanner(in);
        out.println("Enter api password:");
        var apiPassword = "xyz";    // scanner.nextLine();
        scanner.close();
        return apiPassword;
    }

    static CallCredentials buildCallCredentials(String apiPassword) {
        return new CallCredentials() {
            @Override
            public void applyRequestMetadata(RequestInfo requestInfo,
                                             Executor appExecutor,
                                             MetadataApplier metadataApplier) {
                appExecutor.execute(() -> {
                    try {
                        var headers = new Metadata();
                        var passwordKey = Metadata.Key.of("password", ASCII_STRING_MARSHALLER);
                        headers.put(passwordKey, apiPassword);
                        metadataApplier.apply(headers);
                    } catch (Throwable ex) {
                        metadataApplier.fail(UNAUTHENTICATED.withCause(ex));
                    }
                });
            }

            @Override
            public void thisUsesUnstableApi() {
            }
        };
    }

    static void handleError(Throwable t) {
        if (t instanceof StatusRuntimeException) {
            var grpcErrorStatus = ((StatusRuntimeException) t).getStatus();
            err.println(grpcErrorStatus.getCode() + ": " + grpcErrorStatus.getDescription());
        } else {
            err.println("Error: " + t);
        }
    }
}

```
```python
from builtins import print

import grpc

# from getpass import getpass
import bisq.api.grpc_pb2 as bisq_messages
import bisq.api.grpc_pb2_grpc as bisq_service


def main():
    grpc_channel = grpc.insecure_channel('localhost:9998')
    grpc_service_stub = bisq_service.WalletsStub(grpc_channel)
    api_password: str = 'xyz'  # getpass("Enter API password: ")
    try:
        response = grpc_service_stub.VerifyBsqSentToAddress.with_call(
            bisq_messages.VerifyBsqSentToAddressRequest(
                address='Bbcrt1q9elrmtxtzpwt25zq2pmeeu6qk8029w404ad0xn',
                amount='10.00'),
            metadata=[('password', api_password)])
        print('BSQ amount was received at address: ' + str(response[0].is_amount_received))
    except grpc.RpcError as rpc_error:
        print('gRPC API Exception: %s', rpc_error)


if __name__ == '__main__':
    main()
```
### Unary RPC
Verify a specific amount of BSQ was received by a BSQ wallet address.
This is a problematic way of verifying BSQ payment has been received for a v1 trade protocol BSQ-BTC trade,
which has been solved by the introduction of BSQ swap trades, which use a different, unused BSQ address for each trade.


### gRPC Request: VerifyBsqSentToAddressRequest

Name | Type | Description 
 ------------- | ------------- | ------------- 
 address | string | The internal BSQ wallet address. 
 amount | string | The amount supposedly sent to the BSQ wallet address, as a string in "#######,##" format.

### gRPC Response: VerifyBsqSentToAddressReply

Name | Type | Description 
 ------------- | ------------- | ------------- 
 is_amount_received | bool | Whether a specific BSQ wallet address has received a specific amount of BSQ.  If the same address has received<br/>the same amount of BSQ more than once, a true value does not indicate payment has been made for a v1 protocol<br/>BSQ-BTC trade.  This BSQ payment verification problem is solved with BSQ swaps, which use a different BSQ<br/>address for each swap transaction.<br/>

# gRPC Messages
## AchTransferAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 holder_address | string |

## AddressBalanceInfo

Name | Type | Description 
 ------------- | ------------- | ------------- 
 address | string | The bitcoin wallet address. 
 balance | int64 | The address' BTC balance in satoshis. 
 num_confirmations | int64 | The number of confirmations for the most recent transaction referencing the output address. 
 is_address_unused | bool | Whether the bitcoin address has ever been used, or not.

## AdvancedCashAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 account_nr | string |

## AliPayAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 account_nr | string |

## AmazonGiftCardAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 email_or_mobile_nr | string |  
 country_code | string |

## AustraliaPayidPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 bank_account_name | string |  
 payid | string |

## AvailabilityResultWithDescription
An offer's current availability status.

Name | Type | Description 
 ------------- | ------------- | ------------- 
 availability_result | [AvailabilityResult](#availabilityresult) | An offer's current status as an eum. 
 description | string | A user friendly description of an offer's current availability status.

## BalancesInfo

Name | Type | Description 
 ------------- | ------------- | ------------- 
 bsq | [BsqBalanceInfo](#bsqbalanceinfo) | BSQ wallet balance information. 
 btc | [BtcBalanceInfo](#btcbalanceinfo) | BTC wallet balance information.

## BankAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 holder_name | string |  
 bank_name | string |  
 bank_id | string |  
 branch_id | string |  
 account_nr | string |  
 account_type | string |  
 holder_tax_id | string |  
 one of { | &nbsp; | Field value will be one of the following. 
 &nbsp;&nbsp;&nbsp;&nbsp;ach_transfer_account_payload | [AchTransferAccountPayload](#achtransferaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;domestic_wire_transfer_account_payload | [DomesticWireTransferAccountPayload](#domesticwiretransferaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;national_bank_account_payload | [NationalBankAccountPayload](#nationalbankaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;same_bank_accont_payload | [SameBankAccountPayload](#samebankaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;specific_banks_account_payload | [SpecificBanksAccountPayload](#specificbanksaccountpayload) |  
 } | &nbsp; | &nbsp; 
 national_account_id | string |

## BizumAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 mobile_nr | string |

## BsqBalanceInfo
TODO Thoroughly review field descriptions.

Name | Type | Description 
 ------------- | ------------- | ------------- 
 available_confirmed_balance | uint64 | The BSQ amount currently available to send to other addresses at the user's discretion, in satoshis. 
 unverified_balance | uint64 | The BSQ amount currently being used in send transactions, in satoshis. Unverified BSQ balances are<br/>not spendable until returned to the available_confirmed_balance when send transactions have been confirmed.<br/> 
 unconfirmed_change_balance | uint64 | The BSQ transaction change amount tied up in unconfirmed transactions, remaining unspendable until transactions<br/>have been confirmed and the change returned to the available_confirmed_balance.<br/> 
 locked_for_voting_balance | uint64 | The locked BSQ amount held by DAO voting transaction. 
 lockup_bonds_balance | uint64 | The locked BSQ amount held by DAO bonding transaction. 
 unlocking_bonds_balance | uint64 | The BSQ bonding amount in unlocking state, awaiting a lockup transaction's lock time expiry before the funds<br/>can be spent in normal transactions.<br/>

## BsqSwapAccountPayload

## BsqSwapTradeInfo
BSQ Swap protocol specific fields not common to Bisq v1 trade protocol fields.

Name | Type | Description 
 ------------- | ------------- | ------------- 
 tx_id | string | The BSQ swap's bitcoin transaction id. 
 bsq_trade_amount | uint64 | The amount of BSQ swapped in satoshis. 
 btc_trade_amount | uint64 | The amount of BTC swapped in satoshis. 
 bsq_maker_trade_fee | uint64 | The swap offer maker's BSQ trade fee. 
 bsq_taker_trade_fee | uint64 | The swap offer taker's BSQ trade fee. 
 tx_fee_per_vbyte | uint64 | The swap transaction's bitcoin transaction id. 
 maker_bsq_address | string | The swap offer maker's BSQ wallet address. 
 maker_btc_address | string | The swap offer maker's BTC wallet address. 
 taker_bsq_address | string | The swap offer taker's BSQ wallet address. 
 taker_btc_address | string | The swap offer taker's BTC wallet address. 
 num_confirmations | uint64 | The confirmations count for the completed swap's bitcoin transaction. 
 error_message | string | An explanation for a failure to complete the swap. 
 payout | uint64 | The amount of the user's trade payout in satoshis. 
 swap_peer_payout | uint64 | The amount of the peer's trade payout in satoshis.

## BtcBalanceInfo
TODO Thoroughly review field descriptions.

Name | Type | Description 
 ------------- | ------------- | ------------- 
 available_balance | uint64 | The BTC amount currently available to send to other addresses at the user's discretion, in satoshis. 
 reserved_balance | uint64 | The BTC amount currently reserved to cover open offers' security deposits, and BTC sellers' payout amounts,<br/>in satoshis.  Reserved funds are not spendable, but are recoverable by users. When a user cancels an offer<br/>funds reserved for that offer are returned to the available_balance.<br/> 
 total_available_balance | uint64 | The sum of available_balance + reserved_balance, in satoshis. 
 locked_balance | uint64 | The BTC amount being locked to cover the security deposits and BTC seller's pending trade payouts.  Locked<br/>funds are not recoverable until a trade is completed, when security deposits are returned to the available_balance.<br/>

## CapitualAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 account_nr | string |

## CashAppAccountPayload
Deprecated, not used.

Name | Type | Description 
 ------------- | ------------- | ------------- 
 cash_tag | string |

## CashByMailAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 postal_address | string |  
 contact | string |  
 extra_info | string |

## CashDepositAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 holder_name | string |  
 holder_email | string |  
 bank_name | string |  
 bank_id | string |  
 branch_id | string |  
 account_nr | string |  
 account_type | string |  
 requirements | string |  
 holder_tax_id | string |  
 national_account_id | string |

## CelPayAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 email | string |

## ChaseQuickPayAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 email | string |  
 holder_name | string |

## ClearXchangeAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 holder_name | string |  
 email_or_mobile_nr | string |

## ContractInfo

Name | Type | Description 
 ------------- | ------------- | ------------- 
 buyer_node_address | string | The BTC buyer peer's node address. 
 seller_node_address | string | The BTC seller peer's node address. 
 mediator_node_address | string | If the trade was disputed, the Bisq mediator's node address. 
 refund_agent_node_address | string | If a trade refund was requested, the Bisq refund agent's node address. 
 is_buyer_maker_and_seller_taker | bool | Whether the BTC buyer created the original offer, or not. 
 maker_account_id | string | The offer maker's payment account id. 
 taker_account_id | string | The offer taker's payment account id. 
 maker_payment_account_payload | [PaymentAccountPayloadInfo](#paymentaccountpayloadinfo) | A summary of the offer maker's payment account. 
 taker_payment_account_payload | [PaymentAccountPayloadInfo](#paymentaccountpayloadinfo) | A summary of the offer taker's payment account. 
 maker_payout_address_string | string | The offer maker's BTC payout address. 
 taker_payout_address_string | string | The offer taker's BTC payout address. 
 lock_time | uint64 | The earliest time a transaction can be added to the block chain.

## CountryBasedPaymentAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 countryCode | string |  
 one of { | &nbsp; | Field value will be one of the following. 
 &nbsp;&nbsp;&nbsp;&nbsp;bank_account_payload | [BankAccountPayload](#bankaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;bizum_account_payload | [BizumAccountPayload](#bizumaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;cash_deposit_account_payload | [CashDepositAccountPayload](#cashdepositaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;f2f_account_payload | [F2FAccountPayload](#f2faccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;ifsc_based_account_payload | [IfscBasedAccountPayload](#ifscbasedaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;nequi_account_payload | [NequiAccountPayload](#nequiaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;paytm_account_payload | [PaytmAccountPayload](#paytmaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;pix_account_payload | [PixAccountPayload](#pixaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;satispay_account_payload | [SatispayAccountPayload](#satispayaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;sepa_account_payload | [SepaAccountPayload](#sepaaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;sepa_instant_account_payload | [SepaInstantAccountPayload](#sepainstantaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;strike_account_payload | [StrikeAccountPayload](#strikeaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;tikkie_account_payload | [TikkieAccountPayload](#tikkieaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;transferwise_usd_account_payload | [TransferwiseUsdAccountPayload](#transferwiseusdaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;upi_account_payload | [UpiAccountPayload](#upiaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;western_union_account_payload | [WesternUnionAccountPayload](#westernunionaccountpayload) |  
 } | &nbsp; | &nbsp;

## CryptoCurrency

Name | Type | Description 
 ------------- | ------------- | ------------- 
 is_asset | bool |

## CryptoCurrencyAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 address | string |

## Currency

Name | Type | Description 
 ------------- | ------------- | ------------- 
 currency_code | string |

## DomesticWireTransferAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 holder_address | string |

## F2FAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 contact | string |  
 city | string |  
 extra_info | string |

## FasterPaymentsAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 sort_code | string |  
 account_nr | string |

## FiatCurrency

Name | Type | Description 
 ------------- | ------------- | ------------- 
 currency | [Currency](#currency) |

## HalCashAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 mobile_nr | string |

## IfscBasedAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 holder_name | string |  
 account_nr | string |  
 ifsc | string |  
 one of { | &nbsp; | Field value will be one of the following. 
 &nbsp;&nbsp;&nbsp;&nbsp;imps_account_payload | [ImpsAccountPayload](#impsaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;neft_account_payload | [NeftAccountPayload](#neftaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;rtgs_account_payload | [RtgsAccountPayload](#rtgsaccountpayload) |  
 } | &nbsp; | &nbsp;

## ImpsAccountPayload

## InstantCryptoCurrencyAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 address | string |

## InteracETransferAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 email | string |  
 holder_name | string |  
 question | string |  
 answer | string |

## JapanBankAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 bank_name | string |  
 bank_code | string |  
 bank_branch_name | string |  
 bank_branch_code | string |  
 bank_account_type | string |  
 bank_account_name | string |  
 bank_account_number | string |

## MoneseAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 mobile_nr | string |  
 holder_name | string |

## MoneyBeamAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 account_id | string |

## MoneyGramAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 holder_name | string |  
 country_code | string |  
 state | string |  
 email | string |

## NationalBankAccountPayload

## NeftAccountPayload

## NequiAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 mobile_nr | string |

## OKPayAccountPayload
Deprecated, not used.

Name | Type | Description 
 ------------- | ------------- | ------------- 
 account_nr | string |

## OfferInfo
OfferInfo describes an offer to a client.  It is derived from the heavier
Offer object in the daemon, which holds too much state to be sent to clients.

Name | Type | Description 
 ------------- | ------------- | ------------- 
 id | string | The offer's unique identifier. 
 direction | string | The offer's BUY (BTC) or SELL (BTC) direction. 
 price | string | For fiat offers:  the fiat price for 1 BTC to 4 decimal places, e.g., 45000 EUR is "45000.0000".<br/>For altcoin offers:  the altcoin price for 1 BTC to 8 decimal places, e.g., 0.00005 BTC is "0.00005000".<br/> 
 use_market_based_price | bool | Whether the offer price is fixed, or market price margin based. 
 market_price_margin_pct | double | The offer's market price margin above or below the current market BTC price, e.g., 5.00 represents 5%. 
 amount | uint64 | The offer's BTC amount in satoshis.  Ten million satoshis is represented as 10000000. 
 min_amount | uint64 | The offer's minimum BTC amount in satoshis.  One million satoshis is represented as 1000000. 
 volume | string | The rounded volume of currency to be traded for BTC.<br/>Fiat volume is rounded to whole currency units (no cents).  Altcoin volume is rounded to 2 decimal places.<br/> 
 min_volume | string | The rounded, minimum volume of currency to be traded for BTC.<br/>Fiat volume is rounded to whole currency units (no cents).  Altcoin volume is rounded to 2 decimal places.<br/> 
 buyer_security_deposit | uint64 | A long representing the BTC buyer's security deposit in satoshis. 
 trigger_price | string | A market price margin based offer's trigger price is the market BTC price at which the offer is automatically disabled.<br/>Disabled offers are never automatically enabled, they must be manually re-enabled.<br/>A zero value indicates trigger price is not set.  Trigger price does not apply to fixed price offers.<br/> 
 is_currency_for_maker_fee_btc | bool | Whether the offer maker paid the trading fee in BTC or not (BSQ). 
 payment_account_id | string | The unique identifier of the payment account used to create the offer. 
 payment_method_id | string | The unique identifier of the payment method used to create the offer. 
 payment_method_short_name | string | The short description of the payment method used to create the offer. 
 base_currency_code | string | For fiat offers, the baseCurrencyCode is BTC, and the counter_currency_code is the fiat currency code.<br/>For altcoin offers it is the opposite, the baseCurrencyCode is the altcoin code and the counter_currency_code is BTC.<br/> 
 counter_currency_code | string | For fiat offers, the base_currency_code is BTC, and the counter_currency_code is the fiat currency code.<br/>For altcoin offers it is the opposite, the base_currency_code is the altcoin code and the counter_currency_code is BTC.<br/> 
 date | uint64 | The creation date of the offer as a long: the number of milliseconds that have elapsed since January 1, 1970. 
 state | string | The internal state of the offer, e.g., AVAILABLE, NOT_AVAILABLE, REMOVED, etc. 
 seller_security_deposit | uint64 | A long representing the BTC seller's security deposit in satoshis. 
 offer_fee_payment_tx_id | string | The bitcoin transaction id of the offer maker's fee payment. 
 tx_fee | uint64 | The bitcoin transaction fee (amount) for the offer maker's fee payment transaction, in satoshis. 
 maker_fee | uint64 | The offer maker's Bisq trade fee amount in satoshis. 
 is_activated | bool | Whether the offer is currently enabled or not. 
 is_my_offer | bool | Whether the offer was created by the user or not. 
 is_my_pending_offer | bool | Whether the newly created offer was created by the user or not. 
 is_bsq_swap_offer | bool | Whether the offer is a BSQ swap offer or not (v1 protocol offer). 
 owner_node_address | string | The offer creator's Tor onion address. 
 pub_key_ring | string | The offer creator's public key ring as a string. 
 version_nr | string | The Bisq software version used to create the offer. 
 protocol_version | int32 | The bitcoin protocol version used to create the offer.

## PaxumAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 email | string |

## PaymentAccount

Name | Type | Description 
 ------------- | ------------- | ------------- 
 id | string |  
 creation_date | int64 |  
 payment_method | [PaymentMethod](#paymentmethod) |  
 account_name | string |  
 trade_currencies | [array TradeCurrency](#tradecurrency) |  
 selected_trade_currency | [TradeCurrency](#tradecurrency) |  
 payment_account_payload | [PaymentAccountPayload](#paymentaccountpayload) |

## PaymentAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 id | string |  
 payment_method_id | string |  
 one of { | &nbsp; | Field value will be one of the following. 
 &nbsp;&nbsp;&nbsp;&nbsp;advanced_cash_account_payload | [AdvancedCashAccountPayload](#advancedcashaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;ali_pay_account_payload | [AliPayAccountPayload](#alipayaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;amazon_gift_card_account_payload | [AmazonGiftCardAccountPayload](#amazongiftcardaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;australia_payid_payload | [AustraliaPayidPayload](#australiapayidpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;bsq_swap_account_payload | [BsqSwapAccountPayload](#bsqswapaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;capitual_account_payload | [CapitualAccountPayload](#capitualaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;cash_app_account_payload | [CashAppAccountPayload](#cashappaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;cash_by_mail_account_payload | [CashByMailAccountPayload](#cashbymailaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;cel_pay_account_payload | [CelPayAccountPayload](#celpayaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;chase_quick_pay_account_payload | [ChaseQuickPayAccountPayload](#chasequickpayaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;clear_xchange_account_payload | [ClearXchangeAccountPayload](#clearxchangeaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;country_based_payment_account_payload | [CountryBasedPaymentAccountPayload](#countrybasedpaymentaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;crypto_currency_account_payload | [CryptoCurrencyAccountPayload](#cryptocurrencyaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;faster_payments_account_payload | [FasterPaymentsAccountPayload](#fasterpaymentsaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;hal_cash_account_payload | [HalCashAccountPayload](#halcashaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;instant_crypto_currency_account_payload | [InstantCryptoCurrencyAccountPayload](#instantcryptocurrencyaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;interac_e_transfer_account_payload | [InteracETransferAccountPayload](#interacetransferaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;japan_bank_account_payload | [JapanBankAccountPayload](#japanbankaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;monese_account_payload | [MoneseAccountPayload](#moneseaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;money_beam_account_payload | [MoneyBeamAccountPayload](#moneybeamaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;money_gram_account_payload | [MoneyGramAccountPayload](#moneygramaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;o_k_pay_account_payload | [OKPayAccountPayload](#okpayaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;Paxum_account_payload | [PaxumAccountPayload](#paxumaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;Paysera_account_payload | [PayseraAccountPayload](#payseraaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;perfect_money_account_payload | [PerfectMoneyAccountPayload](#perfectmoneyaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;popmoney_account_payload | [PopmoneyAccountPayload](#popmoneyaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;prompt_pay_account_payload | [PromptPayAccountPayload](#promptpayaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;revolut_account_payload | [RevolutAccountPayload](#revolutaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;swift_account_payload | [SwiftAccountPayload](#swiftaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;swish_account_payload | [SwishAccountPayload](#swishaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;Transferwise_account_payload | [TransferwiseAccountPayload](#transferwiseaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;u_s_postal_money_order_account_payload | [USPostalMoneyOrderAccountPayload](#uspostalmoneyorderaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;uphold_account_payload | [UpholdAccountPayload](#upholdaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;venmo_account_payload | [VenmoAccountPayload](#venmoaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;verse_account_payload | [VerseAccountPayload](#verseaccountpayload) |  
 &nbsp;&nbsp;&nbsp;&nbsp;we_chat_pay_account_payload | [WeChatPayAccountPayload](#wechatpayaccountpayload) |  
 } | &nbsp; | &nbsp; 
 exclude_from_json_data | map&#60;string, string&#62; | todo

## PaymentAccountPayloadInfo

Name | Type | Description 
 ------------- | ------------- | ------------- 
 id | string | The unique identifier of the payment account. 
 payment_method_id | string | The unique identifier of the payment method. 
 address | string | The optional altcoin wallet address associated with the (altcoin) payment account. 
 payment_details | string | The optional payment details, if available.

## PaymentMethod

Name | Type | Description 
 ------------- | ------------- | ------------- 
 id | string |  
 max_trade_period | int64 |  
 max_trade_limit | int64 |

## PayseraAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 email | string |

## PaytmAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 email_or_mobile_nr | string |

## PerfectMoneyAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 account_nr | string |

## PixAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 pix_key | string |

## PopmoneyAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 account_id | string |  
 holder_name | string |

## PromptPayAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 prompt_pay_id | string |

## RevolutAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 account_id | string |  
 user_name | string |

## RtgsAccountPayload

## SameBankAccountPayload

## SatispayAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 mobile_nr | string |  
 holder_name | string |

## SepaAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 holder_name | string |  
 iban | string |  
 bic | string |  
 accepted_country_codes | array string |

## SepaInstantAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 holder_name | string |  
 iban | string |  
 bic | string |  
 accepted_country_codes | array string |

## SpecificBanksAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 accepted_banks | array string |

## StrikeAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 holder_name | string |

## SwiftAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 beneficiary_name | string |  
 beneficiary_account_nr | string |  
 beneficiary_address | string |  
 beneficiary_city | string |  
 beneficiary_phone | string |  
 special_instructions | string |  
 bank_swift_code | string |  
 bank_country_code | string |  
 bank_name | string |  
 bank_branch | string |  
 bank_address | string |  
 intermediary_swift_code | string |  
 intermediary_country_code | string |  
 intermediary_name | string |  
 intermediary_branch | string |  
 intermediary_address | string |

## SwishAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 mobile_nr | string |  
 holder_name | string |

## TikkieAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 iban | string |

## TradeCurrency

Name | Type | Description 
 ------------- | ------------- | ------------- 
 code | string |  
 name | string |  
 one of { | &nbsp; | Field value will be one of the following. 
 &nbsp;&nbsp;&nbsp;&nbsp;crypto_currency | [CryptoCurrency](#cryptocurrency) |  
 &nbsp;&nbsp;&nbsp;&nbsp;fiat_currency | [FiatCurrency](#fiatcurrency) |  
 } | &nbsp; | &nbsp;

## TradeInfo
TODO Modify bisq-grpc-api-doc to include core Trade enums in API Reference.

Name | Type | Description 
 ------------- | ------------- | ------------- 
 offer | [OfferInfo](#offerinfo) | The original offer. 
 trade_id | string | The unique identifier of the trade. 
 short_id | string | An abbreviation of unique identifier of the trade.  It cannot be used as parameter to rpc methods GetTrade,<br/>ConfirmPaymentStarted, CloseTrade, etc., but it may be useful while interacting with support or trading peers.<br/> 
 date | uint64 | The creation date of the trade as a long: the number of milliseconds that have elapsed since January 1, 1970. 
 role | string | A brief description of the user's role in the trade, i.e., an offer maker or taker, a BTC buyer or seller. 
 is_currency_for_taker_fee_btc | bool | Whether the offer taker's Bisq trade fee was paid in BTC or not (BSQ). 
 tx_fee_as_long | uint64 | The bitcoin miner transaction fee in satoshis. 
 taker_fee_as_long | uint64 | The offer taker's Bisq trade fee in satoshis. 
 taker_fee_tx_id | string | The bitcoin transaction id for offer taker's Bisq trade fee. 
 deposit_tx_id | string | The bitcoin transaction id for the offer taker's security deposit. 
 payout_tx_id | string | The bitcoin transaction id for trade payout. 
 trade_amount_as_long | uint64 | The trade payout amount in satoshis. 
 trade_price | string | For fiat trades:  the fiat price for 1 BTC to 4 decimal places, e.g., 41000.50 EUR is "41000.5000".<br/>For altcoin trades:  the altcoin price for 1 BTC to 8 decimal places, e.g., 0.5 BTC is "0.50000000".<br/> 
 trading_peer_node_address | string | The trading peer's node address. 
 state | string | The internal state of the trade. (TODO bisq-grpc-api-doc -> include Trade.State in API Reference.) 
 phase | string | The internal phase of the trade. (TODO bisq-grpc-api-doc -> include Trade.Phase in API Reference.) 
 trade_period_state | string | How much of the trade protocol's time limit has elapsed. (TODO bisq-grpc-api-doc -> include Trade.TradePeriodState in API Reference.) 
 is_deposit_published | bool | Whether the trade's security deposit bitcoin transaction has been broadcast, or not. 
 is_deposit_confirmed | bool | Whether the trade's security deposit bitcoin transaction has been confirmed at least once, or not. 
 is_payment_started_message_sent | bool | Whether the trade's 'start payment' message has been sent by the BTC buyer, or not. 
 is_payment_received_message_sent | bool | Whether the trade's 'payment received' message has been sent by the BTC seller, or not. 
 is_payout_published | bool | Whether the trade's payout bitcoin transaction has been confirmed at least once, or not. 
 is_completed | bool | Whether the trade's payout has been completed and the trade is now closed, or not. 
 contract_as_json | string | The entire trade contract as a json string. 
 contract | [ContractInfo](#contractinfo) | The summary of the trade contract. 
 trade_volume | string | The volume of currency traded for BTC. 
 bsq_swap_trade_info | [BsqSwapTradeInfo](#bsqswaptradeinfo) | The details specific to the BSQ swap trade.  If the trade is not a BSQ swap, this field should be ignored. 
 closing_status | string | Needed by open/closed/failed trade list items.

## TransferwiseAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 email | string |

## TransferwiseUsdAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 email | string |  
 holder_name | string |  
 beneficiary_address | string |

## TxFeeRateInfo

Name | Type | Description 
 ------------- | ------------- | ------------- 
 use_custom_tx_fee_rate | bool | Whether the daemon's custom btc transaction fee rate preference is set, or not. 
 custom_tx_fee_rate | uint64 | The daemon's custom btc transaction fee rate preference, in sats/byte. 
 fee_service_rate | uint64 | The Bisq network's most recently available btc transaction fee rate, in sats/byte. 
 last_fee_service_request_ts | uint64 | The date of the most recent Bisq network fee rate request as a long: the number of milliseconds that have elapsed since January 1, 1970. 
 min_fee_service_rate | uint64 | The Bisq network's minimum btc transaction fee rate, in sats/byte.

## TxInfo

Name | Type | Description 
 ------------- | ------------- | ------------- 
 tx_id | string | The bitcoin transaction id. 
 input_sum | uint64 | The sum of the bitcoin transaction's input values in satoshis. 
 output_sum | uint64 | The sum of the bitcoin transaction's output values in satoshis. 
 fee | uint64 | The bitcoin transaction's miner fee in satoshis. 
 size | int32 | The bitcoin transaction's size in bytes. 
 is_pending | bool | Whether the bitcoin transaction has been confirmed at least one time, or not. 
 memo | string | An optional memo associated with the bitcoin transaction.

## USPostalMoneyOrderAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 postal_address | string |  
 holder_name | string |

## UpholdAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 account_id | string |  
 account_owner | string |

## UpiAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 virtual_payment_address | string |

## VenmoAccountPayload
Deprecated, not used.

Name | Type | Description 
 ------------- | ------------- | ------------- 
 venmo_user_name | string |  
 holder_name | string |

## VerseAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 holder_name | string |

## WeChatPayAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 account_nr | string |

## WesternUnionAccountPayload

Name | Type | Description 
 ------------- | ------------- | ------------- 
 holder_name | string |  
 city | string |  
 state | string |  
 email | string |

# gRPC Enums
## AvailabilityResult


 Constant | Value | Description 
 ------------- | ------------- | ------------- 
 PB_ERROR | 0 |  
 UNKNOWN_FAILURE | 1 |  
 AVAILABLE | 2 |  
 OFFER_TAKEN | 3 |  
 PRICE_OUT_OF_TOLERANCE | 4 |  
 MARKET_PRICE_NOT_AVAILABLE | 5 |  
 NO_ARBITRATORS | 6 |  
 NO_MEDIATORS | 7 |  
 USER_IGNORED | 8 |  
 MISSING_MANDATORY_CAPABILITY | 9 |  
 NO_REFUND_AGENTS | 10 |  
 UNCONF_TX_LIMIT_HIT | 11 |  
 MAKER_DENIED_API_USER | 12 |  
 PRICE_CHECK_FAILED | 13 |

# Errors

Errors sent from the Java-based gRPC daemon to gRPC clients are instances
of [StatusRuntimeException](https://github.com/grpc/grpc-java/blob/master/api/src/main/java/io/grpc/StatusRuntimeException.java)
. For non-Java gRPC clients, the equivelant gRPC error type is sent. Exceptions sent to gRPC clients contain
a [Status.Code](https://github.com/grpc/grpc-java/blob/master/api/src/main/java/io/grpc/Status.java) to aid client-side
error handling. The Bisq API daemon does not use all sixteen of the gRPC status codes, below are the currently used
status codes.

Code | Value | Description
 ------------- |-------| ------------- 
UNKNOWN | 2     | An unexpected error occurred in the daemon, and it was not mapped to a meaningful status code.
INVALID_ARGUMENT | 3     | An invalid parameter value was sent to the daemon.
NOT_FOUND | 5     | A requested entity was not found.
ALREADY_EXISTS | 6     | An attempt to change some value or state in the daemon failed because the value or state already exists.
FAILED_PRECONDITION | 9     | An attempted operation failed because some pre-condition was not met.
UNIMPLEMENTED | 12    | An attempt was made to perform an unsupported operation.
UNAVAILABLE | 14    | Some resource is not available at the time requested.
UNAUTHENTICATED | 16    | The gRPC client did not correctly authenticate to the daemon.

