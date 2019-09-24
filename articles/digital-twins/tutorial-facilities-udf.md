---
title: 'Oktatóanyag: Tér monitorozása az Azure Digital Twins használatával | Microsoft Docs'
description: Ismerje meg, hogyan hozhatja létre a térbeli erőforrásokat, és hogyan figyelheti meg a munkafeltételeket az Azure digitális Twins szolgáltatásban az oktatóanyag lépéseivel.
services: digital-twins
author: alinamstanciu
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: alinast
ms.openlocfilehash: e483ac8e56ce39cbb05c5d00634c6327b497bab5
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219910"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins-preview"></a>Oktatóanyag: Az Azure Digital Twins előzetes verziójának kiépítése és a munkafeltételek monitorozása

Ez az oktatóanyag azt mutatja be, hogyan használható az Azure Digital Twins Preview a szóközök a kívánt hőmérsékleti feltételek és a komfort szintjének figyeléséhez. A [minta-összeállítás konfigurálása](tutorial-facilities-setup.md)után kiépítheti az összeállítást, és egyéni függvényeket futtathat az érzékelők adatain az oktatóanyag lépéseinek használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Határozza meg a figyelni kívánt feltételeket.
> * Hozzon létre egy felhasználó által definiált függvényt (UDF).
> * Az érzékelőre vonatkozó adatszimulálás
> * Felhasználó által definiált függvény eredményeinek beolvasása.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy [befejezte az Azure digitális Twins-telepítőjét](tutorial-facilities-setup.md). Mielőtt továbblépne, győződjön meg arról, hogy rendelkezik a következőkkel:

