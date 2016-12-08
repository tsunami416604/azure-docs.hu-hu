---
title: "A Service Bus által felügyelt üzenettovábbítás REST oktatóanyaga | Microsoft Docs"
description: "A felügyelt üzenettovábbítás REST oktatóanyaga."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9b7a8147-a1b1-42fc-b30e-f52e79a902b5
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 9ace119de3676bcda45d524961ebea27ab093415
ms.openlocfilehash: b2cd9e5db765aa2ffbb00063ae193e39ffe9de4e


---
# <a name="service-bus-brokered-messaging-rest-tutorial"></a>A Service Bus által felügyelt üzenettovábbítás REST oktatóanyaga
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ez az oktatóanyag bemutatja, hogyan hozhat létre REST-alapú Azure Service Bus-üzenetsort, illetve témakört/előfizetést.

## <a name="create-a-namespace"></a>Névtér létrehozása
Az első lépés egy szolgáltatásnévtér létrehozása, valamint egy [közös hozzáférésű jogosultságkód](service-bus-sas-overview.md) (SAS-) kulcs beszerzése. A névtér egy alkalmazáshatárt biztosít a Service Buson keresztül közzétett minden alkalmazáshoz. Az SAS-kulcsot a rendszer automatikusan előállítja a szolgáltatásnévtér létrehozásakor. A szolgáltatásnévtér és a SAS-kulcs együttes használata hitelesítő adatokat biztosít a Service Bus számára, amellyel hitelesíti a hozzáférést egy alkalmazáshoz.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-console-client"></a>Konzolügyfél létrehozása
A Service Bus-üzenetsorok lehetővé teszik az üzenetek érkezési sorrendben történő tárolását. Az üzenettémák és előfizetések közzététel/előfizetés mintát valósítanak meg. Először létrehozhat egy üzenettémakört, majd egy vagy több, az üzenettémakörhöz rendelt előfizetést. Az üzenettémakörbe küldött üzeneteket a témakör előfizetői azonnal megkapják.

A jelen oktatóanyagban található kód a következőt hajtja végre.

* A névtér és a [közös hozzáférésű jogosultságkód](service-bus-sas-overview.md) (SAS-) kulcs segítségével hozzáfér a Service Bus-névtér erőforrásaihoz.
* Létrehoz egy üzenetsort, egy üzenetet küld az üzenetsorba, és kiolvassa az üzenetet az üzenetsorból.
* Létrehoz egy üzenettémakört, egy előfizetést az üzenettémára, majd elküldi és kiolvassa az üzenetet az előfizetésből.
* A Service Bustól lekéri az üzenetsorra, az üzenettémakörre és az előfizetésre vonatkozó összes információt, beleértve az előfizetési szabályokat.
* Törli az üzenetsor, az üzenettémakör és az előfizetés erőforrásait.

A szolgáltatás egy REST-stílusú webszolgáltatás, ezért nem tartalmaz speciális típusokat, mivel a teljes adatcsere karakterláncokon alapul. Ez azt jelenti, hogy a Visual Studio-projekt nem hivatkozhat egy Service Bus-könyvtárra sem.

Miután az első lépésben beszerezte a névteret és a hitelesítő adatokat, egy alapszintű Visual Studio-konzolalkalmazást kell létrehoznia.

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása
1. Indítsa el a Visual Studiót rendszergazdaként. Ehhez a **Start** menüben kattintson a jobb gombbal a programra, majd kattintson a **Futtatás rendszergazdaként** parancsra.
2. Hozzon létre új egy új konzolalkalmazás-projektet. Kattintson a **Fájl** menüre, és kattintson az **Új**, majd a **Projekt** elemre. Az **Új projekt** párbeszédpanelen kattintson a **Visual C#** elemre (ha a **Visual C#** nem jelenik meg, keresse meg a **Más nyelvek** területen), válassza ki a **Konzolalkalmazás** sablont, és nevezze el **Microsoft.ServiceBus.Samples** néven. Használja az alapértelmezett helyet. A projekt létrehozásához kattintson az **OK** gombra.
3. A Program.cs fájlban győződjön meg róla, hogy a(z) `using` utasítás a következőképpen jelenik meg:
   
    ```
    using System;
    using System.Globalization;
    using System.IO;
    using System.Net;
    using System.Security.Cryptography;
    using System.Text;
    using System.Xml;
    ```
