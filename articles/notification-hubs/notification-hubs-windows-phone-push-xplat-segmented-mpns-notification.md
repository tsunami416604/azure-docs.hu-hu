---
title: "(Windows Phone) legfrissebb hírek elküldése a Notification Hubs használatával"
description: "Azure Notification Hubs használatával regisztrációk címke használatával legfrissebb hírek elküldése egy Windows Phone-alkalmazáshoz."
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: 
ms.assetid: 42726bf5-cc82-438d-9eaa-238da3322d80
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 3a6a69bf555c7267d3fbeb03ff6c03054991960f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-notification-hubs-to-send-breaking-news"></a>A legfrissebb hírek elküldése a Notification Hubs használatával
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Áttekintés
Ez a témakör bemutatja, hogyan szórási legfrissebb híreket tartalmazó értesítések Windows Phone 8.0/8.1 Silverlight-alkalmazásokhoz az Azure Notification Hubs használatával. Ha Windows Áruházbeli és Windows Phone 8.1-alkalmazást céloz meg, olvassa el a a [univerzális Windows-](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md) verziója. Amikor végzett, akkor fog számára megtörje érdekli hírek kategóriák regisztrálni, és ezen kategóriák csak leküldéses értesítések fogadásához. Ebben a forgatókönyvben számos alkalmazás általános felépítését, ahol értesítések kell őket, például az RSS-olvasóval, az alkalmazások zene ventilátorok stb érdeklődik elemnek már deklarálva felhasználói csoportokat kell küldeni.

Szórási forgatókönyvek engedélyezve vannak, beleértve a következőket egy vagy több *címkék* regisztráció létrehozásakor az értesítési központban. Amikor a rendszer értesítéseket küld egy címkét, akkor a címke regisztrált minden eszköz a értesítést kap. Mivel a címkékkel egyszerűen csak karakterláncok, nem rendelkeznek előre kell építeni. Címkékkel kapcsolatos további információkért tekintse meg [Notification Hubs útválasztási és címke kifejezések](notification-hubs-tags-segment-push-message.md).

## <a name="prerequisites"></a>Előfeltételek
Ez a témakör a létrehozott alkalmazás épül [Ismerkedés a Notification Hubs]. Az oktatóanyag elindítása előtt már végrehajtotta [Ismerkedés a Notification Hubs].

## <a name="add-category-selection-to-the-app"></a>Kategória kiválasztása hozzáadása az alkalmazáshoz
Az első lépés a felhasználói felületi elemek hozzáadása a meglévő fő lapján, amelyek lehetővé teszik a felhasználó számára a kategóriák regisztrálni kívánt. A felhasználó által kiválasztott kategóriák tárolódnak az eszközön. Az alkalmazás indításakor a eszközregisztráció címkeként az értesítési központ, a kiválasztott kategóriákra jön létre.

1. Nyissa meg a MainPage.xaml projektfájlt, majd cserélje le a **rács** nevű elem `TitlePanel` és `ContentPanel` az alábbi kódra:
   
        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>
   
        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>
2. A projekt, hozzon létre egy új osztályt **értesítések**, adja hozzá a **nyilvános** osztály definíciójához módosító majd adja hozzá a következő **használatával** utasítást, hogy az új kódfájl:
   
        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
        using System.IO.IsolatedStorage;
        using System.Windows;
