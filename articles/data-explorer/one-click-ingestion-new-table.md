---
title: Egykattintásos betöltés használata az adatok új Azure Data Explorer-táblába való betöltéséhez
description: Adatok betöltése (betöltése) egy új Azure Data Explorer-táblába egyszerűen, egy kattintással történő betöltéssel.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 1e337a46d1d8ddda1b07bde6f12d4c1f7feda42f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549221"
---
# <a name="use-one-click-ingestion-to-ingest-data-to-a-new-table-in-azure-data-explorer"></a>Egykattintásos betöltés használata adatok új táblába való betöltéséhez az Azure Data Explorerben

Az egykattintásos betöltés lehetővé teszi, hogy gyorsan betöltése adatok JSON, CSV és más formátumokban egy táblázatba. Az Azure Data Explorer webes felhasználói felületének használatával adatokat betöltése a tárolóból, egy helyi fájlból vagy egy tárolóból. 

Ez a dokumentum az intuitív egykattintásos varázsló használatával ismerteti a CSV-adatok betöltését egy tárolóból egy új táblába. Ezután szerkesztheti a táblát, és lekérdezéseket futtathat az Azure Data Explorer webfelhasználói felületén. Azt is beállíthatja, hogy a folyamatos betöltés automatikusan beolvassa az adatokat a táblába, amikor a forrásadatok frissülnek.

Az egykattintásos betöltés különösen akkor hasznos, ha először ad be adatokat, vagy ha az adatok sémája ismeretlen az Ön számára. 

Az egykattintásos betöltés ről és az előfeltételek listájáról az [Egykattintásos betöltés](ingest-data-one-click.md)című témakörben olvashat.
Az Azure Data Explorer meglévő táblájába történő adatbetöltéséről az [Egy kattintásos betöltés egy meglévő táblába](one-click-ingestion-existing-table.md) című témakörben talál további információt.

## <a name="ingest-new-data"></a>Új adatok betöltése

1. A webes felhasználói felület bal oldali menüjében kattintson a jobb gombbal egy *adatbázisra,* és válassza **az Új adatok betöltése (előnézet) parancsot.**

    ![Egyetlen kattintásos betöltés kijelölése a webes felhasználói felületen](media/one-click-ingestion-new-table/one-click-ingestion-in-web-ui.png)   
 
1. Az **Új adatok betöltése (előnézet)** ablakban a **Forrás** lap automatikusan ki van jelölve. 

1. Válassza **az Új tábla létrehozása lehetőséget,** és adja meg az új tábla nevét. Használhat alfanumerikus, kötőjeleket és aláhúzásjeleket. A speciális karakterek nem támogatottak.

![Új tábla létrehozása](media/one-click-ingestion-new-table/create-new-table.png) 

[!INCLUDE [data-explorer-one-click-ingestion-types](../../includes/data-explorer-one-click-ingestion-types.md)]

A **séma szerkesztése** lehetőséget a táblázatoszlop konfigurációjának megtekintéséhez és szerkesztéséhez válassza. A rendszer véletlenszerűen kiválasztja a blobok egyikét, és a séma az adott blob alapján jön létre. A forrás nevének megvizsgálásával a szolgáltatás automatikusan azonosítja, hogy tömörített-e vagy sem.

## <a name="edit-the-schema"></a>A séma szerkesztése

1. A **Séma** lapon:

    1. **Adatformátum**kiválasztása:

        [!INCLUDE [data-explorer-one-click-ingestion-edit-schema](../../includes/data-explorer-one-click-ingestion-edit-schema.md)]

    1. Ha a **JSON lehetőséget választja,** a **JSON-szinteket**is 1-től 10-ig kell választania. A szintek hatással vannak a táblaoszlop adatainak ábrázolására. 

    ![JSON-szintek kiválasztása](media/one-click-ingestion-new-table/json-levels.png)

    * Ha a JSON formátumtól eltérő formátumot választ, az **Oszlopnevek belefoglalása** jelölőnégyzet bejelölésével figyelmen kívül hagyhatja a fájl címsorsoránakat.

        ![Oszlopnevek belefoglalása jelölőnégyzetet.](media/one-click-ingestion-new-table/non-json-format.png)
        
