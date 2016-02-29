<properties 
    pageTitle="如何搭配使用服務匯流排主題與 PHP | Microsoft Azure" 
    description="了解如何在 Azure 對於 PHP 使用服務匯流排主題。" 
    services="service-bus" 
    documentationCenter="php" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/06/2015" 
    ms.author="sethm"/>


# 如何使用服務匯流排主題和訂用帳戶

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本文示範如何使用服務匯流排主題和訂用帳戶。 在 PHP 中並使用這些範例 [Azure SDK for PHP](../php-download-sdk.md)。 涵蓋的案例包括 **建立主題和訂閱**, ，**建立訂閱篩選器**, ，**傳送訊息至主題**, ，**從訂閱接收訊息**, ，和 **刪除主題和訂閱**。

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## 建立 PHP 應用程式

唯一的需求建立 PHP 應用程式，使其存取 Azure Blob 服務是以參考中的類別 [Azure SDK for PHP](../php-download-sdk.md) 從您的程式碼。 您可以使用任何開發工具來建立應用程式，或記事本。

> [AZURE.NOTE] 您的 PHP 安裝也必須 [OpenSSL 延伸](http://php.net/openssl) 安裝並啟用。

本文說明如何使用可從 PHP 應用程式內本機呼叫的服務功能，或可在 Azure Web 角色、背景工作角色或網站內執行的程式碼中呼叫的服務功能。

## 取得 Azure 用戶端程式庫

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## 設定應用程式以使用服務匯流排

若要使用服務匯流排 API：

1. 參考自動換片器檔案使用 [require_once][require-once] 陳述式。
2. 參考任何您可能使用的類別。

下列範例顯示如何納入自動換片器檔案及參考 **ServiceBusService** 類別。

> [AZURE.NOTE] 此範例 (和本文中的其他範例) 假設您已安裝 PHP Client Libraries for Azure 透過編輯器。 如果您在手動或以 PEAR 封裝安裝程式庫，您必須參考 **WindowsAzure.php** 自動換片器檔案。

```
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

在下列各範例中，一律會顯示 `require_once` 陳述式，但只會參考要執行之範例所需的類別。

## 設定服務匯流排連接

若要具現化 Azure 服務匯流排用戶端，您必須先具備符合下列格式的有效連接字串：

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

其中，`Endpoint` 的格式通常是 `https://[yourNamespace].servicebus.windows.net`。

若要建立任何 Azure 服務用戶端，您必須使用 **ServicesBuilder** 類別。 您可以：

* 直接將連接字串傳遞給它。
* 使用 **CloudConfigurationManager (CCM)** 檢查多種外部來源的連接字串:
    * 預設已支援一種外部來源，即環境變數。
    * 您可以擴充，以加入新來源 **ConnectionStringSource** 類別。

在本文的各範例中，將會直接傳遞連接字串。

```
require_once 'vendor\autoload.php';

use WindowsAzure\Common\ServicesBuilder;
    
$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## 建立主題

您可以執行管理作業，透過服務匯流排主題的 **ServiceBusRestProxy** 類別。 A **ServiceBusRestProxy** 物件透過建構 **Createservicebusservice** factory 方法，使用封裝了權杖權限加以管理的適當連接字串。

下列範例示範如何具現化 **ServiceBusRestProxy** 呼叫 **servicebusrestproxy->createtopic** 來建立名為 `mytopic` 內 `MySBNamespace` 命名空間:

```
require_once 'vendor\autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;
    
// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {       
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [AZURE.NOTE] 您可以使用 `listTopics` 方法 `ServiceBusRestProxy` 物件來檢查服務命名空間內是否已存在具有指定名稱的主題。

## 建立訂用帳戶

也會建立主題訂閱 **servicebusrestproxy->createsubscription** 方法。 為訂閱命名，且能包含選擇性篩選器，以用來限制傳遞至訂閱的虛擬佇列的訊息集合。

### 使用預設 (MatchAll) 篩選器建立訂用帳戶

 **MatchAll** 篩選器是預設篩選器，如果沒有指定篩選時建立新的訂閱。 當 **MatchAll** 篩選器時，發佈至主題的所有訊息都會被都置於訂閱的虛擬佇列。 下列範例會建立名為 'mysubscription' 的訂閱，並使用預設 **MatchAll** 篩選器。

```
require_once 'vendor\autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

### 使用篩選器建立訂用帳戶

您也可以設定篩選器，讓您指定傳送至主題的哪些訊息應出現在特定主題訂用帳戶中。 訂閱所支援的篩選器的最具彈性的型別是 **SqlFilter**, ，可用來實作 SQL92 的子集。 SQL 篩選器會對發佈至主題之訊息的屬性運作。 如需 SqlFilters 的詳細資訊，請參閱 [SqlFilter.SqlExpression 屬性][sqlfilter]。

> [AZURE.NOTE] 訂閱的每個規則處理內送訊息獨立地將其結果訊息新增至訂閱。 此外，每個新的訂閱都有預設值 **規則** 與將主題中的所有訊息都新增至訂閱的篩選條件的物件。 To receive only messages matching your filter, you must remove the default rule. 您可以使用 `ServiceBusRestProxy->deleteRule` 方法以移除預設規則。

下列範例會建立名為 **HighMessages** 與 **SqlFilter** 只選取自訂的訊息 **MessageNumber** 屬性大於 3 (請參閱 [傳送訊息至主題](#send-messages-to-a-topic) 有關如何將自訂屬性新增至訊息):

```
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

請注意，此程式碼必須使用額外的命名空間：`WindowsAzure\ServiceBus\Models\SubscriptionInfo`。

同樣地，下列範例會建立名為 **LowMessages** 與 **SqlFilter** sqlfilter 只選取 **MessageNumber** 屬性小於或等於 3:

```
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

現在，當訊息傳送至 `mytopic` 主題時，一律會將該訊息傳遞至已訂閱 `mysubscription` 訂用帳戶的接收者，並選擇性地傳遞至已訂閱 `HighMessages` 和 `LowMessages` 訂用帳戶的接收者 (視訊息內容而定)。

## 傳送訊息至主題

若要將訊息傳送至服務匯流排主題，應用程式會呼叫 **servicebusrestproxy->sendtopicmessage** 方法。 下列程式碼示範如何傳送訊息至 `mytopic` 先前中建立的主題
`MySBNamespace` 服務命名空間。

```
require_once 'vendor\autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");
    
    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

傳送至服務匯流排主題的訊息是 **BrokeredMessage** 類別。 **BrokeredMessage** 物件具有一組標準屬性和方法 (例如 **getLabel**, ，**getTimeToLive**, ，**setLabel**, ，和 **setTimeToLive**)，以及可以用來保存自訂應用程式特定屬性的屬性。 下列範例示範如何將 5 則測試訊息傳送至先前建立的 `mytopic` 主題。  **SetProperty** 方法用來加入自訂屬性 (`MessageNumber`) 至每個訊息。 請注意， `MessageNumber` 屬性值的變化每個訊息 (您可以使用此值來判斷哪些訂閱會接收，如所示 [建立訂閱](#create-a-subscription) 一節):

```
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);
            
    // Set custom property.
    $message->setProperty("MessageNumber", $i);
            
    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

服務匯流排佇列最多可支援 256 KB 的訊息大小 (包含標準和自訂應用程式屬性的標頭可以容納 64 KB 的大小上限)。 佇列中所保存的訊息數目沒有限制，但佇列所保存的訊息大小總計會有最高限制。 佇列大小的這項上限為 5 GB。 如需配額的詳細資訊，請參閱 [Azure 佇列和服務匯流排佇列][]。

## 自訂閱接收訊息

自訂閱接收訊息的最佳方式是使用 **servicebusrestproxy->receivesubscriptionmessage** 方法。 已接收的訊息可在兩種不同模式運作: **ReceiveAndDelete** (預設值) 和 **PeekLock**。

當使用 **ReceiveAndDelete** 模式中，接收是一次性作業; 也就是當服務匯流排在訂閱中收到訊息的讀取的要求，它會標示為已取用的訊息並傳回應用程式。 **ReceiveAndDelete** 模式是最簡單的模型，是最適合的案例
不處理訊息的應用程式案例。 若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。 因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

在 **PeekLock** 模式中，接收訊息會變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。 當服務匯流排收到要求時，它會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。 藉由傳遞至接收的訊息應用程式完成處理訊息 (或可靠地儲存供未來處理) 之後，完成接收程序的第二個階段 **servicebusrestproxy->deletemessage**。 當服務匯流排看到 **deleteMessage** 呼叫時，它會將標示為已取用的訊息並從佇列中移除。

下列範例示範如何接收和處理訊息使用 **PeekLock** 模式 (不是預設模式)。 

```
require_once 'vendor\autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();
    
    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";
        
    /*---------------------------
        Process message here.
    ----------------------------*/
        
    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/windowsazure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。 如果接收者應用程式無法處理訊息，因為某種原因，則它可以呼叫 **unlockMessage** 所接收訊息的方法 (而不是 **deleteMessage** 方法)。 這將導致服務匯流排將佇列中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與在佇列內鎖定之訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

該應用程式當機之前處理訊息之後 **deleteMessage** 發出要求，然後將訊息傳遞給應用程式重新啟動時。 這通常稱為 **至少處理一次**; 也就是說，每個訊息會至少處理一次，但在某些情況下可能會重新傳遞相同訊息。 如果案例無法容許重複處理，則應用程式開發人員應在應用程式中加入其他邏輯，以處理重複的訊息傳遞。 這通常您可使用 **getMessageId** 訊息會維持不變各個傳遞嘗試的方法。

## 刪除主題和訂用帳戶

若要刪除的主題或訂閱，請使用 **servicebusrestproxy->deletetopic** 或 **servicebusrestproxy->deletesubscripton** 方法，分別。 請注意，刪除主題也將會刪除對主題註冊的任何訂用帳戶。

下列範例示範如何刪除名為 `mytopic` 的主題及其註冊的訂用帳戶。

```
require_once 'vendor\autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {       
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

使用 **deleteSubscription** 方法中，您可以個別刪除某個訂閱:

```
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## 後續步驟

現在您已了解服務匯流排佇列的基本概念，請參閱 [佇列、 主題和訂閱][] 如需詳細資訊。

[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[require-once]: http://php.net/require_once
[Azure Queues and Service Bus queues]: service-bus-azure-and-service-bus-queues-compared-contrasted.md#capacity-and-quotas

