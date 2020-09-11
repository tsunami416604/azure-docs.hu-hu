---
title: 'Oktatóanyag: a IoT térbeli elemzés megvalósítása Microsoft Azure térképekkel'
description: IoT Hub integrálása a Microsoft Azure Maps Service API-kkal.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 648feedfb82ad43af8f350bd25c9deb5d6ceec03
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007281"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Oktatóanyag: a IoT térbeli elemzés megvalósítása Azure Maps használatával

Egy IoT-forgatókönyv esetében gyakori, hogy rögzítse és nyomon követhesse a térben és időben előforduló releváns eseményeket. Ilyenek például a flotta kezelése, az eszközök nyomon követése, a mobilitás és az intelligens városi alkalmazások. Ez az oktatóanyag végigvezeti egy olyan megoldáson, amely a Azure Maps API-k használatával követi nyomon a használatban lévő autóbérlési mozgást.

Ebben az oktatóanyagban a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Hozzon létre egy Azure Storage-fiókot az autó követési adatnaplójának megjelenítéséhez.
> * Töltsön fel egy geokerítésen a Azure Maps adatszolgáltatásba az adatfeltöltő API használatával.
> * Hozzon létre egy IoT Hub, és regisztrálja az eszközt.
> * Hozzon létre egy függvényt Azure Functionsban, és alkalmazza az üzleti logikát Azure Maps térbeli elemzés alapján.
> * Fizessen elő a IoT Device telemetria-események az Azure-függvényből Event Grid használatával.
> * A telemetria események szűrése IoT Hub üzenet-útválasztás használatával.

## <a name="prerequisites"></a>Előfeltételek

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. [Hozzon létre egy Azure Maps fiókot](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](how-to-manage-authentication.md).

4. [Hozzon létre egy erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups). Ebben az oktatóanyagban az erőforráscsoport- *ContosoRental*nevezjük el, de tetszőleges nevet választhat.