1. A **Leképezés neve** mezőbe írjon be egy leképezésnevet. Alfanumerikus karaktereket és aláhúzásjeleket is használhat. A szóközök, a speciális karakterek és a kötőjelek nem támogatottak.
    
    ![Táblaleképezés neve](media/one-click-ingestion-new-table/table-mapping.png)

## <a name="copy-and-paste-queries"></a>Lekérdezések másolása és beillesztése

1. A **Szerkesztő** ablaktábla felett válassza a **v** gombot a szerkesztő megnyitásához. A szerkesztőben megtekintheti és másolhatja a bemenetekből generált automatikus lekérdezéseket. 
1. A táblázatban: 
    * Kattintson duplán a szerkesztéshez az új oszlopnévre.
    * Jelöljön ki új oszlopfejléceket, és tegye az alábbiak egyikét:
    
|Műveletek         |Leírás                                  |
|-----------------|-------------------------------------------|
|Adattípus módosítása |Az adattípus módosítása a szolgáltatás által automatikusan kiválasztott adattípusról a többi [támogatott adattípus](#edit-the-schema) egyikére|
|Oszlop átnevezése    |Az oszlop nevének módosítása |
|Új oszlop       |Új oszlop hozzáadása|
|Oszlop törlése    |A kijelölt oszlop törlése|
|Rendezés növekvő sorrendbe   |A táblázat rendezése a kijelölt oszlop szerint növekvő sorrendben (csak meglévő oszlopok esetén)|
|Rendezés csökkenő sorrendbe  |A táblázat rendezése a kijelölt oszlop szerint csökkenő sorrendben (csak meglévő oszlopok esetén) |

> [!Note]
> Táblázatos formátumok esetén minden oszlop egyetlen oszlopba használható az Azure Data Explorerben.
> Különböző JSON-szintekről hozhat létre új oszlopokat.

[![](media/one-click-ingestion-new-table/edit-view.png "One-click ingestion edit view")](media/one-click-ingestion-new-table/edit-view.png#lightbox) 

## <a name="start-ingestion"></a>A bevétel megkezdése

A tábla és a leképezés létrehozásához, valamint az adatok betöltésének megkezdéséhez válassza a **Betöltés megkezdése** lehetőséget.
![A bevétel megkezdése](media/one-click-ingestion-new-table/start-ingestion.png)

## <a name="data-ingestion-completed"></a>Az adatok betöltése befejeződött

Az **Adatbetöltés befejeződött** ablakban mindhárom lépés zöld pipával lesz megjelölve, ha az adatok betöltése sikeresen befejeződik.
 
![Egykattintásos adatbetöltés kész](media/one-click-ingestion-new-table/one-click-data-ingestion-complete.png)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](../../includes/data-explorer-one-click-ingestion-query-data.md)]

**Folyamatos lenyelés**

A folyamatos betöltés lehetővé teszi, hogy hozzon létre egy eseményrácsot, amely figyeli a forrástárolót. Minden olyan új blob, amely megfelel az előre meghatározott paraméterek (előtag, utótag és így tovább) feltételeinek, automatikusan bekerül a céltáblába.

> [!Note]
> A folyamatos lenyelés csak akkor fontos, ha egy tartályból kerül be.

1. Válassza **a Folyamatos betöltés lehetőséget** az Azure Portal megnyitásához. Az adatkapcsolat lap megnyílik az eseményrács adatösszekötő nyitott és a forrás- és célparaméterek már megadva (forrástároló, táblák és leképezések).

1. Válassza **a Létrehozás** lehetőséget, ha olyan adatkapcsolatot szeretne létrehozni, amely figyeli a tárolótárolóban bekövetkező módosításokat. 

## <a name="next-steps"></a>További lépések

* [Adatok lekérdezése az Azure Data Explorer webes felhasználói felületén](/azure/data-explorer/web-query-data)
* [Lekérdezések írása az Azure Data Explorer hez a Kusto lekérdezési nyelv használatával](/azure/data-explorer/write-queries)
