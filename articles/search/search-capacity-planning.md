---
title: Partíciók és replikák kiosztani a lekérdezés és az Azure Search indexelő |} Microsoft Docs
description: Állítsa be az Azure Search, ahol az egyes erőforrások árképzéséről számlázható keresési egység a partícióazonosító és másodpéldány számítógép-erőforrásokat.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: b6c2c8283d5a60013c525db296bf84cc50d76617
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="allocate-partitions-and-replicas-for-query-and-indexing-workloads-in-azure-search"></a>Partíciók és replikák kiosztani a lekérdezés és a munkaterhelések az Azure Search indexelő
Miután [válasszon egy tarifacsomagot](search-sku-tier.md) és [egy keresési szolgáltatás kiépítése](search-create-service-portal.md), a következő lépésre szükség növelje a replikák és a szolgáltatás által használt partíciók számát. Minden egyes kínál, számlázási egységek rögzített száma. Ez a cikk azt ismerteti, hogyan azokat az egységeket, hogy a lekérdezés-végrehajtás indexelő és tárolási követelményeinek optimális konfigurálása során eléréséhez lefoglalni.

Erőforrás beállítási lehetőségek érhetők el, ha beállít egy szolgáltatást a [alapszintű rétegben](http://aka.ms/azuresearchbasic) vagy az egyik a [szabványos rétegek](search-limits-quotas-capacity.md). Ezek a rétegek szolgáltatások, a kapacitás lépésekben vásárolt *keresési egységet* (SUs) ahol minden partíció és a replika számít-e egy SU. 

Kevesebb SUs-eredmények arányosan alacsonyabb számlázási használata. Számlázási számára van, amíg a szolgáltatás be van állítva. A szolgáltatás nem ideiglenesen használ, ha a csak számlázási elkerülése érdekében, a szolgáltatás törlése, és majd újra hozza létre, ha esetleg szükség lenne rá.

> [!Note]
> A szolgáltatás törlése töröl minden rajta. Nem a létesítmény biztonsági mentése az Azure Search belül van, és keresési adatainak visszaállítása maradnak. Egy új szolgáltatás a meglévő index újratelepíteni, a program létrehozásához, és töltse be eredetileg kell futtatni. 

## <a name="terminology-partitions-and-replicas"></a>Terminológia: partíciók és replikák
Partíciók és a replikák is az elsődleges erőforrások biztonsági másolatot a keresési szolgáltatást.

| Erőforrás | Meghatározás |
|----------|------------|
|*Partíciók* | Index tároló és az I/O biztosít olvasási/írási műveletek (például amikor újraépítését, vagy az index frissítése).|
|*Replikák* | A keresési szolgáltatás példánya, elsősorban az egyenleg lekérdezési műveletek betöltéséhez. Minden egyes replikának mindig tartalmazó egy indexet. Ha 12 replikákat, hogy minden betöltve a szolgáltatás az index 12 másolatát.|

> [!NOTE]
> Nincs semmilyen módon nem lehet közvetlenül kezelheti, vagy olyan replikán, futtassa mely Indexek kezelése. Minden index minden replikán egy példánya a service-architektúra része.
>

## <a name="how-to-allocate-partitions-and-replicas"></a>Partíciók és replikák lefoglalása
Az Azure Search szolgáltatás eredetileg lefoglalt egy partíciót és egy másodpéldány-erőforrások megadott minimális szintjét. Az rétegek, amelyek támogatják ezt a partíciók növelésével, ha több tároló és az I/O kell, vagy adja hozzá a további replikák nagyobb lekérdezés kötetek vagy jobb teljesítményt Növekményesen módosíthatja számítási erőforrások. Egyetlen szolgáltatás kell rendelkezik elegendő erőforrással (indexelő és lekérdezések) összes munkaterhelések kezelésére. Munkaterhelésének több szolgáltatás nem tudja tovább.

Növelheti vagy csökkentheti a lefoglalást a replikák és a partíciók, javasoljuk, az Azure portál használatával. A portál érvénybe lépteti a megengedett maximális határértékek alatt maradnak kombinációk vonatkozó korlátozások:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/) , és válassza ki a keresési szolgáltatást.
2. A **beállítások**, nyissa meg a **méretezési** panel és a csúszkák használatával növelheti vagy csökkentheti a partíciók és replikák száma.