4. Ha szükséges, nevezze át a program névterét a Visual Studio alapértelmezett értékéről a következőre: `Microsoft.ServiceBus.Samples`.
5. A(z) `Program` osztályon belül adja hozzá a következő globális változókat:
   
    ```
    static string serviceNamespace;
    static string baseAddress;
    static string token;
    const string sbHostName = "servicebus.windows.net";
    ```
6. A(z) `Main()` területre illessze be a következő kódot:
   
    ```
    Console.Write("Enter your service namespace: ");
    serviceNamespace = Console.ReadLine();
   
    Console.Write("Enter your SAS key: ");
    string SASKey = Console.ReadLine();
   
    baseAddress = "https://" + serviceNamespace + "." + sbHostName + "/";
    try
    {
        token = GetSASToken("RootManageSharedAccessKey", SASKey);
   
        string queueName = "Queue" + Guid.NewGuid().ToString();
   
        // Create and put a message in the queue
        CreateQueue(queueName, token);
        SendMessage(queueName, "msg1");
        string msg = ReceiveAndDeleteMessage(queueName);
   
        string topicName = "Topic" + Guid.NewGuid().ToString();
        string subscriptionName = "Subscription" + Guid.NewGuid().ToString();
        CreateTopic(topicName);
        CreateSubscription(topicName, subscriptionName);
        SendMessage(topicName, "msg2");
   
        Console.WriteLine(ReceiveAndDeleteMessage(topicName + "/Subscriptions/" + subscriptionName));
   
        // Get an Atom feed with all the queues in the namespace
        Console.WriteLine(GetResources("$Resources/Queues"));
   
        // Get an Atom feed with all the topics in the namespace
        Console.WriteLine(GetResources("$Resources/Topics"));
   
        // Get an Atom feed with all the subscriptions for the topic we just created
        Console.WriteLine(GetResources(topicName + "/Subscriptions"));
   
        // Get an Atom feed with all the rules for the topic and subscription we just created
        Console.WriteLine(GetResources(topicName + "/Subscriptions/" + subscriptionName + "/Rules"));
   
        // Delete the queue we created
        DeleteResource(queueName);
   
        // Delete the topic we created
        DeleteResource(topicName);
   
        // Get an Atom feed with all the topics in the namespace, it shouldn't have the one we created now
        Console.WriteLine(GetResources("$Resources/Topics"));
   
        // Get an Atom feed with all the queues in the namespace, it shouldn't have the one we created now
        Console.WriteLine(GetResources("$Resources/Queues"));
    }
    catch (WebException we)
    {
        using (HttpWebResponse response = we.Response as HttpWebResponse)
        {
            if (response != null)
            {
                Console.WriteLine(new StreamReader(response.GetResponseStream()).ReadToEnd());
            }
            else
            {
                Console.WriteLine(we.ToString());
            }
        }
    }
   
    Console.WriteLine("\nPress ENTER to exit.");
    Console.ReadLine();
    ```

## <a name="create-management-credentials"></a>Felügyeleti hitelesítő adatok létrehozása
A következő lépés egy olyan metódus megírása, amely feldolgozza az előző lépésben megadott névteret és SAS-kulcsot, és egy SAS-jogkivonatot ad vissza. Ez a példa egy egy órán keresztül érvényes SAS-jogkivonatot hoz létre.

### <a name="create-a-getsastoken-method"></a>GetSASToken() metódus létrehozása
Illessze be a következő kódot a(z) `Main()` metódus után, a(z) `Program` osztályon belül:

