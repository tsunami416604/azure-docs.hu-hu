---
title: 'Oktatóanyag: IoT térbeli elemzés megvalósítása | Microsoft Azure Maps'
description: Integrálja az IoT Hubot a Microsoft Azure Maps szolgáltatás API-jaival.
author: philmea
ms.author: philmea
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: cfea9aa7bfcc9a9698bb93bdf54797481b8539ce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333965"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Oktatóanyag: IoT térbeli elemzés megvalósítása az Azure Maps használatával

Egy IoT-forgatókönyvben gyakori a térben és időben előforduló releváns események rögzítése és nyomon követése. Példaforgatókönyvek közé tartozik a flottakezelés, az eszközkövetés, a mobilitás és az intelligens városi alkalmazások. Ez az oktatóanyag végigvezeti a megoldásmintán az Azure Maps API-k használatával. A releváns eseményeket az IoT Hub rögzíti az Event Grid által biztosított esemény-előfizetési modell használatával.

Ebben a tutorial lesz:

> [!div class="checklist"]
> * IoT Hub létrehozása
> * Geokerítés-terület feltöltése az Azure Maps adatszolgáltatásban az Adatfeltöltés API használatával.
> * Hozzon létre egy függvényt az Azure Functionsben, és valósítsa meg az Azure Maps térbeli elemzésen alapuló üzleti logikáját.
> * Iratkozzon fel az IoT-eszköz telemetriai események az Azure-függvény event griden keresztül.
> * Szűrje a telemetriai eseményeket az IoT Hub-üzenet-útválasztás használatával.
> * Hozzon létre egy tárfiókot a releváns eseményadatok tárolására.
> * Járműbe építkező IoT-eszköz szimulálása.
    

## <a name="use-case"></a>Használati eset

Ez a megoldás egy olyan forgatókönyvet mutat be, amelyben egy autókölcsönző cég azt tervezi, hogy figyelemmel kíséri és naplózza a bérelt autóinak eseményeit. Autókölcsönző cégek általában autót bérelni, hogy egy adott földrajzi régióban. Le kell nyomozniuk az autókat, amíg bérbe adják őket. A kiválasztott földrajzi régiót elhagyó autó példányait naplózni kell. Az adatok naplózása biztosítja, hogy a házirendek, díjak és egyéb üzleti szempontok megfelelően legyenek kezelve.

Használati esetünkben a bérelt autók olyan IoT-eszközökkel vannak felszerelve, amelyek rendszeresen telemetriai adatokat küldenek az Azure IoT Hubnak. A telemetria tartalmazza az aktuális helyet, és jelzi, hogy az autó motorja jár-e. Az eszköz helysémája megfelel a térinformatikai adatok IoT [Plug and Play sémájának.](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md) A bérelt autó eszköztelemetriai sémája a következőképpen néz ki:

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

Használjuk a járműbe beépített eszköz telemetriát a célunk eléréséhez. Geofencing szabályokat akarunk végrehajtani. És szeretnénk válaszolni, ha kapunk egy eseményt, amely jelzi, hogy az autó elmozdult. Ehhez az eszköz telemetriai eseményeire az IoT Hubról az Event Griden keresztül előfizetünk. 

Számos módja van az Event Gridre való feliratkozáshoz, ebben az oktatóanyagban az Azure Functions-t használjuk. Az Azure Functions reagál az Event Gridben közzétett eseményekre. Azt is megvalósítja autókölcsönző üzleti logika, amely az Azure Maps térbeli elemzés. 

Az Azure-függvényen belüli kód ellenőrzi, hogy a jármű elhagyta-e a geokerítést. Ha a jármű elhagyta a geokerítést, az Azure-függvény további információkat gyűjt, például az aktuális helyhez társított címet. A függvény is megvalósítja a logikát, hogy értelmes eseményadatokat tároljon egy adatblob-tárolóban, amely segít az esemény körülményeinek leírásában. 

Az esemény körülményei hasznosak lehetnek az autókölcsönző cég és a kölcsönző számára. Az alábbi ábra magas szintű áttekintést nyújt a rendszerről.

 
  <center>

  ![Rendszer áttekintése](./media/tutorial-iot-hub-maps/system-diagram.png)
  
  </center>