5. Töltse le a [RentalCarSimulation C# projektet](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

Ez az oktatóanyag a [Poster](https://www.postman.com/) alkalmazást használja, de más API-fejlesztési környezetet is választhat.

## <a name="use-case-rental-car-tracking"></a>Használati eset: autóbérlés követése

Ez az oktatóanyag a következő forgatókönyvet mutatja be: egy autókölcsönző cég szeretné naplózni a tartózkodási hely adatait, a megtett távolságot és a futó állapotot a bérelt autók számára. Emellett a vállalat szeretné tárolni ezeket az információkat, amikor egy autó elhagyja a megfelelő, jóváhagyott földrajzi régiót.

A használati esetünkben a bérelt autók olyan IoT-eszközökkel vannak ellátva, amelyek rendszeresen küldenek telemetria-t az Azure IoT Hubba. A telemetria tartalmazza az aktuális helyet, és jelzi, hogy az autó motorja fut-e. Az eszköz helyének sémája megfelel a [térinformatikai IoT Plug and Play sémájának](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). A bérleti autó eszközének telemetria sémája a következő JSON-kódra hasonlít:

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": {
            "location": {
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            }
        }
    }
}
```

Ebben az oktatóanyagban csak egy járművet fogunk nyomon követni. Az Azure-szolgáltatások beállítása után le kell töltenie a [RentalCarSimulation C# projektet ](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) a Vehicle Simulator futtatásához. A teljes folyamatot, az eseményektől a végrehajtásig, a következő lépésekben összegzi:

1. A járművön belüli eszköz telemetria adatokat küld az IoT hub-nak.

2. Ha az autó motorja fut, az IoT hub közzéteszi a telemetria-információkat a Event Grid.

3. Az Azure-függvényt a rendszer az telemetria eseményeinek esemény-előfizetése miatt indítja el.

4. A függvény naplózza a jármű-eszköz helyének koordinátáit, az esemény időpontját és az eszköz AZONOSÍTÓját. Ezután a [térbeli Geokerítésen Get API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) -val meghatározhatja, hogy az autó a geokerítésen kívülre vezetett-e. Ha a geokerítésen határain kívül utazott, a függvény az eseménytől kapott helyadatok adatait a blob-tárolóba tárolja. Emellett a függvény lekérdezi a [fordított címek keresését](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) , hogy lefordítsa a koordináta helyét egy utcanév-címnek, és tárolja az eszköz többi adatával.

A következő diagram áttekintést nyújt a rendszerről.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="A rendszerek áttekintése":::

Az alábbi ábra kiemeli a geokerítésen területét. A bérelt autó útvonalát zöld vonallal jelölik.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Geokerítésen útvonal":::

## <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása

Az autó megsértésének nyomon követésére szolgáló adat tárolásához hozzunk létre egy [általános célú v2-es Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-account-overview#general-purpose-v2-accounts) az erőforráscsoporthoz. Ha még nem hozott létre erőforráscsoportot, kövesse az [erőforráscsoport létrehozása](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups)című témakör utasításait. Ebben az oktatóanyagban az erőforráscsoport *ContosoRental*nevet fogjuk megtekinteni.

A Storage-fiók létrehozásához kövesse a Storage- [fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)című témakör utasításait. Ebben az oktatóanyagban megnevezjük a Storage-fiók *contosorentalstorage*, de bármilyen nevet megadhat.

Miután sikeresen létrejött a Storage-fiókja, létre kell hoznia egy tárolót a naplózási adattároláshoz.

1. Navigáljon az újonnan létrehozott Storage-fiókhoz. Kattintson a **tárolók** hivatkozásra az alapvető erőforrások szakaszban.

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Tárolók blob Storage-hoz":::

2. Kattintson a bal felső sarokban található **+ Container (tároló** ) gombra. Egy panel jelenik meg a böngésző jobb oldalán. Nevezze el a tároló *contoso-Rental-logs* nevet, és kattintson a **Létrehozás**gombra.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Blobtároló létrehozása":::

3. Navigáljon a Storage-fiók **hozzáférési kulcsok** paneljére, és másolja a **Storage-fiók nevét** és a **kulcs** értékét a **key1** szakaszban. Mindkét értékre szükség lesz az [Azure-függvény létrehozása és Event Grid-előfizetés hozzáadása](#create-an-azure-function-and-add-an-event-grid-subscription) szakaszban.

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="A Storage-fiók nevének és kulcsának másolása":::

## <a name="upload-a-geofence"></a>Geokerítésen feltöltése

Most a [Poster alkalmazást](https://www.getpostman.com) fogjuk használni a geokerítésen a Azure Maps szolgáltatásba való [feltöltéséhez](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) . A geokerítésen határozza meg a bérelt jármű számára az illetékes földrajzi területét.  Az Azure-függvény geokerítésen használatával megállapítható, hogy az autó a geokerítésen területén kívülre került-e.

Nyissa meg a Poster alkalmazást, és kövesse az alábbi lépéseket a geokerítésen a Azure Maps adatfeltöltő API használatával történő feltöltéséhez.  

1. Nyissa meg a Poster alkalmazást. A Poster alkalmazás teteje közelében válassza az **új**lehetőséget. Az **új létrehozása** ablakban válassza a **gyűjtemény**elemet.  Nevezze el a gyűjteményt, és válassza a **Létrehozás** gombot.

2. A kérelem létrehozásához válassza az **új** újra lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem**lehetőséget. Adja meg a kérelem **nevét** . Válassza ki az előző lépésben létrehozott gyűjteményt, majd kattintson a **Mentés**gombra.

3. Válassza a http **post** metódust a Builder (szerkesztő) lapon, és adja meg a következő URL-címet a geokerítésen az adatfeltöltő API-ba való feltöltéséhez. Győződjön meg arról, hogy az `{subscription-key}` elsődleges előfizetési kulccsal van lecserélve.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    Az URL-cím paraméteréhez tartozó "geojson" érték a `dataFormat` feltöltött adatformátumot jelöli.

4. Kattintson a **törzs** elemre, majd válassza a **nyers** bemeneti formátum lehetőséget, majd a legördülő listából válassza a **JSON** lehetőséget. Nyissa meg a JSON [-adatfájlt](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4), és másolja a JSON-t a törzs szakaszba. Kattintson a **Küldés** gombra.

5. Kattintson a kék **Küldés** gombra, és várjon, amíg a rendszer feldolgozza a kérést. A kérés befejeződése után lépjen a válasz **fejlécek** lapjára. Másolja a **hely** kulcsának értékét, amely a `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Az API-hívás állapotának megtekintéséhez hozzon létre egy **Get** http-kérelmet a on `status URL` . A hitelesítéshez hozzá kell fűzni az elsődleges előfizetési kulcsot az URL-címhez. A **Get** kérelemnek a következő URL-címnek kell megjelennie:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}

