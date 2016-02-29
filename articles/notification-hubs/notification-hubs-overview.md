<properties
    pageTitle="Azure 通知中心"
    description="了解如何在 Azure 中使用推播通知。 程式碼範例是以 C# 撰寫並使用 .NET API。"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="notification-hubs"
    documentationCenter=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="12/10/2015"
    ms.author="wesmc"/>


#Azure 通知中心

##概觀

Azure 通知中樞提供一種易用、多平台、向外延展的推播基礎結構，可讓您將行動推播通知從任何後端 (在雲端或內部部署) 傳送到任何行動平台。

使用通知中樞，您可以輕鬆地傳送跨平台個人化推播通知，並擷取不同平台通知系統 (PNS) 的詳細資料。 使用單一 API 呼叫，您可以跨所有裝置將目標設為個別使用者或整個對象區段 (包含數百萬個使用者)。

在企業和消費者案例中，您可以使用通知中樞。 例如：

- 將即時新聞通知傳送給數百萬名使用者 (通知中樞強力支援所有 Windows 和 Windows Phone 裝置上已預先安裝的 Bing 應用程式)，且延遲性低。
- 將位置型折價券傳送至使用者區段。
- 將事件通知傳送給運動/財金/遊戲應用程式使用者或群組。
- 通知使用者有關企業事件 (例如新訊息/電子郵件) 和潛在業務機會。
- 傳送 Multi-Factor Authentication 所需的一次性密碼。



##什麼是推播通知？

智慧型手機和平板電腦都具有在事件發生時「通知」使用者的功能。 這些通知可以有許多形式。

在 Windows 市集和 Windows Phone 應用程式中，通知可以是格式 _快顯_: 非強制回應視窗隨即出現，而且聲音，用來指出新的通知。 其他通知類型的支援包括 _磚_, ，_原始_, ，和 _徽章_ 通知。 如需有關 Windows 裝置，請參閱 < 支援的通知類型的 [磚、 徽章和通知](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)。

