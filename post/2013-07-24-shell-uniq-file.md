# 一个用shell对文件进行去重的例子

linux下对文件去重通常有两种方法：

1. sort -u file
2. 使用awk

第一种方法直接简单，但会对文件内容排序。

	#!/bin/bash


	function run()
	{

	 a=($(find $1 -name '*.dat'))
	 # echo ${a[*]}
	 for x in ${a[*]}
	 do
	  echo 'current file:' $x
	  fn=$(basename $x)
	  path=$(basename $(dirname $x))
	  newPath=$2'/'$path'/'
	  mkdir -p $newPath
	  newFileName=$newPath$fn
	  touch $newFileName
	  echo '---------- new path:'$newPath
	  awk -v newPath=$newFileName '{ if(!line[$0]++) { print $0 > newPath ; } }' $x
	 done

	}

	run $1 $2



以上方法效果：

	cat /home/path1/test1/test1.dat

	1111
	2222
	2222
	3333
	4444
	2222

	cat /home/path1/test2/test2.dat

	555
	666
	7777
	7777
	8888
	666
	555

运行：` ./uniq_ads.sh /home/path1/ /home/path2/ `

运行结果：

	cat /home/path2/test1/test1.dat
	1111
	2222
	3333
	4444


	cat /home/path2/test2/test2.dat
	555
	666
	7777
	8888

