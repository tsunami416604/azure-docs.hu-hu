---
title: Foglaljon le a partíciókat és -replikákat a lekérdezést, és az Azure Search szolgáltatásban az indexelés |} A Microsoft Docs
description: Módosítsa a partíció- és a replika számítógépes erőforrások az Azure Search szolgáltatásban, ahol az egyes erőforrások a számlázható keresési egységek díjszabása.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: fa1a13c5c786867f6e92a678c40a491e0a226076
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238735"
---
# <a name="allocate-partitions-and-replicas-for-query-and-indexing-workloads-in-azure-search"></a>A lekérdezés és a számítási feladatok indexeléséhez az Azure Search partíciókat és -replikákat foglalása
Miután [válasszon egy tarifacsomagot](search-sku-tier.md) és [egy keresési szolgáltatás kiépítése](search-create-service-portal.md), a következő lépés az, hogy szükség esetén a replikákat és partíciókat, a szolgáltatás által használt számának növelésére. Minden egyes számlázási egységek rögzített számú kínálja. Ez a cikk ismerteti azokat az egységeket optimális konfigurációt, amely elosztja a lekérdezés-végrehajtás, indexelési és tárolási követelményeinek elérésére lefoglalása.

Erőforrás-konfigurációban érhető el a szolgáltatás üzembe helyezésekor meg a [alapszintű csomag](https://aka.ms/azuresearchbasic) vagy az egyik a [normál szintű csomagjai](search-limits-quotas-capacity.md). Ezek a csomagok, szolgáltatások, előállításának egységnyi növekményekben *keresési egységek* (su) Ha mindegyik partíciót és a replika számít-e egy SU. 

SUs kevesebb találatot használata egy arányosan alacsonyabb számlákat. A számlázás érvényben a mindaddig, amíg a szolgáltatás be van állítva. Átmenetileg nem használ olyan szolgáltatást, ha az egyetlen lehetőség a számlázás elkerülése érdekében a a szolgáltatás törlése, és ezután hozza létre újra szükség esetén.

> [!Note]
> Szolgáltatás törlése töröl minden rajta. Nincs a létesítmény biztonsági mentése az Azure Search belül van, és a keresési adatok visszaállítása megőrzött. Ismételt üzembe helyezése egy új szolgáltatás a meglévő index, hozhat létre és feltöltheti azokat eredetileg program kell futtatásakor. 

## <a name="terminology-partitions-and-replicas"></a>Terminológiai: partíciókat és -replikákat
Partíciókat és -replikákat olyan biztonsági másolatot egy keresési szolgáltatás elsődleges-erőforrásokat.

| Erőforrás | Meghatározás |
|----------|------------|
|*Partíciók* | Indexelt tárolási és i/o biztosít olvasási és írási műveletek (például amikor újraépítése vagy egy index frissítése).|
|*Replikák* | A keresési szolgáltatás elsősorban az egyenleg lekérdezési műveletek betöltéséhez használt példánya. Minden egyes replikának mindig futtatja az index egy példányát. Ha 12 replikákat, kell betölteni a szolgáltatás minden egyes indexnek 12 példányait.|

> [!NOTE]
> Nincs közvetlenül módosítására vagy kezelésére, mely indexek futtatunk egy replika mód. Minden egyes minden replika indexe egy példányát a service-architektúra része.
>

## <a name="how-to-allocate-partitions-and-replicas"></a>Partíciók és a replika lefoglalása
Az Azure Search szolgáltatás eredetileg lefoglalt egy partíciót és a egy replika erőforrások megadott minimális szintjét. Az azt támogató szolgáltatásaihoz fokozatosan módosíthatja számítási erőforrást növeli a partíciók számát, ha szükség van további tárolási és i/o, vagy nagyobb lekérdezési kötetek vagy a jobb teljesítmény érdekében több replika hozzáadása. Egy egyetlen szolgáltatást kell rendelkezik elegendő erőforrással (indexelése és lekérdezések) az összes számítási feladatok kezelésére. Több szolgáltatás számítási feladatokat nem feloszthatja.

Szeretné növelni, vagy a lefoglalást a replikák és partíciók, javasoljuk, az Azure portal használatával. A portál kikényszeríti a megengedett maximális korlát alatti maradjon kombinációk vonatkozó korlátozások:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) , és válassza ki a keresési szolgáltatást.
2. A **beállítások**, nyissa meg a **méretezési** panel és a csúszkák használatával növelheti vagy csökkentheti a partíciókat és -replikákat számát.

