---
title: Leküldéses értesítések küldése az Azure Notification Hubs használatával Windows Phone rendszerű eszközökre | Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére Windows Phone 8 és Windows Phone 8.1 rendszeren futó Silverlight-alkalmazásokba.
services: notification-hubs
documentationcenter: windows
keywords: leküldéses értesítés,leküldéses értesítés,windows phone leküldéses értesítés
author: wesmc7777
manager: erikre
editor: erikre

ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: wesmc

---
# Leküldéses értesítések küldése az Azure Notification Hubs használatával Windows Phone rendszerű eszközökre
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## Áttekintés
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

## Előfeltételek
Az oktatóanyaghoz az alábbiakra lesz szükség:

* [Windows Phone-hoz készült Visual Studio 2012 Express] vagy újabb verzió.

Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, Windows Phone 8-alkalmazásokkal kapcsolatos Notification Hubs-oktatóanyag elvégzéséhez.

## Az értesítési központ létrehozása
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Kattintson a <b>Notification Services</b> (Értesítési szolgáltatások) szakaszra (a <i>Beállítások</i> között), majd a <b>Windows Phone (MPNS)</b> lehetőségre, végül jelölje be az <b>Enable unauthenticated push</b> (Nem hitelesített leküldés engedélyezése) jelölőnégyzetet.</p>
</li>
</ol>

&emsp;&emsp;![Azure portál – Nem hitelesített leküldéses értesítések engedélyezése](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

A szolgáltatás létrehozza a központot, és nem hitelesített értesítések Windows Phone rendszerű eszközökre való küldésére konfigurálja.

> [!NOTE]
> Ez az oktatóanyag nem hitelesített módban használja az MPNS-t. Az MPNS nem hitelesített módja korlátozásokat tartalmaz az egyes csatornákra küldhető értesítések tekintetében. A Notification Hubs tanúsítvány feltöltésének lehetővé tételével támogatja az [MPNS hitelesített módját](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx).
> 
> 

## Az alkalmazás csatlakoztatása az értesítési központhoz
1. Hozzon létre egy új Windows Phone 8-alkalmazást a Visual Studióban.
   
    ![Visual Studio – Új projekt – Windows Phone-alkalmazás][13]
   
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
   
    ![Visual Studio – Windows Phone-alkalmazás képességei][14]
   
    Ezzel biztosítja, hogy az alkalmazás képes legyen leküldéses értesítések fogadására. Ennek hiányában a leküldéses üzeneteknek az alkalmazás számára történő küldésére tett minden kísérlet meghiúsul.
7. Nyomja le az `F5` billentyűt az alkalmazás futtatásához.
   
    Megjelenik egy regisztrációs üzenet az alkalmazásban.
8. Zárja be az alkalmazást.  
   
   > [!NOTE]
   > A bejelentési leküldéses értesítések fogadásához az alkalmazás nem futhat az előtérben.
   > 
   > 

## Leküldéses értesítések küldése a háttérrendszerből
A Notification Hubs használatával bármilyen háttérrendszerből küldhet leküldéses értesítést a nyilvános <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST-felületen</a> keresztül. Ebben az oktatóanyagban leküldéses értesítéseket küld egy .NET-konzolalkalmazás használatával. 

Az [Azure Notification Hubs Notify Users with .NET backend](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) (Azure Notification Hubs – felhasználók értesítése .NET-háttérrendszerrel) című témakörben találhat példát arra, hogy hogyan küldhetők leküldéses értesítések a Notification Hubs szolgáltatással integrált ASP.NET WebAPI háttérrendszerből.  

A [How to use Notification Hubs from Java](notification-hubs-java-push-notification-tutorial.md) (A Notification Hubs használata Javából) és a [How to use Notification Hubs from PHP](notification-hubs-php-push-notification-tutorial.md) (A Notification Hubs használata PHP-ből) című témakörben találhat példát arra, hogy hogyan küldhetők leküldéses értesítések a [REST API-k](https://msdn.microsoft.com/library/azure/dn223264.aspx) használatával.

1. Kattintson a jobb gombbal a megoldásra, válassza a **Hozzáadás** és az **Új projekt…** elemet, majd a **Visual C#** részen kattintson a **Windows** és a **Konzolalkalmazás** elemre, majd az **OK** gombra.
   
    ![Visual Studio – Új projekt – Konzolalkalmazás][6]
   
    Ezzel hozzáad egy új Visual C#-konzolalkalmazást a megoldáshoz. Ezt egy külön megoldásban is megteheti.
2. Kattintson az **Eszközök**, a **Library Package Manager** (Kódtár-csomagkezelő), majd a **Package Manager Console** (Csomagkezelő konzol) elemre.
   
    Megjelenik a Package Manager Console (Csomagkezelő konzol) ablak.
3. A **Package Manager Console** (Csomagkezelő konzol) ablakban az **Alapértelmezett projekt** értékeként adja meg az új konzolalkalmazás-projektet, majd a konzolablakban hajtsa végre az alábbi parancsot:
   
       Install-Package Microsoft.Azure.NotificationHubs
   
   Ezzel hozzáad a <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet-csomagot</a> használó Azure Notification Hubs SDK-ra mutató hivatkozást.
4. Nyissa meg a `Program.cs` fájlt, és adja hozzá a következő `using` utasítást:
   
        using Microsoft.Azure.NotificationHubs;
5. A `Program` osztályban adja hozzá a következő metódust:
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }
   
    A `<hub name>` helyőrzőt cserélje le az értesítési központnak a portálon megjelenő nevére. Továbbá cserélje le a kapcsolati karakterlánc helyőrzőjét „Az értesítési központ konfigurálása” szakaszban beszerzett **DefaultFullSharedAccessSignature** nevű kapcsolati karakterláncra.
   
   > [!NOTE]
   > A kapcsolati karakterláncot **Teljes**, és ne **Figyelési** hozzáféréssel használja. A Figyelési hozzáféréssel rendelkező kapcsolati karakterláncok nem rendelkeznek jogosultsággal leküldéses értesítések küldéséhez.
   > 
   > 
6. Adja hozzá a következő sort a `Main` metódusban:
   
         SendNotificationAsync();
         Console.ReadLine();
7. Futtassa a Windows Phone-emulátort, és zárja be az alkalmazást, majd állítsa be a konzolalkalmazás-projektet az alapértelmezett indulási projektként, végül nyomja le az `F5` billentyűt az alkalmazás futtatásához.
   
    Egy bejelentési leküldéses értesítést fog kapni. A bejelentési szalagcímre kattintva a rendszer betölti az alkalmazást.

Az összes lehetséges hasznos adatot megtalálja az MSDN [bejelentéskatalógussal] és [csempekatalógussal] foglalkozó témakörében.

## Következő lépések
Ebben az egyszerű példában leküldéses értesítéseket küldött az összes Windows Phone 8 rendszerű eszközre. 

Ha adott felhasználóknak szeretne értesítést küldeni, tekintse meg [A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára] oktatóanyagot. 

Ha a felhasználókat érdeklődési körök alapján szeretné szegmentálni, olvassa el a [Use Notification Hubs to send breaking news](Friss hírek küldése Notification Hubs használatával.md) című témakört. 

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
[Az MPNS hitelesített módja]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[A legfrissebb hírek elküldése a Notification Hubs használatával]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[bejelentéskatalógussal]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[csempekatalógussal]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notification Hubs – Windows Phone Silverlight-oktatóanyagot]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp




<!--HONumber=Oct16_HO1-->


