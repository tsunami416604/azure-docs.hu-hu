<properties
    pageTitle="Ismerkedés az Azure Notification Hubs univerzális platformon futó Windows-alkalmazásokkal való használatával | Microsoft Azure"
    description="Ebből az oktatóanyagból elsajátíthatja, hogyan használható az Azure Notification Hubs leküldéses értesítések univerzális platformon futó Windows-alkalmazásra történő küldéséhez."
    services="notification-hubs"
    documentationCenter="windows"
    authors="wesmc7777"
    manager="erikre"
    editor="erikre"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="07/21/2016"
    ms.author="wesmc"/>

# Ismerkedés a Notification Hubs univerzális platformon futó Windows-alkalmazásokkal történő használatával

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Áttekintés

Ebből az oktatóanyagból elsajátíthatja, miként használható az Azure Notification Hubs leküldéses értesítések küldéséhez univerzális platformon futó Windows-alkalmazások (UWP-alkalmazások) esetén.

Az oktatóanyagban létrehoz egy üres Windows Áruházbeli alkalmazást, amely leküldéses értesítéseket fogad a Windows leküldéses értesítési szolgáltatásának (WNS) a használatával. Amikor végzett, képes lesz az értesítési központ használatával leküldéses értesítéseket küldeni az alkalmazást futtató összes eszközre.


## Előkészületek

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Az oktatóanyag teljes kódja a GitHubon, [itt](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal) található meg.



##Előfeltételek

Az oktatóanyaghoz az alábbiakra lesz szükség:

+ [Microsoft Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs) vagy újabb

+ [Az univerzális Windows-alkalmazások fejlesztőeszközei telepítve vannak](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)

+ Aktív Azure-fiók <br/>Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-store-dotnet-get-started%2F).

+ Aktív Windows Áruházbeli fiók



Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, univerzális platformon futó Windows-alkalmazásokkal kapcsolatos Notification Hubs-oktatóanyag elvégzéséhez.

##Az alkalmazás regisztrálása a Windows Áruházban

Leküldéses értesítéseknek UWP-alkalmazásokba való küldéséhez társítania kell az alkalmazást a Windows Áruházhoz. Ezután konfigurálnia kell az értesítési központot a WNS-sel való integrációra.

1. Ha még nem regisztrálta az alkalmazást, nyissa meg a [Windows fejlesztőközpontját](https://dev.windows.com/overview), jelentkezzen be Microsoft-fiókjával, majd kattintson az **Új alkalmazás létrehozása** elemre.


2. Írja be az alkalmazás nevét, majd kattintson az **Alkalmazásnév lefoglalása** parancsra.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hubs-win8-app-name.png)

    Ezzel létrehoz egy új Windows Áruházbeli regisztrációt az alkalmazás számára.

3. A Visual Studióban hozzon létre egy új Visual C# Áruházbeli alkalmazások projektet a **Blank App** (Üres alkalmazás) sablonnal, majd kattintson az **OK** gombra.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png)

4. A cél- és a minimális platformverziók esetében fogadja el az alapértelmezett értékeket.


5. A Megoldáskezelőben kattintson a jobb gombbal a Windows Áruházbeli alkalmazás projektjére, kattintson a **Store** (Áruház), majd az **Associate App with the Store...** (Alkalmazás társítása az Áruházzal…) elemre.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png)


    Megjelenik az **Associate Your App with the Windows Store** (Alkalmazás társítása a Windows Áruházzal) varázsló.

6. A varázslóban kattintson a **Sign in** (Bejelentkezés) elemre, és jelentkezzen be Microsoft-fiókjával.

7. Kattintson a 2. lépésben regisztrált alkalmazásra, majd kattintson a **Next** (Tovább), végül az **Associate** (Társítás) gombra.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-app-name.png)

    Ezzel hozzáadja a szükséges Windows Áruházbeli regisztrációs adatokat az alkalmazásjegyzékhez.

8. Visszatérve az új alkalmazás [Windows fejlesztőközpontbeli](http://go.microsoft.com/fwlink/p/?LinkID=266582) oldalára, kattintson a **Szolgáltatások**, a **Leküldéses értesítések**, majd a **Live Services webhely** elemre **A Windows leküldéses értesítési szolgáltatása (WNS) és a Microsoft Azure Mobile Apps** részen.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hubs-uwp-app-live-services.png)

9. Az alkalmazás regisztrációs lapján jegyezze fel az **Alkalmazáskulcs** jelszót és a **Csomag biztonsági azonosítóját (SID)**, amelyek a **Windows Áruház** platformbeállításai között találhatóak meg.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hubs-uwp-app-push-auth.png)


    > [AZURE.WARNING]
    Az alkalmazáskulcs és a csomag biztonsági azonosítója fontos biztonsági hitelesítő adatok. Ezeket az értékeket ne ossza meg senkivel, és ne terjessze az alkalmazással.

