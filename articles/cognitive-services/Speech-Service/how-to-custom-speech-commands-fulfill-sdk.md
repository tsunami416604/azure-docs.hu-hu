---
title: Az ügyfél parancsainak teljesítése a Speech SDK-val
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben bemutatjuk, hogyan kell kezelni az egyéni parancsok tevékenységeit egy ügyfélen a beszédskó-SDK-val.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367744"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Parancsok teljesítése egy ügyféltől a beszédfelismerési SDK-val (előzetes verzió)

Az egyéni parancsok alkalmazással végzett feladatok elvégzéséhez egyéni hasznos adatokat küldhet egy csatlakoztatott ügyféleszközre.

Ebben a cikkben a következőket fogja:

- Egyéni JSON-tartalom definiálása és küldése az egyéni parancsok alkalmazásból
- Az egyéni JSON-tartalom tartalom fogadása és megjelenítése c# UWP Speech SDK ügyfélalkalmazásból

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Egy Azure-előfizetési kulcs a beszédszolgáltatáshoz
  - [Szerezzen be egyet ingyen,](get-started.md) vagy hozza létre az [Azure Portalon](https://portal.azure.com)
- Egy korábban létrehozott Egyéni parancsok alkalmazás
  - [Rövid útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió)](./quickstart-custom-speech-commands-create-parameters.md)
- BeszédbeszédSDK-kompatibilis ügyfélalkalmazás
  - [Rövid útmutató: Csatlakozás egyéni parancsalkalmazáshoz a beszédfelismerési SDK-val (előzetes verzió)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Nem kötelező: Gyors kezdés

Ez a cikk lépésről lépésre ismerteti, hogyan lehet egy ügyfélalkalmazást az egyéni parancsok alkalmazással való kapcsolatra. Ha jobban bele szeretne merülni, a cikkben használt teljes, fordításra kész forráskód a [Beszéd SDK-minták](https://aka.ms/csspeech/samples)ban érhető el.

## <a name="fulfill-with-json-payload"></a>Teljesíts a JSON hasznos teherrel

1. A korábban létrehozott Egyéni parancsok alkalmazás megnyitása a [Beszédstúdióból](https://speech.microsoft.com/)
1. Ellenőrizze a **Befejezési szabályok** szakaszt, és győződjön meg arról, hogy a korábban létrehozott szabály, amely válaszol a felhasználónak
1. Ha egy hasznos adatközvetlenül az ügyfélnek szeretne küldeni, hozzon létre egy új szabályt a Tevékenység küldése művelettel

   > [!div class="mx-imgBorder"]
   > ![Tevékenység-kiegészítési szabály küldése](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Beállítás | Ajánlott érték | Leírás |
   | ------- | --------------- | ----------- |
   | Szabály neve | UpdateDeviceState | A szabály célját leíró név |
   | Feltételek | Kötelező paraméter `OnOff` - és`SubjectDevice` | Feltételek, amelyek meghatározzák, hogy a szabály mikor futhat |
   | Műveletek | `SendActivity`(lásd alább) | A szabályfeltétel igaz állapotában végrehajtandó művelet |

   > [!div class="mx-imgBorder"]
   > ![Tevékenység hasznos adatának küldése](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Vizualizációk létrehozása az eszköz állapotán kívül

Rövid [útmutató: Csatlakozás egyéni parancsalkalmazáshoz a Speech SDK (Előzetes verzió) segítségével](./quickstart-custom-speech-commands-speech-sdk.md) létrehozott egy beszédfelismerési SDK-ügyfélalkalmazást, amely olyan parancsokat kezelt, mint `turn on the tv`például a `turn off the fan`. Most adjon hozzá néhány vizualizációt, hogy láthassa a parancsok eredményét.

Címkézett mezők hozzáadása **be-** vagy **kikapcsolt** szöveggel a következő XML-fájl használatával`MainPage.xaml.cs`

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

## <a name="handle-customizable-payload"></a>Testre szabható hasznos adat kezelése

Most, hogy létrehozott egy JSON-hasznos adat, hozzáadhat egy hivatkozást a [JSON.NET](https://www.newtonsoft.com/json) könyvtárdeszerializálás kezelésére.

> [!div class="mx-imgBorder"]
> ![Tevékenység hasznos adatának küldése](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

Az `InitializeDialogServiceConnector` alábbi hozzáadása `ActivityReceived` az eseménykezelőhöz. A kiegészítő kód kivonja a hasznos terhet a tevékenységből, és ennek megfelelően módosítja a tv vagy a ventilátor vizuális állapotát.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);

    if(activity?.name == "SetDeviceState")
    {
        var state = activity?.state;
        var device = activity?.device;
        switch(device)
        {
            case "tv":
                State_TV.Text = state;
                break;
            case "fan":
                State_Fan.Text = state;
                break;
            default:
                NotifyUser($"Received request to set unsupported device {device} to {state}");
                break;
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
1. Válassza a Mikrofon engedélyezése lehetőséget
1. A Beszélgetés gomb kijelölése
1. Mondani`turn on the tv`
1. A tv vizuális állapotának "Be"

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató: Érvényesítéshozzáadása az egyéni parancsparaméterekhez (előzetes verzió)](./how-to-custom-speech-commands-validations.md)
