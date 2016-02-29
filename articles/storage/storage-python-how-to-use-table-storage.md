<properties
    pageTitle="如何使用 Python 的資料表儲存體 | Microsoft Azure"
    description="了解您能如何使用 Python 的表格服務來建立和刪除資料表，以及插入和查詢資料表。"
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


# 如何使用 Python 的資料表儲存體

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## 概觀

本指南說明如何使用 Azure 資料表儲存體服務執行一般案例。 在 Python 中並使用這些範例 [Python Azure 儲存體封裝][]。 所涵蓋的案例包括 「 建立和刪除
除了插入及查詢實體資料表中的資料表。

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.NOTE] 如果您需要安裝 Python 或 [Python Azure 封裝][], ，請參閱 [Python 安裝指南](../python-how-to-install.md)。


## 建立資料表

 **TableService** 物件可讓您操作資料表服務。 Auch die Eigenschaften
下列程式碼會建立 **TableService** 物件。 新增附近的程式碼
您想要在其中以程式設計方式存取 Azure 儲存體之任何 Python 檔案內的頂端附近：

    from azure.storage.table import TableService, Entity

下列程式碼會建立 **TableService** 物件使用的儲存體帳戶名稱和帳戶金鑰。  將 'myaccount' 和 'mykey' 取代為真實的帳戶和金鑰。

    table_service = TableService(account_name='myaccount', account_key='mykey')

    table_service.create_table('tasktable')

## 將實體加入至資料表

若要新增實體，請先建立一個定義實體屬性名稱和值的字典
。 請注意，每個實體，您必須
指定 **PartitionKey** 和 **RowKey**。 這些是實體的唯一
實體的識別碼。 您可以更查詢這些值
您可以查詢其他屬性快。 系統會使用 **PartitionKey** 至
自動將資料表實體分散在許多儲存體節點上。
具有相同的實體 **PartitionKey** 會儲存在相同節點上。 **RowKey** 是實體在資料分割內的唯一識別碼它
。

若要將實體新增至資料表，請將字典物件傳至
若要 **insert\_entity** 方法。

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
    table_service.insert_entity('tasktable', task)

您也可以傳遞的執行個體 **實體** 類別 **insert\_entity** 方法。

    task = Entity()
    task.PartitionKey = 'tasksSeattle'
    task.RowKey = '2'
    task.description = 'Wash the car'
    task.priority = 100
    table_service.insert_entity('tasktable', task)

## 更新實體

此程式碼說明如何以更新版本取代舊版本的現有實體
。

    task = {'description' : 'Take out the garbage', 'priority' : 250}
    table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

如果正在更新的實體不存在，則更新
操作便會失敗。 如果您想要儲存實體
不論它是否存在之前，使用 **insert\_or\_replace_entity**。
在下列範例中，第一個呼叫將取代現有實體。 第二個呼叫會插入新實體，因為沒有具有指定之實體 **PartitionKey** 和 **RowKey** 存在於資料表中。

    task = {'description' : 'Take out the garbage again', 'priority' : 250}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

    task = {'description' : 'Buy detergent', 'priority' : 300}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## 變更一組實體

有時候批次提交多個操作是有意義的，
可以確保伺服器會進行不可部分完成的處理。 若要達到此目的，
使用 **begin\_batch** 方法 **TableService** ，然後呼叫
操作系列。 當您真的想提交
批次時，呼叫 **commit\_batch**。 請注意，所有實體都必須位於相同的資料分割中，才能以批次方式進行變更。 以下範例在一個批次中同時新增兩個實體。

    task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
    task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
    table_service.begin_batch()
    table_service.insert_entity('tasktable', task10)
    table_service.insert_entity('tasktable', task11)
    table_service.commit_batch()

## 查詢實體

若要查詢資料表中的實體，請使用 **get\_entity** 方法
傳遞 **PartitionKey** 和 **RowKey**。

    task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
    print(task.description)
    print(task.priority)

## 查詢實體集合

這個範例會尋找在西雅圖的所有工作將會都根據 **PartitionKey**。

    tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
    for task in tasks:
        print(task.description)
        print(task.priority)

## 查詢實體屬性的子集

一項資料表查詢可以只擷取實體的少數屬性。
這項技術稱為 *投影*, 、 可減少頻寬並提高
查詢效能 (尤其是對大型實體而言)。 使用 **選取**
參數並傳遞您想要將屬性名稱
。

下列程式碼中的查詢傳回的說明
。

[AZURE.NOTE] 下列程式碼片段僅適用於雲端的運作方式
儲存體服務。 這不是支援的儲存體
模擬器。

    tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
    for task in tasks:
        print(task.description)

## 刪除實體

您可以使用實體的資料分割和資料列索引鍵來刪除實體。

    table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## 刪除資料表

下列程式碼會從儲存體帳戶刪除資料表。

    table_service.delete_table('tasktable')

## 後續步驟

既然您已了解資料表儲存體的基礎概念之後，請參考下列連結
若要了解有關更複雜的儲存體工作:

-   請參閱 MSDN 參考資料 [Azure 儲存體][]。
-   請瀏覽 [Azure 儲存體團隊部落格][]。

如需詳細資訊，請參閱 [Python 開發人員中心](/develop/python/)。

[Azure Storage Team blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Python Azure package]: https://pypi.python.org/pypi/azure
[Python Azure Storage package]: https://pypi.python.org/pypi/azure-storage

