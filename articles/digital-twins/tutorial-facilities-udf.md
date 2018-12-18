---
title: 'Oktatóanyag: Tér monitorozása az Azure Digital Twins használatával | Microsoft Docs'
description: Ismerje meg, hogyan lehet a térbeli erőforrások kiépítése és megfigyelik a munkát az Azure digitális Twins ebben az oktatóanyagban szereplő lépések segítségével.
services: digital-twins
author: dsk-2015
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/26/2018
ms.author: dkshir
ms.openlocfilehash: 077dee19bbe32379bc88919117b3c61177828094
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556101"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins"></a>Oktatóanyag: Üzembe helyezheti a létrehozása és használata az Azure digitális Twins feltételek figyelése

Ez az oktatóanyag bemutatja, hogyan monitorozhatja a terek kívánt hőmérsékletét és komfortszintjét az Azure Digital Twins használatával. Miután [konfigurálja a minta létrehozása](tutorial-facilities-setup.md), üzembe helyezése a az épület, és felhasználói függvények futhatnak az érzékelőktől kapott adatok ebben az oktatóanyagban szereplő lépések segítségével.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adja meg a figyelendő feltételeit.
> * Hozzon létre egy felhasználói függvény (UDF).
> * Érzékelőktől kapott adatok szimulálásához.
> * Egy felhasználó által definiált függvény eredményeinek beolvasása.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy [befejeződött az Azure digitális Twins telepítő](tutorial-facilities-setup.md). Mielőtt továbblépne, győződjön meg arról, hogy rendelkezik a következőkkel:
- Egy [Azure-fiók](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy futó Digital Twins-példány. 
- A munkavégzéshez használt gépre letöltött és kicsomagolt [Digital Twins C#-minták](https://github.com/Azure-Samples/digital-twins-samples-csharp). 
- [.NET core SDK 2.1.403 verzió vagy újabb](https://www.microsoft.com/net/download) a fejlesztői gépen való létrehozásához és a minta futtatásához. Futtatás `dotnet --version` , győződjön meg arról, hogy telepítve van-e a megfelelő verziót. 
- [Visual Studio Code](https://code.visualstudio.com/) a mintakód vizsgálatához. 

## <a name="define-conditions-to-monitor"></a>A monitorozni kívánt feltételek meghatározása
Meghatározhat adott feltételkészleteket, az eszköz vagy az érzékelőadatok adatok, nevű figyelése *matchers*. Megadhatja, melynek neve *felhasználó által definiált függvények*. Felhasználó által definiált függvények adatokon, a tárolóhelyek és az eszközök, előre a matchers által megadott feltételek esetén hajtsa végre az egyéni logikát. További információkért olvassa el [adatfeldolgozás és a felhasználó által definiált függvények](concepts-user-defined-functions.md). 

Az a **foglaltsága-quickstart** projekt minta, nyissa meg a fájlt **src\actions\provisionSample.yaml** Visual Studio Code-ban. Figyelje meg a **megfeleltetők** típussal kezdődő szakaszt. Minden ilyen típusú bejegyzést hoz létre egy megfeleltetőben megadott a megadott **neve**. A megfeleltetőben megadott figyelni fogja az érzékelő típusú **dataTypeValue**. Figyelje meg, hogy miként kapcsolódik a nevű terület *fókusz szoba A1*, amely rendelkezik egy **eszközök** néhány érzékelők tartalmazó csomópont. Üzembe helyez egy megfeleltetőben megadott érzékelőktől egyik követő, győződjön meg arról, hogy a **dataTypeValue** megegyezik az érzékelő **dataType**. 

Adja hozzá a következő megfeleltetőben megadott, a meglévő matchers alatt. Ellenőrizze, hogy a kulcsok vannak elhelyezve, és a tárolóhelyek tabulátorokkal nem cserélhető le.

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Ez megfeleltetőben megadott fogja követni az SAMPLE_SENSOR_TEMPERATURE érzékelő, amelyet a [első oktatóanyaga](tutorial-facilities-setup.md). Ezek a sorok is megtalálhatók a *provisionSample.yaml* ellátva kibővített sorok fájlt. Akkor is távolítsa el őket eltávolításával a `#` karakter minden sor elé. 

<a id="udf" />

## <a name="create-a-user-defined-function"></a>Felhasználó által meghatározott függvény létrehozása
Felhasználó által definiált függvények segítségével testre szabhatja a feldolgozást az érzékelők adatstreamének felhasználásával. Azok az egyéni JavaScript-kódot, az Azure digitális Twins példány futtathat, amikor megadott események leírtak szerint a matchers történnek. Matchers és felhasználó által definiált függvények minden érzékelő, amely a figyelni kívánt hozhat létre. További információkért olvassa el [adatfeldolgozás és a felhasználó által definiált függvények](concepts-user-defined-functions.md). 

A minta provisionSample.yaml fájlban keresse meg egy szakaszt, azzal a típussal kezdődő **userdefinedfunctions**. Ez a szakasz építi ki a felhasználó által definiált függvény és egy adott **neve**. Az UDF-ben kezelje alatt matchers listájának **matcherNames**. Figyelje meg, hogy megadhatja **szkriptként** a saját JavaScript-fájlját a felhasználó által meghatározott függvényhez. 

A **roleassignments** nevű szakaszt is figyelje meg. A hely rendszergazdai szerepkört rendel a felhasználó által definiált függvényt. Ez a szerepkör lehetővé teszi, hogy hozzáférjen az események, amelyek a kiosztott tárolóhelyek bármelyikét származnak. 

1. Az UDF-ben, hogy tartalmazzák a hőmérséklet-megfeleltetőben megadott hozzáadásával, illetve a következő sort a uncommenting konfigurálása a `matcherNames` provisionSample.yaml fájl csomópont:

    ```yaml
            - Matcher Temperature
    ```

1. Nyissa meg a fájlt **src\actions\userDefinedFunctions\availability.js** a saját szerkesztőben. Ez az, hogy a fájl hivatkozik a **parancsfájl** provisionSample.yaml eleme. A felhasználó által definiált függvény ebben a fájlban a feltételek úgy tűnik, nincs mozgásban van a helyiségben észlel, és szén-dioxid szintje alatt 1000 ppm van. 

   A JavaScript-fájlt, a figyelő hőmérséklet és egyéb feltételek módosítása. Adja hozzá a következő kódsorokat és tekintse meg a feltételek nem mozgásban van a helyiségben észlel, szén-dioxid szintje alatt 1000 ppm van, és hőmérséklet 78 fok Fahrenheit alatt van.

   > [!NOTE]
   > Ez a szakasz módosítja a fájlt *src\actions\userDefinedFunctions\availability.js* úgy tudhat meg részletesen egyik módja egy felhasználó által definiált függvény írni. Választhat, azonban közvetlenül a fájl használatára [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) a beállításokban. Ebben a fájlban szerepel az oktatóanyaghoz szükséges összes módosítás. Ha inkább ezt a fájlt használja, ügyeljen arra, hogy a megfelelő fájlt a nevet a **parancsfájl** kulcs az [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

    a. A fájl elején adja hozzá a következő sorokat a hőmérséklethez az `// Add your sensor type here` megjegyzés alá:

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. Adja hozzá a következő sorokat, amely meghatározza az utasítás után `var motionSensor`, a hozzászólás alatt `// Add your sensor variable here`:

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    c. Adja hozzá a következő sort, amely meghatározza az utasítás után `var carbonDioxideValue`, a hozzászólás alatt `// Add your sensor latest value here`:

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

            // Set up custom notification for air quality
            parentSpace.Notify(JSON.stringify(availableFresh));
        }
        else {
            log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

            // Set up custom notification for air quality
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

1. Nyisson meg egy parancsablakot, és lépjen abba a mappába **foglaltsága-quickstart\src**. A következő parancsot a térbeli intelligencia graph és a felhasználó által definiált függvény üzembe helyezése:

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > Jogosulatlan hozzáférés elkerülése érdekében a digitális Twins felügyeleti API-t, hogy a **foglaltsága-quickstart** alkalmazása szükséges, hogy jelentkezzen be Azure-fiók hitelesítő adataival. A hitelesítő adatok mentése egy rövid ideig, így előfordulhat, hogy nem kell minden alkalommal, amikor, jelentkezzen be. Először a program fut, és a mentett hitelesítő adatait, amely után jár le, amikor az alkalmazás egy bejelentkezési oldalra irányítja, és lehetővé teszi, hogy az oldalon adja meg egy munkamenet-specifikus kódot. Az utasításokat követve jelentkezzen be Azure-fiókjával.

1. A fiók hitelesítését követően az alkalmazás megkezdi egy minta térbeli graph provisionSample.yaml konfigurált. Várjon, amíg a kiépítési végeztével. Néhány percet vesz igénybe. Ezt követően vizsgálja meg az üzeneteket a parancssori ablakba, és figyelje meg, hogyan a térbeli graph jön létre. Figyelje meg, hogyan az alkalmazás létrehoz egy IoT hubot a gyökércsomópont vagy a `Venue`.

1. A kimenet a parancssori ablakba, másolja a értékét `ConnectionString`alatt a `Devices` szakaszban a vágólapra. Ez az érték a következő szakaszban az eszköz kapcsolata szimulálásához lesz szüksége.

    ![Minta üzembe helyezése](./media/tutorial-facilities-udf/run-provision-sample.png)

> [!TIP]
> Ha hibaüzenetet hasonló "az i/o-művelet megszakadt egy hozzászóláslánc kilépési vagy adott alkalmazáskérelem miatt" a kiépítés közepén, próbálja meg újra futtatni a parancsot. Ez akkor fordulhat elő, ha a HTTP-alapú hálózati probléma, az időkorlátot.

<a id="simulate" />

## <a name="simulate-sensor-data"></a>Az érzékelőadatok szimulálása

Ebben a szakaszban nevű projekt használatával *eszközkapcsolattal* a mintában. Érzékelőktől kapott adatok felismeri a mozgásban lévő adatoknak egyaránt hőmérséklet és szén-dioxid fog szimulálásához. Ez a projekt véletlenszerű értéket állít elő az érzékelők számára, és az eszköz kapcsolati sztringjével elküldi őket az IoT Hubnak.

1. Egy különálló parancssori ablakban nyissa meg az Azure digitális Twins minta majd a **eszközkapcsolattal** mappát.

1. Futtassa ezt a parancsot annak ellenőrzéséhez, hogy helyesek-e a projekt függőségei:

    ```cmd/sh
    dotnet restore
    ```

1. Nyissa meg a **appSettings.json** fájlt a szerkesztőben, majd szerkessze a következő értékeket:

   a. **DeviceConnectionString**: Az értéket `ConnectionString` a kimeneti ablakban az előző szakaszban. Másolja ezt a karakterláncot teljesen, idézőjelek, így a szimulátor megfelelően csatlakozhatnak az IoT hubbal.

   b. **HardwareId** belül a **érzékelők** tömb: Események az érzékelők kiépítése az Azure digitális Twins-példányhoz van szimuláló, mert a Hardverazonosítót és az érzékelők, a fájl nevét meg kell egyeznie a `sensors` provisionSample.yaml fájl csomópont.

      Adjon hozzá egy új bejegyzést a hőmérséklet-érzékelő. A **érzékelők** csomópont az appSettings.JSON fájl a következőhöz hasonlóan kell kinéznie:

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
   > A szimuláció minta nem kommunikálnak közvetlenül a digitális Twins példányt, mert nem igényel hitelesítést.

## <a name="get-results-of-the-user-defined-function"></a>A felhasználó által definiált függvény eredményeinek beolvasása
A felhasználó által meghatározott függvény mindig fut, amikor a példány eszköz- és érzékelőadatokat fogad. Ez a szakasz lekérdezi a felhasználó által definiált függvény eredményeinek lekérése az Azure digitális Twins-példány. Látni fogja közel valós időben, amikor egy hely áll rendelkezésre, hogy a levegőben friss és hőmérséklet a legmegfelelőbb. 

1. Nyissa meg a parancsablakot, amellyel hozhat létre a mintát, vagy egy új parancsablakot, és nyissa meg a **foglaltsága-quickstart\src** mappát a minta újra.

1. Futtassa a következő parancsot, és jelentkezzen be, amikor a rendszer kéri:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

A kimeneti ablakban jeleníti meg, hogyan a felhasználó által definiált függvény fut, és elfogja az eszközszimuláció eseményeit. 

   ![Az UDF kimenete](./media/tutorial-facilities-udf/udf-running.png)

Ha a figyelt feltétel teljesül, a felhasználó által definiált függvény értékét állítja be a megfelelő üzenetet a terület, ahogyan [korábbi](#udf). A `GetAvailableAndFreshSpaces` függvény kiírja ezt az üzenetet a konzolon.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, ezen a ponton felfedezése az Azure digitális Twins leállítására, nyugodtan ebben az oktatóanyagban létrehozott erőforrások törléséhez:

1. A bal oldali menüben lévő a [az Azure portal](http://portal.azure.com), jelölje be **összes erőforrás**, a digitális Twins erőforráscsoportot, válassza ki és **törlése**.

    > [!TIP]
    > Ha törli a digitális Twins-példány problémajegyek tapasztal, szolgáltatás frissítése lett állítva a javítás. Ismételje meg a példány törlése.

2. Ha szükséges, törölje a munkahelyi számítógépen mintaalkalmazásból.

## <a name="next-steps"></a>További lépések

Most, hogy a tárolóhelyek kiépítése, és létrehozott egy egyéni értesítések aktiválásához keretrendszert, nyissa meg az alábbi oktatóanyagok egyikét:

> [!div class="nextstepaction"]
> [Oktatóanyag: Értesítések fogadása a Logic Apps használata az Azure digitális Twins tárolóhelyek](tutorial-facilities-events.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Segítségével vizualizálhatja és elemezheti a Time Series Insights használatával az Azure digitális Twins tárolóhelyek eseményei](tutorial-facilities-analyze.md)
