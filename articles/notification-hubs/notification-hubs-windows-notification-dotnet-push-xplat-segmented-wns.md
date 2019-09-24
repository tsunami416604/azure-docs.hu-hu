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
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: efe668e42e04942cc0d9fc99670057ab5bdd302a
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212127"
---
# <a name="tutorial-push-notifications-to-specific-windows-devices-running-universal-windows-platform-applications"></a>Oktatóanyag: Leküldéses értesítések küldése Univerzális Windows-platform alkalmazást futtató adott Windows-eszközökön

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt mutatja be, hogyan használható az Azure Notification Hubs a legfrissebb hírekről szóló értesítések küldésére Windows Áruházbeli és Windows Phone 8.1 rendszeren futó (nem Silverlight) alkalmazásokba. A Windows Phone 8.1 Silverlight rendszerre vonatkozó útmutatásért tekintse meg a [Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) rendszerrel kapcsolatos verziót.

Ebből az oktatóanyagból elsajátíthatja, hogyan használható az Azure Notification Hubs leküldéses értesítések olyan adott Windows-eszközökre történő küldéséhez, amelyek Univerzális Windows-platform (UWP-) alkalmazásokat futtatnak. Ha elvégezte az oktatóanyagot, regisztrálhat a friss hírek Önt érdeklő kategóriáira, és csak ezekhez a kategóriákhoz tartozó leküldéses értesítéseket fog kapni.

A közvetítési forgatókönyveket úgy lehet engedélyezni, ha az értesítési központban a regisztráció létrehozásakor hozzáad egy vagy több *címkét*. Ha az értesítések küldése egy címkére történik, az adott címkére regisztrált eszközök mindegyike megkapja az értesítést. A címkékkel kapcsolatban [a regisztrációkban használt címkékkel](notification-hubs-tags-segment-push-message.md) foglalkozó témakörben tekinthet meg további információt.

