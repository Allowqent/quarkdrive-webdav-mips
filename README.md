# QuarkDrive WebDAV For MIPS (MT7621 / OpenWrt)

适用于 `ramips/mt7621` 架构路由器的 QuarkDrive WebDAV 服务端移植版本。

---

## 安装与使用

### 1. 创建软链接（仅需执行一次）

通过 SSH 登录路由器，执行以下命令，创建 musl 动态链接器的兼容软链接：

```bash
ln -s /lib/libc.so /lib/ld-musl-mipsel.so.1
```

---

### 2. 上传二进制文件

使用 `scp` 或 WinSCP 等工具，将解压得到的 `quarkdrive-webdav` 二进制文件上传至路由器的 `/usr/bin/` 目录。

---

### 3. 赋予可执行权限

```bash
chmod +x /usr/bin/quarkdrive-webdav
```

---

### 4. 前台测试启动

运行以下命令，测试是否能正常工作（请替换 `你的夸克超长Cookie` 为真实 Cookie）：

```bash
/usr/bin/quarkdrive-webdav --quark-cookie "你的夸克超长Cookie" -U admin -W password -p 8080
```

- `-U`：WebDAV 用户名（示例为 `admin`）
- `-W`：WebDAV 密码（示例为 `password`）
- `-p`：监听端口（示例为 `8080`）

若前台输出无报错，表示运行正常，可按 `Ctrl+C` 终止。

---

### 5. 创建 OpenWrt 服务管理脚本（可选）

创建启动脚本：

```bash
vi /etc/init.d/quarkdrive
```

将以下内容粘贴到文件中（**务必替换 `your_quark_cookie` 为真实 Cookie，保留双引号**）：

```bash
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
```

保存退出后，设置权限并启用服务：

```bash
chmod +x /etc/init.d/quarkdrive
/etc/init.d/quarkdrive enable
/etc/init.d/quarkdrive start
```

---

## 注意事项

- 本程序仅适用于 **MIPS 1004KEc (32-bit)** 架构，已在 MT7621 平台（如新路由3、小米路由器3G/4、K2P 等）的 OpenWrt 系统上测试通过。
- 若启动失败，请检查是否已安装 `libstdcpp6` 等基础库，或尝试静态编译版本。
- 服务运行后，可通过 `logread -f` 实时查看程序输出日志。

---

## 版本信息

- **版本**：v1.0.0-mips  
- **编译环境**：OpenWrt SDK (musl-libc, 硬浮点)
- **发布日期**：2026-08-22