```
private static string GetSASToken(string SASKeyName, string SASKeyValue)
{
  TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
  string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + 3600);
  string stringToSign = WebUtility.UrlEncode(baseAddress) + "\n" + expiry;
  HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(SASKeyValue));

  string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
  string sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}",
      WebUtility.UrlEncode(baseAddress), WebUtility.UrlEncode(signature), expiry, SASKeyName);
  return sasToken;
}
```
## <a name="create-the-queue"></a>Az üzenetsor létrehozása
A következő lépés egy olyan metódus írása, amely a REST-stílusú HTTP PUT parancsot használja egy üzenetsor létrehozásához.

Illessze be a következő kódot közvetlenül az előző lépésben hozzáadott `GetSASToken()` kód után:

```
// Uses HTTP PUT to create the queue
private static string CreateQueue(string queueName, string token)
{
    // Create the URI of the new queue, note that this uses the HTTPS scheme
    string queueAddress = baseAddress + queueName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating queue {0}", queueAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                          <title type=""text"">" + queueName + @"</title>
                          <content type=""application/xml"">
                            <QueueDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                          </content>
                        </entry>";

    byte[] response = webClient.UploadData(queueAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

## <a name="send-a-message-to-the-queue"></a>Üzenet küldése az üzenetsorba
Ebben a lépésben egy REST-stílusú HTTP POST parancsot használó metódust vesz fel, amely egy üzenetet küld az előző lépésben létrehozott üzenetsorba.

1. Illessze be a következő kódot közvetlenül az előző lépésben hozzáadott `CreateQueue()` kód után:
   
    ```
    // Sends a message to the "queueName" queue, given the name and the value to enqueue
    // Uses an HTTP POST request.
    private static void SendMessage(string queueName, string body)
    {
        string fullAddress = baseAddress + queueName + "/messages" + "?timeout=60&api-version=2013-08 ";
        Console.WriteLine("\nSending message {0} - to address {1}", body, fullAddress);
        WebClient webClient = new WebClient();
        webClient.Headers[HttpRequestHeader.Authorization] = token;
   
        webClient.UploadData(fullAddress, "POST", Encoding.UTF8.GetBytes(body));
    }
    ```
2. A közvetítőalapú üzenet standard tulajdonságai egy `BrokerProperties` HTTP-fejlécben vannak elhelyezve. A közvetítő tulajdonságait JSON formátumban kell szerializálni. Ahhoz, hogy a program 30 másodperces **TimeToLive** értéket határozzon meg, és hozzáadja az „M1” címkét az üzenethez, vegye fel a következő kódot közvetlenül az előző példában szereplő `webClient.UploadData()` hívás elé:
   
    ```
    // Add brokered message properties "TimeToLive" and "Label"
    webClient.Headers.Add("BrokerProperties", "{ \"TimeToLive\":30, \"Label\":\"M1\"}");
    ```
   
    Vegye figyelembe, hogy a rendszer hozzáadta, és hozzá fogja adni a közvetítőalapú üzenet tulajdonságait. Emiatt a küldési kérésnek egy olyan API-verziót kell meghatároznia, amely a kérés részét képező közvetítőalapú üzenet összes tulajdonságát támogatja. Ha a meghatározott API-verzió nem támogatja a közvetítőalapú üzenet egyik tulajdonságát, az adott tulajdonságot figyelmen kívül hagyja a rendszer.
3. Az egyéni üzenettulajdonságok kulcs-érték párok készleteként definiálhatók. Minden egyéni tulajdonságot külön TPPT-fejléc tárol. A „Prioritás” és „Ügyfél” egyéni tulajdonságok felvételéhez, vegye fel a következő kódot közvetlenül az előző példában szereplő `webClient.UploadData()` hívás elé:
   
    ```
    // Add custom properties "Priority" and "Customer".
    webClient.Headers.Add("Priority", "High");
    webClient.Headers.Add("Customer", "12345");
    ```

## <a name="receive-and-delete-a-message-from-the-queue"></a>Üzenet fogadása és törlése az üzenetsorból
A következő lépés egy olyan metódus hozzáadása, amely a REST-stílusú HTTP DELETE parancsot használja egy üzenet fogadásához és törléséhez az üzenetsorból.

Illessze be a következő kódot közvetlenül az előző lépésben hozzáadott `SendMessage()` kód után:

```
// Receives and deletes the next message from the given resource (queue, topic, or subscription)
// using the resourceName and an HTTP DELETE request
private static string ReceiveAndDeleteMessage(string resourceName)
{
    string fullAddress = baseAddress + resourceName + "/messages/head" + "?timeout=60";
    Console.WriteLine("\nRetrieving message from {0}", fullAddress);
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
    string responseStr = Encoding.UTF8.GetString(response);

    Console.WriteLine(responseStr);
    return responseStr;
}
```

## <a name="create-a-topic-and-subscription"></a>Üzenettémakör és előfizetés létrehozása
A következő lépés egy olyan metódus írása, amely a REST-stílusú HTTP PUT parancsot használja egy üzenettémakör létrehozásához. Ezután egy olyan metódust kell írnia, amely létrehoz egy előfizetést az adott üzenettémakörhöz.

### <a name="create-a-topic"></a>Üzenettémakör létrehozása
Illessze be a következő kódot közvetlenül az előző lépésben hozzáadott `ReceiveAndDeleteMessage()` kód után:

```
// Using an HTTP PUT request.
private static string CreateTopic(string topicName)
{
    var topicAddress = baseAddress + topicName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating topic {0}", topicAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + topicName + @"</title>
                                  <content type=""application/xml"">
                                    <TopicDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(topicAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

### <a name="create-a-subscription"></a>Előfizetés létrehozása
A következő kód egy előfizetést hoz létre az előző lépésben létrehozott üzenettémakörhöz. Adja hozzá a következő kódot közvetlenül a(z) `CreateTopic()` definíciója után:

```
private static string CreateSubscription(string topicName, string subscriptionName)
{
    var subscriptionAddress = baseAddress + topicName + "/Subscriptions/" + subscriptionName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating subscription {0}", subscriptionAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + subscriptionName + @"</title>
                                  <content type=""application/xml"">
                                    <SubscriptionDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(subscriptionAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

## <a name="retrieve-message-resources"></a>Üzenet-erőforrások lekérése
Ebben a lépésben fel kell vennie azt a kódot, amely lekéri az üzenet tulajdonságait, majd törli az előző lépésben létrehozott üzenetkezelési erőforrásokat.

### <a name="retrieve-an-atom-feed-with-the-specified-resources"></a>Atom-hírcsatorna lekérése a megadott erőforrásokkal
Vegye fel a következő kódot közvetlenül az előző lépésben felvett `CreateSubscription()` kód után:

```
private static string GetResources(string resourceAddress)
{
    string fullAddress = baseAddress + resourceAddress;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;
    Console.WriteLine("\nGetting resources from {0}", fullAddress);
    return FormatXml(webClient.DownloadString(fullAddress));
}
```

### <a name="delete-messaging-entities"></a>Üzenetküldési entitások törlése
Vegye fel a következő kódot közvetlenül az előző lépésben felvett kód után:

```
private static string DeleteResource(string resourceName)
{
    string fullAddress = baseAddress + resourceName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nDeleting resource at {0}", fullAddress);
    byte[] response = webClient.UploadData(fullAddress, "DELETE", newbyte[0]);
    return Encoding.UTF8.GetString(response);
}
```

### <a name="format-the-atom-feed"></a>Az Atom-hírcsatorna formázása
A(z) `GetResources()` metódus meghívja a(z) `FormatXml()` metódust, amely újraformázza a lekért Atom-hírcsatornát, hogy jobban olvasható legyen. A(z) `FormatXml()` definíciója a következőkben látható. Vegye fel ezt a kódot közvetlenül az előző lépésben létrehozott `DeleteResource()` kód után:

```
// Formats the XML string to be more human-readable; intended for display purposes
private static string FormatXml(string inputXml)
{
    XmlDocument document = new XmlDocument();
    document.Load(new StringReader(inputXml));

    StringBuilder builder = new StringBuilder();
    using (XmlTextWriter writer = new XmlTextWriter(new StringWriter(builder)))
    {
        writer.Formatting = Formatting.Indented;
        document.Save(writer);
    }

    return builder.ToString();
}
```

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása
Most már lefordíthatja és futtathatja az alkalmazást. A Visual Studio **Létrehozás** menüjében kattintson a **Megoldás fordítása** elemre, vagy nyomja le a **Ctrl+Shift+B** billentyűkombinációt.

### <a name="run-the-application"></a>Az alkalmazás futtatása
Ha nem voltak benne hibák, az alkalmazás futtatásához nyomja le az F5 billentyűt. Amikor a program kéri, adja meg a névteret, a SAS-kulcs nevét, és az első lépésben beszerzett SAS-kulcs értékét.

### <a name="example"></a>Példa
A következő példában a teljes kód látható, ahogy meg kell jelennie, ha követte az oktatóanyag összes lépését.

```
using System;
using System.Globalization;
using System.IO;
using System.Net;
using System.Security.Cryptography;
using System.Text;
using System.Xml;

namespace Microsoft.ServiceBus.Samples
{
    class Program
    {
        static string serviceNamespace;
        static string baseAddress;
        static string token;
        const string sbHostName = "servicebus.windows.net";

        static void Main(string[] args)
        {
            Console.Write("Enter your service namespace: ");
            serviceNamespace = Console.ReadLine();

            Console.Write("Enter your SAS key: ");
            string SASKey = Console.ReadLine();

            baseAddress = "https://" + serviceNamespace + "." + sbHostName + "/";
            try
            {
                token = GetSASToken("RootManageSharedAccessKey", SASKey);

                string queueName = "Queue" + Guid.NewGuid().ToString();

                // Create and put a message in the queue
                CreateQueue(queueName, token);
                SendMessage(queueName, "msg1");
                string msg = ReceiveAndDeleteMessage(queueName);

                string topicName = "Topic" + Guid.NewGuid().ToString();
                string subscriptionName = "Subscription" + Guid.NewGuid().ToString();
                CreateTopic(topicName);
                CreateSubscription(topicName, subscriptionName);
                SendMessage(topicName, "msg2");

                Console.WriteLine(ReceiveAndDeleteMessage(topicName + "/Subscriptions/" + subscriptionName));

                // Get an Atom feed with all the queues in the namespace
                Console.WriteLine(GetResources("$Resources/Queues"));

                // Get an Atom feed with all the topics in the namespace
                Console.WriteLine(GetResources("$Resources/Topics"));

                // Get an Atom feed with all the subscriptions for the topic we just created
                Console.WriteLine(GetResources(topicName + "/Subscriptions"));

                // Get an Atom feed with all the rules for the topic and subscription we just created
                Console.WriteLine(GetResources(topicName + "/Subscriptions/" + subscriptionName + "/Rules"));

                // Delete the queue we created
                DeleteResource(queueName);

                // Delete the topic we created
                DeleteResource(topicName);

                // Get an Atom feed with all the topics in the namespace, it shouldn't have the one we created now
                Console.WriteLine(GetResources("$Resources/Topics"));

                // Get an Atom feed with all the queues in the namespace, it shouldn't have the one we created now
                Console.WriteLine(GetResources("$Resources/Queues"));
            }
            catch (WebException we)
            {
                using (HttpWebResponse response = we.Response as HttpWebResponse)
                {
                    if (response != null)
                    {
                        Console.WriteLine(new StreamReader(response.GetResponseStream()).ReadToEnd());
                    }
                    else
                    {
                        Console.WriteLine(we.ToString());
                    }
                }
            }

            Console.WriteLine("\nPress ENTER to exit.");
            Console.ReadLine();
        }

        private static string GetSASToken(string SASKeyName, string SASKeyValue)
        {
            TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
            string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + 3600);
            string stringToSign = WebUtility.UrlEncode(baseAddress) + "\n" + expiry;
            HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(SASKeyValue));

            string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
            string sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}",
                WebUtility.UrlEncode(baseAddress), WebUtility.UrlEncode(signature), expiry, SASKeyName);
            return sasToken;
        }

        // Uses HTTP PUT to create the queue
        private static string CreateQueue(string queueName, string token)
        {
            // Create the URI of the new queue, note that this uses the HTTPS scheme
            string queueAddress = baseAddress + queueName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating queue {0}", queueAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + queueName + @"</title>
                                  <content type=""application/xml"">
                                    <QueueDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(queueAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        // Sends a message to the "queueName" queue, given the name and the value to enqueue
        // Uses an HTTP POST request.
        private static void SendMessage(string queueName, string body)
        {
            string fullAddress = baseAddress + queueName + "/messages" + "?timeout=60&api-version=2013-08 ";
            Console.WriteLine("\nSending message {0} - to address {1}", body, fullAddress);
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;
            // Add brokered message properties “TimeToLive” and “Label”.
            webClient.Headers.Add("BrokerProperties", "{ \"TimeToLive\":30, \"Label\":\"M1\"}");
            // Add custom properties “Priority” and “Customer”.
            webClient.Headers.Add("Priority", "High");
            webClient.Headers.Add("Customer", "12345");
            webClient.UploadData(fullAddress, "POST", Encoding.UTF8.GetBytes(body));

        }

        // Receives and deletes the next message from the given resource (queue, topic, or subscription)
        // using the resourceName and an HTTP DELETE request.
        private static string ReceiveAndDeleteMessage(string resourceName)
        {
            string fullAddress = baseAddress + resourceName + "/messages/head" + "?timeout=60";
            Console.WriteLine("\nRetrieving message from {0}", fullAddress);
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
            string responseStr = Encoding.UTF8.GetString(response);

            Console.WriteLine(responseStr);
            return responseStr;
        }

        // Using an HTTP PUT request.
        private static string CreateTopic(string topicName)
        {
            var topicAddress = baseAddress + topicName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating topic {0}", topicAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + topicName + @"</title>
                                  <content type=""application/xml"">
                                    <TopicDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(topicAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        private static string CreateSubscription(string topicName, string subscriptionName)
        {
            var subscriptionAddress = baseAddress + topicName + "/Subscriptions/" + subscriptionName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating subscription {0}", subscriptionAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + subscriptionName + @"</title>
                                  <content type=""application/xml"">
                                    <SubscriptionDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(subscriptionAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        private static string GetResources(string resourceAddress)
        {
            string fullAddress = baseAddress + resourceAddress;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;
            Console.WriteLine("\nGetting resources from {0}", fullAddress);
            return FormatXml(webClient.DownloadString(fullAddress));
        }

        private static string DeleteResource(string resourceName)
        {
            string fullAddress = baseAddress + resourceName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nDeleting resource at {0}", fullAddress);
            byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
            return Encoding.UTF8.GetString(response);
        }

        // Formats the XML string to be more human-readable; intended for display purposes
        private static string FormatXml(string inputXml)
        {
            XmlDocument document = new XmlDocument();
            document.Load(new StringReader(inputXml));

            StringBuilder builder = new StringBuilder();
            using (XmlTextWriter writer = new XmlTextWriter(new StringWriter(builder)))
            {
                writer.Formatting = Formatting.Indented;
                document.Save(writer);
            }

            return builder.ToString();
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések
További információért lásd a következő cikkeket:

* [Service Bus messaging overview](service-bus-messaging-overview.md) (A Service Bus üzenetkezelésének áttekintése)
* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus Relay REST tutorial](../service-bus-relay/service-bus-relay-rest-tutorial.md) (A Service Bus relay REST-oktatóanyaga)




<!--HONumber=Nov16_HO3-->


