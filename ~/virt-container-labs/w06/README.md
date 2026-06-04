# W06｜Docker Image 與 Dockerfile

## 映像組成
- Layers 是什麼：Layers 是映像檔內一堆唯讀的壓縮檔tarball，紀錄了每次指令對檔案系統所做的差異變更。

- Config 是什麼：是一份 JSON 格式的設定檔，主要紀錄容器啟動時的預設行為與 metadata，例如預設執行命令 CMD/ENTRYPOINT、工作目錄 WorkingDir、環境變數 Env 與暴露埠 Expose...等。

- Manifest 是什麼：是最外層的清單檔案，負責把 Config設定檔與所有的 Layers綁在一起，並明確記錄每個 Layer 的雜湊值digest和檔案大小。

## python:3.12-slim inspect 摘錄
- Config.Cmd：["python3"] 

![Config.Cmd](images/step1.2.png)

- Config.Env：
           [
                "PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "LANG=C.UTF-8",
                "GPG_KEY=7169605F62C751356D054A26A821E680E5FA6305",
                "PYTHON_VERSION=3.12.13",
                "PYTHON_SHA256=c08bc65a81971c1dd5783182826503369466c7e67374d1646519adf05207b684"
            ]
- Config.WorkingDir："" (預設為空)
- RootFS.Layers 數量：4層

![用 inspect 看 config](images/step3-1.png)
![用 inspect 看 config](images/step3-2.png)

## Layer 快取實驗
| 情境 | build 時間 |
|---|---|
| v1 首次 build | （0m9.338s） |
| v1 改 app.py 後 rebuild | （0m7.520s） |
| v2 首次 build | （0m6.437s） |
| v2 改 app.py 後 rebuild | （0m0.365s） |

![v1 首次 build](images/step8.png)
![驗證](images/step9.png)

![重 build不改任何東西](images/step10.png)

![v1改app.py後 rebuild](images/step11.png)

![v2首次 build](images/step12.13-1.png)
![v2首次 build](images/step12.13-2.png)

![v2改 app.py後 rebuild](images/step12.13-3.png)

- Q: 觀察：為什麼 v2 的 rebuild 這麼快？
- A: 因為在v1時是整個一起複製，若改變程式碼則要pip instal全部要重跑使得耗時。在v2裡，我們把不會變的 requirements.txt和一直需要改的app.py分開複製。這次我們只改了 app.py 的程式碼，Docker往下檢查時發現 requirements.txt 沒動過，所以就直接拿之前做好的快取CACHED來用，跳過最浪費時間的安裝過程，所以速度變更加快速了。

## CMD vs ENTRYPOINT 實驗
| 寫法 | `docker run <img>` 輸出 | `docker run <img> extra1 extra2` 輸出 |
|---|---|---|
| CMD shell form | （填入） | （填入） |
| CMD exec form | （填入） | （填入） |
| ENTRYPOINT + CMD | （填入） | （填入） |

結論（用自己的話寫）：

## Multi-stage 大小對照
| Image | SIZE |
|---|---|
| python:3.12（builder base） | （1.62GB) |
| python:3.12-slim（runtime base） | （179MB） |
| myapp:v2（單階段） | （填入） |
| myapp:multi（多階段） | （填入） |

![觀察共享 layer](images/step4.png)

解釋（用自己的話寫）：builder stage 的 layer 去哪了？

## .dockerignore 故障注入
| 項目 | 故障前 | 故障中 | 回復後 |
|---|---|---|---|
| du -sh . | （填入） | （填入） | （填入） |
| build context 傳輸大小 | （填入） | （填入） | （填入） |
| build 時間 | （填入） | （填入） | （填入） |

## 排錯紀錄
- 症狀：
- 診斷：
- 修正：
- 驗證：

## 設計決策
（說明本週至少 1 個技術選擇與取捨，例如：為什麼 runtime 選 `python:3.12-slim` 而不是 `alpine`？）
