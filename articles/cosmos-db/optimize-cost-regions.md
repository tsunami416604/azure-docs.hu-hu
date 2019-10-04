---
title: A többrégiós üzemelő példányok díjainak optimalizálása Azure Cosmos DB
description: Ez a cikk azt ismerteti, hogyan kezelhető a többrégiós telepítések költségei a Azure Cosmos DBban.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rimman
ms.openlocfilehash: 233eab1fc49d7ce4cbb1e5b98b67eda9a64aa195
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667594"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Többrégiós díj optimalizálása Azure Cosmos DB

Bármikor hozzáadhat és eltávolíthat régiókat az Azure Cosmos-fiókjához. A különböző Azure Cosmos-adatbázisokhoz és-tárolóhoz konfigurált átviteli sebesség a fiókhoz társított minden régióban le van foglalva. Ha az óránként kiépített átviteli sebesség (ru/s) az Azure Cosmos-fiókhoz tartozó összes adatbázisban és tárolóban konfigurálva van `T` , és az adatbázis- `N`fiókhoz társított Azure-régiók száma, akkor a teljes a Cosmos-fiók kiépített átviteli sebessége egy adott órában egyenlő:

1. `T x N RU/s`Ha az Azure Cosmos-fiókja egyetlen írási régióval van konfigurálva. 

1. `T x (N+1) RU/s`Ha az Azure Cosmos-fiókja minden olyan régióval konfigurálva van, amely képes az írási műveletek feldolgozására. 

Az egyszeri írási régióval kiépített átviteli sebesség 0.008/óra/100 RU/s, a kiépített átviteli sebesség pedig több írható régióval együtt a 0.016/óra/óra 100 RU/s. További információt a Azure Cosmos DB díjszabását ismertető [oldalon](https://azure.microsoft.com/pricing/details/cosmos-db/)talál.

## <a name="costs-for-multiple-write-regions"></a>Több írási régió díja

A több főkiszolgálós rendszerekben az írási műveletekhez rendelkezésre álló, a `N` net számára `N` elérhető, az írási régiók száma növekszik. Az egyrégiós írásokkal ellentétben az egyes régiók már írhatók, és támogatniuk kell az ütközés feloldását. Az írók munkaterhelésének mennyisége nőtt. A költségmegtakarítás szempontjából, ha `M` ru/s értékre van szükség a globális írásokhoz, az M `RUs` -t egy tárolóban vagy adatbázis szintjén kell kiépíteni. Ezt követően annyi régiót adhat hozzá, amennyit csak szeretne, és ezeket az írásokhoz is `M` felhasználhatja a globális írási műveletek elvégzéséhez. 

### <a name="example"></a>Példa

Vegye figyelembe, hogy az USA nyugati régiójában a (z) 10K RU/s-vel lett kiépítve, és ebben a hónapban 1 TB-nyi adat van tárolva. Tegyük fel, hogy három régiót vesz fel – az USA keleti régiója, Észak-Európa és Kelet-Ázsia, amelyek mindegyike ugyanazzal a tárolóval és adatátvitelsel rendelkezik, és a globálisan elosztott alkalmazásokból mind a négy régióban szeretné írni a tárolókat. Havonta a havi számla (feltéve, hogy 31 nap) a következő:

|**Elem**|**Használat (havi)**|**Sebessége**|**Havi költség**|
|----|----|----|----|
|Adatátviteli számla az USA nyugati régiójában lévő tárolóhoz (több írási régió) |10K RU/s * 24 * 31 |$0,016/100 RU/s óránként |$1 190,40 |
|Adatátviteli számla 3 további régióhoz – az USA keleti régiója, Észak-Európa és Kelet-Ázsia (több írási régió) |(3 + 1) * 10K RU/s * 24 * 31 |$0,016/100 RU/s óránként |$4 761,60 |
|Adattárolásra vonatkozó számla az USA nyugati régiójában lévő tároló esetén |1 TB (vagy 1 024 GB) |0,25/GB |$256 |
|Adattárolásra vonatozó számla 3 további régió esetén – az USA keleti régiója, Észak-Európa, Kelet-Ázsia |3 * 1 TB (vagy 3 072 GB) |0,25/GB |$768 |
|**Teljes**|||**$6 976** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Az adatforgalom kihasználtságának növelése régiónként-alapon

Ha nem hatékony kihasználtságot használ, például egy vagy több, a használaton kívüli vagy túlhasznált régiót, a következő lépések végrehajtásával javíthatja az átviteli sebesség kihasználtságát:  

1. Győződjön meg arról, hogy először optimalizálja a kiépített átviteli sebességet (RUs) az írási régióban, majd az olvasási régiókban használja az RUs maximálisan megengedett használatát a beolvasási régióból stb. 

2. Több írási régió olvasása és írása is méretezhető az Azure Cosmos-fiókhoz társított összes régióban. 

3. A régiókban figyelheti a tevékenységeket, és igény szerint hozzáadhat és eltávolíthat régiókat az olvasási és írási sebesség méretezéséhez.

## <a name="next-steps"></a>További lépések

A következő cikkekben további tudnivalókat talál a Azure Cosmos DB a Cost optimizationról:

* További információ a [fejlesztés és a tesztelés optimalizálásáról](optimize-dev-test.md)
* További információ [a Azure Cosmos db-számla megismeréséről](understand-your-bill.md)
* További információ az [átviteli sebesség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási díjak optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási díjak optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések díjszabásának optimalizálásáról](optimize-cost-queries.md)

