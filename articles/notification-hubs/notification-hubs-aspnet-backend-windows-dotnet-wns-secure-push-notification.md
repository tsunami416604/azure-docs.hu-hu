---
title: Az Azure Értesítési központok biztonságos leküldése a Windows rendszerhez
description: Ismerje meg, hogyan küldhet biztonságos leküldéses értesítéseket az Azure-ban. A kódminták C# nyelven íródtak, a .NET API használatával.
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: db42cf7f886855af77073963e6f04ac088ca5612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530731"
---
# <a name="securely-push-notifications-from-azure-notification-hubs"></a>Biztonságos leküldéses értesítések az Azure Értesítési központokból

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Áttekintés

A Microsoft Azure leküldéses értesítési támogatása lehetővé teszi egy könnyen használható, többplatformos, kibővített leküldéses infrastruktúra elérését, ami nagyban leegyszerűsíti a leküldéses értesítések megvalósítását mind a fogyasztói, mind a nagyvállalati mobilalkalmazások számára Platformok.

Szabályozási vagy biztonsági korlátok miatt előfordulhat, hogy egy alkalmazás szeretne felvenni valamit az értesítésben, amely nem továbbítható a szabványos leküldéses értesítési infrastruktúrán keresztül. Ez az oktatóanyag ismerteti, hogyan érheti el ugyanazt a felhasználói élményt azáltal, hogy bizalmas adatokat küld az ügyféleszköz és az alkalmazás háttérrendszere közötti biztonságos, hitelesített kapcsolaton keresztül.

Magas szinten az áramlás a következő:

1. Az alkalmazás háttér-vége:
   * Biztonságos tartalom tárolható a háttér-adatbázisban.
   * Elküldi az értesítés azonosítóját az eszköznek (nem küld biztonságos adatokat).
2. Az alkalmazás az eszközön, amikor megkapja az értesítést:
   * Az eszköz kapcsolatba lép a biztonsági adatért, a háttérrel.
   * Az alkalmazás értesítésként jelenítheti meg a hasznos adatot az eszközön.

Fontos megjegyezni, hogy az előző folyamat (és ebben az oktatóanyagban), feltételezzük, hogy az eszköz tárolja a hitelesítési jogkivonatot a helyi tárolóban, miután a felhasználó bejelentkezik. Ez garantálja a teljesen zökkenőmentes élményt, mivel az eszköz ezzel a jogkivonattal lekérheti az értesítés biztonságos hasznos adatait. Ha az alkalmazás nem tárolja a hitelesítési jogkivonatokat az eszközön, vagy ha ezek a tokenek lejárhatnak, az eszközalkalmazásnak az értesítés kézhezvételekor meg kell jelenítenie egy általános értesítést, amely felszólítja a felhasználót az alkalmazás elindítására. Az alkalmazás ezután hitelesíti a felhasználót, és megjeleníti az értesítési hasznos terhet.

Ez a Biztonságos leküldéses oktatóanyag bemutatja, hogyan küldhet leküldéses értesítést biztonságosan. Az oktatóanyag a [Felhasználók értesítése](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) oktatóanyagra épül, ezért először végre kell hajtania az oktatóanyag lépéseit.

> [!NOTE]
> Ez az oktatóanyag feltételezi, hogy az értesítési központot az [Értesítési központ (Windows Áruház) – első lépések az értesítési központokkal (Windows Áruház)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)című részben leírtak szerint hozta létre és konfigurálta.
> Vegye figyelembe azt is, hogy a Windows Phone 8.1-es telefon windowsos (nem Windows Phone) hitelesítő adatokat igényel, és hogy a háttérfeladatok nem működnek windows Phone 8.0 vagy Silverlight 8.1 rendszeren. Windows Áruházbeli alkalmazások esetén csak akkor kaphat értesítéseket háttérfeladaton keresztül, ha az alkalmazás zárolt (kattintson az Appmanifest jelölőnégyzetére).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>A Windows Phone-projekt módosítása

1. Az **NotifyUserWindowsPhone** projektben adja hozzá a következő kódot a App.xaml.cs a leküldéses háttérfeladat regisztrálásához. Szúrja be az alábbi kódsort az `OnLaunched()` metódus végén:

    ```csharp
    RegisterBackgroundTask();
    ```
2. Még mindig App.xaml.cs, adja hozzá a `OnLaunched()` következő kódot közvetlenül a módszer után:

    ```csharp
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
    ```
