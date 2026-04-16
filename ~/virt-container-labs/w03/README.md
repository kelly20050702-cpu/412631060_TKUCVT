# W03｜多 VM 架構：分層管理與最小暴露設計

## 網路配置

| VM | 角色 | 網卡 | 模式 | IP | 開放埠與來源 |
|---|---|---|---|---|---|
| bastion | 跳板機 | NIC 1 | NAT | （192.168.233.133） | SSH from any |
| bastion | 跳板機 | NIC 2 | Host-only | （192.168.221.128） | — |
| app | 應用層 | NIC 1 | Host-only | （192.168.221.129） | SSH from 192.168.221.0/24 |
| db | 資料層 | NIC 1 | Host-only | （192.168.221.130） | SSH from app + bastion |

> bastion
![NAT&Host-only](images/step2b.png)

> app
![Host-only](images/step2a.png)

> db
![Host-only](images/step2d.png)

## SSH 金鑰認證

- 金鑰類型：（例：ed25519）

![產生金鑰](images/step6b.png)

- 公鑰部署到：（例：app 和 db 的 ~/.ssh/authorized_keys）

![部署公鑰](images/step7b.png)
![部署公鑰](images/step8a.png)

- 免密碼登入驗證：
  - bastion → app：（貼上輸出）
  - bastion → db：（貼上輸出）

![停用密碼登入](images/step9a.png)
![停用密碼登入驗證](images/step9b.png)

## 防火牆規則

### app 的 ufw status
（貼上 `sudo ufw status verbose` 輸出）

![app設定規則](images/step10a.png)

### db 的 ufw status
（貼上 `sudo ufw status verbose` 輸出）

![db設定規則](images/step11d.png)

### 防火牆確實在擋的證據
（貼上步驟 13 的 curl 8080 失敗輸出）

![在app上跑](images/step13a.png)
![在bastion上跑](images/step13b.png)

## ProxyJump 跳板連線
- 指令：（貼上你使用的 ssh -J 或 ssh config 設定）

![ssh -J/ssh config設定](images/step14h.png)

- 驗證輸出：（貼上連線成功的 hostname 輸出）

![hostname輸出](images/step15h.png)

- SCP 傳檔驗證：（貼上結果）

![SCP驗證](images/step1617h.png)

## 故障場景一：防火牆全封鎖

| 項目 | 故障前 | 故障中 | 回復後 |
|---|---|---|---|
| app ufw status | active + rules(allow 22) | deny all | （active + rules(allow 22)） |
| bastion ping app | 成功 | （成功） | （成功） |
| bastion SSH app | 成功 | **timed out** | （成功） |

> 故障前
![故障前](images/step18b1.png)
![故障前](images/step18b2.png)

> 故障中
![注入故障](images/step19a.png)
![觀測故障](images/step20b.png)

> 回復
![回復防火牆](images/step21a1.png)
![回復防火牆](images/step21a2.png)
![回復驗證](images/step22b.png)

## 故障場景二：SSH 服務停止

| 項目 | 故障前 | 故障中 | 回復後 |
|---|---|---|---|
| ss -tlnp grep :22 | 有監聽 | 無監聽 | （填入） |
| bastion ping app | 成功 | 成功 | （填入） |
| bastion SSH app | 成功 | **refused** | （填入） |

> 故障前
![故障前](images/step23a1.png)

> 故障中
![注入故障](images/step23a2.png)
![觀測故障](images/step24b.png)

> 回復
![回復ssh服務](images/step25a.png)
![回復驗證](images/step25b.png)

## timeout vs refused 差異
（用自己的話說明兩種錯誤的差異、各自指向什麼排錯方向）

## 網路拓樸圖

![網路拓樸圖](network-diagram.png)

## 排錯紀錄
- 症狀：
- 診斷：（你首先查了什麼？）
- 修正：（做了什麼改動？）
- 驗證：（如何確認修正有效？）

## 設計決策
（說明本週至少 1 個技術選擇與取捨，例如：為什麼 db 允許 bastion 直連而不是只允許從 app 跳？）