Ha szüksége van egy parancsprogram-alapú vagy a kód-alapú üzembe helyezési módszert használja, a [felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/services) alternatívájaként a portálon.

Általában az alkalmazások keresése, mint a partíciók több replika kell, különösen akkor, ha a szolgáltatási műveletek vannak torzítatlan lekérdezési számítási feladatok felé. A szakasz a [magas rendelkezésre állású](#HA) ismerteti az okokat.

> [!NOTE]
> Egy szolgáltatás kiépítése, után azt egy magasabb szintű termékváltozatra nem frissíthető. A search szolgáltatás létrehozása az új rétegben kell, és töltse be újra az indexek. Lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) segítség a szolgáltatás üzembe helyezését.
>
>

<a id="HA"></a>

## <a name="high-availability"></a>Magas rendelkezésre állás
Mivel a szolgáltatás egyszerűen és viszonylag gyorsan vertikális, általában javasoljuk, hogy először a több partíciót és a egy vagy két replika, majd vertikális felskálázási lekérdezés kötetek létrehozása. Lekérdezési számítási feladatok futtatásához, elsősorban a replikákon. Ha további kapacitás vagy a magas rendelkezésre állású van szüksége, további replikák valószínűleg szüksége lesz.

Általános javaslatok a magas rendelkezésre állás érdekében a következők:

* Csak olvasható munkaterhelések (lekérdezések) magas rendelkezésre álláshoz két replika
* Három vagy több replikát, a magas rendelkezésre állású olvasási és írási számítási feladatok (lekérdezések és az indexelés, egyes dokumentumok hozzáadása, frissített vagy törölt)

Szolgáltatói szerződések (SLA) az Azure Search lekérdezési műveletek vagy egy index frissítéseit hozzáadása, frissítése vagy törlése a dokumentumok álló célozzák meg.

Alapszintű csomag korona egy partíciót és három replika készül. Ha azt szeretné, hogy az indexelés és a lekérdezési teljesítmény iránti igény ingadozása azonnal reagálni a rugalmasságot, fontolja meg a Standard csomagok valamelyikére.

### <a name="index-availability-during-a-rebuild"></a>Index elérhetőségére újjáépítést során

Magas rendelkezésre állás az Azure Search lekérdezések és az index újraépítése nem érintő frissítések index tartozik. Ha egy mező törölhető, adatok típusának módosítása, vagy nevezze át a mezőt, szüksége lesz az index újraépítése. Az index újraépítése kell törli az indexet, hozza létre újból az indexet, és töltse be újra az adatokat.

> [!NOTE]
> Új mezőket adhat az Azure Search-index, anélkül, hogy az index újraépítése. Az új mező értéke null a következőnél az indexben már szereplő összes dokumentumot lesz.

Az index rendelkezésre állás fenntartása újjáépítést során, az index egy másik névvel, az ugyanazt a szolgáltatást egy példányát, vagy az index ugyanazzal a névvel, egy másik Service egy példányát, és átirányítása vagy feladatátvételi logika adja meg a kódban.

## <a name="disaster-recovery"></a>Vészhelyreállítás
Jelenleg nincs katasztrófa utáni helyreállítás esetén nincs beépített mechanizmus. Partíciók vagy a replikák hozzáadásával a nem megfelelő stratégiát, a vész-helyreállítási célok teljesítése lenne. A leggyakrabban használt módszer, amelyek a redundancia szolgáltatási szintű beállításával egy második keresési szolgáltatás egy másik régióban. Csakúgy, mint a rendelkezésre állás az indexkészítés során, az átirányítás vagy feladatátvételi logika kell származnia a kódot.

