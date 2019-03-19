---
title: Leküldéses értesítések küldése az Azure Notification Hubs és a node.js használatával
description: Megtudhatja, hogyan használható a Notification Hubs leküldéses értesítések küldéséhez a Node.js-alkalmazás.
keywords: leküldéses értesítés, leküldéses notifications,node.js leküldéses, ios leküldéses
services: notification-hubs
documentationcenter: nodejs
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: d90f23f52ca4c0cce3d853114acf673aa085d3c5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57889784"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Leküldéses értesítések küldése az Azure Notification Hubs és a node.js használatával

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Áttekintés

> [!IMPORTANT]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, hozzon létre egy ingyenes próbafiókot néhány perc alatt keresztül a [Azure ingyenes kipróbálását](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Ez az útmutató bemutatja, hogyan küldhetők leküldéses értesítések segítségével közvetlenül az Azure Notification hubs egy [Node.js](https://nodejs.org) alkalmazás.

Az ismertetett forgatókönyvek között megtalálható, leküldéses értesítések küldése a következő platformokon alkalmazások:

- Android
- iOS
- Univerzális Windows-platform
- Windows Phone

## <a name="notification-hubs"></a>Notification Hubs

Az Azure Notification Hubs egy egyszerűen használható, többplatformos, méretezhető infrastruktúrát biztosítanak leküldéses értesítések küldése mobileszközökre. A szolgáltatás-infrastruktúra részletes ismertetéséért lásd: a [Azure Notification Hubs](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx) lapot.

## <a name="create-a-nodejs-application"></a>Node.js-alkalmazás létrehozása

Ebben az oktatóanyagban az első lépés egy új üres Node.js-alkalmazás létrehozása. Node.js-alkalmazás létrehozásával kapcsolatos útmutatóért lásd: [létrehozása és a Node.js-alkalmazás üzembe helyezése az Azure-webhely][nodejswebsite], [Node.js Felhőszolgáltatás] [ Node.js Cloud Service] Windows PowerShell használatával vagy [webhely webmatrixban][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>A Notification Hubs-alkalmazás konfigurálása

Az Azure Notification Hubs használatával, letöltheti és használhatja a Node.js szeretne [azure csomag](https://www.npmjs.com/package/azure), amely tartalmaz egy beépített, amely a leküldéses értesítési REST szolgáltatások kommunikálni segítő kódtárak.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Használja a Node Package Manager (NPM) a csomag beszerzése

1. Például használja a parancssori felület **PowerShell** (Windows), **terminálon** (Mac), vagy **Bash** (Linux), és lépjen abba a mappába, amelyben létrehozta az üres alkalmazás.
2. Hajtsa végre `npm install azure-sb` a parancssori ablakba.
3. Manuálisan futtathatja a `ls` vagy `dir` paranccsal ellenőrizheti, hogy egy `node_modules` mappában jött létre.
4. Keresse meg a mappán belül a **azure** csomagot, amely tartalmazza a kódtárakat, hozzá kell férnie az értesítési központban.

> [!NOTE]
> További információ a hivatalos az npm-et telepíti [NPM blog](https://blog.npmjs.org/post/85484771375/how-to-install-npm).

### <a name="import-the-module"></a>A modul importálása

Egy szövegszerkesztővel, adja hozzá a következő elejéhez a `server.js` fájlt az alkalmazás:

    ```javascript
    var azure = require('azure');
    ```

### <a name="set-up-an-azure-notification-hub-connection"></a>Az Azure Notification Hub-kapcsolat beállítása

A `NotificationHubService` objektum lehetővé teszi, hogy a notification hubs használatával. Az alábbi kód létrehoz egy `NotificationHubService` objektum az értesítési központ nevű `hubname`. Tetején adja hozzá a `server.js` fájl, az utasítást, hogy az azure-modul importálása után:

    ```javascript
    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
    ```

Szerezze be a kapcsolat `connectionstring` értéket a [Azure Portal] a következő lépések végrehajtásával:

1. Kattintson a bal oldali navigációs ablaktáblában **Tallózás**.
2. Válassza ki **Notification Hubs**, majd keresse meg a mintát használni szeretné a hubhoz. Olvassa el a [Windows Store első lépéseket ismertető oktatóanyag](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) Ha egy új értesítési központ létrehozása segítségre van szüksége.
3. Válassza ki **beállítások**.
4. Kattintson a **hozzáférési házirendek**. Láthatja, hogy mindkét megosztott és teljes körű hozzáférést kapcsolati karakterláncot.

![Az Azure portal – Notification hubs használatával](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> A kapcsolati karakterlánc használatával is lekérhet a **Get-AzureSbNamespace** parancsmag által biztosított [Azure PowerShell-lel](/powershell/azureps-cmdlets-docs) vagy a **azure sb névtér show** parancsot a [Azure parancssori felület (Azure CLI)](../cli-install-nodejs.md).

## <a name="general-architecture"></a>Általános architektúra

A `NotificationHubService` objektum a következő objektum példányok a leküldéses értesítések küldését adott eszközöket és alkalmazásokat tesz elérhetővé:

- **Android** – használja az `GcmService` objektum, amely érhető el: `notificationHubService.gcm`
- **iOS-es** – használja az `ApnsService` objektum, amely érhető el: `notificationHubService.apns`
- **Windows Phone** – használja az `MpnsService` objektum, amely érhető el: `notificationHubService.mpns`
- **Univerzális Windows Platform** – használja az `WnsService` objektum, amely érhető el: `notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Útmutató: Leküldéses értesítések küldésére Android-alkalmazások

A `GcmService` objektum tartalmazza a `send` leküldéses értesítések küldésére Android-alkalmazások segítségével módszert. A `send` módszer a következő paramétereket fogadja:

- **A címkék** -címke azonosítója. Ha nincs címke van megadva, minden ügyfél elküldi az értesítést.
- **Hasznos adat** – az üzenet JSON vagy nyers karakterlánc adattartalma.
- **A visszahívás** – a visszahívási függvény.

Az adattartalom formátuma további információkért lásd: a **adattartalom** szakaszában a [GCM-kiszolgáló végrehajtási](https://developer.android.com/google/gcm/server.html#payload) dokumentumot.

A következő kódban a `GcmService` példány által elérhetővé tett a `NotificationHubService` minden regisztrált ügyfél leküldéses értesítés küldéséhez.

    ```javascript
    var payload = {
      data: {
        message: 'Hello!'
      }
    };
    notificationHubService.gcm.send(null, payload, function(error){
      if(!error){
        //notification sent
      }
    });
    ```

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Útmutató: Leküldéses értesítések küldése iOS-alkalmazások

Ugyanaz, mint az Android-alkalmazások, a fent leírt a `ApnsService` objektum tartalmazza a `send` leküldéses értesítések küldése iOS-alkalmazások segítségével módszert. A `send` módszer a következő paramétereket fogadja:

- **A címkék** -címke azonosítója. Ha nincs címke van megadva, minden ügyfél elküldi az értesítést.
- **Hasznos adat** – az üzenet JSON vagy karakterlánc adattartalma.
- **A visszahívás** – a visszahívási függvény.

További információ az adattartalom formátuma: A **értesítési tartalom** szakaszában a [helyi és leküldéses értesítés programozási útmutató](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) dokumentumot.

A következő kódban a `ApnsService` példány által elérhetővé tett a `NotificationHubService` riasztási üzenetet küldi el az összes ügyfeleknek:

    ```javascript
    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
         // notification sent
      }
    });
    ```

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Útmutató: Leküldéses értesítések küldésére Windows Phone-alkalmazások

A `MpnsService` objektum tartalmazza a `send` leküldéses értesítések küldésére Windows Phone-alkalmazások segítségével módszert. A `send` módszer a következő paramétereket fogadja:

- **A címkék** -címke azonosítója. Ha nincs címke van megadva, minden ügyfél elküldi az értesítést.
- **Hasznos adat** – az üzenet XML-tartalma.
- **TargetName**  -  `toast` bejelentési értesítéseket. `token` csempe az értesítésekhez.
- **NotificationClass** – az értesítés prioritását. Tekintse meg a **HTTP-fejléc elemek** szakaszában a [leküldéses értesítések küldését egy kiszolgáló](https://msdn.microsoft.com/library/hh221551.aspx) dokumentum érvényes értékeket.
- **Beállítások** – nem kötelező kérésfejlécek.
- **A visszahívás** – a visszahívási függvény.

Érvényes listáját `TargetName`, `NotificationClass` és a beállítások fejléc, tekintse meg a [leküldéses értesítések küldését egy kiszolgáló](https://msdn.microsoft.com/library/hh221551.aspx) lapot.

Az alábbi példa a kódot használja a `MpnsService` példány által elérhetővé tett a `NotificationHubService` bejelentési leküldéses értesítések küldéséhez:

    ```javascript
    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });
    ```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Útmutató: Leküldéses értesítések küldéséhez univerzális Windows Platform (UWP-) alkalmazások

A `WnsService` objektum tartalmazza a `send` metódushoz, amely a leküldéses értesítések küldéséhez univerzális Windows-Platformos alkalmazásokhoz is használható.  A `send` módszer a következő paramétereket fogadja:

- **A címkék** -címke azonosítója. Ha nincs címke van megadva, minden regisztrált ügyfél elküldi az értesítést.
- **Hasznos adat** – az XML-üzenet adattartalma.
- **Típus** – az értesítés típusa.
- **Beállítások** – nem kötelező kérésfejlécek.
- **A visszahívás** – a visszahívási függvény.

Az érvényes típusok és kérésfejlécek listáját lásd: [leküldéses értesítési szolgáltatás-kérelmek és válaszfejlécek](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

A következő kódban a `WnsService` példány által elérhetővé tett a `NotificationHubService` egy bejelentési leküldéses értesítést küldhet egy UWP-alkalmazás:

    ```javascript
    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
         // notification sent
      }
    });
    ```

## <a name="next-steps"></a>További lépések

A fenti példa kódrészletek építsenek szolgáltatás infrastruktúrát a leküldéses értesítések kézbesíthetők az eszközök széles köréhez teszi lehetővé. Most, hogy megismerte a Notification Hubs használata a node.js alapjait, az alábbi hivatkozásokból tudhat meg többet hogyan terjesztheti ki további ezeket a képességeket.

- Lásd az MSDN-referencia a [Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx).
- Látogasson el a [Az Azure SDK a Node] tárházban a Githubon további minták és megvalósítási részletei.

[Az Azure SDK a Node]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: https://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs
[webmatrix]: https://docs.microsoft.com/aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure Portal]: https://portal.azure.com
