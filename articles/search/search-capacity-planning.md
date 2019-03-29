---
title: Méretezési csoport partíciókat és -lekérdezési és indexelési - replikákat Azure keresése
description: Módosítsa a partíció- és a replika számítógépes erőforrások az Azure Search szolgáltatásban, ahol az egyes erőforrások a számlázható keresési egységek díjszabása.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6879dd975f97ba2746165e87a135e5d90e8b229f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620636"
---
# <a name="scale-partitions-and-replicas-for-query-and-indexing-workloads-in-azure-search"></a>Méretezési csoport partíciókat és -replikákat a lekérdezés és a számítási feladatok indexeléséhez az Azure Search
Miután [válasszon egy tarifacsomagot](search-sku-tier.md) és [egy keresési szolgáltatás kiépítése](search-create-service-portal.md), a következő lépés az, hogy szükség esetén a replikákat és partíciókat, a szolgáltatás által használt számának növelésére. Minden egyes számlázási egységek rögzített számú kínálja. Ez a cikk ismerteti azokat az egységeket optimális konfigurációt, amely elosztja a lekérdezés-végrehajtás, indexelési és tárolási követelményeinek elérésére lefoglalása.

Erőforrás-konfigurációban érhető el a szolgáltatás üzembe helyezésekor meg a [alapszintű csomag](https://aka.ms/azuresearchbasic) vagy az egyik a [Standard vagy a tárolásra optimalizált szint](search-limits-quotas-capacity.md). Ezek a csomagok, szolgáltatások, előállításának egységnyi növekményekben *keresési egységek* (su) Ha mindegyik partíciót és a replika számít-e egy SU. 

SUs kevesebb találatot használata egy arányosan alacsonyabb számlákat. A számlázás érvényben a mindaddig, amíg a szolgáltatás be van állítva. Átmenetileg nem használ olyan szolgáltatást, ha az egyetlen lehetőség a számlázás elkerülése érdekében a a szolgáltatás törlése, és ezután hozza létre újra szükség esetén.

> [!Note]
> Szolgáltatás törlése töröl minden rajta. Nincs a létesítmény biztonsági mentése az Azure Search belül van, és a keresési adatok visszaállítása megőrzött. Ismételt üzembe helyezése egy új szolgáltatás a meglévő index, hozhat létre és feltöltheti azokat eredetileg program kell futtatásakor. 

## <a name="terminology-replicas-and-partitions"></a>Terminológiai: replikák és partíciók
Replikák és partíciók olyan biztonsági másolatot egy keresési szolgáltatás elsődleges-erőforrásokat.

| Erőforrás | Meghatározás |
|----------|------------|
|*Partíciók* | Indexelt tárolási és i/o biztosít olvasási és írási műveletek (például amikor újraépítése vagy egy index frissítése).|
|*Replikák* | A keresési szolgáltatás elsősorban az egyenleg lekérdezési műveletek betöltéséhez használt példánya. Minden egyes replikának mindig futtatja az index egy példányát. Ha 12 replikákat, kell betölteni a szolgáltatás minden egyes indexnek 12 példányait.|

> [!NOTE]
> Nincs közvetlenül módosítására vagy kezelésére, mely indexek futtatunk egy replika mód. Minden egyes minden replika indexe egy példányát a service-architektúra része.
>


## <a name="how-to-allocate-replicas-and-partitions"></a>Replikák és partíciók lefoglalása
Az Azure Search szolgáltatás eredetileg lefoglalt egy partíciót és a egy replika erőforrások megadott minimális szintjét. Az azt támogató szolgáltatásaihoz fokozatosan módosíthatja számítási erőforrást növeli a partíciók számát, ha szükség van további tárolási és i/o, vagy nagyobb lekérdezési kötetek vagy a jobb teljesítmény érdekében több replika hozzáadása. Egy egyetlen szolgáltatást kell rendelkezik elegendő erőforrással (indexelése és lekérdezések) az összes számítási feladatok kezelésére. Több szolgáltatás számítási feladatokat nem feloszthatja.

Szeretné növelni, vagy a lefoglalást a replikák és partíciók, javasoljuk, az Azure portal használatával. A portál megköveteli a megengedett maximális korlát alatti maradjon kombinációk vonatkozó korlátozások. Ha szüksége van egy parancsprogram-alapú vagy a kód-alapú üzembe helyezési módszert használja, a [Azure PowerShell-lel](search-manage-powershell.md) vagy a [felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/services) alternatív megoldás.

Általában az alkalmazások keresése, mint a partíciók több replika kell, különösen akkor, ha a szolgáltatási műveletek vannak torzítatlan lekérdezési számítási feladatok felé. A szakasz a [magas rendelkezésre állású](#HA) ismerteti az okokat.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) , és válassza ki a keresési szolgáltatást.
2. A **beállítások**, nyissa meg a **méretezési** lap használatával módosíthatja a replikák és partíciók. 

   Az alábbi képernyőfelvételen egy standard szintű service, egy replikát, illetve a partíciót. A képlet alsó azt jelzi, hogy hány keresési egységek éppen használt (1). Ha azt az Egységár 100 USD (nem a tényleges díj) volt, ez a szolgáltatás futtatásának havi díja lesz 100 USD átlagosan.

   ![Méretezés oldal aktuális értékek megjelenítése](media/search-capacity-planning/1-initial-values.png "méretezés oldal aktuális értékek megjelenítése")

3. A csúszka segítségével növelje vagy csökkentse a partíciók számát. A képlet alsó azt jelzi, hogy hány keresési egységet használ.

   Ebben a példában kapacitás, a két replika megduplázódik, és minden egyes partíciók. Figyelje meg, hogy a keresési egység száma; Ez azért most négy a számlázási képlete megszorozza (2 x 2) a partíciók replikáit. Kapacitás kétszeresére több, mint a szolgáltatás költségének kétszeresére nő. Ha a keresés egységköltség 100 USD volt, az új havi számla most lenne 400 USD.

   Az egyes csomagok költségek a jelenlegi, látogasson el a [díjszabási oldalunkon](https://azure.microsoft.com/pricing/details/search/).

   ![Adja hozzá a replikák és partíciók](media/search-capacity-planning/2-add-2-each.png "adja hozzá a replikák és partíciók")

3. Kattintson a **mentése** , hagyja jóvá a módosításokat.

   ![Hagyja jóvá a módosításokat a méretezés és a számlázási](media/search-capacity-planning/3-save-confirm.png "hagyja jóvá a módosításokat a méretezés és számlázás")

   Kapacitás a módosítások végrehajtásához több órába telhet. A folyamat elindult, és a hiba nem a replika és a partíció korrekciókhoz nincs valós idejű figyelés nem szakítható meg. Azonban a következő üzenet marad látható, miközben a módosítások vannak folyamatban.

   ![A portálon állapotüzenet](media/search-capacity-planning/4-updating.png "állapotüzenet a portálon")


> [!NOTE]
> Egy szolgáltatás kiépítése, után azt egy magasabb szintű termékváltozatra nem frissíthető. A search szolgáltatás létrehozása az új rétegben kell, és töltse be újra az indexek. Lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) segítség a szolgáltatás üzembe helyezését.
>
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Partíció és a replika kombinációk

Egy egyszerű szolgáltatást is pontosan egy partícióval rendelkezik, és legfeljebb három replika, legfeljebb korlátozhatja három SUS. A csak állítható erőforrás a replikákat. A lekérdezések magas rendelkezésre állás érdekében legalább két replika van szüksége.

Minden standard szintű és tárolásra optimalizált keresési szolgáltatások feltételezheti, hogy a következő fájlattribútumok replikákat és partíciókat, a 36-SU korlát vonatkozik. 

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


<a id="HA"></a>

## <a name="high-availability"></a>Magas rendelkezésre állás
Mivel a szolgáltatás egyszerűen és viszonylag gyorsan vertikális, általában javasoljuk, hogy először a több partíciót és a egy vagy két replika, majd vertikális felskálázási lekérdezés kötetek létrehozása. Lekérdezési számítási feladatok futtatásához, elsősorban a replikákon. Ha további kapacitás vagy a magas rendelkezésre állású van szüksége, további replikák valószínűleg szüksége lesz.

Általános javaslatok a magas rendelkezésre állás érdekében a következők:

* Csak olvasható munkaterhelések (lekérdezések) magas rendelkezésre álláshoz két replika
* Három vagy több replikát, a magas rendelkezésre állású olvasási és írási számítási feladatok (lekérdezések és az indexelés, egyes dokumentumok hozzáadása, frissített vagy törölt)

Szolgáltatói szerződések (SLA) az Azure Search lekérdezési műveletek vagy egy index frissítéseit hozzáadása, frissítése vagy törlése a dokumentumok álló célozzák meg.

Alapszintű csomag korona egy partíciót és három replika készül. Ha azt szeretné, hogy az indexelés és a lekérdezési teljesítmény iránti igény ingadozása azonnal reagálni a rugalmasságot, fontolja meg a Standard csomagok valamelyikére.  Ha megtalálta a tárolási igényei folyamatosan nőnek sokkal gyorsabban, mint a lekérdezési teljesítmény, fontolja meg a tárolásra optimalizált csomagok valamelyikére.

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


## <a name="next-steps"></a>További lépések

[Válasszon egy tarifacsomagot az Azure Search](search-sku-tier.md)
