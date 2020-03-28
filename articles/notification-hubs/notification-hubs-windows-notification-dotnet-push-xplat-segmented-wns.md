---
title: Értesítések küldése adott eszközökre (Univerzális Windows-platform) | Microsoft Docs
description: A regisztráció címkéi révén az Azure Notification Hubs használatával friss híreket küldhet Univerzális Windows-platformalkalmazásokba.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 9151870836b1a616a79e54275ed185a425c11f0c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72385603"
---
# <a name="tutorial-send-notifications-to-specific-devices-running-universal-windows-platform-applications"></a>Oktatóanyag: Értesítések küldése univerzális Windows Platform-alkalmazásokat futtató eszközökre

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Értesítési központokat a legfrissebb hírekről szóló értesítések közvetítésére. Ez az oktatóanyag a Windows Áruházbeli vagy a Nem Silverlight-alapú Windows Phone 8.1-es (nem Silverlight) alkalmazásokat ismerteti. Ha a Windows Phone 8.1 Silverlight szolgáltatást célozza meg, olvassa el az [Értesítések leküldéses értesítéseket az Azure Notification Hubs használatával adott Windows Phone-eszközökre.](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)

Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure Értesítési központokat az értesítések leküldéses adott Windows-eszközök egy univerzális Windows-platform (UWP) alkalmazás használatával. Miután befejezte az oktatóanyagot, regisztrálhat az önt érdeklő legfrissebb hírek kategóriáira. Csak ezekre a kategóriákra vonatkozó leküldéses értesítéseket fog kapni.

A szórásos forgatókönyvek engedélyezéséhez adjon meg egy vagy több *címkét,* amikor regisztrációt hoz létre az értesítési központban. Amikor értesítéseket küld egy címkének, a címkéhez regisztrált összes eszköz megkapja az értesítést. A címkékről további információt az [Útválasztás és címkekifejezések című témakörben talál.](notification-hubs-tags-segment-push-message.md)

> [!NOTE]
> A Visual Studio 2019 nem támogatja a Windows Áruház és a Windows Phone 8.1-es és korábbi verzióit. További információ: [Visual Studio 2019 Platform Célzás and Compatibility](/visualstudio/releases/2019/compatibility).

Ebben az oktatóanyagban a következő feladatokat kell elvégeznie:

> [!div class="checklist"]
> * Választott kategória hozzáadása a mobilalkalmazáshoz
> * Regisztráció értesítésekre
> * Címkézett értesítések küldése
> * Az alkalmazás futtatása és értesítések létrehozása

## <a name="prerequisites"></a>Előfeltételek

Ezen oktatóanyag megkezdése előtt végezze el az [Oktatóanyag: Értesítések küldése az Univerzális Windows-platformon alapuló alkalmazásoknak az Azure Notification Hubs használatával][get-started] című oktatóanyagot.  

## <a name="add-category-selection-to-the-app"></a>Kategóriaválasztó hozzáadása az alkalmazáshoz

Az első lépésben hozzá kell adni a felhasználói felületi elemeket a meglévő főoldalhoz, amely lehetővé teszi a felhasználó számára a regisztrálni kívánt kategóriák kiválasztását. A kiválasztott kategóriákat az eszköz tárolja. Amikor az alkalmazás elindul, létrehoz egy eszközregisztrációt az értesítési központban, a kiválasztott kategóriákat címkékként.

1. Nyissa meg a *MainPage.xaml* projektfájlt, majd `Grid` másolja a következő kódot az elembe:

    ```xml
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
    ```

1. A **Megoldáskezelőben**kattintson a jobb gombbal a projektre, és válassza az**Osztály** **hozzáadása parancsot.** >  Az **Új elem hozzáadása csoportban**nevezze el az értesítések *osztályt,* és válassza a **Hozzáadás**lehetőséget. Ha szükséges, `public` adja hozzá a módosítót az osztálydefinícióhoz.

1. Adja hozzá `using` a következő állításokat az új fájlhoz:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

1. Másolja a következő kódot `Notifications` az új osztályba:

    ```csharp
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
    ```

    Ez az osztály a helyi tárolóban tárolja a hírkategóriákat, amelyeket ennek az eszköznek meg kell kapnia. Ahelyett, hogy `RegisterNativeAsync` meghívja `RegisterTemplateAsync` a metódust, hívja meg a kategóriák regisztrálását egy sablon regisztrációjával.

    Ha egynél több sablont szeretne regisztrálni, adjon meg egy sablonnevet, például *simpleWNSTemplateExample*. A sablonok elnevezése lehetővé teszi a frissítésüket vagy törlésüket. Előfordulhat, hogy egynél több sablont regisztrál, hogy egy bejelentési értesítéshez és egy csempéhez legyen.

    >[!NOTE]
    > Az értesítési központok segítségével az eszközök több sablont is regisztrálhatnak ugyanazzal a címkével. Ebben az esetben egy bejövő üzenet, amely a címkét célozza, azt eredményezi, hogy több értesítés érkezik az eszközre, mindegyik sablonhoz. Ez a folyamat lehetővé teszi, hogy ugyanazt az üzenetet több vizuális értesítésben is megjelenítse, például jelvényként és bejelentési értesítésként egy Windows Áruházbeli alkalmazásban.

    További információért lásd a [Sablonok](notification-hubs-templates-cross-platform-push-messages.md) szakaszt.

