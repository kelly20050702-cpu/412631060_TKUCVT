# W01｜虛擬化概論、環境建置與 Snapshot 機制

## 環境資訊
- Host OS：Windows 11 家用版
- VM 名稱：Ubuntu_tkucvt
- Ubuntu 版本：
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 24.04.4 LTS
Release:	24.04
Codename:	noble
- Docker 版本：Docker version 29.3.0, build 5927d80
- Docker Compose 版本：Docker Compose version v5.1.0
![enviornment](./images/enviornment.png)
![enviornment](./images/enviornment2.png)
![enviornment](./images/step2.png)
![enviornment](./images/step3.png)
![enviornment](./images/step3-2.png)

## VM 資源配置驗證

| 項目 | VMware 設定值 | VM 內命令 | VM 內輸出 |
|---|---|---|---|
| CPU | 2 vCPU | `lscpu \| grep "^CPU(s)"` | （4） |
| 記憶體 | 4 GB | `free -h \| grep Mem` | （3.8Gi） |
| 磁碟 | 40 GB | `df -h /` | （40G） |
| Hypervisor | VMware | `lscpu \| grep Hypervisor` | （VMware） |

![source](./images/source.png)

## 四層驗收證據
- [x] ① Repository：`cat /etc/apt/sources.list.d/docker.list` 輸出
![Repository](./images/step8.png)
- [x] ② Engine：`dpkg -l | grep docker-ce` 輸出
![Engine](./images/step9.png)
- [x] ③ Daemon：`sudo systemctl status docker` 顯示 active
![Daemon](./images/step10.png)
- [x] ④ 端到端：`sudo docker run hello-world` 成功輸出
![Hello World](./images/step11.png)
- [x] Compose：`docker compose version` 可執行
![Compose](./images/step10.png)

## 容器操作紀錄
- [x] nginx：`sudo docker run -d -p 8080:80 nginx` + `curl localhost:8080` 輸出
![nginx](./images/step15.png)
- [x] alpine：`sudo docker run -it --rm alpine /bin/sh` 內部命令與輸出
![alpine](./images/step16.png)
- [x] 映像列表：`sudo docker images` 輸出
![img](./images/step14.png)

## Snapshot 清單

| 名稱 | 建立時機 | 用途說明 | 建立前驗證 |
|---|---|---|---|
| clean-baseline | 剛裝好 Ubuntu並更新完系統套件，且確認環境健康 | 清乾淨狀態，用來避免系統壞掉可重頭開始 | ![Snapshot](./images/step17.png) |
| docker-ready | docker引擎安裝好且 hello-world跑通後，確認環境健康 | docker環境已準備驗通過，可開始實驗 | ![Snapshot](./images/step18.png) |

![Snapshot](./images/step19.png)

## 故障演練三階段對照

| 項目 | 故障前（基線） | 故障中（注入後） | 回復後 |
|---|---|---|---|
| docker.list 存在 | 是 | 否 | （是） |
| apt-cache policy 有候選版本 | 是 | 否 | （是） |
| docker 重裝可行 | 是 | 否 | （是） |
| hello-world 成功 | 是 | N/A | （是） |
| nginx curl 成功 | 是 | N/A | （是） |

![Snapshot](./images/step20.png)
![Snapshot](./images/step21.png)
![Snapshot](./images/step21-2.png)
![Snapshot](./images/step25.png)

## 手動修復 vs Snapshot 回復

| 面向 | 手動修復 | Snapshot 回復 |
|---|---|---|
| 所需時間 | （較久） | （較短） |
| 適用情境 | （確認知道哪裡要改） | （不確定哪裡有錯） |
| 風險 | （可能會有更多錯誤） | （完整修復到過去） |

![manualrepair](./images/step2223.png)

## Snapshot 保留策略
- 新增條件：每次要執行大動作更改設定、破壞實驗、安裝前，也要確認前置驗證皆通過。
- 保留上限：最多3個新的snapshot，避免檔案過大佔用空間，拖慢整台虛擬機。
- 刪除條件：當有新的snapshot且經過驗證通過，舊的也不需要用到時則可刪除。

## 最小可重現命令鏈
（列出讓他人能重現故障注入與回復驗證的命令序列）
- 再次入故障:
sudo mv /etc/apt/sources.list.d/docker.list /etc/apt/sources.list.d/docker.list.broken
- 驗證故障:
sudo apt update
- 使用snapshot回復執行操作：
VM 關機後 → Snapshot Manager → 選 docker-ready → Revert → 開機
- 回復驗證成功:
ls /etc/apt/sources.list.d/docker.list
sudo systemctl status docker --no-pager
sudo docker --version
docker compose version
sudo docker run --rm hello-world

## 排錯紀錄
- 症狀：移開docker.list後，執行sudo apt update而發生錯誤。
- 診斷：發現/etc/apt/sources.list.d/變成 broken。
- 修正：使用snapshot將狀態回到docker-ready。
- 驗證：重新開機後執行sudo apt update，docker系統狀態還原，hello-world恢復正常。

## 設計決策
Q:為什麼不在windows跑docker，而是在VM裡跑？
A:因為虛擬機裡面有snapshot回復功能，實驗時錯誤的話好解決，好回復到初始正常狀態。
