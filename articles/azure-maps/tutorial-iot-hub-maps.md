---
title: A IoT térbeli elemzés megvalósítása a Azure Maps használatával | Microsoft Docs
description: IoT Hub integrálása Azure Maps Service API-kkal.
author: walsehgal
ms.author: v-musehg
ms.date: 08/13/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 7c3f72c3ce6acfb63e682b479519dba02a9900eb
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844909"
---
# <a name="implement-iot-spatial-analytics-using-azure-maps"></a>A IoT térbeli elemzés megvalósítása Azure Maps használatával

A térben és időben előforduló releváns események nyomon követése és rögzítése gyakori IoT forgatókönyv. Például a flotta kezelése, az eszközök nyomon követése, a mobilitás és az intelligens városi alkalmazások esetében. Ez az oktatóanyag végigvezeti a Azure Maps API-k a IoT Hub által rögzített, a Event Grid által megadott esemény-előfizetési modell használatával történő használatának megoldásán.

Ebben az oktatóanyagban a következőket fogja elsajátítani:

> [!div class="checklist"]
> * IoT Hub létrehozása
> * Az adatfeltöltő API használatával töltse fel a geokerítésen területét a Azure Mapsba.
> * Hozzon létre egy függvényt Azure Functionsban, és alkalmazza az üzleti logikát Azure Maps térbeli elemzés alapján.
> * Fizessen elő a IoT Device telemetria-események az Azure-függvényből Event Grid használatával.
> * A telemetria események szűrése IoT Hub üzenet-útválasztás használatával.
> * Hozzon létre egy Storage-fiókot a kapcsolódó esemény-adatértékek tárolásához.
> * A járművön belüli IoT-eszköz szimulálása.
    

## <a name="use-case"></a>Használati eset

Megmutassa a megoldást arra a helyzetre, amikor egy autókölcsönző cég megtervezi, hogy figyeli és naplózza az eseményeket a kibérelt autók számára. A bérelt vállalatok gyakran egy adott földrajzi régióhoz tartozó autókat bérelnek, és a bérletük után nyomon kell követniük a tartózkodási helyüket. Minden olyan példányt, amely magában foglalja a kijelölt földrajzi régiót elhagyó autót, naplózni kell, hogy a szabályzatok, díjak és egyéb üzleti szempontok megfelelően kezelhetők legyenek.

A használati esetünkben a bérleti autók olyan IoT-eszközökkel vannak ellátva, amelyek rendszeresen küldenek telemetria-és Azure-IoT Hub. A telemetria tartalmazza az aktuális helyet, és jelzi, hogy az autó motorja fut-e. Az eszköz helyének sémája megfelel a [térinformatikai IoT Plug and Play sémájának](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md). A bérleti autó eszközének telemetria sémája a következőképpen néz ki:

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

A cél eléréséhez a járművön belüli eszköz telemetria használható. Célunk, hogy geokerítések szabályokat hajtson végre, és végezze el a megfelelő lépéseket minden alkalommal, amikor egy olyan esemény érkezik, amely az autó áthelyezésének helyét jelzi. Ehhez az IoT Hub-on keresztül Event Grid telemetria-eseményekre kell fizetnünk, így a kívánt üzleti logikát csak akkor lehet végrehajtani, ha szükséges. A Event Gridra való előfizetések többféleképpen is felhasználhatók, ebben az oktatóanyagban a Azure Functions használatát fogjuk használni. A Azure Functions a Event Gridban közzétett eseményekre fog támaszkodni, és Azure Maps térbeli elemzések alapján valósítja meg az autókölcsönzés üzleti logikáját. Az Azure-függvény azt ellenőrzi, hogy a jármű elhagyta-e a geokerítésen, és ha igen, további információkat, például az aktuális helyhez társított címeket gyűjtsön össze. A függvény emellett olyan logikát is megvalósít, amely az adatblob-tárolóban található, az esemény körülményeinek pontos leírását, valamint a bérleti ügyfelet is lehetővé teszi.

A következő diagram áttekintést nyújt a rendszerről.

 
  <center>

  ![A rendszerek áttekintése](./media/tutorial-iot-hub-maps/system-diagram.png)</center>

