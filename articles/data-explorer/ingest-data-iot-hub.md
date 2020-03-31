---
title: Adatok betöltése az IoT Hubról az Azure Data Explorerbe
description: Ebben a cikkben megtudhatja, hogyan töltheti be (töltheti be) az adatokat az Azure Data Explorerbe az IoT Hubról.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 78455c90bab694b77a5e4a56d0b40518867d8d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188364"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer"></a>Adatok betöltése az IoT Hubról az Azure Data Explorerbe 

> [!div class="op_single_selector"]
> * [Portál](ingest-data-iot-hub.md)
> * [C #](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager-sablon](data-connection-iot-hub-resource-manager.md)

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Data Explorer az IoT Hubból, egy big data streaming platformról és az IoT-betöltési szolgáltatásból biztosít betöltést (adatbetöltést).

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Hozzon létre [egy tesztfürtöt és adatbázist](create-cluster-database-portal.md) *a testdb*adatbázisnévvel.
* [Mintaalkalmazás](https://github.com/Azure-Samples/azure-iot-samples-csharp) és dokumentáció az eszköz szimulálásához.
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) a mintaalkalmazás futtatásához.

## <a name="create-an-iot-hub"></a>Iot hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Eszköz regisztrálása az IoT Hubba

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Céltábla létrehozása az Azure Data Explorerben

Most hozzon létre egy táblát az Azure Data Explorerben, amelybe az IoT Hubs adatokat küld. A táblát az [**Előfeltételek**](#prerequisites)csoportban kiépített fürtben és adatbázisban hozza létre.

1. Az Azure Portalon keresse meg a fürtöt, és válassza a **Lekérdezés**lehetőséget.

    ![ADX-lekérdezés a portálon](media/ingest-data-iot-hub/adx-initiate-query.png)

1. Másolja a következő parancsot az ablakba, és válassza a **Futtatás** lehetőséget a bevitt adatokat fogadó tábla (TestTable) létrehozásához.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![Létrehozási lekérdezés futtatása](media/ingest-data-iot-hub/run-create-query.png)

1. Másolja a következő parancsot az ablakba, és válassza a **Futtatás** lehetőséget, ha a bejövő JSON-adatokat a tábla oszlopnevéhez és adattípusához (TestTable) szeretné leképezni.

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Az Azure Data Explorer tábla csatlakoztatása az IoT-központhoz

Most csatlakozik az IoT Hub az Azure Data Explorer. Ha ez a kapcsolat befejeződött, a sokpontos elosztóba áramló adatok a [létrehozott céltáblába](#create-a-target-table-in-azure-data-explorer)kerülnek.

1. Válassza **az értesítések az** eszköztáron, hogy ellenőrizze, hogy az IoT Hub üzembe helyezése sikeres volt-e.

1. A létrehozott fürt alatt válassza az **Adatbázisok** lehetőséget, majd jelölje ki a testdb által létrehozott **adatbázist.**
    
    ![Tesztadatbázis kiválasztása](media/ingest-data-iot-hub/select-database.png)

1. Válassza **az Adatbetöltés** és **az Adatkapcsolat hozzáadása**lehetőséget. Ezután töltse ki az űrlapot a következő információkkal. Ha végzett, válassza a **Létrehozás** gombot.

    ![IoT Hub-kapcsolat](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Adatforrás:**

    **Beállítás** | **Mező leírása**
    |---|---|
    | Adatkapcsolat neve | Az Azure Data Explorerben létrehozni kívánt kapcsolat neve
    | IoT Hub | IoT Hub neve |
    | Megosztott hozzáférési házirend | A megosztott hozzáférési házirend neve. Olvasási engedéllyel kell rendelkeznie |
    | Fogyasztói csoport |  Az IoT Hub beépített végpontjában definiált fogyasztói csoport |
    | Az eseményrendszer tulajdonságai | Az [IoT Hub eseményrendszer tulajdonságai.](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages) Rendszertulajdonságok hozzáadásakor [hozzon létre](/azure/kusto/management/create-table-command) vagy [frissítsen](/azure/kusto/management/alter-table-command) táblasémát, és [képezképezést](/azure/kusto/management/mappings) a kijelölt tulajdonságokhoz. | | | 

    > [!NOTE]
    > [Manuális feladatátvétel](/azure/iot-hub/iot-hub-ha-dr#manual-failover)esetén újra létre kell hoznia az adatkapcsolatot.

    **Céltábla:**

    A bevitt adatok útválasztása két lehetőség közül választhat: *statikus* és *dinamikus.* 
    Ebben a cikkben statikus útválasztást kell használnia, ahol megadhatja a tábla nevét, adatformátumát és leképezését. Ezért hagyja a **Saját adatok útválasztási adatait** bejelölve.

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Tábla | *TestTable* | A **testdb-ben**létrehozott tábla . |
    | Adatformátum | *JSON* | A támogatott formátumok: Avro, CSV, JSON, MULTILINE JSON, PSV, SOHSV, SCSV, TSV, TSVE és TXT. |
    | Oszlopleképezés | *TestMapping* | A **testdb-ben**létrehozott [leképezés](/azure/kusto/management/mappings) , amely leképezi a bejövő JSON-adatokat a **testdb**oszlopnevéhez és adattípusához. JSON, MULTILINE JSON és AVRO esetén, és más formátumok esetén nem kötelező.|
    | | |

    > [!NOTE]
    > * Válassza **a Saját adatok között szerepelnek útválasztási adatok** dinamikus útválasztás, ahol az adatok tartalmazzák a szükséges útválasztási információkat, mint a [minta alkalmazás](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) megjegyzéseket. Ha mind a statikus, mind a dinamikus tulajdonságok be vannak állítva, a dinamikus tulajdonságok felülírják a statikus tulajdonságokat. 
    > * Csak az adatkapcsolat létrehozása után várólistára helyezett események kerülnek betöltésre.

[!INCLUDE [data-explorer-container-system-properties](../../includes/data-explorer-container-system-properties.md)]

## <a name="generate-sample-data-for-testing"></a>Mintaadatok létrehozása teszteléshez

A szimulálteszköz-alkalmazás egy az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és hőmérséklettel és páratartalommal kapcsolatos szimulált telemetriát küld.

1. Töltse le a C#-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip címről, és bontsa ki a ZIP-archívumot.

1. Egy helyi terminálablakban keresse meg a C#-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\simulated-device** mappába.

1. Nyissa meg a **SimulatedDevice.cs** fájlt egy Ön által választott szövegszerkesztőben.

    Cserélje le a `s_connectionString` változó értékét az eszköz kapcsolati karakterláncára [az eszköz regisztrálása az IoT Hub ra.](#register-a-device-to-the-iot-hub) Ezután mentse a **SimulatedDevice.cs** fájl módosításait.

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

Az alkalmazás generáló adatok, most már láthatja az adatfolyamot az IoT hub a fürt táblája.

1. Az Azure Portalon az IoT-központ, láthatja a tevékenység kiugrása az alkalmazás futása közben.

    ![IoT Hub-metrikák](media/ingest-data-iot-hub/iot-hub-metrics.png)

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
    
    ![Bevitt adatok eredményeinek megjelenítése](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Az Azure Data Explorer rendelkezik egy aggregációs (kötegelési) szabályzat adatbetöltési, célja, hogy optimalizálja a betöltési folyamat. A házirend alapértelmezés szerint 5 percre vagy 500 MB-ra van konfigurálva, így késésléphet. Az [batching policy](/azure/kusto/concepts/batchingpolicy) összesítési beállításokat a kötegelési házirendben láthatja. 
    > * Állítsa be a táblát, hogy támogassa a streamelést, és távolítsa el a válaszidő késését. Lásd a [streamelési házirendet.](/azure/kusto/concepts/streamingingestionpolicy) 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem tervezi, hogy újra használja az IoT Hubot, tisztítsa meg **a test-hub-rg-t**a költségek elkerülése érdekében.

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen, majd a létrehozott erőforráscsoport.  

    Ha a bal oldali menü össze van csukva, kattintson a ![Kibontás gombra](media/ingest-data-event-hub/expand.png) a kinyitásához.

   ![A törölni kívánt erőforráscsoport kiválasztása](media/ingest-data-event-hub/delete-resources-select.png)

1. A **test-resource-group** alatt válassza az **Erőforráscsoport törlése** elemet.

1. Az új ablakban írja be a törölni kívánt erőforráscsoport nevét (*test-hub-rg*), majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

* [Adatok lekérdezése az Azure Data Explorerben](web-query-data.md)
