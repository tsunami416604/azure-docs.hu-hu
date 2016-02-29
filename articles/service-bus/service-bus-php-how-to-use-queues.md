<properties 
    pageTitle="如何將服務匯流排佇列搭配 PHP 使用 | Microsoft Azure" 
    description="了解如何使用 Azure 中的服務匯流排佇列。 程式碼範例以 PHP 撰寫。" 
    services="service-bus" 
    documentationCenter="php" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/14/2015" 
    ms.author="sethm"/>

# 如何使用服務匯流排佇列

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

本指南將說明如何使用服務匯流排佇列。 在 PHP 中並使用這些範例 [Azure SDK for PHP](../php-download-sdk.md)。 涵蓋的案例包括 **建立佇列**, ，**傳送和接收訊息**, ，和 **刪除佇列**。

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## 建立 PHP 應用程式

唯一的需求建立 PHP 應用程式，使其存取 Azure Blob 服務是參考中的類別 [Azure SDK for PHP](../php-download-sdk.md) 從您的程式碼。 您可以使用任何開發工具來建立應用程式，或記事本。

> [AZURE.NOTE] 您的 PHP 安裝也必須 [OpenSSL 延伸](http://php.net/openssl) 安裝並啟用。

在本指南中，您將使用可從 PHP 應用程式內本機呼叫的服務功能，或可從 Azure Web 角色、背景工作角色或網站內執行的程式碼中呼叫的資料表服務功能。

## 取得 Azure 用戶端程式庫

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## 設定應用程式以使用服務匯流排

若要使用 Azure 服務匯流排佇列 API，請執行下列動作：

1. 參考自動換片器檔案使用 [require_once][require_once] 陳述式。
2. 參考任何您可能使用的類別。

下列範例顯示如何納入自動換片器檔案及參考 **ServicesBuilder** 類別。

> [AZURE.NOTE] 此範例 (和本文中的其他範例) 假設您已安裝 PHP Client Libraries for Azure 透過編輯器。 如果您在手動或以 PEAR 封裝安裝程式庫，您必須參考 **WindowsAzure.php** 自動換片器檔案。

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

在下列各範例中，一律會顯示 `require_once` 陳述式，但只會參考要執行之範例所需的類別。

## 設定 Azure 服務匯流排連接

若要具現化服務匯流排用戶端，您必須具備符合下列格式的有效連接字串：

    Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

其中 **端點** 格式通常是 `https://[yourNamespace].servicebus.windows.net`。

若要建立任何 Azure 服務用戶端，您必須使用 **ServicesBuilder** 類別。 您可以：

* 直接將連接字串傳遞給它。
* 使用 **CloudConfigurationManager (CCM)** 檢查多種外部來源的連接字串:
    * 預設已支援一種外部來源，即環境變數
    * 您可以擴充，以加入新來源 **ConnectionStringSource** 類別

在本文的各範例中，將會直接傳遞連接字串。

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);


## 如何建立佇列

您可以執行管理作業，透過服務匯流排佇列的 **ServiceBusRestProxy** 類別。 A **ServiceBusRestProxy** 物件透過建構 **Createservicebusservice** factory 方法，使用封裝了權杖權限加以管理的適當連接字串。

下列範例示範如何具現化 **ServiceBusRestProxy** 呼叫 **servicebusrestproxy->createqueue** 來建立名為的佇列 `myqueue` 內 `MySBNamespace` 服務命名空間:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\QueueInfo;

    // Create Service Bus REST proxy.
        $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
    try {
        $queueInfo = new QueueInfo("myqueue");
        
        // Create queue.
        $serviceBusRestProxy->createQueue($queueInfo);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/library/windowsazure/dd179357
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

> [AZURE.NOTE] 您可以使用 `listQueues` 方法 `ServiceBusRestProxy` 物件來檢查服務命名空間內是否已存在具有指定名稱的佇列。

## 如何傳送訊息至佇列

若要將訊息傳送至服務匯流排佇列，應用程式會呼叫 **servicebusrestproxy->sendqueuemessage** 方法。 下列程式碼示範如何傳送訊息至 `myqueue` 先前中建立佇列
`MySBNamespace` 服務命名空間。

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
        $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/library/windowsazure/hh780775
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

傳送至 (和接收自) 服務匯流排佇列是執行個體的 **BrokeredMessage** 類別。 **BrokeredMessage** 物件具有一組標準方法 (例如 **getLabel**, ，**getTimeToLive**, ，**setLabel**, ，和 **setTimeToLive**) 和用來保存自訂應用程式特定屬性，以及一堆任意的應用程式資料的屬性。

服務匯流排佇列最多可支援 256 KB 的訊息大小 (包含標準和自訂應用程式屬性的標頭可以容納 64 KB 的大小上限)。 佇列中所保存的訊息數目沒有限制，但佇列所保存的訊息大小總計會有最高限制。 佇列大小的這項上限為 5 GB。

## 如何從佇列接收訊息

從佇列接收訊息的最佳方式是使用 **servicebusrestproxy->receivequeuemessage** 方法。 在兩個不同的模式來接收訊息: **ReceiveAndDelete** (預設值) 和 **PeekLock**。

當使用 **ReceiveAndDelete** 模式中，接收是一次性作業; 也就是說，當服務匯流排佇列中收到訊息的讀取的要求，它將訊息標示為已取用，傳回應用程式。 **ReceiveAndDelete** 模式是最簡單的模型，且最適用於應用程式容許在不處理訊息發生失敗時的案例。 若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。 因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

在 **PeekLock** 模式中，接收訊息會變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。 當服務匯流排收到要求時，它會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。 藉由傳遞至接收的訊息應用程式完成處理訊息 (或可靠地儲存供未來處理) 之後，完成接收程序的第二個階段 **servicebusrestproxy->deletemessage**。 當服務匯流排看到 **deleteMessage** 呼叫時，它會將標示為已取用的訊息並從佇列中移除。

下列範例顯示收到訊息的方式，以及處理使用 **PeekLock** 模式 (不是預設模式)。

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
        // Set the receive mode to PeekLock (default is ReceiveAndDelete).
        $options = new ReceiveMessageOptions();
        $options->setPeekLock();
        
        // Receive message.
        $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
        echo "Body: ".$message->getBody()."<br />";
        echo "MessageID: ".$message->getMessageId()."<br />";
        
        /*---------------------------
            Process message here.
        ----------------------------*/
        
        // Delete message. Not necessary if peek lock is not set.
        echo "Message deleted.<br />";
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

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。 如果接收者應用程式無法處理訊息，因為某種原因，則它可以呼叫 **unlockMessage** 所接收訊息的方法 (而不是 **deleteMessage** 方法)。 這將導致服務匯流排將佇列中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與在佇列內鎖定之訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

該應用程式當機之前處理訊息之後 **deleteMessage** 發出要求，然後將訊息傳遞給應用程式重新啟動時。 這通常稱為 **至少處理一次**; 也就是說，每個訊息會至少處理一次，但在某些情況下可能會重新傳遞相同訊息。 如果案例無法容許重複處理，建議您在應用程式中新增其他邏輯，以處理重複的訊息傳遞。 這通常您可使用 **getMessageId** 訊息會維持不變各個傳遞嘗試的方法。

## 後續步驟

現在您已了解服務匯流排佇列的基本概念，請參閱 [佇列、 主題和訂閱][] 如需詳細資訊。

如需詳細資訊，請參閱 [PHP 開發人員中心](/develop/php/)。

[Queues, Topics, and Subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once

 

