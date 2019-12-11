---
title: Az Azure-ba való betöltéshez használjon egykattintásos betöltést Adatkezelő
description: Ismerje meg, hogyan tölthetők be (betöltési) adatai az Azure-ba Adatkezelő egyszerűen csak egy kattintással történő betöltéssel.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 0d7c8b2661ee3361b3a485b8cae4eef3a8225120
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975244"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Az Azure-ba való betöltéshez használjon egykattintásos betöltést Adatkezelő

Ebből a cikkből megtudhatja, hogyan használhatja az egykattintásos betöltést egy új tábla gyors betöltéséhez JSON-vagy CSV-formátumban. Az adatok menthetők a tárterületről vagy egy helyi fájlból egy meglévő táblába vagy egy új táblába. Használja az intuitív egykattintásos varázslót, és néhány percen belül betölti az adatait. Ezután szerkesztheti a táblázatot, és futtathat lekérdezéseket az Azure Adatkezelő webes felhasználói felületén keresztül.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Jelentkezzen be az [alkalmazásba](https://dataexplorer.azure.com/).
* Hozzon létre [egy Azure adatkezelő-fürtöt és-adatbázist](create-cluster-database-portal.md).
* Jelentkezzen be a [webes kezelőfelületre](https://dataexplorer.azure.com/) , és [vegyen fel egy kapcsolódást a fürthöz](/azure/data-explorer/web-query-data#add-clusters).
* Adatforrás létrehozása az Azure Storage-ban.

## <a name="ingest-new-data"></a>Új adatfeldolgozás

1. Kattintson a jobb gombbal a webes felhasználói felület bal oldali menüjében található *adatbázis* vagy *táblázat* sorra, és válassza az **új adatmennyiség (előzetes verzió)** lehetőséget.

    ![Válassza az egykattintásos betöltés lehetőséget a webes felhasználói felületen.](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. Az új adatok beolvasása **(előzetes verzió)** ablakban válassza ki a **forrás** fület, és fejezze be a **projekt részleteit**:

    * A **tábla**mezőben válasszon ki egy meglévő táblanév-nevet a legördülő menüből, vagy válassza az új létrehozása lehetőséget az új tábla **létrehozásához** .
    * A betöltés **típusa**beállításnál válassza **a tárolóból** vagy **a fájlból**lehetőséget.
        * Ha a **tárterület**lehetőséget választotta, válassza az URL-cím hozzáadásához a **tárolóhoz való csatolás** lehetőséget. A [blob sas URL-cím](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) használata a privát Storage-fiókokhoz. 
            * Ha a **fájlból**lehetőséget választotta, válassza a **Tallózás** lehetőséget, majd húzza a fájlt a mezőbe.
    * Válassza a **séma szerkesztése** lehetőséget a tábla oszlopai konfigurációjának megtekintéséhez és szerkesztéséhez.
 
    ![Egy kattintással történő betöltési forrás részletei.](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Ha az új adatok beolvasása **(előzetes verzió)** lehetőséget választja egy *táblázat* soraiban, a kiválasztott tábla neve megjelenik a **projekt részletei**között.

1. Ha kiválasztott egy meglévő táblázatot, megnyílik a **Térkép oszlopok** ablak, amely a forrás adatoszlopokat a céltábla oszlopaihoz rendeli. 
    * A **kihagyás oszlop** használatával távolíthatja el a kívánt oszlopot a táblából. 
    * Új **oszlop** hozzáadása új oszlophoz a táblához. 

    ![Oszlopok leképezése ablak](media/ingest-data-one-click/one-click-map-columns-window.png)

1. A **séma** lapon:

    * Válassza a **tömörítési típus** lehetőséget a legördülő menüből, majd válassza a **kitömörítve** vagy a **gzip**lehetőséget.
    * Válassza az **adatformátum** lehetőséget a legördülő menüből, majd válassza a **JSON**, **a CSV**, a **TSV**, a **SCSV**, a **SOHSV**, a **TSVE**vagy a **PSV**lehetőséget. 
        * A **JSON** formátum kiválasztásakor a **JSON-szintet**is ki kell választania 1 és 10 között. A szintek hatással vannak a táblázat oszlop adatábrázolására. 
        * Ha a JSON formátumtól eltérő formátumot választ, jelölje be a jelölőnégyzetet **is** , hogy a rendszer figyelmen kívül hagyja a fájl fejlécét.
    * A **leképezés neve** automatikusan be van állítva, de szerkeszthető is lehet.
    * Ha kiválasztott egy meglévő táblázatot, az **oszlopok leképezése** lehetőség kiválasztásával megnyithatja a **Térkép oszlopok** ablakot.

    ![Egykattintásos betöltési CSV-formátum sémája.](media/ingest-data-one-click/one-click-csv-format.png)

1. **A szerkesztő ablaktábla felett** kattintson a **v** gombra a szerkesztő megnyitásához. A szerkesztőben megtekintheti és átmásolhatja a bemenetek által generált automatikus lekérdezéseket. 

1.  A táblában: 
    * Kattintson a jobb gombbal az új oszlopfejlécek lehetőségre az **adattípus módosításához**, **oszlop átnevezéséhez**, **oszlop törléséhez**, **növekvő rendezéshez**vagy **Rendezés csökkenő**sorrendbe. A meglévő oszlopokban csak az Adatrendezés érhető el. 
    * Kattintson duplán az új oszlop nevére a szerkesztéshez.

1. Válassza a betöltés **indítása** lehetőséget a tábla és a leképezés létrehozásához, valamint az adatfeldolgozás megkezdéséhez.

    ![Egykattintásos betöltési JSON formátum sémája.](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Adatok lekérdezése

1. Az **adatfeldolgozás befejezve** ablakban mindhárom lépést zöld pipa jelöli, ha az adatfeldolgozás sikeresen befejeződött.
 
    ![Egy kattintással elvégezhető az adatfeldolgozás.](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. A lekérdezés megnyitásához kattintson a **v** gombra. Másolás a webes KEZELŐFELÜLETre a lekérdezés szerkesztéséhez.

1. A jobb oldali menüben a **gyors lekérdezések** és az **eszközök** beállításai láthatók. 

    * A **gyors lekérdezések** a webes kezelőfelületre mutató hivatkozásokat tartalmaznak, például lekérdezésekkel.
    * Az **eszközök** tartalmaz egy hivatkozást a webes felhasználói felületen a **parancsok eldobására** , ami lehetővé teszi a problémák megoldását a megfelelő `.drop` parancsok futtatásával.

    > [!TIP]
    > Előfordulhat, hogy `.drop` parancsok használatával megszakad az adatvesztés. Gondosan használja őket.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Adatkezelő webes felhasználói felületének lekérdezése](web-query-data.md)
* [Írási lekérdezések az Azure Adatkezelő Kusto lekérdezési nyelv használatával](write-queries.md)
