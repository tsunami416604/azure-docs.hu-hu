---
title: Egyéni parancsok tevékenység küldése ügyfélalkalmazás számára
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan küldhet tevékenységeket egyéni parancsok alkalmazásából a Speech SDK-t futtató ügyfélalkalmazás számára.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 0a3e3455615006c0e93cf32eebcdaedac9960a79
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307733"
---
# <a name="send-custom-commands-activity-to-client-application"></a>Egyéni parancsok tevékenység küldése ügyfélalkalmazás számára

Ebből a cikkből megtudhatja, hogyan küldhet tevékenységeket egyéni parancsok alkalmazásából a Speech SDK-t futtató ügyfélalkalmazás számára.

A következő feladatokat hajtja végre:

- Egyéni JSON-adattartalom definiálása és küldése az egyéni parancsok alkalmazásából
- Egyéni JSON-adattartalom tartalmának fogadása és megjelenítése C# UWP Speech SDK-ügyfélalkalmazás alapján

## <a name="prerequisites"></a>Előfeltételek
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Egy Azure-előfizetési kulcs a Speech Service-hez: [egyszeri](get-started.md) letöltés vagy létrehozás a [Azure Portal](https://portal.azure.com)
> * Egy korábban [létrehozott egyéni parancsok alkalmazás](quickstart-custom-commands-application.md)
> * Egy Speech SDK-kompatibilis ügyfélalkalmazás: [útmutató: integrálás egy ügyfélalkalmazás használatával a SPEECH SDK-val](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>A telepítő tevékenység küldése az ügyfélnek 
1. Nyissa meg a korábban létrehozott egyéni parancsok alkalmazást
1. Válassza a **TurnOnOff** parancsot, válassza a **ConfirmationResponse** lehetőséget a befejezési szabály területen, majd válassza **a művelet hozzáadása** lehetőséget.
1. Az **új művelet típusa**területen válassza a **tevékenység küldése ügyfélnek** lehetőséget.
1. Az alábbi JSON másolása a **tevékenység tartalmába**
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
1. Kattintson a **Mentés** gombra egy új szabály létrehozásához a küldési tevékenység művelettel

   > [!div class="mx-imgBorder"]
   > ![Tevékenység-befejezési szabály küldése](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>Integrálás az ügyfélalkalmazás alkalmazásával

[Útmutató: az ügyfélalkalmazás beállítása a SPEECH SDK-val (előzetes verzió)](./how-to-custom-commands-setup-speech-sdk.md), a Speech SDK-val létrehozott UWP-ügyfélalkalmazás olyan parancsokat kezelt, mint például a `turn on the tv` , `turn off the fan` . Néhány vizualizáció hozzáadásával megtekintheti a parancsok eredményét.

Címkézett mezők hozzáadása vagy **kikapcsolása** szöveggel **a következő** XML-kód hozzáadásával`MainPage.xaml`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

### <a name="add-reference-libraries"></a>Hivatkozási kódtárak hozzáadása

Mivel létrehozott egy JSON-adattartalmat, a deszerializálás kezeléséhez hozzá kell adnia egy hivatkozást a [JSON.net](https://www.newtonsoft.com/json) -könyvtárhoz.

1. A jobb ügyfél a megoldás.
1. Válassza **a megoldás NuGet-csomagok kezelése**lehetőséget, majd válassza a **telepítés** elemet. 
1. Keresse **megNewtonsoft.jsa** frissítés listán, frissítse a **Microsoft. NETCore. UniversalWindowsPlatform** a legújabb verzióra

> [!div class="mx-imgBorder"]
> ![Tevékenység-adattartalom küldése](media/custom-commands/send-activity-to-client-json-nuget.png)

A "Főoldal. XAML. cs" elemnél adja hozzá a következőt:
- `using Newtonsoft.Json;` 
- `using Windows.ApplicationModel.Core;`

### <a name="handle-the-received-payload"></a>A fogadott tartalom kezelése

A alkalmazásban `InitializeDialogServiceConnector` cserélje le az `ActivityReceived` eseménykezelőt a következő kódra. A módosított `ActivityReceived` eseménykezelő Kinyeri a hasznos adatokat a tevékenységből, és megváltoztatja a televízió vagy a ventilátor vizualizációs állapotát.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.device != null ? activity.state.ToString() : string.Empty;
        var device = activity?.device != null ? activity.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Próba

1. Az alkalmazás elindítása
1. Válassza a mikrofon engedélyezése lehetőséget.
1. A beszélgetés gomb kiválasztása
1. Mondani`turn on the tv`
1. A TV vizualizációs állapotának "on" értékre kell váltania
   > [!div class="mx-imgBorder"]
   > ![Tevékenység-adattartalom küldése](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató: webes végpontok beállítása (előzetes verzió)](./how-to-custom-commands-setup-web-endpoints.md)