---
title: "Azure Notification Hubs használatával (Universal Windows Platform) legfrissebb hírek elküldése"
description: "A regisztrációhoz címkékkel rendelkező Azure Notification Hubs használatával legfrissebb hírek elküldése univerzális Windows Platform-alkalmazásokhoz."
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: 
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: d510e7e665adec9607aeee80802c466b363d5d5b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-notification-hubs-to-send-breaking-news"></a>A legfrissebb hírek elküldése a Notification Hubs használatával
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Áttekintés
Ez a témakör bemutatja, hogyan Azure Notification Hubs használata a Windows Áruházbeli és Windows Phone 8.1 (nem Silverlight) alkalmazás legfrissebb híreket tartalmazó értesítések közvetíti. Ha a Windows Phone 8.1 Silverlight céloz meg, tekintse meg a [Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) verziója. 

Ez a folyamat befejezése után érdekli breaking news kategóriák regisztrálhatja, és csak ezen kategóriák leküldéses értesítéseket kap. Ez a forgatókönyv esetében gyakori, sok alkalmazások (például RSS-olvasók vagy letilthatja a zenei ventilátorok alkalmazásokat) hol kell-e a értesítést küld a felhasználók, akik érdeklődik azokat a csoportokat. 

Szórási lehetőségeket engedélyezhet egy vagy több-ot *címkék* létrehozásakor regisztráció az értesítési központban. Amikor a rendszer értesítéseket küld egy címkét, akkor a címke regisztrált minden eszköz a értesítést kapni. Mivel a címkékkel egyszerűen csak karakterláncok, nem rendelkeznek előre kell létrehozni. Címkékkel kapcsolatos további információkért lásd: [Notification Hubs az Útválasztás és címke kifejezések](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> 8.1 és korábbi Windows áruház és Windows Phone-projekt verziói nem támogatottak a Visual Studio 2017. További információ: [A Visual Studio 2017 platform célcsoportkezelése és kompatibilitása](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs). 

## <a name="prerequisites"></a>Előfeltételek
Ez a témakör az alkalmazás a épít [Ismerkedés a Notification Hubs][get-started]. Ez az oktatóanyag megkezdése előtt végre kell hajtania [Ismerkedés a Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Kategória kiválasztása hozzáadása az alkalmazáshoz
Az első lépés a felhasználói felületi elemei hozzáadása a meglévő fő lapján, hogy a felhasználók kiválaszthatják a kategóriák regisztrálni. A kiválasztott kategóriákra tárolódnak az eszközön. Az alkalmazás indításakor a eszközregisztráció az értesítési központot, és a kiválasztott kategóriákra címkeként jön létre.

1. Nyissa meg a MainPage.xaml projektfájlt, és másolja az alábbi kód a **rács** elem:
   
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>

2. Kattintson a jobb gombbal a **megosztott** projekt nevű új osztályt **értesítések**, vegye fel a **nyilvános** osztály definíciójához módosító és vegye fel a következő **használatával** utasítást, hogy az új kódfájl:
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.Storage;
        using System.Threading.Tasks;

3. Másolja a következő kódot az új **értesítések** osztály:
   
        private NotificationHub hub;
   
        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }
   
        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            return await SubscribeToCategories(categories);
        }
   
        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }
   
        public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            if (categories == null)
            {
                categories = RetrieveCategories();
            }
   
            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.
   
            const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";
   
            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                    categories);
        }
   
    Ez az osztály a helyi tároló, amely ezt az eszközt úgy kell megkapnia hírek kategóriáinak tárolására használja. Hívása helyett a *RegisterNativeAsync* metódust, hívja *RegisterTemplateAsync* kategóriák sablon regisztráció használatával regisztrálni. 
   
    Egynél több sablont (például egy bejelentési értesítést) és a csempék egy regisztrálni kívánt, mert is adja meg a sablon nevét (például "simpleWNSTemplateExample"). A sablonok nevezze el, hogy a frissítés, vagy törölje őket.
   
    >[!NOTE]
    >Ha egy eszköz több sablon regisztrálja az azonos címkével, amelynek célpontja a címke bejövő üzenet hatására az eszköz (egy mindegyik sablon) küldendő több értesítés is érkezett. Ez a viselkedés akkor hasznos, ha ugyanazon logikai üzenet eredménye több visual értesítéseket (például egy jelvény, mind a bejelentési megjelenítő a Windows Áruházbeli alkalmazások).
   
    További információkért lásd: [sablonok](notification-hubs-templates-cross-platform-push-messages.md).

4. Az App.xaml.cs projektfájlt, adja hozzá a következő tulajdonság a **App** osztály:
   
        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
   
    Ez a tulajdonság létrehozásához és eléréséhez használja a **értesítések** példány.
   
    A kód cserélje le a `<hub name>` és `<connection string with listen access>` helyőrzőket az értesítési központ nevére és a kapcsolati karakterláncot *DefaultListenSharedAccessSignature*, amely korábban beszerzett.
   
   > [!NOTE]
   > Ügyfél alkalmazáshoz elosztott hitelesítő adatok nem általában biztonságos, mert csak a kulcs terjesztése *figyelésére* hozzáférés az ügyfélalkalmazás. Figyelési hozzáféréssel rendelkező az alkalmazás regisztrálhatja az értesítéseket, de meglévő regisztrációját nem lehet módosítani, és értesítések nem küldhető el. A teljes körű hozzáférési kulcs használatban van egy védett háttérszolgáltatásnak értesítések küldését, és meglévő regisztrációk módosítása.
   > 
   > 
