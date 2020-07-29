#!/usr/bin/env bash

path=$(dirname $0)
echo $path

IFS=$'\n'
for line in $(cat ${path}/nginx.tmpl)
do
    echo "      $line"
done


https://askubuntu.com/questions/267384/using-read-without-losing-the-tab