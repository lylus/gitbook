##内网穿透自用脚本

## Frps服务端


frps.sh
```

#!/bin/bash

# 先运行 frps

/usr/local/frps/frps -c /usr/local/frps/frps.ini &

# 获取frps最新版本号

	api_url="https://api.github.com/repos/fatedier/frp/releases/latest"

	new_ver=`curl ${PROXY} -s ${api_url} --connect-timeout 10| grep 'tag_name' | cut -d\" -f4`

	touch ./version.txt
	cat <<EOF > ./version.txt
${new_ver}
EOF

	sed -i 's/v//g' ./version.txt
	get_releases=$(cat ./version.txt)

	releases_url=https://github.com/fatedier/frp/releases/download/${new_ver}/frp_${get_releases}_linux_amd64.tar.gz
	rm -rf ./version.txt


# 检测frps版本


        old_ver=`/usr/local/frps/frps -v`

#       echo "check old frp version results are: $old_ver"
        echo "检测到本机运行的版本: $old_ver"

#       echo "check new frp version results are: $get_releases"
        echo "检测到最新的版本: $get_releases"

if [ "$old_ver" = "$get_releases" ]

then

echo "没有检测到新的版本!"
#echo "check new frp version"

else
# 安装frps

        rm -rf /usr/local/frps/temp

        mkdir /usr/local/frps/temp

        cd /usr/local/frps/temp

#	wget --no-check-certificate ${releases_url}

	wget -N --no-check-certificate ${releases_url}

	tar -zxvf frp*.tar.gz

	mv /usr/local/frps/temp/frp*/frps /usr/local/frps/frpsbak

	rm -rf /usr/local/frps/temp/frp*
	
	cp -f /usr/local/frps/frpsbak /usr/local/frps/frps
	
        cd /usr/local/frps

	chmod 755 frps

     echo "检测到新版本，已更新完成!"
 #   echo "no no no run frp"


reboot


fi


     echo "frps运行成功！"


```
