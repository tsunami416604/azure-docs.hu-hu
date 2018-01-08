---
title: "Ismerkedés az Azure Notification Hubs Windows Phone-alkalmazásokkal való használatával | Microsoft Docs"
description: "Ebben az oktatóanyagban elsajátíthatja, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére Windows Phone 8 és Windows Phone 8.1 rendszeren futó Silverlight-alkalmazásokba."
services: notification-hubs
documentationcenter: windows
keywords: "leküldéses értesítés,leküldéses értesítés,windows phone leküldéses értesítés"
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 7d44d0a0f8683ad6ad55136ad17879e98e26498b
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-windows-phone-apps"></a>Ismerkedés az Azure Notification Hubs Windows Phone-alkalmazásokkal való használatával
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Áttekintés
> [!NOTE]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).
> 
> 

Ez az oktatóanyag azt mutatja be, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére Windows Phone 8 és Windows Phone 8.1 rendszeren futó Silverlight-alkalmazásokba. A Windows Phone 8.1 (nem Silverlight) rendszerre vonatkozó útmutatásért tekintse meg az [univerzális Windows-alkalmazásokkal kapcsolatos](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) verziót.
Az oktatóanyagban létrehoz egy üres Windows Phone 8-alkalmazást, amely leküldéses értesítéseket fogad a Microsoft leküldéses értesítéseket kezelő szolgáltatásának (MPNS) a használatával. Amikor végzett, képes lesz az értesítési központ használatával leküldéses értesítéseket küldeni az alkalmazást futtató összes eszközre.

> [!NOTE]
> A Notification Hubs Windows Phone SDK nem támogatja a Windows leküldéses értesítéseket kezelő szolgáltatásának (WNS) használatát Windows Phone 8.1 Silverlight-alkalmazások esetén. Ha a WNS-t szeretné használni (az MPNS helyett) Windows Phone 8.1 Silverlight-alkalmazásokkal kövesse a [Notification Hubs – Windows Phone Silverlight-oktatóanyagot], amely REST API-kat használ.
> 
> 

Ez az oktatóanyag az egyszerű küldési forgatókönyvet mutatja be a Notification Hubs használatával.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyaghoz az alábbiakra lesz szükség:

* [Windows Phone-hoz készült Visual Studio 2012 Express] vagy újabb verzió.

Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, Windows Phone 8-alkalmazásokkal kapcsolatos Notification Hubs-oktatóanyag elvégzéséhez.

## <a name="create-your-notification-hub"></a>Az értesítési központ létrehozása
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Az <b>Értesítési szolgáltatások</b> szakaszban válassza ki a <b>Windows Phone (MPNS)</b> lehetőséget, majd kattintson az <b>Nem hitelesített leküldéses értesítések engedélyezése</b> jelölőnégyzetre.</p>
</li>
</ol>

&emsp;&emsp;![Azure Portal – Nem hitelesített leküldéses értesítések engedélyezése](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

A szolgáltatás létrehozza a központot, és nem hitelesített értesítések Windows Phone rendszerű eszközökre való küldésére konfigurálja.

> [!NOTE]
> Ez az oktatóanyag nem hitelesített módban használja az MPNS-t. Az MPNS nem hitelesített módja korlátozásokat tartalmaz az egyes csatornákra küldhető értesítések tekintetében. A Notification Hubs tanúsítvány feltöltésének lehetővé tételével támogatja az [MPNS hitelesített módját](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx).
> 
> 

## <a name="connecting-your-app-to-the-notification-hub"></a>Az alkalmazás csatlakoztatása az értesítési központhoz
1. Hozzon létre egy új Windows Phone 8-alkalmazást a Visual Studióban.
   
       ![Visual Studio - New Project - Windows Phone App][13]
   
    A Visual Studio 2013 Update 2 vagy újabb verzióban Windows Phone Silverlight-alkalmazást hozzon létre.
   
    ![Visual Studio – Új projekt – Üres alkalmazás – Windows Phone Silverlight][11]
2. A Visual Studióban kattintson a jobb gombbal a megoldásra, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) elemre.
   
    Ekkor megjelenik a **Manage NuGet Packages** (NuGet-csomagok kezelése) párbeszédpanel.
