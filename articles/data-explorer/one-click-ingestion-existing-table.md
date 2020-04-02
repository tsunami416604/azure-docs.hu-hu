---
title: Egykattintásos betöltés használata az adatok meglévő Azure Data Explorer-táblába történő betöltéséhez
description: Adatok betöltése (betöltése) egy meglévő Azure Data Explorer-táblába egyszerűen, egy kattintással történő betöltéssel.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: a7fdab66394c132bcd9134669b841d178c559f28
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546210"
---
# <a name="use-one-click-ingestion-to-ingest-data-to-an-existing-table-in-azure-data-explorer"></a>Egykattintásos betöltés használata az adatok meglévő táblába való betöltéséhez az Azure Data Explorerben

Az egykattintásos betöltés lehetővé teszi, hogy gyorsan betöltése adatok JSON, CSV és más formátumokban egy táblázatba. Az Azure Data Explorer webes felhasználói felületének használatával adatokat betöltése a tárolóból, egy helyi fájlból vagy egy tárolóból. 

Ez a dokumentum az intuitív egykattintásos varázsló használatával írja le a JSON-adatok fájlból egy meglévő táblába való betöltését. Ezután szerkesztheti a táblát, és lekérdezéseket futtathat az Azure Data Explorer webfelhasználói felületén.

Az egykattintásos betöltés különösen akkor hasznos, ha először ad be adatokat, vagy ha az adatok sémája ismeretlen az Ön számára. 

Az egykattintásos betöltés ről és az előfeltételek listájáról az [Egykattintásos betöltés](ingest-data-one-click.md)című témakörben olvashat.
Az adatok új táblába való betöltéséről az Azure Data Explorerben című témakörben talál további információt [az Egykattintásos betöltés egy új táblába című témakörben.](one-click-ingestion-new-table.md)

## <a name="ingest-new-data"></a>Új adatok betöltése

1. A webes felhasználói felület bal oldali menüjében kattintson a jobb gombbal egy *adatbázisra* vagy *táblára,* és válassza **az Új adatok betöltése (előnézet) parancsot.**

    ![Egyetlen kattintásos betöltés kijelölése a webes felhasználói felületen](media/one-click-ingestion-existing-table/one-click-ingestion-in-webui.png)   
 
1. Az **Új adatok betöltése (előnézet)** ablakban a **Forrás** lap automatikusan ki van jelölve.

1. Ha a **Tábla** mező nincs automatikusan kitöltve, válasszon egy meglévő táblanevet a legördülő menüből.
    > [!TIP]
    > Ha egy *táblázatsorban* az **Új adatok betöltése (előnézet)** lehetőséget választja, a kijelölt táblanév megjelenik a **Projekt részletei képernyőn.**

[!INCLUDE [data-explorer-one-click-ingestion-types](../../includes/data-explorer-one-click-ingestion-types.md)]
    
A **séma szerkesztése** lehetőséget a táblázatoszlop konfigurációjának megtekintéséhez és szerkesztéséhez válassza.

## <a name="edit-the-schema"></a>A séma szerkesztése

1. Megnyílik **az Oszlopok leképezése** párbeszédpanel, és a forrásadatok oszlopait leképezheti a céltábla-oszlopokhoz. 
    * A **Forrás oszlopok** mezőkben adja meg a **Cél oszlopokkal**leképezendő oszlopneveket.
    * A leképezés törléséhez jelölje ki a kuka ikont.

    ![Oszlopok leképezése ablak](media/one-click-ingestion-existing-table/map-columns.png)

1. Válassza a **Frissítés** lehetőséget.
1. A **Séma** lapon:
    1. Válassza **a Tömörítés típusa**lehetőséget, majd válassza a **Tömörítetlen** vagy a **GZip lehetőséget.**

        [!INCLUDE [data-explorer-one-click-ingestion-edit-schema](../../includes/data-explorer-one-click-ingestion-edit-schema.md)]
        
    1. Ha a **JSON lehetőséget választja,** a **JSON-szinteket**is 1-től 10-ig kell választania. A szintek hatással vannak a táblaoszlop adatainak ábrázolására.

    ![JSON-szintek kiválasztása](media/one-click-ingestion-existing-table/json-levels.png)

    * Ha a JSON formátumtól eltérő formátumot választ, az **Oszlopnevek belefoglalása** jelölőnégyzet bejelölésével figyelmen kívül hagyhatja a fájl címsorsoránakat.
        
    ![Oszlopnevek belefoglalása jelölőnégyzetet.](media/one-click-ingestion-existing-table/non-json-format.png)

    > [!Note]
    > Táblázatos formátumok betöltése oszlopadatok at csak egy oszlop egy Azure Data Explorer-táblázatban. 

    * Az új hozzárendelések beállítása automatikusan megtörténik, de módosíthatja, hogy egy meglévőt használjon. 
    * Az **Oszlopok leképezése** lehetőséget választva megnyithatja az **Oszlopok leképezése** ablakot.

## <a name="copy-and-paste-queries"></a>Lekérdezések másolása és beillesztése

1. A **Szerkesztő** ablaktábla felett válassza a **v** gombot a szerkesztő megnyitásához. A szerkesztőben megtekintheti és másolhatja a bemenetekből létrehozott automatikus parancsokat. 
1. A táblázatban: 
    * Új oszlopfejlécek kijelölése **Új oszlop,** **Oszlop törlése,** **Növekvő rendezés**vagy Csökkenő **rendezés hozzáadása.** A meglévő oszlopokon csak az adatok rendezése érhető el.

    > [!Note]
    > * A meglévő táblák neve és adattípusa nem frissíthető.
    > * A legördülő parancsok csak a betöltési folyamat (új kiterjedések és oszlopok) által végrehajtott módosításokat fogják visszaállítani. Semmi mást nem fognak eldobni.

[![](media/one-click-ingestion-existing-table/edit-view.png "One-click ingestion edit view")](media/one-click-ingestion-existing-table/edit-view.png#lightbox) 

## <a name="start-ingestion"></a>A bevétel megkezdése

A tábla és a leképezés létrehozásához, valamint az adatok betöltésének megkezdéséhez válassza a **Betöltés megkezdése** lehetőséget.

![A bevétel megkezdése](media/one-click-ingestion-existing-table/start-ingestion.png)

## <a name="data-ingestion-completed"></a>Az adatok betöltése befejeződött

Az **Adatbetöltés befejeződött** ablakban mindhárom lépés zöld pipával lesz megjelölve, ha az adatok betöltése sikeresen befejeződik.
 
![Egykattintásos adatbetöltés kész](media/one-click-ingestion-existing-table/one-click-data-ingestion-complete.png)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](../../includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="next-steps"></a>További lépések

* [Adatok lekérdezése az Azure Data Explorer webes felhasználói felületén](/azure/data-explorer/web-query-data)
* [Lekérdezések írása az Azure Data Explorer hez a Kusto lekérdezési nyelv használatával](/azure/data-explorer/write-queries)
