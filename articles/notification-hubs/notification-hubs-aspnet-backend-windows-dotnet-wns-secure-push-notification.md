---
title: Azure Notification Hubs Secure Push
description: Ismerje meg, hogyan küldhet biztonságos leküldéses értesítéseket az Azure-ban. A kódminták C# nyelven íródtak, a .NET API használatával.
documentationcenter: windows
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: cf23ef5df3bdcaad23841da111fa06cc36b4cd57
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475679"
---
# <a name="securely-push-notifications-from-azure-notification-hubs"></a>Biztonságosan leküldéses értesítések az Azure Notification hubs használatával

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Áttekintés

Leküldéses értesítés támogatása a Microsoft Azure segítségével elérheti egy könnyen használható, többplatformos, kibővített leküldéses infrastruktúra, amely jelentősen egyszerűsíti a leküldéses értesítések a mobile fogyasztói és a vállalati alkalmazások számára Platform.

Szabályozási miatt, vagy biztonsági okokból, néha egy alkalmazás előfordulhat, hogy szeretne foglalni valamit a értesítést, amelyet nem lehet megadni a standard szintű leküldéses értesítési infrastruktúrát keresztül. Ebben az oktatóanyagban értékre a bizalmas adatokat az ügyféleszköz- és az alkalmazási háttérrendszer között biztonságos, hitelesített kapcsolaton keresztül küldésével ugyanazt a felhasználói élményt ismerteti.

Magas szinten a folyamat a következőképpen történik:

1. A háttér:
   * Háttér-adatbázisban tárolja biztonságos adattartalma.
   * Ez az értesítés azonosítója elküldi az eszköznek (nem biztonságos küld adatokat).
2. Az alkalmazás az eszközön, az értesítés fogadásakor:
   * Az eszköz kapcsolatba lép a háttéralkalmazás a biztonságos hasznos adatokat kér.
   * Az alkalmazás az eszközön értesítést, a hasznos adatokat jeleníti meg.

Fontos megjegyezni, hogy az előző folyamat (és ebben az oktatóanyagban) feltételezzük, hogy az eszköz tárolja a egy hitelesítési tokent a helyi tárban, miután a felhasználó bejelentkezik. Teljesen zökkenőmentes élményt, Ez garantálja a szerint az eszköz kérheti le az értesítési e-jogkivonat használatával biztonságos hasznos. Ha az alkalmazás nem tárolja a hitelesítési tokenek az eszközön, vagy ha ezek a jogkivonatok is járhatott, az eszköz alkalmazást, az értesítés fogadásakor indítsa el az alkalmazást a felhasználó megkérdezése általános értesítést kell megjelenítenie. Az alkalmazás ezután hitelesíti a felhasználót, és az értesítési tartalom látható.

Biztonságos leküldéses oktatóanyag bemutatja, hogyan biztonságosan küldhet leküldéses értesítéseket. Az oktatóanyag épül, amely a [– felhasználók értesítése](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) így, hajtsa végre a lépéseket, hogy az oktatóanyagban először az oktatóanyagban.

