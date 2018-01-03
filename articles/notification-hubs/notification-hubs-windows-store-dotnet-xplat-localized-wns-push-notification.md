---
title: "A Notification Hubs honosított Breaking News oktatóanyag"
description: "Megtudhatja, hogyan használható az Azure Notification Hubs honosított legfrissebb híreket tartalmazó értesítések küldése."
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: 
ms.assetid: c454f5a3-a06b-45ac-91c7-f91210889b25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 8f205188bd68e53b187b71981ed36dcf9129ec62
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="use-notification-hubs-to-send-localized-breaking-news"></a>Honosított legfrissebb hírek elküldése a Notification Hubs használatával
> [!div class="op_single_selector"]
> * [Windows áruház C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 
> 

## <a name="overview"></a>Áttekintés
Ez a témakör bemutatja, hogyan használható a **sablon** az Azure Notification Hubs – legfrissebb híreket tartalmazó értesítések nyelv és az eszköz honosított katalóguselemekről adás szolgáltatása. Ez az oktatóanyag a kiindulási pont a Windows Áruházbeli alkalmazásban létrehozott [legfrissebb hírek küldése Notification Hubs használata]. Amikor végzett, lesz kategóriák szeretné regisztrálni, adja meg a nyelvet, amelyen az értesítéseket, és csak a kiválasztott kategóriákra leküldéses értesítéseket kapni az adott nyelveken.

Ez a forgatókönyv két részből áll:

* a Windows Áruházbeli alkalmazás tesz lehetővé az nyelv megadása, és fizessen elő a különböző breaking news kategóriák;
* a háttér-közzéteszi az értesítéseket, a **címke** és **sablon** az Azure Notification Hubs feautres.

## <a name="prerequisites"></a>Előfeltételek
Már végrehajtotta a [legfrissebb hírek küldése Notification Hubs használata] oktatóanyagot, és a kód érhető el, mert ez az oktatóanyag közvetlenül épít, hogy a kód.

Szükség a Visual Studio 2012 vagy újabb.

## <a name="template-concepts"></a>Sablon fogalmak
A [legfrissebb hírek küldése Notification Hubs használata] olyan alkalmazás, amelynek használt parancsfájlkezelő **címkék** előfizetés az értesítésekre hírek különböző kategóriákban.
Számos alkalmazás, azonban több piacok célként, és honosítási igényelnek. Ez azt jelenti, hogy a tartalom maguk értesítést kell honosított és kívánt eszközök beküldeni.
Ebben a témakörben bemutatjuk, hogyan használható a **sablon** könnyen képes biztosítani a honosított legfrissebb híreket tartalmazó értesítések a Notification Hubs szolgáltatása.

Megjegyzés: egy honosított értesítések küldéséhez módja az egyes címkék több verzióját. Például angol, francia és Mandarin támogatásához lenne szükséges három különböző címkék híreket: "world_en", "world_fr" és "world_ch". A Microsoft majd kellene elküldeni a híreket honosított verzióját az egyes ezekkel a címkékkel. Ebben a témakörben a sablonok a címkék elterjedése és több üzenetet küldeni a követelmény elkerülése érdekében használjuk.

Magas szinten sablonok, amelyek egy adja meg, hogy egy adott eszközhöz egy értesítést kell kapnia. A sablon a pontos az adattartalom formátuma az app-háttér által küldött üzenet részét képező tulajdonságok alapján határozza meg. Ebben az esetben az összes támogatott nyelvek tartalmazó területibeállítás-független üzenetet küldünk:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Ezután azt fogja győződjön meg arról, hogy az eszközök regisztrálása a sablont, amely a megfelelő tulajdonság hivatkozik. Például egy Windows Áruházbeli alkalmazást, amely kéri a egyszerű bejelentési üzenet regisztrálja a következő sablon bármely megfelelő címkékkel:

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">$(News_English)</text>
        </binding>
      </visual>
    </toast>



Sablonok egyik újdonsága nagyon hatékony többet is megtudhat arról a a [sablonok](notification-hubs-templates-cross-platform-push-messages.md) cikk. 

## <a name="the-app-user-interface"></a>Az alkalmazás felhasználói felülete
A Microsoft most módosítja a Megtörje hírek alkalmazást, amely létrehozta a következő témakör [legfrissebb hírek küldése Notification Hubs használata] küldése honosított a legfrissebb hírek sablonok használatával.

A Windows Áruházbeli alkalmazásban:

A területi beállítás kombinált lista tartalmazza a MainPage.xaml módosítása:

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

## <a name="building-the-windows-store-client-app"></a>A Windows áruház-ügyfélalkalmazás létrehozása
1. Az értesítések osztályban adja hozzá a területi beállítással a *StoreCategoriesAndSubscribe* és *SubscribeToCateories* módszerek.
   
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
   
    Vegye figyelembe, hogy hívása helyett a *RegisterNativeAsync* metódus hívása *RegisterTemplateAsync*: egy adott értesítés formátuma, amelyben a sablont függ a területi beállítás regisztrálja azt. Egy nevet a sablonnak ("localizedWNSTemplateExample"), azt is adja meg, mert előfordulhat, hogy szeretné regisztrálni (például egy bejelentési értesítést) és a csempék egy több sablon, ezért ellenőriznünk kell a nevét ahhoz, hogy a frissítés vagy törlés őket.
   
    Vegye figyelembe, hogy ha egy eszköz több sablon regisztrálja az azonos címkével, egy bejövő üzenet célcsoport-kezelési eredményező címke több értesítés is érkezett kell juttatni az eszközre (minden sablon egy). Ez a viselkedés akkor hasznos, ha ugyanazon logikai üzenet van több vizuális értesítések, például a Windows Áruházbeli alkalmazások megjelenítő egy jelvény és egy bejelentési is eredményezheti.
2. Adja hozzá a következő metódust beolvasni a tárolt nyelvterületi beállításokat:
   
        public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }
3. A MainPage.xaml.cs, frissítse a gomb látható beolvasása a területi beállítás kombinált lista aktuális értéke, és hogy az értesítések osztály hívása kezelő kattintson:
   
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
4. Végezetül az App.xaml.cs fájlban ellenőrizze, hogy frissítse a `InitNotificationsAsync` beolvasni a területi és használatra, ha az előfizetés módszert:
   
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

## <a name="send-localized-notifications-from-your-back-end"></a>Honosított értesítések küldése a háttérrendszerből
[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[legfrissebb hírek küldése Notification Hubs használata]: /notification-hubs/notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
