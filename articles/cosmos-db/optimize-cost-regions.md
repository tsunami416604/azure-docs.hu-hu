---
title: A több régióra kiterjedő telepítések költségeinek optimalizálása az Azure Cosmos DB-ben
description: Ez a cikk bemutatja, hogyan kezelheti a több régióra kiterjedő központi telepítések költségeit az Azure Cosmos DB-ben.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: e0a24b52c12bce6a8e016a926dfa64a1e36a7cc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72753319"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Több régióban való üzemeltetés költségeinek optimalizálása az Azure Cosmos DB-ben

Az Azure Cosmos-fiók régióit bármikor hozzáadhatja és eltávolíthatja. A különböző Azure Cosmos-adatbázisokhoz és -tárolókhoz konfigurált átviteli hang a fiókhoz társított minden régióban le van foglalva. Ha az óránként kiosztott átviteli forgalom, azaz az Azure Cosmos-fiók összes adatbázisában és `T` tárolóján konfigurált RU/s összege, `N`és az adatbázis-fiókhoz társított Azure-régiók száma, akkor a Cosmos-fiók teljes kiosztott átviteli forgalma egy adott órában egyenlő:

1. `T x N RU/s`ha az Azure Cosmos-fiók egyetlen írási régióval van konfigurálva. 

1. `T x (N+1) RU/s`ha az Azure Cosmos-fiók konfigurálva van az összes olyan régióval, amely képes az írások feldolgozására. 

A kiosztott átviteli sebesség költsége egy írási régió esetén: 0,008 USD/óra másodpercenként 100 kérelemegységre vonatkozóan; a kiosztott átviteli sebesség költsége több írható régió esetén: 0,016 USD/óra másodpercenként 100 kérelemegységre vonatkozóan. További információ: Azure Cosmos DB [Pricing page](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="costs-for-multiple-write-regions"></a>Több írási régió költségei

Többfős rendszerben az írási műveletekhez rendelkezésre álló `N` nettó `N` rúmiák megnőnek, ahol az írási régiók száma. Az egyrégiós írásoktól eltérően minden régió írható, és támogatnia kell az ütközések feloldását. Az írók munkaterhelése nőtt. A költségtervezési szempontból, a `M` RU/s értékű írások világszerte, ki `RUs` kell építenie m egy tároló vagy adatbázis szintjén. Ezután annyi régiót adhat hozzá, amennyit csak szeretne, `M` és használhatja őket az írások hoz végre RU értékű világszerte írások. 

### <a name="example"></a>Példa

Vegye figyelembe, hogy van egy konténer az USA nyugati részén kiépített átviteli 10K RU/s átviteli és tárolja 1 TB-os adatok ebben a hónapban. Tegyük fel, hogy három régiót ad hozzá - USA keleti régióját, Észak-Európát és Kelet-Ázsiát, amelyek mindegyike azonos tárhellyel és átviteli sebességű, és szeretné, hogy a globálisan elosztott alkalmazásból mind a négy régióban írhass a tárolókba. A teljes havi számla (31 napot feltételezve) egy hónapban a következő:

|**Elem**|**Használat (havi)**|**Rate (Egységár)**|**Havi költség**|
|----|----|----|----|
|Átviteli cím a tárolóhoz az USA nyugati régiójában (több írási régió) |10K RU/s 24 31 |$0.016/100 RU/s/óra |$1,190.40 |
|Átviteli számla további 3 régióra - USA keleti régióra, Észak-Európára és Kelet-Ázsiára (több írási régió) |(3 + 1) 10 K RU/s 24 * 31 |$0.016/100 RU/s/óra |$4,761.60 |
|Tárolószámla az USA nyugati részén |1 TB (vagy 1024 GB) |$0,25/GB |256 $ |
|További 3 régió – USA keleti régió, Észak-Európa és Kelet-Ázsia – tárolási számlája |3 * 1 TB (vagy 3072 GB) |$0,25/GB |$768 |
|**Összesen**|||**$6,976** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Az átviteli teljesítmény kihasználásának javítása régiónként

Ha nem hatékony kihasználtsa, például egy vagy több kihasználatlan vagy túlhasznált terület, a következő lépéseket teheti az átviteli teljesítmény kihasználásának javítására:  

1. Győződjön meg arról, hogy először optimalizálja a kiosztott átviteli értéket (RUs) az írási régióban, majd a rúnák maximális kihasználását az olvasási régiókban az olvasási terület módosítási hírcsatornájának használatával stb. 

2. Több írási régió olvasási és írási skálázási skálázhatók az Azure Cosmos-fiókkal társított összes régióban. 

3. Figyelje a tevékenységet a régiókban, és igény szerint hozzáadhat és eltávolíthat régiókat az olvasási és írási átviteli szint méretezéséhez.

## <a name="next-steps"></a>További lépések

Ezután az alábbi cikkekkel többet tudhat meg a költségoptimalizálásról az Azure Cosmos DB-ben:

* További információ [az optimalizálásról fejlesztési és tesztelési célokra](optimize-dev-test.md)
* További információ [az Azure Cosmos DB-számlájának ismertetéséről](understand-your-bill.md)
* További információ az [átviteli költség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási költség optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási költségek optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések költségének optimalizálásáról](optimize-cost-queries.md)

