---
title: A Power BI - Azure használatával távoli figyelési adatok megjelenítése |} Microsoft Docs
description: Ez az oktatóanyag a Power BI Desktop és használja Cosmos DB integerate adatok egy távoli figyelésére szolgáló megoldás a testreszabott képi megjelenítés be. A felhasználók a saját egyéni irányítópultok építhetnek és megosztásához azokat nem a megoldás a felhasználók számára.
services: iot-suite
suite: iot-suite
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 8c9807119519dd1f93f08d0366311619a2e793eb
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>A Power BI használatával távoli figyelési adatok megjelenítése

Ez az oktatóanyag végigvezeti beépülő modul távoli figyelési megoldást adatait a Power BI-bA CosmosDB módjáról. A a kapcsolat létrejött majd a saját egyéni irányítópultot létrehozni, szabadon adja hozzá a távoli figyelési megoldást irányítópult alakzatot vissza. A workstream konkrétabb diagramjait keletkezik, kívül, amelyekbe már nem teszi lehetővé. Ez az oktatóanyag integrálása más adatfolyamokat vagy egyéni irányítópultok számára a távoli figyelésére szolgáló megoldás kívül használhatja. A Power BI irányítópultok felépítése azt jelenti, hogy minden egyes panel egymással nehezen kiválasztásakor is tehet. Például lehet egy, a szimulált teherautók csak információkat jeleníti meg, szűrő, és minden egyes adatra az irányítópult volna módosításának csak szimulált teherautó információk megjelenítéséhez. Ha azt szeretné, csak a Power BI eszközök használata, ezeket a lépéseket a képi megjelenítés tetszőleges eszköz általi használatát, és ha létrehozott egy Cosmos-adatbázis, vagy egyéni adatbázis bekapcsolódhatnak is kiterjeszthető. 

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell egy távoli figyelésére szolgáló megoldás jelenleg fut.
- Hozzáféréssel kell rendelkeznie [Azure Portal](https://portal.azure.com) és az előfizetés, amelyen az IoT Hub és a megoldás fut
- Rendelkeznie kell [Power BI desktop](https://powerbi.microsoft.com) bármely verziójának fog tenni telepítve,


## <a name="information-needed-from-azure-portal"></a>Azure-portálon szükséges információk

1. Navigáljon a [Azure Portal](https://portal.azure.com) , és jelentkezzen be, szükség esetén

2. A bal oldali panelen kattintson a csoportok

    ![Ügyféloldali Panel Nav](media/iot-suite-remote-monitoring-powerbi/side_panel.png)

3. Keresse meg az erőforráscsoportot, amelyek az Iot-megoldásból fut, és kattintson adott erőforráscsoport – Áttekintés lapon kell fordítani. 

4. Az adott áttekintése lapon kattintson az elem, amelynek típusa "Azure Cosmos DB fiók", majd megnyílik a Cosmos DB adatfolyam – áttekintés oldalra, hogy az IoT-megoldás.

    ![Erőforráscsoport](media/iot-suite-remote-monitoring-powerbi/resource_groups.png)

5. A bal oldali panelen kattintson a "Kulcsok" szakaszban, és jegyezze fel a Power bi használhatók a következő értékeket:

    - URI
    - Elsődleges kulcs

    ![keys](media/iot-suite-remote-monitoring-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>A Power BI-adatfolyam beállítása
  
1. Nyissa meg a Power bi-ban egy asztali alkalmazás, és kattintson az "Adatok beolvasása" a bal felső sarkában. 

    ![Adatok beolvasása](media/iot-suite-remote-monitoring-powerbi/get_data.png)

2. Adatok megadását kéri, válassza a "Azure Cosmos DB" keresése, és válassza ezt az összekötőt. Ez az összekötő lényegében kéri le az adatokat közvetlenül a az Azure IoT-megoldásból cosmos adatbázisból
  
    ![Cosmos DB](media/iot-suite-remote-monitoring-powerbi/cosmos_db.png)
  
3. Adja meg az adatokat, amelyek fölött rögzített:

    * URI
    * Elsődleges kulcs

4. Válassza ki a Power BI-bA importálni kívánt összes tábla. Ez a művelet az adatok betöltésének indítsa lesz. Minél hosszabb a megoldás fut, a hosszabb ideig is igénybe vehet az adatok betöltése (legfeljebb néhány óra). 

    ![Tábla importálása](media/iot-suite-remote-monitoring-powerbi/import_tables.png)

5. Miután befejezte az adatok betöltését, kattintson a "Szerkesztés" a lekérdezésekben Power BI felső sorában, és bontsa ki a táblákat a nyilak a sárga sávon minden táblához kattintva. Ez lényegében bővített az összes oszlopot. Megfigyelheti, milyen műveleteket, mint a nedvességnek, sebesség idő stb. az adatok nincsenek a megfelelő típusú.

    ![Új oszlop](media/iot-suite-remote-monitoring-powerbi/new_column.png)
  
    Például a Power BI-bA származó adatokat UNIX idő, amikor érkezett az összekötő segítségével történő megváltozott. Az átalakításhoz beállításához módosítástól fogva lehet egy olyan új oszlop létrehozása és használni a egyenlet idő dátumformátumra alakítja: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Frissített tábla](media/iot-suite-remote-monitoring-powerbi/updated_table.png)
  
    Document.Device.msg.Received UNIX formázással oszlop csak egy, és másokkal átalakítás igénylő helyett. 
  
    Más adatpontok karakterlánc módosíthatja a Double típust vagy Int, ha a fentiek szerint lépések megfelelő használja ugyanazt lett konvertálva.

## <a name="creating-a-dashboard"></a>Irányítópult létrehozása

Az adatfolyam a csatlakozás után készen áll a testreszabott irányítópultok létrehozásával! Az alábbi irányítópult véve a telemetriai adatokat a szimulált eszköz immmited alatt, és különböző megjelenítő pivots köré, mint például: 

* Eszköz helyét a térképen (megfelelő)
* Az állapot és a súlyosság rendelkező eszközök. (bal felső)
* Eszközök szabályok, és ha bármely számukra (bal alsó) állapotra vált riasztást

![Power bi képi megjelenítés](media/iot-suite-remote-monitoring-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Közzététel az irányítópult és az Adatfrissítés

Miután sikeresen létrehozta az irányítópultok, azt javasoljuk, hogy Ön [közzététele a Power BI-irányítópult](https://docs.microsoft.com/en-us/power-bi/desktop-upload-desktop-files) másokkal.

Emellett érdemes [adatok frissítése](https://docs.microsoft.com/en-us/power-bi/refresh-data) a közzétett irányítópultján győződjön meg arról, hogy rendelkezik-e a legújabb adathalmaz.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, megjelenítheti a Power BI használatával távoli figyelési adatok kapcsolatos

A távoli felügyeleti megoldás testreszabásával kapcsolatos további információkért lásd:

* [A távoli figyelési megoldást igényelnek felhasználói felület testreszabása](iot-suite-remote-monitoring-customize.md)
* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Fejlesztői hibaelhárítási útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