##Az értesítési központ konfigurálása

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Válassza ki az <b>Értesítési szolgáltatások</b> és a <b>Windows (WNS)</b> lehetőséget. Ezt követően írja be az <b>Alkalmazáskulcs</b> jelszót a <b>Biztonsági kulcs</b> mezőbe. Adja meg a <b>Csomag biztonsági azonosítójának</b> a WNS-ből az előző szakaszban beszerzett értékét, majd kattintson a <b>Mentés</b> gombra.</p>
</li>
</ol>

&emsp;&emsp;![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)


Az értesítési központ konfigurálva lett a WNS-sel való együttműködésre, és rendelkezik a kapcsolati karakterláncokkal az alkalmazás regisztrálásához és értesítések küldéséhez.

##Az alkalmazás csatlakoztatása az értesítési központhoz

1. A Visual Studióban kattintson a jobb gombbal a megoldásra, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) elemre.

    Ekkor megjelenik a **Manage NuGet Packages** (NuGet-csomagok kezelése) párbeszédpanel.

2. Keresse meg a `WindowsAzure.Messaging.Managed` csomagot, kattintson a **Telepítés** gombra, majd fogadja el a használati feltételeket.

    ![][20]

    A <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet-csomag</a> használatával a rendszer letölti és telepíti a Windows rendszerhez készült Azure üzenetküldési kódtárat, és hozzáad egy rá mutató hivatkozást is.

3. Nyissa meg az App.xaml.cs projektfájlt, majd adja hozzá a következő `using` utasításokat. 

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;



4. Az App.xaml.cs fájlban adja hozzá a következő **InitNotificationsAsync** metódusdefiníciót is az **App** osztályhoz:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("< your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
            var result = await hub.RegisterNativeAsync(channel.Uri);

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }

        }

    Ez a kód lekéri a csatorna URI azonosítóját az alkalmazás számára a WNS-ből, majd regisztrálja a csatorna URI azonosítóját az értesítési központban.

    >[AZURE.NOTE] A „your hub name” helyőrzőt cserélje le az értesítési központ Azure Portalon megjelenő nevére. Továbbá cserélje le a kapcsolati karakterlánc helyőrzőjét az egyik előző szakaszban az értesítési központ **Hozzáférési szabályzatok** oldaláról beszerzett **DefaultListenSharedAccessSignature** kapcsolati karakterláncra.

5. Az App.xaml.cs fájlban található **OnLaunched** eseménykezelő tetején adja hozzá a következő hívást az új **InitNotificationsAsync** metódushoz:

        InitNotificationsAsync();

    Ezzel biztosítja azt, hogy a csatorna URI azonosítója legyen regisztrálva az értesítési központban az alkalmazás minden indításakor.

6. Nyomja le az **F5** billentyűt az alkalmazás futtatásához. Megjelenik egy felugró párbeszédpanel a regisztrációs kulccsal.

    ![][19]




Az alkalmazás készen áll bejelentési értesítéseket fogadni.

##Értesítések küldése 

Az értesítések fogadásának az alkalmazásban való gyors teszteléséhez értesítéseket küldhet az [Azure Portalról](https://portal.azure.com/) az értesítési központ **Tesztküldés** gombjának használatával, az alábbi képernyőn látható módon.

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

A leküldéses értesítések küldése általában olyan háttérszolgáltatásokon keresztül történik egy kompatibilis kódtár használatával, mint a Mobile Services vagy az ASP.NET. Az értesítések küldéséhez használhatja közvetlenül a REST API-t is, ha a háttérszolgáltatáshoz nem érhető el kódtár. 

Ebben az oktatóanyagban az egyszerűbb megoldást választjuk, és az ügyfélalkalmazás tesztelését egy konzolalkalmazás értesítési központjának .NET SDK-ja használatával küldött értesítésekkel mutatjuk be háttérszolgáltatás használata helyett. Az értesítéseknek ASP.NET-háttérrendszerből történő küldéséhez következő lépésként [A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára] oktatóanyagot ajánljuk. Értesítések küldéséhez azonban az alábbi megközelítések is alkalmazhatók:

* **REST-felület**: A [REST-felület](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) használatával bármilyen háttérplatformon támogathatja az értesítéseket.

* **Microsoft Azure Notification Hubs .NET SDK**: A Visual Studio NuGet-csomagkezelőjében futtassa a következő parancsot: [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js**: [A Notification Hub használata a Node.js-ből](notification-hubs-nodejs-push-notification-tutorial.md).

* **Azure Mobile Apps**: A [Leküldéses értesítések hozzáadása Mobile Apps-alkalmazáshoz](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md) témakörben találhat példát arra, hogy hogyan küldhetők értesítések a Notification Hubs szolgáltatással integrált Azure Mobile Apps alkalmazásból.

* **Java/PHP**: „A Notification Hubs használata Javából/PHP-ből” ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)) témakörben találhat példát arra, hogyan küldhetők értesítések a REST API-k használatával.


