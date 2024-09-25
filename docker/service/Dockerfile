FROM debian:11-slim
RUN apt-get update && \
    apt-get uprade && \
    useradd -ms /bin/bash myapp && \
    file=path/to/file \
    bucket=your-bucket \
    resource="/${bucket}/${file}" \
    contentType="application/x-compressed-tar" \
    dateValue="`date +'%a, %d %b %Y %H:%M:%S %z'`" \
    stringToSign="GET \ 
    ${contentType} \
    ${dateValue} \
    ${resource}" \ 
    s3Key=xxx \
    s3Secret= xxx \
    signature=`/bin/echo -en "$stringToSign" | openssl sha1 -hmac ${s3Secret} -binary | base64` \
    curl -H "Host: ${bucket}.s3.amazonaws.com" \
    -H "Date: ${dateValue}" \
    -H "Content-Type: ${contentType}" \ 
    -H "Authorization: AWS ${s3Key}:${signature}" \ 
     https://${bucket}.s3.amazonaws.com/${file} && \
     mv ${file} /home/myapp && \
     chown -R myapp:myapp /home/myapp && \
     nohup /home/myapp/${file}

