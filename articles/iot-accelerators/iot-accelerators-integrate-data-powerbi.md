---
title: Távoli figyelési adatmegjelenítés a Power BI használatával – Azure | Microsoft Docs
description: Ez az oktatóanyag Power BI Desktop és Cosmos DB használatával integrálja a távoli figyelési megoldásból származó adatok testreszabott vizualizációba való integrálását. Így a felhasználók saját egyéni irányítópultokat hozhatnak létre, és megoszthatják őket a megoldásban nem szereplő felhasználók számára.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 1a7d29583636879698abc598e1935358796b6062
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90976006"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Távoli figyelési adatmegjelenítés Power BI használatával

Ez az oktatóanyag végigvezeti a távoli figyelési megoldás adatainak a CosmosDB-ből Power BIba való csatlakoztatásának lépésein. Ezzel a szolgáltatással létrehozhatja saját egyéni irányítópultját, és visszahelyezheti őket a távoli figyelési megoldás irányítópultján. Ez a workstream lehetővé teszi, hogy több speciális gráfot hozzon létre a dobozon kívül is. Ezt az oktatóanyagot követve integrálhat más adatfolyamokkal, vagy létrehozhat olyan egyéni irányítópultokat, amelyeket a távoli figyelési megoldáson kívül is fel kell használni. Az irányítópultok Power BIban történő létrehozása azt jelenti, hogy az egyes paneleket az adott darabok kiválasztásával egymással is megteheti. Például rendelkezhet egy olyan szűrővel, amely csak a szimulált teherautókra vonatkozó információkat jeleníti meg, és az irányítópult minden egyes eleme úgy fog működni, hogy csak a szimulált tehergépkocsi-információkat jelenítse meg. Ha nem Power BI eszközt szeretne használni, kiterjesztheti ezeket a lépéseket a vizualizációs eszköz megválasztásához, illetve a Cosmos-adatbázishoz való csatlakozáshoz, illetve az egyéni adatbázishoz, ha beállított egyet. 

## <a name="prerequisites"></a>Előfeltételek

- A jelenleg futó távoli figyelési megoldásnak kell lennie
- A IoT Hub és a megoldás futtatásához hozzáféréssel kell rendelkeznie [Azure Portal](https://portal.azure.com) és az előfizetéséhez
- Telepítenie kell [Power bi Desktopot](https://powerbi.microsoft.com) , a bármely verziót


## <a name="information-needed-from-azure-portal"></a>A Azure Portal szükséges információk

1. Ha szükséges, navigáljon [Azure Portal](https://portal.azure.com) és jelentkezzen be

2. A bal oldali panelen kattintson az erőforráscsoportok elemre.

    ![Oldalsó panel NAV](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Navigáljon ahhoz az erőforráscsoporthoz, amelynek a IOT-megoldása fut, és kattintson az erőforráscsoport áttekintő oldalára. 

4. Az áttekintő lapon kattintson arra az elemre, amelynek típusa "Azure Cosmos DB fiók", ezt követően a IoT-megoldáshoz tartozó Cosmos DB stream áttekintés lapjára kerül.

    ![Erőforráscsoport](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. A bal oldali panelen kattintson a "Keys" (kulcsok) szakaszra, és jegyezze fel a következő értékeket a Power BI:

   - URI
   - Elsődleges kulcs

     ![Képernyőfelvétel: egy Azure Cosmos D B-fiók, amely a kijelölt kulcsok elemet tartalmazza.](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>A stream beállítása Power BI
  
1. Nyissa meg a Power BI asztali alkalmazást, és kattintson az "adatok lekérése" elemre a bal felső sarokban. 

    ![Adatok beolvasása](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Amikor a rendszer kéri az adatbevitelt, válassza a "Azure Cosmos DB" kifejezésre, és válassza ki ezt az összekötőt. Ez az összekötő lényegében az Azure IoT-megoldás Cosmos adatbázisából kéri le az adatok adatait
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Adja meg a fent rögzített adatokat:

    * URI
    * Elsődleges kulcs

4. Válassza ki az Power BIba importálandó összes táblát. Ez a művelet elindítja az betöltést. Minél hosszabb a megoldás, annál hosszabb ideig tart az adat betöltése (akár néhány óráig is). 

    ![Táblák importálása](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Miután az adatgyűjtés befejeződött, kattintson a "lekérdezések szerkesztése" gombra a Power BI felső sorában, és bontsa ki az összes táblát a sárga sávban található nyilakra kattintva. Ez lényegében kibontva jelenik meg az összes oszlop megjelenítéséhez. Megfigyelheti, hogy az egyes dolgok, például a páratartalom, a sebesség és a nem megfelelő típusú adatok.

    ![Új oszlop](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Például a Power BIba érkező adatforgalom UNIX-ra változott, amikor az összekötőn keresztül jött létre. Az átalakításhoz való alkalmazkodáshoz hozzon létre egy új oszlopot, és használja ezt az egyenletet a dátum-idő formátumba való beolvasásához: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Frissített táblázat](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document. Device. msg. Received csak az egyik oszlop UNIX formázással, és helyettesíthető másokkal, amelyeknek átalakításra van szükségük. 
  
    Más adatpontokat a string típusúra alakítottak át, és a fenti lépésekkel megegyező lépések használatával megváltoztathatók a páros vagy az int érték.

## <a name="creating-a-dashboard"></a>Irányítópult létrehozása

A stream csatlakoztatása után készen áll a személyre szabott irányítópultok létrehozására. Az alábbi irányítópulton látható egy példa arra, hogy a szimulált eszközök milyen módon bocsátják ki a telemetria, és különböző kimutatásokat jelenítenek meg, például a következőt: 

* Eszköz helye a térképen (jobb oldalon)
* Állapottal és súlyossággal rendelkező eszközök. (bal felső)
* Érvényben lévő szabályokkal rendelkező eszközök, és ha vannak riasztások kikapcsolva (alul balra)

![PowerBi vizualizáció](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Az irányítópult közzététele és az adatgyűjtés frissítése

Az irányítópultok sikeres létrehozása után javasoljuk, hogy [tegye közzé a Power bi-irányítópultokat](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) másokkal való megosztáshoz.

Emellett [frissítenie](https://docs.microsoft.com/power-bi/refresh-data) kell a közzétett irányítópulton lévőket is, hogy megbizonyosodjon arról, hogy rendelkezik a legújabb adatkészlettel.

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan jelenítheti meg a távoli monitorozási adatait a Power BI használatával

A távoli figyelési megoldás testreszabásával kapcsolatos további információkért lásd:

* [A távoli figyelési megoldás felhasználói felületének testreszabása](iot-accelerators-remote-monitoring-customize.md)
* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Fejlesztői hibaelhárítási útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

