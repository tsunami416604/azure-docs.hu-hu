<properties 
    pageTitle="透過 Azure 入口網站管理 DocumentDB 帳戶 | Microsoft Azure" 
    description="了解如何透過 Azure 入口網站管理 DocumentDB 帳戶。 尋找使用 Azure 入口網站來檢視、複製、刪除和存取帳戶的指南。" 
    keywords="Azure Portal, documentdb, azure, Microsoft azure"
    services="documentdb" 
    documentationCenter="" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/18/2015" 
    ms.author="anhoh"/>

# 如何管理 DocumentDB 帳戶

了解如何使用金鑰、一致性設定，並了解如何在 Azure 入口網站中刪除帳戶。

## <a id="keys"></a>檢視、複製和重新產生存取金鑰
當您建立 DocumentDB 帳戶時，服務會產生兩個主要存取金鑰 
用於驗證時 DocumentDB
存取帳戶。 藉由提供兩個存取金鑰，DocumentDB 可讓
您可以重新產生金鑰，DocumentDB 不需要中斷
。

在 [Microsoft Azure 入口網站](https://portal.azure.com/),，
存取 **金鑰** 部分從您 **DocumentDB 帳戶** 刀鋒視窗中，若要檢視，
複製和重新產生存取金鑰，以供存取程式
。

![Azure 入口網站 [金鑰] 刀鋒視窗的螢幕擷取畫面](media/documentdb-manage-account/keys.png)

### 在 Azure 入口網站中檢視並複製存取金鑰

1.      In the [Azure Portal](https://portal.azure.com/), access your DocumentDB account. 

2.      In the **Summary** lens, click **Keys**.

3.      On the **Keys** blade, click the **Copy** button to the right of the
您想要複製的索引鍵。

  ![在 Azure 入口網站 [金鑰] 刀鋒視窗中檢視並複製存取金鑰](./media/documentdb-manage-account/image004.jpg)

### 重新產生存取金鑰

您應該變更存取金鑰，DocumentDB 帳戶
定期以協助您讓連線更加安全。 兩個存取金鑰
指派給可讓您維持 DocumentDB 的連線
使用其中一個存取金鑰，當您重新產生存取金鑰的帳戶。

> [AZURE.WARNING] 重新產生存取金鑰會影響任何應用程式
相依於目前金鑰。 所有用戶端，使用便捷鍵
存取 DocumentDB 帳戶必須更新為使用新的金鑰。

如果您的應用程式或雲端服務正在使用 DocumentDB 帳戶，
您將會導致連線中斷重新產生金鑰，除非您變換
您的金鑰。 下列步驟概述變換金鑰所牽涉的程序。

1.      Update the access key in your application code to reference the
DocumentDB 帳戶的次要存取金鑰。

2.      Regenerate the primary access key for your DocumentDB account.
在 [Azure 入口網站](https://portal.azure.com/),，
存取 DocumentDB 帳戶。

3.      In the Summary lens, click **Keys**.

4.      On the **Keys** blade, click the **Regenerate Primary** command, then
按一下 [ **確定** 來確認您想要產生新的金鑰。

5.      Once you have verified that the new key is available for use
(約 5 分鐘後重新產生)，更新存取金鑰。
應用程式程式碼以參考新的主要存取金鑰。

6.      Regenerate the secondary access key.

*請注意，可能需要幾分鐘的時間可以新產生的金鑰
可用來存取 DocumentDB 帳戶。*

## <a id="consistency"></a>管理 DocumentDB 一致性設定
DocumentDB 支援四個定義完善的使用者可設定資料一致性
層級，使開發人員得以在預測
一致性、 可用性和延遲的權衡得失。

- **強式** 一致性可保證讀取作業一律
傳回上次寫入的值。

- **界限-陳舊** 一致性可保證讀取
並未過時太久。 它尤其能保證讀取任何
多個 *K* 比上次寫入的版本還舊的版本。 

- **工作階段** 一致性可保證單純讀取 (您永遠不會
讀取舊資料，則新的則舊一次)、 單純寫入 (寫入為
依照順序)，以及您閱讀任何單一內最近的寫入
用戶端的觀點來看。

- **最終** 一致性可保證讀取作業
一律讀取寫入的有效子集，最終會趨於一致。

*請注意，根據預設，DocumentDB 帳戶佈建工作階段
層級一致性。  如需有關 DocumentDB 一致性
設定，請參閱 [一致性
層級](http://go.microsoft.com/fwlink/p/?LinkId=402365) 一節。*

### 指定 DocumentDB 帳戶的預設一致性

1.      In the [Azure Portal](https://portal.azure.com/), access your DocumentDB account. 

2.      In the **Configuration** lens, click **Default Consistency**.

3.      On the **Default Consistency** blade, select the default consistency
您要用於 DocumentDB 帳戶的層級。

![預設一致性工作階段](./media/documentdb-manage-account/image005.png)

![預設一致性界限](./media/documentdb-manage-account/image006.png)

4.      Click **Save**.

5.      The progress of the operation may be monitored via the Azure Portal Notifications hub.

*請注意，可能需要幾分鐘，再變更為預設值
一致性設定才會生效，整個 DocumentDB 帳戶。*

## <a id="delete"></a> 如何: 刪除 DocumentDB 帳戶，在 Azure 入口網站
若要移除您不再使用 Azure 入口網站的 DocumentDB 帳戶，請使用
**刪除** 命令 **DocumentDB 帳戶** 刀鋒視窗。

![如何在 Azure 入口網站中刪除 DocumentDB 帳戶](./media/documentdb-manage-account/image009.png)

1.      In the [Azure Portal](https://portal.azure.com/), access the DocumentDB Account you
想要刪除。 

2.      On the **DocumentDB Account** blade, click the **Delete** command.

3.      On the resulting confirmation blade, type the DocumentDB Account
若要確認您想要刪除的帳戶名稱。

4.      Click the **Delete** button on the confirmation blade.

## <a id="next"></a>後續步驟

了解如何 [開始使用 DocumentDB
   帳戶](http://go.microsoft.com/fwlink/p/?LinkId=402364)。

若要深入了解 DocumentDB，請參閱 Azure DocumentDB
    在文件
    [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409)。

 
 