## (Választható) Értesítések küldése konzolalkalmazásból


Ha .NET-konzolalkalmazás használatával szeretne értesítéseket küldeni, kövesse az alábbi lépéseket. 

1. Kattintson a jobb gombbal a megoldásra, válassza a **Hozzáadás** és az **Új projekt…** elemet, majd a **Visual C#** részen kattintson a **Windows** és a **Konzolalkalmazás** elemre, majd az **OK** gombra.

    ![][13]

    Ezzel hozzáad egy új Visual C#-konzolalkalmazást a megoldáshoz. Ezt egy külön megoldásban is megteheti.

2. A Visual Studióban kattintson az **Eszközök**, a **NuGet Package Manager** (NuGet-csomagkezelő), majd a **Package Manager Console** (Csomagkezelő konzol) elemre.

    Megjelenik a Package Manager Console (Csomagkezelő konzol) ablak a Visual Studióban.

3. A Package Manager Console (Csomagkezelő konzol) ablakban az **Alapértelmezett projekt** értékeként adja meg az új konzolalkalmazás-projektet, majd a konzolablakban hajtsa végre az alábbi parancsot:

        Install-Package Microsoft.Azure.NotificationHubs

    Ezzel hozzáad a <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet-csomagot</a> használó Azure Notification Hubs SDK-ra mutató hivatkozást.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)


4. Nyissa meg a Program.cs fájlt, majd adja hozzá a következő `using` utasítást:

        using Microsoft.Azure.NotificationHubs;

5. A **Program** osztályban adja hozzá a következő metódust:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

    A „hub name” helyőrzőt cserélje le az értesítési központ Azure Portalon megjelenő nevére. Továbbá cserélje le a kapcsolati karakterlánc helyőrzőjét „Az értesítési központ konfigurálása” szakaszban az értesítési központ **Hozzáférési szabályzatok** oldaláról beszerzett **DefaultFullSharedAccessSignature** kapcsolati karakterláncra.

    >[AZURE.NOTE]A kapcsolati karakterláncot **Teljes**, és ne **Figyelési** hozzáféréssel használja. A Figyelési hozzáféréssel rendelkező kapcsolati karakterláncok nem rendelkeznek jogosultsággal értesítések küldéséhez.

6. Adja hozzá a következő sorokat a **Main** metódusban:

         SendNotificationAsync();
         Console.ReadLine();

7. Kattintson a jobb gombbal a konzolalkalmazás-projektre a Visual Studióban, kattintson a **Beállítás kezdőprojektként** elemre a projekt beállításához kezdőprojektként. Ezután nyomja le az **F5** billentyűt az alkalmazás futtatásához.

    ![][14]

    Egy bejelentési értesítést fog kapni az összes regisztrált eszközön. A bejelentési szalagcímre kattintva vagy koppintva a rendszer betölti az alkalmazást.

Az összes támogatott hasznos adatot megtalálja az MSDN [bejelentéskatalógussal], [csempekatalógussal] és a [jelvények áttekintésével] foglalkozó témakörében.

##Következő lépések

Ebben az egyszerű példában értesítéseket küldött az összes Windows rendszerű eszközre a portál vagy egy konzolalkalmazás használatával. Következő lépésként javasoljuk [A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára] oktatóanyag megtekintését. Ebben bemutatjuk, hogy hogyan küldhet értesítéseket ASP.NET-háttérrendszerből adott felhasználók számára címkék használatával.

Ha a felhasználókat érdeklődési körök alapján szeretné szegmentálni, olvassa el a [Use Notification Hubs to send breaking news] (Friss hírek küldése Notification Hubs használatával) című témakört. 

A Notification Hubs használatával kapcsolatban a [Notification Hubs használatával](notification-hubs-push-notification-overview.md) foglalkozó témakörben tekinthet meg további általános információt.






<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->

[A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[A legfrissebb hírek elküldése a Notification Hubs használatával]: notification-hubs-windows-store-dotnet-send-breaking-news.md

[bejelentéskatalógussal]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[csempekatalógussal]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[jelvények áttekintésével]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx



<!--HONumber=sep16_HO1-->


