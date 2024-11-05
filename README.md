# win-auth-proxy

A simple Windows Authentication proxy.

This application injects the current session's Kerberos token in the communication between a client software and a corporate proxy.

The client software is unable to perform the Negotiate authentication exchanges and the corporate proxy only accepts authenticated users. Usually, the corporate proxy accepts NTLM and Negotiate as authentication protocols.

## Which is the use case, exactly?

Many package managers and source control managers are not able to perform a Negotiate exchange to authenticate the communication.

This means that npm, git, docker, bower and so on will be unable to pass through a corporate proxy. Some tools, like CNTLM, allow you to pass your NTLM token to the proxy. This is a different protocol, less secure than Negotiate. CNTLM is configured by writing your username/domain/encrypted password to a file.

A patch for CNTLM allows you to use the Negotiate protocol (avoiding the need for a password saved in a file), but I've not found a working binary, and compiling my own leads to CNTLM crashing with segfaults which I have no idea how to fix.

## Building

The following command will build the application.

```
go get github.com/qichaozhao/authentication-proxy
```

## User manual

	:: authentication-proxy proxies a corporate proxy on port 80
	:: this proxy supports Negotiate or NTLM
    > .\authentication-proxy.exe http://a_corporate_proxy:80

    :: authentication-proxy listens on 3128
    > set HTTPS_PROXY=http://127.0.0.1:3128

    :: one of the following ..
    > npm install
    > git clone https://github.com/..
    > go get github.com/..

## Notes

* You must run the application as user having a valid kerberos session ticket (i.e. log in with your corporate identity, then start this software). You may need to refresh your kerberos sessions every now and then by logging out (not just locking your screen) and logging in again.
* Does not reply to mutual authentication request, but it's probably somewhat rare to bump into with web applications.
* 64-bit platforms should still offer 32-bit compatible library/API so the application should compile and work. There's afaik no reason for which the application should be 64-bit.
* The application does not change any headers besides Proxy-Authorization intentionally.
* Works only on Windows (because of the syscalls being called)
* The application acts as a MITM proxy for the purposes of SSL connections, so you will need to disregard SSL certificate warnings (or install the self signed certificate) in order to use this tool. I would be welcome to any PR that allows this to seamlessly proxy SSL requests without certificate issues, but I haven't been able to figure this out myself (SSL is hard!)
