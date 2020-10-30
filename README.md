# Not the real minio!

This is a slightly modified minio that can simulate download errors

- truncation of objects
- missing objects that are otherwise listed

To switch on the ugly hack search the words "BAREOS-TEST" and set the appropriate flags to "true" then compile and start minio as a server using "minio server data-directory"

Objects in the buckets should have specific names, and should have specific sizes to test the bareos-libcloud-plugin.

Therefore prepare bucket objects similar to this:

```sh
for num in $(seq 1 100); do dd if=/dev/random of=/dev/data_${num} bs=512 count=1; done
for num in $(seq 101 200); do dd if=/dev/random of=/dev/data_${num} bs=512 count=512; done
for num in $(seq 300 301); do dd if=/dev/random of=/dev/data_${num} bs=512 count=512000; done

s3cmd -c s3cfg sync . S3://bareos-test
```

```sh
# File s3cfg
# Setup endpoint
host_base = 127.0.0.1:9000
host_bucket = 127.0.0.1:9000
bucket_location = us-east-1
use_https = False

# Setup access keys
access_key = minioadmin
secret_key = minioadmin

# Enable S3 v4 signature APIs
signature_v2 = False
```

## Install from Source
Source installation is only intended for developers and advanced users. If you do not have a working Golang environment, please follow [How to install Golang](https://golang.org/doc/install). Minimum version required is [go1.15](https://golang.org/dl/#stable)

```sh
GO111MODULE=on go get github.com/minio/minio
```

