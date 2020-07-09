---
title: 'Oktatóanyag: a IoT térbeli elemzésének megvalósítása | Microsoft Azure térképek'
description: IoT Hub integrálása a Microsoft Azure Maps Service API-kkal.
author: philmea
ms.author: philmea
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: cf1d732391f86bec9c0ec2de1e6bace2e808bb19
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85318926"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Oktatóanyag: a IoT térbeli elemzés megvalósítása Azure Maps használatával

Egy IoT-forgatókönyv esetében gyakori, hogy rögzítse és nyomon követhesse a térben és időben előforduló releváns eseményeket. Ilyenek például a flotta kezelése, az eszközök nyomon követése, a mobilitás és az intelligens városi alkalmazások. Ez az oktatóanyag végigvezeti a Azure Maps API-kat használó megoldási mintákon. A kapcsolódó eseményeket a IoT Hub rögzíti a Event Grid által biztosított esemény-előfizetési modell használatával.

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

Ez a megoldás egy olyan forgatókönyvet mutat be, amelyben egy autókölcsönző cég tervezi a bérelt személygépkocsik eseményeinek figyelését és naplózását. Az autókölcsönző vállalatok általában egy adott földrajzi régióba bérelnek autót. Az autókat a bérletük ideje alatt nyomon kell követni. A kiválasztott földrajzi régiót elhagyó autó példányait naplózni kell. A naplózási adatai biztosítják, hogy a szabályzatok, díjak és egyéb üzleti szempontok megfelelően kezelhetők legyenek.

A használati esetünkben a bérelt autók olyan IoT-eszközökkel vannak ellátva, amelyek rendszeresen küldenek telemetria-t az Azure IoT Hubba. A telemetria tartalmazza az aktuális helyet, és jelzi, hogy az autó motorja fut-e. Az eszköz helyének sémája megfelel a [térinformatikai IoT Plug and Play sémájának](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). A bérleti autó eszközének telemetria sémája a következőképpen néz ki:

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

A következő lépésekkel végezheti el a járműbeli eszközök telemetria a célunk eléréséhez. Geokerítések-szabályokat szeretnénk végrehajtani. És azt szeretnénk, hogy válaszoljon arra az esetre, amikor az autót áthelyezték. Ehhez az IoT Hub telemetria-eseményeit Event Grid-on keresztül előfizetjük az eszközre. 

A Event Gridra való előfizetésnek számos módja van, ebben az oktatóanyagban a Azure Functionst használjuk. A Azure Functions a Event Grid közzétett eseményekre lép át. Emellett autókölcsönzési üzleti logikát is megvalósít, amely Azure Maps térbeli elemzéseken alapul. 

Az Azure-függvényen belüli kód ellenőrzi, hogy a jármű elhagyta-e a geokerítésen. Ha a jármű elhagyta a geokerítésen, az Azure-függvény további információkat gyűjt, például az aktuális helyhez társított címeket. A függvény emellett olyan logikát is megvalósít, amely egy adatblob-tárolóban található, az eseményekre vonatkozó körülmények leírását megkönnyítő, értelmezhető esemény-adatmennyiséget tárol. 

Az esemény körülményei hasznosak lehetnek az autókölcsönző vállalat és a bérleti ügyfél számára. A következő diagram áttekintést nyújt a rendszerről.

 
  <center>

  ![A rendszerek áttekintése](./media/tutorial-iot-hub-maps/system-diagram.png)
  
  </center>

