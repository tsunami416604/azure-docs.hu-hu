---
title: Távoli figyelési adatok megjelenítése használatával a Power bi-ban – Azure |} A Microsoft Docs
description: Ebben az oktatóanyagban a Power BI Desktop és a Cosmos DB integerate adatok távoli figyelési megoldást, egy egyéni vizualizációt. Így a felhasználók is a saját egyéni irányítópultok létrehozása és megosztása őket, ne a megoldás a felhasználók számára.
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 9daf0f0d1c7ca94308bfb0d894366b457a045770
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470647"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>A Power BI segítségével történő távoli megfigyelési adatok megjelenítése

Ez az oktatóanyag végigvezeti a távoli figyelési megoldás adatok a Power BI-bA CosmosDB beépülő modul hogyan. Ez a kapcsolat létrejött is majd a saját egyéni irányítópultokat hozhat létre, és adja hozzá őket vissza arra a távoli figyelési megoldás irányítópultján. Ez workstream lehetővé teszi a speciális diagramjait létrehozni, beépített kívül. Ebben az oktatóanyagban használhatja integrálása más adatfolyamokat, vagy a távoli figyelési megoldás kívül fogják egyéni irányítópultokat készíthet majd. A Power bi Szolgáltatásban irányítópultokat készít, az azt jelenti, hogy minden egyes egymással kapcsolatot tarthatnak egyes funkciódarabokat kiválasztása panel is teheti. Például lehet egy szűrőt, amely megjeleníti a szimulált látható információk csak adatait, és az irányítópult egyes adathordozónevek lenne használhatják csak szimulált teherautó információk megjelenítéséhez. Ha szeretné a Power BI eltérő eszközzel, ezeket a lépéseket, a tetszőleges vizualizációs eszköz használatát, és az, Cosmos-adatbázis, vagy egyéni adatbázis környezet igénybe vételét, ha már beállította egy is kiterjesztheti. 

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell a távoli figyelési megoldás jelenleg fut.
- Hozzáféréssel kell rendelkeznie [az Azure Portal](https://portal.azure.com) és az előfizetés, amelyre az IoT Hub és a megoldás futtatása
- Rendelkeznie kell [Power BI desktop](https://powerbi.microsoft.com) telepítve van, bármely verziója újrabuildelése


## <a name="information-needed-from-azure-portal"></a>Az Azure Portalról szükséges információk

1. Navigáljon a [az Azure Portal](https://portal.azure.com) , és jelentkezzen be szükség esetén

2. A bal oldali panelen kattintson az erőforráscsoportok

    ![Panel navigációs oldal](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Keresse meg az erőforráscsoportot, amelynek az Iot-megoldás fut-e, és kattintson az adott erőforráscsoportba tartozó – áttekintés oldalra kell venni. 

4. Az adott áttekintése oldalon kattintson az elem, amelynek típusa "Az Azure Cosmos DB-fiók", majd megnyílik a Cosmos DB-adatfolyam áttekintés oldalán, az adott IoT-megoldás.

    ![Erőforráscsoport](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. A bal oldali panelen kattintson a "Kulcsok" szakaszt, és jegyezze fel a következő értékeket a Power bi-ban használható:

    - URI
    - Elsődleges kulcs

    ![keys](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>A Power bi-ban a Stream beállítása
  
1. Nyissa meg a Power BI asztali alkalmazás, és kattintson az "Adatok lekérése" a bal felső sarokban. 

    ![Adatok lekérése](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Ha a rendszer kéri, adja meg az adatokat, válassza az "Azure Cosmos DB" Search, és válassza ki a összekötőt. Ez az összekötő lényegében közvetlenül az Azure IoT-megoldások a cosmos-adatbázis az adatokat kér le
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Adja meg az adatokat, amelyek a fenti rögzített:

    * URI
    * Elsődleges kulcs

4. Válassza ki a Power BI-bA importálni kívánt összes tábla. Ez a művelet elindít az adatok betöltését. A megoldás minél hosszabb ideje fut, a hosszabb ideig is igénybe vehet az adatok betöltéséhez (akár néhány órát). 

    ![Tábla importálása](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Miután befejeződött az adatok betöltése, kattintson a "Lekérdezések szerkesztése" a Power BI a felső sorba, és bontsa ki az összes táblát a sárga sáv minden egyes nyilakra kattintva. Ez lényegében bővített az összes oszlopot. Megfigyelheti, hogyan többek között az páratartalom, idő csökkentéséhez, stb. az adatok nem megfelelő típusú állnak.

    ![Új oszlop](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Például a Power BI-bA érkező adatok módosítva lett, mikor, honnan az összekötőn keresztül UNIX-ideje. Az átalakításhoz beállításához továbbítja azt is egy olyan új oszlop létrehozása és használata a egyenlőségi kaphat a dátum-idő formátuma: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Frissíteni a táblát](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document.Device.msg.Received csak az egyik az oszlopok UNIX formázásával és helyett az átalakítás igénylő másokkal. 
  
    Egyéb adatpontok átalakítottunk karakterlánc is módosítható Double típusú értékekkel, típus vagy Int, ahol megfelelő lépésekkel a fentiek szerint.

## <a name="creating-a-dashboard"></a>Irányítópult létrehozása

A stream a csatlakozás után készen áll a személyre szabott irányítópultokat hozhat létre! Az irányítópult az alábbi egy példa a telemetriát a szimulált eszközök, és különböző szűkítése ábrázoló körülötte például alatt kibocsátott állapotba: 

* Eszköz helyét egy térképen (jobb oldali)
* Állapot és súlyosság eszközök. (bal felső sarokban)
* Eszközök szabályokkal helyen, és ha vannak olyan riasztások kezdeti időszak után őket (bal alsó)

![A Power bi-Vizualizáció](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Az irányítópult közzététele, és az adatok frissítése

Miután sikeresen létrehozta az irányítópultokon, azt javasoljuk, hogy Ön [közzététele a Power BI-irányítópultok](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) másokkal való megosztásához.

Is érdemes [frissítheti az adatokat](https://docs.microsoft.com/power-bi/refresh-data) , győződjön meg arról, hogy rendelkezik-e a legújabb adatkészlet, a közzétett irányítópultján.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan jelenítheti meg a távoli figyelési adatok a Power BI használatával kapcsolatos

A távoli figyelési megoldás testreszabásával kapcsolatos további információkért lásd:

* [A távoli figyelési megoldás felhasználói felület testreszabása](iot-accelerators-remote-monitoring-customize.md)
* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Fejlesztői hibaelhárítási útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

