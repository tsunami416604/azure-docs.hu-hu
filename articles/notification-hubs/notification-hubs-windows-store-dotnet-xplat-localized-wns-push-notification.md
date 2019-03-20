---
title: Honosított értesítések küldése Windows-alkalmazásokba az Azure Notification Hubs használatával | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogy hogyan küldhet honosított értesítéseket a friss hírekről az Azure Notification Hubs használatával.
services: notification-hubs
documentationcenter: windows
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: c454f5a3-a06b-45ac-91c7-f91210889b25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: b4f308e7053e000115f22abd291d934d90c11a94
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57837592"
---
# <a name="tutorial-push-localized-notifications-to-windows-apps-by-using-azure-notification-hubs"></a>Oktatóanyag: Honosított leküldéses értesítések küldésére Windows-alkalmazások az Azure Notification Hubs használatával

> [!div class="op_single_selector"]
> * [Windows Áruház C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt mutatja be, hogy hogyan lehet honosított leküldéses értesítéseket küldeni a Notification Hubs szolgáltatásban regisztrált mobileszközökre. Az oktatóanyagban létrehozott alkalmazásokat frissíteni a [oktatóanyag: Értesítések küldése megadott eszközökre (Universal Windows Platform)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) támogatásához a következő esetekben:

- A Windows Áruházbeli alkalmazás lehetővé teszi az ügyféleszközök számára nyelvek megadását, valamint a feliratkozást a különböző frisshír-kategóriákra.
- A háttéralkalmazás közzéteszi az értesítéseket, a használatával a **címke** és **sablon** Azure Notification Hubs funkcióit.

Az oktatóanyag befejezését követően a mobilalkalmazás lehetővé teszi a felhasználót érdeklő kategóriákra való regisztrálást, valamint azt, hogy a felhasználó, milyen nyelven szeretné fogadni az értesítéseket. A háttéralkalmazás nyelv és eszköz alapján honosított értesítéseket küld.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Windows-alkalmazás frissítése a területi beállítási adatok támogatására
> * A háttéralkalmazás frissítése honosított értesítések küldéséhez
> * Az alkalmazás tesztelése

## <a name="prerequisites"></a>Előfeltételek

Végezze el a [oktatóanyag: Értesítések küldése megadott eszközökre (Universal Windows Platform)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

Az a [oktatóanyag: Értesítések küldése megadott eszközökre (Universal Windows Platform)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md), egy alkalmazást, amelyet használni létrehozott **címkék** előfizetés az értesítésekre különböző hírek **kategóriák**. Ebben az oktatóanyagban a Notification Hubs **sablon** funkcióját használva fog **honosított** értesítéseket küldeni a friss hírekkel kapcsolatban.

A sablonokkal lényegében azt a formátumot adhatja meg, amelyben egy adott eszköznek fogadnia kell az értesítéseket. A sablon meghatározza a hasznos adatok pontos formátumát a háttéralkalmazás által küldött üzenet részét képező tulajdonságokra hivatkozva. Ebben az oktatóanyagban a háttéralkalmazás egy területi beállításoktól független üzenetet küld el, amely minden támogatott nyelvet tartalmaz:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Az eszközök egy sablonnal regisztrálnak, amely a megfelelő tulajdonságra hivatkozik. A bejelentési üzeneteket angol nyelven fogadni kívánó Windows Áruházbeli alkalmazás például az alábbi sablonra regisztrál a megfelelő címkékkel:

```xml
<toast>
    <visual>
    <binding template=\"ToastText01\">
        <text id=\"1\">$(News_English)</text>
    </binding>
    </visual>
</toast>
```

A sablonokkal kapcsolatos további információkért lásd: [sablonok tolás](notification-hubs-templates-cross-platform-push-messages.md).

## <a name="update-windows-app-to-support-locale-information"></a>A Windows-alkalmazás frissítése a területi beállítási adatok támogatására

1. Nyissa meg a Visual Studio-megoldás számára létrehozott a [oktatóanyag: Értesítések küldése megadott eszközökre (Universal Windows Platform)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).
2. A megoldás frissítése `MainPage.xaml` -fájl egy területi beállítás kombinált lista:

    ```xml
    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
3. Az a `Notifications` osztályhoz, a nyelv paraméter hozzáadása a `StoreCategoriesAndSubscribe` és `SubscribeToCategories` módszereket.

    ```csharp
    public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        ApplicationData.Current.LocalSettings.Values["locale"] = locale;
        return await SubscribeToCategories(categories);
    }

    public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration. This makes supporting notifications across other platforms much easier.
        // Using the localized tags based on locale selected.
        string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
    }
    ```

    Hívása helyett a `RegisterNativeAsync` metódus, a hívás `RegisterTemplateAsync`. Egy adott értesítésformátumra kell regisztrálnia, amelyben a sablon a területi beállítástól függ. A sablont el is kell neveznie (például: „localizedWNSTemplateExample”), mert érdemes egynél több sablont regisztrálni (például egyet a bejelentési értesítésekhez és egyet a csempékhez). A sablonok elnevezése a frissítésükhöz és törlésükhöz is szükséges.

    Ha egy eszköz több, ugyanazon címkével rendelkező sablont is regisztrál, az adott címkét célzó bejövő üzenetekről több értesítés is érkezik az adott eszközre (egy-egy mindegyik sablon esetén). Ez a működés akkor hasznos, ha egyetlen logikai üzenetnek több látható értesítést kell eredményeznie, például egy jelvény megjelenítését és egy bejelentést a Windows Áruházbeli alkalmazásban.
4. Adja hozzá az alábbi metódust a tárolt területi beállítás lekéréséhez:

    ```csharp
    public string RetrieveLocale()
    {
        var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
        return locale != null ? locale : "English";
    }
    ```

5. Az a `MainPage.xaml.cs` fájlt, frissítse a gombra való kattintással kezelő kérje le az aktuális értéket a területi beállítás kombinált lista, és adja meg a hívást a `Notifications` osztály:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var locale = (string)Locale.SelectedItem;

        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                categories);

        var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
            string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```
