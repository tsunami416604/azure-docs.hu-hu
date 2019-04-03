---
title: Az Azure Cosmos DB-elszámolások ismertetése
description: Ez a cikk bemutatja, hogyan lehet néhány példa az Azure Cosmos DB-hez kapcsolódó számlák magyarázata.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: d3bfe1b54409fd57f7535bac2362dc7040975061
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877635"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Az Azure Cosmos DB-elszámolások ismertetése

Teljes körűen felügyelt felhőbeli natív adatbázis-szolgáltatás az Azure Cosmos DB leegyszerűsíti a számlázás, csak a kiosztott átviteli sebesség díjszabási, és a felhasznált tárolási. Nincsenek további díjat, hardver, segédprogram költségeit, vagy létesítmény költségek a helyszíni vagy IaaS által üzemeltetett alternatívák képest. Meghatározásakor a több régióban képességek az Azure Cosmos DB, az adatbázis-szolgáltatást biztosít a költségek a meglévő helyszíni vagy IaaS-megoldások képest jelentős csökkentését.

Az Azure Cosmos DB számlázása óraszám alapján, a kiosztott átviteli sebesség és a felhasznált tárterület mérete. Az a kiosztott átviteli sebesség, a számlázási egység óránként 100 RU/s, díja $0.008 óránként, feltéve, hogy a szabványos nyilvános díjszabás, tekintse meg a [díjszabási oldalunkon](https://azure.microsoft.com/pricing/details/cosmos-db/). A felhasznált tárterület mérete, a számlázott $0,25 tárolási havonta 1 GB-onkénti, tekintse meg a [díjszabási oldalunkon](https://azure.microsoft.com/pricing/details/cosmos-db/). 

Ebben a cikkben példák segítségével megismerheti a részleteket, akkor jelenik meg a havi számla. A példákban szemléltetett módon számok eltérő, ha az Azure Cosmos-tárolók egy átviteli sebesség kiosztott részéért, ha több régióban span, illetve az egy másik futnak adott időszakban több mint egy hónapja különböző méretű lehet.

## <a name="billing-examples"></a>Számlázási példák

### <a name="billing-example---throughput-on-a-container-full-month"></a>A számlázás Példa - átviteli sebességet egy tárolón (teljes hónap)

* Tegyük fel, a tároló konfigurálja egy 1000 RU/s átviteli sebesség, és 24 óra * 30 napig hónapban létezik = teljes 720 óra.  

* 1000 RU/s a tárolók meglétének minden órája óránként 100 RU/s 10 egységet (azaz 1000/100 = 10). 

* (/ 100 RU/s / óra) 0.008 $ költsége meg kell szorozni 10 egység / óra = meg 0,08 / óra. 

* A 0,08 által a hónap órák száma óránként egységárat egyenlő $0,08 * 24 óra * 30 nap = $57.60 hónapban.  

* A teljes havi számlán a 7200 egységét (100 Kérelemegységenként), $57.60 díjait.

### <a name="billing-example---throughput-on-a-container-partial-month"></a>A számlázás Példa - átviteli sebességet egy tárolón (hónap)

* Tegyük fel, hogy hozzon létre egy tárolót, a 2500 RU/s kiosztott átviteli sebesség. A tároló lakik a hónapban 24 órán keresztül (például, hogy törölné hozunk létre, azt követő 24 órában).  

* 600 egységek a számlán láthatjuk (2500 RU/s / 100 RU/mp/egység * 24 óra). A költség lesz $4.80 (600 egység * 0.008 $/ egység).

* Teljes kiszámlázott díja a havi $4.80 lesz.

### <a name="billing-rate-if-storage-size-changes"></a>Óradíj tárméret módosításakor

A tárolási kapacitás elszámolási egysége a tárolt adatok havi időszakban (GB-ban) mért maximális óránkénti mennyisége. Ha például a hónap első felében 100 GB tárhelyet használt fel, a hónap második felében pedig 50 GB-ot, akkor az adott hónapban 75 GB adattárolásnak megfelelő használati díjat számítunk fel.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Ha a tároló vagy egy tárolók aktívak egy óránál rövidebb ideig díjszabása

Ön díjszabása alapján, átalánydíjjal történik, a tároló vagy az adatbázis üzemeltetéséért óradíjat, függetlenül attól, hogy a használat, vagy hogy a tároló vagy az adatbázis egy óránál rövidebb ideig. Például ha egy tároló vagy az adatbázis létrehozásához, és 5 perc múlva törli azt, a számla tartalmazza egy óra.

### <a name="billing-rate-when-throughput-on-a-container-or-database-scales-updown"></a>Óradíj, amikor egy tároló vagy az adatbázis átviteli sebességet méretezhető felfelé és lefelé

Kor 9:30 400 RU/s 1000 RU/s kiosztott átviteli sebesség növelése és majd csökkentheti, 10:45-kor vissza a 400 RU/s kiosztott átviteli sebesség, akkor 1000 RU/s két órányi díjat. 

Ha, 9:30-tól 100-K RU/s a 200-K RU/s és alacsonyabb, majd kiosztott átviteli sebesség, 10:45-kor vissza a 100-K RU/s kiosztott átviteli sebesség egy tárolót vagy tárolók növeli, akkor 200 K RU/s két órányi használatát díjat kell fizetnie.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>A számlázás példa: több dedikált kiosztott átviteli üzemmódban tároló

* Ha létrehoz egy Azure Cosmos fiókot az USA keleti régiójában két tárolóval 500 RU/s és 700 RU/s kiosztott átviteli sebesség 2 törléseknek megfelelő, egy teljes kiosztott átviteli kapacitás 1200 RU/s kell.  

* Számlázunk 1200/100 * $0.008 = 0,096 USD / óra. 

* Ha az átviteli sebességet módosítania kell, és létrehoz egy új korlátlan tárolót 20 000 RU/s az egyes tárolók kapacitását 500 RU/s már elsajátította, a teljes kihasznált kapacitás lenne 22.200 RU/s (1000 RU/s + 1200 RU/s + 20, 000RU/mp).  

* A számla a következőre módosul: $0.008 x 222 = 1.776 $/ óra. 

* 720 óra (24 óra * 30 nap), ha 500 órán keresztül kiosztott átviteli sebesség egy hónapnyi 1200 RU/s volt, és a fennmaradó 220 órán keresztül kiosztott átviteli sebesség volt 22.200 RU/s teljesítménnyel, a havi számla összegét mutatja: 500 x 0,096 USD / óra + 220 x $1.776 / óra = 438.72 $/ hó.

![A dedikált átviteli sebességet számlázási példa](./media/understand-your-bill/bill-example1.png)

### <a name="billing-example-containers-with-shared-throughput-mode"></a>A számlázás a példában: tárolók megosztott átviteli mód

* Ha egy Azure Cosmos-fiókot az USA keleti RÉGIÓJA 2, a két Azure Cosmos-adatbázis (más-tárolók, az átviteli sebességet, az adatbázis szintjén megosztása) rendre 50-K RU/s és 70-K RU/s kiosztott átviteli hoz létre, akkor kell kiosztott összesen 120 ezer RU/s átviteli sebesség.  

* 1200-as díja x $0.008 = $9.60 / óra. 

* Ha az átviteli sebességet módosítania kell, és növeli a minden egyes adatbázis kiosztott átviteli sebesség 10 K RU/s minden egyes adatbázishoz, és ad hozzá egy új tárolót az első adatbázis a dedikált átviteli mód 15-K RU/s a megosztott átviteli-adatbázissal a teljes kihasznált kapacitás lenne 155-K RU/s (60 K RU/s + 80 K RU/s + 15 K RU/s).  

* A számla a következőre módosul: 1,550 * $0.008 = $12.40 / óra.  

* Az adott hónapban 720 óra Ha 300 órát a kiosztott átviteli sebesség 120-K RU/s volt, és a fennmaradó 420 órán keresztül kiosztott átviteli sebesség 155-K RU/s volt a havi számlán: 300 x $9.60 / óra + 420 x $12.40 / óra = $2,880 + $5,208 = $8,088 / hó. 

![Átviteli sebesség számlázási példa megosztott](./media/understand-your-bill/bill-example2.png)

## <a name="billing-examples-with-geo-replication-and-multi-master"></a>A georeplikáció és több főkiszolgálós számlázási példák  

Ön hozzáadhat/eltávolíthat a világ bármely pontján található Azure-régiók, a Azure Cosmos DB-fiókot bármikor. Az átviteli sebességet, hogy konfigurálta a különböző Azure Cosmos DB-adatbázisok és tárolók az Azure Cosmos database fiókhoz társított Azure-régiók mindegyikében fogja lefoglalni. Ha konfigurálta a kiosztott átviteli kapacitás (RU/s) összege az adatbázisok és a tárolók az Azure Cosmos Database fiók (kiosztott / óra) T pedig az adatbázis-fiókhoz társított Azure-régiók száma N, majd az összes adott órában, az Azure Cosmos T x N RU/s és (b) az összes régióban írások feldolgozására alkalmas állapotban konfigurálva adatbázis-fiókot (a) konfigurált egy egyetlen írási régió megegyezik a kiosztott átviteli sebesség (N + 1) x T egyenlő RU/s , illetve. A költségeket $0.008 és óránként 100 RU/s kiosztott átviteli sebesség (egyetlen írási régió), és a kiosztott átviteli sebesség több írható régióval (több főkiszolgálós konfiguráció) költségek $0.016 / óránként és 100 RU/s (lásd a [díjszabási oldalunkon](https://azure.microsoft.com/pricing/details/cosmos-db/)). Az egyetlen régióban, vagy több írási régiót írni, hogy az Azure Cosmos DB lehetővé teszi bármelyik régióban adatokat olvasni.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>A számlázás példa: több régióban az Azure Cosmos-fiók, egyetlen régióban ír

Tegyük fel, hogy egy Azure Cosmos-tárolóhoz, az USA nyugati RÉGIÓJA. A tároló létrejön az átviteli sebesség 10 K RU/s, és 1 TB adat ebben a hónapban tárolására. Tegyük fel, adjon hozzá három régióban (USA keleti RÉGIÓJA, Észak-Európában és Kelet-Ázsia) az Azure Cosmos-fiókjába, mindegyik ilyen tárolási és átviteli sebesség. Havi számlájának végösszege fog kell (feltéve, hogy az adott hónapban 30 nap). A számla a következőképpen nézne ki: 

|**Elem** |**Felhasználás (havi)** |**Rate (Egységár)** |**Havi költség** |
|---------|---------|---------|-------|
|Átviteli sebességre vonatkozó számla az USA nyugati régiójában lévő tároló esetén      | 10 K RU/s * 24 * 30    |0.008 $ / 100 RU/s / óra   |$576|
|Átviteli sebességre vonatkozó számla 3 további régió esetén – az USA keleti régiója, Észak-Európa, Kelet-Ázsia       | 3 * 10K RU/s * 24 * 30    |0.008 $ / 100 RU/s / óra  |$1,728|
|Adattárolásra vonatkozó számla az USA nyugati régiójában lévő tároló esetén      | 250 GB    |$ 0,25/GB  |$62.50|
|Adattárolásra vonatozó számla 3 további régió esetén – az USA keleti régiója, Észak-Európa, Kelet-Ázsia      | 3 * 250 GB    |$ 0,25/GB  |$187.50|
|**Összes**     |     |  |**$2,554**|

*Azt is feltételezzük, hogy kimenő forgalmat bonyolít, 100 GB adat minden hónapban replikálja az adatokat az USA keleti RÉGIÓJA, Észak-Európa és Kelet-Ázsia, USA nyugati Régiójában lévő tárolóban. A számlázás kimenő adatátviteli megfelelően.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>A számlázás példa: több régióban az Azure Cosmos-fiók, több régióban ír

Tegyük fel, az USA nyugati Régiójában egy Azure Cosmos-tárolót hoz létre. A tároló létrejön az átviteli sebesség 10 K RU/s, és 1 TB adat ebben a hónapban tárolására. Tegyük fel, adjon hozzá három régióban (USA keleti RÉGIÓJA, Észak-Európa és Kelet-Ázsia) mindegyike ugyanazt a tárhelyet és átviteli sebességet, és szeretné írni, minden régióban az Azure Cosmos-fiókhoz társított tárolókat. Havi számlájának végösszege fog kell (feltéve, hogy az adott hónapban 30 nap) a következő:

|**Elem** |**Felhasználás (havi)**|**Rate (Egységár)** |**Havi költség** |
|---------|---------|---------|-------|
|Átviteli sebességre vonatkozó számla az USA nyugati RÉGIÓJA (az összes régióban is írható) lévő tároló esetén       | 10 K RU/s * 24 * 30    |0.016 $ / 100 RU/s / óra    |$1,152 |
|Átviteli sebességre vonatkozó számla 3 további régió – USA keleti RÉGIÓJA, Észak-Európa és Kelet-Ázsia (az összes régióban is írható) esetén        | (3 + 1) * 10 K RU/s * 24 * 30    |0.016 $ / 100 RU/s / óra   |$4,608 |
|Adattárolásra vonatkozó számla az USA nyugati régiójában lévő tároló esetén      | 250 GB    |$ 0,25/GB  |$62.50|
|Adattárolásra vonatozó számla 3 további régió esetén – az USA keleti régiója, Észak-Európa, Kelet-Ázsia      | 3 * 250 GB    |$ 0,25/GB  |$187.50|
|**Összes**     |     |  |**$6,010**|

*Azt is feltételezzük, hogy kimenő forgalmat bonyolít, 100 GB adat minden hónapban replikálja az adatokat az USA keleti RÉGIÓJA, Észak-Európa és Kelet-Ázsia, USA nyugati Régiójában lévő tárolóban. A számlázás kimenő adatátviteli megfelelően.*

### <a name="billing-example-azure-cosmos-account-with-multi-master-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>A számlázás példa: Több főkiszolgálós, adatbázisszintű sebessége, beleértve a dedikált átviteli mód az egyes tárolókhoz az Azure Cosmos-fiók

Vegyünk például a következő példa, ahol van egy többrégiós Azure Cosmos-fiók írható (több főkiszolgálós konfiguráció) esetén az összes régióban. Az egyszerűség kedvéért indulunk ki tárméret állandó marad az és nem módosíthatja és hagyja ki itt, hogy a példában egyszerűbb. A hónapban a kiosztott átviteli sebesség különböző módon (feltételezve 30 nap vagy 720 óra): 

[0 – 100 óra]:  

* Létrehozott egy három régióban az Azure Cosmos fiókot (USA nyugati RÉGIÓJA, USA keleti RÉGIÓJA, Észak-Európa), ahol minden régióban írható 

* A megosztott átviteli sebesség 10 ezer létrehoztunk egy adatbázist (D1) RU/s 

* Létrehozott egy adatbázist (D2) megosztott a 30-K RU/s átviteli sebességgel és  

* A dedikált átviteli sebesség 20 K létrehoztunk egy tárolót (C1) RU/s 

[101 – 200 – óra]:  

* Ezzel felskáláztuk 50 K RU/s-adatbázis (D1) 

* Ezzel felskáláztuk 70 K RU/s-adatbázis (D2)  

* Hogy törölt tároló (C1)  

[201-es – 300 órát]:  

* Létrehozott tároló (C1) ismét a dedikált átviteli sebesség 20 K RU/s 

[301-400 óra]:  

* Eltávolítottuk a régiók közül az Azure Cosmos-fiókból (# írható régiók jelenleg 2) 

* Azt vertikálisan leskálázni 10 K RU/s-adatbázis (D1) 

* Ezzel felskáláztuk 80 K RU/s-adatbázis (D2)  

* Hogy törölt tároló (C1) újra 

[óra 401-500]:  

* Azt vertikálisan leskálázni 10 K RU/s-adatbázis (D2)  

* Létrehozott tároló (C1) ismét a dedikált átviteli sebesség 20 K RU/s 

[501-700 óra]:  

* Ezzel felskáláztuk 20 K RU/s-adatbázis (D1)  

* Ezzel felskáláztuk 100 ezer Kérelemegység/s-adatbázis (D2)  

* Hogy törölt tároló (C1) újra  

[701-720 óra]:  

* Azt vertikálisan leskálázni 50 K RU/s-adatbázis (D2)  

Vizuálisan a módosításokat a teljes kiosztott átviteli kapacitás 720 órát a hónap során az alábbi ábrán láthatók: 

![Valós életbeli példa](./media/understand-your-bill/bill-example3.png)

A teljes havi számla fog kell (feltéve, hogy 30 nap/720 órában az adott hónapban) kiszámítása a következőképpen történik:

|**Óra**  |**RU/s** |**Elem** |**Használat (óránként)** |**Költségek** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10K <br/>D2:30K <br/>C1:20K |Átviteli sebességre vonatkozó számla az USA nyugati RÉGIÓJA (az összes régióban is írható) lévő tároló esetén  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$960  |
| | |Átviteli sebességre vonatkozó számla 2 további régiók: USA keleti RÉGIÓJA, Észak-Európa (az összes régióban is írható)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2,880  |
|[101-200] |D1:50K <br/>D2:70K <br/>C1: -- |Átviteli sebességre vonatkozó számla az USA nyugati RÉGIÓJA (az összes régióban is írható) lévő tároló esetén  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |$1920  |
| | |Átviteli sebességre vonatkozó számla 2 további régiók: USA keleti RÉGIÓJA, Észak-Európa (az összes régióban is írható)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |$5,760  |
|[201-300]  |D1:50K <br/>D2:70K <br/>C1:20K |Átviteli sebességre vonatkozó számla az USA nyugati RÉGIÓJA (az összes régióban is írható) lévő tároló esetén  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$2,240  |
| | |Átviteli sebességre vonatkozó számla 2 további régiók: USA keleti RÉGIÓJA, Észak-Európa (az összes régióban is írható)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |$6,720 |
|[301-400] |D1:10K <br/>D2:80K <br/>C1: -- |Átviteli sebességre vonatkozó számla az USA nyugati RÉGIÓJA (az összes régióban is írható) lévő tároló esetén  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |$1,440   |
| | |Átviteli sebességre vonatkozó számla 2 további régiók: USA keleti RÉGIÓJA, Észak-Európa (az összes régióban is írható)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2,880  |
|[401-500] |D1:10K <br>D2:10K <br>C1:20K |Átviteli sebességre vonatkozó számla az USA nyugati RÉGIÓJA (az összes régióban is írható) lévő tároló esetén  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$640  |
| | |Átviteli sebességre vonatkozó számla 2 további régiók: USA keleti RÉGIÓJA, Észak-Európa (az összes régióban is írható)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$1,280  |
|[501-700] |D1:20K <br>D2:100K <br>C1: -- |Átviteli sebességre vonatkozó számla az USA nyugati RÉGIÓJA (az összes régióban is írható) lévő tároló esetén  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |$3,840  |
| | |Átviteli sebességre vonatkozó számla 2 további régiók: USA keleti RÉGIÓJA, Észak-Európa (az összes régióban is írható)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |$7,680  |
|[701-720] |D1:20K <br/>D2:50K <br/>C1: -- |Átviteli sebességre vonatkozó számla az USA nyugati RÉGIÓJA (az összes régióban is írható) lévő tároló esetén  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |$224  |
| | |Átviteli sebességre vonatkozó számla 2 további régiók: USA keleti RÉGIÓJA, Észak-Európa (az összes régióban is írható)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |$224  |
|| |**Teljes havi díj**  | |**$38,688**   |

## <a name="proactively-estimating-your-monthly-bill"></a>Proaktív módon a havi számla becslése  

Vegyünk például egy másik példa, ahol szeretné a proaktív módon megbecsülhetők a hónap vége előtt. A számla megbecsülheti a következő:

|**Tárolási költségek** | |
|----|----|
|Rekord átlagos mérete (KB) |1 |
|Rekordok száma  |100,000,000  |
|Teljes tárterület (GB)  |100 |
|GB-onkénti havi költség  |$0.25  |
|A Storage várható havi költség   |$25.00  |

<br>

|**Átviteli sebesség költség** | | | |
|----|----|----|----|
|Művelettípus| Requests/sec| Átl. RU/kérelem| Kérelemegység szükséges|
|Írás| 100 | 5 | 500|
|Olvasás| 400| 1| 400|

Összes RU/mp: 500-as + 400 = 900 óránkénti költség: 900/100 * 0.008 $ $0.072 várható havi díj (31 napos feltételezve) átviteli sebességet =: $0.072 * 24 * 31 = $53.57

**Teljes havi díj**

Havi összköltség = tárolási havi költségek + az átviteli sebesség havi díja a havi költség = 25,00 + 53.57 $ $78.57 =

*Az árképzés eltérő lehet régiónként. Naprakész díjszabással kapcsolatos információk: a [díjszabási oldalunkon](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>A számlázás az Azure Cosmos DB lefoglalt kapacitás

Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás lehetővé teszi a kiosztott átviteli sebesség előre (lefoglalt kapacitás vagy a foglalás), amely az összes Azure-régiók között alkalmazhatók az Azure Cosmos DB-adatbázisok és a tárolókat (az API-t vagy az adatok modell) megvásárlását. Mivel a kiosztott átviteli kapacitás ára régiónként változik, segít úgy gondolja, hogy a fizetésre használható kredit, a kedvezménnyel, amely az egyes régiókban a megfelelő áron a kiosztott átviteli sebesség lehet levonni megvásárolt fenntartott kapacitás. Például tegyük fel, hogy egy Azure Cosmos fiók az egyetlen tároló kiosztott RU/s 50-K és globálisan replikált két régió – USA keleti RÉGIÓJA és kelet-japán. Ha a használatalapú fizetési lehetőséget választja, meg kell:  

* az USA keleti régiójában: a 0.008 $ / 100 RU/s az adott régióban, a díj 50-K RU/s 

* Kelet-japán régióban: a 0.009 $ / 100 RU/s az adott régióban, a díj 50-K RU/s

A teljes számlája (fenntartott kapacitás) nélküli lenne (feltételezve 30 nap vagy 720 óra): 

|**Régió**| **Az óradíjas ár / 100 RU/s**|**egység (RU/mp)**|**Számlázott összeg (óránként)**| **Számlázott összeg (havonta)**|
|----|----|----|----|----|
|USA keleti régiója|$0.008 |50 K|$4|$2,880 |
|Kelet-Japán|$0.009 |50 K| $4.50 |$3,240 |
|Összes|||$8.50|$6,120 |

Vegyünk például, hogy, hogy inkább is tartalékkapacitást vásárolt. Fenntartott kapacitás 100-K RU/s (20 %-os kedvezménnyel) egy évig 56,064 $ vagy 6.40 $ / óra díjért vásárolhat. Lefoglalt kapacitás a díjszabási a [díjszabási oldalunkon](https://azure.microsoft.com/pricing/details/cosmos-db/)).  

* Átviteli sebesség (használatalapú fizetés) költségek: 100 000 RU/mp/100 * $0.008/óra * 8760 óra egy évben = $70,080 

* (A lefoglalt kapacitás) átviteli sebesség díja 20 %-os kedvezményes $70,080 $56,064 = 

Milyen hatékonyan vásárolt $ 8 óránként, 100 ezer RU/s használatával a Listaár az USA keleti régiójában, az árat óránként 6.40 $ értékű kreditet. Majd rajzolhat, az előre fizetett átviteli foglalás az a kiosztott átviteli kapacitás a árakon megfelelő regionális listában állítsa be az előfizetéshez tartozó minden olyan globális Azure-régióban óránkénti alapon. Ebben a példában, amelyben üzembe helyezi az 50 K RU/s egyes az USA keleti RÉGIÓJA és kelet-japán, tudni rajzolása 8,00 $ értékű óránként kiosztott átviteli sebesség, és lesz 0,50 USD / óra (vagy $360/ hónap), a kerettúllépés díjszabása. 

|**Régió**| **Az óradíjas ár / 100 RU/s**|**egység (RU/mp)**| **Számlázott összeg (óránként)**| **Számlázott összeg (havonta)**|
|----|----|----|----|----|
|USA keleti régiója|$0.008 |50 K|$4|$2,880 |
|Kelet-Japán|$0.009 |50 K| $4.50 |$3,240 |
|||Utólagos, használatalapú fizetés|$8.50|$6120|
|Vásárolt fenntartott kapacitás|$0.0064 (20 %-os) |100 RU/s, vagy előre megvásárolt 8 dollár kapacitás |-$8|-$5,760 |
|Nettó számla|||$0.50 |$360 |

## <a name="next-steps"></a>További lépések

Ezután folytathatja kapcsolatos költségek optimalizálása az Azure Cosmos DB az alábbi cikkeket:

* Tudjon meg többet [Cosmos DB-hogyan díjszabási modell az ügyfelek költséghatékony](total-cost-ownership.md)
* Tudjon meg többet [optimalizálása fejlesztéshez és teszteléshez](optimize-dev-test.md)
* Tudjon meg többet [átviteli költségek optimalizálása](optimize-cost-throughput.md)
* Tudjon meg többet [tárolási költségek optimalizálása](optimize-cost-storage.md)
* Tudjon meg többet [olvasási és írási a költségek optimalizálása](optimize-cost-reads-writes.md)
* Tudjon meg többet [lekérdezések költségeinek optimalizálása](optimize-cost-queries.md)
* Tudjon meg többet [többrégiós Azure Cosmos-fiókok költségeinek optimalizálása](optimize-cost-regions.md)
