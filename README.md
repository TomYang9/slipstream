# Slipstream

This is a proof of concept for the NAT slipstreaming vulnerability discussed [here](https://samy.pl/slipstream).

## Why another implementation

After spending many hours attempting to get the original code working with no success I was left at a point of not knowing if my router was simply not vulnerable, I had misconfigured the code, the code was broken, or there were other implementation details stopping it from working. Eventually I was shown [another implementation](https://embracethered.com/blog/posts/2020/nat-slipstreaming-simplified/) of the attack that skipped over the web based delivery instead focusing just on exploitation of the ALGs. This code is heavily based on that implementation though provides an end to end client and server to make testing simpler.

## What about web based delivery

At time of writing the major browser vendors (Chromium and Firefox) have since provided mitigations against this through blocking outbound connections to port 5060. It's theoretically possible that this could be bypassed by switching to a different port or attempting to use a different ALG altogether. I'm assuming SIP was chosen due to it's similarity to HTTP however in testing some of the higher end enterprise gear we discovered that due to slight differences (the `/` used in the HTTP path and the HTTP version, rather than SIP/2.0) some networking equipment fails to parse the SIP requests and simply drops them at the router. Given that it's been blocked by browsers and delivery is unreliable no attempt was made to port the newer [webscan](https://samy.pl/webscan) technique for local ip discovery for web based delivery.

## Building

slipstream has no external dependencies and does not depend on CGO. You can build the executable and/or cross compile for other platforms using the go compiler with the following command:

```sh
go build
```

## Usage

slipstream will produce a single executable that is both the server and client. You must first setup the server on a remote host that it outside of your NAT:

```sh
./slipstream -l -lp 5060
```

You can then use slipstream to connect to the host outside of your NAT and let it attempt to connect back to you:

```sh
./slipstream -ip <local ip> -host <remote host> -rp 5060 -lp <local port>
```

## License

[MIT](LICENSE)