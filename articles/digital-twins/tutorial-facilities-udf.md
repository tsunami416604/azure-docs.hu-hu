---
title: 'Oktatóanyag: IoT-eszközterület figyelése – Azure Digital Twins| Microsoft dokumentumok'
description: Ismerje meg, hogyan építheti ki a térbeli erőforrásokat, és figyelheti a munkakörülményeket az Azure Digital Twins segítségével az oktatóanyag lépései vel.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 6cf6a8f7de181a81d60028e33ba2631815c8ca04
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75895367"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins-preview"></a>Oktatóanyag: Az azure digital twins előzetes verzióval kiépítheti az épületet és figyelheti a munkakörülményeket

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Digital Twins Preview-t a kívánt hőmérsékleti feltételek és komfortszint figyeléséhez. A [mintaépület konfigurálása](tutorial-facilities-setup.md)után kiépítheti az épületet, és egyéni függvényeket futtathat az érzékelő adatokon az oktatóanyag lépései vel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adja meg a figyelni ezeket a feltételeket.
> * Felhasználó által definiált függvény (UDF) létrehozása.
> * Érzékelőadatok szimulálása.
> * A felhasználó által definiált függvény eredményeinek beszerezése.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy [befejezte az Azure Digital Twins beállítását.](tutorial-facilities-setup.md) Mielőtt továbblépne, győződjön meg arról, hogy rendelkezik a következőkkel:

