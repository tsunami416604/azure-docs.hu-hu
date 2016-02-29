<properties 
    pageTitle="如何使用 Python 的佇列儲存體 | Microsoft Azure" 
    description="了解如何使用 Python 的 Azure 佇列服務來建立和刪除佇列，以及插入、取得和刪除訊息。" 
    services="storage" 
    documentationCenter="python" 
    authors="emgerner-msft" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="12/11/2015" 
    ms.author="emgerner"/>

# 如何使用 Python 的佇列儲存體

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## 概觀

本指南說明如何使用 Azure 佇列儲存體服務執行一般案例。 在 Python 中並使用這些範例 [Python Azure 儲存體封裝][]。 涵蓋的案例包括 **插入**, ，**查看**,，
**取得**, ，和 **刪除** 佇列訊息，以及 **建立和
刪除佇列**。 如需佇列的詳細資訊，請參閱 [後續步驟][] 一節。

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


> [AZURE.NOTE] 如果您需要安裝 Python 或 [Python Azure 封裝][], ，請參閱 [Python 安裝指南](../python-how-to-install.md)。

## 作法：建立佇列

 **QueueService** 物件可讓您處理佇列。 下列程式碼會建立 **QueueService** 物件。 將下列內容新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 Python 檔案內的頂端附近：

    from azure.storage.queue import QueueService

下列程式碼會建立 **QueueService** 物件使用的儲存體帳戶名稱和帳戶金鑰。 將 'myaccount' 和 'mykey' 取代為真實的帳戶和金鑰。

    queue_service = QueueService(account_name='myaccount', account_key='mykey')

    queue_service.create_queue('taskqueue')


## 作法：將訊息插入佇列中

若要將訊息插入佇列中，使用 **put\_message** 方法
建立新訊息，並將它新增至佇列。

    queue_service.put_message('taskqueue', 'Hello World')


## 作法：預覽下一個訊息

您可以在佇列前面查看訊息，而無需將它
從佇列呼叫 **peek\_messages** 方法。 依預設，
**peek\_messages** 會查看單一訊息。

    messages = queue_service.peek_messages('taskqueue')
    for message in messages:
        print(message.message_text)


## 作法：清除下一個佇列訊息

您的程式碼可以使用兩個步驟來將訊息從佇列中移除。 呼叫
**get\_messages**, ，您取得佇列中的下一個訊息的預設值。 A
傳回從訊息 **get\_messages** 就看不到任何其他
程式碼而言將會是不可見的。 依預設，此訊息會維持
30 秒的不可見狀態。 若要完成從佇列中移除訊息，
您還必須呼叫 **delete\_message**。 這個移除訊息的兩步驟程序
可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，
另一個程式碼的執行個體可以取得
相同訊息並再試一次。 您的程式碼呼叫 **delete\_message** 右
。

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        print(message.message_text)
        queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)


## 作法：變更佇列訊息的內容

您可以在佇列中就地變更訊息內容。 如果
訊息代表工作作業，則您可以使用此功能來更新
工作作業的狀態。 以下程式碼使用 **update\_message**
方法來更新訊息。

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        queue_service.update_message('taskqueue', message.message_id, 'Hello World Again', message.pop_receipt, 0)

## 作法：清除佇列訊息的其他選項

自訂從佇列中擷取訊息的方法有兩種。
首先，您可以取得一批訊息 (最多 32 個)。 其次，您可以設定
較長或較短的可見度逾時，讓您的程式碼有較長或較短的
時間可以完全處理每個訊息。 下列程式碼範例將使用
**get\_messages** 方法一次呼叫中取得 16 個訊息。 接著，它會
使用 for 迴圈處理每個訊息。 它也會將隱藏逾時設定為
每個訊息五分鐘。

    messages = queue_service.get_messages('taskqueue', numofmessages=16, visibilitytimeout=5*60)
    for message in messages:
        print(message.message_text)
        queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

## 作法：取得佇列長度

您可以取得佇列中的估計訊息數目。 Auch die Eigenschaften
**get\_queue\_metadata** 方法會要求佇列服務傳回中繼資料
佇列的相關和 **x ms-近似-訊息-計數** 應做為索引傳回字典以找出計數。
由於佇列服務在回應您的要求之後可以新增或移除訊息，
此結果僅為近似值。

    queue_metadata = queue_service.get_queue_metadata('taskqueue')
    count = queue_metadata['x-ms-approximate-messages-count']

## 作法：刪除佇列

若要刪除佇列及其內含的所有訊息，請呼叫
**delete\_queue** 方法。

    queue_service.delete_queue('taskqueue')

## 後續步驟

了解佇列儲存體的基礎概念之後，請參考下列連結
以深入了解更複雜的儲存體工作。

-   請瀏覽 [Azure 儲存體團隊部落格][]

如需詳細資訊，請參閱 [Python 開發人員中心](/develop/python/)。

[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Python Azure package]: https://pypi.python.org/pypi/azure
[Python Azure Storage package]: https://pypi.python.org/pypi/azure-storage 
 