Ha szüksége van egy parancsprogram-alapú vagy a kód-alapú üzembe helyezési módszert használja, a [felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/services) a portál alternatív megoldás.

Alkalmazások keresése általában akkor kell további replikák partíciók, mint, különösen akkor, amikor a szolgáltatási műveletek felé lekérdezés munkaterhelések vannak optimalizálva. A szakasz a [magas rendelkezésre állású](#HA) miért ismerteti.

> [!NOTE]
> A szolgáltatás üzembe helyezése után nem lehet frissíteni egy magasabb másikra. Hozzon létre egy keresési szolgáltatást, az új réteget kell, és töltse be újra az indexek. Lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) szolgáltatás kiépítése segítségre.
>
>

<a id="HA"></a>

## <a name="high-availability"></a>Magas rendelkezésre állás
Mivel a szolgáltatás egyszerű és viszonylag gyors méretezést kívánó, általában ajánlott a kiindulási pont egy partíciót, és egy vagy két replikákat, és ezután lekérdezés kötetként felskálázott készítsen. Lekérdezés munkaterhelések futtatásához elsősorban a replikákon. Ha további átviteli vagy magas rendelkezésre állású, további replikák valószínűleg szüksége lesz.

A magas rendelkezésre állás általános ajánlások a következők:

* A magas rendelkezésre állás csak olvasható munkaterhelések (lekérdezések) két replika
* Három vagy több replikák a magas rendelkezésre állás olvasási/írási munkaterhelések (lekérdezések és indexelő, egyes dokumentumok hozzáadott, frissítése vagy törlése)

Szolgáltatásszint-szerződések (SLA) az Azure Search lekérdezési műveletek és az index frissítések, amelyek hozzáadása, frissítése vagy törlése a dokumentumok célozzák meg.

Az alapszintű csomag fent egy partíciót és három replikákat. Ha azt szeretné, hogy igény szerint indexelő és lekérdezés átviteli sebesség eléréséhez a ingadozását azonnal reagálni a rugalmasságot, fontolja meg a normál szintek egyikét.

### <a name="index-availability-during-a-rebuild"></a>Során egy rebuild index elérhetősége

Az Azure Search magas rendelkezésre állású lekérdezések és nem tartalmaz, amely az index újraépítése index frissítések vonatkozik. Ha mező törlése, módosítása adattípust vagy mező átnevezése, szüksége lesz az index újraépítése. Az index újraépítése kell törölni az indexet, hozza létre újból az indexet, és töltse be újra az adatokat.

> [!NOTE]
> Új mezőket adhat az Azure Search-index, anélkül, hogy az index újraépítése. Az új mező értékét null értéket már az index található összes dokumentum lesz.

Hogy index rendelkezésre álljon egy Újraépítés alatt, a ugyanazt a szolgáltatást egy eltérő nevű index másolatát, vagy a az index egy másik szolgáltatásra ugyanazzal a névvel rendelkezik, és átirányítása vagy feladatátvételi logika adja meg a kódban.

## <a name="disaster-recovery"></a>Vészhelyreállítás
Jelenleg nincs vész-helyreállítási beépített mechanizmus. Hozzáadását partíciók, sem a replikákat a vész-helyreállítási célkitűzések helytelen stratégiája lenne. A leggyakoribb megoldás, a szolgáltatás szintű redundancia hozzáadása beállítása egy második keresési szolgáltatást, egy másik régióban. Csakúgy, mint a rendelkezésre állási egy index rebuild során, az átirányítás vagy feladatátvételi logika kell származnia a kódot.

## <a name="increase-query-performance-with-replicas"></a>Növelje a lekérdezési teljesítmény replikával
Lekérdezés-késleltetés értéke azt jelzi, hogy van-e szükség további replikákat. Általában a lekérdezési teljesítmény első lépését hoz adhat hozzá további ehhez az erőforráshoz. Replikák hozzáadása során, további példányokat az index online állapotba nagyobb lekérdezés munkaterhelések támogatásához, és betölteni a kérelmek kiegyenlítheti a több replika keresztül.

