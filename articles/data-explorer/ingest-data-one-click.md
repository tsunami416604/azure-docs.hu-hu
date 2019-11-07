---
title: Az Azure-ba való betöltéshez használjon egykattintásos betöltést Adatkezelő
description: Ismerje meg, hogyan tölthetők be (betöltési) adatai az Azure-ba Adatkezelő egyszerűen csak egy kattintással történő betöltéssel.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 98598a28e14dfd8175cbb019ff1b001c65503580
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73644600"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Az Azure-ba való betöltéshez használjon egykattintásos betöltést Adatkezelő

Ez a cikk bemutatja, hogyan használható az egykattintásos betöltés a JSON-vagy CSV-formátumú új tábla gyors betöltéséhez a Storage szolgáltatásból az Azure Adatkezelőba. Az adatgyűjtés után szerkesztheti a táblázatot, és futtathatja a lekérdezéseket a webes KEZELŐFELÜLET használatával.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Jelentkezzen be az [alkalmazásba](https://dataexplorer.azure.com/).
* [Azure adatkezelő-fürt és-adatbázis](create-cluster-database-portal.md) létrehozása
* Jelentkezzen be a [webes kezelőfelületre](https://dataexplorer.azure.com/) , és [vegyen fel egy kapcsolódást a fürthöz](/azure/data-explorer/web-query-data#add-clusters)
* Az Azure Storage-ban tárolt adatforrások.

## <a name="ingest-new-data"></a>Új adatfeldolgozás

1. Kattintson a jobb gombbal az *adatbázisra* vagy a *táblázat* sorra a webes felhasználói felület bal oldali menüjében, és válassza az **új adatmennyiség (előzetes verzió) lehetőséget.**

    ![a webes felhasználói felületen kattintson a Betöltés gombra.](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. Az új adatok beolvasása **(előzetes verzió)** ablakban a **forrás** lapon hajtsa végre a **projekt részleteit**:

    * **Tábla**: válassza a létező táblanév lehetőséget a legördülő listából, vagy válassza az új létrehozása lehetőséget az új tábla **létrehozásához** .
    * Válassza ki a betöltési **típust** > **Storage** -ból vagy **fájlból**.
        * Ha a **Storage-ból**lehetőséget választotta, adja meg a Storage- **ra mutató hivatkozást** , hogy hozzáadja az URL-címet a tárolóhoz. A [blob sas URL-cím](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) használata a privát Storage-fiókokhoz. 
        * Ha a **fájlból**lehetőséget választotta, válassza a **Tallózás** lehetőséget, majd húzza a fájlt a mezőbe.
    * Válassza a **séma szerkesztése** lehetőséget a tábla oszlopai konfigurációjának megtekintéséhez és szerkesztéséhez.
 
    ![egy kattintás a betöltési forrás részleteit](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Ha az új adatok beolvasása **(előzetes verzió)** lehetőséget választja egy *táblázat* soraiban, a kiválasztott tábla neve megjelenik a **projekt részletei**között.

1. Ha kiválasztott egy meglévő táblázatot, megnyílik a **Térkép oszlopok** ablak, amely a forrás adatoszlopokat a céltábla oszlopaihoz rendeli. 
    * A **kihagyás oszlop** használatával távolíthatja el a kívánt oszlopot a táblából. 
    * Új **oszlop** hozzáadása egy új oszlophoz a táblához. 

    ![Oszlopok leképezése ablak](media/ingest-data-one-click/one-click-map-columns-window.png)

1. A **séma** lapon:

    * Válassza ki a **tömörítési típust** a legördülő > **tömörítetlen** vagy **gzip**.
    * Válassza ki az **adatformátumot** a legördülő > **JSON**-, **CSV**-, **TSV**-, **SCSV**-, **SOHSV**-, **TSVE**-vagy a **PSV**-ből. 
        * Ha a **JSON** -formátumot választja, válassza a **JSON-szintek**: 1-10 lehetőséget. A szintek hatással vannak a táblázat oszlop adatábrázolására. 
        * Ha a JSON formátumtól eltérő formátumot választ, jelölje be a jelölőnégyzetet, hogy az **oszlopnevek** figyelmen kívül hagyja a fájl fejlécét.    
    * A **leképezés neve** automatikusan be van állítva, de szerkeszthető is lehet.
    * Ha egy meglévő táblázatot jelölt ki, akkor az **oszlopok leképezése** gombra kattintva megnyithatja a **Térkép oszlopok** ablakot.

    ![egy kattintással betöltés CSV formátum Schema. png](media/ingest-data-one-click/one-click-csv-format.png)

1. A **szerkesztőben**válassza a jobb oldali **V** lehetőséget a szerkesztő megnyitásához. A szerkesztőben megtekintheti és átmásolhatja a bemenetek által generált automatikus lekérdezéseket. 

1.  A táblában: 
    * Kattintson a jobb gombbal az új oszlopfejlécek lehetőségre az **adattípus módosításához**, **oszlop átnevezéséhez**, **oszlop törléséhez**, **növekvő rendezéshez**vagy **Rendezés csökkenő**sorrendbe. A meglévő oszlopokban csak az Adatrendezés érhető el. 
    * Kattintson duplán az új oszlop nevére a szerkesztéshez.

1. Válassza a betöltés **indítása** lehetőséget a tábla létrehozásához, a leképezés létrehozásához és az adatfeldolgozáshoz.

    ![egy kattintással betöltési JSON formátum sémája](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Adatok lekérdezése

1. Az **adatfeldolgozás befejezve** ablakban mindhárom lépés zöld pipa színnel lesz megjelölve, ha az adatfeldolgozás sikeresen befejeződött. 
 
    ![Egy kattintásos adatfeldolgozás kész](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. A lekérdezés megnyitásához válassza a **V** lehetőséget. Másolás a webes KEZELŐFELÜLETre a lekérdezés szerkesztéséhez.

1. A jobb oldali menü **gyors lekérdezéseket** és **eszközöket**tartalmaz. 

    * A **gyors lekérdezések** a webes kezelőfelületre mutató hivatkozásokat tartalmaznak, például lekérdezésekkel.
    * Az **eszközök** a webes felhasználói felületre mutató hivatkozásokat tartalmaznak olyan **drop parancsokkal** , amelyek lehetővé teszik a problémák megoldását a megfelelő `.drop` parancs futtatásával.

    > [!TIP]
    > Előfordulhat, hogy `.drop` parancsok használatával elvesznek az adatai. Gondosan használja őket.

## <a name="next-steps"></a>További lépések

* [Az Azure Adatkezelő webes felhasználói felületének lekérdezése](web-query-data.md)
* [Írási lekérdezések az Azure Adatkezelő Kusto lekérdezési nyelv használatával](write-queries.md)
