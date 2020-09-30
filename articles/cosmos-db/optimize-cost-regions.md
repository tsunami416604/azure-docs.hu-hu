---
title: A többrégiós üzemelő példányok díjainak optimalizálása Azure Cosmos DB
description: Ez a cikk azt ismerteti, hogyan kezelhető a többrégiós telepítések költségei a Azure Cosmos DBban.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 8d98c9a7e58f08d9ad63183805cd6cd0d2ab3b3d
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570164"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Több régióban való üzemeltetés költségeinek optimalizálása az Azure Cosmos DB-ben

Bármikor hozzáadhat és eltávolíthat régiókat az Azure Cosmos-fiókjához. A különböző Azure Cosmos-adatbázisokhoz és-tárolóhoz konfigurált átviteli sebesség a fiókhoz társított minden régióban le van foglalva. Ha az óránként kiépített átviteli sebesség az Azure Cosmos-fiók összes adatbázisán és tárolóján konfigurált RU/s összege, `T` valamint az adatbázis-fiókhoz társított Azure-régiók száma `N` , akkor a Cosmos-fiók teljes kiosztott átviteli sebessége az adott órában egyenlő:

1. `T x N RU/s` Ha az Azure Cosmos-fiókja egyetlen írási régióval van konfigurálva. 

1. `T x (N+1) RU/s` Ha az Azure Cosmos-fiókja minden olyan régióval konfigurálva van, amely képes az írási műveletek feldolgozására. 

A kiosztott átviteli sebesség költsége egy írási régió esetén: 0,008 USD/óra másodpercenként 100 kérelemegységre vonatkozóan; a kiosztott átviteli sebesség költsége több írható régió esetén: 0,016 USD/óra másodpercenként 100 kérelemegységre vonatkozóan. További információt a Azure Cosmos DB [díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/cosmos-db/)talál.

## <a name="costs-for-multiple-write-regions"></a>Több írási régió díja

A többrégiós írások esetében a rendelkezésre álló, írási műveletekhez elérhető net RUs az `N` `N` írási régiók számát is növeli. Az egyrégiós írásokkal ellentétben az egyes régiók már írhatók, és támogatniuk kell az ütközés feloldását. Az írók munkaterhelésének mennyisége nőtt. A költségmegtakarítás szempontjából, ha `M` ru/s értékre van szükség a globális írásokhoz, az M-t `RUs` egy tárolóban vagy adatbázis szintjén kell kiépíteni. Ezt követően annyi régiót adhat hozzá, amennyit csak szeretne, és ezeket az írásokhoz is felhasználhatja a globális írási műveletek elvégzéséhez `M` . 

### <a name="example"></a>Példa

Vegye figyelembe, hogy az USA nyugati régiójában a (z) 10K RU/s-vel lett kiépítve, és ebben a hónapban 1 TB-nyi adat van tárolva. Tegyük fel, hogy három régiót vesz fel – az USA keleti régiója, Észak-Európa és Kelet-Ázsia, amelyek mindegyike ugyanazzal a tárolóval és adatátvitelsel rendelkezik, és a globálisan elosztott alkalmazásokból mind a négy régióban szeretné írni a tárolókat. Havonta a havi számla (feltéve, hogy 31 nap) a következő:

|**Elem**|**Használat (havi)**|**Sebesség**|**Havi költség**|
|----|----|----|----|
|Adatátviteli számla az USA nyugati régiójában lévő tárolóhoz (több írási régió) |10K RU/s * 24 * 31 |$0,016/100 RU/s óránként |$1 190,40 |
|Adatátviteli számla 3 további régióhoz – az USA keleti régiója, Észak-Európa és Kelet-Ázsia (több írási régió) |(3 + 1) * 10K RU/s * 24 * 31 |$0,016/100 RU/s óránként |$4 761,60 |
|Storage-számla az USA nyugati régiójában lévő tárolóhoz |1 TB (vagy 1 024 GB) |0,25/GB |$256 |
|Storage-számla 3 további régióhoz – az USA keleti régiója, Észak-Európa és Kelet-Ázsia |3 * 1 TB (vagy 3 072 GB) |0,25/GB |$768 |
|**Összesen**|||**$6 976** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Az adatforgalom kihasználtságának növelése régiónként-alapon

Ha nem hatékony kihasználtságot használ, például egy vagy több, a használaton kívüli vagy túlhasznált régiót, a következő lépések végrehajtásával javíthatja az átviteli sebesség kihasználtságát:  

1. Győződjön meg arról, hogy először optimalizálja a kiépített átviteli sebességet (RUs) az írási régióban, majd az olvasási régiókban használja az RUs maximálisan megengedett használatát a beolvasási régióból stb. 

2. Több írási régió olvasása és írása is méretezhető az Azure Cosmos-fiókhoz társított összes régióban. 

3. A régiókban figyelheti a tevékenységeket, és igény szerint hozzáadhat és eltávolíthat régiókat az olvasási és írási sebesség méretezéséhez.

## <a name="next-steps"></a>Következő lépések

A következő cikkekben további tudnivalókat talál a Azure Cosmos DB a Cost optimizationról:

* További információ a [fejlesztés és a tesztelés optimalizálásáról](optimize-dev-test.md)
* További információ [a Azure Cosmos db-számla megismeréséről](understand-your-bill.md)
* További információ az [átviteli sebesség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási díjak optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási díjak optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések díjszabásának optimalizálásáról](optimize-cost-queries.md)

