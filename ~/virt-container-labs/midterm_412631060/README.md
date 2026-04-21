# 期中實作 — 412631060 莊佩欣

## 1. 架構與 IP 表

![網路拓樸圖](network-diagram.png)

## 2. Part A：VM 與網路

#### [ip -4 addr show 與 兩端互ping]

> bastion
![ip & ping](screenshots/ipb.png)

> app
![ip & ping](screenshots/ipa.png)

## 3. Part B：金鑰、ufw、ProxyJump

#### [防火牆規則]

> bastion
![ufw](screenshots/ufwb.png)

> app
![ufw](screenshots/ufwa.png)

#### [ssh app成功]

> app
![ssh](screenshots/ssh-proxyjump.png)

## 4. Part C：Docker 服務

> systemctl status docker
![status](screenshots/docker-running1.png)

> curl(ufw 規則處理）
![curl](screenshots/docker-running2.png)

## 5. Part D：故障演練
### 故障 1：<F2>
- 注入方式：

- 故障前：

![curl](screenshots/fault-F2-before1.png)
![curl](screenshots/fault-F2-before2.png)

- 故障中：

![curl](screenshots/fault-F2-during1.png)
![curl](screenshots/fault-F2-during2.png)
![curl](screenshots/fault-F2-during3.png)
![curl](screenshots/fault-F2-during4.png)

- 回復後：

![curl](screenshots/fault-F2-after1.png)
![curl](screenshots/fault-F2-after2.png)

- 診斷推論：

### 故障 2：<F3>
- 注入方式：

- 故障前：

![curl](screenshots/fault-F3-before1.png)
![curl](screenshots/fault-F3-before2.png)

- 故障中：

![curl](screenshots/fault-F3-during1.png)
![curl](screenshots/fault-F3-during2.png)
![curl](screenshots/fault-F3-during3.png)
![curl](screenshots/fault-F3-during4.png)

- 回復後：

![curl](screenshots/fault-F3-after1.png)
![curl](screenshots/fault-F3-after2.png)

- 診斷推論：


### 症狀辨識（若選 F1+F2 必答）
兩個都 timeout，我怎麼分？

## 6. 反思（200 字）
這次做完，對「分層隔離」或「timeout 不等於壞了」的理解有什麼改變？

## 7. Bonus（選做）
