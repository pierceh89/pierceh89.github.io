---
layout: post
title: 파일의 라인별로 명령어 실행시키기
summary : 
date    : 2017-08-13
tag     : shell-script
toc     : false
comment : true
public  : false
---

The original content is from [here](https://linuxprograms.wordpress.com/2010/12/14/shell-script-execute-command-every-line-file/)

s3에서 복잡한 파일트리 구조로 흩어져 있는 파일경로의 리스트를 모으고 한번에 다운로드를 받을 수 없을까? 역시 구글링해보니 bash로 스크립트를 짜면 해결할 수 있었다.
내가 만든 파일 리스트에서 라인을 특정 커맨드에 append해야했기 때문에, 원본에서 파일을 조금 수정했다. shell script나 command line tool들은 알면 알수록 유용한 기능이 많고 응용 범위가 방대하다. [The Art of Command Line](https://github.com/jlevy/the-art-of-command-line)에 가면 정리가 잘 되어 있으니 시간이 날 때마다 정리해둬야겠다.

```bash
#/bin/sh

if [ $# -ne 1 ]
then
   echo "Usage: execute_command_per_line filename"
   exit 1
fi

filename=$1

while read line
do
   aws s3 --region ap-northeast-2 cp s3://[s3_bucket_name]/$line ./logs
done < $filename
```
