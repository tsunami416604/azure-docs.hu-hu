---
title: Az ügyfél parancsainak teljesítése a Speech SDK-val
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan kezelheti az egyéni parancsok tevékenységeit egy ügyfélen a Speech SDK-val.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: trbye
ms.openlocfilehash: f11f5f3c2ad4c9f0241d34edeb664f739f88d15c
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871731"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Az ügyfél parancsainak teljesítése a Speech SDK-val (előzetes verzió)

A feladatok egyéni parancsok használatával történő elvégzéséhez egyéni adattartalomot küldhet egy csatlakoztatott ügyfél-eszközre.

Ebben a cikkben a következőket fogja megtekinteni:

- Egyéni JSON-adattartalom definiálása és küldése az egyéni parancsok alkalmazásából
- Egyéni JSON-adattartalom tartalmának fogadása és megjelenítése C# UWP Speech SDK-ügyfélalkalmazás alapján

## <a name="prerequisites"></a>Előfeltételek
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Egy Azure-előfizetési kulcs a Speech Service-hez: [egyszeri](get-started.md) letöltés vagy létrehozás a [Azure Portal](https://portal.azure.com)
> * Korábban létrehozott egyéni parancsok alkalmazás: gyors útmutató [: Egyéni parancs létrehozása paraméterekkel (előzetes verzió)](./quickstart-custom-speech-commands-create-parameters.md)
> * Egy Speech SDK-kompatibilis ügyfélalkalmazás: gyors útmutató [: Kapcsolódás egyéni parancssori alkalmazáshoz a SPEECH SDK-val (előzetes verzió)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Opcionális: gyors kezdés

Ez a cikk részletesen ismerteti, hogyan teheti meg az ügyfélalkalmazás az egyéni parancsok alkalmazással való kommunikációt. Ha szeretné, hogy a jelen cikkben szereplő teljes, kész fordítási forráskód elérhető legyen a [SPEECH SDK-mintákban](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>A JSON-adattartalommal való ellátás

1. Nyissa meg a korábban a gyors útmutatóból létrehozott egyéni parancsok alkalmazást [: hozzon létre egy egyéni parancsot paraméterekkel](./quickstart-custom-speech-commands-create-parameters.md)
1. A **befejezési szabályok** szakaszban ellenőrizze, hogy rendelkezik-e a korábban létrehozott szabállyal, amely visszaválaszol a felhasználónak
1. Ha közvetlenül az ügyfélnek szeretné elküldeni a hasznos adatokat, hozzon létre egy új szabályt egy küldési tevékenység művelettel.

   > [!div class="mx-imgBorder"]
   > ![Tevékenység-befejezési szabály küldése](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Beállítás | Ajánlott érték | Leírás |
   | ------- | --------------- | ----------- |
   | Szabály neve | UpdateDeviceState | A szabály célját leíró név |
   | Feltételek | Kötelező paraméter – `OnOff` és`SubjectDevice` | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e |
   | Műveletek | `SendActivity`(lásd alább) | A szabály feltételének teljesülésekor végrehajtandó művelet |

1. Az alábbi JSON másolása a **tevékenység tartalmába**
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
   > [!div class="mx-imgBorder"]
   > ![Tevékenység-adattartalom küldése](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

## <a name="create-visuals-for-device-on-or-off-state"></a>Vizualizációk létrehozása az eszköz be-és kikapcsolási állapotában

A rövid `turn on the tv`útmutató [: Kapcsolódás egyéni parancshoz a Speech SDK-val](./quickstart-custom-speech-commands-speech-sdk.md), létrehozott egy Speech SDK-ügyfélalkalmazás, amely a következő parancsokat kezeli:. `turn off the fan` Néhány vizualizáció hozzáadásával megtekintheti a parancsok eredményét.

Címkézett mezők hozzáadása vagy **kikapcsolása** szöveggel **a következő** XML-kód hozzáadásával`MainPage.xaml`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

## <a name="handle-customizable-payload"></a>Testreszabható hasznos adatok kezelése
### <a name="add-reference-libraries"></a>Hivatkozási kódtárak hozzáadása

Mivel létrehozott egy JSON-adattartalmat, a deszerializálás kezeléséhez hozzá kell adnia egy hivatkozást a [JSON.net](https://www.newtonsoft.com/json) -könyvtárhoz.
- A jobb ügyfél a megoldás.
- Válassza **a megoldás NuGet-csomagok kezelése**lehetőséget, majd válassza a **telepítés** elemet. 
- Keresse meg a **Newtonsoft. JSON** fájlt a frissítés listán, frissítse a **Microsoft. NETCore. UniversalWindowsPlatform** fájlt a legújabb verzióra

> [!div class="mx-imgBorder"]
> ![Tevékenység-adattartalom küldése](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

A "Főoldal. XAML. cs" elemnél adja hozzá a következőt:
- `using Newtonsoft.Json;` 
- `using Windows.ApplicationModel.Core;`

### <a name="handle-received-payload"></a>A fogadott tartalom kezelése

A `InitializeDialogServiceConnector`alkalmazásban cserélje `ActivityReceived` le az eseménykezelőt a következő kódra. A módosított `ActivityReceived` eseménykezelő Kinyeri a hasznos adatokat a tevékenységből, és ennek megfelelően megváltoztatja a televízió vagy ventilátor vizualizációs állapotát.

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
   > ![Tevékenység-adattartalom küldése](media/custom-speech-commands/fulfill-sdk-turn-on-tv.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató: az egyéni parancsok paramétereinek megadása (előzetes verzió)](./how-to-custom-speech-commands-validations.md)