3. Adja hozzá `using` a következő állításokat a App.xaml.cs fájl tetején:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```
4. A Visual Studio **File** (Fájl) menüjében kattintson az **Save All** (Összes mentése) parancsra.

## <a name="create-the-push-background-component"></a>A leküldéses háttér összetevő létrehozása

A következő lépés a leküldéses háttér-összetevő létrehozása.

1. A Megoldáskezelőben kattintson a jobb gombbal a megoldás legfelső szintű csomópontjára (**Megoldás SecurePush** ebben az esetben), majd kattintson a **Hozzáadás**parancsra, majd az **Új projekt parancsra.**
2. Bontsa ki **az Áruházbeli alkalmazások at,** majd kattintson a **Windows Phone-alkalmazások**elemre, majd a **Windows-futásidejű összetevő (Windows Phone) parancsra.** Nevezze el a project **PushBackgroundComponent nevű projektet,** majd kattintson az **OK** gombra a projekt létrehozásához.

    ![][12]
3. A Megoldáskezelőben kattintson a jobb gombbal a **PushBackgroundComponent (Windows Phone 8.1)** projektre, majd kattintson a **Hozzáadás**parancsra, majd az **Osztály**parancsra. Nevezze el `PushBackgroundTask.cs`az új osztályt . Az osztály létrehozásához kattintson a **Hozzáadás** gombra.
4. Cserélje le a `PushBackgroundComponent` névtér definíciójának teljes tartalmát a következő `{back-end endpoint}` kódra, helyettesítve a helyőrzőt a háttérvégpontgal, amelyet a háttérrendszer telepítése során kapott:

    ```csharp
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
    ```
5. A Megoldáskezelőben kattintson a jobb gombbal a **PushBackgroundComponent (Windows Phone 8.1)** projektre, majd kattintson **a NuGet-csomagok kezelése parancsra.**
6. A képernyő bal oldalán kattintson az **Online** lehetőségre.
7. A **Search** (Keresés) mezőbe írja be a **Http Client** (HTTP-ügyfél) kifejezést.
8. Az eredménylistában kattintson a **Microsoft HTTP-ügyféltárak**elemre, majd a **Telepítés gombra.** Fejezze be a telepítést.
9. A NuGet **Search** (Keresés) mezőjébe írja be a **Json.net** kifejezést. Telepítse a **Json.NET** csomagot, majd zárja be a NuGet Csomagkezelő ablakot.
10. A `PushBackgroundTask.cs` fájl `using` tetején a következő utasítások vannak felrakva:

    ```csharp
    using Windows.ApplicationModel.Background;
    using Windows.Networking.PushNotifications;
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using Windows.UI.Notifications;
    using Windows.Data.Xml.Dom;
    ```
11. A Megoldáskezelő **értesítéseWindowsPhone (Windows Phone 8.1)** projektben kattintson a jobb gombbal **a Hivatkozások**elemre, majd kattintson a **Hivatkozás hozzáadása... parancsra.** A Referenciakezelő párbeszédpanelen jelölje be a **PushBackgroundComponent**elem melletti jelölőnégyzetet, majd kattintson az **OK**gombra.
12. A Megoldáskezelőben kattintson duplán a **Package.appxmanifest** fájlra az **NotifyUserWindowsPhone (Windows Phone 8.1)** projektben. Az **Értesítések**csoportban állítsa a **Toast Capable (Pirítós képes** ) lehetőséget **igen**re.

    ![][3]
13. Még mindig **a Package.appxmanifest**fájlban, kattintson a **deklarációk** menü tetejére. Az **Elérhető deklarációk** legördülő menüben kattintson a **Háttérfeladatok**elemre, majd a **Hozzáadás**gombra.
14. A **Package.appxmanifest**fájlban a **Tulajdonságok csoportban**jelölje be a **Leküldéses értesítés**jelölőnégyzetet.
15. A **Package.appxmanifest** **fájlban**az Alkalmazásbeállítások csoportban írja be a **PushBackgroundComponent.PushBackgroundTask** parancsot a **Belépési pont** mezőbe.

    ![][13]
16. Kattintson a **File** (Fájl) menü **Save All** (Összes mentése) elemére.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásához tegye a következőket:

1. A Visual Studióban futtassa az **AppBackend** Web API-alkalmazást. Megjelenik egy ASP.NET weblap.
2. A Visual Studio alkalmazásban futtassa az **NotifyUserWindowsPhone (Windows Phone 8.1)** Windows Phone alkalmazást. A Windows Phone emulátor automatikusan futtatja és tölti be az alkalmazást.
3. Az **NotifyUserWindowsPhone** alkalmazás felhasználói felületén adjon meg egy felhasználónevet és egy jelszót. Ezek bármilyen karakterlánc lehetnek, de azonos értékűeknek kell lenniük.
4. Az **NotifyUserWindowsPhone** alkalmazás felhasználói felületén kattintson **a Bejelentkezés és a regisztráció gombra.** Ezután kattintson **a Leküldéses küldés**gombra.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