3. A következő kódot bemásolhatja az új **értesítések** osztály:
   
        private NotificationHub hub;
   
        // Registration task to complete registration in the ChannelUriUpdated event handler
        private TaskCompletionSource<Registration> registrationTask;
   
        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }
   
        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
            return categories != null ? categories.Split(',') : new string[0];
        }
   
        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            var categoriesAsString = string.Join(",", categories);
            var settings = IsolatedStorageSettings.ApplicationSettings;
            if (!settings.Contains("categories"))
            {
                settings.Add("categories", categoriesAsString);
            }
            else
            {
                settings["categories"] = categoriesAsString;
            }
            settings.Save();
   
            return await SubscribeToCategories();
        }
   
        public async Task<Registration> SubscribeToCategories()
        {
            registrationTask = new TaskCompletionSource<Registration>();
   
            var channel = HttpNotificationChannel.Find("MyPushChannel");
   
            if (channel == null)
            {
                channel = new HttpNotificationChannel("MyPushChannel");
                channel.Open();
                channel.BindToShellToast();
                channel.ChannelUriUpdated += channel_ChannelUriUpdated;
   
                // This is optional, used to receive notifications while the app is running.
                channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
            }
   
            // If channel.ChannelUri is not null, we will complete the registrationTask here.  
            // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
            if (channel.ChannelUri != null)
            {
                await RegisterTemplate(channel.ChannelUri);
            }
   
            return await registrationTask.Task;
        }
   
        async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
        {
            await RegisterTemplate(e.ChannelUri);
        }
   
        async Task<Registration> RegisterTemplate(Uri channelUri)
        {
            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.
   
            const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                                "<wp:Toast>" +
                                                    "<wp:Text1>$(messageParam)</wp:Text1>" +
                                                "</wp:Toast>" +
                                            "</wp:Notification>";
   
            // The stored categories tags are passed with the template registration.
   
            registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(), 
                templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));
   
            return await registrationTask.Task;
        }
   
        // This is optional. It is used to receive notifications while the app is running.
        void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
        {
            StringBuilder message = new StringBuilder();
            string relativeUri = string.Empty;
   
            message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());
   
            // Parse out the information that was part of the message.
            foreach (string key in e.Collection.Keys)
            {
                message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);
   
                if (string.Compare(
                    key,
                    "wp:Param",
                    System.Globalization.CultureInfo.InvariantCulture,
                    System.Globalization.CompareOptions.IgnoreCase) == 0)
                {
                    relativeUri = e.Collection[key];
                }
            }
   
            // Display a dialog of all the fields in the toast.
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() => 
            { 
                MessageBox.Show(message.ToString()); 
            });
        }

    Ez az osztály az elszigetelt tárolók használ, amely az eszköz fogadásához hírek kategóriáinak tárolásához. Ezen kategóriák használatával regisztrálnia módszerek is tartalmaz egy [sablon](notification-hubs-templates-cross-platform-push-messages.md) értesítési regisztrációt.


1. Az App.xaml.cs projektfájlt, adja hozzá a következő tulajdonság a **App** osztály. Cserélje le a `<hub name>` és `<connection string with listen access>` helyőrzőket az értesítési központ nevére és a kapcsolati karakterláncot *DefaultListenSharedAccessSignature* korábban beszerzett.
   
        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
   
   > [!NOTE]
   > Eszközzel együtt egy ügyfélalkalmazás hitelesítő adatok nem általában biztonságos, mert csak kell terjeszteni a figyelési hozzáférési kulcs ügyfél alkalmazása. Figyeljen hozzáférés lehetővé teszi, hogy az alkalmazás regisztrálásához értesítések, de a meglévő regisztrációk nem módosítható, és értesítések nem küldhető el. A teljes körű hozzáférési kulcs értesítések küldését, és meglévő regisztrációk módosítása védett háttérszolgáltatás használatban.
   > 
   > 
2. A MainPage.xaml.cs adja hozzá a következő sort:
   
        using Windows.UI.Popups;
3. A MainPage.xaml.cs projekt fájlban adja hozzá a következő metódust:
   
        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
          var categories = new HashSet<string>();
          if (WorldCheckBox.IsChecked == true) categories.Add("World");
          if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
          if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
          if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
          if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
          if (SportsCheckBox.IsChecked == true) categories.Add("Sports");
   
          var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
   
          MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
             result.RegistrationId);
        }
   
    Ezzel a módszerrel hoz létre a kategóriák és a használja listáját a **értesítések** osztályra, hogy a lista a helyi tároló tárolja, és regisztrálja a megfelelő címkéket az értesítési központban. Kategóriák módosításakor a regisztrációs újra létrejön az új kategóriák.

