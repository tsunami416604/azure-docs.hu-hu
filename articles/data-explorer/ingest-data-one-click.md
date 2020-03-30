---
title: Az azure Data Explorer betöltéséhez egykattintásos betöltéssel
description: Ismerje meg, hogyan töltheti be (töltheti be) az adatokat az Azure Data Explorerbe egy kattintással történő betöltéssel.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 4a53f7e68501ce7f9b19dea0822d3896ec241fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444557"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Az azure Data Explorer betöltéséhez egykattintásos betöltéssel

Ez a cikk bemutatja, hogyan használhatja az egykattintásos betöltést egy új tábla JSON- vagy CSV-formátumban történő gyors betöltéséhez. Az adatok a tárolóból vagy egy helyi fájlból egy meglévő táblába vagy egy új táblába is bevihetők. Használja az intuitív egykattintásos varázslót, és néhány percen belül betöltése az adatokat. Ezután szerkesztheti a táblát, és lekérdezéseket futtathat az Azure Data Explorer webfelhasználói felületének használatával.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Jelentkezzen be [az alkalmazásba.](https://dataexplorer.azure.com/)
* Hozzon létre [egy Azure Data Explorer-fürtöt és -adatbázist.](create-cluster-database-portal.md)
* Jelentkezzen be a [webes felhasználói felületre,](https://dataexplorer.azure.com/) és [adjon hozzá egy kapcsolatot a fürthöz.](/azure/data-explorer/web-query-data#add-clusters)

## <a name="ingest-new-data"></a>Új adatok betöltése

1. Kattintson a jobb gombbal a webes felhasználói felület bal oldali menüjében lévő *adatbázisra* vagy táblasorra, és válassza **az Új adatok betöltése (előnézet) parancsot.** *table*

    ![Egyetlen kattintásos betöltés kijelölése a webes felhasználói felületen](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. Az **Új adatok betöltése (előnézet)** ablakban válassza a **Forrás** lapot, és fejezze be a **Projekt részletei :**

    * A **Táblázat csoportban**válasszon ki egy meglévő táblanevet a legördülő menüből, vagy válassza az **Új létrehozása lehetőséget** új tábla létrehozásához.
    * A **Betöltés típusa esetén**válassza a **raktárból** vagy **a fájlból lehetőséget.**
      * Ha a **tárolóból**lehetőséget választotta, válassza **a Hivatkozás a tárolóhoz** lehetőséget az URL-cím hozzáadásához. Blob [SAS URL-címe](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) magántárfiókokhoz. 
      * Ha **a fájlból**kijelölte a Lehetőséget, válassza a **Tallózás** lehetőséget, és húzza a fájlt a mezőbe.
    * A **séma szerkesztése** lehetőséget a táblázatoszlop konfigurációjának megtekintéséhez és szerkesztéséhez válassza.
 
    ![Egykattintásos betöltési forrás részletei](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Ha egy *táblázatsorban* az **Új adatok betöltése (előnézet)** lehetőséget választja, a kijelölt táblanév megjelenik a **Projekt részletei képernyőn.**

1. Ha meglévő táblát jelölt ki, megnyílik az **Oszlopok leképezése** ablak, amely a forrásadatoszlopokat céltábla-oszlopokhoz képezi. 
    * A Céloszlop eltávolítása a táblázatból a **Kihagyás oszlopmal.**
    * **Az Új oszlop mal** új oszlopot vehet fel a táblába.

    ![Oszlopok leképezése ablak](media/ingest-data-one-click/one-click-map-columns-window.png)

1. A **Séma** lapon:

    * Válassza a Legördülő menü **Tömörítés típusa parancsát,** majd válassza a **Tömörítetlen** vagy a **GZip lehetőséget.**
    * Válassza az **Adatformátum lehetőséget** a legördülő menüből, majd válassza a **JSON,** **CSV,** **TSV**, **SCSV**, **SOHSV**, **TSVE**vagy **PSV**lehetőséget. 
        * A **JSON** formátum kiválasztásakor a **JSON-szinteket**is ki kell választania 1 és 10 között. A szintek hatással vannak a táblaoszlop adatainak ábrázolására. 
        * Ha a JSON formátumtól eltérő formátumot választ, a fájl címsorsorának figyelmen kívül hagyásához jelölje be az **Oszlopnevek belefoglalása** jelölőnégyzetet.
    * **A leképezési név** beállítása automatikusan megtörténik, de szerkeszthető.
    * Ha meglévő táblázatot választott, az **Oszlopok leképezése** lehetőséget választva megnyithatja az **Oszlopok leképezése** ablakot.

    ![Egykattintásos csv formátumú séma](media/ingest-data-one-click/one-click-csv-format.png)

1. A **Szerkesztő** ablaktábla felett válassza a **v** gombot a szerkesztő megnyitásához. A szerkesztőben megtekintheti és másolhatja a bemenetekből generált automatikus lekérdezéseket. 

1. A táblázatban: 
    * Kattintson a jobb gombbal az új oszlopfejlécek elemre az **Adattípus módosítása**, **Az Oszlop átnevezése,** **az Oszlop törlése,** **a Növekvő rendezés**vagy a Csökkenő rendezés **elemre.** A meglévő oszlopokon csak az adatok rendezése érhető el. 
    * Kattintson duplán a szerkesztéshez az új oszlopnévre.

1. A tábla és a leképezés létrehozásához, valamint az adatok betöltésének megkezdéséhez válassza a **Betöltés megkezdése** lehetőséget.

    ![Egykattintásos betöltésI JSON formátumú séma](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Adatok lekérdezése

1. Az **Adatbetöltés befejeződött** ablakban mindhárom lépés zöld pipával lesz megjelölve, ha az adatok betöltése sikeresen befejeződik.
 
    ![Egykattintásos adatbetöltés kész](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. A lekérdezés megnyitásához kattintson a **v** gombra. Másolás a webes felhasználói felületre a lekérdezés szerkesztéséhez.

1. A jobb oldali menü **gyorslekérdezéseket** és **eszközök** beállításait tartalmazza. 

    * **A gyorslekérdezések** a webes felhasználói felületre mutató lekérdezésekkel rendelkező hivatkozásokat tartalmaznak.
    * **Az eszközök** a webes felhasználói felületen található **Drop parancsokra** mutató hivatkozást tartalmaznak, amely lehetővé teszi a problémák elhárítását a megfelelő `.drop` parancsok futtatásával.

    > [!TIP]
    > Parancsok használatakor `.drop` adatok veszhetnek el. Óvatosan használja őket.

## <a name="next-steps"></a>További lépések

* [Adatok lekérdezése az Azure Data Explorer webes felhasználói felületén](web-query-data.md)
* [Lekérdezések írása az Azure Data Explorer hez a Kusto lekérdezési nyelv használatával](write-queries.md)