3. Keresse meg a `WindowsAzure.Messaging.Managed` csomagot, kattintson a **Telepítés** gombra, majd fogadja el a használati feltételeket.
   
    ![Visual Studio – NuGet Package Manager (NuGet-csomagkezelő)][20]
   
    A <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet-csomag</a> használatával a rendszer letölti és telepíti a Windows rendszerhez készült Azure üzenetküldési kódtárat, és hozzáad egy rá mutató hivatkozást is.
4. Nyissa meg az App.xaml.cs fájlt, majd adja hozzá a következő `using` utasításokat:
   
        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
5. Adja hozzá a következő kódot az **Application_Launching** metódus tetején az App.xaml.cs fájlban:
   
        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }
   
        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());
   
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });
   
   > [!NOTE]
   > A **MyPushChannel** értéke egy index, amelynek használatával megkereshető egy létező csatorna a [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx) gyűjteményben. Amennyiben nem létezik ott ilyen, hozzon létre egy bejegyzést ezen a néven.
   > 
   > 
   
    Szúrja be a központ nevét és az előző szakaszban beszerzett, **DefaultListenSharedAccessSignature** nevű kapcsolati karakterláncot.
    Ez a kód lekéri a csatorna URI azonosítóját az alkalmazás számára az MPNS-ből, majd regisztrálja a csatorna URI azonosítóját az értesítési központban. Emellett biztosítja azt, hogy a csatorna URI azonosítója legyen regisztrálva az értesítési központban az alkalmazás minden indításakor.
   
   > [!NOTE]
   > Ez az oktatóanyag egy bejelentési értesítést küld az eszközre. Amikor csempeértesítést küld, a **BindToShellTile** metódust kell meghívnia a csatornán. Ha a bejelentési és a csempeértesítéseket egyaránt támogatni kívánja, hívja meg a **BindToShellTile** és a **BindToShellToast** metódust is.
   > 
   > 
6. A Megoldáskezelőben bontsa ki a **Tulajdonságok** csomópontot, nyissa meg a `WMAppManifest.xml` fájlt, kattintson a **Képességek** fülre, és jelölje be az **ID_CAP_PUSH_NOTIFICATION** képességet.
   
       ![Visual Studio - Windows Phone App Capabilities][14]
   
       This ensures that your app can receive push notifications. Without it, any attempt to send a push notification to the app will fail.
7. Nyomja le az `F5` billentyűt az alkalmazás futtatásához.
   
    Megjelenik egy regisztrációs üzenet az alkalmazásban.
8. Zárja be az alkalmazást.  
   
   > [!NOTE]
   > A bejelentési leküldéses értesítések fogadásához az alkalmazás nem futhat az előtérben.
   >

## <a name="next-steps"></a>További lépések
Ebben az egyszerű példában leküldéses értesítéseket küldött az összes Windows Phone 8 rendszerű eszközre. 

Ha adott felhasználóknak szeretne értesítést küldeni, tekintse meg [A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára] oktatóanyagot. 

Ha a felhasználókat érdeklődési körök alapján szeretné szegmentálni, olvassa el a [Use Notification Hubs to send breaking news] (Friss hírek küldése Notification Hubs használatával) című témakört. 

A Notification Hubs használatával kapcsolatban a [Notification Hubs használatával] foglalkozó témakörben tekinthet meg további információt.

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Windows Phone-hoz készült Visual Studio 2012 Express]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Notification Hubs használatával]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[toast catalog]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[tile catalog]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notification Hubs – Windows Phone Silverlight-oktatóanyagot]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

