<properties
    pageTitle="在 HDInsight 上對 Hadoop 進行偵錯：檢視記錄與解譯錯誤訊息 | Microsoft Azure"
    description="了解您使用 PowerShell 來管理 HDInsight 時可能收到的錯誤訊息，以及可採取來回復的步驟。"
    services="hdinsight"
    tags="azure-portal"
    editor="cgronlun"
    manager="paulettm"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2015"
    ms.author="jgao"/>

# 在 HDInsight 上對 Hadoop 進行偵錯：檢視記錄與解譯錯誤訊息

本主題列舉的錯誤訊息可協助 Azure HDInsight 的 Hadoop 使用者了解，他們在使用 Azure PowerShell 來管理服務時可能遭遇的錯誤狀況，並建議一些步驟供他們從錯誤中回復。

使用 Azure 入口網站管理 HDinsight 叢集時也可能會看到其中某些錯誤訊息。 但由於此情況下可能採取的補救動作有其限制，您可能看到的其他錯誤訊息比較不詳細。 在很顯然有緩和措施的情況下，則會提供其他錯誤訊息。 舉例來說，如果違反參數的限制，則輸入值的方塊右上角會彈出訊息。 以下是要求太多資料節點的例子。 補救方法是將數量減少到 33 以下的允許值。

![HDInsight 入口網站錯誤訊息][image-hdi-debugging-error-messages-portal]

