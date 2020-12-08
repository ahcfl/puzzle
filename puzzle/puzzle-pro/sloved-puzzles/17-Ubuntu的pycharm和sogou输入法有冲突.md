   先查找软件 sudo dpkg -l sog*
    卸载 sudo dpkg -P sogoupinyin
    在设置中，选择Language Support 中将fcitx修改为IBus
    彻底删除软件 sudo apt-get purge fcitx
    删除之前遗留的一些依赖 sudo apt-get autoremove
