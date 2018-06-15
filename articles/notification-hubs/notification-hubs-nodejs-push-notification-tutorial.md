---
title: Leküldéses értesítések küldése az Azure Notification Hubs és a Node.js
description: Tudnivalók a Notification Hubs használata leküldéses értesítések küldéséhez egy Node.js-alkalmazás.
keywords: leküldéses értesítési, leküldéses notifications,node.js leküldéses, ios leküldéses
services: notification-hubs
documentationcenter: nodejs
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 7463d41382c59e4f7f03b58dbcbc3f5c45e9d15c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778358"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Leküldéses értesítések küldése az Azure Notification Hubs és a Node.js
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Áttekintés
> [!IMPORTANT]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).
> 
> 

Ez az útmutató bemutatja, hogyan küldhetők leküldéses értesítések segítségével az Azure Notification Hubs közvetlenül egy Node.js-alkalmazást. 

A tárgyalt forgatókönyvekben szerepel a leküldéses értesítések küldése a következő platformokon alkalmazások:

* Android
* iOS
* Windows Phone
* Az univerzális Windows Platform 

Notification hubs használatával kapcsolatban lásd: a [lépések](#next) szakasz.

## <a name="what-are-notification-hubs"></a>Mi a Notification Hubs szolgáltatás?
Az Azure Notification Hubs egy egyszerűen használható, többplatformos, méretezhető infrastruktúrát biztosít a leküldéses értesítések küldése a mobileszközökre. A szolgáltatás-infrastruktúrát a részletekért lásd: a [Azure Notification Hubs](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx) lap.

## <a name="create-a-nodejs-application"></a>Node.js-alkalmazás létrehozása
Az első lépés az oktatóanyag egy új, üres Node.js-alkalmazást hoz létre. A Node.js-alkalmazás létrehozása, lásd: [létrehozása és központi telepítése egy Node.js-alkalmazás számára az Azure webhelyén][nodejswebsite], [Node.js Felhőszolgáltatás] [ Node.js Cloud Service] Windows PowerShell használatával vagy [webhelyet a WebMatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Állítsa be az alkalmazását a Notification Hubs használatával
Azure Notification Hubs használatával, töltse le és használja a Node.js szüksége [azure-csomag](https://www.npmjs.com/package/azure), amely tartalmaz egy beépített segítő szalagtár szerepel, amely a leküldéses értesítési REST szolgáltatásokkal kommunikálni.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Csomópont Package Manager (NPM) használja a csomag beszerzése
1. Használjon például egy parancssori felületet **PowerShell** (Windows), **Terminálszolgáltatások** (Mac), vagy **Bash** (Linux), és lépjen abba a mappába, amelyben létrehozta az üres alkalmazás.
2. Típus **npm telepítése azure-sb** a parancsablakban.
3. Manuálisan futtatható a **ls** vagy **dir** parancs futtatásával ellenőrizze, hogy egy **csomópont\_modulok** mappa hozták létre. A mappában található a **azure** csomagot, amely tartalmazza a könyvtárak, hozzá kell férnie az értesítési központban.

> [!NOTE]
> A hivatalos NPM telepítéséről többet is megtudhat [NPM blog](http://blog.npmjs.org/post/85484771375/how-to-install-npm). 
> 
> 

### <a name="import-the-module"></a>A modul importálása
Egy szövegszerkesztővel, adja hozzá a következő elejéhez a **server.js** fájl tartalmát:

    var azure = require('azure');

### <a name="set-up-an-azure-notification-hub-connection"></a>Az Azure Notification Hub-kapcsolat beállítása
A **NotificationHubService** objektum lehetővé teszi, hogy a notification hubs használatával. Az alábbi kód létrehoz egy **NotificationHubService** objektum az értesítési központ nevű **hubname**. Az tetején adja hozzá a **server.js** fájl, az utasítást, hogy az azure modul importálása után:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

A kapcsolat **connectionstring** érték lehet lekérni a [Azure-portálon] az alábbi lépések elvégzésével:

1. A bal oldali navigációs ablaktáblán kattintson **Tallózás**.
2. Válassza ki **Notification Hubs**, majd keresse meg a hub szeretné használni a mobilszolgáltatáshoz. Olvassa el a [Windows Store bevezető oktatóanyag](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) Ha segítségre van szüksége egy új értesítési központ létrehozása.
3. Válassza ki **beállítások**.
4. Kattintson a **hozzáférési házirendek**. Mindkét megosztott és teljes körű hozzáférést kapcsolati karakterláncok láthatja.

![Azure portál – Notification Hubs](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Akkor is is le a kapcsolati karakterláncot a **Get-AzureSbNamespace** parancsmag által biztosított [Azure PowerShell](/powershell/azureps-cmdlets-docs) vagy a **azure sb névtér megjelenítése** parancsot a [Azure parancssori felület (CLI)](../cli-install-nodejs.md).
> 
> 

## <a name="general-architecture"></a>Általános architektúra
A **NotificationHubService** vezérlőnek az adott eszközök és alkalmazások leküldéses értesítések küldése a következő figyelőobjektum-példányok:

* **Android** -használja a **GcmService** objektum, amely érhető el **notificationHubService.gcm**
* **iOS** -használja a **ApnsService** objektum, amely elérhető a **notificationHubService.apns**
* **Windows Phone** -használja a **MpnsService** objektum, amely érhető el **notificationHubService.mpns**
* **Univerzális Windows Platform** -használja a **WnsService** objektum, amely érhető el **notificationHubService.wns**

### <a name="how-to-send-push-notifications-to-android-applications"></a>Útmutató: az Android-alkalmazások leküldéses értesítések küldése
A **GcmService** objektum tartalmaz egy **küldése** módszer, amely a leküldéses értesítések küldésére Android-alkalmazásokban használható. A **küldése** metódus a következő paramétereket fogadja:

* **Címkék** -a címke azonosítója. Ha nincs címke van megadva, minden ügyfél elküldi az értesítést.
* **Hasznos** – az üzenet JSON vagy raw hasznos adatcsomag található.
* **A visszahívási** -a visszahívási függvény.

Az adattartalom formátuma további információkért tekintse meg a **hasznos** szakasza a [GCM-kiszolgáló végrehajtási](http://developer.android.com/google/gcm/server.html#payload) dokumentum.

A következő kódban a **GcmService** példány által elérhetővé tett a **NotificationHubService** leküldéses értesítés küldése az összes regisztrált ügyfelek számára.

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

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Hogyan: leküldéses értesítések küldése iOS-alkalmazások
Ugyanaz, mint a fent említett Android alkalmazásokkal a **ApnsService** objektum tartalmaz egy **küldése** módszer, amely leküldéses értesítéseket küldeni az iOS-alkalmazásokhoz használható. A **küldése** metódus a következő paramétereket fogadja:

* **Címkék** -a címke azonosítója. Ha nincs címke van megadva, minden ügyfél elküldi az értesítést.
* **Hasznos** – az üzenet JSON vagy karakterlánc hasznos.
* **A visszahívási** -a visszahívási függvény.

További információ az adattartalom formátuma: A **értesítési tartalom** szakasza a [helyi és leküldéses értesítések programozásával foglalkozó útmutatójában](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) dokumentum.

A következő kódban a **ApnsService** példány által elérhetővé tett a **NotificationHubService** riasztási üzenet küldhető összes ügyfelére:

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
         // notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Útmutató: Windows Phone-alkalmazások leküldéses értesítések küldése
A **MpnsService** objektum biztosít egy **küldése** módszer, amely a leküldéses értesítések küldésére Windows Phone-alkalmazások is használható. A **küldése** metódus a következő paramétereket fogadja:

* **Címkék** -a címke azonosítója. Ha nincs címke van megadva, minden ügyfél elküldi az értesítést.
* **Hasznos** -XML üzenetadatokat.
* **TargetName**  -  `toast` bejelentési értesítéseket. `token` a csempe értesítések.
* **NotificationClass** -értesítés prioritását. Tekintse meg a **HTTP-fejléc elemek** szakasza a [leküldéses értesítések kiszolgálóról](http://msdn.microsoft.com/library/hh221551.aspx) érvényes értékek a dokumentum.
* **Beállítások** – nem kötelező kérelem fejlécei.
* **A visszahívási** -a visszahívási függvény.

Érvényes listája **TargetName**, **NotificationClass** és fejléc lehetőségekről, tekintse meg a [leküldéses értesítések kiszolgálóról](http://msdn.microsoft.com/library/hh221551.aspx) lap.

A következő mintát kódot használja a **MpnsService** példány által elérhetővé tett a **NotificationHubService** bejelentési leküldéses értesítések küldéséhez:

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Útmutató: az univerzális Windows Platform (UWP) alkalmazások leküldéses értesítések küldése
A **WnsService** objektum tartalmaz egy **küldése** univerzális Windows Platform-alkalmazások leküldéses értesítések küldésére szolgáló módszert.  A **küldése** metódus a következő paramétereket fogadja:

* **Címkék** -a címke azonosítója. Ha nincs címke van megadva, minden regisztrált ügyfél elküldi az értesítést.
* **Hasznos** -XML üzenetadatokat.
* **Típus** -értesítés típusát.
* **Beállítások** – nem kötelező kérelem fejlécei.
* **A visszahívási** -a visszahívási függvény.

Az érvényes típusok és kérelemfejléc listájáért lásd: [leküldéses értesítési szolgáltatás kérés- és válaszfejlécekről](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

A következő kódban a **WnsService** példány által elérhetővé tett a **NotificationHubService** egy bejelentési leküldéses értesítést küldeni egy UWP-alkalmazást:

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
         // notification sent
      }
    });

## <a name="next-steps"></a>További lépések
A fenti példa kódtöredékek egyszerűen hozhatók létre a szolgáltatás-infrastruktúrát, hogy leküldéses értesítéseket az eszközök széles körének teszik lehetővé. Most, hogy megismerte a Notification Hubs használata node.js alapjait, az alábbi hivatkozásokból tudhat meg többet hogyan bővítheti a további ezeket a képességeket.

* Lásd az MSDN mutató hivatkozás [az Azure Notification hubs használatával](https://msdn.microsoft.com/library/azure/jj927170.aspx).
* Látogasson el a [Azure SDK for csomópont] GitHub tárházából további mintákat és megvalósítás részletei.

[Azure SDK for csomópont]: https://github.com/WindowsAzure/azure-sdk-for-node
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
[SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[webhelyet a WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure Portal]: https://portal.azure.com
