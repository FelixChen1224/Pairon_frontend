# Pairon_frontend

本專案為個人碩論[整合深度學習與多重特徵提取之人物辨識及全身追蹤系統](https://nfuedu-my.sharepoint.com/:b:/g/personal/11161115_nfu_edu_tw/EYsucjSdPC5EilwhdXpUICMBe2WGj3V94p1UbM2N3-0HIg?e=vmi2eI)的前端實作部分，需要配合[後端](https://github.com/FelixChen1224/Pairon)運行。

## 專案流程

```mermaid
sequenceDiagram

    participant User
    participant Vue as Vue Interface
    participant REST as RESTful API
    participant Socket as Socket.IO
    participant Backend

    User->>Vue: 1.1 選擇影片檔案
    Vue->>REST: 1.2 POST /upload/video
    REST-->>Vue: 1.3 回傳上傳確認
    Vue->>Socket: 1.4 建立WebSocket連接
    Socket-->>Vue: 1.5 連接確認

    Backend->>Socket: 2.1 傳送處理進度更新
    Socket->>Vue: 2.2 更新進度資訊
    Vue->>User: 2.3 顯示進度指標

    Backend->>Socket: 3.1 傳送特徵辨識結果
    Socket->>Vue: 3.2 更新辨識資訊
    Vue->>User: 3.3 更新視覺化元件

    Backend->>Socket: 4.1 處理完成通知
    Socket->>Vue: 4.2 傳送完成狀態
    Vue->>REST: 4.3 GET /video/result
    REST-->>Vue: 4.4 回傳處理結果
    Vue->>User: 4.5 顯示完整報告
```

## 專案畫面

![首頁](https://i.imgur.com/CWQEuHl.png)