Az alábbi ábra a kék és a bérelt jármű útvonalán zöld vonalként jelölt geokerítésen jelöli.

  ![Geokerítésen útvonal](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Előfeltételek 

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az oktatóanyag lépéseinek elvégzéséhez először létre kell hoznia egy erőforráscsoportot a Azure Portal. Erőforráscsoport létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza az **erőforráscsoportok**lehetőséget.
    
   ![Erőforráscsoportok](./media/tutorial-iot-hub-maps/resource-group.png)

3. Az erőforráscsoportok területen válassza a **Hozzáadás**lehetőséget.
    
   ![Erőforráscsoport hozzáadása](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Adja meg a következő tulajdonságértékek értékét:
    * **Előfizetés** Válassza ki az Azure-előfizetést.
    * **Erőforráscsoport:** Adja meg az "ContosoRental" nevet az erőforráscsoport neveként.
    * **Régió** Válassza ki az erőforráscsoport régióját.  

    ![Erőforráscsoport részletei](./media/tutorial-iot-hub-maps/resource-details.png)

    Kattintson a **felülvizsgálat + létrehozás** gombra, és válassza a **Létrehozás** lehetőséget a következő oldalon.

### <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása 

Az üzleti logika Azure Maps térbeli elemzésen alapuló megvalósításához létre kell hozni egy Azure Maps fiókot az általunk létrehozott erőforráscsoporthoz. A [fiók kezelése](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys) lehetőségre kattintva hozzon létre egy Azure Maps-fiók előfizetését S1 árképzési szinten, és tekintse meg a [hitelesítés részleteit](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication#view-authentication-details) , hogy megtudja, hogyan szerezhet be előfizetési kulcsot.


### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Az események naplózása érdekében hozzon létre egy általános célú **v2storage** -fiókot a "ContosoRental" erőforráscsoporthoz az adat blobként való tárolásához. A Storage-fiók létrehozásához kövesse a [Storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)című témakör utasításait. Ezután létre kell hoznia egy tárolót a Blobok tárolásához. Ehhez kövesse az alábbi lépéseket:

1. A Storage-fiókban navigáljon a Blobok elemre.

    ![Blobok](./media/tutorial-iot-hub-maps/blobs.png)

2. Kattintson a bal felső sarokban található Container (tároló) gombra, és nevezze el a "contoso-Rental-logs" tárolót, és kattintson az "OK" gombra.

    ![BLOB tároló](./media/tutorial-iot-hub-maps/blob-container.png)

3. Navigáljon a Storage-fiók **hozzáférési kulcsok** paneljére, és másolja a fiók nevét és elérési kulcsát, majd később fogjuk használni őket.

    ![hozzáférési kulcsok](./media/tutorial-iot-hub-maps/access-keys.png)


Most, hogy rendelkezünk egy Storage-fiókkal és egy tárolóval az események naplózásához, a következő lépés egy IoT hub-t hozunk létre.

### <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Az IoT Hub egy olyan felügyelt szolgáltatás a felhőben, amely központi üzenetsorként működik a IoT-alkalmazások és az általa felügyelt eszközök közötti kétirányú kommunikációhoz. Ahhoz, hogy az eszköz telemetria az üzeneteket egy Event Gridba, létre kell hoznia egy IoT Hub a "ContosoRental" erőforráscsoporthoz, és be kell állítania egy üzenet-útvonal-integrációt, ahol a rendszer az autó motorjának állapota alapján szűri az üzeneteket, és elküldi az eszköz telemetria üzenetek a Event Grid, amikor az autó mozog. 

> [!Note] 
> A IoT Hub funkciói az eszközön telemetria események közzétételét Event Grid a nyilvános előzetes verzióban. A nyilvános előzetes verzió funkciói az USA keleti régiója, az USA nyugati régiója, **Nyugat-Európa, Azure Government, az Azure China 21Vianet** és az **Azure Germany**kivételével minden régióban elérhetők. 

Hozzon létre egy IOT hubot a [IoT hub létrehozása szakasz](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub)lépéseit követve.


### <a name="register-a-device"></a>Eszköz regisztrálása 

Ahhoz, hogy csatlakozhasson a IoT Hubhoz, regisztrálni kell egy eszközt. Az IoT hub-eszközök regisztrálásához kövesse az alábbi lépéseket:

1. A IoT Hub kattintson az "IoT-eszközök" panelre, és kattintson az "új" elemre.

    ![eszköz hozzáadása](./media/tutorial-iot-hub-maps/add-device.png)

2. Az eszköz létrehozása lapon nevezze el a IoT eszközt, és kattintson a Save (Mentés) gombra.
    
    ![eszköz regisztrálása](./media/tutorial-iot-hub-maps/register-device.png)


## <a name="upload-geofence"></a>Geokerítésen feltöltése

A [Poster alkalmazást](https://www.getpostman.com) használva [feltöltjük a geokerítésen](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) a Azure Maps szolgáltatásba a Azure Maps adatfeltöltő API-val. Minden olyan esemény, amely a geokerítésen kívül esik, a rendszer naplózza.

Nyissa meg a Poster alkalmazást, és kövesse az alábbi lépéseket a geokerítésen feltöltéséhez a Azure Maps, az adatfeltöltő API használatával.  

1. A Poster alkalmazásban kattintson az új elemre | Hozzon létre egy újat, és válassza a kérelem lehetőséget. Adja meg a geokerítésen-adatok feltöltésére vonatkozó kérelem nevét, válassza ki azt a gyűjteményt vagy mappát, ahová menteni szeretné, majd kattintson a Mentés gombra.

    ![Geofences feltöltése a Poster használatával](./media/tutorial-iot-hub-maps/postman-new.png)

2. Válassza a HTTP POST metódus lehetőséget a Builder (szerkesztő) lapon, és adja meg a következő URL-címet a POST-kérelem létrehozásához.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Az URL-cím `dataFormat` paraméteréhez tartozó "geojson" érték a feltöltött adatformátumot jelöli.

3. Kattintson a **Paraméterek**elemre, és adja meg a következő kulcs/érték párokat, amelyeket a post kérelem URL-címéhez kíván használni. Cserélje le az előfizetés-kulcs értékét a Azure Maps előfizetési kulcsára.
   
    ![Kulcs-érték paraméterek Poster](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Kattintson a **törzs** elemre, majd válassza a **nyers** bemeneti formátum lehetőséget, majd a legördülő listából válassza a **JSON (alkalmazás/szöveg)** lehetőséget. Nyissa meg a JSON- [adatfájlt](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4), és másolja a JSON-t a Poster-ben lévő törzs szakaszba, és kattintson a **Send (Küldés**) gombra.
    
    ![közzététel](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Tekintse át a válasz fejléceit. Sikeres kérelem esetén a **hely** fejléce tartalmazza az állapot URI-ját a feltöltési kérelem aktuális állapotának vizsgálatához. Az állapot URI-ja a következő formátumú lesz. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Másolja az állapot-URI-t `subscription-key` , és fűzze hozzá a paramétert a Azure Maps fiókja előfizetési kulcsának értékéhez. Az állapot URI-formátumának az alábbihoz hasonlónak kell lennie:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. A létrehozásához `udId` nyisson meg egy új fület a Poster alkalmazásban, majd válassza a http-módszer beolvasása lehetőséget a Builder (szerkesztő) lapon, és tegyen egy Get-kérést az állapot URI-ja Ha az adatok feltöltése sikeres volt, egy udId fog kapni a válasz törzsében. Másolja a udId későbbi használatra.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


A következő lépésben létrehozunk egy Azure-függvényt a "ContosoRental" erőforráscsoporthoz, majd az telemetria üzenetek szűréséhez az üzenet útvonalát a IoT Hubban.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Azure-függvény létrehozása és Event Grid-előfizetés hozzáadása

A Azure Functions egy kiszolgáló nélküli számítási szolgáltatás, amely lehetővé teszi a kód igény szerinti futtatását anélkül, hogy explicit módon kellene kiépíteni vagy kezelni a számítási infrastruktúrát. Ha többet szeretne megtudni a Azure Functionsről, tekintse meg az [Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) dokumentációját. A függvényben megvalósított logika a geokerítésen állapotának értékeléséhez a járművön kívüli eszköz telemetria érkező helyadatok használatával történik. Abban az esetben, ha egy adott jármű a geokerítésen kívül esik, a függvény ezután további információkat gyűjt, például a hely címét a [Search reverse API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) -n keresztül, amely egy adott hely koordinálását egy emberi érthető lakcímre fordítja. A rendszer ezután a blob-tárolóban tárolja az összes kapcsolódó eseményt. Az alábbi 5. lépés az ilyen logikát megvalósító végrehajtható kódra mutat. Az alábbi lépéseket követve hozzon létre egy Azure-függvényt, amely adatnaplókat küld a Storage-fiók blob-tárolójába, és adjon hozzá egy Event Grid-előfizetést.

1. Az Azure Portal irányítópulton válassza az erőforrás létrehozása lehetőséget. Válassza a **számítás** lehetőséget az elérhető erőforrástípusok listájából, majd válassza a **Function app**elemet.

    ![erőforrás létrehozása](./media/tutorial-iot-hub-maps/create-resource.png)

2. A Function app-létrehozás lapon nevezze el a Function alkalmazást az **erőforráscsoport** területen válassza a **meglévő használata**lehetőséget, majd a legördülő listából válassza a "ContosoRental" lehetőséget. Válassza a ".NET Core" lehetőséget futtatókörnyezeti Veremként, a **tárolás** területen válassza a **meglévő használata** lehetőséget, majd a legördülő listából válassza a "contosorentaldata" elemet, majd kattintson a **Létrehozás**gombra.
    
    ![létrehozás – alkalmazás](./media/tutorial-iot-hub-maps/rental-app.png)

3. Az alkalmazás létrehozása után hozzá kell adnia egy függvényt. Nyissa meg a Function alkalmazást, és kattintson az **új függvény** lehetőségre egy függvény hozzáadásához, válassza a **portálon** a fejlesztői környezet lehetőséget, majd válassza a **Folytatás**lehetőséget.

    ![Create-Function](./media/tutorial-iot-hub-maps/function.png)

4. Válassza a **további sablonok** lehetőséget, majd kattintson **a Befejezés és a sablonok megtekintése**elemre. 

5. Válassza ki a sablont **Azure Event Grid triggerrel**. Ha a rendszer kéri, telepítse a bővítményeket, nevezze el a függvényt, és válassza a **Létrehozás**műveletet.

    ![függvény – sablon](./media/tutorial-iot-hub-maps/eventgrid-funct.png)

6. Másolja a [c#-kódot](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) a függvénybe, és kattintson a **Save (Mentés**) gombra.
 
7. A c# parancsfájlban cserélje le a következő paramétereket:
    * Cserélje le a **SUBSCRIPTION_KEY** a Azure Maps fiókjának előfizetési kulcsára.
    * Cserélje le a **UDID** a feltöltött geokerítésen UDID. 
    * A szkriptben a **CreateBlobAsync** függvény egy blobot hoz létre eseményként az adattároló-fiókban. Cserélje le a **ACCESS_KEY**, a **ACCOUNT_NAME** és a **STORAGE_CONTAINER_NAME** a Storage-fiók hozzáférési kulcsára, valamint a fiók nevére és az adattároló-tárolóra.

10. Kattintson a **Event Grid-előfizetés hozzáadása**elemre.
    
    ![Event-Grid hozzáadása](./media/tutorial-iot-hub-maps/add-egs.png)

11. Töltse ki az előfizetés részleteit az **esemény-előfizetés részletei** név alatt az előfizetés és az esemény-séma területen válassza a "Event Grid séma" lehetőséget. A **témakör részletei** területen válassza az "Azure IoT hub fiókok" lehetőséget a témakör típusaként, válassza ki ugyanazt az előfizetést, amelyet az erőforráscsoport létrehozásához használt, válassza a "ContosoRental" elemet az "erőforráscsoport" elemnél, és válassza ki az erőforrásként létrehozott IoT hub. Válassza ki az **eszköz telemetria** . Miután kiválasztotta ezeket a beállításokat, a "témakör típusa" beállítás automatikusan "IoT Hub" értékre vált.

    ![Event-Grid-előfizetés](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Események szűrése IoT Hub üzenet-útválasztás használatával

Miután hozzáadta Event Grid-előfizetést az Azure-függvényhez, mostantól megtekintheti a IoT Hub **üzenet-útválasztási** paneljén Event Grid üzenet alapértelmezett útvonalát. Az üzenet-útválasztás lehetővé teszi különböző adattípusok, például az eszköz telemetria üzenetei, az eszköz életciklusa eseményeinek és az eszközök kettős változási eseményeinek különböző végpontokra való továbbítását. További információ az IoT hub üzenet-útválasztásáról: [IoT hub üzenet-útválasztás használata](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

![központ – például útvonal](./media/tutorial-iot-hub-maps/hub-route.png)

Példánkban szeretnénk kiszűrni az összes olyan üzenetet, ahol a kölcsönzési jármű mozog. Ahhoz, hogy az adott eszközön telemetria az eseményeket a Event Gridba, az útválasztási lekérdezés használatával szűrheti azokat az eseményeket, `Engine` amelyeken a tulajdonság **"on"** . Az eszközről a felhőbe irányuló IoT lekérdezések többféleképpen is megtekinthetők, ha többet szeretne megtudni az üzenetek útválasztási szintaxisáról: [IoT hub üzenet-útválasztás](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax). Útválasztási lekérdezés létrehozásához kattintson a **RouteToEventGrid** útvonalra, és cserélje le az **útválasztási lekérdezést** **"Engine =" on ""** értékre, majd kattintson a Save ( **Mentés**) gombra. A IoT hub most csak azt a telemetria teszi közzé, ahol a motor be van kapcsolva.

![központ – pl. szűrő](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Telemetria-IoT Hub küldése

Ha már működik az Azure-függvény, most elküldjük a telemetria az IoT Hub, amely átirányítja a Event Gridba. C#-alkalmazással szimuláljuk a bérelt autó járműbeli eszközén tárolt helyadatok helyét. Az alkalmazás futtatásához szüksége lesz a fejlesztői gépen a .NET Core SDK 2.1.0 vagy annál nagyobbra. Kövesse az alábbi lépéseket a szimulált telemetria-adatIoT Hubek küldéséhez.

1. Töltse le a [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) c# projektet. 

2. Nyissa meg a simulatedCar.cs-fájlt egy tetszőleges szövegszerkesztőben, és cserélje le az értékét `connectionString` az eszköz regisztrálásakor mentettre, és mentse a fájl módosításait.
 
3. A helyi terminál ablakban navigáljon a C# projekt gyökérkönyvtárához, és futtassa a következő parancsot a szükséges csomagok telepítéséhez a szimulált eszköz alkalmazáshoz:
    
    ```cmd/sh
    dotnet restore
    ```

4. Ugyanebben a terminálban futtassa a következő parancsot az autókölcsönző szimulációs alkalmazás létrehozásához és futtatásához:

    ```cmd/sh
    dotnet run
    ```

  A helyi terminálnak az alábbihoz hasonlóan kell kinéznie.

  ![Terminál kimenete](./media/tutorial-iot-hub-maps/terminal.png)

Ha most megnyitja a blob Storage-tárolót, akkor négy blobot láthat a helyekhez, ahol a jármű kívül esik a geokerítésen.

![Adja meg a blobot](./media/tutorial-iot-hub-maps/blob.png)

Az alábbi Térkép négy pontot mutat be, ahol a jármű kívül esik a geokerítésen, és rendszeres időközönként naplózva van.

![szabálysértési Térkép](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>További lépések

Az oktatóanyagban használt Azure Maps API-k megismeréséhez lásd:

* [Fordított keresési címek lekérése](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Geokerítésen beolvasása](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

A Azure Maps REST API-k teljes listájáért lásd:

* [Azure Maps REST API-k](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

A IoT Plug and Play további tudnivalókért lásd:

* [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

A IoT Azure minősítéssel rendelkező eszközök listájának lekéréséhez látogasson el a következő webhelyre:

* [Azure Certified-eszközök](https://catalog.azureiotsolutions.com/)

Ha többet szeretne megtudni arról, hogyan küldhet eszközről a Felhőbeli telemetria, és fordítva, tekintse meg a következőt:

* [Telemetria küldése egy eszközről](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