- Egy [Azure-fiók](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy futó Digital Twins-példány. 
- A munkavégzéshez használt gépre letöltött és kicsomagolt [Digital Twins C#-minták](https://github.com/Azure-Samples/digital-twins-samples-csharp). 
- [.Net Core SDK 2.1.403 vagy újabb verziót](https://www.microsoft.com/net/download) a fejlesztői gépen a minta létrehozásához és futtatásához. A `dotnet --version` futtatásával ellenőrizze, hogy a megfelelő verzió van-e telepítve. 
- [Visual Studio Code](https://code.visualstudio.com/) a mintakód vizsgálatához. 

> [!TIP]
> Új példány kiépítés esetén használjon egyedi digitális Twins-példány nevét.

## <a name="define-conditions-to-monitor"></a>A monitorozni kívánt feltételek meghatározása

Megadhatja az eszközön vagy az érzékelőn belüli figyeléshez megadott feltételek meghatározott feltételeit. Ezután megadhatja a függvények *felhasználó által definiált függvények*nevű függvényét. A felhasználó által definiált függvények egyéni logikát hajtanak végre a szóközökből és eszközökből származó adatokon, ha az egyeztetések által megadott feltételek történnek. További információért olvassa el az [adatfeldolgozás és a felhasználó által definiált függvények](concepts-user-defined-functions.md)című témakört. 

A **Foglaltság –** rövid útmutató minta projektben nyissa meg a **Src\actions\provisionSample.YAML** fájlt a Visual Studio Code-ban. Figyelje meg a **megfeleltetők** típussal kezdődő szakaszt. Az ebbe a típusba tartozó minden bejegyzés egy Matcher hoz létre a megadott **névvel**. A Matcher **dataTypeValue**típusú érzékelőt fog figyelni. Figyelje meg, hogy az a *Focus Room a1*nevű területhez kapcsolódik, amely tartalmaz néhány érzékelőt tartalmazó **eszközök** csomópontot. Egy olyan Matcher kiépítéséhez, amely ezen érzékelők valamelyikét nyomon fogja követni, győződjön meg arról, hogy a **dataTypeValue** megfelel az érzékelő adattípusának. 

Adja hozzá a következő Matcher a meglévő egyeztetések alatt. Ügyeljen arra, hogy a kulcsok összhangban legyenek, és a szóközök ne legyenek lecserélve tabulátorokra. Ezek a sorok a *provisionSample. YAML* fájlban is szerepelnek megjegyzésekkel ellátható sorokban. Az egyes sorok előtt a karakter eltávolításával `#` törölheti a megjegyzéseket.

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Ez a Matcher fogja követni `SAMPLE_SENSOR_TEMPERATURE` az [első oktatóanyagban](tutorial-facilities-setup.md)hozzáadott érzékelőt. 

## <a name="create-a-user-defined-function"></a>Felhasználó által meghatározott függvény létrehozása

A felhasználó által definiált függvények segítségével testre szabhatja az érzékelők adatainak feldolgozását. Egyéni JavaScript-kódok, amelyek az Azure Digital Twins-példányon futtathatók, ha az egyeztetések által leírtak szerint meghatározott feltételek történnek. A figyelni kívánt érzékelőkhöz tartozó egyezőket és felhasználó által definiált függvényeket is létrehozhat. További információért olvassa el az [adatfeldolgozás és a felhasználó által definiált függvények](concepts-user-defined-functions.md)című témakört. 

A minta *provisionSample. YAML* fájlban keressen egy olyan szakaszt, amely a **userdefinedfunctions**típussal kezdődik. Ez a szakasz egy felhasználó által definiált függvényt foglal le egy adott **névvel**. Ez az UDF a **matcherNames**alatti egyezések listáján működik. Figyelje meg, hogy megadhatja **szkriptként** a saját JavaScript-fájlját a felhasználó által meghatározott függvényhez.

A **roleassignments** nevű szakaszt is figyelje meg. Hozzárendeli a terület rendszergazdai szerepkört a felhasználó által definiált függvényhez. Ez a szerepkör lehetővé teszi az informatikai részleg számára a kiépített szóközökből származó események elérését. 

1. Konfigurálja úgy az UDF-et, hogy tartalmazza a hőmérséklet-megfeleltetőt. Ehhez adja hozzá az alábbi sort, vagy távolítsa el a megjegyzést az alábbi sorból a *provisionSample.yaml* fájl `matcherNames` csomópontjában:

    ```yaml
            - Matcher Temperature
    ```

1. Nyissa meg a szerkesztőben a **src\actions\userDefinedFunctions\availability.js** fájlt. Ez az *provisionSample. YAML* **parancsfájl** elemében hivatkozott fájl. A fájl felhasználó által definiált funkciója olyan feltételeket keres, amikor a helyiségben nem észlelhető mozgás, és a szén-dioxid szintje 1 000 ppm alá esik. 

   Módosítsa a JavaScript-fájlt a hőmérséklet és más feltételek figyelésére. Adja hozzá a következő sornyi kódot a feltételek megkereséséhez, ha a helyiségben nem észlelhető mozgás, a szén-dioxid szintje 1 000 ppm, és a hőmérséklet a 78 fokos Fahrenheit-fok alá esik.

   > [!NOTE]
   > Ez a szakasz módosítja a *src\actions\userDefinedFunctions\availability.js* fájlt, így részletesen megismerheti egy felhasználó által definiált függvény írására szolgáló módszert. Azonban dönthet úgy is, hogy közvetlenül a telepítőben használja a [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) fájlt. Ebben a fájlban szerepel az oktatóanyaghoz szükséges összes módosítás. Ha ehelyett ezt a fájlt használja, ügyeljen arra, hogy a [src\actions\provisionSample.YAML](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml)a parancsfájlhoz a megfelelő fájlnevet használja.

    a. A fájl elején adja hozzá a következő sorokat a hőmérséklethez az `// Add your sensor type here` megjegyzés alá:

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. Adja hozzá a következő sorokat a Megjegyzés `var motionSensor` `// Add your sensor variable here`alá tartozó utasítás után:

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    c. Adja hozzá a következő sort a Megjegyzés `var carbonDioxideValue` `// Add your sensor latest value here`alá tartozó utasítás után:

    ```JavaScript
        var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
    ```

    d. Távolítsa el a következő kódsorokat `// Modify this line to monitor your sensor value` megjegyzés alól:

     ```JavaScript
        if(carbonDioxideValue === null || motionValue === null) {
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide or motion are null, returning");
            return;
        }
    ```

    Cserélje le ezeket a következő sorokra:

    ```JavaScript
        if(carbonDioxideValue === null || motionValue === null || temperatureValue === null){
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide, motion, or temperature are null, returning");
            return;
        }
    ```

    e. Távolítsa el a következő kódsorokat `// Modify these lines as per your sensor` megjegyzés alól:

    ```JavaScript
        var availableFresh = "Room is available and air is fresh";
        var noAvailableOrFresh = "Room is not available or air quality is poor";
    ```

    Cserélje le ezeket a következő sorokra:

    ```JavaScript
        var alert = "Room with fresh air and comfortable temperature is available.";
        var noAlert = "Either room is occupied, or working conditions are not right.";
    ```

    f. Távolítsa el a következő *if-else* kódblokkot a `// Modify this code block for your sensor` megjegyzés után:

    ```JavaScript
        // If carbonDioxide less than threshold and no presence in the room => log, notify and set parent space computed value
        if(carbonDioxideValue < carbonDioxideThreshold && !presence) {
            log(`${availableFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, availableFresh);
        }
        else {
            log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

            // Set up custom notification for poor air quality
            parentSpace.Notify(JSON.stringify(noAvailableOrFresh));
        }
    ```

    És cserélje le az alábbi *if-else* blokkra:

    ```JavaScript
        // If sensor values are within range and room is available
        if(carbonDioxideValue < carbonDioxideThreshold && temperatureValue < temperatureThreshold && !presence) {
            log(`${alert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, alert);

            // Set up notification for this alert
            parentSpace.Notify(JSON.stringify(alert));
        }
        else {
            log(`${noAlert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAlert);
        }
    ```

    A módosított UDF olyan feltételt keres, amelyben elérhetővé válik egy helyiség, ahol a szén-dioxid és a hőmérséklet a megengedett korlátokon belül van. Létrehoz egy értesítést a `parentSpace.Notify(JSON.stringify(alert));` utasítással, amikor ez a feltétel teljesül. A feltétel teljesülésétől függetlenül beállítja a monitorozott tér értékét a megfelelő üzenettel.

    g. Mentse a fájlt.

1. Nyisson meg egy parancssorablakot, és lépjen a **Occupancy-quickstart\src**mappába. Futtassa a következő parancsot a térbeli intelligencia gráf és a felhasználó által definiált függvény kiépítéséhez:

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > Ha meg szeretné akadályozni, hogy a digitális Twins felügyeleti API jogosulatlanul hozzáférhessen, a foglalást kérő alkalmazás használatához be kell jelentkeznie az Azure-fiók hitelesítő adataival. Egy rövid ideig elmenti a hitelesítő adatait, így előfordulhat, hogy nem kell minden egyes futtatásakor bejelentkeznie. A program első futtatásakor, amikor a mentett hitelesítő adatok lejárnak, az alkalmazás egy bejelentkezési oldalra irányítja, és egy munkamenet-specifikus kódot ad meg az adott oldalon. Az utasításokat követve jelentkezzen be Azure-fiókjával.

1. A fiók hitelesítése után az alkalmazás megkezdi a *provisionSample. YAML*-ben konfigurált minta térbeli gráf létrehozását. Várjon, amíg befejeződik a kiépítés. Néhány percet is igénybe vehet. Ezután figyelje meg az üzeneteket a parancsablakban, és figyelje meg, hogyan jött létre a térbeli gráf. Figyelje meg, hogy az alkalmazás hogyan hoz létre egy IoT hubot a legfelső szintű csomóponton vagy a `Venue`-ben.

1. A parancs ablakában lévő kimenetből másolja a `ConnectionString`következőt a vágólapra a `Devices` szakasz alatt:. Erre az értékre szüksége lesz az eszköz kapcsolódásának szimulálása érdekében a következő szakaszban.

    ![Minta üzembe helyezése](./media/tutorial-facilities-udf/run-provision-sample.png)

> [!TIP]
> Ha a kiépítés során egy szál kilépése vagy egy alkalmazásra vonatkozó kérelem miatt megszakadt az I/O-művelethez hasonló hibaüzenet, próbálkozzon újra a parancs futtatásával. Ez akkor fordulhat elő, ha a HTTP-ügyfél hálózati hiba esetén időtúllépést okoz.

## <a name="simulate-sensor-data"></a>Az érzékelőadatok szimulálása

Ebben a szakaszban az *eszköz-kapcsolat* nevű projektet fogja használni a mintában. A mozgás, a hőmérséklet és a szén-dioxid észleléséhez szimulálnia kell az érzékelőt. Ez a projekt véletlenszerű értéket állít elő az érzékelők számára, és az eszköz kapcsolati sztringjével elküldi őket az IoT Hubnak.

1. Egy külön parancssorablakban nyissa meg az Azure Digital Twins mintát, majd az **eszköz-kapcsolat** mappát.

1. Futtassa ezt a parancsot annak ellenőrzéséhez, hogy helyesek-e a projekt függőségei:

    ```cmd/sh
    dotnet restore
    ```

1. Nyissa meg a [appSettings. JSON](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) fájlt a szerkesztőben, és szerkessze a következő értékeket:

   a. **DeviceConnectionString**: Rendelje hozzá az `ConnectionString` értéket az előző szakasz output (kimenet) ablakában. Másolja a karakterláncot teljes egészében az idézőjelek között, hogy a szimulátor megfelelően kapcsolódjon az IoT hub-hoz.

   b. **HardwareId** az **érzékelők** tömbön belül: Mivel az Azure Digital Twins-példányra kiépített érzékelőkből származó eseményeket szimulálja, az ebben a fájlban lévő hardver-azonosítónak és az érzékelők nevének meg kell `sensors` egyeznie a *provisionSample. YAML* fájl csomópontjához.

      Adjon hozzá egy új bejegyzést a hőmérséklet-érzékelőhöz. A *appSettings. JSON* **érzékelők** csomópontjának a következőhöz hasonlóan kell kinéznie:

      ```JSON
      "Sensors": [{
        "DataType": "Motion",
        "HardwareId": "SAMPLE_SENSOR_MOTION"
      },{
        "DataType": "CarbonDioxide",
        "HardwareId": "SAMPLE_SENSOR_CARBONDIOXIDE"
      },{
        "DataType": "Temperature",
        "HardwareId": "SAMPLE_SENSOR_TEMPERATURE"
      }]
      ```

1. Futtassa ezt a parancsot a hőmérséklettel, a mozgással és a szén-dioxiddal kapcsolatos eszközesemények szimulálásának elindításához:

    ```cmd/sh
    dotnet run
    ```

   > [!NOTE]
   > Mivel a szimulációs minta nem kommunikál közvetlenül a digitális Twins-példánnyal, nem szükséges hitelesítenie.

## <a name="get-results-of-the-user-defined-function"></a>A felhasználó által definiált függvény eredményeinek beolvasása

A felhasználó által meghatározott függvény mindig fut, amikor a példány eszköz- és érzékelőadatokat fogad. Ez a szakasz lekérdezi az Azure Digital Twins-példányt, hogy beolvassa a felhasználó által definiált függvény eredményét. A közel valós időben jelenik meg, amikor egy helyiség elérhetővé válik, hogy a levegő friss, a hőmérséklet pedig megfelelő legyen. 

1. Nyissa meg a minta kiépítéséhez használt parancssorablakot vagy egy új parancssorablakot, és lépjen a minta **Occupancy-quickstart\src** mappájába.

1. Futtassa a következő parancsot, és jelentkezzen be, amikor a rendszer kéri:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

A kimenet ablakban látható, hogy a felhasználó által definiált függvény hogyan fut, és az eszköz szimulációjában eseményeket észlel. 

   ![Az UDF kimenete](./media/tutorial-facilities-udf/udf-running.png)

Ha a figyelt feltétel teljesül, a felhasználó által definiált függvény megadja a terület értékét a megfelelő üzenettel, ahogy [korábban](#create-a-user-defined-function)láttuk. A `GetAvailableAndFreshSpaces` függvény kiírja az üzenetet a-konzolon.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, ezen a ponton felfedezése az Azure digitális Twins leállítására, nyugodtan ebben az oktatóanyagban létrehozott erőforrások törléséhez:

1. A bal oldali menüben lévő a [az Azure portal](https://portal.azure.com), jelölje be **összes erőforrás**, a digitális Twins erőforráscsoportot, válassza ki és **törlése**.

    > [!TIP]
    > Ha törli a digitális Twins-példány problémajegyek tapasztal, szolgáltatás frissítése lett állítva a javítás. Ismételje meg a példány törlése.

2. Ha szükséges, törölje a mintául szolgáló alkalmazásokat a munkahelyi gépen.

## <a name="next-steps"></a>További lépések

Most, hogy kiépítte a tárhelyeit, és létrehozott egy keretrendszert az egyéni értesítések elindításához, az alábbi oktatóanyagok egyikét használhatja:

> [!div class="nextstepaction"]
> [Oktatóanyag: Értesítések fogadása Azure digitális Twins-tárhelyekről Logic Apps használatával](tutorial-facilities-events.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure digitális Twins-terek eseményeinek megjelenítése és elemzése Time Series Insights használatával](tutorial-facilities-analyze.md)
