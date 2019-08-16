---
title: Az Azure-ba való betöltéshez használjon egykattintásos betöltést Adatkezelő
description: Ismerje meg, hogyan tölthetők be (betöltési) adatai az Azure-ba Adatkezelő egyszerűen csak egy kattintással történő betöltéssel.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: b1ce2d9efe44021b4e3191739bd2f922e34c44cb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520048"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Az Azure-ba való betöltéshez használjon egykattintásos betöltést Adatkezelő

Ez a cikk bemutatja, hogyan használható az egykattintásos betöltés a JSON-vagy CSV-formátumú új tábla gyors betöltéséhez a Storage szolgáltatásból az Azure Adatkezelőba. Az adatgyűjtés után szerkesztheti a táblázatot, és futtathatja a lekérdezéseket a webes KEZELŐFELÜLET használatával.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Jelentkezzen be az [alkalmazásba](https://dataexplorer.azure.com/).
* [Azure adatkezelő-fürt és-adatbázis](create-cluster-database-portal.md) létrehozása
* Az Azure Storage-ban tárolt adatforrások.

## <a name="ingest-new-data"></a>Új adatfeldolgozás

1. Kattintson a jobb gombbal az *adatbázis nevére* , és válassza az **új adatfeldolgozás (előzetes verzió) lehetőséget.**

    ![a webes felhasználói felületen kattintson a Betöltés gombra.](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. Az **adatfeldolgozás (előzetes verzió)** ablakban a **forrás** lapon adja meg a **projekt részleteit**:

    * Adja meg az új **Táblanév nevet**. 
    * Válassza > ki a betöltési típust**a tárolóból**.
    * Adja meg a Storage-hoz tartozó URL-cím hozzáadása **tárolóra mutató hivatkozást** . A blob SAS URL-cím használata a privát Storage-fiókokhoz. 
    * Válassza a **séma szerkesztése** lehetőséget.
 
    ![egy kattintás a betöltési forrás részleteit](media/ingest-data-one-click/one-click-ingestion-source.png) 

1. A **séma** lapon válassza ki az adatformátum elemet a legördülő > **JSON** -vagy **CSV**- **fájlból** . 
   
   **CSV**-fájl kiválasztása esetén:
    * Jelölje be a jelölőnégyzet **figyelmen kívül hagyása** a CSV-fájl fejlécének figyelmen kívül hagyása jelölőnégyzetet.    
    * A **leképezés neve** automatikusan be van állítva, de szerkeszthető is lehet.

    ![egy kattintással betöltés CSV formátum Schema. png](media/ingest-data-one-click/one-click-csv-format.png)

   Ha a **JSON**-t választja:
    * **JSON-szintek**kiválasztása: 1-10 a legördülő menüből. A JSON-fájl szintjei a jobb alsó sarokban látható táblázatban jelennek meg. 
    * A **leképezés neve** automatikusan be van állítva, de szerkeszthető is lehet.

    ![egy kattintással betöltési JSON formátum sémája](media/ingest-data-one-click/one-click-json-format.png)  

1. Aszerkesztőben válassza a jobb oldali **V** -t a szerkesztő megnyitásához. A szerkesztőben megtekintheti és átmásolhatja a bemenetek által generált automatikus lekérdezéseket. 

1.  A lenti táblázatban a jobb oldalon: 
    * Az oszlop jobb oldalán válassza a **V** lehetőséget az oszlop **átnevezéséhez**, az **oszlop törléséhez**, a **növekvő rendezéshez**vagy a **csökkenő rendezéshez** .
    * Kattintson duplán az oszlop nevére a szerkesztéshez.
    * Válassza ki az oszlop neve bal oldalán található ikont az adattípus módosításához. 

1. Válassza a betöltés **indítása** lehetőséget a tábla létrehozásához, a leképezés létrehozásához és az adatfeldolgozáshoz.
 
## <a name="query-data"></a>Adatok lekérdezése

1. Az **adatfeldolgozás befejezve** ablakban mindhárom lépés zöld pipa színnel lesz megjelölve, ha az adatfeldolgozás sikeresen befejeződött. 
 
    ![Egy kattintásos adatfeldolgozás kész](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. A lekérdezés megnyitásához válassza a **V** lehetőséget. Másolás a webes KEZELŐFELÜLETre a lekérdezés szerkesztéséhez.

1. A jobb oldali menü **gyors lekérdezéseket** és **eszközöket**tartalmaz. 

    * A **gyors lekérdezések** a webes kezelőfelületre mutató hivatkozásokat tartalmaznak, például lekérdezésekkel.
    * Az **eszközök** a webes felhasználói felületre mutató hivatkozásokat tartalmaznak a **drop parancsokkal** , amelyek lehetővé teszik `.drop` a problémák megoldását a megfelelő parancs futtatásával.

    > [!TIP]
    > Előfordulhat, hogy a `.drop` parancsokkal az adatai elvesznek. Gondosan használja őket.

## <a name="next-steps"></a>További lépések

* [Az Azure Adatkezelő webes felhasználói felületének lekérdezése](web-query-data.md)
* [Írási lekérdezések az Azure Adatkezelő Kusto lekérdezési nyelv használatával](write-queries.md)