若發生了 Azure HDInsight 特定的錯誤，最好是能夠了解錯誤的相關資訊。 請參閱 [HDInsight 錯誤碼](#hdi-error-codes) 來了解不同的錯誤碼，以及如何修正這些。 在某些情況下，您可能想要存取 Hadoop 記錄本身。 您可以直接從 Azure 入口網站進行。

## 檢視叢集健康情況和工作記錄檔

* **存取 Hadoop UI**。 從 Azure 入口網站中，按一下 HDInsight 叢集名稱以開啟叢集刀鋒視窗。 從 [叢集] 分頁中，按一下 [ **儀表板**。

    ![啟動叢集儀表板](./media/hdinsight-debug-jobs/hdi-debug-launch-dashboard.png)
  
    出現提示時，輸入叢集系統管理員認證。 在 [開啟查詢主控台中，按一下 [ **Hadoop UI**。

    ![啟動 Hadoop UI](./media/hdinsight-debug-jobs/hdi-debug-launch-dashboard-hadoop-ui.png)

* **存取 Yarn UI**。 從 Azure 入口網站中，按一下 HDInsight 叢集名稱以開啟叢集刀鋒視窗。 從 [叢集] 分頁中，按一下 [ **儀表板**。 出現提示時，輸入叢集系統管理員認證。 在 [開啟查詢主控台中，按一下 [ **YARN UI**。

    您可以使用 YARN UI 來執行下列動作：

    * **取得叢集狀態**。 從左窗格中，展開 **叢集**, ，然後按一下 **有關**。 這樣即會顯示叢集狀態詳細資料，例如，配置的記憶體總和、使用的核心數目、叢集資源管理員的狀態、叢集版本等。

        ![啟動叢集儀表板](./media/hdinsight-debug-jobs/hdi-debug-yarn-cluster-state.png)

    * **取得節點狀態**。 從左窗格中，展開 **叢集**, ，然後按一下 **節點**。 這樣會列出叢集中的所有節點、每個節點的 HTTP 位址、配置給每個節點的資源等資訊。

    * **監視工作狀態**。 從左窗格中，展開 **叢集**, ，然後按一下 [ **應用程式** 以列出叢集中的所有工作。 如果您想要查看特定狀態 (例如新的、 送出，執行等) 中的工作，請按一下下方的適當連結 **應用程式**。 您可以進一步按一下工作名稱來深入了解該工作，例如包含輸出、記錄等相關資訊。

* **存取 HBase UI**。 從 Azure 入口網站中，按一下 HDInsight HBase 叢集名稱以開啟叢集刀鋒視窗。 從 [叢集] 分頁中，按一下 [ **儀表板**。 出現提示時，輸入叢集系統管理員認證。 在 [開啟查詢主控台中，按一下 [ **HBase UI**

## <a id="hdi-error-codes"></a>HDInsight 錯誤代碼

使用者可能會在 Azure PowerShell 或入口網站中遇到的錯誤，會按照名稱的字母順序列出。 錯誤會接著連結中的項目 [描述和錯誤的緩和](#discription-mitigation-errors) 一節，提供錯誤的下列資訊:

- **描述**: 使用者看見錯誤訊息
- **緩和**: 可採取以便從錯誤中回復的步驟。



- [AtleastOneSqlMetastoreMustBeProvided](#AtleastOneSqlMetastoreMustBeProvided)
- [AzureRegionNotSupported](#AzureRegionNotSupported)
- [ClusterContainerRecordNotFound](#ClusterContainerRecordNotFound)
- [ClusterDnsNameInvalidReservedWord](#ClusterDnsNameInvalidReservedWord)
- [ClusterNameUnavailable](#ClusterNameUnavailable)
- [ClusterUserNameInvalid](#ClusterUserNameInvalid)
- [ClusterUserNameInvalidReservedWord](#ClusterUserNameInvalidReservedWord)
- [ContainerNameMisMatchWithDnsName](#ContainerNameMisMatchWithDnsName)
- [DataNodeDefinitionNotFound](#DataNodeDefinitionNotFound)
- [DeploymentDeletionFailure](#DeploymentDeletionFailure)
- [DnsMappingNotFound](#DnsMappingNotFound)
- [DuplicateClusterContainerRequest](#DuplicateClusterContainerRequest)
- [DuplicateClusterInHostedService](#DuplicateClusterInHostedService)
- [FailureToUpdateDeploymentStatus](#FailureToUpdateDeploymentStatus)
- [HdiRestoreClusterAltered](#HdiRestoreClusterAltered)
- [HeadNodeConfigNotFound](#HeadNodeConfigNotFound)
- [HeadNodeConfigNotFound](#HeadNodeConfigNotFound)
- [HostedServiceCreationFailure](#HostedServiceCreationFailure)
- [HostedServiceHasProductionDeployment](#HostedServiceHasProductionDeployment)
- [HostedServiceNotFound](#HostedServiceNotFound)
- [HostedServiceWithNoDeployment](#HostedServiceWithNoDeployment)
- [InsufficientResourcesCores](#InsufficientResourcesCores)
- [InsufficientResourcesHostedServices](#InsufficientResourcesHostedServices)
- [InternalErrorRetryRequest](#InternalErrorRetryRequest)
- [InvalidAzureStorageLocation](#InvalidAzureStorageLocation)
- [InvalidNodeSizeForDataNode](#InvalidNodeSizeForDataNode)
- [InvalidNodeSizeForHeadNode](#InvalidNodeSizeForHeadNode)
- [InvalidRightsForDeploymentDeletion](#InvalidRightsForDeploymentDeletion)
- [InvalidStorageAccountBlobContainerName](#InvalidStorageAccountBlobContainerName)
- [InvalidStorageAccountConfigurationSecretKey](#InvalidStorageAccountConfigurationSecretKey)
- [InvalidVersionHeaderFormat](#InvalidVersionHeaderFormat)
- [MoreThanOneHeadNode](#MoreThanOneHeadNode)
- [OperationTimedOutRetryRequest](#OperationTimedOutRetryRequest)
- [ParameterNullOrEmpty](#ParameterNullOrEmpty)
- [PreClusterCreationValidationFailure](#PreClusterCreationValidationFailure)
- [RegionCapabilityNotAvailable](#RegionCapabilityNotAvailable)
- [StorageAccountNotColocated](#StorageAccountNotColocated)
- [SubscriptionIdNotActive](#SubscriptionIdNotActive)
- [SubscriptionIdNotFound](#SubscriptionIdNotFound)
- [UnableToResolveDNS](#UnableToResolveDNS)
- [UnableToVerifyLocationOfResource](#UnableToVerifyLocationOfResource)
- [VersionCapabilityNotAvailable](#VersionCapabilityNotAvailable)
- [VersionNotSupported](#VersionNotSupported)
- [VersionNotSupportedInRegion](#VersionNotSupportedInRegion)
- [WasbAccountConfigNotFound](#WasbAccountConfigNotFound)



## <a id="discription-mitigation-errors"></a>錯誤的診斷和緩和


### <a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided
- **描述**: 請提供至少一個元件的 Azure SQL 資料庫的詳細資訊，以便使用 Hive 和 Oozie metastore 的自訂設定。
- **緩和**: 使用者必須提供有效的 SQL Azure metastore，然後重試要求。  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
- **描述**: 無法在區域中建立叢集 *nameOfYourRegion*。 請使用有效的 HDInsight 區域，然後重試要求。
- **緩和**: 客戶應該建立目前支援他們的叢集區域: 東南亞、 西歐、 北歐、 美國東部或美國西部。  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
- **描述**: 伺服器找不到要求的叢集記錄。  
- **緩和**: 重試作業。

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
- **描述**: 叢集 DNS 名稱 *yourDnsName* 不正確。 請確定名稱以英數字元開始和結束，且只包含 '-' 特殊字元  
- **緩和**: 請確定您已經使用有效的 DNS 名稱為您的叢集，開頭和結束是英數字元，且不包含任何特殊字元不是虛線 '-'，然後重試作業。

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
- **描述**: 叢集名稱 *yourClusterName* 無法使用。 請挑選其他名稱。  
- **緩和**: 使用者應該指定唯一的叢集名稱並不存在，再試一次。 如果使用者在使用入口網站，當建立步驟中已使用叢集名稱時，UI 會通知使用者。


### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
- **描述**: 叢集密碼無效。 密碼長度至少必須為 10 個字元，且至少必須包含一個數字、大寫字母、小寫字母和特殊字元，不可有空格，而且不得包含使用者名稱。  
- **緩和**: 提供有效的叢集密碼，然後再次嘗試操作。

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
- **描述**: 叢集使用者名稱無效。 請確定使用者名稱不含特殊字元或空格。  
- **緩和**: 提供有效的叢集使用者名稱，然後再試。

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
- **描述**: 叢集 DNS 名稱 *yourDnsClusterName* 不正確。 請確定名稱以英數字元開始和結束，且只包含 '-' 特殊字元  
- **緩和**: 提供有效的 DNS 叢集使用者名稱，然後再試。

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
- **描述**: URI 中的容器名稱 *yourcontainerURI* 和 DNS 名稱 *yourDnsName* 在要求主體必須是相同。  
- **緩和**: 請確定容器名稱和 DNS 名稱相同，然後重試作業。

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
- **描述**: 叢集組態無效。 在節點大小中找不到任何資料節點定義。  
- **緩和**: 重試作業。

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
- **描述**: 刪除部署叢集失敗。  
- **緩和**: 重試刪除作業。

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
- **描述**: 服務組態錯誤。 找不到必要的 DNS 對應資訊。  
- **緩和**: 刪除叢集，然後建立新的叢集。

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
- **描述**: 叢集嘗試建立重複容器。 存在記錄 *nameOfYourContainer* 但 Etag 不符。
- **緩和**: 提供容器的唯一名稱，然後重試建立作業。

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
- **描述**: 託管服務 *nameOfYourHostedService* 已包含叢集。 代管服務不可包含多個叢集  
- **緩和**: 將叢集裝載於其他代管服務。

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
- **描述**: 伺服器無法更新叢集部署的狀態。  
- **緩和**: 重試作業。 如果此情況發生多次，請連絡 CSS。

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
- **描述**: 叢集 *yourClusterName* 維護已刪除。 請重新建立叢集。
- **緩和**: 重新建立叢集。

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
- **描述**: 叢集組態無效。 在節點大小中找不到必要的前端節點組態。
- **緩和**: 重試作業。

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
- **描述**: 無法建立代管的服務 *nameOfYourHostedService*。 請重試要求。  
- **緩和**: 重試要求。

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
- **描述**: 託管服務 *nameOfYourHostedService* 已有生產部署。 代管服務不可包含多個生產部署 請以不同的叢集名稱來重試要求。
- **緩和**: 使用不同的叢集名稱，然後重試要求。

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
- **描述**: 託管服務 *nameOfYourHostedService* 找不到叢集。  
- **緩和**: 如果叢集處於錯誤狀態時，將它刪除，並再試一次。

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
- **描述**: 託管服務 *nameOfYourHostedService* 沒有相關聯的部署。  
- **緩和**: 如果叢集處於錯誤狀態時，將它刪除，並再試一次。

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
- **描述**: SubscriptionId *yourSubscriptionId* 來建立叢集沒有剩餘的核心 *yourClusterName*。 必要: *resourcesRequired*, 、 可用: *resourcesAvailable*。  
- **緩和**: 釋出訂閱中的資源或增加可供訂閱的資源，然後再試一次建立叢集。

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
- **描述**: 訂用帳戶 ID *yourSubscriptionId* 沒有配額可供新的 HostedService 建立叢集 *yourClusterName*。  
- **緩和**: 釋出訂閱中的資源或增加可供訂閱的資源，然後再試一次建立叢集。

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
- **描述**: 伺服器發生內部錯誤。 請重試要求。  
- **緩和**: 重試要求。

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
- **描述**: Azure 儲存體位置 *dataRegionName* 不是有效的位置。 請確定區域正確，然後重試要求。
- **緩和**: 選取支援 HDInsight 的儲存體位置，檢查您的叢集已並存，然後再試。

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
- **描述**: 資料節點的 VM 大小無效。 所有資料節點僅支援 'Large VM' 大小。  
- **緩和**: 指定支援的節點大小的資料節點，然後再次嘗試操作。

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
- **描述**: 前端節點的 VM 大小無效。 前端節點僅支援 'ExtraLarge VM' 大小。  
- **緩和**: 指定支援的節點大小前端節點，然後再次嘗試操作

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
- **描述**: 訂用帳戶 ID *yourSubscriptionId* 正在使用沒有足夠的權限來執行刪除作業叢集 *yourClusterName*。  
- **緩和**: 如果叢集處於錯誤狀態時，卸除它，並再試一次。  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
- **描述**: 外部儲存帳戶 blob 容器名稱 *yourContainerName* 不正確。 請確定名稱以字母開頭，且只包含小寫字母、數字和虛線。  
- **緩和**: 請指定有效的儲存體帳戶 blob 容器名稱，然後再試。

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
- **描述**: 外部儲存體帳戶組態 *yourStorageAccountName* 需要有密碼詳細資料設定。  
- **緩和**: 指定有效的秘鑰，之儲存體帳戶，然後再試。

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
- **描述**: 版本標頭 *yourVersionHeader* 不是有效格式的 yyyy 公釐。  
- **緩和**: 指定有效的格式版本標頭，然後重試要求。

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
- **描述**: 叢集組態無效。 發現多個前端節點組態。  
- **緩和**: 編輯組態，指定僅一個前端節點。

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
- **描述**: 允許的時間內無法完成作業，或達到最大重試次數。 請重試要求。  
- **緩和**: 重試要求。

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
- **描述**: 參數 *yourParameterName* 不可為 null 或空白。  
- **緩和**: 指定參數的有效值。

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
- **描述**: 一或多個叢集建立要求輸入無效。 請確定輸入值正確，然後重試要求。  
- **緩和**: 請確定輸入的值正確無誤，然後重試要求。

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
- **描述**: 無法使用區域功能 *yourRegionName* 和訂閱 ID *yourSubscriptionId*。  
- **緩和**: 指定支援 HDInsight 叢集的區域。 公開支援的區域為：東南亞、西歐、北歐、美國東部或美國西部。

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
- **描述**: 儲存體帳戶 *yourStorageAccountName* 區域中 *currentRegionName*。 它應該是相同的叢集區域 *yourClusterRegionName*。  
- **緩和**: 您的叢集是在相同區域中指定的儲存體帳戶或是如果您的資料已儲存體帳戶中，建立新的叢集與現有的儲存體帳戶位於相同區域中。 如果您在使用入口網站，UI 會事先通知此問題。

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
- **描述**: 給定的訂閱識別碼 *yourSubscriptionId* 不在使用中。  
- **緩和**: 重新啟動您的訂閱或取得新的有效訂用帳戶。

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
- **描述**: 訂用帳戶 ID *yourSubscriptionId* 找不到。  
- **緩和**: 請檢查您的訂閱識別碼有效，然後再試。

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
- **描述**: 無法解析 DNS *yourDnsUrl*。 請確定您提供 Blob 端點的完整 URL。  
- **緩和**: 提供有效的 blob URL。 URL 必須是完全有效，包括開頭 *http://* 中和結束 *.com*。

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
- **描述**: 無法驗證資源的位置 *yourDnsUrl*。 請確定您提供 Blob 端點的完整 URL。  
- **緩和**: 提供有效的 blob URL。 URL 必須是完全有效，包括開頭 *http://* 中和結束 *.com*。

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
- **描述**: 無法使用版本功能 *specifiedVersion* 和訂閱 ID *yourSubscriptionId*。  
- **緩和**: 選擇可用的版本，然後再次嘗試操作。

### <a id="VersionNotSupported"></a>VersionNotSupported
- **描述**: 版本 *specifiedVersion* 不支援。
- **緩和**: 選擇支援的版本，然後再次嘗試操作。

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
- **描述**: 版本 *specifiedVersion* Azure 區域中沒有 *Specifiedversion*。  
- **緩和**: 選擇指定區域中支援的版本，然後再次嘗試操作。

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
- **描述**: 叢集組態無效。 在外部帳戶中找不到必要的 WASB 帳戶組態。  
- **緩和**: 確認帳戶是否存在且於組態中指定正確，然後再試。

## <a id="resources"></a>其他偵錯資源

* [Azure HDInsight SDK 文件][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[image-hdi-debugging-error-messages-portal]: ./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png

