---
title: Partíciók és replikák vertikális felskálázása a lekérdezési és indexelési munkaterhelések kapacitásának hozzáadásához
titleSuffix: Azure Cognitive Search
description: A partíciós és a replika számítógép erőforrásainak módosítása az Azure Cognitive Searchban, ahol az egyes erőforrások számlázható keresési egységekben vannak.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8613ddc668df338c4f96a9d37f32120718513925
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792500"
---
# <a name="scale-up-partitions-and-replicas-to-add-capacity-for-query-and-index-workloads-in-azure-cognitive-search"></a>Partíciók és replikák vertikális felskálázása az Azure-beli lekérdezési és indexelési feladatok kapacitásának növeléséhez Cognitive Search

Miután [kiválasztotta a díjszabási szintet](search-sku-tier.md) , és [kiépít egy keresési szolgáltatást](search-create-service-portal.md), a következő lépés a szolgáltatás által használt replikák vagy partíciók számának megadása. Az egyes szintek rögzített számú számlázási egységet biztosítanak. Ez a cikk bemutatja, hogyan oszthatja ki ezeket az egységeket olyan optimális konfiguráció eléréséhez, amely kiegyenlíti a lekérdezések végrehajtásához, indexeléséhez és tárolásához szükséges követelményeket.

Az erőforrás-konfiguráció akkor érhető el, ha az [alapszintű](https://aka.ms/azuresearchbasic) vagy a [standard vagy a Storage optimalizált szintjein](search-limits-quotas-capacity.md)állít be szolgáltatást. Ezen rétegek szolgáltatásai esetében a kapacitás a *keresési egységek* (SUs) növekményei között vásárolható meg, ahol az egyes partíciók és replikák egy Su-ként számítanak. 

Ha kevesebb SUs-eredményt kíván használni, egy arányosan alacsonyabb számlán kell lennie. A számlázás mindaddig érvényes, amíg a szolgáltatás be van állítva. Ha átmenetileg nem használ szolgáltatást, a számlázás elkerülésének egyetlen módja, ha törli a szolgáltatást, majd újra létrehozza azt, amikor szüksége van rá.

> [!Note]
> A szolgáltatás törlésével mindent töröl. Az Azure Cognitive Searchon belül nem áll rendelkezésre a megőrzött keresési adatbiztonsági mentés és helyreállítás. Egy meglévő index új szolgáltatáson való újbóli üzembe helyezéséhez futtassa az eredeti létrehozásához és betöltéséhez használt programot. 

## <a name="terminology-replicas-and-partitions"></a>Terminológia: replikák és partíciók
A replikák és partíciók a keresési szolgáltatást támogató elsődleges erőforrások.

| Erőforrás | Meghatározás |
|----------|------------|
|*Partíciók* | Az írási és olvasási műveletekhez (például az indexek újraépítésekor vagy frissítésekor) index-tárolót és I/O-t biztosít.|
|*Replikák* | A keresési szolgáltatás azon példányai, amelyek elsődlegesen a lekérdezési műveletek terheléselosztására szolgálnak. Minden replika mindig az index egy példányát tárolja. 12 replika esetén a szolgáltatásban betöltött összes index 12 példánya lesz.|

> [!NOTE]
> A replikán futtatott indexek közvetlen módosítása és kezelése nem végezhető el. Az egyes replikák egy példánya a szolgáltatási architektúra részét képezi.
>


## <a name="how-to-allocate-replicas-and-partitions"></a>Replikák és partíciók lefoglalása
Az Azure Cognitive Search-ban a szolgáltatás eredetileg egy partícióból és egy replikából álló minimális szintű erőforrásokat foglal le. Az azt támogató rétegek esetén a partíciók növelésével fokozatosan állíthatja be a számítási erőforrásokat, ha több tárterületre és I/O-ra van szüksége, vagy további replikákat ad hozzá a nagyobb lekérdezési kötetekhez vagy jobb teljesítményhez. Egyetlen szolgáltatásnak elegendő erőforrással kell rendelkeznie az összes számítási feladat (indexelés és lekérdezések) kezeléséhez. A számítási feladatok több szolgáltatás között nem oszthatók ki.

A replikák és partíciók kiosztásának növeléséhez vagy módosításához javasolt a Azure Portal használata. A portál a maximális határértékek alatti megengedett kombinációk korlátozásait kényszeríti ki. Ha parancsfájl-vagy programkód-alapú létesítési megközelítésre van szüksége, akkor a [Azure PowerShell](search-manage-powershell.md) vagy a [felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/services) alternatív megoldás.

Általában a keresési alkalmazásoknak több replikára van szükségük, mint a partíciók, különösen akkor, ha a szolgáltatási műveletek a lekérdezési munkaterhelések szempontjából elfogultak. A [magas rendelkezésre állásról](#HA) szóló szakasz ismerteti, hogy miért.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és válassza ki a keresési szolgáltatást.

2. A **Beállítások**területen nyissa meg a **skálázás** lapot a replikák és partíciók módosításához. 

   Az alábbi képernyőfelvételen egy replikával és partícióval kiépített szabványos szolgáltatás látható. A lenti képlet azt jelzi, hogy hány keresési egység van használatban (1). Ha az egység ára $100 (nem valós díj), akkor a szolgáltatás futtatásának havi költsége átlagosan $100.

   ![Az aktuális értékeket mutató méretezési oldal](media/search-capacity-planning/1-initial-values.png "Az aktuális értékeket mutató méretezési oldal")

3. A csúszka használatával növelheti vagy csökkentheti a partíciók számát. A lenti képlet azt jelzi, hogy hány keresési egységet használ a rendszer.

   Ez a példa dupla kapacitást, két replikával és partícióval rendelkezik. Figyelje meg a keresési egységek darabszámát; most négy, mert a számlázási képlet a replikák szorozza a partíciókkal (2 x 2). A kapacitás megkettőzése több, mint megduplázza a szolgáltatás futtatásának költségeit. Ha a keresési egység díja $100 volt, az új havi számla mostantól $400 lesz.

   Az egyes szintek aktuális egységenkénti költségeiért látogasson el a [díjszabási oldalra](https://azure.microsoft.com/pricing/details/search/).

   ![Replikák és partíciók hozzáadása](media/search-capacity-planning/2-add-2-each.png "Replikák és partíciók hozzáadása")

3. Kattintson a **Save (Mentés** ) gombra a módosítások megerősítéséhez.

   ![A skálázás és a számlázás változásainak megerősítése](media/search-capacity-planning/3-save-confirm.png "A skálázás és a számlázás változásainak megerősítése")

   A kapacitás változásai több órát is igénybe vehetik. A folyamat elindítása után nem lehet megszakítani a műveletet, és nincs valós idejű figyelés a replika és a partíciók beállításaihoz. A következő üzenet azonban továbbra is látható, amíg megváltozik.

   ![Állapotjelző üzenet a portálon](media/search-capacity-planning/4-updating.png "Állapotjelző üzenet a portálon")


> [!NOTE]
> A szolgáltatás üzembe helyezését követően nem lehet magasabb SKU-ra frissíteni. Létre kell hoznia egy keresési szolgáltatást az új szinten, és újra kell töltenie az indexeket. A szolgáltatás kiépítési támogatásához tekintse meg az [Azure Cognitive Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) című témakört.
>
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Partíció-és replika-kombinációk

Az alapszintű szolgáltatásoknak pontosan egy partíciójuk és legfeljebb három replikájuk lehet, legfeljebb három SUs esetén. Az egyetlen állítható erőforrás replika. A lekérdezésekben a magas rendelkezésre állás érdekében legalább két replikára van szükség.

A standard és a Storage-alapú optimalizált keresési szolgáltatások a 36-SU korlátnak megfelelő replikák és partíciók következő kombinációit vehetik igénybe. 

|   | **1 partíció** | **2 partíció** | **3 partíció** | **4 partíció** | **6 partíció** | **12 partíció** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replika** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 replika** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 replika** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 replika** |4 SU |8 SU |12 SU |16 SU |24 SU |– |
| **5 replika** |5 SU |10 SU |15 SU |20 SU |30 SU |– |
| **6 replika** |6 SU |12 SU |18 SU |24 SU |36 SU |– |
| **12 replika** |12 SU |24 SU |36 SU |– |– |– |

Az SUs, a díjszabás és a kapacitás részletes ismertetését az Azure webhelyén találja. További információkért tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> A replikák és partíciók száma egyenletesen oszlik el a 12 (pontosabban, 1, 2, 3, 4, 6, 12). Ennek az az oka, hogy az Azure Cognitive Search az egyes indexeket 12 szegmensre osztja, így a partíciók között egyenlő arányban terjedhet. Ha például a szolgáltatás három partícióval rendelkezik, és létrehoz egy indexet, minden partíció az index négy szegmensét fogja tartalmazni. Az Azure Cognitive Search az indexek egy implementációs részletességgel rendelkeznek, amely a jövőbeli kiadásokban változhat. Bár a szám 12 ma, nem várható, hogy ez a szám mindig 12 lesz a jövőben.
>


<a id="HA"></a>

## <a name="high-availability"></a>Magas rendelkezésre állás
Mivel a méretezés egyszerűen és viszonylag gyorsan megoldható, általában azt javasoljuk, hogy először egy partíciót és egy vagy két replikát válasszon, majd a skálázási folyamat létrehozásakor. A lekérdezési feladatok elsődlegesen a replikákat futtatják. Ha nagyobb átviteli sebességre vagy magas rendelkezésre állásra van szüksége, valószínűleg további replikákat kell megadnia.

A magas rendelkezésre állásra vonatkozó általános javaslatok a következők:

* Két replika a csak olvasási terhelések magas rendelkezésre állásához (lekérdezések)

* Három vagy több replika az olvasási/írási munkaterhelések magas rendelkezésre állásához (lekérdezések és az indexelés egyéni dokumentumok hozzáadása, frissítése vagy törlése)

Az Azure Cognitive Search szolgáltatói szerződések (SLA) a lekérdezési műveletekre és a dokumentumok hozzáadását, frissítését vagy törlését tartalmazó index-frissítésekre irányulnak.

Az alapszintű csomag egy partíción és három replikán található. Ha azt szeretné, hogy a rugalmasság azonnal reagáljon az indexelési és a lekérdezési átviteli sebesség igény szerinti ingadozására, vegye figyelembe az egyik standard szintet is.  Ha úgy találja, hogy a tárolási követelmények sokkal gyorsabban növekednek, mint a lekérdezési átviteli sebesség, vegye figyelembe a tárterület optimalizált szintjeinek egyikét.

### <a name="index-availability-during-a-rebuild"></a>Az index rendelkezésre állása Újraépítés közben

Az Azure Cognitive Search magas rendelkezésre állása olyan lekérdezésekre és index-frissítésekre vonatkozik, amelyek nem járnak az indexek újraépítésekor. Ha töröl egy mezőt, módosít egy adattípust, vagy átnevez egy mezőt, újra létre kell hoznia az indexet. Az index újraépítéséhez törölnie kell az indexet, újra létre kell hoznia az indexet, és újra kell töltenie az adatfájlokat.

> [!NOTE]
> Az index újraépítése nélkül új mezőket adhat hozzá egy Azure Cognitive Search indexhez. Az új mező értéke NULL lesz az indexben már szereplő összes dokumentum esetében.

Ahhoz, hogy az index rendelkezésre álljon az Újraépítés során, az index egy másik névvel kell rendelkeznie ugyanazon a szolgáltatáson, vagy az index egy másolata ugyanazzal a névvel egy másik szolgáltatáson, majd a kódban át kell adni az átirányítási vagy feladatátvételi logikát.

## <a name="disaster-recovery"></a>Vészhelyreállítás
Jelenleg nincs beépített mechanizmus a vész-helyreállításhoz. Partíciók vagy replikák hozzáadása nem megfelelő stratégiát jelentene a vész-helyreállítási célkitűzések teljesítéséhez. A leggyakoribb módszer a redundancia a szolgáltatási szinten való hozzáadása egy másik régióban található második keresési szolgáltatás beállításával. Az indexek újraépítése során a rendelkezésre álláshoz hasonlóan az átirányítás vagy a feladatátvételi logikának a kódból kell származnia.

## <a name="increase-query-performance-with-replicas"></a>A lekérdezési teljesítmény fokozása replikákkal
A lekérdezés késése azt jelzi, hogy további replikák szükségesek. Általánosságban elmondható, hogy a lekérdezési teljesítmény javításának első lépése a további erőforrás hozzáadása. A replikák hozzáadásakor az index további példányai online állapotba kerülnek a nagyobb lekérdezési feladatok támogatásához és a több replikán keresztüli kérelmek elosztásához.

A lekérdezések másodpercenkénti számát (QPS) nem lehet megbecsülni: a lekérdezési teljesítmény a lekérdezés és a versengő számítási feladatok összetettsége alapján változhat. Bár a replikák hozzáadása egyértelműen jobb teljesítményt eredményez, az eredmény nem feltétlenül lineáris: három replika hozzáadása nem garantálja a háromszoros átviteli sebességet.

A számítási feladatok QPS becslésével kapcsolatos útmutatásért lásd: az [Azure Cognitive Search teljesítmény-és optimalizálási szempontjai](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Az indexelési teljesítmény javítása a partíciókkal
A közel valós idejű adatfrissítést igénylő alkalmazások kereséséhez a replikák több partícióra van szükségük. A partíciók hozzáadásával az írási/olvasási műveletek nagyobb számú számítási erőforráson keresztül terjednek ki. Emellett további lemezterületet biztosít a további indexek és dokumentumok tárolásához.

A nagyobb indexek lekérése hosszabb időt vesz igénybe. Ezért előfordulhat, hogy a partíciók növekményes növekedésének a replikák kisebb, de arányos növekedése szükséges. A lekérdezések és a lekérdezési kötetek összetettsége azt eredményezi, hogy milyen gyorsan történik a lekérdezés végrehajtása.


## <a name="next-steps"></a>Következő lépések

[Válasszon árképzési szintet az Azure Cognitive Search](search-sku-tier.md)
