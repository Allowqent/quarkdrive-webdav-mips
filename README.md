通过 SSH 登录到路由器，执行以下命令创建一个软链接（只需执行一次）：
ln -s /lib/libc.so /lib/ld-musl-mipsel.so.1
使用 SCP 或 WinSCP 工具，将解压得到的 quarkdrive-webdav 二进制文件上传至路由器的 /usr/bin/ 目录。
赋予可执行权限：
chmod +x /usr/bin/quarkdrive-webdav
执行前台测试启动：
/usr/bin/quarkdrive-webdav --quark-cookie "你的夸克超长Cookie" -U admin -W password -p 8080
创建服务管理脚本：（可选）
vi /etc/init.d/quarkdrive
填入以下配置（注意替换 your_quark_cookie 字段的内容，保留双引号）：
#!/bin/sh /etc/rc.common

USE_PROCD=1
START=99
STOP=15

PROG=/usr/bin/quarkdrive-webdav

start_service() {
    procd_open_instance

    # 配置启动命令及参数
    procd_set_param command "$PROG"
    procd_append_param command --quark-cookie "your_quark_cookie"
    procd_append_param command -U "admin"
    procd_append_param command -W "password"
    procd_append_param command -p 8080

    # 启用崩溃自动重启机制
    procd_set_param respawn
    # 将日志接管至系统 logread
    procd_set_param stdout 1
    procd_set_param stderr 1

    procd_close_instance
}
保存退出后，设置权限并启动服务：
chmod +x /etc/init.d/quarkdrive
/etc/init.d/quarkdrive enable
/etc/init.d/quarkdrive start
