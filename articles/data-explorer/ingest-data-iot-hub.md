---
title: Adatok beolvasása IoT Hubból az Azure-ba Adatkezelő
description: Ebből a cikkből megtudhatja, hogyan végezheti el az adatok betöltését az Azure Adatkezelőba IoT Hubból.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 83f5339dbc4f093ba0b7287b53c053e319f928c9
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937390"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer-preview"></a>Adatok beolvasása IoT Hubból az Azure Adatkezelőba (előzetes verzió)

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Adatkezelő a IoT Hub, a big data streaming platform és a IoT betöltési szolgáltatás által betöltött adatok betöltését biztosítja.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

* Hozzon létre egy *testdb*adatbázis-névvel rendelkező [tesztelési fürtöt és adatbázist](create-cluster-database-portal.md) .

* [Egy minta alkalmazás](https://github.com/Azure-Samples/azure-iot-samples-csharp) és egy eszköz szimulálása.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) a minta alkalmazás futtatásához.

## <a name="create-an-iot-hub"></a>IOT hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Eszköz regisztrálása a IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Céltábla létrehozása az Azure Data Explorerben

Most létrehoz egy táblát az Azure Adatkezelőban, amelybe az IoT-hubok elküldik az adatküldést. Létrehozza a táblát a fürtben, és az [**előfeltételként**](#prerequisites)kiépített adatbázist.

1. A Azure Portal navigáljon a fürthöz, és válassza a **lekérdezés**lehetőséget.

    ![ADX-lekérdezés a portálon](media/ingest-data-iot-hub/adx-initiate-query.png)

1. Másolja a következő parancsot az ablakba, és válassza a **Futtatás** elemet a betöltött adatot fogadó tábla (TestTable) létrehozásához.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![Létrehozási lekérdezés futtatása](media/ingest-data-iot-hub/run-create-query.png)

1. Másolja a következő parancsot az ablakba, és válassza a **Futtatás** elemet a bejövő JSON-adattípusok a tábla oszlopnevek és adattípusai (TestTable) szerinti leképezéséhez.

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Azure Adatkezelő-tábla összekötése az IoT hub-vel

Most csatlakozik az Azure Adatkezelő IoT Hub. Ha ez a csatlakozás befejeződött, az IOT hub felé irányuló, az [Ön által létrehozott cél táblába](#create-a-target-table-in-azure-data-explorer)beáramló adatforgalom.

1. Az eszköztáron válassza az **értesítések** lehetőséget annak ellenőrzéséhez, hogy a IoT hub központi telepítés sikeres volt-e.

1. A létrehozott fürt alatt válassza az **adatbázisok** lehetőséget, majd válassza ki a **testdb**létrehozott adatbázist.
    
    ![Tesztadatbázis kiválasztása](media/ingest-data-iot-hub/select-database.png)

1. Válassza **az adatfeldolgozás** lehetőséget, és **adja hozzá az adatkapcsolatok**elemet. Ezután töltse ki az űrlapot a következő információkkal. Ha elkészült, válassza a **Létrehozás** lehetőséget.

    ![IoT Hub-kapcsolatok](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Adatforrás**:

    **Beállítás** | **Mező leírása**
    |---|---|
    | Adatkapcsolat neve | Az Azure Data Explorerben létrehozni kívánt kapcsolat neve.
    | IoT Hub | IoT Hub neve. |
    | Megosztott elérési házirend | A megosztott hozzáférési szabályzat neve. Olvasási engedélyekkel kell rendelkeznie. |
    | Fogyasztói csoport |  A IoT Hub beépített végpontban definiált fogyasztói csoport. |
    | Esemény rendszertulajdonságai | A IoT Hub eseményrendszer tulajdonságai Ha az eseményen több rekord van, akkor a rendszer tulajdonságai hozzáadódnak az elsőhöz. |
    | | 

    > [!NOTE]
    > [Manuális feladatátvétel](/azure/iot-hub/iot-hub-ha-dr#manual-failover)esetén újra létre kell hoznia az adathálózatot.

    **Céltábla**:

    Két lehetőség van a betöltött adatmennyiség útválasztására: *statikus* és *dinamikus*. 
    Ebben a cikkben statikus útválasztást használ, ahol megadhatja a tábla nevét, az adatformátumot és a leképezést. Ezért hagyja, hogy az adatok között ne legyenek kiválasztva **az útválasztási adatok** .

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Tábla | *TestTable* | A **testdb**-ben létrehozott tábla. |
    | Adatformátum | *JSON* | A támogatott formátumok a következők: Avro, CSV, JSON, többsoros JSON, PSV, rendszerállapot-kimutatás, SCSV, TSV és TXT. |
    | Oszlop-hozzárendelés | *TestMapping* | A **testdb**-ben létrehozott leképezés, amely leképezi a bejövő JSON-adattípusokat a **testdb**tartozó oszlopnevek és adattípusok számára. JSON-, többsoros JSON-és AVRO szükséges, és nem kötelező más formátumokhoz.|
    | | |

    > [!NOTE]
    > * Válassza a **saját adatok: útválasztási információ** lehetőséget a dinamikus útválasztás használatához, ahol az adatok tartalmazzák a szükséges útválasztási információkat a [minta alkalmazás](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) megjegyzésében látható módon. Ha a statikus és a dinamikus tulajdonságok is be vannak állítva, a dinamikus tulajdonságok felülbírálják a statikus fájlokat. 
    > * A rendszer csak az adatkapcsolatok létrehozását követően várólistán lévő eseményeket.

## <a name="generate-sample-data-for-testing"></a>Mintaadatok készítése teszteléshez

A szimulálteszköz-alkalmazás egy az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és hőmérséklettel és páratartalommal kapcsolatos szimulált telemetriát küld.

1. Töltse le a C#-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip címről, és bontsa ki a ZIP-archívumot.

1. Egy helyi terminálablakban keresse meg a C#-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\simulated-device** mappába.

1. Nyissa meg a **SimulatedDevice.cs** fájlt egy Ön által választott szövegszerkesztőben.

    Cserélje le a `s_connectionString` változó értékét az eszköz-kapcsolódási sztringre az [eszköz regisztrálása a IoT hub](#register-a-device-to-the-iot-hub). Ezután mentse a **SimulatedDevice.cs** fájl módosításait.

1. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazáshoz szükséges csomagok telepítéséhez:

    ```cmd/sh
    dotnet restore
    ```

1. Futtassa az alábbi parancsot a helyi terminálablakban a szimulálteszköz-alkalmazás létrehozásához és futtatásához:

    ```cmd/sh
    dotnet run
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](media/ingest-data-iot-hub/simulated-device.png)

## <a name="review-the-data-flow"></a>Az adatfolyam áttekintése

Ha az alkalmazás létrehozza az adatforgalmat, most már láthatja az IoT hub adatfolyamatát a fürtben lévő táblába.

1. A Azure Portal az IoT hub alatt megjelenik a nyárs tevékenység, miközben az alkalmazás fut.

    ![IoT Hub metrikák](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. A következő lekérdezés a tesztadatbázison való futtatásával ellenőrizze, hogy hány üzenet került át eddig a pillanatig az adatbázisba.

    ```Kusto
    TestTable
    | count
    ```

1. Az üzenetek tartalmának megtekintéséhez futtassa a következő lekérdezést:

    ```Kusto
    TestTable
    ```

    Az eredményhalmaz:
    
    ![Betöltött adateredmények megjelenítése](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Az Azure Adatkezelő a betöltési folyamat optimalizálására szolgáló összesítési (batch-) szabályzattal rendelkezik az adatfeldolgozáshoz. A házirend alapértelmezés szerint 5 percre vagy 500 MB-ra van konfigurálva, így késést tapasztalhat. Lásd: az összesítési beállításokra vonatkozó [kötegelt házirend](/azure/kusto/concepts/batchingpolicy) . 
    > * Konfigurálja a táblázatot a folyamatos átvitel támogatásához, és távolítsa el a késést a válaszadás időpontjában. Lásd: [streaming Policy](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem tervezi a IoT Hub újbóli használatát, törölje a **test-hub-RG-** t, hogy elkerülje a költségek felmerülését.

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen, majd a létrehozott erőforráscsoport.  

    Ha a bal oldali menü össze van csukva, kattintson a ![Kibontás gomb](media/ingest-data-event-hub/expand.png) a kinyitásához.

   ![A törölni kívánt erőforráscsoport kiválasztása](media/ingest-data-event-hub/delete-resources-select.png)

1. A **test-resource-group** alatt válassza az **Erőforráscsoport törlése** elemet.

1. Az új ablakban írja be a törölni kívánt erőforráscsoport nevét (*test-hub-rg*), majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

* [Az Azure Adatkezelő lekérdezése](web-query-data.md)