Rögzített becslések nem nyújtunk a lekérdezések / másodperc (QPS): a lekérdezés a lekérdezés és versengő munkaterhelések bonyolultságától függ. Bár egyértelműen replikák hozzáadása jobb teljesítményt eredményez, az eredmény nincs szigorúan lineáris: három replikák hozzáadása nem garantálja háromszoros átviteli sebességet.

A QPS becslése a munkaterhelések útmutatóért lásd: [Azure Search teljesítmény- és optimalizálási szempontok](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>A partíciók indexelési teljesítmény növelése
Közel valós idejű adatok frissítést igénylő alkalmazások keresése mint replikák arányosan több partíciót kell. Partíciók hozzáadása között osztja el olvasási/írási műveletek nagy számú számítási erőforrások között. Azt is lehetővé teszi több lemezterületet tárolásához további indexekhez és dokumentumokhoz.

A lekérdezés hosszabb ideig nagyobb indexeket. Előfordulhat például, hogy minden egyes partíciók növekményes növekedése igényel-e a kisebb, de arányos növelését a replikákat. A lekérdezések és a lekérdezés kötet összetettsége fog tényező be, hogy milyen gyorsan be van kapcsolva a lekérdezés-végrehajtás.

## <a name="basic-tier-partition-and-replica-combinations"></a>Az alapszintű csomag: partícióazonosító és másodpéldány kombinációk
Egy alapszintű service pontosan egy partícióval rendelkezik, és legfeljebb három replikákat, egy maximális határérték három SUS-t. A csak állítható erőforrás a replikákat. Legalább két replika lekérdezések magas rendelkezésre állásra van szükség.

<a id="chart"></a>

## <a name="standard-tiers-partition-and-replica-combinations"></a>Standard rétegek: partícióazonosító és másodpéldány kombinációk
Az alábbi táblázatban a SUS-t a replikák és a partíciók 36-SU korlátainak függvényében kiegészítve az összes szabványos rétegek támogatásához szükséges.

|   | **1 partíció** | **2 partíció** | **3 partíció** | **4 partíciók** | **6 partíciók** | **12 partíciók** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replika** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 replikák** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 replikák** |3 SU |6 SU |9-ES SU |12 SU |18 SU |36 SU |
| **4 replikák** |4 SU |8 SU |12 SU |16 SU |24 SU |– |
| **5 replikák** |5 SU |10 SU |15 SU |20 SU |30 SU |– |
| **6 replikák** |6 SU |12 SU |18 SU |24 SU |36 SU |– |
| **12 replikák** |12 SU |24 SU |36 SU |– |N/A |– |

SUS-t, a díjszabás és a kapacitás részletes magyarázatához lásd az Azure webhelyén. További információkért lásd: [díjszabás](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> A replikák és a partíciók száma egyenlően osztja 12 (pontosabban, 1, 2, 3, 4, 6, 12). Ennek oka az Azure Search előre osztja minden index 12 szilánkok, így az is lehet elosztva egyenlő részre minden partíció. Például ha a szolgáltatás három partíciókkal rendelkezik, és az index létrehozása, mindegyik partíció négy szilánkok az index fogja tartalmazni. Hogyan index egy Azure Search szilánkok egy megvalósítási részletei, a későbbiekben változhat. Bár a szám 12 ma, akkor nem vár mindig lehet a jövőben a 12.
>
>

## <a name="billing-formula-for-replica-and-partition-resources"></a>A replika és a partíció erőforrások számlázási képlet
A kiszámítása bizonyos kombinációinál használt hány SUs képlete a replikák és a partíciók, a termék vagy (R X P = SU). Például három replikák és a három kilenc SUs terheli.

A réteg, mint a standard Basic alacsonyabb egység számlázási sebességet SU onkénti költséget határozza meg. Az egyes rétegek található minősíti [díjszabás](https://azure.microsoft.com/pricing/details/search/).
