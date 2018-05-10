---
title: Biztonságos Azure Notification Hubs leküldéses
description: 'Útmutató: a biztonságos leküldéses értesítéseket küldeni az Azure-ban. A kódminták C# nyelven íródtak, a .NET API használatával.'
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 8d051107a5e114ed8aa5f4b5a629a439519157b3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="azure-notification-hubs-secure-push"></a>Biztonságos Azure Notification Hubs leküldéses
> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Áttekintés
Leküldéses értesítési támogatása a Microsoft Azure lehetővé teszi egy könnyen használható, többplatformos, kibővített leküldéses infrastruktúrában, ami jelentősen egyszerűsíti a leküldéses értesítések mobil platformokhoz fogyasztói, valamint a vállalati alkalmazások eléréséhez.

Szabályozó miatt vagy biztonsági korlátozások néha egy alkalmazás előfordulhat, hogy szeretne valamit az értesítés, amely a szabványos leküldéses értesítési infrastruktúrát keresztül nem lehet megadni. Ez az oktatóanyag ismerteti, hogyan által a biztonságos és hitelesített kapcsolatot az ügyféleszközön és a háttéralkalmazás keresztül érzékeny adatokat küld a felhasználói élmény eléréséhez.

Magas szinten a folyamat a következőképpen történik:

1. Az alkalmazás háttér:
   * Háttér-adatbázisban tárolja biztonságos hasznos.
   * Az értesítés Azonosítójának elküldi az eszköznek (nem biztonságos információk küldése).
2. Az alkalmazást az eszközön, az értesítés fogadása közben:
   * Az eszköz kapcsolatot létesít a háttér-kérő a biztonságos tartalom.
   * Az alkalmazás a tartalom megjeleníthető értesítésként az eszközön.

Fontos megjegyezni, hogy az előző folyamatában (és ebben az oktatóanyagban), feltételezzük, hogy az eszköz tárol egy hitelesítési jogkivonatot helyi tároló, a felhasználó bejelentkezése után. Ez biztosítja, hogy teljesen zökkenőmentes élményt, mivel az eszköz le az értesítési biztonságos tartalom a tokent. Ha az alkalmazás nem tárolja a hitelesítési tokenek az eszközön, vagy ezeket a jogkivonatokat is lejárt, az eszköz alkalmazást, és az értesítés fogadásakor megjelenjen-e a felhasználó megkérdezése általános értesítési indíthatja el az alkalmazást. Az alkalmazás majd hitelesíti a felhasználót, és az értesítési tartalom jeleníti meg.

Biztonságos leküldéses az oktatóanyag bemutatja, hogyan biztonságosan leküldéses értesítés küldéséhez. Az oktatóanyag épít, a [felhasználók értesítése](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) oktatóanyag, ezért el kell végeznie a lépéseket, hogy az oktatóanyagban először.

> [!NOTE]
> Ez az oktatóanyag feltételezi, hogy létrehozta és leírtak szerint konfigurálta az értesítési központ [Ismerkedés a Notification Hubs (a Windows áruházban)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Emellett vegye figyelembe, hogy a Windows Phone 8.1 (nem Windows Phone) Windows hitelesítő adatok szükségesek, és hogy háttérfeladatok nem működnek a Windows Phone 8.0-s vagy Silverlight 8.1. A Windows Áruházbeli alkalmazásokhoz is fogadhatja az értesítéseket keresztül háttérfeladat csak akkor, ha az alkalmazás zárolási képernyő engedélyezve (kattintson a CheckBox osztály a Appmanifest).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>A Windows Phone-projekt módosítása
1. Az a **NotifyUserWindowsPhone** projektre, adja hozzá a következő kódot regisztrálni a leküldéses háttérfeladat App.xaml.cs. Szúrja be az alábbi kódsort az `OnLaunched()` metódus végén:
   
        RegisterBackgroundTask();
2. Továbbra is az App.xaml.cs fájlban adja hozzá a következő kód után azonnal a `OnLaunched()` módszert:
   
        private async void RegisterBackgroundTask()
        {
            if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
            {
                var result = await BackgroundExecutionManager.RequestAccessAsync();
                var builder = new BackgroundTaskBuilder();
   
                builder.Name = "PushBackgroundTask";
                builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
                builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
                BackgroundTaskRegistration task = builder.Register();
            }
        }
3. Adja hozzá a következő `using` tetején az App.xaml.cs fájlban található utasításokat:
   
        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;
4. A Visual Studio **File** (Fájl) menüjében kattintson az **Save All** (Összes mentése) parancsra.

## <a name="create-the-push-background-component"></a>A leküldéses háttér összetevő létrehozása
A következő lépés, ha a leküldéses háttér-összetevő.

1. A Megoldáskezelőben kattintson a jobb gombbal a legfelső szintű csomópontja a megoldás (**megoldás SecurePush** ebben az esetben), majd kattintson **hozzáadása**, majd kattintson a **új projekt**.
2. Bontsa ki a **Áruházbeli alkalmazások**, majd kattintson a **Windows Phone-alkalmazások**, kattintson a **Windows-Futtatókörnyezetű összetevőben (Windows Phone)**. Nevet a projektnek **PushBackgroundComponent**, és kattintson a **OK** a projekt létrehozásához.
   
    ![][12]
3. A Megoldáskezelőben kattintson a jobb gombbal a **PushBackgroundComponent (Windows Phone 8.1)** projektre, majd kattintson a **Hozzáadás**, majd kattintson a **osztály**. Az új osztály neve **PushBackgroundTask.cs**. Kattintson a **Hozzáadás** az osztály létrehozásához.
4. Cserélje le a teljes tartalmát a **PushBackgroundComponent** névtér-definíciót az alábbi kódra, és a helyőrző `{back-end endpoint}` a háttér-végponthoz, a háttér-másikban kapott:
   
        public sealed class Notification
            {
                public int Id { get; set; }
                public string Payload { get; set; }
                public bool Read { get; set; }
            }
   
            public sealed class PushBackgroundTask : IBackgroundTask
            {
                private string GET_URL = "{back-end endpoint}/api/notifications/";
   
                async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
                {
                    // Store the content received from the notification so it can be retrieved from the UI.
                    RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                    var notificationId = raw.Content;
   
                    // retrieve content
                    BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                    var httpClient = new HttpClient();
                    var settings = ApplicationData.Current.LocalSettings.Values;
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);
   
                    var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);
   
                    var notification = JsonConvert.DeserializeObject<Notification>(notificationString);
   
                    ShowToast(notification);
   
                    deferral.Complete();
                }
   
                private void ShowToast(Notification notification)
                {
                    ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                    XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                    XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                    toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                    ToastNotification toast = new ToastNotification(toastXml);
                    ToastNotificationManager.CreateToastNotifier().Show(toast);
                }
            }