> [!NOTE]
> A Windows Áruház, valamint a Windows Phone 8.1-es és korábbi verziójú projektek nem támogatottak a Visual Studio 2017-ben. További információ: [A Visual Studio 2017 platform célcsoportkezelése és kompatibilitása](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Választott kategória hozzáadása a mobilalkalmazáshoz
> * Regisztráció értesítésekre
> * Címkézett értesítés küldése
> * Az alkalmazás futtatása és értesítések létrehozása

## <a name="prerequisites"></a>Előfeltételek

Fejezze be [az oktatóanyagot: Az oktatóanyag megkezdése előtt küldjön értesítéseket][get-started] univerzális Windows-platform alkalmazásoknak az Azure Notification Hubs használatával.  

## <a name="add-category-selection-to-the-app"></a>Kategóriaválasztó hozzáadása az alkalmazáshoz

Az első lépésben hozzá kell adni a felhasználói felületi elemeket a meglévő főoldalhoz, amely lehetővé teszi a felhasználó számára a regisztrálni kívánt kategóriák kiválasztását. A kiválasztott kategóriákat az eszköz tárolja. Az alkalmazás indításakor egy eszközregisztráció jön létre az értesítési központban, amely címkeként tartalmazza a választott kategóriákat.

1. Nyissa meg a Főoldal. XAML projektfájlt, majd másolja a következő kódot `Grid` a elembe:

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

2. **Megoldáskezelőban**kattintson a jobb gombbal a projektre, és adjon hozzá egy új osztályt: **Értesítések**. Adja hozzá a **nyilvános** módosítót az osztály definícióhoz, majd adja hozzá a `using` következő utasításokat az új kódhoz:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

3. Másolja az alábbi kódot az új `Notifications` osztályba:

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

    Ez az osztály a helyi tárolóban tárolja a hírkategóriákat, amelyeket ennek az eszköznek meg kell kapnia. A `RegisterNativeAsync` metódus meghívása helyett hívja `RegisterTemplateAsync` meg a regisztrációt a kategóriákhoz a sablon regisztrációjának használatával.

    Ha egynél több sablont kíván regisztrálni (például egy bejelentési értesítésekhez és egy csempékhez tartozó sablont), adjon meg egy sablonnevet (például „simpleWNSTemplateExample”). A sablonok elnevezése lehetővé teszi a frissítésüket vagy törlésüket.

    >[!NOTE]
    >Ha egy eszköz több sablont regisztrál azonos címkével, az adott címkét célzó bejövő üzenetekről több értesítés is érkezik az adott eszközre (sablononként egy). Ez a viselkedés akkor hasznos, ha egyetlen logikai üzenetnek több látható értesítést is kell eredményeznie, (például egy jelvény megjelenítését és egy bejelentést egy Windows Áruházbeli alkalmazásban).

    További információért lásd a [Sablonok](notification-hubs-templates-cross-platform-push-messages.md) szakaszt.

4. A app.XAML.cs projekt fájljában adja hozzá a következő tulajdonságot a `App` osztályhoz:

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    Ezt a tulajdonságot használja egy `Notifications` példány létrehozásához és eléréséhez.

    A `<hub name>` és a `<connection string with listen access>` helyőrzőt cserélje le a kódban az értesítési központ nevére és a *DefaultListenSharedAccessSignature* korábban beszerzett kapcsolati sztringjére.

   > [!NOTE]
   > Mivel az ügyfélalkalmazással terjesztett hitelesítő adatok általában nem biztonságosak, csak a *figyelési* hozzáférés kulcsát terjessze az ügyfélalkalmazással. A figyelési hozzáférés lehetővé teszi, hogy az alkalmazás regisztráljon értesítésekre, a meglévő regisztrációkat azonban nem lehet módosítani, és értesítéseket sem lehet küldeni. A teljes körű hozzáférési kulcsot egy biztonságos háttérszolgáltatásban használja a rendszer értesítések kiküldésére és a meglévő regisztrációk módosítására.

5. `MainPage.xaml.cs` A fájlban adja hozzá a következő sort:

    ```csharp
    using Windows.UI.Popups;
    ```

6. `MainPage.xaml.cs` A fájlban adja hozzá a következő metódust:

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

    Ez a módszer kategóriákat hoz létre, és a `Notifications` osztály használatával tárolja a listát a helyi tárolóban. Ezenkívül a megfelelő címkéket is regisztrálja az értesítési központban. A kategóriák módosításakor a rendszer újra létrehozza a regisztrációt az új kategóriákkal.

Az alkalmazás most már képes egy kategóriakészlet tárolására az eszköz helyi tárterületén. Az alkalmazás regisztrálja az értesítési központban, ha egy felhasználó módosítja a kiválasztott kategóriáit.

## <a name="register-for-notifications"></a>Regisztráció értesítésekre

Ebben a szakaszban elvégzi az értesítési központban való regisztrációt az indításkor, a helyi tárterületen tárolt kategóriák használatával.

> [!NOTE]
> Mivel a Windows Notification Service (WNS) által hozzárendelt csatorna URI bármikor megváltozhat, gyakran regisztráljon az értesítésekre, hogy elkerülhesse az értesítési hibákat. Ebben a példában a rendszer az alkalmazás minden egyes indításakor regisztrál az értesítésekre. Gyakran (naponta egynél többször) futtatott alkalmazások esetében a sávszélesség megtartása érdekében akár ki is hagyhatja a regisztrációt, ha kevesebb mint egy nap telt el az előző regisztráció óta.

1. Ha az `notifications` osztályt a kategóriák alapján szeretné használni, nyissa meg a app.XAML.cs fájlt, és frissítse `InitNotificationsAsync` a metódust.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Ez a folyamat biztosítja, hogy az alkalmazás indításkor lekérje a kategóriákat a helyi tárterületből, és regisztrációt kérelmezzen ezekre a kategóriákra vonatkozóan. A `InitNotificationsAsync` metódust az első [lépések Notification Hubs][get-started] oktatóanyag részeként hozta létre.
2. A Project fájlban adja hozzá a következő kódot `OnNavigatedTo` a metódushoz: `MainPage.xaml.cs`

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

Az alkalmazás ezzel elkészült. Képes egy kategóriakészlet tárolására az eszköz helyi tárterületén, illetve az értesítési központban való regisztrálásra, ha a felhasználó módosítja a választott kategóriákat. A következő szakaszban meghatároz egy háttérrendszert, amely kategóriaértesítéseket küldhet ennek az alkalmazásnak.

## <a name="run-the-uwp-app"></a>A UWP alkalmazás futtatása 
1. Az alkalmazás lefordításához és indításához nyomja le az **F5** billentyűt a Visual Studióban. Az alkalmazás felhasználói felületén váltógombok segítségével választhatja ki, hogy mely kategóriákra szeretne feliratkozni.

    ![Legfrissebb hírek alkalmazás](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png)

2. Válasszon ki egy vagy több kategória-váltógombot, majd kattintson a **Subscribe** (Feliratkozás) elemre.

    Az alkalmazás címkékké alakítja át a kiválasztott kategóriákat, és új eszközregisztrációt kezdeményez az értesítési központban a kiválasztott címkékre vonatkozóan. A rendszer visszaadja, és egy párbeszédablakban jeleníti meg a regisztrált kategóriákat.

    ![Kategória-váltógombok és Előfizetés gomb](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>Konzolos alkalmazás létrehozása címkézett értesítések küldéséhez

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>A konzol alkalmazás futtatása címkézett értesítések küldéséhez

1. Futtassa az előző szakaszban létrehozott alkalmazást.
2. A kijelölt kategóriák értesítései bejelentési értesítésként jelennek meg. Ha az értesítést választja, megjelenik az első UWP-alkalmazás ablaka. 

     ![Bejelentési értesítések](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png)


## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerhette a friss hírek kategóriák szerinti küldését. A háttéralkalmazás címkézett értesítéseket küld azokra az eszközökre, amelyek az adott címkéhez tartozó értesítések fogadására vannak regisztrálva. Ha szeretné megtudni, hogyan küldhet leküldéses értesítéseket adott felhasználóknak az általuk használt eszköztől függetlenül, lépjen tovább a következő oktatóanyagra:

> [!div class="nextstepaction"]
> [Honosított leküldéses értesítések küldése](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

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
