<properties
    pageTitle="開始使用以 Java 撰寫的事件中樞 | Microsoft Azure"
    description="遵循此教學課程，以開始使用 Azure 事件中樞；透過 Java 傳送事件並使用 EventProcessorHost 以 C# 接收事件。"
    services="event-hubs"
    documentationCenter=""
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="core"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/05/2015"
    ms.author="sethm"/>

# 開始使用事件中心

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## 簡介

事件中心是可高度擴充的擷取系統，每秒可以吸收數以百萬計的事件，讓應用程式能處理和分析已連線裝置和應用程式所產生的大量資料。 收集到事件中樞後，您可以使用任何即時分析提供者或儲存體叢集轉換和儲存資料。

如需詳細資訊，請參閱 [事件中樞概觀][]。

本教學課程示範如何將訊息擷取到事件中心使用主控台應用程式在 Java 中，以及使用 C# 的平行擷取它們 [事件處理器主機][] 程式庫。

若要完成本教學課程，您需要下列項目：

+ Java 開發環境。 本教學課程中，我們將假設 [Eclipse](https://www.eclipse.org/)。

+ Microsoft Visual Studio Express for Windows

+ 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

## 建立事件中心

1. 登入 [Azure 傳統入口網站][], ，然後按一下 **新增** 螢幕的底部。

2. 按一下 [ **應用程式服務**, ，然後 **服務匯流排**, ，然後 **事件中心**, ，然後 **快速建立**。

    ![][1]

3. 輸入事件中樞的名稱，選取您所需的區域，然後按一下 **建立新的事件中樞**。

    ![][2]

4. 按一下您剛才建立的命名空間 (通常是 ***事件中樞名稱*ns**)。

    ![][3]

5. 按一下 [ **事件中樞** 標籤頁面的頂端，然後按一下您剛才建立的事件中心。

    ![][4]

6. 按一下 [ **設定** 頁面頂端的索引標籤中，新增名為 **SendRule** 與 *傳送* 權限，並新增另一個規則呼叫 **ReceiveRule** 與 *管理、 傳送、 接聽* 權限，然後再按一下 **儲存**。

    ![][5]

7. 在相同頁面上，記下個產生的金鑰，如 **SendRule**。

    ![][6b]

8. 按一下 [ **儀表板** 在頁面頂端的標籤，然後按一下 **連接資訊**。 記下這兩個連接字串。

    ![][6]

現已建立事件中心，並具有傳送與接收事件所需的連接字串。

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1.  執行 **接收者** 專案從 Visual Studio 中，然後等它啟動所有資料分割的接收器。

    ![][21]

2.  執行 **寄件者** 專案，請按 **Enter** 在主控台視窗，並查看接收器視窗中出現的事件。

    ![][22]

## 後續步驟

您已經建置工作應用程式，可建立「事件中樞」和傳送及接收資料，接下來可進行下列案例：

- 完整 [範例會使用事件中心的應用程式][]。
-  [相應放大事件處理與事件中樞][] 範例。
- A [佇列訊息解決方案][] 使用服務匯流排佇列。

如需詳細資訊，請參閱 [Java 開發人員中心](/develop/java/)。

<!-- Images. -->
[1]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub1.png
[2]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub2.png
[3]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub3.png
[4]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub4.png
[5]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub5.png
[6]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub6.png
[6b]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub6b.png


[21]: ./media/event-hubs-java-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-java-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[queued messaging solution]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

