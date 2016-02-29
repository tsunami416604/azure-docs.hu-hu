<properties 
   pageTitle="雲端服務角色回收的常見原因 |Microsoft Azure"
   description="突然回收的雲端服務角色可能會造成顯著的停機時間。 以下是導致角色回收的一些常見問題，或許有助於縮短停機時間。"
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="msmets"
   editor=""
   tags="top-support-issue"/>
<tags 
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/14/2015"
   ms.author="daleche" />

# 導致角色回收的常見問題

在此提供部署問題的常見原因，和可協助您解決這些問題的疑難排解秘訣。 表示應用程式出現問題時，角色執行個體無法啟動，或之間循環 **初始化**, ，**忙碌**, ，和 **停止** 狀態。

## 連絡 Azure 客戶支援

如果您需要更多協助，在本文中的任何時間點，您可以與 Azure 專家 [MSDN Azure 和堆疊溢位論壇](http://azure.microsoft.com/support/forums/)。

或者，您也可以提出 Azure 支援事件。 移至 [Azure 支援網站](http://azure.microsoft.com/support/options/) ，然後按一下 **取得支援**。 使用 Azure 支援的相關資訊，請參閱 [Microsoft Azure 支援常見問題集](http://azure.microsoft.com/support/faq/)。


## 遺失執行階段相依性

如果應用程式中的角色依賴任何不屬於 .NET Framework 或 Azure Managed 程式庫的組件，您必須明確地在應用程式封裝中包含該組件。 請記住，其他 Microsoft 架構依預設並未在 Azure 上提供。 如果您的角色依賴這類架構，您必須將這些組件新增至應用程式封裝。

在您建置及封裝應用程式之前，請確認下列項目：

- 如果使用 Visual studio，請確定 **複製到本機** 屬性設定為 **True** 每個參考組件不是 Azure SDK 或.NET Framework 的一部分，會在專案中。

- 請確定 **web.config** 檔案未參考任何未使用的組件中 **編譯** 項目。
 
-  **建置動作** 的每個.cshtml 檔案設定為 **內容**。 這可確保檔案會正確出現在封裝中，並且讓其他參考的檔案出現在封裝中。



## 組件以錯誤的平台作為目標

Azure 是 64 位元環境。 因此，針對 32 位元目標編譯的 .NET 組件無法在 Azure 上運作。



## 角色在初始化或停止時會擲回未處理的例外狀況

方法會擲回任何例外狀況 [RoleEntryPoint] 類別，其中包括 [OnStart], ，[OnStop], ，和 [Run], ，是未處理的例外狀況。 如果其中有一個方法發生未處理的例外狀況，角色就會進行回收。 如果角色重複回收，它可能會在每次嘗試啟動時擲回未處理的例外狀況。


## 角色因 Run 方法而回收

 [Run] 方法要無限期地執行。 如果您的程式碼會覆寫 [Run] 方法，它應該睡眠無限期。 如果 [Run] 方法傳回時，在角色回收。




## 不正確的 DiagnosticsConnectionString 設定

如果應用程式使用 Azure 診斷，則您的服務組態檔必須指定 `DiagnosticsConnectionString` 組態設定。 此設定應指定您在 Azure 中的儲存體帳戶的 HTTPS 連線。

為了在您將應用程式封裝部署至 Azure 之前確保 `DiagnosticsConnectionString` 設定正確無誤，請確認下列事項：  

- `DiagnosticsConnectionString` 設定指向 Azure 中的有效儲存體帳戶。  
  根據預設，此設定會指向模擬儲存體帳戶，因此您必須在部署應用程式封裝之前明確變更這項設定。 若未變更此設定，當角色執行個體嘗試啟動診斷監視器時，將會擲回例外狀況。 這可能會導致角色執行個體無限期地回收。
  
- 在下列指定的連接字串 [格式](storage-configure-connection-string.md) (通訊協定必須指定為 HTTPS)。 取代 *MyAccountName* 的儲存體帳戶名稱和 *MyAccountKey* 與便捷鍵:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  如果您正在開發使用 Azure Tools for Microsoft Visual Studio 應用程式，您可以使用 [屬性頁](https://msdn.microsoft.com/library/ee405486) 來設定此值。 



## 匯出的憑證未包含私密金鑰

若要在 SSL 下執行 Web 角色，您必須確保匯出的管理憑證包含私密金鑰。 如果您使用 *Windows 憑證管理員* 匯出的憑證，請務必選取 *是*, ，匯出私密金鑰] 選項。 憑證必須匯出為 PFX 格式，這是目前唯一支援的格式。



## 後續步驟

檢視更多 [疑難排解文章](..\?tag=top-support-issue&service=cloud-services) 雲端服務。




[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Run]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