Az alábbi ábra a kékkel kiemelve geokerítés-területet ábrázolja. A bérelt jármű útvonalát zöld vonal jelzi.

  ![Geokerítés útvonal](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Előfeltételek 

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az oktatóanyag lépéseit végre kell hoznia egy erőforráscsoportot az Azure Portalon. Erőforráscsoport létrehozásához tegye a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. Válassza az **Erőforráscsoportok** lehetőséget.
    
   ![Erőforráscsoportok](./media/tutorial-iot-hub-maps/resource-group.png)

3. Az **Erőforráscsoportok csoportban**válassza a **Hozzáadás**lehetőséget.
    
   ![Erőforráscsoport hozzáadása](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Adja meg a következő tulajdonságértékeket:
    * **Előfizetés:** Válassza ki az Azure-előfizetést.
    * **Erőforráscsoport:** Írja be a "ContosoRental" értéket az erőforráscsoport neveként.
    * **Régió:** Válasszon egy régiót az erőforráscsoporthoz.  

    ![Erőforráscsoport részletei](./media/tutorial-iot-hub-maps/resource-details.png)

    Válassza **a Véleményezés + létrehozás**lehetőséget, majd a következő lapon a **Létrehozás** lehetőséget.

### <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása 

Az Azure Maps térbeli elemzésen alapuló üzleti logika megvalósításához létre kell hoznunk egy Azure Maps-fiókot az általunk létrehozott erőforráscsoportban. Kövesse a [Fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps) című, Azure Maps-fiók-előfizetés s1-es tarifacsomaggal való létrehozásához kövesse az utasításokat. Kövesse az [elsődleges kulcs beszerzése](quick-demo-map-app.md#get-the-primary-key-for-your-account) című lépéseket a fiók elsődleges kulcsának beszerzéséhez. Az Azure Maps hitelesítéssel kapcsolatos további tudnivalókról az [Azure Maps hitelesítésének kezelése](how-to-manage-authentication.md)című témakörben talál.



### <a name="create-a-storage-account"></a>Create a storage account

Az eseményadatok naplózásához létrehozunk egy általános célú **v2storage-t,** amely hozzáférést biztosít az Összes Azure Storage-szolgáltatáshoz: blobokhoz, fájlokhoz, várólistákhoz, táblákhoz és lemezekhez.  Ezt a tárfiókot a "ContosoRental" erőforráscsoportba kell helyeznünk az adatok blobként való tárolásához. Tárfiók létrehozásához kövesse a tárfiók létrehozása című [útmutatóutasításait.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) Ezután létre kell hoznunk egy tárolót a blobok tárolásához. Ehhez kövesse az alábbi lépéseket:

1. A "tárfiók - blob, fájl, tábla, várólista", keresse meg a tárolók.

    ![Festékfoltok](./media/tutorial-iot-hub-maps/blobs.png)

2. Kattintson a bal felső sarokban lévő tárológombra, és nevezze el a tárolót "contoso-rental-logs"-nak, majd kattintson az "OK" gombra.

    ![blob-tároló](./media/tutorial-iot-hub-maps/blob-container.png)

3. Nyissa meg a tárfiók **Access gombpaneljét,** és másolja a "tárfiók nevét" és a "hozzáférési kulcsot". Egy későbbi lépésben van rájuk szükség.

    ![hozzáférési kulcsok](./media/tutorial-iot-hub-maps/access-keys.png)


Most már van egy tárfiókot, és egy tárolót az eseményadatok naplózásához. Ezután hozzon létre egy IoT hubot.

### <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Az IoT Hub egy felügyelt szolgáltatás a felhőben. Az IoT Hub központi üzenetközpontként működik az IoT-alkalmazások és az általa kezelt eszközök közötti kétirányú kommunikációhoz. Az eszköz telemetriai üzenetek et egy eseményrácsra irányítsa, hozzon létre egy IoT Hubot a "ContosoRental" erőforráscsoporton belül. Állítson be egy üzenetútvonal-integrációt, ahol az üzeneteket az autó motorjának állapota alapján szűrjük. Az eszköz telemetriai üzeneteit is elküldjük az Eseményrácsra, amikor az autó mozog.

> [!Note] 
> Az IoT Hub eszköztelemetriai események az Event Grid nyilvános előzetes verzióban való közzétételére irányuló funkciói. A nyilvános előzetes verziófunkciói **az USA keleti régióiban, az USA nyugati régiójában, Nyugat-Európában, az Azure Governmentben, az Azure China 21Vianetben** és az Azure **Germany-ban**érhetők el. 

Hozzon létre egy Iot Hub ot az [IoT Hub szakasz létrehozásának](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub)lépéseit követve.


### <a name="register-a-device"></a>Eszköz regisztrálása 

Az IoT Hubhoz való csatlakozáshoz egy eszközt regisztrálni kell. Ha regisztrálni szeretne egy eszközt az IoT hubbal, kövesse az alábbi lépéseket:

1. Az IoT Hubban kattintson az "IoT-eszközök" panelre, és kattintson az "Új" gombra.

    ![eszköz hozzáadása](./media/tutorial-iot-hub-maps/add-device.png)

2. Az eszköz létrehozása lapon nevezze el az IoT-eszközt, és kattintson a "Mentés" gombra.
    
    ![regiszter-eszköz](./media/tutorial-iot-hub-maps/register-device.png)

3. Mentse az eszköz **elsődleges kapcsolati karakterláncát,** hogy azt egy későbbi lépésben használhassa, amelyben ezzel a kapcsolati karakterlánccal helyőrzőt kell módosítania.

    ![eszköz hozzáadása](./media/tutorial-iot-hub-maps/connection-string.png)

## <a name="upload-geofence"></a>Geokerítés feltöltése

A [Postman alkalmazás](https://www.getpostman.com) segítségével [feltöltjük a geokerítést](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) az Azure Maps szolgáltatásba az Azure Maps Adatfeltöltési API használatával. Minden olyan esemény, amikor az autó kívül van a geokerítésen, naplózódik.

Nyissa meg a Postman alkalmazást, és kövesse az alábbi lépéseket a geokerítés feltöltéséhez az Azure Maps adatfeltöltési API használatával.  

1. A Postman alkalmazásban kattintson az új | Hozzon létre újat, és válassza a Kérés lehetőséget. Adja meg a Geokerítés-adatok feltöltése kérelem nevét, jelölje ki azt a gyűjteményt vagy mappát, amelybe menteni szeretné őket, majd kattintson a Mentés gombra.

    ![Geokerítések feltöltése postás használatával](./media/tutorial-iot-hub-maps/postman-new.png)

2. Válassza a POST HTTP metódus lehetőséget a szerkesztő lapon, és írja be a következő URL-címet a POST kéréshez.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    A "geojson" érték `dataFormat` az URL-elérési út paraméterével szemben a feltöltött adatok formátumát jelöli.

3. Kattintson a **Params**gombra, és adja meg a következő kulcs/érték párokat, amelyeket a POST kérés URL-címéhez használni kell. Cserélje le az előfizetési kulcs értékét az Azure Maps-kulcsra.
   
    ![Kulcs-érték params postás](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Kattintson **a Törzs** gombra, majd válassza ki a **nyers** beviteli formátumot, és válassza a **JSON (alkalmazás/szöveg)** elemet a legördülő listából. Nyissa meg a JSON-adatfájlt [itt,](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)és másolja a Jsont a törzsrészbe a feltöltendő adatokként, és kattintson a **Küldés gombra.**
    
    ![adatok feladása](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Tekintse át a válaszfejléceket. Sikeres kérés esetén a **Hely** fejléce tartalmazza az állapotURI-t a feltöltési kérelem aktuális állapotának ellenőrzéséhez. Az állapotURI a következő formátumú lesz. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Másolja az állapotURI-t, és fűzze hozzá egy `subscription-key` paramétert. Rendelje hozzá az Azure Maps-fiók `subscription-key` előfizetési kulcsának értékét a paraméterhez. Az állapotURI-formátumnak az alábbihoz `{Subscription-key}` hasonlónak kell lennie, és az előfizetési kulccsal kell helyettesítenie.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. A rendszer `udId` lekéréséhez nyisson meg egy új lapot a Postman alkalmazásban, és válassza a HTTP-módszer beírása lehetőséget az építő lapon, és kérjen GET-kérelmet az állapotURI-nál. Ha az adatok feltöltése sikeres volt, egy udId-ot fog kapni a választörzsben. Másolja az udId-et későbbi használatra.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


Ezután létrehozunk egy Azure-függvényt a "ContosoRental" erőforráscsoporton belül, majd beállítunk egy üzenetútvonalat az IoT Hubban az eszköz telemetriai üzenetek szűréséhez.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Azure-függvény létrehozása és Event Grid-előfizetés hozzáadása

Az Azure Functions egy kiszolgáló nélküli számítási szolgáltatás, amely lehetővé teszi számunkra, hogy igény szerint kódot futtassunk anélkül, hogy kifejezetten ki kellene építeni vagy kezelni kellene a számítási infrastruktúrát. Ha többet szeretne megtudni az Azure Functionsről, tekintse meg az [Azure-függvények dokumentációját.](https://docs.microsoft.com/azure/azure-functions/functions-overview) 

A függvényben megvalósított logika a járműbe tartozó eszköz telemetriáiból érkező helyadatokat használja a geokerítés állapotának felméréséhez. Abban az esetben, ha egy adott jármű kívül kerül a geokerítésen, a függvény több információt gyűjt, például a hely címét a [Keresés címének visszafordítása API-n](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)keresztül. Ez az API egy adott helykoordinátát egy emberi érthető utcacímké fordítja. 

Az összes releváns eseményinformáció ezután a blob tárolóban marad. 5. lépés alatt rámutat, hogy a futtatható kódot végrehajtó ilyen logika. Az alábbi lépéseket követve hozzon létre egy Azure-függvényt, amely adatnaplókat küld a blob storage-fiók blobtárolójába, és hozzáad hozzá egy Event Grid-előfizetést.

1. Az Azure Portal irányítópultján válassza az erőforrás létrehozása lehetőséget. Válassza a **Számítási lehetőséget** az elérhető erőforrástípusok listájából, majd a **Függvényalkalmazás lehetőséget.**

    ![létrehozás-erőforrás](./media/tutorial-iot-hub-maps/create-resource.png)

2. A **Függvényalkalmazás** létrehozása lapon nevezze el a függvényalkalmazást. Az **Erőforráscsoport**csoportban válassza **a Meglévő használata**lehetőséget, és válassza a "ContosoRental" lehetőséget a legördülő listából. Válassza a ".NET Core" lehetőséget futásidejű veremként. A **Tárhely** **csoportban a Storage-fiók**csoportban válassza ki a tárfiók nevét egy korábbi lépésből. A mi korábbi lépésben, mi nevű tárfiók **v2storage.**  Ezután válassza **a Véleményezés+Létrehozás lehetőséget.**
    
    ![létrehozás-alkalmazás](./media/tutorial-iot-hub-maps/rental-app.png)

2. Tekintse át a függvényalkalmazás részleteit, és válassza a "Létrehozás" lehetőséget.

3. Az alkalmazás létrehozása után hozzá kell adnunk egy függvényt. Nyissa meg a függvényalkalmazást. A függvény hozzáadásához kattintson az **Új függvény** gombra, és válassza a **portálon való** fejlesztői környezetet. Ezután válassza a **Folytatás gombot.**

    ![létrehozás-függvény](./media/tutorial-iot-hub-maps/function.png)

4. Válassza a **További sablonok lehetőséget,** majd kattintson **a Befejezés gombra és a sablonok megtekintésére**. 

5. Válassza ki a sablont egy **Azure Event Grid Trigger**. Ha a program kéri, telepítse a bővítményeket, nevezze el a függvényt, és válassza a **Létrehozás gombot.**

    ![függvénysablon](./media/tutorial-iot-hub-maps/eventgrid-funct.png)
    
    Az **Azure Event Hub trigger** és az Azure Event Grid **Trigger** hasonló ikonokat tartalmaznak. Győződjön meg arról, hogy az **Azure Event Grid Trigger lehetőséget választja.**

6. Másolja a [C# kódot](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) a függvénybe.
 
7. A C# parancsfájlban cserélje le a következő paramétereket. Kattintson a **Mentés** gombra. Még ne kattintsa a **Futtatás** gombra
    * Cserélje le a **SUBSCRIPTION_KEY** az Azure Maps-fiók elsődleges előfizetési kulcsára.
    * Cserélje le az **UDID-t** a feltöltött geokerítés azonosítójára, 
    * A **CreateBlobAsync** függvény a parancsfájlban létrehoz egy blob ot eseményenként az adattárfiókban. Cserélje le a **ACCESS_KEY**, **ACCOUNT_NAME**és **STORAGE_CONTAINER_NAME** a tárfiók hozzáférési kulcsára, fióknevére és adattárolótárolójára.

10. Kattintson az **Event Grid-előfizetés hozzáadása gombra.**
    
    ![add-event-grid](./media/tutorial-iot-hub-maps/add-egs.png)

11. Töltse ki az előfizetés adatait az **EVENT SUBSCRIPTION DETAILS (Esemény- előfizetés részletei)** csoportban, és nevezze el esemény-előfizetését. Az Eseményséma (Eseményséma) lehetőséget válassza az "Event Grid Séma" lehetőséget. A **TOPIC DETAILS (TÉMAKÖR RÉSZLETEI)** csoportban válassza az "Azure IoT Hub-fiókok" témakörtípust. Válassza ki ugyanazt az előfizetést, amelyet az erőforráscsoport létrehozásához használt, válassza a "ContosoRental" lehetőséget "Erőforráscsoportként". Válassza ki az IoT Hub létrehozott "erőforrásként". Válassza **az eszköztelemetriai adatokat** eseménytípusként. Ezek a beállítások kiválasztása után látni fogja a "Témakör típusa" változás "IoT Hub" automatikusan.

    ![esemény-rács-előfizetés](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Események szűrése az IoT Hub üzenet-útválasztásával

Miután hozzáadott egy Event Grid-előfizetést az Azure-függvényhez, megjelenik egy alapértelmezett üzenetútvonal az Event Gridhöz az IoT Hub **Üzenet-útválasztási** paneljén. Az üzenet-útválasztás lehetővé teszi, hogy különböző adattípusokat irányítson különböző végpontokhoz. Például az eszköz telemetriai üzenetek, eszköz életciklus-események és az eszköz ikermódosítási események. Az IoT hub üzenetútválasztásáról az [IoT Hub üzenetútválasztásának használata.](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)

![hub-EG-útvonal](./media/tutorial-iot-hub-maps/hub-route.png)

A mi példa forgatókönyv, azt szeretnénk, hogy kiszűrje az összes üzenetet, ahol a bérelt jármű mozog. Az ilyen eszköztelemetriai események eseményrácson való közzétételéhez az útválasztási lekérdezést használjuk azon események szűrésére, ahol a `Engine` tulajdonság **"ON"**. Az IoT-eszközök közötti üzenetek lekérdezése különböző módokon történik, ha többet szeretne megtudni az üzenetek útválasztási szintaxisáról, olvassa el az [IoT Hub üzenetútválasztáscímű témakört.](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax) Útválasztási lekérdezés létrehozásához kattintson a **RouteToEventGrid** útvonalra, és cserélje le az **Útválasztási lekérdezést** **"Motor='ON"-ra,** majd kattintson a **Mentés gombra.** Most az IoT hub csak akkor teszi közzé az eszköz telemetriai, ahol a motor be van kapcsolva.

![hub-EG-szűrő](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Telemetriai adatok küldése az IoT Hubra

Miután az Azure-függvény működik, most már telemetriai adatokat küldhetünk az IoT Hubnak, amely átirányítja azt az Eseményrácsra. Használjuk a C# alkalmazás szimulálására helyadatok egy járműbe tartozó eszköz egy bérelt autó. Az alkalmazás futtatásához a .NET Core SDK 2.1.0 vagy nagyobb szükséges a fejlesztői gépen. Az alábbi lépéseket követve küldjön szimulált telemetriai adatokat az IoT Hubnak.

1. Töltse le a [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C# projektet. 

2. Nyissa meg a simulatedCar.cs fájlt egy ön által választott `connectionString` szövegszerkesztőben, és cserélje le az eszköz regisztrálásakor mentett értékre, és mentse a módosításokat a fájlba.
 
3. Ellenőrizze, hogy telepítve van-e a .NET Core a számítógépen. A helyi terminálablakban keresse meg a C# projekt gyökérmappáját, és futtassa a következő parancsot a szimulált eszközalkalmazáshoz szükséges csomagok telepítéséhez:
    
    ```cmd/sh
    dotnet restore
    ```

4. Ugyanebben a terminálban futtassa a következő parancsot a bérelt autó szimulációs alkalmazás létrehozásához és futtatásához:

    ```cmd/sh
    dotnet run
    ```

  A helyi terminálnak az alábbinak kell lennie.

  ![Terminálkimenet](./media/tutorial-iot-hub-maps/terminal.png)

Ha most megnyitja a blob tárolótároló, képesnek kell lennie arra, hogy négy blobok helyeken, ahol a jármű kívül volt a geokerítés.

![Blob megadása](./media/tutorial-iot-hub-maps/blob.png)

Az alábbi térkép négy pontot mutat, ahol a jármű a geokerítésen kívül volt, rendszeres időközönként naplózva.

![szabálysértési térkép](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>További lépések

Az ebben az oktatóanyagban használt Azure Maps API-k megismeréséhez lásd:

* [A keresési cím sztornírozása](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Geokerítés bekéselése](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Az Azure Maps REST API-k teljes listáját a következő témakörben található:

* [Azure Maps REST API-k](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Az IoT Plug and Play című témakörben olvashat bővebben:

* [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

Az IoT-hez hitelesített Azure-tanúsítvánnyal rendelkező eszközök listájának be) a következő oldalra látogathatja:

* [Azure-tanúsítvánnyal rendelkező eszközök](https://catalog.azureiotsolutions.com/)

Ha többet szeretne tudni arról, hogyan küldheti el az eszközt a felhőbeli telemetriai adatoknak, és fordítva, olvassa el az:

* [Telemetria küldése egy eszközről](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
