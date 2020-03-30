---
title: Az Azure Cosmos DB-számlájának ismertetése
description: Ez a cikk ismerteti, hogyan ismerje meg az Azure Cosmos DB-számlát néhány példával.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 5954c8eda370c0734985c47cfff6d073f5d76d17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258022"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Az Azure Cosmos DB számláinak ismertetése

Teljes körűen felügyelt natív adatbázis-szolgáltatásként az Azure Cosmos DB leegyszerűsíti a számlázást, mivel csak a kiosztott átviteli és a felhasznált tárhelyért számít fel díjat. A helyszíni vagy az IaaS által üzemeltetett alternatívákhoz képest nincsenek további licencdíjak, hardver-, közüzemi vagy létesítményköltségek. Ha figyelembe veszi az Azure Cosmos DB több régiós képességeit, az adatbázis-szolgáltatás a meglévő helyszíni vagy IaaS-megoldásokhoz képest jelentősen csökkenti a költségeket.

Az Azure Cosmos DB-vel a kiosztott átviteli kapacitás és a felhasznált tárterület alapján óránként számlázunk. A kiosztott átviteli forgalom esetében a számlázási egység 100 RU/s óránként, óránként 0,008 USD-ért számítva, szabványos nyilvános díjszabást feltételezve, lásd az [Árképzés i.](https://azure.microsoft.com/pricing/details/cosmos-db/) A felhasznált tárterület ért $0.25/1 GB tárterület havonta, lásd a [Díjszabás oldalon](https://azure.microsoft.com/pricing/details/cosmos-db/). 

Ez a cikk néhány példán keresztül elmagyarázza, mit jelentenek a havi számla különböző részei. A példákban szereplő számok az Önétől eltérőek lehetnek, ha az Ön Azure Cosmos-tárolói számára kiosztott átviteli sebesség eltérő, ha több régióra kiterjednek, vagy eltérő hosszúságú időszakban futnak havonta.

> [!NOTE]
> A számlázás a falióra bármely részére szól, nem pedig 60 perces időtartamra.

## <a name="billing-examples"></a>Számlázási példák

### <a name="billing-example---throughput-on-a-container-full-month"></a>Számlázási példa - átviteli egy tárolón (teljes hónap)

* Tegyük fel, hogy 1000 RU/s átviteli-átviteli beállítást konfigurál egy tárolón, és 24 órán keresztül * 30 napig létezik a hónap = összesen 720 óra.  

* 1000 RU/s 10 egység 100 RU/sec óránként minden órában a tárolók létezik (azaz 1000/100 = 10). 

* Óránként 10 egység szorzata $0.008 költséggel (100 RU/mp/per óra) = 0,08 0800 000/óra. 

* Az óránkénti 0,08 08 0800 000 000 000 000 000 08 * 24 óra * 30 nap = 57,60 USD a hónapra.  

* A teljes havi számla 7200 egységet mutat (100 Darab ot), ami 57,60 dollárba fog kerülni.

### <a name="billing-example---throughput-on-a-container-partial-month"></a>Számlázási példa - átviteli képesség egy tárolón (részleges hónap)

* Tegyük fel, hogy 2500 RU/s kiosztott átviteli fokkal rendelkező tárolót hozunk létre. A tároló a hónap során 24 órán át él (például 24 órával a létrehozás után töröljük).  

* Ezután 600 egységet fogunk látni a számlán (2500 RU/sec / 100 RU/sec / egység * 24 óra). A költség 4,80 USD (600 egység * 0,008 USD/egység).

* A hónap teljes számlája 4,80 dollár lesz.

### <a name="billing-rate-if-storage-size-changes"></a>Számlázási díj, ha a tárhely mérete megváltozik

A tárolási kapacitás számlázása a gb-ban tárolt adatok maximális óránkénti mennyiségének egységében történik egy havi időszakban. Ha például a hónap felében 100 GB tárhelyet, a hónap második felében pedig 50 GB-ot használt, akkor az adott hónapban 75 GB-nak megfelelő tárhelyet kell fizetnie.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Számlázási sebesség, ha a tároló vagy a tárolók egy készlete egy óránál rövidebb ideig aktív

A rendszer minden egyes óráért az átalánydíjat számláz, amely a tároló vagy az adatbázis létezik, függetlenül attól, hogy a használat, vagy ha a tároló vagy adatbázis aktív kevesebb, mint egy óra. Ha például létrehoz egy tárolót vagy adatbázist, és 5 perccel később törli, a számla egy órát fog tartalmazni.

### <a name="billing-rate-when-throughput-on-a-container-or-database-scales-updown"></a>Számlázási sebesség, ha egy tároló vagy adatbázis átviteli sebessége felfelé/lefelé skálázódik

Ha a kiosztott átviteli áteresztőképest 9:30-nál 400 RU/s-ról 1000 RU/mp-re növeli, majd 10:45-kor visszacsökkenti a kiosztott átviteli-átbocsátást 400 RU/mp-re, akkor két óra 1000 RU/s-ra kell fizetnie. 

Ha egy tároló vagy tárolókészlet kiosztott átviteli fazekatétet 9:30-kor 100 K RU/s-ról 200 K RU/mp-re növeli, majd 10:45-kor visszacsökkenti a kiosztott átviteli-átbocsátást 100 K RU/mp-re, akkor két óra 200 K RU/mp-re kell fizetnie.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Számlázási példa: több tároló, mindegyik dedikált kiosztott átviteli móddal

* Ha létrehoz egy Azure Cosmos-fiókot az USA keleti részén 2 két tárolóval, amelyek kiosztott átviteli-, 500 RU/s és 700 RU/s, illetve 1200 RU/s teljes kiosztott átviteli kapcsolattal rendelkezik.  

* 1200/100 * $0.008 = $0.096/óra díjat számítunk fel. 

* Ha az átviteli igény megváltozott, és az egyes tárolók kapacitását 500 RU/s-szal növelte, miközben egy új, 20 000 RU/s-t tartalmazó korlátlan tárolót hozott létre, a teljes kiosztott kapacitás 22 200 RU/s (1000 RU/sec + 1200 RU/sec + 20 000 RU/mp) lesz.  

* A számla ezután a következőre változik: $0.008 x 222 = $1.776/óra. 

* Egy 720 órás (24 órás * 30 nap) hónapban, ha 500 óra kiosztott átviteli forgalom 1200 RU/s volt, és a fennmaradó 220 óra kiosztott átviteli forgalom 22 200 RU/mp volt, a havi számla: 500 x $0.096/óra + 220 x $1.776/óra = $438.72/hó.

![Példa dedikált átviteli számlázásra](./media/understand-your-bill/bill-example1.png)

### <a name="billing-example-containers-with-shared-throughput-mode"></a>Számlázási példa: megosztott átviteli móddal rendelkező tárolók

* Ha létrehoz egy Azure Cosmos-fiókot az USA keleti részén 2-ben két Azure Cosmos-adatbázissal (az átviteli szintet az adatbázis szintjén megosztott tárolókkal) az 50 K RU/sec és a 70 K RU/sec kiosztott átviteli sebességgel, akkor 120 K RU/s teljes kiosztott átviteli sebességgel rendelkezik.  

* Akkor kell fizetnie 1200 x $ 0,008 = $ 9,60 / óra. 

* Ha az átviteli igény megváltozott, és minden egyes adatbázis kiosztott átviteli igényét minden egyes adatbázishoz 10 K RU/s-szal növelte, és új tárolót ad hozzá az első adatbázishoz, amely 15 K RU/sec dedikált átviteli móddal rendelkezik a megosztott átviteli-adatbázishoz, a teljes kiosztott kapacitás 155 K RU/sec (60 K RU/sec + 80 K RU/sec + 15 K RU/sec).  

* A számla ezután a következőre változik: 1550 * $0.008 = $12.40/óra.  

* Egy 720 órás hónapban, ha a 300 órás kiosztott átviteli forgalom 120 K RU/s volt, és a fennmaradó 420 óra kiosztott átviteli 155 K RU/s volt, a havi számla: 300 x $9.60/óra + 420 x $12.40/óra = $2,880 + $5,208 = $8,08/hó. 

![Példa a megosztott átviteli számlázásra](./media/understand-your-bill/bill-example2.png)

## <a name="billing-examples-with-geo-replication-and-multi-master"></a>Számlázási példák georeplikációval és többfőkiszolgálós  

Az Azure Cosmos-adatbázis-fiókhoz bármikor hozzáadhat/eltávolíthat Azure-régiókat a világ bármely pontjára. A különböző Azure Cosmos-adatbázisokhoz és -tárolókhoz konfigurált átviteli lesz fenntartva az Azure Cosmos-adatbázis-fiókhoz társított minden Egyes Azure-régióban. Ha az Azure Cosmos-adatbázisfiók összes adatbázisában és tárolóján (óránként kiépített) konfigurált kiépített átviteli-idő (RU/s) összege T, és az adatbázisfiókhoz társított Azure-régiók száma N, akkor a teljes kiosztott átviteli egy adott órában, az Azure Cosmos-adatbázis-fiók, (a) konfigurált egy írási régió egyenlő T x N RU/sec és (b) konfigurálva az összes régió képes az írások feldolgozása egyenlő T x (N+1) RU/sec, illetve. A kiosztott átviteli sebességű (egyírási régió) 100 RU/s-onként $0.008/óra, a több írható régióval rendelkező kiosztott átviteli-idő (több főkiszolgálós konfiguráció) pedig 100 RU/s-onként óránként $0.016/óra (lásd a [díjszabási oldalt).](https://azure.microsoft.com/pricing/details/cosmos-db/) Akár egyetlen írási régió, akár több írási régió, az Azure Cosmos DB lehetővé teszi, hogy bármilyen régióból adatokat olvasson be.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Számlázási példa: többrégiós Azure Cosmos-fiók, egyrégiós írások

Tegyük fel, hogy rendelkezik egy Azure Cosmos-tárolóval az USA nyugati részén. A tároló 10 K RU/s átviteli fokkal jön létre, és ebben a hónapban 1 TB adatot tárol. Tegyük fel, hogy három régiót (USA keleti régiója, Észak-Európa és Kelet-Ázsia) ad hozzá az Azure Cosmos-fiókhoz, amelyek mindegyike azonos tárhellyel és átviteli fokkal rendelkezik. A teljes havi számla lesz (feltételezve, hogy 30 nap egy hónapban). A számla a következő lenne: 

|**Elem** |**Használat (hónap)** |**Rate (Egységár)** |**Havi költség** |
|---------|---------|---------|-------|
|Átviteli számla a tárolóhoz az USA nyugati részén      | 1000 RU/mp * 24 * 30    |$0.008/100 RU/mp óránként   |576 $|
|Átviteli számla további 3 régióra - USA keleti régióra, Észak-Európára és Kelet-Ázsiára       | 3 * 10K RU/sec * 24 * 30    |$0.008/100 RU/mp óránként  |$1,728|
|Tárolószámla az USA nyugati részén      | 250 GB    |$0,25/GB  |$62.50|
|További 3 régió – USA keleti régió, Észak-Európa és Kelet-Ázsia – tárolási számlája      | 3 * 250 GB    |$0,25/GB  |$187.50|
|**Összesen**     |     |  |**2554 $**|

*Tegyük fel azt is, hogy havonta 100 GB adatot lép ki az USA nyugati részén lévő tárolóból, hogy adatokat replikáljon az USA keleti részébe, Észak-Európába és Kelet-Ázsiába. A kimenő forgalomért az adatátviteli sebesség nek kiszámlázva kell fizetnie.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Számlázási példa: többrégiós Azure Cosmos-fiók, többrégiós írások

Tegyük fel, hogy hozzon létre egy Azure Cosmos-tárolót az USA nyugati részén. A tároló 10 K RU/s átviteli fokkal jön létre, és ebben a hónapban 1 TB adatot tárol. Tegyük fel, hogy három régiót (USA keleti, Észak-Európa és Kelet-Ázsia) ad hozzá, amelyek mindegyike azonos tárhellyel és átviteli amelyekkel rendelkezik, és szeretné, hogy az Azure Cosmos-fiókhoz társított összes régióban is írhatana a tárolókba. A teljes havi számla lesz (feltételezve, hogy 30 nap egy hónapban) az alábbiak szerint:

|**Elem** |**Használat (hónap)**|**Rate (Egységár)** |**Havi költség** |
|---------|---------|---------|-------|
|Átviteli cím a tárolóhoz az USA nyugati régiójában (minden régió írható)       | 1000 RU/mp * 24 * 30    |$0.016/100 RU/mp óránként    |1152 $ |
|Átviteli számla további 3 régióra – USA keleti régiójára, Észak-Európára és Kelet-Ázsiára vonatkozóan (minden régió írható)        | (3 + 1) * 10K RU/mp * 24 * 30    |$0.016/100 RU/mp óránként   |4608 $ |
|Tárolószámla az USA nyugati részén      | 250 GB    |$0,25/GB  |$62.50|
|További 3 régió – USA keleti régió, Észak-Európa és Kelet-Ázsia – tárolási számlája      | 3 * 250 GB    |$0,25/GB  |$187.50|
|**Összesen**     |     |  |**$6,010**|

*Tegyük fel azt is, hogy havonta 100 GB adatot lép ki az USA nyugati részén lévő tárolóból, hogy adatokat replikáljon az USA keleti részébe, Észak-Európába és Kelet-Ázsiába. A kimenő forgalomért az adatátviteli sebesség nek kiszámlázva kell fizetnie.*

### <a name="billing-example-azure-cosmos-account-with-multi-master-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Számlázási példa: Azure Cosmos-fiók többfős, adatbázis-szintű átviteli, beleértve a dedikált átviteli mód egyes tárolók

Vegyük a következő példát, ahol egy többrégiós Azure Cosmos-fiókkal rendelkezünk, ahol az összes régió írható (többfős konfiguráció). Az egyszerűség kedvéért feltételezzük, hogy a tárolási méret állandó marad, és nem változik, és kihagyja itt, hogy a példa egyszerűbb maradjon. A kiosztott átbocsátás a hónap során a következőképpen változott (30 napot vagy 720 órát feltételezve): 

[0-100 óra]:  

* Létrehoztunk egy három régiós Azure Cosmos-fiókot (USA nyugati régiója, USA keleti régiója, Észak-Európa), ahol minden régió írható 

* Létrehoztunk egy adatbázist (D1) 10 K RU/s megosztott átviteli fokkal 

* Létrehoztunk egy adatbázist (D2) 30 K RU/s megosztott átviteli sebességű  

* Létrehoztunk egy 20 K RU/sec-es dedikált átviteli eresztővel rendelkező tárolót (C1) 

[101-200 óra]:  

* Az adatbázist (D1) 50 K RU/mp-re emeltük 

* Az adatbázist (D2) 70 K RU/mp-re emeltük  

* Töröltük konténer (C1)  

[201-300 óra]:  

* A 20 K RU/sec dedikált átviteli értékkel ismét létrehoztuk a tárolót (C1) 

[301-400 óra]:  

* Eltávolítottuk az egyik régiót az Azure Cosmos-fiókból (az írható régiók száma most 2) 

* Az adatbázist (D1) 10K RU/mp-re csökkentettük 

* Az adatbázist (D2) 80 K RU/mp-re emeltük  

* Ismét töröltük a tárolót (C1) 

[401-500 óra]:  

* Az adatbázist (D2) 10K RU/mp-re csökkentettük  

* A 20 K RU/sec dedikált átviteli értékkel ismét létrehoztuk a tárolót (C1) 

[501-700 óra]:  

* Az adatbázist (D1) 20 K RU/mp-re emeltük  

* Az adatbázist (D2) 100 K RU/mp-re emeltük  

* Ismét töröltük a tárolót (C1)  

[701-720 óra]:  

* Az adatbázist (D2) 50 K RU/mp-re csökkentettük  

A teljes kiosztott átviteli érték 720 óra alatt bekövetkező változások vizuálisan az alábbi ábrán láthatók: 

![Valós példa](./media/understand-your-bill/bill-example3.png)

A teljes havi számla (feltételezve, hogy 30 nap/ 720 óra egy hónapban) kerül kiszámításra a következőképpen:

|**Óra**  |**RU/s** |**Elem** |**Használat (óránként)** |**Költségek** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10K <br/>D2:30K <br/>C1:20K |Átviteli cím a tárolóhoz az USA nyugati régiójában (minden régió írható)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |960 $  |
| | |Átviteli számla 2 további régióra: USA keleti régiója, Észak-Európa (minden régió írható)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2,880  |
|[101-200] |D1:50K <br/>D2:70K <br/>C1: -- |Átviteli cím a tárolóhoz az USA nyugati régiójában (minden régió írható)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |1920$  |
| | |Átviteli számla 2 további régióra: USA keleti régiója, Észak-Európa (minden régió írható)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |5760 $  |
|[201-300]  |D1:50K <br/>D2:70K <br/>C1:20K |Átviteli cím a tárolóhoz az USA nyugati régiójában (minden régió írható)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$2,240  |
| | |Átviteli számla 2 további régióra: USA keleti régiója, Észak-Európa (minden régió írható)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |$6,720 |
|[301-400] |D1:10K <br/>D2:80K <br/>C1: -- |Átviteli cím a tárolóhoz az USA nyugati régiójában (minden régió írható)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |$1,440   |
| | |Átviteli számla 2 további régióra: USA keleti régiója, Észak-Európa (minden régió írható)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2,880  |
|[401-500] |D1:10K <br>D2:10K <br>C1:20K |Átviteli cím a tárolóhoz az USA nyugati régiójában (minden régió írható)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$640  |
| | |Átviteli számla 2 további régióra: USA keleti régiója, Észak-Európa (minden régió írható)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$1,280  |
|[501-700] |D1:20K <br>D2:100K <br>C1: -- |Átviteli cím a tárolóhoz az USA nyugati régiójában (minden régió írható)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |3840 $  |
| | |Átviteli számla 2 további régióra: USA keleti régiója, Észak-Európa (minden régió írható)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |$7,680  |
|[701-720] |D1:20K <br/>D2:50K <br/>C1: -- |Átviteli cím a tárolóhoz az USA nyugati régiójában (minden régió írható)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |224 $  |
| | |Átviteli számla 2 további régióra: USA keleti régiója, Észak-Európa (minden régió írható)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |224 $  |
|| |**Teljes havi költség**  | |**$38,688**   |

## <a name="billing-examples-with-free-tier-accounts"></a>Számlázási példák ingyenes szintű fiókokkal
Az Azure Cosmos DB ingyenes szinttel az első 400 RU/s és 5 GB tárterületet kapja a fiókjában ingyenesen, a fiók szintjén alkalmazva. A 400 RU/s-on és 5 GB-on túli ru/s-t és tárhelyet az árképzési oldalonkénti rendszeres díjszabásszerint számlázunk. A számlán nem fog látni egy díjat vagy sort az ingyenes 400 Ru /s és 5 GB, csak a RU /s és a tárolás túl, amit az ingyenes szint vonatkozik. A 400 RU/s vonatkozik bármilyen típusú RU/s - kiépített átviteli, robotpilóta (előzetes verzió) és a többfőes.  

### <a name="billing-example---container-or-database-with-provisioned-throughput"></a>Számlázási példa – kiépített átviteli sebességű tároló vagy adatbázis
- Tegyük fel, hogy hozzon létre egy adatbázist vagy tárolót egy 400 RU/s és 5 GB tárhellyel rendelkező ingyenes rétegfiókban.
- A számla nem jelenít meg semmilyen díjat az erőforrásért. Az óránkénti és havi költség 0 dollár lesz.
- Most tegyük fel, hogy ugyanabban a fiókban egy másik adatbázist vagy tárolót adunk hozzá 1000 RU/s és 10 GB tárhellyel.
- A számla most megjelenik az 1000 RU/s és a 10 GB tárterület díja. 

### <a name="billing-example---container-or-database-with-autopilot-throughput-preview"></a>Számlázási példa – robotpilóta átviteli sebességű tároló vagy adatbázis (előzetes verzió)
- Tegyük fel, hogy egy ingyenes rétegfiókban létrehozunk egy adatbázist vagy tárolót, amelyen engedélyezve van a robotpilóta, és legfeljebb 4000 RU/s ru/s. Ez az erőforrás automatikusan 400 RU/s - 4000 RU/s között méreteződik. 
- Tegyük fel, hogy az 1 és 10 óra között az erőforrás legalább 400 RU/s. A 11.
- Az 1 és 10 óra között $0-t számlázunk az átviteli forgalomért, mivel a 400 RU/s-t ingyenes szint fedi. 
- A 11 óra alatt 1000 RU/s - 400 RU/s = 600 RU/s értékért kell fizetnie, mivel ez az óra legmagasabb RU/s-a. Ez 6 egység 100 RU/s lesz az órára, így az óra teljes átviteli költsége 6 egység * $0.012 = $0.072 lesz. 
- Az első 5 GB-on túli tárhelyek számlázása normál tárolási áron lesz. 

### <a name="billing-example---multi-region-single-write-region-account"></a>Számlázási példa - többrégiós, egyírási régiós fiók
- Tegyük fel, hogy egy ingyenes rétegfiókban létrehozunk egy adatbázist vagy tárolót 1200 RU/s és 10 GB tárhellyel. A fiókot 3 régióra replikáljuk, és egy főkiszolgálós (egy írási régió) fiókkal rendelkezünk.
- Összesen, ingyenes szint nélkül, 3 * 1200 RU/s = 3600 RU/s és 3 * 10 GB = 30 GB tárhely et számlázunk.
- Az ingyenes szint kedvezmény, eltávolítása után 400 RU/s és 5 GB-os tárhely, mi lesz számlázott egy hatékony 3200 RU/s (32 egység) a kiosztott átviteli sebesség az egyetlen írási régió díja és 25 GB-os tárhely.
- A RU/s havi költsége: 32 egység * $0.008 * 24 óra * 31 nap = $190.46. A havi tárolási költség: 25 GB * 0,25 / GB = 6,25 $. A teljes költség lenne 190,46 $ + 6,25 $ = 196,71 $.
- Megjegyzés: ha a VT/s vagy a storage egységára régiónként eltérő, az ingyenes 400 RU/s és 5 GB szint annak a régiónak a díjait fogja tükrözni, amelyben a fiókot létrehozták.

### <a name="billing-example---multi-region-multi-master-multiple-write-region-account"></a>Számlázási példa - többrégiós, többfős (több írási régió) fiók

Ez a [példa](https://azure.microsoft.com/pricing/details/cosmos-db/) a 2019. 
- Tegyük fel, hogy egy ingyenes rétegfiókban létrehozunk egy adatbázist vagy tárolót 1200 RU/s és 10 GB tárhellyel. A fiókot 3 régióra replikáljuk, és több főkiszolgálós (több írási régió) fiókkal rendelkezünk. 
- Összesen, ingyenes szint nélkül, 3 * 1200 RU/s = 3600 RU/s és 3 * 10 GB = 30 GB tárhely et számlázunk.
- Az ingyenes szint kedvezmény, eltávolítása után 400 RU/s és 5 GB-os tárhely, mi lesz számlázott egy hatékony 3200 RU/s (32 egység) a kiosztott átviteli sebesség a több írási régió aránya és 25 GB-os tárhely.
- A RU/s havi költsége: 32 egység * $0.016 * 24 óra * 31 nap = $380.93. A havi tárolási költség: 25 GB * 0,25 / GB = 6,25 $. A teljes költség lenne 380,93 $ + 6,25 $ = 387,18 $.

## <a name="proactively-estimating-your-monthly-bill"></a>A havi számla proaktív becslése  

Vegyünk egy másik példát is, ahol proaktív módon szeretné megbecsülni a számlát a hónap vége előtt. A számlát a következőképpen becsülheti meg:

|**Tárolási költség** | |
|----|----|
|Átlagos rekordméret (KB) |1 |
|Rekordok száma  |100,000,000  |
|Teljes tárhely (GB)  |100 |
|Havi gb-onkénti költség  |$0,25  |
|Várható havi tárolási költség   |$25.00  |

<br>

|**Átviteli költség** | | | |
|----|----|----|----|
|Művelettípus| Kérések/mp| Átlagos RU/kérelem| Szükséges rt-k|
|Írás| 100 | 5 | 500|
|Olvasás| 400| 1| 400|

Teljes RU/mp: 500 + 400 = 900 Óránkénti költség: 900/100 * $0.008 = $0.072 Várható havi átviteli költség (31 nap értékében): $0.072 * 24 * 31 = $53.57

**Teljes havi költség**

Teljes havi költség = Havi tárolási költség + Havi átmenő átmenő költség Teljes havi költség = $25.00 + $53.57 = $78.57

*Az árak régiónként eltérőek lehetnek. A naprakész árakról az Árképzés [oldalon](https://azure.microsoft.com/pricing/details/cosmos-db/)találja.*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Számlázás az Azure Cosmos DB fenntartott kapacitásával

Az Azure Cosmos DB fenntartott kapacitása lehetővé teszi, hogy előre megvásárolja a kiosztott átviteli kapacitást (fenntartott kapacitást vagy foglalást), amely az összes Azure Cosmos-adatbázisra és -tárolóra alkalmazható (bármely API-hoz vagy adatmodellhez) az összes Azure-régióban. Mivel a kiosztott átviteli kapacitás régiónként változik, segít a fenntartott kapacitást a kedvezményes áron vásárolt pénzbeli jóváírásként kezelni, amely a kiosztott átviteli teljesítményhez az egyes régiók megfelelő árán is lehívható. Tegyük fel például, hogy rendelkezik egy Azure Cosmos-fiókkal, amely egyetlen, 50 K RU/s-os tárolóval van kiépítve, és globálisan replikált két régiót – USA keleti és kelet-japán. Ha a felosztó-ki-ki-fel opciót választja, akkor a következőket kell fizetnie:  

* USA keleti régiójában: 50 K RU/s esetén, 100 RU/s/mp/s arány mellett az adott régióban 

* Kelet-Japánban: 50 K RU/mp esetén 0,009 USD/100 RU/s arányért az adott régióban

A teljes számla (fenntartott kapacitás nélkül) lenne (feltételezve, hogy 30 nap vagy 720 óra): 

|**Régió**| **Óránkénti ár 100 RU/s-onként**|**Egységek (RU/s)**|**Számlázott összeg (óránként)**| **Számlázott összeg (havi)**|
|----|----|----|----|----|
|USA keleti régiója|$0.008 |50 ezer|$4|$2,880 |
|Kelet-Japán|$0.009 |50 ezer| 4,50 $ |3240 $ |
|Összesen|||8,50 $|$6,120 |

Vegyük figyelembe, hogy ehelyett fenntartott kapacitást vásárolt. Egy dollárért 56 064 USD (20%-os kedvezménnyel) vagy óránként 6,40 USD áron vásárolhat fenntartott kapacitást 100 K RU/s áron. Lásd a fenntartott kapacitás díjszabását az [Árképzés oldalon](https://azure.microsoft.com/pricing/details/cosmos-db/)).  

* Az átviteli arány költsége (felosztó-kifizetés): 100 000 RU/mp/100 * $0.008/óra * 8760 óra egy évben = $70,080 

* Az átviteli képesség költsége (fenntartott kapacitással) $70,080 diszkontálva 20% = $56,064 

Amit ténylegesen vásárolt a hitel 8 $ / óra, a 100 K RU / sec segítségével a listaár az USA keleti részén, az ára 6,40 $ / óra. Ezután lehívhatja ezt az előre fizetett átviteli sebességű foglalást óránként bármely globális Azure-régióban a megfelelő regionális listaárak on your subscription. Ebben a példában, ahol 50 K RU/sec-et létesít az USA keleti részén és Kelet-Japánban, akkor 8,00 USD értékű kiépített átviteli forgalmat vonhat le óránként, és 0,50 USD/óra (vagy $360/hó) túlterhelést számít ki. 

|**Régió**| **Óránkénti ár 100 RU/s-onként**|**Egységek (RU/s)**| **Számlázott összeg (óránként)**| **Számlázott összeg (havi)**|
|----|----|----|----|----|
|USA keleti régiója|$0.008 |50 ezer|$4|$2,880 |
|Kelet-Japán|$0.009 |50 ezer| 4,50 $ |3240 $ |
|||Felosztó-ki-kiosztó|8,50 $|$6120|
|Megvásárolt lefoglalt kapacitás|$0.0064 (20% kedvezmény) |100 RU/mp vagy $8 kapacitás előre megvásárolt |-$8|-$5,760 |
|Nettó számla|||$0.50 |360 $ |

## <a name="next-steps"></a>Következő lépések

Ezután az alábbi cikkekkel ismerheti meg az Azure Cosmos DB költségoptimalizálását:

* További információ [arról, hogy a Cosmos DB díjszabási modellje hogyan költséghatékony az ügyfelek számára](total-cost-ownership.md)
* További információ [az optimalizálásról fejlesztési és tesztelési célokra](optimize-dev-test.md)
* További információ az [átviteli költség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási költség optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási költségek optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések költségének optimalizálásáról](optimize-cost-queries.md)
* További információ a több régióra kiterjedő [Azure Cosmos-fiókok költségeinek optimalizálásáról](optimize-cost-regions.md)
