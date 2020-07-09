---
title: Webes végpontok beállítása (előzetes verzió)
titleSuffix: Azure Cognitive Services
description: webes végpontok beállítása egyéni parancsokhoz
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 0197bb81fdba8bab20742d95aebaa2028bb90c18
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027681"
---
# <a name="set-up-web-endpoints"></a>Webes végpontok beállítása

Ebből a cikkből megtudhatja, hogyan telepíthet webes végpontokat egy egyéni parancsok alkalmazásban, amely lehetővé teszi, hogy HTTP-kéréseket hozzon egy ügyfélalkalmazás számára. A következő feladatokat kell végrehajtania:

- Webes végpontok beállítása egyéni parancsok alkalmazásban
- Webes végpontok hívása egyéni parancsok alkalmazásban
- A webes végpontok válaszának fogadása 
- Webes végpontok válaszának integrálása egyéni JSON-adattartalomba, küldés és megjelenítés egy C# UWP Speech SDK-ügyfélalkalmazás alapján

## <a name="prerequisites"></a>Előfeltételek
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Egy Azure-előfizetési kulcs a Speech Service-hez: [egyszeri](get-started.md) letöltés vagy létrehozás a [Azure Portal](https://portal.azure.com)
> * Egy korábban [létrehozott egyéni parancsok alkalmazás](quickstart-custom-commands-application.md)
> * Egy beszédfelismerési SDK-val rendelkező ügyfélalkalmazás: [útmutató: tevékenység befejezése az ügyfélalkalmazás számára](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>Webes végpontok beállítása

1. Nyissa meg a korábban létrehozott egyéni parancsok alkalmazást. 
1. Nyissa meg a "webes végpontok" lapot, és kattintson az "új webes végpont" elemre.

   > [!div class="mx-imgBorder"]
   > ![Új webes végpont](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Beállítás | Ajánlott érték | Leírás |
   | ------- | --------------- | ----------- |
   | Name | UpdateDeviceState | A webes végpont neve. |
   | URL-cím | https://webendpointexample.azurewebsites.net/api/DeviceState | Annak a végpontnak az URL-címe, amelyet az egyéni alkalmazásnak szeretne beszélni. |
   | Metódus | POST | Az engedélyezett interakciók (például GET, POST) és a végpont.|
   | Fejlécek | Kulcs: alkalmazás, érték: a applicationId első 8 számjegyének elkészítése | A kérelem fejlécében szerepeltetni kívánt fejléc-paraméterek.|

    > [!NOTE]
    > - Az [Azure Function](https://docs.microsoft.com/azure/azure-functions/)használatával létrehozott webes végpont, amely összekapcsolja az adatbázist, amely a televízió és a ventilátor eszköz állapotát menti
    > - A javasolt fejléc csak a példában szereplő végponthoz szükséges.
    > - Annak érdekében, hogy a fejléc értéke egyedi legyen a példában szereplő végponton, a applicationId első 8 számjegyét használja
    > - A valós világban a webes végpont az eszközöket kezelő [IOT hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) végpontja lehet.

1. Kattintson a **Save** (Mentés) gombra.

## <a name="call-web-endpoints"></a>Webes végpontok hívása

1. Nyissa meg a **TurnOnOff** parancsot, válassza a **ConfirmationResponse** lehetőséget a befejezési szabály területen, majd válassza **a művelet hozzáadása**lehetőséget.
1. Az **új művelet típusa**területen válassza a **hívás webes végpont** elemet.
1. A **művelet szerkesztése – végpontok**területen válassza a **UpdateDeviceState**elemet, amely az általunk létrehozott webes végpont.  
1. A **konfiguráció**területen tegye a következő értékeket: 
   > [!div class="mx-imgBorder"]
   > ![Webes végpontok műveleti paramétereinek hívása](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Beállítás | Ajánlott érték | Leírás |
   | ------- | --------------- | ----------- |
   | Végpontok | UpdateDeviceState | A műveletben hívni kívánt webes végpont. |
   | Lekérdezési paraméterek | Item = {SubjectDevice} &&érték = {javítás OnOff} | A webes végpont URL-címéhez hozzáfűzni kívánt lekérdezési paraméterek.  |
   | Szövegtörzs tartalma | N.A. | A kérelem törzsének tartalma. |

    > [!NOTE]
    > - A javasolt lekérdezési paraméterek csak a példában szereplő végponthoz szükségesek

1. A **sikeres művelet végrehajtásához**válassza a **beszédfelismerési válasz küldése**lehetőséget.
    
    Az **egyszerű szerkesztőben**írja be a értéket `{SubjectDevice} is {OnOff}` .
   
   > [!div class="mx-imgBorder"]
   > ![A webes végpontok műveletének hívása sikeres](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Beállítás | Ajánlott érték | Leírás |
   | ------- | --------------- | ----------- |
   | Végrehajtandó művelet | Beszédfelismerési válasz küldése | Végrehajtandó művelet, ha a webes végpontra irányuló kérelem sikeres |
   
   > [!NOTE]
   > - A használatával közvetlenül is elérheti a mezőket a http-válaszban `{YourWebEndpointName.FieldName}` . Például:`{UpdateDeviceState.TV}`

1. A **sikertelen művelet végrehajtásához**válassza a **beszédfelismerési válasz küldése** lehetőséget.

    Az **egyszerű szerkesztőben**írja be a értéket `Sorry, {WebEndpointErrorMessage}` .

   > [!div class="mx-imgBorder"]
   > ![Hiba esetén a webes végpontok hívása művelet](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Beállítás | Ajánlott érték | Leírás |
   | ------- | --------------- | ----------- |
   | Végrehajtandó művelet | Beszédfelismerési válasz küldése | Végrehajtandó művelet, ha a webes végpontra irányuló kérelem meghiúsul |

   > [!NOTE]
   > - A(z) `{WebEndpointErrorMessage}` nem kötelező. Ha nem szeretne hibaüzenetet kitenni, távolítsa el.
   > - A példában szereplő végponton a http-választ a gyakori hibákra, például a hiányzó fejléc-paraméterekre vonatkozó részletes hibaüzenetek küldi vissza. 

### <a name="try-it-out-in-test-portal"></a>Kipróbálás a tesztelési portálon
- Sikeres válasz \
Mentés, betanítás és tesztelés
   > [!div class="mx-imgBorder"]
   > ![A webes végpontok műveletének hívása sikeres](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Sikertelen válasz \
A lekérdezési paraméterek egyikének eltávolítása, mentés, újraképzés és tesztelés
   > [!div class="mx-imgBorder"]
   > ![A webes végpontok műveletének hívása sikeres](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integrálás az ügyfélalkalmazás alkalmazásával

[Útmutató: tevékenység küldése az ügyfélalkalmazás számára (előzetes verzió)](./how-to-custom-commands-send-activity-to-client.md)a **küldési tevékenység hozzáadása az ügyfélhez** művelet. A rendszer elküldi a tevékenységet az ügyfélalkalmazás számára, hogy a **webes végpont** művelete sikeres-e, vagy sem.
Azonban a legtöbb esetben csak akkor szeretne tevékenységet küldeni az ügyfélalkalmazás számára, ha a webes végpontra irányuló hívás sikeres. Ebben a példában ez az, amikor az eszköz állapota sikeresen frissült.

1. Törölje a **küldési tevékenységet a korábban hozzáadott ügyfél-** művelethez.
1. Hívás webes végpontjának szerkesztése: 
    1. A **konfiguráció**területen győződjön meg arról, hogy a **lekérdezési paraméterek**`item={SubjectDevice}&&value={OnOff}`
    1. A **sikeres**művelet esetén a **tevékenység küldése az ügyfélnek** **művelet végrehajtásához** módosítsa a műveletet.
    1. Másolja az alábbi JSON-t a **tevékenység tartalmába**
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
    > [!div class="mx-imgBorder"]
    > ![Sikeres művelet küldése](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-activity.png)
   
Most csak akkor küld tevékenységet az ügyfélnek, ha a webes végpontra irányuló kérelem sikeres.

### <a name="create-visuals-for-syncing-device-state"></a>Vizualizációk létrehozása az eszköz állapotának szinkronizálásához
Adja hozzá a következő XML-kódot a `MainPage.xaml` `"EnableMicrophoneButton"` blokk fölé.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Eszköz állapotának szinkronizálása 

A alkalmazásban `MainPage.xaml.cs` adja hozzá a hivatkozást `using Windows.Web.Http;` . Adja hozzá a következő kódot a `MainPage` osztályhoz. Ez a metódus GET kérelmet küld a példában szereplő végpontnak, és kibontja az alkalmazás aktuális állapotát. Ügyeljen arra, hogy `<your_app_name>` az egyéni parancs webes végpontjában a **fejlécben** használt értékre váltson.

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request. 
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>Próba

1. Az alkalmazás elindítása
1. Kattintson az eszköz állapotának szinkronizálása elemre. \
Ha az előző szakaszban kipróbálta az alkalmazást `turn on tv` , a TV a következőt jeleníti meg: "on".
    > [!div class="mx-imgBorder"]
    > ![Eszköz állapotának szinkronizálása](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Válassza a mikrofon engedélyezése lehetőséget.
1. A beszélgetés gomb kiválasztása
1. Mondani`turn on the fan`
1. A ventilátor vizualizációs állapotának "on" értékre kell váltania
    > [!div class="mx-imgBorder"]
    > ![Ventilátor bekapcsolása](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [CI/CD-folyamat engedélyezése az egyéni parancsok alkalmazáshoz](./how-to-custom-commands-deploy-cicd.md)