---
title: Távoli figyelési adatok megjelenítése a Power BI használatával – Azure | Microsoft dokumentumok
description: Ez az oktatóanyag a Power BI Desktop és a Cosmos DB segítségével integrálja a távoli figyelési megoldásból származó adatokat egy testreszabott vizualizációba. Így a felhasználók létrehozhatják saját egyéni irányítópultjaikat, és megoszthatják azokat a nem a megoldáson lévő felhasználókkal.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 8b0f395936c8628ff7b8dab601168538f97ccdb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184246"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Távfigyelő adatok megjelenítése a Power BI használatával

Ez az oktatóanyag bemutatja, hogyan csatlakoztathatja a Távoli figyelési megoldás adatait a CosmosDB-ből a Power BI-ba. Ezzel a kapcsolattal létrehozva létrehozhatja saját egyéni irányítópultjait, és hozzáadhatja őket a távoli figyelési megoldás irányítópultjához. Ez a munkafolyam lehetővé teszi, hogy speciálisabb grafikonok jönnek létre, a dobozból kiállók mellett. Ezután használhatja ezt az oktatóanyagot más adatfolyamokkal való integrációhoz, vagy egyéni irányítópultokat hozhat létre a távoli figyelési megoldáson kívül. Az irányítópultok létrehozása a Power BI-ban azt jelenti, hogy az egyes paneleket is kapcsolatba hozhatja egymással, amikor kiválaszt bizonyos darabokat. Lehet például egy szűrője, amely csak a szimulált teherautókra vonatkozó információkat jeleníti meg, és az irányítópult minden egyes darabja kölcsönhatásba lép, hogy csak a szimulált teherautó-adatokat jelenítse meg. Ha a Power BI-tól eltérő eszközt szeretne használni, akkor ezeket a lépéseket is kiterjesztheti a kiválasztott vizualizációs eszköz használatára, és ha beállított egyet, a Cosmos-adatbázisba vagy az egyéni adatbázisba. 

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell egy távoli figyelési megoldással, amely jelenleg fut
- Hozzáféréssel kell rendelkeznie az [Azure Portalhoz](https://portal.azure.com) és az előfizetéséhez, amelyen az IoT Hub és a Solution fut
- Telepítve kell lennie a [Power BI asztali verziójának,](https://powerbi.microsoft.com) minden


## <a name="information-needed-from-azure-portal"></a>Az Azure Portalról szükséges információk

1. Navigálás az [Azure Portalra](https://portal.azure.com) és szükség esetén a bejelentkezéshez

2. A bal oldali panelen kattintson az Erőforráscsoportok elemre.

    ![Oldalsó panel nav](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Nyissa meg azt az erőforráscsoportot, amelyen az Iot-megoldás fut, és kattintson az erőforráscsoport áttekintése lapjára. 

4. Ezen az áttekintő lapon kattintson az elemre, amely "Azure Cosmos DB-fiók" típusú, majd az IoT-megoldás Cosmos DB-adatfolyam áttekintő lapjára kerül.

    ![Erőforráscsoport](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. A bal oldali panelen kattintson a "Kulcsok" szakaszra, és vegye figyelembe a Power BI-ban használandó alábbi értékeket:

   - URI
   - Elsődleges kulcs

     ![keys](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Az adatfolyam beállítása a Power BI-ban
  
1. Nyissa meg az asztali Power BI alkalmazást, és kattintson a bal felső sarokban az "Adatok bekéselése" gombra. 

    ![Adatok beolvasása](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Amikor a rendszer adatok bevitelére kéri, válassza az "Azure Cosmos DB" keresése lehetőséget, és válassza ki ezt az összekötőt. Ez az összekötő lényegében közvetlenül az Azure IoT-megoldás cosmos-adatbázisából származik.
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Adja meg a fent rögzített adatokat:

    * URI
    * Elsődleges kulcs

4. Jelölje ki a Power BI-ba importálandó összes táblát. Ez a művelet elindítja az adatok betöltését. Minél tovább fut a megoldás, annál tovább tarthat az adatok betöltése (akár néhány óra). 

    ![Táblák importálása](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Miután az adatok betöltése befejeződött, kattintson a Power BI felső sorában a "Lekérdezések szerkesztése" gombra, és bontsa ki az összes táblát az egyes táblák sárga sávján lévő nyilakra kattintva. Ez lényegében kibontva jeleníti meg az összes oszlopot. Észre fogja venni, hogy az adatok a dolgok, mint a páratartalom, a sebesség idő, stb nem a megfelelő típusú.

    ![Új oszlop](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    A Power BI-ba érkező adatok például UNIX-időre módosultak, amikor az összekötőn keresztül érkeztek. Az átalakításhoz való igazításhoz a jövőben új oszlopot hozhat létre, és ezzel az egyenlettel dátumidő-formátumba állíthatja be: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Frissített tábla](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    A Document.device.msg.received csak az egyik UNIX-formázással rendelkező oszlop, és helyettesíthető azokkal, amelyekátalakítást igényelnek. 
  
    A String típusúsá konvertált egyéb adatpontok at adott esetben párosvagy int-re lehet módosítani a fentiekben meghatározott lépésekkel.

## <a name="creating-a-dashboard"></a>Irányítópult létrehozása

Miután a patak csatlakoztatva van, készen áll a személyre szabott irányítópultok létrehozására! Az alábbi irányítópult egy példa arra, hogy a szimulált eszközeink által kibocsátott telemetriai adatokat, és különböző pivotokat jelenít meg körülötte, mint például: 

* Az eszköz helye a térképen (jobbra)
* Az állapotukkal és súlyosságukkal rendelkező eszközök. (bal felső sarok)
* A szabályokkal rendelkező eszközök, és ha vannak olyan riasztások, amelyek kivannak kapcsolva (bal alsó)

![PowerBi-képi megjelenítés](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Az irányítópult közzététele és az adatok frissítése

Miután sikeresen létrehozta az irányítópultokat, javasoljuk, hogy [tegye közzé a Power BI-irányítópultjait,](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) hogy másokkal megoszthassa őket.

Azt is szeretné [frissíteni az adatokat](https://docs.microsoft.com/power-bi/refresh-data) a közzétett irányítópulton, hogy megbizonyosodjon arról, hogy a legújabb adatkészletet.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtudhatja, hogyan jelenítheti meg a távoli figyelési adatokat a Power BI használatával

A távfigyelési megoldás testreszabásáról a következő témakörben talál további információt:

* [A távoli figyelési megoldás felhasználói felületének testreszabása](iot-accelerators-remote-monitoring-customize.md)
* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Fejlesztői hibaelhárítási útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