5. A Megoldáskezelőben kattintson a jobb gombbal a **PushBackgroundComponent (Windows Phone 8.1)** projektre, majd kattintson **NuGet-csomagok kezelése**.
6. A bal oldalon kattintson **Online**.
7. Az a **keresési** mezőbe írja be **HTTP-alapú**.
8. Az eredmények listájában kattintson **Microsoft HTTP ügyfél függvénytárainak**, és kattintson a **telepítése**. A telepítés befejezéséhez.
9. A NuGet a **keresési** mezőbe írja be **Json.net**. Telepítse a **Json.NET** csomagot, majd zárja be a NuGet Package Manager ablakát.
10. Adja hozzá a következő `using` tetején lévő utasítások a **PushBackgroundTask.cs** fájlt:
    
        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;
11. A Megoldáskezelőben a a **NotifyUserWindowsPhone (Windows Phone 8.1)** projektre, kattintson a jobb gombbal **hivatkozások**, majd kattintson a **hivatkozás hozzáadása...** . A hivatkozáskezelő párbeszédpanelen jelölje be a jelölőnégyzetet a **PushBackgroundComponent**, és kattintson a **OK**.
12. A Megoldáskezelőben kattintson duplán a **Package.appxmanifest** a a **NotifyUserWindowsPhone (Windows Phone 8.1)** projekt. A **értesítések**, beállíthatja **bejelentési képes** való **Igen**.
    
    ![][3]
13. Még mindig **Package.appxmanifest**, kattintson a **nyilatkozatok** menü felső részén. Az a **elérhető nyilatkozatok** legördülő menüben kattintson a **háttérfeladatok**, és kattintson a **Hozzáadás**.
14. A **Package.appxmanifest**a **tulajdonságok**, ellenőrizze **leküldéses értesítési**.
15. A **Package.appxmanifest**a **Alkalmazásbeállítások**, típus **PushBackgroundComponent.PushBackgroundTask** a a **belépési pont** mező.
    
    ![][13]
16. Kattintson a **File** (Fájl) menü **Save All** (Összes mentése) elemére.

## <a name="run-the-application"></a>Futtassa az alkalmazást
Futtassa az alkalmazást, tegye a következőket:

1. A Visual Studio, futtassa a **AppBackend** webes API-alkalmazás. Az ASP.NET-weblap jelenik meg.
2. A Visual Studio, futtassa a **NotifyUserWindowsPhone (Windows Phone 8.1)** Windows Phone-alkalmazás. A Windows Phone-emulátor fut, és automatikusan betölti az alkalmazást.
3. Az a **NotifyUserWindowsPhone** alkalmazás felhasználói felületén, adja meg a felhasználónevet és jelszót. Bármilyen karakterlánc is lehetnek, de ugyanarra az értékre kell lenniük.
4. Az a **NotifyUserWindowsPhone** alkalmazás felhasználói felületén kattintson **jelentkezzen be, és regisztrálja**. Kattintson a **leküldéses küldése**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