5. A MainPage.xaml.cs projekt fájlban adja hozzá a következő sort:
   
        using Windows.UI.Popups;

6. A MainPage.xaml.cs projekt fájlban adja hozzá a következő metódust:
   
        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");
   
            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
   
            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
   
    Ezzel a módszerrel hoz létre a kategóriák és a használja listáját a **értesítések** osztályra, hogy a lista a helyi tárolóban tárolni. Azt is regisztrálja a megfelelő címkéket az értesítési központban. A kategóriák megváltozásakor a regisztrációs újra létrejön az új kategóriát.

Az alkalmazás most tárolhat kategóriák készlete helyi tárterület az eszközön. Az alkalmazás regisztrál az értesítési központban, amikor a felhasználók módosíthatják a kategória kiválasztása.

## <a name="register-for-notifications"></a>Az értesítések regisztrálása
Ebben a szakaszban a helyi tárolóban tárolt kategóriákat használatával regisztrál az értesítési központ indításakor.

> [!NOTE]
> A csatorna URI Azonosítóját, a Windows értesítési szolgáltatásának (WNS) hozzárendelt bármikor módosíthatja, mert az értesítések a notification hibák elkerülése érdekében gyakran kell regisztrálni. Ebben a példában regisztrál az értesítési minden alkalommal, az alkalmazás indítása. Gyakran (több mint naponta egyszer) futó alkalmazások valószínűleg kihagyhatja az regisztrációs Ha kevesebb mint egy nap az előző regisztráció óta eltelt a sávszélesség megőrzése érdekében.
> 
> 

1. Használatához a `notifications` kategóriák alapján osztály előfizetni, nyissa meg az App.xaml.cs fájlt, és frissítse a **InitNotificationsAsync** metódust.
   
        // *** Remove or comment out these lines *** 
        //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
        //var hub = new NotificationHub("your hub name", "your listen connection string");
        //var result = await hub.RegisterNativeAsync(channel.Uri);
   
        var result = await notifications.SubscribeToCategories();
   
    Ez a folyamat biztosítja, hogy az alkalmazás indításakor a kategóriák átmásolja a helyi tároló, ezen kategóriák regisztrálását kéri. Létrehozott a **InitNotificationsAsync** metódus részeként a [Ismerkedés a Notification Hubs] [ get-started] oktatóanyag.

2. A MainPage.xaml.cs projekt fájlban adja hozzá az alábbi kódot a *OnNavigatedTo* módszert:
   
        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();
   
            if (categories.Contains("World")) WorldToggle.IsOn = true;
            if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
            if (categories.Contains("Business")) BusinessToggle.IsOn = true;
            if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
            if (categories.Contains("Science")) ScienceToggle.IsOn = true;
            if (categories.Contains("Sports")) SportsToggle.IsOn = true;
        }
   
    Ez a kód frissíti a fő lapján, a korábban mentett kategóriák állapota alapján.

Az alkalmazás most már befejeződött. Az eszköz helyi tárolójára, amellyel az értesítési központ regisztrálása, amikor a felhasználó módosítja a kategória kiválasztása kategóriák készlete tárolhatja. A következő szakaszban határozza meg, amely ennek az alkalmazásnak kategória értesítéseket küldhet a háttérből.

## <a name="send-tagged-notifications"></a>Címkézett értesítések küldése
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-app-and-generate-notifications"></a>Futtassa az alkalmazást, és értesítések
1. A Visual Studio válassza **F5** fordítsa le és indítsa el az alkalmazást.  
    Az alkalmazás felhasználói felület számos váltógombok, amely lehetővé teszi, hogy válassza ki a előfizetni. 
   
    ![Hírek app megszakítása][1]

2. Egy vagy több kategóriához váltógombok engedélyezése, és kattintson a **előfizetés**.
   
    Az app alakítja át a kiválasztott kategóriákra címkék, és egy új eszköz regisztrálása a kijelölt címkék kéri le az értesítési központban. A regisztrált kategóriák tér vissza, és megjelenik egy párbeszédpanel megnyitásához.
   
    ![Kategória váltógombok és előfizetés gomb][19]

3. A háttérből új értesítés küldése a következő módszerek valamelyikével:

   * **Konzolalkalmazás**: Indítsa el a konzolalkalmazást.
   * **Java/php-ből**: az alkalmazás vagy a parancsfájl futtatásához.
     
     A kiválasztott kategóriákra értesítések bejelentési értesítések jelennek meg.
     
     ![Bejelentési értesítések][14]

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtudta, hogyan kell közvetíteni legfrissebb hírek kategória szerint. Vegye figyelembe a következő oktatóanyag, amely kiemeli a Notification Hubs egy másik speciális helyzetben befejezése:

* [Notification Hubs használatával honosított legfrissebb hírek szórási] Ez az oktatóanyag bemutatja, hogy miként bontsa ki a legfrissebb hírek app honosított értesítések küldését.

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification
[Notification Hubs használatával honosított legfrissebb hírek szórási]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