> [!NOTE]
> Ez az oktatóanyag feltételezi, hogy létrehozta és leírtak szerint konfigurálta az értesítési központ [Ismerkedés a Notification hubs használatával (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Továbbá vegye figyelembe, hogy a Windows Phone 8.1-es (nem Windows Phone) Windows hitelesítő adatok szükségesek, és, hogy a háttérben futó feladatok nem működnek a Windows Phone 8.0-s vagy a Silverlight 8.1. Windows Store-alkalmazások esetén is fogadhatja az értesítéseket a háttérfeladat keresztül csak akkor, ha az alkalmazás nem engedélyezett zárolási képernyőjén (kattintson a jelölőnégyzetet a Appmanifest).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>A Windows Phone-projekt módosítása

1. Az a **NotifyUserWindowsPhone** projektre, adja meg a következő kódot az App.xaml.cs regisztrálni a leküldéses háttérfeladatot. Szúrja be az alábbi kódsort az `OnLaunched()` metódus végén:

    ```c#
    RegisterBackgroundTask();
    ```
2. Továbbra is az App.XAML.cs fájlban adja hozzá a következő kód után azonnal a `OnLaunched()` módszer:

    ```c#
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
3. Adja hozzá a következő `using` tetején az App.xaml.cs fájlban található utasításokat:

    ```c#
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```
4. A Visual Studio **File** (Fájl) menüjében kattintson az **Save All** (Összes mentése) parancsra.

## <a name="create-the-push-background-component"></a>A leküldéses háttér összetevő létrehozása

A következő lépés, hogy a leküldéses háttér-összetevőt.

1. A Megoldáskezelőben kattintson a jobb gombbal a legfelső csomópontra, a megoldás (**megoldás SecurePush** ebben az esetben), majd kattintson a **Hozzáadás**, majd kattintson a **új projekt**.
2. Bontsa ki a **Store Apps**, majd kattintson a **Windows Phone-alkalmazások**, majd kattintson a **Windows futásidejű összetevő (Windows Phone)**. Adja a projektnek **PushBackgroundComponent**, és kattintson a **OK** a projekt létrehozásához.

    ![][12]
3. A Megoldáskezelőben kattintson a jobb gombbal a **PushBackgroundComponent (Windows Phone 8.1)** projektre, majd kattintson a **Hozzáadás**, majd kattintson a **osztály**. Az új osztály neve `PushBackgroundTask.cs`. Kattintson a **Hozzáadás** az osztály létrehozásához.
4. Cserélje le a teljes tartalmát a `PushBackgroundComponent` névtér-definíciót a következő kódra, és cserélje le a helyőrző `{back-end endpoint}` a háttér-végponttal, a háttér-üzembe helyezése során kapott:

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
5. A Megoldáskezelőben kattintson a jobb gombbal a **PushBackgroundComponent (Windows Phone 8.1)** projektre, majd kattintson **NuGet-csomagok kezelése**.
6. A képernyő bal oldalán kattintson az **Online** lehetőségre.
7. A **Search** (Keresés) mezőbe írja be a **Http Client** (HTTP-ügyfél) kifejezést.
8. Az eredmények listájában kattintson **Microsoft HTTP-Ügyfélkódtárak**, és kattintson a **telepítése**. Fejezze be a telepítést.
9. A NuGet **Search** (Keresés) mezőjébe írja be a **Json.net** kifejezést. Telepítse a **Json.NET** csomagot, majd zárja be a NuGet-Csomagkezelő ablakát.
10. Adja hozzá a következő `using` tetején található utasításokat a `PushBackgroundTask.cs` fájlt:

    ```c#
    using Windows.ApplicationModel.Background;
    using Windows.Networking.PushNotifications;
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using Windows.UI.Notifications;
    using Windows.Data.Xml.Dom;
    ```
11. A Megoldáskezelőben a a **NotifyUserWindowsPhone (Windows Phone 8.1)** projektre, kattintson a jobb gombbal **hivatkozások**, majd kattintson az **hivatkozás hozzáadása...** . A referencia-kezelő párbeszédpanelen jelölje be a jelölőnégyzetet a **PushBackgroundComponent**, és kattintson a **OK**.
12. A Megoldáskezelőben kattintson duplán a **Package.appxmanifest** a a **NotifyUserWindowsPhone (Windows Phone 8.1)** projekt. A **értesítések**állítsa be **bejelentési képes** való **Igen**.

    ![][3]
13. Még mindig **Package.appxmanifest**, kattintson a **nyilatkozatok** menü felső részén. Az a **elérhető nyilatkozatok** legördülő menüben kattintson a **háttérfeladatokat**, és kattintson a **Hozzáadás**.
14. A **Package.appxmanifest**alatt **tulajdonságok**, ellenőrizze **leküldéses értesítés**.
15. A **Package.appxmanifest**alatt **Alkalmazásbeállítások**, típus **PushBackgroundComponent.PushBackgroundTask** a a **belépési pont** a mező.

    ![][13]
16. Kattintson a **File** (Fájl) menü **Save All** (Összes mentése) elemére.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásához tegye a következőket:

1. A Visual Studióban futtassa a **AppBackend** webes API alkalmazást. Egy ASP.NET-weblap jelenik meg.
2. A Visual Studióban futtassa a **NotifyUserWindowsPhone (Windows Phone 8.1)** Windows Phone-alkalmazás. A Windows Phone-emulátort fut, és automatikusan betölti az alkalmazást.
3. Az a **NotifyUserWindowsPhone** alkalmazás felhasználói felületén, adjon meg egy felhasználónevet és jelszót. Ezek bármilyen karakterlánc lehet, de ugyanazt az értéket kell lenniük.
4. Az a **NotifyUserWindowsPhone** alkalmazás felhasználói felületén, kattintson a **jelentkezzen be, és regisztrálja**. Kattintson a **küldjön leküldéses**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
