# Huawei OBS 挂载与 WebDAV 服务快速指南

以下内容为 ubuntu 24.04上测试通过，其他服务器未测试。

## 1. 安装 rclone
```bash
sudo apt update
sudo apt install -y unzip curl fuse3
curl -fsSL https://rclone.org/install.sh | sudo bash
rclone version
```

---

## 2. 配置 rclone
创建配置文件：
```bash
mkdir -p ~/.config/rclone
nano ~/.config/rclone/rclone.conf
```

粘贴以下内容（替换 `YOUR_AK` 和 `YOUR_SK`）：
```ini
[huawei]
type = s3
provider = Other
access_key_id = YOUR_AK
secret_access_key = YOUR_SK
region = ap-southeast-1
endpoint = https://obs.ap-southeast-1.myhuaweicloud.com
location_constraint = ap-southeast-1
force_path_style = false
```

保存并加权限：
```bash
chmod 600 ~/.config/rclone/rclone.conf
```

---

## 3. 检查配置
```bash
# 列出所有桶
rclone lsd huawei:

# 列出 sharemydata 桶内容
rclone lsd huawei:sharemydata
```

---

## 4. 挂载 OBS 桶
创建挂载点：
```bash
sudo mkdir -p /mnt/obs
sudo chown $USER:$USER /mnt/obs
```

前台挂载测试：
```bash
rclone mount huawei:sharemydata /mnt/obs   --vfs-cache-mode full   --allow-other   -vv
```

确认挂载：
```bash
df -h | grep obs
ls -lh /mnt/obs
```

后台挂载：
```bash
rclone mount huawei:sharemydata /mnt/obs   --vfs-cache-mode full   --allow-other   --daemon   --log-file /var/log/rclone-mount.log -vv
```

卸载：
```bash
fusermount -u /mnt/obs
```

---

## 5. 快速上传/下载（直接将网盘当作文件夹使用亦可）
上传：
```bash
rclone copy /path/to/local/file.txt huawei:sharemydata/ -P
```

下载：
```bash
rclone copy huawei:sharemydata/file.txt /path/to/local/ -P
```

---

## 6. 启动 WebDAV 服务（可选，mac m系列电脑无法挂载时，可以采用以下备用方法）
创建脚本：
```bash
nano ~/start_obs_webdav.sh
```

粘贴以下内容：
```bash
#!/bin/bash
# 启动 rclone WebDAV 服务，挂载 Huawei OBS 桶 sharemydata

LOG_FILE="$HOME/rclone_webdav.log"

rclone serve webdav huawei:sharemydata   --addr 0.0.0.0:8686   --vfs-cache-mode full   -vv >> "$LOG_FILE" 2>&1 &

echo "WebDAV 服务已启动，端口：8686，日志：$LOG_FILE"
```

赋予执行权限：
```bash
chmod +x ~/start_obs_webdav.sh
```

运行：
```bash
~/start_obs_webdav.sh
```

查看日志：
```bash
tail -f ~/rclone_webdav.log
```

停止服务：
```bash
pkill -f "rclone serve webdav huawei:sharemydata"
```

访问地址：
```
http://<服务器IP>:8686
```