A következő ábra a kék színnel jelölt geokerítésen jelöli. A bérleti jármű útvonalát zöld vonallal jelölik.

  ![Geokerítésen útvonal](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Előfeltételek 

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az oktatóanyag lépéseinek elvégzéséhez először létre kell hoznia egy erőforráscsoportot a Azure Portal. Erőforráscsoport létrehozásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza az **Erőforráscsoportok** lehetőséget.
    
   ![Erőforráscsoportok](./media/tutorial-iot-hub-maps/resource-group.png)

3. Az **erőforráscsoportok**területen válassza a **Hozzáadás**lehetőséget.
    
   ![Erőforráscsoport hozzáadása](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Adja meg a következő tulajdonságértékek értékét:
    * **Előfizetés:** Válassza ki az Azure-előfizetését.
    * **Erőforráscsoport:** Adja meg az "ContosoRental" nevet az erőforráscsoport neveként.
    * **Régió:** Válassza ki az erőforráscsoport régióját.  

    ![Erőforráscsoport részletei](./media/tutorial-iot-hub-maps/resource-details.png)

    Válassza a **felülvizsgálat + létrehozás**lehetőséget, majd válassza a **Létrehozás** lehetőséget a következő oldalon.

### <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása 

Az üzleti logika Azure Maps térbeli elemzésen alapuló megvalósításához létre kell hozni egy Azure Maps fiókot az általunk létrehozott erőforráscsoporthoz. Kövesse a [fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps) Azure Maps fiók előfizetésének S1 árképzési szinten való létrehozásához című témakör utasításait. A fiók elsődleges kulcsának beszerzéséhez kövesse az [elsődleges kulcs beolvasása](quick-demo-map-app.md#get-the-primary-key-for-your-account) című témakör lépéseit. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](how-to-manage-authentication.md).



### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Az események naplózásához hozzunk létre egy általános célú **v2storage** , amely hozzáférést biztosít az összes Azure Storage-szolgáltatáshoz: Blobok, fájlok, várólisták, táblák és lemezek.  Ezt a Storage-fiókot a "ContosoRental" erőforráscsoporthoz kell helyezni az adatblobok tárolásához. A Storage-fiók létrehozásához kövesse a [Storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)című témakör utasításait. Ezután létre kell hoznia egy tárolót a Blobok tárolásához. Ehhez kövesse az alábbi lépéseket:

1. A "Storage-fiók – blob, fájl, tábla, üzenetsor" területen navigáljon a tárolók elemre.

    ![Blobok](./media/tutorial-iot-hub-maps/blobs.png)

2. Kattintson a bal felső sarokban található Container (tároló) gombra, és nevezze el a "contoso-Rental-logs" tárolót, és kattintson az "OK" gombra.

    ![BLOB tároló](./media/tutorial-iot-hub-maps/blob-container.png)

3. Navigáljon a Storage-fiók **hozzáférési kulcsok** paneljére, és másolja a "Storage-fiók neve" és a "hozzáférési kulcs" kifejezést. Egy későbbi lépésben szükségesek.

    ![hozzáférési kulcsok](./media/tutorial-iot-hub-maps/access-keys.png)


Most már rendelkezik egy Storage-fiókkal és egy tárolóval az események naplózásához. Ezután létrehozunk egy IoT hubot.

### <a name="create-an-iot-hub"></a>IoT Hub létrehozása

A IoT Hub a felhőben felügyelt szolgáltatás. A IoT Hub a IoT-alkalmazás és az általa felügyelt eszközök közötti kétirányú kommunikációhoz központi üzenetsorként működik. Az eszköz telemetria-üzeneteinek egy Event Gridba való átirányításához hozzon létre egy IoT Hub a "ContosoRental" erőforráscsoporthoz. Egy üzenet-útválasztási integráció beállítása, ahol az üzenetek szűrése az autó motorjának állapota alapján történik. Az telemetria üzeneteket a Event Grid is küldi a rendszer az autó áthelyezésekor.

> [!Note] 
> A IoT Hub funkciói az eszközön telemetria események közzétételét Event Grid a nyilvános előzetes verzióban. A nyilvános előzetes verzió funkciói az USA keleti régiója, az USA nyugati régiója, **Nyugat-Európa, Azure Government, az Azure China 21Vianet** és az **Azure Germany**kivételével minden régióban elérhetők. 

Hozzon létre egy IOT hubot a [IoT hub létrehozása szakasz](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub)lépéseit követve.


### <a name="register-a-device"></a>Eszköz regisztrálása 

Ahhoz, hogy csatlakozhasson a IoT Hubhoz, regisztrálni kell egy eszközt. Az IoT hub-eszközök regisztrálásához kövesse az alábbi lépéseket:

1. A IoT Hub kattintson az "IoT-eszközök" panelre, és kattintson az "új" elemre.

    ![eszköz hozzáadása](./media/tutorial-iot-hub-maps/add-device.png)

2. Az eszköz létrehozása lapon nevezze el a IoT eszközt, és kattintson a Save (Mentés) gombra.
    
    ![eszköz regisztrálása](./media/tutorial-iot-hub-maps/register-device.png)

3. Mentse az eszköz **elsődleges kapcsolódási karakterláncát** egy későbbi lépésben való használatra, amelyben módosítania kell egy helyőrzőt ezzel a kapcsolódási karakterlánccal.

    ![eszköz hozzáadása](./media/tutorial-iot-hub-maps/connection-string.png)

## <a name="upload-geofence"></a>Geokerítésen feltöltése

A [Poster alkalmazást](https://www.getpostman.com) használva [feltöltjük a geokerítésen](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) a Azure Maps szolgáltatásba a Azure Maps adatfeltöltő API használatával. Minden olyan esemény, amely a geokerítésen kívül esik, a rendszer naplózza.

Nyissa meg a Poster alkalmazást, és kövesse az alábbi lépéseket a geokerítésen feltöltéséhez a Azure Maps, az adatfeltöltő API használatával.  

1. A Poster alkalmazásban kattintson az új elemre | Hozzon létre egy újat, és válassza a kérelem lehetőséget. Adja meg a geokerítésen-adatok feltöltésére vonatkozó kérelem nevét, válassza ki azt a gyűjteményt vagy mappát, ahová menteni szeretné, majd kattintson a Mentés gombra.

    ![Geofences feltöltése a Poster használatával](./media/tutorial-iot-hub-maps/postman-new.png)

2. Válassza a HTTP POST metódus lehetőséget a Builder (szerkesztő) lapon, és adja meg a következő URL-címet a POST-kérelem létrehozásához.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Az URL-cím paraméteréhez tartozó "geojson" érték a `dataFormat` feltöltött adatformátumot jelöli.

3. Kattintson a **Paraméterek**elemre, és adja meg a következő kulcs/érték párokat, amelyeket a post kérelem URL-címéhez kíván használni. Cserélje le az előfizetés-kulcs értékét a Azure Maps kulcsra.
   
    ![Kulcs-érték paraméterek Poster](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Kattintson a **törzs** elemre, majd válassza a **nyers** bemeneti formátum lehetőséget, majd a legördülő listából válassza a **JSON (alkalmazás/szöveg)** lehetőséget. Nyissa meg a JSON-adatfájlt, és másolja a JSON-t a törzs szakaszba a feltölteni kívánt adatok [alapján, majd](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)kattintson a **Küldés**gombra.
    
    ![közzététel](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Tekintse át a válasz fejléceit. Sikeres kérelem esetén a **hely** fejléce tartalmazza az állapot URI-ját a feltöltési kérelem aktuális állapotának vizsgálatához. Az állapot URI-ja a következő formátumú lesz. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Másolja ki az állapot-URI-t, és fűzze hozzá a `subscription-key` paramétert. Rendelje hozzá a Azure Maps fiók előfizetési kulcsának értékét a `subscription-key` paraméterhez. Az állapot URI-formátumának az alábbihoz hasonlóan kell lennie, és az `{Subscription-key}` előfizetési kulccsal kell helyettesíteni.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. A létrehozásához `udId` Nyisson meg egy új fület a Poster alkalmazásban, majd válassza a http-módszer BEolvasása lehetőséget a Builder (szerkesztő) lapon, és tegyen egy Get-kérést az állapot URI-ja Ha az adatok feltöltése sikeres volt, egy udId fog kapni a válasz törzsében. Másolja a udId későbbi használatra.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


A következő lépésben létrehozunk egy Azure-függvényt a "ContosoRental" erőforráscsoporthoz, majd az telemetria üzenetek szűréséhez az üzenet útvonalát a IoT Hubban.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Azure-függvény létrehozása és Event Grid-előfizetés hozzáadása

Azure Functions egy kiszolgáló nélküli számítási szolgáltatás, amely lehetővé teszi a kód igény szerinti futtatását anélkül, hogy explicit módon kellene kiépíteni vagy kezelni a számítási infrastruktúrát. Ha többet szeretne megtudni a Azure Functionsről, tekintse meg az [Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) dokumentációját. 

A függvényben megvalósított logika a geokerítésen állapotának értékeléséhez a járművön kívüli eszköz telemetria érkező helyadatok használatával történik. Abban az esetben, ha egy adott jármű a geokerítésen kívül esik, a függvény további információkat gyűjt, például a hely címét a [keresési címek fordított API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)-n keresztül. Ez az API egy adott helyet hangol le egy emberi, érthető lakcímre. 

Ekkor a rendszer a blob-tárolóban tárolja az összes releváns eseményt. Az alábbi 5. lépés az ilyen logikát megvalósító végrehajtható kódra mutat. Az alábbi lépéseket követve hozzon létre egy Azure-függvényt, amely adatnaplókat küld a blob Storage-fiókban található blob tárolóba, és adjon hozzá egy Event Grid-előfizetést.

1. Az Azure Portal irányítópulton válassza az erőforrás létrehozása lehetőséget. Válassza ki a **számítás** elemet az elérhető erőforrástípusok listájából, majd válassza a **függvényalkalmazás**lehetőséget.

    ![erőforrás létrehozása](./media/tutorial-iot-hub-maps/create-resource.png)

2. A **függvényalkalmazás** létrehozás lapon nevezze el a Function alkalmazást. Az **erőforráscsoport**területen válassza a **meglévő használata**lehetőséget, majd a legördülő listából válassza a "ContosoRental" lehetőséget. Válassza a ".NET Core" lehetőséget a futásidejű Veremként. A tárolás **alatt,** a **Storage-fiók**területen válassza ki a Storage-fiók nevét egy korábbi lépésből. Az előző lépésben elnevezte a Storage-fiók **v2storage**.  Ezután válassza a **felülvizsgálat + létrehozás**elemet.
    
    ![létrehozás – alkalmazás](./media/tutorial-iot-hub-maps/rental-app.png)

2. Tekintse át a Function app részleteit, és válassza a létrehozás lehetőséget.

3. Az alkalmazás létrehozása után hozzá kell adnia egy függvényt. Nyissa meg a Function alkalmazást. A függvény hozzáadásához kattintson az **új függvény** elemre, majd válassza a **portálon** a fejlesztői környezet lehetőséget. Ezután válassza a **Folytatás**lehetőséget.

    ![Create-Function](./media/tutorial-iot-hub-maps/function.png)

4. Válassza a **további sablonok** lehetőséget, majd kattintson **a Befejezés és a sablonok megtekintése**elemre. 

5. Válassza ki a sablont **Azure Event Grid triggerrel**. Ha a rendszer kéri, telepítse a bővítményeket, nevezze el a függvényt, és válassza a **Létrehozás**lehetőséget.

    ![függvény – sablon](./media/tutorial-iot-hub-maps/eventgrid-funct.png)
    
    Az **Azure Event hub eseményindítójának** és a **Azure Event Grid eseményindítónak** hasonló ikonjai vannak. Győződjön meg arról, hogy a **Azure Event Grid triggert**választotta.

6. Másolja a [C#-kódot](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) a függvénybe.
 
7. A C# parancsfájlban cserélje le a következő paramétereket. Kattintson a **Save** (Mentés) gombra. Még ne kattintson a **Futtatás** gombra
    * Cserélje le a **SUBSCRIPTION_KEYt** a Azure Maps fiók elsődleges előfizetési kulcsára.
    * Cserélje le a **UDID** a feltöltött geokerítésen UDID. 
    * A szkriptben a **CreateBlobAsync** függvény egy blobot hoz létre eseményként az adattároló-fiókban. Cserélje le a **ACCESS_KEY**, **ACCOUNT_NAME**és **STORAGE_CONTAINER_NAME** a Storage-fiók hozzáférési kulcsára, a fiók nevére és az adattároló-tárolóra.

10. Kattintson a **Event Grid-előfizetés hozzáadása**elemre.
    
    ![Event-Grid hozzáadása](./media/tutorial-iot-hub-maps/add-egs.png)

11. Töltse ki az előfizetés részleteit az esemény- **előfizetés részletei** területen az esemény-előfizetés mezőben. Az Event Schema esetében válassza a "Event Grid séma" lehetőséget. A **témakör részletek** területén válassza az "Azure IoT hub fiókok" lehetőséget a témakör típusaként. Válassza ki ugyanazt az előfizetést, amelyet az erőforráscsoport létrehozásához használt, válassza az "ContosoRental" lehetőséget az "erőforráscsoport" elemnél. Válassza ki az erőforrásként létrehozott IoT Hub. Válassza ki az **eszköz telemetria** . Miután kiválasztotta ezeket a beállításokat, a "témakör típusa" beállítás automatikusan "IoT Hub" értékre vált.

    ![Event-Grid-előfizetés](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Események szűrése IoT Hub üzenet-útválasztás használatával

Miután hozzáadta Event Grid-előfizetést az Azure-függvényhez, megjelenik egy alapértelmezett üzenet, amely az IoT Hub **üzenet-útválasztási** paneljén Event Grid jelenik meg. Az üzenet-útválasztás lehetővé teszi különböző adattípusok különböző végpontokra való továbbítását. Átirányíthatja például az eszköz telemetria-üzeneteit, az eszközök életciklusával kapcsolatos eseményeket és az eszközök kettős változási eseményeit. További információ az IoT hub üzenet-útválasztásáról: [IoT hub üzenet-útválasztás használata](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

![központ – például útvonal](./media/tutorial-iot-hub-maps/hub-route.png)

Példánkban szeretnénk kiszűrni az összes olyan üzenetet, ahol a kölcsönzési jármű mozog. Ahhoz, hogy az adott eszközön telemetria az eseményeket a Event Gridba, az útválasztási lekérdezés használatával szűrheti azokat az eseményeket, amelyeken a `Engine` tulajdonság **"on"**. Az eszközről a felhőbe irányuló IoT lekérdezések többféleképpen is megtekinthetők, ha többet szeretne megtudni az üzenetek útválasztási szintaxisáról: [IoT hub üzenet-útválasztás](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax). Útválasztási lekérdezés létrehozásához kattintson a **RouteToEventGrid** útvonalra, és cserélje le az **útválasztási lekérdezést** **"Engine =" on ""** értékre, majd kattintson a Save ( **Mentés**) gombra. A IoT hub most csak azt a telemetria teszi közzé, ahol a motor be van kapcsolva.

![központ – pl. szűrő](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Telemetria-IoT Hub küldése

Ha már működik az Azure-függvény, mostantól elküldhetjük a telemetria a IoT Hubba, amely átirányítja a Event Grid. Alkalmazzunk egy C#-alkalmazást, amellyel szimulálható a helyszíni eszközre vonatkozó helyadatok egy bérelt autóban. Az alkalmazás futtatásához szüksége lesz a fejlesztői gépen a .NET Core SDK 2.1.0 vagy annál nagyobbra. Kövesse az alábbi lépéseket a szimulált telemetria-adatIoT Hubek küldéséhez.

1. Töltse le a [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C# projektet. 

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

Ha többet szeretne megtudni arról, hogyan lehet eszközt küldeni a Felhőbeli telemetria, és fordítva, tekintse meg a következőt:

* [Telemetria küldése egy eszközről](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
