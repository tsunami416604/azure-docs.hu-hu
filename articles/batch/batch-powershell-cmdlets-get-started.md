<properties
   pageTitle="開始使用 Azure Batch PowerShell | Microsoft Azure"
   description="取得您可以用來管理 Azure Batch 服務之 Azure PowerShell Cmdlet 的快速簡介"
   services="batch"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="10/13/2015"
   ms.author="danlep"/>

# 開始使用 Azure Batch PowerShell Cmdlet
本文是 Azure PowerShell Cmdlet 的簡介，可用來管理 Batch 帳戶並取得 Batch 作業、工作及其他詳細資訊的相關資訊。

如需詳細的 cmdlet 語法輸入 `get-help <Cmdlet_name>` 或參閱 [Azure 批次 cmdlet 參考文件](https://msdn.microsoft.com/library/azure/mt125957.aspx)。

[AZURE.INCLUDE [powershell-preview-include](../../includes/powershell-preview-include.md)]

## 必要條件

* **Azure PowerShell** -Azure 資源管理員模組中隨附的批次 cmdlet。 請參閱 [Azure 資源管理員 cmdlet](https://msdn.microsoft.com/library/azure/mt125356.aspx) 必要條件、 安裝指示和基本使用方式。



* **以批次的提供者命名空間 （一次性） 註冊** -之前使用批次帳戶，您必須註冊批次的提供者命名空間。 每個訂用帳戶只需要執行這項作業一次。

    ```
    Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch
    ```

## 管理 Batch 帳戶和金鑰


### 建立批次帳戶：

**新 AzureRmBatchAccount** 在指定的資源群組中建立新的批次帳戶。 如果您還沒有資源群組，建立一個執行 [新增 AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx) 指令程式，指定 Azure 區域的其中一個 **位置** 參數，例如"Central US"。 例如：

```
New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

接著，在資源群組中，指定的帳戶名稱建立新的批次帳戶 <*account_name*> 和批次服務可用的位置。 建立帳戶可能需要幾分鐘才能完成。 例如：

```
New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup
```

> [AZURE.NOTE] 批次帳戶名稱必須是唯一的 Azure、 介於 3 到 24 個字元，再使用小寫字母和數字只。

### 取得帳戶存取金鑰
**Get AzureRmBatchAccountKeys** 顯示 Azure 批次帳戶相關聯的存取金鑰。 例如，執行下列命令以取得您所建立帳戶的主要和次要金鑰。

```
$Account = Get-AzureBatchAccountKeys –AccountName <accountname>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### 產生新的存取金鑰
**新 AzureRmBatchAccountKey** 產生新的主要或次要帳戶金鑰，為 Azure 批次帳戶。 例如，若要為您的 Batch 帳戶產生新的主要金鑰，請輸入：

```
New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [AZURE.NOTE] 若要產生新的次要金鑰，指定為 「 次要 」 **KeyType** 參數。 您必須個別重新產生主要和次要金鑰。

### 刪除 Batch 帳戶
**移除 AzureRmBatchAccount** 刪除批次帳戶。 例如：

```
Remove-AzureRmBatchAccount -AccountName <account_name>
```

出現提示時，請確認您想要移除的帳戶。 帳戶移除可能需要一些時間來完成。

## 查詢作業、工作及其他詳細資料

使用 cmdlet，例如 **Get-azurebatchjob**, ，**Get-azurebatchtask**, ，和 **Get-azurebatchpool** 查詢的批次帳戶下建立的實體。

若要使用這些指令程式，您必須先建立一個 AzureBatchContext 物件以儲存您的帳戶名稱和金鑰：

```
$context = Get-AzureRmBatchAccountKeys -AccountName <account_name>
```

您可以將此內容傳入與批次服務互動所使用的 cmdlet **BatchContext** 參數。

> [AZURE.NOTE] 根據預設，帳戶的主要金鑰用於驗證，但您可以明確地選取要使用透過變更 BatchAccountContext 物件的索引鍵 **KeyInUse** 屬性 ︰ `$context.KeyInUse = "Secondary"`。


### 查詢資料

例如，使用 **Get-azurebatchpools** 來尋找您的集區。 依預設，此查詢您的帳戶下的所有集區，假設您已經將 BatchAccountContext 物件儲存在 *$context*:

```
Get-AzureBatchPool -BatchContext $context
```
### 使用 OData 篩選

您可以提供 OData 篩選 **篩選** 參數來尋找只將您感興趣的物件。 例如，您可以找到識別碼以 “myPool” 開頭的所有集區：

```
$filter = "startswith(id,'myPool')"

Get-AzureBatchPool -Filter $filter -BatchContext $context
```

雖然這個方法比在本機管線中使用 “Where-Object” 較不具有彈性， 不過查詢將直接傳送進 Batch 服務，讓所有篩選在伺服器端運作，進而省下網際網路頻寬。

### 使用識別碼參數

可作為 OData 篩選的替代方式是使用 **識別碼** 參數。 若要查詢識別碼為 "myPool" 的特定集區：

```
Get-AzureBatchPool -Id "myPool" -BatchContext $context

```
 **識別碼** 參數可支援的完整識別碼搜尋、 不能使用萬用字元或 OData 樣式的篩選。

### 使用管線

Batch Cmdlet 可以利用 PowerShell 管線在 Cmdlet 之間傳送資料。 這和指定參數有相同效果，但讓列出多個實體更容易。 例如，下列可找出您帳戶下的所有工作：

```
Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context
```

### 使用 MaxCount 參數

依預設，每個 Cmdlet 最多傳回 1000 個物件。 如果到達此限制時，請縮小您的篩選以傳回較少的物件，或明確設定 [最大的使用 **MaxCount** 參數。 例如：

```
Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

```

若要移除上限，將 **MaxCount** 為 0 或更少。

## 相關主題
* [下載 Azure PowerShell](http://go.microsoft.com/?linkid=9811175)
* [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)
* [Azure 批次 Cmdlet 參考資料](https://msdn.microsoft.com/library/azure/mt125957.aspx)
* [有效率地查詢 Batch 服務](batch-efficient-list-queries.md)