## <a name="increase-query-performance-with-replicas"></a>Növelje a lekérdezési teljesítmény replikákkal rendelkező
Lekérdezés késései azt jelzi, hogy további replikák van szükség. Általában a lekérdezési teljesítmény javításához első lépését, hogy adjon hozzá további ehhez az erőforráshoz. Replikák hozzáadásakor, további másolatot készít az index online állapotba nagyobb lekérdezési számítási feladatok és betölteni a kérelmeket elosztja a több replika keresztül.

Nehéz a becslések nem biztosítunk a lekérdezések másodpercenkénti (lekérdezési QPS): a lekérdezési teljesítmény függ a lekérdezés és a konkurens számítási feladatokhoz összetettségétől. Egyértelműen replikák hozzáadásával jobb teljesítményt eredményez, de az eredménye nem feltétlenül lineáris: három replika hozzáadása nem garantálja a háromszoros átviteli sebességet.

A számítási feladatok QPS becslése útmutatásért lásd: [Azure Search-teljesítmény és optimalizálás szempontok](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>A partíciók az indexelési teljesítmény növelése
Közel valós idejű adatok frissítése igénylő alkalmazások keresése, mint a replikák arányosan több partíciót kell. Partíciók hozzáadásával terjeszti az olvasási és írási műveletek nagyobb mennyiségű számítási erőforrások között. Azt is lehetővé teszi szabadítson fel több lemezterületet további indexek és dokumentumok tárolására.

A lekérdezés hosszabb ideig nagyobb indexeket. Mint ilyen Észreveheti, hogy minden egyes partíciók növekményes növekedése igényel a replikák kisebb, de arányos növekedése. A lekérdezések és a lekérdezés kötet összetettsége be, hogy mennyi idő alatt be van kapcsolva a lekérdezés végrehajtása lesz tényező.

## <a name="basic-tier-partition-and-replica-combinations"></a>Alapszintű csomag: partíció és a replika kombinációk
Egy egyszerű szolgáltatást is pontosan egy partícióval rendelkezik, és legfeljebb három replika, legfeljebb korlátozhatja három SUS. A csak állítható erőforrás a replikákat. A lekérdezések magas rendelkezésre állás érdekében legalább két replika van szüksége.

<a id="chart"></a>

## <a name="standard-tiers-partition-and-replica-combinations"></a>Standard szint: partíció és a replika kombinációk
Ez a táblázat bemutatja a SUS-t az összes Standard szint a replikákat és partíciókat, a 36-SU korlátozás kombinációit támogatásához szükséges.

|   | **1 partíció** | **2 partíció** | **3 partíció** | **4 partíciók** | **6 partíciók** | **12 partícióra** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replika** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 replika** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 replika** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 replikák** |4 SU |8 SU |12 SU |16 SU |24 SU |– |
| **5 replikák** |5 SU |10 SU |15 SU |20 SU |30 SU |– |
| **6 replika** |6 SU |12 SU |18 SU |24 SU |36 SU |– |
| **12 replikák** |12 SU |24 SU |36 SU |– |N/A |– |

SUS-t, a díjszabást és a kapacitás részletesen az Azure webhelyén. További információkért lásd: [díjszabása](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> 12 egyenletesen osztja a replikák és partíciók száma (pontosabban 1, 2, 3, 4, 6, 12). Ennek az oka az Azure Search előre osztja fel minden index 12 szegmensekre, hogy azt is között lehetnek elosztva egyenlő részeket az összes partíciót. Például ha a szolgáltatás három partícióval rendelkezik, és az index létrehozása, mindegyik partíció az index négy szegmensek fog tartalmazni. Egy index Azure Search-szegmensek egy implementálási részlete, hogy a későbbiekben változhat. Bár a szám 12 még ma, nem várt 12 mindig lesz a jövőben ezt a számot.
>
>

## <a name="billing-formula-for-replica-and-partition-resources"></a>A számlázás képlet replika és a partíció-erőforrások
Hány SUS-t használják kombinációjára kiszámítása képlete a replikákat és partíciókat, a termék vagy (az R-X P = SU). Például három replika készül három partíció megszorozza számlázása kilenc SUs-t.

A szint az alapszintű, mint a standard szintű alacsonyabb egységenkénti számlázási arány költség / SU határozza meg. Értékeli az egyes szintek találhatók [díjszabása](https://azure.microsoft.com/pricing/details/search/).