7. When the **GET** HTTP request completes successfully, it will return a `resourceLocation`. The `resourceLocation` contains the unique `udid` for the uploaded content. You'll need to copy this `udid` for later use in this tutorial.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-azure-iot-hub"></a>Azure IoT Hub létrehozása

Az Azure IoT hub biztonságos és megbízható kétirányú kommunikációt tesz lehetővé egy IoT-alkalmazás és az általa kezelt eszközök között.  A mi esetünkben a járműről származó eszközről szeretnénk adatokat lekérni a bérelt autó helyének meghatározásához. Ebben a szakaszban egy IoT hubot hozunk létre a *ContosoRental* erőforráscsoporthoz. Az IoT hub feladata az eszköz telemetria eseményeinek közzététele.

> [!NOTE]
> A IoT hub azon funkciója, amely az eszköz telemetria események közzétételét Event Grid nyilvános előzetes verzióban érhető el. A nyilvános előzetes verzió funkciói az USA keleti régiója, az USA nyugati régiója, **Nyugat-Európa, Azure Government, az Azure China 21Vianet** és az **Azure Germany**kivételével minden régióban elérhetők.

Az IOT hub *ContosoRental* -erőforráscsoporthoz való létrehozásához kövesse az [IOT hub létrehozása](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub)című témakör lépéseit.

## <a name="register-a-device-in-iot-hub"></a>Eszköz regisztrálása az IoT hub-ban