- Egy [Azure-fiók](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy futó Digital Twins-példány. 
- A munkavégzéshez használt gépre letöltött és kicsomagolt [Digital Twins C#-minták](https://github.com/Azure-Samples/digital-twins-samples-csharp). 
- [A .NET Core SDK 2.1.403-as vagy újabb verziója](https://www.microsoft.com/net/download) a fejlesztőgépen a minta létrehozásához és futtatásához. Futtassa `dotnet --version` a megfelelő verzió telepítésének ellenőrzéséhez. 
- [Visual Studio Code](https://code.visualstudio.com/) a mintakód vizsgálatához. 

>[!TIP]
> Használjon egy egyedi digital Twins-példány nevét, ha új példányt létesít.

## <a name="define-conditions-to-monitor"></a>A monitorozni kívánt feltételek meghatározása

Megadhatja az eszköz vagy az érzékelő adatainak figyelésére vonatkozó meghatározott feltételeket, *amelyeket matchers-nek neveznek.* Ezután megadhatja *a felhasználó által definiált függvényeknek nevezett függvényeket.* A felhasználó által definiált függvények egyéni logikát hajtanak végre a terekből és eszközökből származó adatokon, amikor a matchers által meghatározott feltételek bekövetkeznek. További információ: [Adatfeldolgozás és felhasználó által definiált függvények.](concepts-user-defined-functions.md) 

Az **occupancy-quickstart** mintaprojektben nyissa meg az **src\actions\provisionSample.yaml** nevű fájlt a Visual Studio Code-ban. Figyelje meg a **megfeleltetők** típussal kezdődő szakaszt. Az ilyen típusú bejegyzések minden ilyen típusú egyeztetőt hoznak létre a megadott **névvel.** A matcher **egy dataTypeValue**típusú érzékelőt figyel. Figyelje meg, hogyan kapcsolódik a *Focus Room A1*nevű területhez, amely néhány érzékelőt tartalmazó **eszközcsomóval** rendelkezik. Ha olyan egyezőgépet szeretne kiépíteni, amely nyomon követi az egyik ilyen érzékelőt, győződjön meg arról, hogy az **dataTypeValue** értéke megegyezik az érzékelő **dataType**értékével. 

Adja hozzá a következő matcher alatt a meglévő matchers. Győződjön meg arról, hogy a billentyűk egy vonalban vannak, és a szóközöket nem cserélik le a tabulátorok. Ezek a sorok is szerepelnek a *provisionSample.yaml* fájlban megjegyzéssel kiadott sorokként. A megjegyzéshez fűzött megjegyzés `#` nélkül eltávolíthatja az egyes sorok előtti karaktert.

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Ez a matcher `SAMPLE_SENSOR_TEMPERATURE` nyomon követi az érzékelő, hogy a hozzáadott [az első bemutató](tutorial-facilities-setup.md). 

## <a name="create-a-user-defined-function"></a>Felhasználó által meghatározott függvény létrehozása

A felhasználó által definiált függvények segítségével testreszabhatja az érzékelő adatok feldolgozását. Ezek egyéni JavaScript-kódok, amelyek az Azure Digital Twins-példányon belül futtathatók, ha a matchers által leírt meghatározott feltételek lépnek fel. Mindegyik monitorozni kívánt érzékelőhöz létrehozhat megfeleltetőket és felhasználó által meghatározott függvényeket. További információ: [Adatfeldolgozás és felhasználó által definiált függvények.](concepts-user-defined-functions.md) 

A *mintaprovisionSample.yaml* fájlban keressen egy olyan **szakaszt, amely a userdefinedfunctions**típussal kezdődik. Ez a szakasz egy felhasználó által definiált függvényt határoz meg egy adott **névvel.** Ez az UDF a **matcherNames**alatt lévő egyeztetők listáján szerepel. Figyelje meg, hogy megadhatja **szkriptként** a saját JavaScript-fájlját a felhasználó által meghatározott függvényhez.

A **roleassignments** nevű szakaszt is figyelje meg. A Helykezelő szerepkört a felhasználó által definiált függvényhez rendeli. Ez a szerepkör lehetővé teszi, hogy a kiosztott terek bármelyikéből származó események eléréséhez. 

1. Konfigurálja úgy az UDF-et, hogy tartalmazza a hőmérséklet-megfeleltetőt. Ehhez adja hozzá az alábbi sort, vagy távolítsa el a megjegyzést az alábbi sorból a *provisionSample.yaml* fájl `matcherNames` csomópontjában:

    ```yaml
            - Matcher Temperature
    ```

1. Nyissa meg az **src\actions\userDefinedFunctions\availability.js** fájlt a szerkesztőben. Ez a *provisionSample.yaml* **parancsfájlelemben** hivatkozott fájl. A felhasználó által definiált funkció ebben a fájlban keresi a feltételeket, amikor nem mutat nak ki mozgást a szobában, és a szén-dioxid szintje 1000 ppm alatt van. 

   Módosítsa a JavaScript-fájlt a hőmérséklet és egyéb feltételek figyeléséhez. Add hozzá a következő kódsorokat, hogy megkeressük azokat a körülményeket, amikor a helyiségben nem észlelnek mozgást, a szén-dioxid szintje 1000 ppm alatt van, és a hőmérséklet 78 Fahrenheit fok alatt van.

   >[!NOTE]
   > Ez a szakasz módosítja a fájlt *src\actions\userDefinedFunctions\availability.js,* így részletesen megismerheti a felhasználó által definiált függvény írását. Azonban közvetlenül használhatja a [fájlt src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) a telepítésben. Ebben a fájlban szerepel az oktatóanyaghoz szükséges összes módosítás. Ha ezt a fájlt használja helyette, győződjön meg arról, hogy a **parancsfájlkulcs** megfelelő fájlnevét használja az [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml)fájlban.

    a. A fájl elején adja hozzá a következő sorokat a hőmérséklethez az `// Add your sensor type here` megjegyzés alá:

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. A megjegyzést a megjegyzés alá `var motionSensor`kell adni `// Add your sensor variable here`a következő sorokat a megjegyzést meghatározó utasítás után:

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    c. A megjegyzést a megjegyzés alá `var carbonDioxideValue`kell adni `// Add your sensor latest value here`a következő sort a megjegyzést meghatározó utasítás után:

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

1. Nyisson meg egy parancsablakot, és lépjen a mappa **kihasználtsága\src mappára.** Futtassa a következő parancsot a térintelligencia-grafikon és a felhasználó által definiált függvény kiépítéséhez:

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   >[!IMPORTANT]
   > A Digital Twins Management API-hoz való jogosulatlan hozzáférés megakadályozása érdekében a **kihasználtsági gyorsindítási** alkalmazás megköveteli, hogy az Azure-fiók hitelesítő adataival jelentkezzen be. Rövid időre menti a hitelesítő adatokat, így előfordulhat, hogy nem kell minden alkalommal bejelentkeznie, amikor futtatja. A program első futtatásakor, és amikor a mentett hitelesítő adatok lejárnak, az alkalmazás egy bejelentkezési lapra irányítja, és egy munkamenet-specifikus kódot ad meg az adott oldalon. Az utasításokat követve jelentkezzen be Azure-fiókjával.

1. A fiók hitelesítése után az alkalmazás megkezdi egy minta térbeli grafikon létrehozását *az provisionSample.yaml*konfigurálva. Várjon, amíg a kiépítés befejeződik. Beletelik pár percbe. Ezután figyelje meg az üzeneteket a parancsablakban, és figyelje meg, hogyan jön létre a térbeli grafikon. Figyelje meg, hogy az alkalmazás hogyan hoz `Venue`létre egy IoT-központot a gyökércsomóponton vagy a .

1. A parancsablak kimenetéből `ConnectionString`másolja a szakasz `Devices` alatti értéket a vágólapra. Erre az értékre szüksége lesz az eszközkapcsolat szimulálásához a következő szakaszban.

    [![Rendelkezési minta](./media/tutorial-facilities-udf/run-provision-sample.png)](./media/tutorial-facilities-udf/run-provision-sample.png#lightbox)

>[!TIP]
> Ha a kiépítés közepén a "Az I/O-művelet szálkilépés vagy alkalmazáskérelem miatt" parancshoz hasonló hibaüzenet jelenik meg, próbálja meg újra futtatni a parancsot. Ez akkor fordulhat elő, ha a HTTP-ügyfél időbeli egy hálózati probléma miatt időzít.

## <a name="simulate-sensor-data"></a>Az érzékelőadatok szimulálása

Ebben a szakaszban a mintában az *eszközkapcsolat* nevű projektet fogja használni. Az érzékelők adatait szimulálja a mozgás, a hőmérséklet és a szén-dioxid érzékelésére. Ez a projekt véletlenszerű értéket állít elő az érzékelők számára, és az eszköz kapcsolati sztringjével elküldi őket az IoT Hubnak.

1. Egy külön parancsablakban nyissa meg az Azure Digital Twins mintát, majd az **eszköz-kapcsolat** mappát.

1. Futtassa ezt a parancsot annak ellenőrzéséhez, hogy helyesek-e a projekt függőségei:

    ```cmd/sh
    dotnet restore
    ```

1. Nyissa meg az [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) fájlt a szerkesztőben, és szerkesztse a következő értékeket:

   a. **DeviceConnectionString**: Rendelje hozzá a `ConnectionString` értékét az előző szakasz kimeneti ablakából. Másolja ezt a karakterláncot teljesen az idézőjelek között, így a szimulátor megfelelően csatlakozhat az IoT hubhoz.

   b. **HardwareId** a **érzékelők** tömbön belül: Mivel az Azure Digital Twins-példányra kiépített érzékelők eseményeit szimulálja, a hardverazonosítónak és `sensors` a fájlban lévő érzékelők nevének meg kell egyeznie a *provisionSample.yaml* fájl csomópontjával.

      Adjon hozzá egy új bejegyzést a hőmérséklet-érzékelőhöz. Az *appsettings.json* **érzékelőcsomópontja** a következőhöz hasonlóan néz ki:

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

   >[!NOTE]
   > Mivel a szimulációs minta nem kommunikál közvetlenül a digitális twins-példány, nem igényel hitelesítést.

## <a name="get-results-of-the-user-defined-function"></a>A felhasználó által definiált függvény eredményeinek beszerezése

A felhasználó által meghatározott függvény mindig fut, amikor a példány eszköz- és érzékelőadatokat fogad. Ez a szakasz lekérdezi az Azure digital Twins-példányt a felhasználó által definiált függvény eredményeinek lekérdezi. Majd értesíti a közel valós időben, ha egy szoba áll rendelkezésre, hogy a levegő friss és a hőmérséklet megfelelő. 

1. Nyissa meg a minta vagy egy új parancsablak kiépítéséhez használt parancsablakot, és nyissa meg újra a minta **kihasználtsági gyorsstart\src** mappáját.

1. Futtassa a következő parancsot, és jelentkezzen be, amikor a rendszer kéri:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

A kimeneti ablak azt mutatja be, hogy a felhasználó által definiált függvény hogyan fut, és elfogja az eseményeket az eszközszimulációból. 

   [![Az UDF kimenete](./media/tutorial-facilities-udf/adt-tutorial-udf-running.png)](./media/tutorial-facilities-udf/adt-tutorial-udf-running.png#lightbox)

Ha a figyelt feltétel teljesül, a felhasználó által definiált függvény a megfelelő üzenettel állítja be a tér értékét, ahogy [azt korábban](#create-a-user-defined-function)láttuk. A `GetAvailableAndFreshSpaces` függvény kinyomtatja az üzenetet a konzolon.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezen a ponton le szeretné állítani az Azure Digital Twins felfedezését, nyugodtan törölje az oktatóanyagban létrehozott erőforrásokat:

1. Az [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza az **Összes erőforrás**lehetőséget, válassza ki a Digitális Twins erőforráscsoportot, és válassza a **Törlés**lehetőséget.

    >[!TIP]
    > Ha problémát tapasztalt a Digitális Twins-példány törlésekor, a javítással egy szolgáltatásfrissítés lett elvezetve. Próbálja meg újra kihagyni a példányt.

2. Szükség esetén törölje a mintaalkalmazásokat a munkagépről.

## <a name="next-steps"></a>További lépések

Most, hogy kiépítette a tereket, és létrehozott egy keretrendszert az egyéni értesítések indításához, az alábbi oktatóanyagok egyikét teheti meg:

> [!div class="nextstepaction"]
> [Oktatóanyag: Értesítések fogadása az Azure Digital Twins-terektől a Logic Apps használatával](tutorial-facilities-events.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Digital Twins-terek eseményeinek vizualizációja és elemzése a Time Series Insights használatával](tutorial-facilities-analyze.md)
