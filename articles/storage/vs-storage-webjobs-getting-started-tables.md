<properties
    pageTitle="開始使用 Azure 儲存體和 Visual Studio 已連接服務 (WebJob 專案)"
    description="在使用 Visual Studio 已連接服務連接到儲存體帳戶之後，如何於 Visual Studio Azure WebJobs 專案中開始使用 Azure 資料表儲存體"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/16/2015"
    ms.author="tarcher"/>

# 開始使用 Azure 儲存體 (Azure WebJob 專案)

## 概觀

本文提供了 C# 程式碼範例，示範如何透過 Azure 資料表儲存體服務使用 Azure WebJobs SDK 1.x 版。 此程式碼範例使用 [WebJobs SDK](websites-dotnet-webjobs-sdk.md) 版本 1.x。

Azure 資料表儲存體服務可讓您儲存大量的結構化資料。 此服務是一個 NoSQL 資料存放區，接受來自 Azure 雲端內外經過驗證的呼叫。 Azure 資料表很適合儲存結構化、非關聯式資料。  請參閱 [如何使用資料表儲存體.NET](storage-dotnet-how-to-use-tables.md/#create-a-table "如何使用資料表儲存體.NET") 如需詳細資訊。


一些程式碼片段顯示 **資料表** 屬性的函式中使用 [以手動方式呼叫](vs-storage-webjobs-getting-started-blobs.md#manual), ，也就是不是使用其中一個觸發程序屬性。

## 如何將實體新增至資料表

若要將實體加入至資料表，使用 **資料表** 屬性搭配 **ICollector<T>** 或 **IAsyncCollector<T>** 參數其中 **T** 指定您想要加入之實體的結構描述。 屬性建構函式採用字串參數，來指定資料表的名稱。

以下程式碼範例會將 **人** 名為的資料表的實體 *輸入*。

        [NoAutomaticTrigger]
        public static void IngressDemo(
            [Table("Ingress")] ICollector<Person> tableBinding)
        {
            for (int i = 0; i < 100000; i++)
            {
                tableBinding.Add(
                    new Person() {
                        PartitionKey = "Test",
                        RowKey = i.ToString(),
                        Name = "Name" }
                    );
            }
        }

使用型別通常您與 **設為** 衍生自 **TableEntity** 或實作 **ITableEntity**, ，但並非必要。 下列任一個 **人** 類別與先前所示的程式碼都能 **輸入** 方法。

        public class Person : TableEntity
        {
            public string Name { get; set; }
        }

        public class Person
        {
            public string PartitionKey { get; set; }
            public string RowKey { get; set; }
            public string Name { get; set; }
        }

如果您想要直接使用 Azure 儲存體 API，您可以加入 **CloudStorageAccount** 方法簽章的參數。

## 即時監視

因為資料外送流量函式經常處理大量資料，所以 WebJobs SDK 儀表板提供即時監視資料。  **引動過程記錄** 區段可告訴您是否函式仍在執行。

![輸入函式執行中](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

 **引動過程詳細資料** 頁面會報告函式的進度 （寫入的實體數目） 時它正在執行，並讓您有機會將它中止。

![輸入函式執行中](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

當函式完成時， **引動過程詳細資料** 頁面會報告寫入的資料列數目。

![輸入函式已完成](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## 如何讀取資料表中的多個實體

若要讀取資料表，使用 **資料表** 屬性搭配 **IQueryable<T>** 參數型別 **T** 衍生自 **TableEntity** 或實作 **ITableEntity**。

下列程式碼範例會讀取並記錄所有的資料列 **輸入** 資料表 ︰

        public static void ReadTable(
            [Table("Ingress")] IQueryable<Person> tableBinding,
            TextWriter logger)
        {
            var query = from p in tableBinding select p;
            foreach (Person person in query)
            {
                logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
                    person.PartitionKey, person.RowKey, person.Name);
            }
        }

### 如何讀取資料表中的單一實體

沒有 **資料表** 屬性建構函式搭配兩個額外的參數可讓您指定的資料分割索引鍵和資料列索引鍵，當您想要繫結到單一資料表實體。

下列程式碼範例會讀取的資料表列 **人** 根據資料分割索引鍵與列索引鍵值在佇列訊息中收到的實體 ︰  

        public static void ReadTableEntity(
            [QueueTrigger("inputqueue")] Person personInQueue,
            [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
            TextWriter logger)
        {
            if (personInTable == null)
            {
                logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                        personInQueue.PartitionKey, personInQueue.RowKey);
            }
            else
            {
                logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                        personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
            }
        }


 **人** 在此範例中的類別沒有實作 **ITableEntity**。

## 如何直接利用 .NET 儲存體 API 來使用資料表

您也可以使用 **資料表** 屬性搭配 **CloudTable** 更靈活地使用資料表的物件。

下列程式碼範例會使用 **CloudTable** 要加入至單一實體物件 *輸入* 資料表。

        public static void UseStorageAPI(
            [Table("Ingress")] CloudTable tableBinding,
            TextWriter logger)
        {
            var person = new Person()
                {
                    PartitionKey = "Test",
                    RowKey = "100",
                    Name = "Name"
                };
            TableOperation insertOperation = TableOperation.Insert(person);
            tableBinding.Execute(insertOperation);
        }

如需有關如何使用 **CloudTable** 物件，請參閱 [如何使用資料表儲存體.NET](../storage-dotnet-how-to-use-tables.md)。

## 佇列操作說明文章所涵蓋的相關主題

如需如何處理佇列訊息，所觸發的資料表處理的詳細資訊，或資料表處理特有的 WebJobs SDK 案例，請參閱 [如何透過 WebJobs SDK 使用 Azure 佇列儲存體](vs-storage-webjobs-getting-started-queues.md)。



## 後續步驟

本文提供的程式碼範例示範如何處理使用 Azure 資料表的常見案例。 如需如何使用 Azure WebJobs 和 WebJobs SDK 的詳細資訊，請參閱 [Azure WebJobs 建議資源](http://go.microsoft.com/fwlink/?linkid=390226)。