Az eszközök nem tudnak csatlakozni az IoT hubhoz, kivéve, ha az IoT hub Identity registryben vannak regisztrálva. A forgatókönyvben egyetlen, *InVehicleDevice*nevű eszközt hozunk létre. Az eszköz az IoT hub-ban való létrehozásához és regisztrálásához kövesse az [új eszköz regisztrálása az IoT hub-ban](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#register-a-new-device-in-the-iot-hub)című témakör lépéseit. Ügyeljen arra, hogy az eszköz **elsődleges kapcsolódási karakterláncát** másolja, ahogy azt egy későbbi lépésben fogjuk használni.

## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Azure-függvény létrehozása és Event Grid-előfizetés hozzáadása

Azure Functions egy kiszolgáló nélküli számítási szolgáltatás, amely lehetővé teszi a kis kódrészletek (functions) futtatását anélkül, hogy explicit módon kellene kiépíteni vagy kezelni a számítási infrastruktúrát. További információ a Azure Functionsről: az [Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) dokumentációja.

Egy függvény egy adott esemény által "aktiválva". A forgatókönyvben egy Event Grid trigger által aktivált függvényt hozunk létre. Létrehozjuk a kapcsolatot az eseményindító és a függvény között egy IoT hub Device telemetria eseményekhez tartozó esemény-előfizetés létrehozásával. Ha egy eszköz telemetria esemény következik be, a rendszer végpontként hívja meg a függvényt, és megkapja az [IoT hub-ban korábban regisztrált eszköz](#register-a-device-in-iot-hub)releváns adatait.

[Itt](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx)láthatja, hogy milyen C#-szkriptet fog tartalmazni a függvény.

Most beállítjuk az Azure-függvényt.

1. Az Azure Portal irányítópulton kattintson az **erőforrás létrehozása**elemre. A keresés szövegmezőbe írja be a **függvényalkalmazás** kifejezést. Kattintson **függvényalkalmazás**. Kattintson a **Létrehozás** gombra.

2. A **függvényalkalmazás** létrehozás lapon nevezze el a Function alkalmazást. Az **erőforráscsoport**területen válassza a *ContosoRental* lehetőséget a legördülő listából.  Válassza ki a *.net Core* -t **futtatókörnyezeti veremként**. A lap alján kattintson a **Tovább gombra: >üzemeltetése ** lehetőségre.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Függvényalkalmazás létrehozása":::

3. A **Storage-fiók**területen válassza ki az [Azure Storage-fiók létrehozása](#create-an-azure-storage-account)lapon létrehozott Storage-fiókot. Kattintson a **Felülvizsgálat + létrehozás** elemre.

4. Tekintse át a Function alkalmazás részleteit, és kattintson a **Létrehozás**gombra.

5. Az alkalmazás létrehozása után hozzáadunk egy függvényt. Nyissa meg a Function alkalmazást. Kattintson a **functions (függvények** ) panelre. Kattintson a lap tetején a **+ Hozzáadás** gombra. Megjelenik a függvény sablon panelje. Görgessen lefelé a panelen. Kattintson **Azure Event Grid triggerre**.

     >[!WARNING]
    > Az **Azure Event hub eseményindítója** és a **Azure Event Grid eseményindító** -sablonjai hasonló névvel rendelkeznek. Győződjön meg arról, hogy a **Azure Event Grid trigger** sablonra kattint.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Függvény létrehozása":::

6. Adja meg a függvény nevét. Ebben az oktatóanyagban a *GetGeoFunction*nevet fogjuk használni, de bármilyen nevet használhat. Kattintson a **függvény létrehozása**elemre.

7. Kattintson a **kód + teszt** panelre a bal oldali menüben. Másolja és illessze be a [C#-szkriptet](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) a kód ablakába.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Kód másolása/beillesztése a függvény ablakába":::

8. A C#-kódban cserélje le a következő paramétereket. Kattintson a **Mentés** gombra. Ne kattintson a **tesztelés/Futtatás** lehetőségre
    * Cserélje le a **SUBSCRIPTION_KEYt** a Azure Maps fiók elsődleges előfizetési kulcsára.
    * Cserélje le a **UDID** -t a `udid` [geokerítésen feltöltésével](#upload-a-geofence)feltöltött geokerítésen.
    * A szkriptben a **CreateBlobAsync** függvény egy blobot hoz létre eseményként az adattároló-fiókban. Cserélje le a **ACCESS_KEY**, **ACCOUNT_NAME**és **STORAGE_CONTAINER_NAME** a Storage-fiók hozzáférési kulcsára, a fiók nevére és az adattároló-tárolóra. Ezek az értékek akkor jöttek létre, amikor létrehozta a Storage-fiókot az [Azure Storage-fiók létrehozásakor](#create-an-azure-storage-account).

9. Kattintson az **integráció** panelre a bal oldali menüben. Kattintson **Event Grid triggerre** a diagramon. Írja be az trigger nevét, a *eventGridEvent*, majd kattintson a **Event Grid-előfizetés létrehozása**elemre.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Esemény-előfizetés hozzáadása":::

10. Töltse ki az előfizetés részleteit. Adja meg az esemény-előfizetés nevét. Az *Event Schema*esetében válassza *Event Grid sémát*. A **témakörök típusainál**válassza az *Azure IoT hub-fiókok*lehetőséget. Az **erőforráscsoport**területen válassza ki az oktatóanyag elején létrehozott erőforráscsoportot. Az **erőforrás**mezőben válassza ki az [Azure IoT hub létrehozása](#create-an-azure-iot-hub)területen létrehozott IoT hubot. A **szűrés eseménytípus**esetében válassza az *eszköz telemetria*lehetőséget. Miután kiválasztotta ezeket a beállításokat, a **témakör típusa** módosítás *IoT hubra*. A **Rendszertéma neve**beállításnál használhatja az erőforrás nevét.  Végül kattintson a **végpont kiválasztása** lehetőségre a **végpont részletei** szakaszban. Fogadja el az összes beállítást, majd kattintson a **kijelölés megerősítése**gombra.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Esemény-előfizetés létrehozása":::

11. Tekintse át a beállításokat. Győződjön meg arról, hogy a végpont a szakasz elején létrehozott függvényt adja meg. Kattintson a **Létrehozás** gombra.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Esemény-előfizetés megerősítésének létrehozása":::

12. Most visszatért az **trigger szerkesztése** panelre. Kattintson a **Mentés** gombra.

## <a name="filter-events-using-iot-hub-message-routing"></a>Események szűrése IoT Hub üzenet-útválasztás használatával

Ha Event Grid-előfizetést ad hozzá az Azure-függvényhez, a rendszer automatikusan létrehoz egy üzenetküldési útvonalat a megadott IoT-központban. Az üzenet-útválasztás lehetővé teszi különböző adattípusok különböző végpontokhoz való továbbítását. Átirányíthatja például az eszköz telemetria-üzeneteit, az eszközök életciklusával kapcsolatos eseményeket és az eszközök kettős változási eseményeit. További információ az IoT hub üzenet-útválasztásáról: [IoT hub üzenet-útválasztás használata](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Üzenet-útválasztás az IoT hub-ban":::

Példánkban csak akkor szeretnénk üzeneteket kapni, ha a bérelt autót áthelyezik. Létrehozunk egy útválasztási lekérdezést az események szűréséhez, ahol a `Engine` tulajdonság értéke **"on"**. Útválasztási lekérdezés létrehozásához kattintson a **RouteToEventGrid** útvonalra, és cserélje le az **útválasztási lekérdezést** **"Engine =" on ""** értékre, majd kattintson a Save ( **Mentés**) gombra. A IoT hub most csak azt a telemetria teszi közzé, ahol a motor be van kapcsolva.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Útválasztási üzenetek szűrése":::

>[!TIP]
>Az eszközről a felhőbe irányuló IoT lekérdezések többféleképpen is megtekinthetők, ha többet szeretne megtudni az üzenetek útválasztási szintaxisáról: [IoT hub üzenet-útválasztás](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax).

## <a name="send-telemetry-data-to-iot-hub"></a>Telemetria-IoT Hub küldése

Ha már működik az Azure-függvény, most telemetria az IoT hubhoz, amely átirányítja a Event Grid. Alkalmazzunk egy C#-alkalmazást, amellyel szimulálható a helyszíni eszközre vonatkozó helyadatok egy bérelt autóban. Az alkalmazás futtatásához szüksége lesz a fejlesztői gépen a .NET Core SDK 2.1.0 vagy annál nagyobbra. Kövesse az alábbi lépéseket a szimulált telemetria-adatIoT Hubek küldéséhez.

1. Ha még nem tette meg, töltse le a [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C# projektet.

2. Nyissa meg a simulatedCar.cs-fájlt egy tetszőleges szövegszerkesztőben, és cserélje le az értékét az `connectionString` eszköz regisztrálásakor mentettre, és mentse a fájl módosításait.

3. Győződjön meg arról, hogy a .NET Core telepítve van a gépen. A helyi terminál ablakban navigáljon a C# projekt gyökérkönyvtárához, és futtassa a következő parancsot a szükséges csomagok telepítéséhez a szimulált eszköz alkalmazáshoz:

    ```cmd/sh
    dotnet restore
    ```

4. Ugyanebben a terminálban futtassa a következő parancsot az autókölcsönző szimulációs alkalmazás létrehozásához és futtatásához:

    ```cmd/sh
    dotnet run
    ```

  A helyi terminálnak az alábbihoz hasonlóan kell kinéznie.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Terminál kimenete":::

Ha most megnyitja a blob Storage-tárolót, négy blobot láthat a helyekhez, ahol a jármű kívül esik a geokerítésen.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Blobok megtekintése a tárolón belül":::

Az alábbi Térkép négy járművet mutat be a geokerítésen kívül. Minden hely naplózása rendszeres időközönként történik.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Szabálysértési Térkép":::

## <a name="explore-azure-maps-and-iot"></a>A Azure Maps és a IoT megismerése

Az oktatóanyagban használt Azure Maps API-k megismeréséhez lásd:

* [Fordított keresési címek lekérése](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Geokerítésen beolvasása](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

A Azure Maps REST API-k teljes listájáért lásd:

* [Azure Maps REST API-k](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

A IoT Plug and Play további tudnivalókért lásd:

* [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

A IoT Azure minősítéssel rendelkező eszközök listájának lekéréséhez látogasson el a következő webhelyre:

* [Azure Certified-eszközök](https://catalog.azureiotsolutions.com/)

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni arról, hogyan lehet eszközt küldeni a Felhőbeli telemetria, és fordítva, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Telemetria küldése egy eszközről](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
