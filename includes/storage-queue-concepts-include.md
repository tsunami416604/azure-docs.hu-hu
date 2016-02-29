## 什麼是佇列儲存體？

Azure 佇列儲存體是一種服務，用於儲存
可從世界各地，透過
使用 HTTP 或 HTTPS 驗證的呼叫存取的大量訊息。 單一佇列訊息的大小
可以高達 64 KB，而且一個佇列可以包含數百萬個訊息，高達儲存體帳戶的
總容量限制。 一個儲存體帳戶可包含多達 500 TB 的 Blob、佇列和資料表資料。 請參閱 [Azure 儲存體延展性和效能目標](http://msdn.microsoft.com/library/azure/dn249410.aspx) 儲存體帳戶容量的詳細資料。

佇列儲存體的一般用途包括：

-   建立積存的工作供非同步處理
-   將訊息從 Azure Web 角色傳遞至 Azure 背景工作角色

## 佇列服務概念

佇列服務包含下列元件：

![Queue1](./media/storage-queue-concepts-include/queue1.png)


- **URL 格式:** 可利用下列 URL 格式:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
      
    下列 URL 可定址圖中的佇列：  
        
        http://myaccount.queue.core.windows.net/imagesToDownload

- **儲存體帳戶:** 存取 Azure 儲存體透過儲存體帳戶。 請參閱 [Azure 儲存體延展性和效能目標](../articles/storage/storage-scalability-targets.md) 儲存體帳戶容量的詳細資料。

- **佇列:** 佇列包含一組訊息。 所有訊息都必須放在佇列中。

- **訊息:** 任何格式，最多 64 KB 的訊息。

