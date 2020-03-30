---
title: Leküldéses értesítések küldése az Azure Értesítési központokkal és a Node.js
description: Ismerje meg, hogyan küldhet leküldéses értesítéseket az Értesítési központok segítségével egy Node.js alkalmazásból.
keywords: push értesítés,push értesítések,node.js push,ios push
services: notification-hubs
documentationcenter: nodejs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 6e109c5a7f4911893c81c88ae84322fb962fff6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71213192"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Leküldéses értesítések küldése az Azure Értesítési központokkal és a Node.js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Áttekintés

> [!IMPORTANT]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nem rendelkezik fiókkal, hozzon létre egy ingyenes próbafiókot néhány perc alatt az [Azure ingyenes próbaverzión](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs)keresztül.

Ez az útmutató bemutatja, hogyan küldhet leküldéses értesítéseket az Azure Értesítési központok segítségével közvetlenül egy [Node.js](https://nodejs.org) alkalmazásból.

Az érintett forgatókönyvek közé tartozik a leküldéses értesítések küldése az alkalmazásoknak a következő platformokon:

- Android
- iOS
- Univerzális Windows-platform
- Windows Phone

## <a name="notification-hubs"></a>Notification Hubs

Az Azure Értesítési központok könnyen használható, többplatformos, méretezhető infrastruktúrát biztosítanak a leküldéses értesítések mobileszközökre küldéséhez. A szolgáltatási infrastruktúra részleteiről az [Azure Értesítési központok](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx) lapon.

## <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása

Az oktatóanyag első lépése egy új üres Node.js alkalmazás létrehozása. A Node.js alkalmazás létrehozásával kapcsolatos tudnivalókat a [Node.js alkalmazás létrehozása és központi telepítése az Azure webhelyen][nodejswebsite], a [Node.js felhőszolgáltatás][Node.js Cloud Service] a Windows PowerShell használatával vagy [a WebMatrix webhely használatával][webmatrix]című témakörben találja.

## <a name="configure-your-application-to-use-notification-hubs"></a>Az alkalmazás konfigurálása az értesítési központok használatára

Az Azure Notification Hubs használatához le kell töltenie és használnia kell a Node.js [azure csomagot,](https://www.npmjs.com/package/azure)amely a leküldéses értesítési REST-szolgáltatásokkal kommunikáló segítő kódtárak beépített készletét tartalmazza.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>A csomag beszerzéséhez használja a Csomópontcsomag-kezelőt (NPM).

1. Használjon parancssori felületet, például **a PowerShell** (Windows), **a Terminál** (Mac) vagy a **Bash** (Linux) felületet, és keresse meg azt a mappát, amelyben létrehozta az üres alkalmazást.
2. Végrehajtás `npm install azure-sb` a parancsablakban.
3. A vagy `ls` `dir` a parancs manuális futtatásával ellenőrizheti, hogy egy `node_modules` mappa létrejött-e.
4. Ebben a mappában keresse meg az **azure** csomagot, amely tartalmazza az értesítési központ eléréséhez szükséges könyvtárakat.

> [!NOTE]
> Ha többet szeretne megtudni telepítése NPM a hivatalos [NPM blog](https://blog.npmjs.org/post/85484771375/how-to-install-npm).

### <a name="import-the-module"></a>A modul importálása
Szövegszerkesztő használatával adja hozzá az alkalmazást `server.js` a következő szöveget az alkalmazás fájljának tetejéhez:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Azure Notification Hub-kapcsolat beállítása

Az `NotificationHubService` objektum lehetővé teszi az értesítési központok munkáját. A következő kód `NotificationHubService` létrehoz egy objektumot az értesítési központ számára. `hubname` Adja hozzá a `server.js` fájl tetején, miután az utasítás az azure modul importálásához:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Szerezze be `connectionstring` a kapcsolatértékét az [Azure Portalról] az alábbi lépések végrehajtásával:

1. A bal oldali navigációs ablakban kattintson a **Tallózás gombra.**
2. Válassza **az Értesítési központok lehetőséget,** majd keresse meg a mintához használni kívánt központot. Ha segítségre van szüksége egy új értesítési központ létrehozásához, olvassa el a [Windows Áruház első lépéseit bemutatót.](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
3. Válassza a **Beállítások lehetőséget.**
4. Kattintson az **Access Policies elemre.** A megosztott és a teljes hozzáférésű kapcsolati karakterláncok is megjelennek.

![Azure Portal – Értesítési központok](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> A kapcsolati karakterláncot az [Azure PowerShell](/powershell/azureps-cmdlets-docs) által biztosított **Get-AzureSbNamespace** parancsmag vagy az [Azure command-line interface (Azure CLI)](../cli-install-nodejs.md)segítségével az **azure sb névtér show** parancs használatával is lekérheti.

## <a name="general-architecture"></a>Általános architektúra

Az `NotificationHubService` objektum a következő objektumpéldányokat teszi elérhetővé a leküldéses értesítések adott eszközökre és alkalmazásokra való küldéséhez:

- **Android** - `GcmService` használja az objektumot, amely elérhető`notificationHubService.gcm`
- **iOS** - `ApnsService` használja az objektumot, amely elérhető a`notificationHubService.apns`
- **Windows Phone** - `MpnsService` használja az objektumot, amely elérhető a`notificationHubService.mpns`
- **Univerzális Windows platform** `WnsService` - használja az objektumot, amely elérhető a`notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Útmutató: Push értesítések küldése Android-alkalmazásokra

Az `GcmService` objektum `send` olyan módszert biztosít, amely leküldéses értesítések küldésére használható androidos alkalmazásoknak. A `send` metódus a következő paramétereket fogadja el:

- **Címkék** - a címke azonosítója. Ha nincs megadva címke, a rendszer elküldi az értesítést az összes ügyfélnek.
- **Hasznos tartalom** - az üzenet JSON vagy nyers karakterlánc hasznos.
- **Visszahívás** – a visszahívási funkció.

A hasznos adatformátumról további információt a [Hasznos adatról szóló dokumentációban](https://distriqt.github.io/ANE-PushNotifications/m.FCM-GCM%20Payload)talál.

A következő kód `GcmService` a leküldéses értesítés küldésére használja a `NotificationHubService` példányt, hogy leküldéses értesítést küldjön az összes regisztrált ügyfélnek.

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

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Útmutató: Leküldéses értesítések küldése iOS-alkalmazásokba

Ugyanaz, mint az Android `ApnsService` alkalmazások fent `send` leírt, az objektum egy olyan módszert biztosít, amely lehet használni, hogy küldjön leküldéses értesítéseket iOS alkalmazások. A `send` metódus a következő paramétereket fogadja el:

- **Címkék** - a címke azonosítója. Ha nincs megadva címke, a rendszer elküldi az értesítést az összes ügyfélnek.
- **Hasznos tartalom** - az üzenet JSON vagy string hasznos.
- **Visszahívás** – a visszahívási funkció.

A hasznos adat formátumáról további információt a [Helyi és leküldéses értesítési programozási útmutató](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) dokumentum **Értesítési hasznos adat** című szakaszában talál.

A következő kód `ApnsService` a rendszer `NotificationHubService` által elérhetővé tett példány segítségével küld riasztási üzenetet az összes ügyfélnek:

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

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Útmutató: Leküldéses értesítések küldése Windows Phone-alkalmazásokba

Az `MpnsService` objektum `send` olyan módszert biztosít, amely leküldéses értesítések küldésére használható a Windows Phone-alkalmazásoknak. A `send` metódus a következő paramétereket fogadja el:

- **Címkék** - a címke azonosítója. Ha nincs megadva címke, a rendszer elküldi az értesítést az összes ügyfélnek.
- **Hasznos adat** – az üzenet XML-hasznos adata.
- **TargetName**  -  `toast` a bejelentési értesítésekhez. `token`csempeértesítésekhez.
- **NotificationClass** - Az értesítés prioritása. Az érvényes értékeket a [kiszolgálódokumentum leküldéses értesítéseinek](https://msdn.microsoft.com/library/hh221551.aspx) **HTTP-fejlécelemei** szakaszában tekintse meg.
- **Beállítások** – választható kérelemfejlécek.
- **Visszahívás** – a visszahívási funkció.

Az érvényes `TargetName`és `NotificationClass` a fejlécbeállítások listáját a [Kiszolgálóról érkező leküldéses értesítések](https://msdn.microsoft.com/library/hh221551.aspx) című lapon találja.

A következő mintakód a bejelentési leküldéses értesítés küldéséhez használja a `MpnsService` `NotificationHubService` példányt:

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Útmutató: Leküldéses értesítések küldése univerzális Windows platform (UWP) alkalmazásokba

Az `WnsService` objektum `send` olyan módszert biztosít, amely leküldéses értesítések küldésére használható az univerzális Windows Platform-alkalmazásoknak.  A `send` metódus a következő paramétereket fogadja el:

- **Címkék** - a címke azonosítója. Ha nincs megadva címke, az értesítést a rendszer az összes regisztrált ügyfélnek elküldi.
- **Hasznos adat** - az XML-üzenet hasznos adata.
- **Típus** - az értesítés típusa.
- **Beállítások** – választható kérelemfejlécek.
- **Visszahívás** – a visszahívási funkció.

Az érvényes típusok és a kérelemfejlécek listáját a [Leküldéses értesítési szolgáltatáskérés és válaszfejlécek (Leküldéses értesítési szolgáltatáskérés és válaszfejlécek) (Leküldéses értesítési szolgáltatáskérés és válaszfejlécek) (Leküldéses értesítési szolgáltatáskérés és válaszfejlécek) (Leküldéses szolgáltatás](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx)

A következő kód `WnsService` a bejelentési `NotificationHubService` leküldéses értesítés küldésére használja a példányt az UWP-alkalmazásnak:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Következő lépések

A fenti mintakódrészletek lehetővé teszik, hogy könnyedén hozzon létre szolgáltatási infrastruktúrát, hogy leküldéses értesítéseket küldhessenek az eszközök széles köréhez. Most, hogy megtanulta az értesítési központok csomópontokkal való használatának alapjait, kövesse ezeket a hivatkozásokat, hogy többet tudjon meg arról, hogyan bővítheti ezeket a képességeket tovább.

- Tekintse meg az MSDN-referencia [az Azure Értesítési központok.](https://msdn.microsoft.com/library/azure/jj927170.aspx)
- További mintákért és megvalósítási részletekért látogasson el az [Azure SDK] node-tárházra a GitHubon.

[Azure SDK csomóponthoz]: https://github.com/WindowsAzure/azure-sdk-for-node
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
