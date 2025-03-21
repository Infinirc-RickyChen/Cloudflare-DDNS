# Cloudflare DDNS

這是一個自動化的Python腳本，用於監控網絡接口的IP變化並更新Cloudflare DNS記錄。適用於具有動態IP地址的服務器或家庭網絡。



## 功能特點

- 自動監控IPv4和IPv6地址變化
- 美觀的命令行表格輸出，顯示當前狀態
- 通過Cloudflare API自動更新DNS記錄
- 支持緩存，避免不必要的API調用
- 詳細的日誌記錄
- 支持定時檢查更新(默認每60秒)

## 安裝需求

- Python 3.6 或更高版本
- 已註冊的Cloudflare帳戶和域名
- Cloudflare API Token (具有編輯DNS記錄的權限)
- Linux/Unix系統 (已在CentOS, Ubuntu和Debian上測試)

### 依賴庫

腳本使用Python標準庫，不需要安裝額外的依賴。

## 快速開始

1.編輯腳本開頭的配置部分:
```
# 設置要監控的網卡接口名稱
MONITOR_INTERFACE = "ppp1"

# Cloudflare API 設置
CF_AUTH_TOKEN = "your_api_token"
CF_ZONE_ID = "your_zone_id"
CF_DOMAIN_NAME = "your.domain.com"

# A 記錄（IPv4）ID
CF_IPV4_RECORD_ID = "your_ipv4_record_id"

# AAAA 記錄（IPv6）ID
CF_IPV6_RECORD_ID = "your_ipv6_record_id"
```

運行腳本:
```bash
python cloudflare-ddns.py
```

## 獲取Cloudflare相關資訊
### 獲取API Token

1.登入您的Cloudflare帳戶
2.進入右上角的「My Profile」
3.選擇「API Tokens」菜單
4.點擊「Create Token」
5.選擇「Edit zone DNS」模板
6.設置Zone Resources為您的域名
7.創建並複製生成的Token

#### 獲取Zone ID和Record ID
使用以下命令獲取Zone ID:
```bash
curl -X GET "https://api.cloudflare.com/client/v4/zones" \
    -H "Authorization: Bearer YOUR_API_TOKEN" \
    -H "Content-Type: application/json"
```

使用Zone ID獲取Record ID:
```bash
curl -X GET "https://api.cloudflare.com/client/v4/zones/YOUR_ZONE_ID/dns_records" \
    -H "Authorization: Bearer YOUR_API_TOKEN" \
    -H "Content-Type: application/json"
```




## 設置為系統服務
使用Systemd (推薦)

創建服務文件:
```bash
sudo nano /etc/systemd/system/cloudflare-ddns.service
```
```
[Unit]
Description=Cloudflare DDNS Update Service
After=network.target

[Service]
Type=simple
User=yourusername
ExecStart=/path/to/cloudflare-ddns.py
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl enable cloudflare-ddns
sudo systemctl start cloudflare-ddns
```

檢查服務狀態:
```bash
crontab -e
```
