<properties
    pageTitle="部署大型部署"
    description="了解如何運用適用於 Eclipse 的 Azure 工具組部署大型部署。"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/30/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268601.aspx -->

# 部署大型部署 #

如果您的部署太大，預設的 approot 資料夾無法容納時，可以使用本機儲存資源，作為 JDK 和應用程式伺服器的部署根資料夾。

## 使用本機儲存資源作為大型部署的部署根資料夾 ##

1. 建立新的本機儲存資源。 資源名稱不重要。 儲存資源以角色層級定義。 若要存取本機儲存體組態對話方塊中，您可以從中建立新的本機儲存體資源，最快速的方法是使用下列步驟: 在角色上按一下滑鼠右鍵 **專案總管] 中** 檢視 (依序展開您的 Azure 專案節點如果您沒有看到角色)，按一下 **Azure**, ，然後按一下 [ **本機儲存體**。 內 **本機儲存體** ] 對話方塊中，按一下 [ **新增** 來建立新的本機儲存體資源。
1. 將所需的儲存容量設為至少 2048 MB (若小於此設定值，可能會發生 approot 中相同的容量問題)。
1. 請確認 **回收角色執行個體時清除內容** 選取的; 這將有助於避免部署的啟動邏輯回收角色執行個體時，發生的衝突的資源中已存在的檔案。
1. 請確認 **儲存資源的目錄路徑部署後的環境變數** 值將設為字串 **\{1\&GT;DEPLOYROOT**。 您的本機儲存體資源對話方塊看起來如下所示。
    ![][ic667943]

或者，如果您使用 **\{1\&GT;DEPLOYROOT** 為 *名稱* 您的本機資源，並且不會變更自動產生的環境變數名稱 (這會設定為 **DEPLOYROOT_PATH** 這種情況下)，也將適用於您應用程式。

建立本機儲存體資源的其他資訊，請參閱 [本機儲存體屬性][]。

## 另請參閱 ##

[適用於 Eclipse 的 Azure 工具組][]

[在 Eclipse 中建立適用於 Azure 的 Hello World 應用程式][]

[安裝 Azure Toolkit for Eclipse][] 

如需有關如何使用 java 的 Azure 的詳細資訊，請參閱 [Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Local storage properties]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties

<!-- IMG List -->

[ic667943]: ./media/azure-toolkit-for-eclipse-deploying-large-deployments/ic667943.png