Az alkalmazás már kategóriák készlete tárolja az eszköz helyi tárolóhoz, és regisztrálhatja az értesítési központban, amikor a felhasználó módosítja a kiválasztott kategóriák.

## <a name="register-for-notifications"></a>Az értesítések regisztrálása
Ezeket a lépéseket az értesítési központ indításakor a helyi tárolóban tárolt kategóriák segítségével regisztrálja.

> [!NOTE]
> A csatorna URI által a Microsoft leküldéses értesítési szolgáltatásának (MPNS) hozzárendelt bármikor módosíthatja, mert az értesítések a notification hibák elkerülése érdekében gyakran kell regisztrálni. Ebben a példában a regisztrálja az értesítések minden alkalommal, az alkalmazás indítása. Gyakran futtatott alkalmazások esetén naponta csak egyszer, valószínűleg kihagyhatja regisztrációt, hogy a sávszélesség megőrzése, ha az előző regisztráció óta eltelt egy napnál.
> 
> 

1. Nyissa meg az App.xaml.cs fájlt, és adja hozzá a **aszinkron** való módosító **Application_Launching** metódus, és cserélje le a Notification Hubs-regisztráció során hozzáadott code [Ismerkedés a Notification Hubs] az alábbi kódra:
   
        private async void Application_Launching(object sender, LaunchingEventArgs e)
        {
            var result = await notifications.SubscribeToCategories();
   
            if (result != null)
                System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
                {
                    MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
                });
        }
   
    Ez biztosítja, hogy minden alkalommal, amikor az alkalmazás indítása, a kategóriák átmásolja a helyi tároló, és ezen kategóriák a regisztráció kéri.
2. A MainPage.xaml.cs projekt fájlban adja hozzá az alábbi kódot, amely megvalósítja a **OnNavigatedTo** módszert:
   
        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();
   
            if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
            if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
            if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
            if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
            if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
            if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
        }
   
    Ekkor frissül, a fő lapján, a korábban mentett kategóriák állapota alapján.

Az alkalmazás most már befejeződött, és képes tárolni a kategóriák az eszköz helyi tárolójára való regisztrálásához az értesítési központnak a felhasználói kategóriák kiválasztott megváltozásakor használt. A következő azt határozza meg, amely ennek az alkalmazásnak kategória értesítéseket küldhet háttérkiszolgálón.

## <a name="sending-tagged-notifications"></a>Címkézett értesítések küldése
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-app-and-generate-notifications"></a>Futtassa az alkalmazást, és értesítések
1. A Visual Studio nyomja le az F5 fordítsa le és indítsa el az alkalmazást.
   
    ![][1]
   
    Vegye figyelembe, hogy az alkalmazás felhasználói felület számos váltógombok, amely lehetővé teszi, hogy válassza ki a előfizetni.
2. Egy vagy több kategóriák váltógombok engedélyezése, majd kattintson az **előfizetés**.
   
    Az app alakítja át a kiválasztott kategóriákra címkék, és egy új eszköz regisztrálása a kijelölt címkék kéri le az értesítési központban. A regisztrált kategóriák tér vissza, és megjelenik egy párbeszédpanel.
   
    ![][2]
3. Azután, hogy megérkezett igazolja, hogy van-e előfizetés befejezte a kategóriák, futtassa a konzol alkalmazást, amely az egyes kategóriák érintő értesítések elküldése. Győződjön meg arról, hogy csak az előfizetett kategóriákban értesítést kap.
   
    ![][3]

Ez a témakör befejeződött.

<!--##Next steps

In this tutorial we learned how to broadcast breaking news by category. Consider completing one of the following tutorials that highlight other advanced Notification Hubs scenarios:

+ [Use Notification Hubs to broadcast localized breaking news]

    Learn how to expand the breaking news app to enable sending localized notifications.

+ [Notify users with Notification Hubs]

    Learn how to push notifications to specific authenticated users. This is a good solution for sending notifications only to specific users.
-->

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[Ismerkedés a Notification Hubs]: /manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Use Notification Hubs to broadcast localized breaking news]: ../breakingnews-localized-wp8.md
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone]: ??