1. A *App.xaml.cs* projektfájlban adja hozzá `App` a következő tulajdonságot az osztályhoz:

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    Ezzel a tulajdonsággal hozhat `Notifications` létre és érhet el egy példányt.

    A `<hub name>` és a `<connection string with listen access>` helyőrzőt cserélje le a kódban az értesítési központ nevére és a **DefaultListenSharedAccessSignature** korábban beszerzett kapcsolati sztringjére.

   > [!NOTE]
   > Mivel az ügyfélalkalmazással terjesztett hitelesítő adatok általában nem biztonságosak, csak a *figyelési* hozzáférés kulcsát terjessze az ügyfélalkalmazással. A figyelési hozzáférés lehetővé teszi, hogy az alkalmazás regisztráljon értesítésekre, a meglévő regisztrációkat azonban nem lehet módosítani, és értesítéseket sem lehet küldeni. A teljes körű hozzáférési kulcsot egy biztonságos háttérszolgáltatásban használja a rendszer értesítések kiküldésére és a meglévő regisztrációk módosítására.

1. A *MainPage.xaml.cs* fájlban adja hozzá a következő sort:

    ```csharp
    using Windows.UI.Popups;
    ```

1. A *MainPage.xaml.cs* fájlban adja hozzá a következő módszert:

    ```csharp
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
    ```

    Ez a módszer létrehoz egy listát `Notifications` a kategóriákról, és az osztály segítségével tárolja a listát a helyi tárolóban. Ezenkívül a megfelelő címkéket is regisztrálja az értesítési központban. Amikor a kategóriák megváltoznak, a regisztráció újra létrejön az új kategóriákkal.

Az alkalmazás most már képes egy kategóriakészlet tárolására az eszköz helyi tárterületén. Az alkalmazás regisztrálja az értesítési központban, ha egy felhasználó módosítja a kiválasztott kategóriáit.

## <a name="register-for-notifications"></a>Regisztráció értesítésekre

Ebben a szakaszban elvégzi az értesítési központban való regisztrációt az indításkor, a helyi tárterületen tárolt kategóriák használatával.

> [!NOTE]
> Mivel a Windows Notification Service (WNS) által hozzárendelt csatorna URI bármikor megváltozhat, gyakran regisztráljon az értesítésekre, hogy elkerülhesse az értesítési hibákat. Ebben a példában a rendszer az alkalmazás minden egyes indításakor regisztrál az értesítésekre. A gyakran futtatott alkalmazások, például naponta többször, akkor valószínűleg kihagyja regisztráció sávszélesség megőrzése érdekében, ha kevesebb, mint egy nap telt el az előző regisztráció óta.

1. Ha az `notifications` osztályt kategóriák alapján szeretne feliratkozni, nyissa `InitNotificationsAsync` meg a *App.xaml.cs* fájlt, majd frissítse a metódust.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Ez a folyamat biztosítja, hogy amikor az alkalmazás elindul, lekéri a kategóriákat a helyi tárolóból. Ezután kéri e kategóriák nyilvántartásba vételét. A metódust az `InitNotificationsAsync` Értesítések küldése univerzális Windows [Platform-alkalmazásokba][get-started] az Azure Notification Hubs oktatóanyag használatával hozta létre.
2. A *MainPage.xaml.cs* projektfájlban adja hozzá `OnNavigatedTo` a következő kódot a metódushoz:

    ```csharp
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
    ```

    Ez a kód frissíti a főoldalt, a korábban mentett kategóriák állapota alapján.

Az alkalmazás ezzel elkészült. Az eszköz helyi tárolójában több kategóriát tárolhat. Amikor a felhasználók módosítják a kategóriakiválasztását, a mentett kategóriák az értesítési központtal való regisztrációhoz használatosak. A következő szakaszban meghatároz egy háttérrendszert, amely kategóriaértesítéseket küldhet ennek az alkalmazásnak.

## <a name="run-the-uwp-app"></a>Az UWP alkalmazás futtatása

1. Az alkalmazás lefordításához és indításához nyomja le az F5 billentyűt a Visual Studióban. Az alkalmazás felhasználói felületén váltógombok segítségével választhatja ki, hogy mely kategóriákra szeretne feliratkozni.

   ![Legfrissebb hírek alkalmazás](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breaking-news.png)

1. Engedélyezze egy vagy több kategóriaváltást, majd válassza **a Feliratkozás**lehetőséget.

   Az alkalmazás címkékké alakítja át a kiválasztott kategóriákat, és új eszközregisztrációt kezdeményez az értesítési központban a kiválasztott címkékre vonatkozóan. Az alkalmazás egy párbeszédpanelen jeleníti meg a regisztrált kategóriákat.

    ![Kategória-váltógombok és Előfizetés gomb](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>Konzolalkalmazás létrehozása címkézett értesítések küldéséhez

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>A konzolalkalmazás futtatása címkézett értesítések küldéséhez

Futtassa az előző szakaszban létrehozott alkalmazást. A kijelölt kategóriák értesítései bejelentési értesítésként jelennek meg.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerhette a friss hírek kategóriák szerinti küldését. A háttéralkalmazás leküldi a címkézett értesítéseket olyan eszközökre, amelyek regisztráltak, hogy értesítéseket kapjanak az adott címkéhez. Ha meg szeretné tudni, hogyan küldhet értesítéseket adott felhasználóknak az általuk használt eszköztől függetlenül, olvassa el a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Honosított értesítések leküldése Windows-alkalmazásokba az Azure Értesítési központ használatával](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- URLs.-->
[get-started]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: https://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