6. Végül a a `App.xaml.cs` fájlt, frissítse a `InitNotificationsAsync` a területi beállítása és használata a feliratkozáskor metódust:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

## <a name="send-localized-notifications-from-your-back-end"></a>Honosított értesítések küldése a háttérrendszerből

Sablonértesítések küldésekor csak egy tulajdonságkészletet kell megadnia. Ebben az oktatóanyagban a háttéralkalmazás az aktuális hírek honosított verzióját tartalmazó tulajdonságkészletet küldi el, például:

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

Ebben a szakaszban frissíteni fogja a megoldás konzolalkalmazás projektjét. Módosítsa a `SendTemplateNotificationAsync` metódust a korábban létrehozott konzolalkalmazásban az alábbi kóddal:

> [!IMPORTANT]
> A kódban adja meg az értesítési központhoz teljes hozzáférést biztosító nevet és kapcsolati sztringet.

```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(
        "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications.
    // This includes APNS, FCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

Ez egyszerű hívás biztosít hírek honosított részét **összes** eszközök, függetlenül a platformot, az értesítési központban épít, és biztosít az adott címkére feliratkozott összes eszközök megfelelő natív adattartalom.

## <a name="test-the-app"></a>Az alkalmazás tesztelése

1. Futtassa a Windows Áruházbeli univerzális alkalmazást. Várjon, amíg megjelenik a **Registration successful** (Sikeres regisztráció) üzenet.

    ![Mobilalkalmazás és regisztráció](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/registration-successful.png)
2. Jelölje ki a **kategóriákat** és a **területi beállítást**, majd kattintson a **Subscribe** (Feliratkozás) lehetőségre. Az alkalmazás címkékké alakítja át a kiválasztott kategóriákat, és új eszközregisztrációt kezdeményez az értesítési központban a kiválasztott címkékre vonatkozóan.

    ![Mobilalkalmazás](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/mobile-app.png)
3. Egy **megerősítő** üzenet jelenik meg a **feliratkozásokkal** kapcsolatban.

    ![Feliratkozási üzenet](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/subscription-message.png)
4. Ha megkapta a megerősítést, futtassa a **konzolalkalmazást**, amellyel értesítéseket küldhet az egyes kategóriákra vonatkozóan az összes támogatott nyelven. Ellenőrizze, hogy csak azon kategóriákra vonatkozóan kap-e értesítéseket, amelyekre feliratkozott, és hogy az üzenetek megfelelnek-e a kiválasztott területi beállításnak.

    ![Értesítési üzenetek](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/notifications.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban elsajátította, hogy hogyan küldhet honosított értesítéseket olyan adott eszközökre, amelyek a regisztrációjukhoz társított címkékkel rendelkeznek. Ha szeretné megtudni, hogy hogyan küldhet értesítéseket adott felhasználóknak, akik egynél több eszközt is használhatnak, lépjen tovább a következő oktatóanyagra:

> [!div class="nextstepaction"]
>[Leküldéses értesítések küldése adott felhasználóknak](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)

<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs/notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: https://msdn.microsoft.com/library/jj927172.aspx
