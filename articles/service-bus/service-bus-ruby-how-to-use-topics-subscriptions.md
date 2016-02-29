<properties
    pageTitle="如何使用服務匯流排主題 (Ruby) | Microsoft Azure"
    description="了解如何在 Azure 使用服務匯流排主題及訂用帳戶。 程式碼範例專為 Ruby 應用程式撰寫。"
    services="service-bus"
    documentationCenter="ruby"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="12/09/2015"
    ms.author="sethm"/>

# 如何使用服務匯流排主題/訂閱

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本指南說明如何從 Ruby 應用程式使用服務匯流排主題和訂用帳戶。 涵蓋的案例包括 **建立主題和訂閱、 建立訂閱篩選器、 傳送訊息** 至主題、 **從訂閱接收訊息**, ，和 **刪除主題和訂閱**。 如需主題和訂閱的詳細資訊，請參閱 [下一步](#next-steps) 一節。

## 服務匯流排主題和訂用帳戶

服務匯流排主題和訂閱支援 **發佈/訂閱
訊息通訊** 模型。 當使用主題和訂閱，
分散式應用程式的元件不會直接的通訊
彼此，而是透過做為主題的訊息
媒介。

![主題概念](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

相較於服務匯流排佇列，其中每個訊息只由處理
單一取用者、 主題和訂閱提供 **-一對多** 表單
通訊使用的發佈/訂閱模式。 可以
登錄多個訂閱的主題。 當訊息傳送至
主題中，它可供每個訂閱處理
獨立。

主題訂閱類似於虛擬佇列，可接收的複本
已傳送至主題的訊息。 您可以選擇登錄
主題，可讓您以每個訂閱為基礎的篩選規則
若要篩選/限制主題的哪些訊息接收哪些主題
訂閱。

服務匯流排主題和訂閱可讓您擴大處理程序
非常大量的大量使用者的訊息和
應用程式。

## 建立服務命名空間

若要開始在 Azure 中使用服務匯流排佇列，首先必須建立服務命名空間。 命名空間提供範圍容器內定址服務匯流排資源
匯流排資源。 您必須建立的命名空間，透過命令列介面，因為 [Azure 傳統入口網站][] 不會使用 ACS 連線建立命名空間。

若要建立命名空間：

1. 開啟 Azure Powershell 主控台。

2. 輸入命令以建立命名空間，如下所示。 提供您自己的命名空間值，並指定與您的應用程式相同的區域。

      New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

      ![建立命名空間](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## 取得命名空間的預設管理認證

若要執行管理作業，例如建立新的佇列
命名空間，您必須取得命名空間的管理認證。

您執行以建立服務匯流排命名空間的 PowerShell 指令程式會顯示
索引鍵，可用來管理命名空間。 複製 **DefaultKey** 值。 您
將您的程式碼中使用此值稍後在本教學課程。

      ![Copy key](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

> [AZURE.NOTE]
> 您也可以找到此機碼，如果您登入
> [Azure 傳統入口網站][] 並瀏覽至
> 您的命名空間的連接資訊。

## 建立 Ruby 應用程式

如需指示，請參閱 [Azure 上建立 Ruby 應用程式](/develop/ruby/tutorials/web-app-with-linux-vm/)。

## 設定應用程式以使用服務匯流排

若要使用 Azure 服務匯流排，請下載並使用 Ruby Azure 套件，其中包含一組能與儲存體 REST 服務通訊的便利程式庫。

### 使用 RubyGems 來取得套件

1. 使用命令列介面，例如 **PowerShell** (Windows)、 **終端機** (Mac) 或 **Bash** (Unix)。

2. 在命令視窗中鍵入 "gem install azure" 以安裝 Gem 和相依性。

### 匯入封裝

使用您偏好的文字編輯器，將以下內容加入至您打算使用儲存體的 Ruby 檔案頂端：

    require "azure"

## 設定服務匯流排連接

Azure 模組會讀取環境變數 **AZURE\_SERVICEBUS\_NAMESPACE** 和 **AZURE\_SERVICEBUS\_ACCESS\_KEY**
連接到您的命名空間所需的資訊。 如果未設定這些環境變數，您必須指定命名空間資訊之前使用 **azure:: servicebusservice** 為下列程式碼:

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

將命名空間值設為您建立的值而非整個 URL。 例如，使用 **"yourexamplenamespace"**, ，非"yourexamplenamespace.servicebus.windows.net"。

## 建立主題

 **Azure:: servicebusservice** 物件可讓您處理主題。 下列程式碼會建立 **azure:: servicebusservice** 物件。 若要建立主題，請使用 **create\_topic ()** 方法。 下列範例將建立主題或列印出錯誤 (若有的話)。

    azure_service_bus_service = Azure::ServiceBusService.new
    begin
      topic = azure_service_bus_service.create_queue("test-topic")
    rescue
      puts $!
    end

您也可以傳遞 **Azure::ServiceBus::Topic** 物件與其他選項，可讓您覆寫訊息時間即時或佇列大小上限等預設主題設定。 下列範例說明將佇列大小上限設為 5GB，將存留時間設為 1 分鐘的設定：

    topic = Azure::ServiceBus::Topic.new("test-topic")
    topic.max_size_in_megabytes = 5120
    topic.default_message_time_to_live = "PT1M"

    topic = azure_service_bus_service.create_topic(topic)

## 建立訂用帳戶

也會建立主題訂閱 **azure:: servicebusservice** 物件。 訂用帳戶是具名的，它們能擁有選用的篩選器，以限制傳遞至訂用帳戶之虛擬佇列的訊息集合。

訂閱是持續性的，它們會持續存在，直到本身或相關的主題遭到刪除為止。 如果應用程式含有建立訂閱的邏輯，它應該會先使用 getSubscription 方法檢查訂閱是否存在。

### 使用預設 (MatchAll) 篩選器建立訂用帳戶

 **MatchAll** 篩選器是預設篩選器，如果沒有指定篩選時建立新的訂閱。 當 **MatchAll** 篩選器時，發佈至主題的所有訊息都會被都置於訂閱的虛擬佇列。 下列範例會建立名為"all-messages"的訂閱，並使用預設 **MatchAll** 篩選器。

    subscription = azure_service_bus_service.create_subscription("test-topic",
      "all-messages")

### 使用篩選器建立訂用帳戶

您也可以定義篩選器，讓您指定傳送至主題的哪些訊息應顯示在特定訂用帳戶中。

訂閱所支援的篩選器的最具彈性的型別是 **Azure::ServiceBus::SqlFilter**, ，可用來實作 SQL92 的子集。 SQL 篩選器會對發佈至主題之訊息的屬性運作。 如需可與 SQL 篩選運算式的詳細資訊，請檢閱 [SqlFilter.SqlExpression](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx) 語法。

可以透過將篩選器新增至訂閱 **create\_rule ()** 方法 **azure:: servicebusservice** 物件。 此方法可讓您將篩選器新增至現有的訂用帳戶中。

由於預設篩選器會自動套用至所有新訂閱，您必須先移除預設篩選器，或 **MatchAll** 會覆寫您指定的其他任何篩選器。 您可以使用，以移除預設規則 **delete\_rule ()** 方法 **azure:: servicebusservice** 物件。

下列範例會建立名為 「 high-messages 」 的訂閱 **Azure::ServiceBus::SqlFilter** 只選取自訂的訊息 **message\_number** 屬性大於 3:

    subscription = azure_service_bus_service.create_subscription("test-topic",
      "high-messages")
    azure_service_bus_service.delete_rule("test-topic", "high-messages",
      "$Default")

    rule = Azure::ServiceBus::Rule.new("high-messages-rule")
    rule.topic = "test-topic"
    rule.subscription = "high-messages"
    rule.filter = Azure::ServiceBus::SqlFilter.new({
      :sql_expression => "message_number > 3" })
    rule = azure_service_bus_service.create_rule(rule)

同樣地，下列範例會建立名為 「 low-messages 」 訂閱 **Azure::ServiceBus::SqlFilter** sqlfilter 只選取 **message_number** 屬性小於或等於 3:

    subscription = azure_service_bus_service.create_subscription("test-topic",
      "low-messages")
    azure_service_bus_service.delete_rule("test-topic", "low-messages",
      "$Default")

    rule = Azure::ServiceBus::Rule.new("low-messages-rule")
    rule.topic = "test-topic"
    rule.subscription = "low-messages"
    rule.filter = Azure::ServiceBus::SqlFilter.new({
      :sql_expression => "message_number <= 3" })
    rule = azure_service_bus_service.create_rule(rule)

現在，當訊息傳送至 "test-topic" 時，一律會將該訊息傳遞至已訂閱 "all-messages" 主題訂用帳戶的接收者，並選擇性地傳遞至已訂閱 "high-messages" 和 "low-messages" 主題訂用帳戶的接收者 (視訊息內容而定)。

## 傳送訊息至主題

若要將訊息傳送至服務匯流排主題，您的應用程式必須使用 **send\_topic\_message ()** 方法 **azure:: servicebusservice** 物件。 傳送至服務匯流排主題的訊息是 **brokeredmessage** 物件。 **Brokeredmessage** 物件具有一組標準屬性 (例如 **標籤** 和 **time\_to\_live**)、 用來保存自訂應用程式特定屬性的字典和一堆字串資料。 應用程式可以藉由傳遞字串值，即可設定訊息的本文 **send\_topic\_message ()** 方法和任何所需的標準屬性會填入預設值。

下列範例說明如何將五個測試訊息傳送至 "test-topic"。 請注意， **message_number** 迴圈的反覆項目上的每個訊息的自訂屬性值變化 (這可判斷哪些訂閱會接收它):

    5.times do |i|
      message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
        { :message_number => i })
      azure_service_bus_service.send_topic_message("test-topic", message)
    end

服務匯流排主題支援 256 Kb 的訊息大小上限 (包含標準和自訂應用程式屬性的標頭可以容納 64 Kb 的大小上限)。 主題中所保存的訊息數目沒有限制，但主題所保存的訊息大小總計會有最高限制。 此主題大小會在建立時定義，上限是 5 GB。

## 自訂閱接收訊息

從訂閱接收訊息 **receive\_subscription\_message ()** 方法 **azure:: servicebusservice** 物件。 根據預設，在讀取 (查看) 及鎖定訊息後並不會將其從訂閱中刪除。 您可以讀取，並從訂閱刪除訊息，藉由設定 **peek\_lock** 選項 **false**。

預設行為會使讀取和刪除變成兩階段作業，因此也可以支援無法容許遺漏訊息的應用程式。 當服務匯流排收到要求時，它會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。 藉由呼叫應用程式完成處理訊息 (或可靠地儲存供未來處理) 之後，完成接收程序的第二個階段 **delete\_subscription\_message ()** 方法，並提供要刪除做為參數的訊息。  **Delete\_subscription\_message ()** 方法會標示為已取用的訊息，並將它從訂閱移除。

如果 **: peek\_lock** 參數設為 **false**, ，讀取和刪除訊息將會變成最簡單的模型，且最適用於應用程式容許在不處理訊息發生失敗時的案例。 若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。 因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

下列範例將示範收到訊息的方式與處理使用 **receive\_subscription\_message ()**。 範例首先會接收並刪除來自 「 low-messages 」 訂閱的訊息，使用 **: peek\_lock** 設 **false**, ，然後再接收另一個來自 「 high-messages 」 的訊息，然後刪除訊息使用 **delete\_subscription\_message ()**:

    message = azure_service_bus_service.receive_subscription_message(
      "test-topic", "low-messages", { :peek_lock => false })
    message = azure_service_bus_service.receive_subscription_message(
      "test-topic", "high-messages")
    azure_service_bus_service.delete_subscription_message(message)

## 處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。 如果接收者應用程式無法處理訊息，因為某種原因，則它可以呼叫 **unlock\_subscription\_message ()** 方法 **azure:: servicebusservice** 物件。 這導致服務匯流排將訂用帳戶中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與訂用帳戶內鎖定訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

該應用程式當機之前處理訊息之後 **delete\_subscription\_message ()** 呼叫方法，然後重新啟動時，訊息會傳遞給應用程式。 這通常稱為 **至少處理一次**; 也就是說，每個訊息會至少一次處理，但在某些情況下可能會重新傳遞相同訊息。 如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。 此邏輯通常您可使用 **message\_id** 會保持不變各個傳遞嘗試的訊息屬性。

## 刪除主題和訂用帳戶

主題和訂閱是持續性的必須明確地刪除透過 [Azure 傳統入口網站](https://manage.windowsazure.com) 或以程式設計的方式。 下列範例說明如何刪除名為 "test-topic" 的主題：

    azure_service_bus_service.delete_topic("test-topic")

刪除主題也將會刪除對主題註冊的任何訂用帳戶。 您也可以個別刪除訂用帳戶。 下列程式碼將示範如何從 "test-topic" 主題中刪除名為 "high-messages" 的訂閱：

    azure_service_bus_service.delete_subscription("test-topic", "high-messages")

## 後續步驟

了解基本的服務匯流排主題之後，請參考下列連結以取得更多資訊。

-   請參閱 [佇列、 主題和訂閱](service-bus-queues-topics-subscriptions.md)。
-   API 參考 [SqlFilter](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx)。
-   請瀏覽 [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) GitHub 上的儲存機制。
 
[Azure classic portal]: http://manage.windowsazure.com

