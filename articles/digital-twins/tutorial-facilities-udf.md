---
title: Tér monitorozása az Azure Digital Twins használatával | Microsoft Docs
description: Megtudhatja, hogy a jelen oktatóanyagban lévő lépésekkel hogyan építheti ki a térbeli erőforrásait, illetve hogyan monitorozhatja a működést az Azure Digital Twins használatával.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/26/2018
ms.author: dkshir
ms.openlocfilehash: cf45cb8de0e40dfe5f5772dcb1a0be2aa7585fd6
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50156680"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins"></a>Oktatóanyag: Az épület kiépítése és a működés monitorozása az Azure Digital Twins használatával

Ez az oktatóanyag bemutatja, hogyan monitorozhatja a terek kívánt hőmérsékletét és komfortszintjét az Azure Digital Twins használatával. Miután [konfigurálta a mintaépületet](tutorial-facilities-setup.md), a jelen oktatóanyagban szereplő lépésekkel kiépítheti az épületet, és egyéni függvényeket futtathat az érzékelőktől kapott adatokon.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A monitorozni kívánt feltételek meghatározása
> * Felhasználó által meghatározott függvény létrehozása
> * Az érzékelőadatok szimulálása
> * Felhasználó által meghatározott függvény eredményeinek lekérése

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy [konfigurálta az Azure Digital Twins beállításait](tutorial-facilities-setup.md). Mielőtt továbblépne, győződjön meg arról, hogy rendelkezik a következőkkel:
- egy [Azure-fiók](https://azure.microsoft.com/free/?WT.mc_id=A261C142F),
- egy futó Digital Twins-példány, 
- a munkavégzéshez használt gépre letöltött és kicsomagolt [Digital Twins C#-minták](https://github.com/Azure-Samples/digital-twins-samples-csharp), 
- a [.NET Core SDK 2.1.403-as vagy újabb](https://www.microsoft.com/net/download) verziója a fejlesztői gépen a minta létrehozására és futtatására. A `dotnet --version` parancs futtatásával ellenőrizze, hogy a megfelelő verzió van-e telepítve. 
- [Visual Studio Code](https://code.visualstudio.com/) a mintakód vizsgálatához. 

## <a name="define-conditions-to-monitor"></a>A monitorozni kívánt feltételek meghatározása
Meghatározhatja az eszközön vagy az érzékelőadatok között monitorozni kívánt feltételek csoportját, az úgynevezett **megfeleltetőket**. Ezután *felhasználó által meghatározott függvényeket* adhat meg, amelyek egyéni logikát futtatnak a tárolóhelyekről és eszközökről érkező adatokon, amikor a megfeleltetők által megadott feltételek teljesülnek. További információ: [Adatfeldolgozás és felhasználó által definiált függvények](concepts-user-defined-functions.md). 

Az **_occupancy-quickstart_** mintaprojektben nyissa meg az **_src\actions\provisionSample.yaml_** nevű fájlt a Visual Studio Code-ban. Figyelje meg a **megfeleltetők** típussal kezdődő szakaszt. Az ebbe a típusba tartozó összes bejegyzés a megadott **nevű** megfeleltetőt hozza létre, amely egy **dataTypeValue** típusú érzékelőt fog monitorozni. Figyelje meg, hogyan kapcsolódik a *Focus Room A1* nevű térhez, amely egy **eszközök** csomóponttal rendelkezik, és néhány **érzékelőt** tartalmaz. Az egyik ilyen érzékelőt nyomon követő megfeleltető kiépítéséhez a megfeleltető **dataTypeValue** értékének meg kell egyeznie az érzékelő **dataType** értékével. 

Adja hozzá a következő megfeleltetőt a meglévő megfeleltetők alatt. Gondoskodjon arról, hogy a kulcsok igazodnak egymáshoz, és a szóközök helyén nincsenek tabulátorok:

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Ez [az első oktatóanyagban](tutorial-facilities-setup.md) hozzáadott *SAMPLE_SENSOR_TEMPERATURE* érzékelőt fogja nyomon követni. Vegye figyelembe, hogy ezek a sorok megjegyzésként szerepelnek a *provisionSample.yaml* fájlban. A sorok elején lévő „#” karakter eltávolításával egyszerűen eltávolíthatja a megjegyzésként való jelölést. 

<a id="udf" />

## <a name="create-a-user-defined-function"></a>Felhasználó által meghatározott függvény létrehozása
A felhasználó által meghatározott függvények (vagy UDF-ek) lehetővé teszik az érzékelőadatok feldolgozásának testreszabását. Ezek olyan egyéni JavaScript-kódok, amelyek a megfeleltetők által leírt konkrét feltételek teljesülésekor futhatnak a Digital Twins-példányban. Mindegyik monitorozni kívánt érzékelőhöz létrehozhat *megfeleltetőket* és *felhasználó által meghatározott függvényeket*. További információ: [Adatfeldolgozás és a felhasználó által meghatározott függvények](concepts-user-defined-functions.md). 

A minta *provisionSample.yaml* fájlban keresse meg a **userdefinedfunctions** típussal kezdődő szakaszt. Ez a szakasz adott **névvel** hoz létre egy felhasználó által meghatározott függvényt, amely a **matcherNames** szakaszban szereplő megfeleltetők listáján fut. Figyelje meg, hogy megadhatja **szkriptként** a saját JavaScript-fájlját a felhasználó által meghatározott függvényhez. A **roleassignments** nevű szakaszt is figyelje meg. Ez a *térrendszergazda* szerepkört rendeli a felhasználó által meghatározott függvényhez, amely lehetővé teszi, hogy minden kiépített területről érkező eseményhez hozzáférjen. 

1. Konfigurálja úgy az UDF-et, hogy tartalmazza a hőmérséklet-megfeleltetőt. Ehhez adja hozzá az alábbi sort, vagy távolítsa el a megjegyzést az alábbi sorból a *provisionSample.yaml* fájl `matcherNames` csomópontjában:

    ```yaml
            - Matcher Temperature
    ```

1. Nyissa meg az **_src\actions\userDefinedFunctions\availability.js_** fájlt a szerkesztőben. Erre a fájlra hivatkozik a *provisionSample.yaml* fájl **szkript** eleme. A fájlban lévő, felhasználó által meghatározott függvény olyan feltételeket keres, amikor nem észlelhető mozgás a helyiségben, a szén-dioxid szintje pedig 1000 ppm alatt van. Módosítsa a JavaScript-fájlt, hogy más feltételek mellett a hőmérsékletet is monitorozza. Adja hozzá az alábbi kódsorokat, hogy feltételeket keressen, amikor nem észlelhető mozgás a helyiségben, a szén-dioxid-szintek 1000 ppm alatt vannak, és a hőmérséklet 78 Fahrenheit-fok alatt van.

   > [!NOTE]
   > Ez a szakasz módosítja az *src\actions\userDefinedFunctions\availability.js* fájlt, hogy Ön részletesen megismerhesse a felhasználó által meghatározott függvények írásának egy módját. De a beállításban közvetlenül is használhatja az [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) fájlt. Ebben a fájlban szerepel az oktatóanyaghoz szükséges összes módosítás. Ha inkább ezt a fájlt használja, győződjön meg arról, hogy a megfelelő fájlnevet használja az [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) fájlban lévő **_szkript_** kulcshoz.

    1. A fájl elején adja hozzá a következő sorokat a hőmérséklethez az `// Add your sensor type here` megjegyzés alá:

        ```JavaScript
            var temperatureType = "Temperature";
            var temperatureThreshold = 78;
        ```
   
    1. Adja hozzá a következő sorokat a `var motionSensor` változót meghatározó utasítás után, az `// Add your sensor variable here` megjegyzés alá:

        ```JavaScript
            var temperatureSensor = otherSensors.find(function(element) {
                return element.DataType === temperatureType;
            });
        ```
    
    1. Adja hozzá a következő sort a `var carbonDioxideValue` változót meghatározó utasítás után, az `// Add your sensor latest value here` megjegyzés alá:

        ```JavaScript
            var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
        ```
    
    1. Távolítsa el a következő kódsorokat `// Modify this line to monitor your sensor value` megjegyzés alól: 

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
    
    1. Távolítsa el a következő kódsorokat `// Modify these lines as per your sensor` megjegyzés alól:

        ```JavaScript
            var availableFresh = "Room is available and air is fresh";
            var noAvailableOrFresh = "Room is not available or air quality is poor";
        ```

       Cserélje le ezeket a következő sorokra:

        ```JavaScript
            var alert = "Room with fresh air and comfortable temperature is available.";
            var noAlert = "Either room is occupied, or working conditions are not right.";
        ```
    
    1. Távolítsa el a következő *if-else* kódblokkot a `// Modify this code block for your sensor` megjegyzés után:

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
    
    1. Mentse a fájlt. 
    
1. Nyisson meg egy parancsablakot, és lépjen az **_occupancy-quickstart\src_** mappára. Futtassa az alábbi parancsot a térbeliintelligencia-diagram és a felhasználó által meghatározott függvény létrehozásához. 

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > A Digital Twins felügyeleti API jogosulatlan hozzáférésének megakadályozásához az Azure-fiók hitelesítő adataival kell bejelentkeznie az **_occupancy-quickstart_** alkalmazásba. Egy rövid ideig megőrzi a hitelesítő adatokat, így lehet, hogy nem kell minden futtatáskor bejelentkeznie. A program első futtatásakor, valamint azután a mentett hitelesítő adatok lejártakor átirányítja egy bejelentkezési oldalra, és megad egy munkamenet-specifikus kódot, amelyet az adott oldalon kell megadni. Az utasításokat követve jelentkezzen be Azure-fiókjával.


1. A fiók hitelesítése után az alkalmazás elkezd létrehozni egy minta térbeli diagramot a *provisionSample.yaml* fájlban konfigurált módon. Várja meg az üzembe helyezés befejezését; ez néhány percet vesz igénybe. Az üzembe helyezés befejeztével tekintse meg a parancssori ablakban lévő üzeneteket, és figyelje meg a térbeli diagram létrehozását. Figyelje meg, hogyan hoz létre egy IoT hubot a gyökércsomóponton vagy a `Venue` csomóponton. 

1. A parancsablak kimenetéből másolja a `Devices` szakaszban lévő `ConnectionString` értékét a vágólapra. Erre az értékre a következő szakaszban az eszközkapcsolat szimulálásakor lesz szükség.

    ![Üzembehelyezési minta](./media/tutorial-facilities-udf/run-provision-sample.png)

> [!TIP]
> Ha az üzembe helyezés közben arról szóló üzenetet kap, hogy az I/O-művelet megszakadt egy szál kilépése vagy alkalmazáskérelem miatt, próbálja meg újra futtatni a parancsot. Ez akkor fordulhat elő, ha a HTTP-ügyfél hálózati hiba miatt átlépte az időkorlátot.

<a id="simulate" />

## <a name="simulate-sensor-data"></a>Az érzékelőadatok szimulálása
Ebben a szakaszban a mintában lévő *device-connectivity* nevű projekttel szimulálja a mozgás, a hőmérséklet és a szén-dioxid érzékelői adatait. Ez a projekt véletlenszerű értéket állít elő az érzékelők számára, és az eszköz kapcsolati sztringjével elküldi őket az IoT Hubnak.

1. Egy másik parancsablakban lépjen a Digital Twins-mintára, majd a **_device-connectivity_** mappára.

1. Futtassa ezt a parancsot annak ellenőrzéséhez, hogy helyesek-e a projekt függőségei:

    ```cmd/sh
    dotnet restore
    ```

1. Nyissa meg az *appSettings.json* fájlt a szerkesztőben, és szerkessze az alábbi értékeket:
    1. *DeviceConnectionString*: Rendelje hozzá a `ConnectionString` értékét az előző szakasz kimeneti ablakából. Ügyeljen arra, hogy az idézőjelekben lévő teljes sztringet átmásolja, hogy a szimulátor megfelelően működjön az IoT Hubbal.

    1. A *Sensors* tömbben lévő *HardwareId*: Mivel a Digital Twins-példányban üzembe helyezett érzékelőkről szimulál eseményeket, ebben a fájlban a hardverazonosítónak és az érzékelők nevének egyezniük kell a *provisionSample.yaml* fájl `sensors` csomópontjával. Adjon hozzá egy új bejegyzést a hőmérséklet-érzékelőhöz; az *appSettings.json* fájlban lévő **Sensors** csomópontnak a következőképpen kell kinéznie:

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
   > Mivel a szimulációs minta nem kommunikál közvetlenül a Digital Twins-példánnyal, nem igényel hitelesítést.

## <a name="get-results-of-user-defined-function"></a>Felhasználó által meghatározott függvény eredményeinek lekérése
A felhasználó által meghatározott függvény mindig fut, amikor a példány eszköz- és érzékelőadatokat fogad. Ez a szakasz lekérdezi a Digital Twins-példányt a felhasználó által meghatározott függvény eredményeinek beszerzése érdekében. Közel valós időben látni fogja, amikor egy helyiség elérhető, a levegő friss, és a hőmérséklet megfelelő. 

1. Nyissa meg a minta üzembe helyezéséhez használt parancsablakot vagy egy új parancsablakot, és keresse meg ismét a minta **_occupancy-quickstart\src_** mappáját. 

1. Futtassa a következő parancsot, és jelentkezzen be, amikor a rendszer kéri:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

A kimeneti ablakban látható, hogy a felhasználó által meghatározott függvény hogyan fut, illetve hogyan fogja el az eszközszimuláció eseményeit. 

   ![UDF végrehajtása](./media/tutorial-facilities-udf/udf-running.png)

A felhasználó által meghatározott függvény a monitorozott feltétel teljesülésétől függően állítja be a tér értékét a megfelelő üzenettel, ahogyan [a fenti szakaszban](#udf) láttuk, majd a `GetAvailableAndFreshSpaces` függvény megjeleníti az üzenetet a konzolon. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha most be szeretné fejezni az Azure Digital Twins megismerését, nyugodtan törölje az ebben az oktatóanyagban létrehozott erőforrásokat:

1. Az [Azure Portal](http://portal.azure.com) bal oldali menüjében kattintson a **Minden erőforrás** lehetőségre, válassza ki a Digital Twins-erőforráscsoportot, és **törölje**.
2. Szükség esetén a mintaalkalmazásokat is törölheti a munkavégzéshez használt gépről. 


## <a name="next-steps"></a>További lépések

Most, hogy létrehozta a tereket, valamint egy keretrendszert az egyéni értesítések aktiválásához, a következő oktatóanyagok bármelyikével folytathatja. 

> [!div class="nextstepaction"]
> [Oktatóanyag: Értesítések fogadása az Azure Digital Twins-terektől a Logic Apps használatával](tutorial-facilities-events.md)

Vagy

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Digital Twins-terek eseményeinek vizualizációja és elemzése a Time Series Insights használatával](tutorial-facilities-analyze.md)
