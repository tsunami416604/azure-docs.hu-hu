---
title: Többrégiós üzemelő példányokhoz, az Azure Cosmos DB költségek optimalizálása
description: Ez a cikk bemutatja, hogyan kezelje költségeit a többrégiós üzemelő példányok az Azure Cosmos DB-ben.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: d5e10e04ea9567e2656f592407855a53a70d7b0f
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53263901"
---
# <a name="optimize-the-cost-for-multi-region-deployments-in-azure-cosmos-db"></a>Többrégiós üzemelő példányokhoz, az Azure Cosmos DB a költségek optimalizálása

Adja hozzá, és bármikor eltávolíthatja a régiók az Azure Cosmos-fiókjába. Az átviteli sebességet, a különböző Azure-Cosmos-adatbázis és a tárolók a fiókjához társított minden egyes régióban van fenntartva. Ha az átviteli sebességet, óránként, ez az összeg RU/s konfigurált összes adatbázisok és tárolók az Azure Cosmos-fiók `T` és az adatbázis-fiókhoz társított Azure-régiók száma `N`, majd az összes Cosmos-fiókja, egy adott órán keresztül kiosztott átviteli sebesség megegyezik:

1. ` T x N RU/s` Ha az Azure Cosmos-fiók egy egyetlen írási régió van konfigurálva. 

1. `T x (N+1) RU/s` Ha az Azure Cosmos-fiók van konfigurálva az összes régióban írások feldolgozására alkalmas állapotban. 

A költségeket $0.008 és óránként 100 RU/s kiosztott átviteli sebesség az egyetlen írási régió és írható több régióban a kiosztott átviteli sebesség költségek $0.016 / óránként és 100 RU/s. További tudnivalókért tekintse meg a Cosmos DB [díjszabási oldalunkon](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="costs-for-multiple-write-regions"></a>Több írási régiót költség

Több főkiszolgálós rendszerekben, a nettó elérhető RUs írási műveletek növekszik `N` where alkalommal `N` írási régiók száma. Egyetlen naplórekordjait, ellentétben minden régióban írhatóvá vált, és támogatnia kell a ütközésének feloldása. A írók számítási feladatok mennyisége nőtt. A tervezési szempontból végrehajtásához költsége` M` RU/s alatt az írások világszerte, kell rendelkezni M `RUs` egy tároló vagy az adatbázis szintjén. Hozzáadhatja például a, és használja őket, az írási műveletek végrehajtásához tetszőleges számú régiót `M` világszerte írások RU naplóban. 

### <a name="example"></a>Példa

Fontolja meg egy tárolót az USA nyugati RÉGIÓJA van 10 ezer Kérelemegység/s kiosztott átviteli sebesség és 1 TB-nyi adatot tárol ebben a hónapban. Tegyük fel, akkor adjon hozzá három régiókban – USA keleti RÉGIÓJA, Észak-Európa és Kelet-Ázsia, mindegyike ugyanazt a tárhelyet és átviteli sebességet, és szeretné, hogy a globálisan elosztott alkalmazás ahhoz, hogy az összes négy régióban írni. Havi számlájának végösszege (31 napos feltételezve) az adott hónapban a következőképpen történik:

|**Elem**|**Használat (havonta)**|**Arány**|**Havi költség**|
|----|----|----|----|
|Átviteli sebességre vonatkozó számla az USA nyugati RÉGIÓJA (több írási régió) lévő tároló esetén |10 ezer Kérelemegység/s * 24 * 31. |0.016 $ / 100 Kérelemegység/másodperc óránként |$1,190.40 |
|Átviteli sebességre vonatkozó számla 3 további régió – USA keleti RÉGIÓJA, Észak-Európa és Kelet-Ázsia (több írási régió) esetén |(3 + 1) * 10 ezer Kérelemegység/s * 24 * 31. |0.016 $ / 100 Kérelemegység/másodperc óránként |$4,761.60 |
|Adattárolásra vonatkozó számla az USA nyugati régiójában lévő tároló esetén |100 GB |$ 0,25/GB |25 USD |
|Adattárolásra vonatozó számla 3 további régió esetén – az USA keleti régiója, Észak-Európa, Kelet-Ázsia |3 * 1 TB |$ 0,25/GB |75 $ |
|**Összesen**|||**$6,052** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Átvitel kihasználtsága javíthatja a egy régió-alapon történik

Ha nem hatékony felhasználási, például egy vagy több kevésbé használt vagy túlterhelt felhasznált régiók, elvégezhető átvitel kihasználtsága javítása érdekében az alábbi lépéseket:  

1. Ellenőrizze, hogy először optimalizálása a kiosztott átviteli kapacitás (RU) az írási régió, és végezze el a Kérelemegységek maximális használatát az olvasási régióban az olvasási régiók etc a változáscsatorna használatával. 

2. Több írási régiót olvassa be, és írási műveletek skálázhatók az Azure Cosmos-fiókhoz társított összes régióban. 

3. A régióban tevékenységének figyelését, és sikerült hozzáadása és eltávolítása az olvasási és írási teljesítményt igény szerinti régiók.

## <a name="next-steps"></a>További lépések

Ezután folytassa további tudnivalók a költségek optimalizálása az Azure Cosmos DB az alábbi cikkeket:

* Tudjon meg többet [optimalizálása fejlesztéshez és teszteléshez](optimize-dev-test.md)
* Tudjon meg többet [az Azure Cosmos DB-elszámolások ismertetése](understand-your-bill.md)
* Tudjon meg többet [átviteli költségek optimalizálása](optimize-cost-throughput.md)
* Tudjon meg többet [tárolási költségek optimalizálása](optimize-cost-storage.md)
* Tudjon meg többet [olvasási és írási a költségek optimalizálása](optimize-cost-reads-writes.md)
* Tudjon meg többet [lekérdezések költségeinek optimalizálása](optimize-cost-queries.md)