在 Apple iOS 裝置上，推播同樣會以對話方塊的形式通知使用者，而要求使用者檢視或關閉通知。 按一下 **檢視** 開啟接收訊息的應用程式。 如需 iOS 通知，請參閱 [iOS 通知](http://go.microsoft.com/fwlink/?LinkId=615245)。

推播通知有助於行動裝置在保有足夠資源的情況下顯示最新資訊。 後端系統可以將通知傳送給行動裝置，即使裝置上的對應應用程式未作用也是一樣。 推播通知是消費者應用程式的重要元件，可用來增加應用程式的應用面和使用性。 通知對企業也有幫助，因為掌握最新資訊的員工對於商務活動更能處理得當。

行動應用案例的特定範例包括：

1.  以目前的財務資訊更新 Windows 8 或 Windows Phone 上的磚。
2.  在以工作流程為基礎的企業應用程式中，透過已將某個工作項目指派給使用者的快顯通知，對該名使用者發出警示。
3.  在 CRM 應用程式 (例如 Microsoft Dynamics CRM) 中，顯示具有目前潛在客戶數目的徽章。

##推播通知的運作方式

推播通知透過呼叫的平台特定基礎結構傳送 _平台通知系統_ (PNS)。 PNS 可提供準系統功能 (即不支援廣播和個人化的系統)，不具共同介面。 例如，若要傳送通知給 Windows 市集應用程式，開發人員必須連絡 WNS (Windows 通知服務) 以將通知傳送至 iOS 裝置，且必須連絡 APNS (Apple 推播通知服務)，而再次傳送訊息。 Azure 通知中樞提供通用介面以及其他功能，以協助支援跨每個平台的推播通知。

但綜觀而言，所有平台通知系統都依循相同的模式：

1.  用戶端應用程式會連絡 PNS 以擷取其 _處理_。 控制代碼類型視系統而定。 WNS 的控制代碼類型為 URI 或「通知通道」。 APNS 的控制代碼則為權杖。
2.  用戶端應用程式會將此控制代碼儲存在應用程式 _後端_ 供後續使用。 WNS 的後端通常是雲端服務。 Apple，系統就稱為 _提供者_。
3.  若要傳送推播通知，應用程式後端必須使用控制代碼連絡 PNS，以將特定的用戶端應用程式執行個體訂為目標。
4.  PNS 會將通知轉送至控制代碼所指定的裝置。

![][0]

##推播通知的挑戰

儘管這些系統功能非常強大，但即便 App 開發人員想要實作一般的推播通知工作 (例如廣播或傳送推播通知給分段式使用者)，都有許多工作有待處理。

就行動應用程式而言，推播通知是雲端服務中最常被要求的功能之一。 原因是，其運作所需的基礎結構頗為複雜，且大部分與應用程式的主要商業邏輯沒有關聯。 建置隨選推播基礎結構的挑戰包括：

- **平台相依性。**若要將通知傳送至不同平台上的裝置，必須在後端為多個介面進行編碼。 除了低階細節有所差異外，通知的呈現方式 (磚、快顯通知或徽章) 也會隨平台而不同。 這些差異可能會致使後端程式碼複雜且難以維護。

- **小數位數。**此基礎結構的調整可分為兩個層面：
    + 根據 PNS 準則，在每次啟動應用程式時，都必須重新整理裝置權杖。 在此情況下，光是為了保有最新的裝置權杖，就會產生大量的流量 (和隨之而來的資料庫存取)。 當裝置數目增加時 (可能達百萬以上)，建立及維護此基礎結構的成本將會很可觀。

    + 大部分的 PNS 並不支援廣播至多個裝置。 因此，若要廣播至數百萬個裝置，就必須對 PNS 呼叫數百萬次。 要能夠調整這些要求可不是容易的事，因為應用程式開發人員通常都會想要維持較低的整體延遲性 (例如，最後一個接收訊息的裝置，不應在通知送出的 30 分鐘後才收到通知，因為這樣很可能就失去推播通知的意義了)。
- **路由。**PNS 提供將訊息傳送至裝置的途徑。 但大部分的應用程式通知都會以使用者和 (或) 相關群組 (例如，所有指派至特定客戶帳戶的員工) 為對象。 因此，若要將通知遞送至正確的裝置，應用程式後端必須保有為相關群組與裝置權杖建立關聯的登錄。 這項額外工作將導致應用程式的上市時程延宕和維護成本提高。

##為何要使用通知中心？

通知中樞可消除複雜性：您不需要管理推播通知的挑戰。 而是，您可以使用通知中樞。 通知中樞採用完整的多平台、向外延展的推播通知基礎結構，可大幅減少應用程式後端所執行的推播專用程式碼。 通知中樞可實作推播基礎結構的所有功能。 裝置只需註冊其 PNS 控制代碼即可，後端會負責將平台共用格式訊息傳送給使用者或相關群組 (如下圖所示)：

![][1]


通知中樞提供具有下列優點且容易使用的推播通知基礎結構：

- **多個平台。**
    +  支援所有主要行動平台。 通知中心可傳送推播通知至 Windows 市集、iOS、Android 和 Windows Phone 應用程式。

    +  通知中心提供可將通知傳送至所有支援平台的共同介面。 不需要平台特定通訊協定。 應用程式後端可傳送平台專用格式的通知，或平台共用格式的通知。 應用程式只會與通知中樞通訊。

    +  裝置控制代碼管理。 通知中樞會維護控制代碼登錄以及來自 PNS 的意見。

- **可搭配任何後端**: 雲端或內部，.NET、 PHP、 Java、 節點等等。

- **小數位數。**通知中心可因應數百萬個裝置的需求，而無需重新架構或分區。


- **豐富的傳遞模式集**:

    - *廣播*: 可讓幾乎同時廣播到數百萬個裝置透過單一 API 呼叫。

    - *單點傳播/多點傳播*: 推播至標記代表個別使用者，包括其所有裝置; 或更大的群組; 例如，個別表單係數 (平板電腦與電話)。

    - *分割*: 推播至標記運算式 (例如，紐約追蹤洋基的裝置) 所定義的複雜區段。

    每個裝置時將其控制代碼傳送至通知中心，可以指定一或多個 _標記_。 如需詳細資訊 [tags]。 標籤不一定要預先佈建或處置。 透過標籤，通知將可輕易傳送給使用者或相關群組。 由於標籤可包含任何應用程式專用的識別碼 (例如使用者或群組 ID)，因此使用標籤後，應用程式後端將可省卻儲存及管理裝置控制代碼的工作負擔。

- **個人化**: 每個裝置可以有一或多個範本，以達成每一裝置的當地語系化和個人化，而不會影響後端程式碼。

- **安全性**: 共用存取密碼 (SAS) 或同盟的驗證。

- **豐富遙測**: 可在網站中，以程式設計的方式。


##與 App Service Mobile Apps 整合

完美 facililate 和 Azure 服務間統一經驗 [App Service Mobile Apps] 的內建支援使用通知中心的推播通知。 [App Service Mobile Apps] 企業開發人員和系統整合者可將一組豐富的功能帶入行動開發人員提供高度擴充、 全域使用的行動應用程式開發平台。

Mobile Apps 開發人員可以使用下列流程來利用通知中樞：

1. 擷取裝置 PNS 控制代碼
2. 註冊的裝置和 [templates] 使用通知中心，透過方便行動應用程式用戶端 SDK 註冊 API
    + 請注意，Mobile Apps 會基於安全性考量，去除註冊上的所有標籤。 直接從後端使用通知中樞，將標籤關聯至裝置。
3. 從 App 後端使用通知中樞傳送通知

以下是一些便利帶到這項整合的開發人員:
- **行動應用程式用戶端 Sdk。**這些多平台 SDK 提供簡單的 API 來進行註冊，然後會自動與連結到行動 App 的通知中樞聯繫。 開發人員不需要仔細通知中心認證，並使用其他服務。
    + Sdk 會自動標記為給定的裝置，以啟用使用者案例的推播行動應用程式已驗證使用者識別碼。
    + Sdk 會自動使用行動應用程式的安裝識別碼做為 GUID 註冊與通知中心，節省開發人員維護多個服務 Guid 的麻煩。
- **安裝模式。**行動應用程式搭配使用通知中心的最新推入模型來代表在 JSON 安裝中的推播通知服務與對齊且容易使用的裝置相關聯的所有推入內容。
- **彈性。**開發人員永遠都可以選擇使用通知中心直接甚至就地整合。
- **整合式體驗中的 [Azure Portal]。**Mobile Apps 中會以視覺化方式呈現以功能形式出現的推送，而開發人員可以透過 Mobile Apps 輕鬆使用相關聯的通知中樞。



##後續步驟

您可以在以下主題找到「通知中樞」的詳細資訊：

+ **[客戶如何使用通知中樞]**

+ **[通知中樞教學課程和指南]**

+ **通知中樞快速入門教學課程** ([iOS], ，[Android], ，[Windows Universal], ，[Windows Phone], ，[Kindle], ，[Xamarin.iOS], ，[Xamarin.Android])

針對推播通知，相關的 .NET 受管理 API 參考位於此處：

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [How customers are using Notification Hubs]: http://azure.microsoft.com/services/notification-hubs
  [Notification Hubs tutorials and guides]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [App Service Mobile Apps]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
  [templates]: notification-hubs-templates.md
  [Azure Portal]: https://portal.azure.com
  [tags]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)

