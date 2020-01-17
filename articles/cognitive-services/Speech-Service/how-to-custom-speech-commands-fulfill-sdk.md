---
title: Egyéni parancsok teljesítése az ügyfélen a Speech SDK-val
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan kezelheti az egyéni parancsok tevékenységeit az ügyfélen a Speech SDK-val.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: b55bb1c8379cf0a80a95aa0ba1a29297154d5831
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156505"
---
# <a name="how-to-fulfill-commands-on-the-client-with-the-speech-sdk-preview"></a>Útmutató: parancsok teljesítése az ügyfélen a Speech SDK-val (előzetes verzió)

A feladatok egyéni parancsok használatával történő elvégzéséhez egyéni adattartalomot küldhet egy csatlakoztatott ügyfél-eszközre.

Ebben a cikkben a következőket fogja megtekinteni:

- Egyéni JSON-adattartalom definiálása és küldése az egyéni parancsok alkalmazásából
- Az egyéni JSON-adattartalom tartalmának fogadása és C# megjelenítése egy UWP Speech SDK-ügyfélalkalmazás alapján

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- A Speech Service-hez készült Azure-előfizetési kulcs
  - [Szerezze be ingyen](get-started.md) , vagy hozza létre a [Azure Portal](https://portal.azure.com)
- Egy korábban létrehozott egyéni parancsok alkalmazás
  - [Gyors útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió)](./quickstart-custom-speech-commands-create-parameters.md)
- Egy Speech SDK-kompatibilis ügyfélalkalmazás
  - [Rövid útmutató: Kapcsolódás egyéni parancssori alkalmazáshoz a Speech SDK-val (előzetes verzió)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Opcionális: gyors kezdés

Ez a cikk részletesen ismerteti, hogyan teheti meg az ügyfélalkalmazás az egyéni parancsok alkalmazással való kommunikációt. Ha szeretné, hogy a jelen cikkben szereplő teljes, kész fordítási forráskód elérhető legyen a [SPEECH SDK-mintákban](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>A JSON-adattartalommal való ellátás

1. A korábban létrehozott egyéni parancsok alkalmazás megnyitása a [Speech studióból](https://speech.microsoft.com/)
1. A **befejezési szabályok** szakaszban ellenőrizze, hogy rendelkezik-e a korábban létrehozott szabállyal, amely visszaválaszol a felhasználónak
1. Ha közvetlenül az ügyfélnek szeretné elküldeni a hasznos adatokat, hozzon létre egy új szabályt egy küldési tevékenység művelettel.

   > [!div class="mx-imgBorder"]
   > ![küldési tevékenység befejezési szabálya](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Beállítás | Ajánlott érték | Leírás |
   | ------- | --------------- | ----------- |
   | Szabály neve | UpdateDeviceState | A szabály célját leíró név |
   | Feltételek | Kötelező paraméter – `OnOff` és `SubjectDevice` | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e |
   | Műveletek | `SendActivity` (lásd alább) | A szabály feltételének teljesülésekor végrehajtandó művelet |

   > [!div class="mx-imgBorder"]
   > ![küldési tevékenység adattartalma](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Vizualizációk létrehozása az eszköz be-és kikapcsolási állapotában

A rövid útmutatóban [: Kapcsolódás egyéni parancshoz a SPEECH SDK (előzetes verzió) használatával](./quickstart-custom-speech-commands-speech-sdk.md) létrehozott Speech SDK ügyfélalkalmazás, amely olyan parancsokat kezelt, mint például a `turn on the tv`, `turn off the fan`. Most adjon hozzá néhány vizualizációt, hogy láthassa a parancsok eredményét.

Címkézett mezők hozzáadása vagy **kikapcsolása** a **következő** XML-kód használatával `MainPage.xaml.cs`

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

Most, hogy létrehozott egy JSON-adattartalmat, a deszerializálás kezeléséhez hozzáadhat egy hivatkozást a [JSON.net](https://www.newtonsoft.com/json) -könyvtárhoz.

> [!div class="mx-imgBorder"]
> ![küldési tevékenység adattartalma](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

A `InitializeDialogServiceConnector` adja hozzá a következőt a `ActivityReceived`-eseménykezelőhöz. A további kód Kinyeri a hasznos adatokat a tevékenységből, és ennek megfelelően megváltoztatja a televízió vagy ventilátor vizualizációs állapotát.

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

## <a name="try-it-out"></a>Próbálja ki!

1. Az alkalmazás elindítása
1. Válassza a mikrofon engedélyezése lehetőséget.
1. A beszélgetés gomb kiválasztása
1. Mondjuk `turn on the tv`
1. A TV vizualizációs állapotának "on" értékre kell váltania

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Útmutató: az egyéni parancsok paramétereinek megadása (előzetes verzió)](./how-to-custom-speech-commands-validations.md)
