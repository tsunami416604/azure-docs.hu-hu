---
title: "Az Azure Notification Hubs – felhasználók értesítése .NET-háttérrendszerrel"
description: "Útmutató: a biztonságos leküldéses értesítéseket küldeni az Azure-ban. A kódminták C# nyelven íródtak, a .NET API használatával."
documentationcenter: windows
author: ysxu
manager: erikre
services: notification-hubs
editor: 
ms.assetid: 012529f2-fdbc-43c4-8634-2698164b5880
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: c0b963ef661612b1a176dd8e5f01d56e61eb5acb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-notification-hubs-notify-users-with-net-backend"></a>Az Azure Notification Hubs – felhasználók értesítése .NET-háttérrendszerrel
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Áttekintés
Leküldéses értesítési támogatás az Azure-ban lehetővé teszi egy könnyen kezelhető, multiplatform és kibővített leküldéses infrastruktúrában, ami jelentősen egyszerűsíti a leküldéses értesítések mobil platformokhoz fogyasztói, valamint a vállalati alkalmazások eléréséhez. Az oktatóanyag bemutatja, hogyan küldhetők az Azure Notification Hubs használatával leküldéses értesítések adott alkalmazásfelhasználónak, meghatározott eszközre. ASP.NET WebAPI háttérrendszerből segítségével hitelesíti az ügyfeleket. A hitelesített ügyfelek felhasználó, és címke automatikusan hozzáadódnak a kiszolgáló értesítési regisztrációban használatával. Ezt a címkét a háttérrendszer által küldendő értesítések megjelenítése egy adott felhasználó használható. További információ a Apps-háttéralkalmazás segítségével értesítések regisztrálása a témakör útmutatást [az alkalmazás háttérrendszeréből regisztrálása](http://msdn.microsoft.com/library/dn743807.aspx). Ez az oktatóanyag épít az értesítési központ és a projekt a [Ismerkedés a Notification Hubs] oktatóanyag.

Ez az oktatóanyag egyben a előfeltételeit a [biztonságos leküldéses] oktatóanyag. Ebben az oktatóanyagban a lépések végrehajtása után folytassa a [biztonságos leküldéses] oktatóanyag, amely bemutatja, hogyan lehet módosítani a kód ebben az oktatóanyagban leküldéses értesítés küldése biztonságosan.

## <a name="before-you-begin"></a>Előkészületek
Visszajelzéseit komolyan vesszük. Ha bármilyen nehézségbe ütközik a témakör elvégzése közben, vagy javaslatai vannak, hogyan tehetjük jobbá a tartalmat, örömmel fogadjuk visszajelzéseit az oldal alján.

Az oktatóanyag teljes kódja a GitHubon, [itt](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers) található meg. 

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag elindítása előtt már végrehajtotta a Mobile Services-oktatóanyag:

* [Ismerkedés a Notification Hubs]<br/>Az értesítési központ létrehozása és lefoglalni az alkalmazás nevére, és ha értesítést szeretne kapni az oktatóanyag regisztrálja. Ez az oktatóanyag feltételezi, hogy már végrehajtotta ezeket a lépéseket. Ha nem, kövesse a lépéseket [Ismerkedés a Notification Hubs (a Windows áruházban)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md); pontosabban, a szakaszok [a Windows Áruházbeli alkalmazás regisztrálása](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#register-your-app-for-the-windows-store) és [az értesítési központ konfigurálása](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#configure-your-notification-hub). Különösen, győződjön meg arról, hogy megadta a **CSOMAGAZONOSÍTÓT** és **Ügyfélkulcs** értékei a portálon, a **konfigurálása** az értesítési központ lapján. A konfigurációs eljárást a szakasz [az értesítési központ konfigurálása](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#configure-your-notification-hub). Ez az egy fontos lépés: Ha a portál a hitelesítő adatok nem egyeznek meg az alkalmazás neve mellett dönt, a leküldéses értesítés nem fog sikerülni.

> [!NOTE]
> Ha a Mobile Apps az Azure App Service-ben, a háttérszolgáltatás használ, tekintse meg a [Mobile Apps-verziójáért](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md) oktatóanyag.
> 
> 

&nbsp;

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-client-project"></a>Frissítse a kódot az ügyfél-projekt
Ebben a szakaszban a projekt befejezte a kód frissítése a [Ismerkedés a Notification Hubs] oktatóanyag. A már kell a tároló társított és az értesítési központ konfigurálva. Ebben a szakaszban a kódot, hogy az új WebAPI háttérrendszerből hívja, és regisztrálja, és az értesítések küldése használni fogják.

1. A Visual Studióban nyissa meg a létrehozott a megoldás a [Ismerkedés a Notification Hubs] oktatóanyag.
2. A Megoldáskezelőben kattintson a jobb gombbal a **(Windows 8.1)** projektre, majd kattintson **NuGet-csomagok kezelése**.
3. A bal oldalon kattintson **Online**.
4. Az a **keresési** mezőbe írja be **HTTP-alapú**.
5. Az eredmények listájában kattintson **Microsoft HTTP ügyfél függvénytárainak**, és kattintson a **telepítése**. A telepítés befejezéséhez.
6. A NuGet a **keresési** mezőbe írja be **Json.net**. Telepítse a **Json.NET** csomagot, majd zárja be a NuGet-Csomagkezelő ablak.
7. A fentiekben ismertetett telepítési lépéseit ismételje meg a **(Windows Phone 8.1)** projekt telepítése az **JSON.NET** a Windows Phone-projektet a NuGet-csomagot.
8. A Megoldáskezelőben a a **(Windows 8.1)** projektre, kattintson duplán a **MainPage.xaml** a Visual Studio-szerkesztőben való megnyitásához.
9. Az a **MainPage.xaml** XML-kódot, cserélje le a `<Grid>` szakasz a következő kóddal. Ez a kód hozzáadása, amely hitelesíti a felhasználót a felhasználónév és jelszó szövegmezőjét. Bővíti ezenkívül a szövegmezőből az értesítési üzenet és a felhasználónév-címke, amely a értesítést kell kapnia:
   
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
   
            <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>
   
            <StackPanel Grid.Row="1" VerticalAlignment="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                    </Grid.ColumnDefinitions>
                    <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                    <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
   
                    <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                                Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>
   
                    <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                    <ToggleButton Name="toggleGCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="GCM" />
                    <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />
   
                    <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
                </Grid>
            </StackPanel>
        </Grid>
10. A Megoldáskezelőben a a **(Windows Phone 8.1)** projektben nyissa meg **MainPage.xaml** , és cserélje le a Windows Phone 8.1 `<Grid>` szakasz ismerteti, hogy ugyanazt a fenti kódot. A felület alább látható mi hasonlóan kell kinéznie.
    
    ![][13]
11. A Solution Explorerben nyissa meg a **MainPage.xaml.cs** fájlt a **(Windows 8.1)** és **(Windows Phone 8.1)** projektek. Adja hozzá a következő `using` fájlokra utasításokat:
    
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;
        using System.Threading.Tasks;
12. A **MainPage.xaml.cs** a a **(Windows 8.1)** és **(Windows Phone 8.1)** projektek, adja hozzá a következő tag a `MainPage` osztály. Ügyeljen arra, hogy a csere `<Enter Your Backend Endpoint>` az a korábban beszerzett a tényleges háttér-végpontot. Például: `http://mybackend.azurewebsites.net`.
    
        private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
13. Adja hozzá az alábbi kódot a MainPage osztályhoz a **MainPage.xaml.cs** a a **(Windows 8.1)** és **(Windows Phone 8.1)** projektek.
    
    A `PushClick` módszer a kattintson kezelőt a **küldése leküldéses** gombra. A háttérkiszolgálón való megfelelő felhasználónév címkével ellátott összes eszközre értesítést meghívja a `to_tag` paraméter. Az értesítési üzenetet küld a kérelem törzsében szereplő JSON-tartalomként.
    
    A `LoginAndRegisterClick` módszer a kattintson kezelőt a **jelentkezzen be, és regisztrálja** gombra. A basic tárol hitelesítési jogkivonat a helyi tároló (vegye figyelembe, hogy a hitelesítési sémát használja bármely jogkivonat jelképez), majd használja `RegisterClient` a háttérkiszolgáló használatával értesítések regisztrálása.

        private async void PushClick(object sender, RoutedEventArgs e)
        {
            if (toggleWNS.IsChecked.Value)
            {
                await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleGCM.IsChecked.Value)
            {
                await sendPush("gcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleAPNS.IsChecked.Value)
            {
                await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

            }
        }

        private async Task sendPush(string pns, string userTag, string message)
        {
            var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
                pns + "&to_tag=" + userTag;

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                try
                {
                    await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                        System.Text.Encoding.UTF8, "application/json"));
                }
                catch (Exception ex)
                {
                    MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                    alert.ShowAsync();
                }
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();

            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            // The "username:<user name>" tag gets automatically added by the message handler in the backend.
            // The tag passed here can be whatever other tags you may want to use.
            try
            {
                // The device handle used will be different depending on the device and PNS. 
                // Windows devices use the channel uri as the PNS handle.
                await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

                var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
                SendPushButton.IsEnabled = true;
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
                alert.ShowAsync();
            }
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;

            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }



1. A Solution Explorer alatt a **megosztott** projektben nyissa meg a **App.xaml.cs** fájlt. A hívás található `InitNotificationsAsync()` a a `OnLaunched()` eseménykezelő. Megjegyzéssé, vagy törölje a hívást `InitNotificationsAsync()`. A korábban ismertetett módon hozzáadott gomb kezelő inicializálja értesítési regisztráció.

        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
            //InitNotificationsAsync();


1. A Megoldáskezelőben kattintson a jobb gombbal a **megosztott** projektre, majd kattintson a **Hozzáadás**, és kattintson a **osztály**. Az osztály neve **RegisterClient.cs**, majd kattintson a **OK** az osztály létrehozásához.
   
   Ez az osztály a REST-hívások szükséges ahhoz, hogy a leküldéses értesítések regisztrálása a háttéralkalmazás kapcsolódni fog burkolja. Helyileg tárolja a *registrationIds* hozta létre az értesítési központnak a [az alkalmazás háttérrendszeréből regisztrálása](http://msdn.microsoft.com/library/dn743807.aspx). Vegye figyelembe, hogy használja-e egy helyi storage-ban tárolt kattintva engedélyezési jogkivonatot a **jelentkezzen be, és regisztrálja** gombra.
2. Adja hozzá a következő `using` utasításokat a RegisterClient.cs fájl elejéhez:
   
       using Windows.Storage;
       using System.Net;
       using System.Net.Http;
       using System.Net.Http.Headers;
       using Newtonsoft.Json;
       using System.Threading.Tasks;
       using System.Linq;
3. Adja hozzá a következő kódot a(z) `RegisterClient` osztálydefiníciójához:
   
       private string POST_URL;
   
       private class DeviceRegistration
       {
           public string Platform { get; set; }
           public string Handle { get; set; }
           public string[] Tags { get; set; }
       }
   
       public RegisterClient(string backendEndpoint)
       {
           POST_URL = backendEndpoint + "/api/register";
       }
   
       public async Task RegisterAsync(string handle, IEnumerable<string> tags)
       {
           var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
   
           var deviceRegistration = new DeviceRegistration
           {
               Platform = "wns",
               Handle = handle,
               Tags = tags.ToArray<string>()
           };
   
           var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
   
           if (statusCode == HttpStatusCode.Gone)
           {
               // regId is expired, deleting from local storage & recreating
               var settings = ApplicationData.Current.LocalSettings.Values;
               settings.Remove("__NHRegistrationId");
               regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
               statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
           }
   
           if (statusCode != HttpStatusCode.Accepted)
           {
               // log or throw
               throw new System.Net.WebException(statusCode.ToString());
           }
       }
   
       private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
       {
           using (var httpClient = new HttpClient())
           {
               var settings = ApplicationData.Current.LocalSettings.Values;
               httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);
   
               var putUri = POST_URL + "/" + regId;
   
               string json = JsonConvert.SerializeObject(deviceRegistration);
                               var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
               return response.StatusCode;
           }
       }
   
       private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
       {
           var settings = ApplicationData.Current.LocalSettings.Values;
           if (!settings.ContainsKey("__NHRegistrationId"))
           {
               using (var httpClient = new HttpClient())
               {
                   httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);
   
                   var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                   if (response.IsSuccessStatusCode)
                   {
                       string regId = await response.Content.ReadAsStringAsync();
                       regId = regId.Substring(1, regId.Length - 2);
                       settings.Add("__NHRegistrationId", regId);
                   }
                   else
                   {
                       throw new System.Net.WebException(response.StatusCode.ToString());
                   }
               }
           }
           return (string)settings["__NHRegistrationId"];
   
       }
4. Mentse az összes módosítást.

## <a name="testing-the-application"></a>Az alkalmazás tesztelése
1. Indítsa el az alkalmazást a Windows 8.1 és a Windows Phone 8.1. A Windows Phone 8.1-es futtathatja a példány az emulátor vagy tényleges eszközön.
2. Az alkalmazás Windows 8.1-példányban, adjon meg egy **felhasználónév** és **jelszó** alábbi képernyőn látható módon. Ez eltér a felhasználónevet és jelszót ad meg a Windows Phone kell.
3. Kattintson a **jelentkezzen be, és regisztrálja** , és ellenőrizze, hogy a párbeszédpanelen látható, hogy már bejelentkezett. Ez azt is lehetővé teszi a **küldése leküldéses** gombra.
   
    ![][14]
4. A Windows Phone 8.1-példányon, adjon meg egy felhasználói nevet karakterláncot mindkét a **felhasználónév** és **jelszó** mezők kattintson **bejelentkezési és regisztrációs**.
5. Ezt a a **címzett felhasználónév címke** mezőbe írja be a regisztrált Windows 8.1-felhasználó nevét. Adjon meg egy értesítési üzenetet, és kattintson a **küldése leküldéses**.
   
    ![][16]
6. Csak a megfelelő felhasználónév címke regisztrált eszközök az értesítési üzenet.
   
    ![][15]

## <a name="next-steps"></a>Következő lépések
* Ha a felhasználókat érdeklődési körök alapján szeretné szegmentálni, olvassa el a [Use Notification Hubs to send breaking news] (Friss hírek küldése Notification Hubs használatával) című témakört.
* A Notification Hubs használatával kapcsolatos további tudnivalókért lásd: [Notification Hubs használatával].

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png



<!-- URLs. -->
[Ismerkedés a Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[biztonságos leküldéses]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Notification Hubs használatával]: http://msdn.microsoft.com/library/jj927170.aspx
