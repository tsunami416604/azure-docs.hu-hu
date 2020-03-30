---
title: Szolgáltatásfelügyelet a portálon
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search szolgáltatás, amely egy üzemeltetett felhőalapú keresési szolgáltatás a Microsoft Azure-ban az Azure Portalhasználatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3abbf2c8e0734d17aabadd2ae5f61cc03889964b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282925"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Az Azure Cognitive Search szolgáltatásfelügyelete az Azure Portalon
> [!div class="op_single_selector"]
> * [Powershell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portál](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Az Azure Cognitive Search egy teljes körűen felügyelt, felhőalapú keresési szolgáltatás, amely gazdag keresési élményt nyújt az egyéni alkalmazásokban. Ez a cikk ismerteti a szolgáltatás felügyeleti feladatokat, amelyeket az [Azure Portalon](https://portal.azure.com) egy már kiépített keresési szolgáltatás hoz. A szolgáltatásfelügyelet kialakítása szerint könnyű, a következő feladatokra korlátozódik:

> [!div class="checklist"]
> * A szolgáltatás olvasási vagy írási hozzáféréséhez használt *api-kulcsokhoz* való hozzáférés kezelése.
> * A partíciók és replikák lefoglalásának módosításával módosíthatja a szolgáltatási kapacitást.
> * Erőforrás-használat figyelése, a szolgáltatási szint maximális korlátaihoz viszonyítva.

Figyelje meg, hogy a *frissítés* nem szerepel felügyeleti feladatként. Mivel az erőforrások a szolgáltatás kiépítésekor kerülnek lefoglalva, egy másik szintre való áthelyezéshez új szolgáltatásra van szükség. További információt az [Azure Cognitive Search szolgáltatás létrehozása című témakörben talál.](search-create-service-portal.md)

Figyelheti a lekérdezés iménykötetét és más metrikákat, és ezekkel az elemzésekkel módosíthatja a szolgáltatást a gyorsabb válaszidő érdekében. További információ: [Használati és lekérdezési mutatók figyelése,](search-monitor-usage.md) [teljesítmény és optimalizálás.](search-performance-optimization.md)

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Rendszergazdai jogok
Kiépítése vagy leszerelése a szolgáltatás maga is elvégezheti az Azure-előfizetés rendszergazdája vagy társ-rendszergazda.

A szolgáltatáson belül bárki, aki hozzáfér a szolgáltatás URL-címéhez és egy rendszergazdai api-kulcs hoz olvasási és írási hozzáféréssel rendelkezik a szolgáltatáshoz. Az írás-olvasási hozzáférés lehetővé teszi kiszolgálóobjektumok hozzáadását, törlését vagy módosítását, beleértve az API-kulcsokat, indexeket, indexezőket, adatforrásokat, ütemezéseket és szerepkör-hozzárendeléseket az [RBAC által definiált szerepkörökben megvalósítva.](search-security-rbac.md)

Az Azure Cognitive Search szolgáltatással való összes felhasználói interakció a következő módok egyikébe tartozik: a szolgáltatás olvasási-írási hozzáférése (rendszergazdai jogok) vagy csak olvasható hozzáférés a szolgáltatáshoz (lekérdezési jogok). További információ: [Az api-kulcsok kezelése.](search-security-api-keys.md)

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Naplózás és rendszerinformációk
Az Azure Cognitive Search nem teszi elérhetővé az egyes szolgáltatások naplófájljait a portálon vagy az automatizált felületeken keresztül. Az alapszintű és újabb szinten a Microsoft figyeli az összes Azure Cognitive Search-szolgáltatást a szolgáltatásiszint-szerződések (SLA) 99,9%-os rendelkezésre állása érdekében. Ha a szolgáltatás lassú, vagy a kérelem átviteli túláraja az SLA-küszöbértékek alá esik, a támogatási csoportok áttekintik a számukra elérhető naplófájlokat, és megoldják a problémát.

Ami a szolgáltatással kapcsolatos általános információkat illeti, az alábbi módokon szerezhet idát:

* A portálon, a szolgáltatás irányítópultján, értesítéseken, tulajdonságokon és állapotüzeneteken keresztül.
* A [PowerShell](search-manage-powershell.md) vagy a [Felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/) használatával [szolgáltatástulajdonságok lehívása](https://docs.microsoft.com/rest/api/searchmanagement/services), vagy állapot index erőforrás-használat.


<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Erőforrás-használat figyelése
Az irányítópulton az erőforrás-figyelés a szolgáltatás irányítópultján látható információkra és néhány metrikára korlátozódik, amelyek a szolgáltatás lekérdezésével szerezhetők be. A szolgáltatás irányítópultján a Használat szakaszban gyorsan meghatározhatja, hogy a partíciós erőforrásszintek megfelelőek-e az alkalmazásszámára. Külső erőforrásokat, például az Azure figyelést, ha rögzíteni és megóbisolni szeretné a naplózott eseményeket. További információ: [Monitoring Azure Cognitive Search](search-monitor-usage.md).

A REST API keresési szolgáltatás használatával programozott módon megkaphatja a dokumentumok és indexek számát: 

* [Indexstatisztika beszerezni](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Bizonylatok megszámlálása](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Katasztrófa utáni helyreállítás és szolgáltatáskimaradások

Bár az adatok menthetők, az Azure Cognitive Search nem biztosít azonnali feladatátvételt a szolgáltatás, ha a fürt vagy az adatközpont szintjén kimaradás van. Ha egy fürt meghibásodik az adatközpontban, az operatív csapat észleli és dolgozik a szolgáltatás visszaállításán. A szolgáltatás visszaállítása során állásidőt fog tapasztalni, de a szolgáltatási [szintszerződés (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/)szerint a szolgáltatás elérhetetlensítésének kompenzálására szolgáltatáskrediteket kérhet. 

Ha folyamatos szolgáltatásra van szükség a Microsoft hatáskörén kívül álló katasztrofális hibák esetén, [kiépíthet egy további szolgáltatást](search-create-service-portal.md) egy másik régióban, és végrehajthat egy georeplikációs stratégiát annak biztosítására, hogy az indexek teljesen redundánsak legyenek az összes szolgáltatásban.

Az indexek feltöltésére és frissítésére [indexeket](search-indexer-overview.md) használó ügyfelek ugyanazzal az adatforrással rendelkező geospecifikus indexelőksegítségével is kezelhetik a vészhelyreállítást. Két különböző régióbeli szolgáltatás, amelyek mindegyike indexelőt futtat, indexelheti ugyanazt az adatforrást a georedundancia elérése érdekében. Ha olyan adatforrásokból indexel, amelyek georedundáns, vegye figyelembe, hogy az Azure Cognitive Search indexelők csak az elsődleges replikákniain csak növekményes indexelést (új, módosított vagy törölt dokumentumok frissítéseit egyesítése) végezhetnek. Feladatátvételi esemény esetén győződjön meg arról, hogy az indexelő az új elsődleges replika. 

Ha nem használ indexelők, akkor az alkalmazáskód segítségével leküldéses objektumok és adatok különböző keresési szolgáltatások párhuzamosan. További információ: [Teljesítmény és optimalizálás az Azure Cognitive Search ben.](search-performance-optimization.md)

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

Mivel az Azure Cognitive Search nem elsődleges adattárolási megoldás, nem biztosítunk hivatalos mechanizmust az önkiszolgáló biztonsági mentéshez és visszaállításhoz. Azonban használhatja az **index-backup-restore** mintakódot ebben az [Azure Cognitive Search .NET minta tárház](https://github.com/Azure-Samples/azure-search-dotnet-samples) biztonsági másolatot készíthet az indexdefinícióról és a Pillanatképről egy sor JSON-fájlra, majd szükség esetén ezekkel a fájlokkal visszaállíthatja az indexet. Ez az eszköz is áthelyezheti az indexeket a szolgáltatási szintek között.

Ellenkező esetben az index létrehozásához és feltöltéséhez használt alkalmazáskód a de facto visszaállítási lehetőség, ha véletlenül töröl egy indexet. Az index újraépítéséhez törölnie kell azt (feltéve, hogy létezik), újra létre kell hoznia az indexet a szolgáltatásban, és újra be kell töltenie az adatokat az elsődleges adattárból.

<a id="scale"></a>

## <a name="scale-up-or-down"></a>Vertikális fel- és leskálázás
Minden keresési szolgáltatás legalább egy replika és egy partícióval kezdődik. Ha dedikált [erőforrásokat tartalmazó rétegre](search-limits-quotas-capacity.md)regisztrált, kattintson a **méretezési** csempére a szolgáltatás irányítópultján az erőforrás-használat módosításához.

Ha kapacitást ad hozzá bármelyik erőforráson keresztül, a szolgáltatás automatikusan használja őket. Nincs szükség további műveletre az Ön részéről, de van egy kis késés, mielőtt az új erőforrás hatása megvalósulna. További erőforrások kiépítése 15 percet vagy többet is igénybe vehet.

 ![][10]

### <a name="add-replicas"></a>Replikák hozzáadása
A másodpercenkénti lekérdezések (QPS) növelése vagy a magas rendelkezésre állás elérése replikák hozzáadásával történik. Minden replika rendelkezik egy index másolatával, így egy újabb replika hozzáadása egy további indexre van lefordítva, amely a szolgáltatás lekérdezési kérelmek kezeléséhez érhető el. A magas rendelkezésre álláshoz legalább 3 replikára van szükség (a részletekért lásd a [Kapacitástervezés](search-capacity-planning.md) című témakört).

A több replikával rendelkező keresési szolgáltatás nagyobb számú indexen keresztül töltheti be a lekérdezési kérelmeket. A lekérdezési kötet szintje miatt a lekérdezési átviteli sebessége gyorsabb lesz, ha az index több példányban érhető el a kérelem kiszolgálásához. Ha lekérdezési késést tapasztal, akkor számíthat a teljesítményre, ha a további replikák online állapotba kerülnek.

Bár a lekérdezési átviteli rendszer replikák hozzáadásakor felmegy, nem pontosan dupla vagy háromszoros, ahogy replikák hozzáadása a szolgáltatáshoz. Minden keresési alkalmazás olyan külső tényezőknek van kitéve, amelyek hatással lehetnek a lekérdezés teljesítményére. Az összetett lekérdezések és a hálózati késés két tényező, amelyek hozzájárulnak a lekérdezési válaszidők változásaihoz.

### <a name="add-partitions"></a>Partíciók hozzáadása
A legtöbb szolgáltatásalkalmazás nak nincs szüksége a partíciók helyett több replikára. Azokban az esetekben, ahol a megnövekedett dokumentumszám szükséges, partíciókat adhat hozzá, ha feliratkozott a standard szolgáltatásra. Az alapszintű szint nem biztosít további partíciókat.

A standard szinten a partíciók 12 többszörösei (konkrétan 1, 2, 3, 4, 6 vagy 12) többszörösei. Ez a szilánkok lelete. Az index 12 szegmensben jön létre, amelyek mindegyike 1 partíción tárolható, vagy egyenlően osztható 2, 3, 4, 6 vagy 12 partícióra (partíciónként egy shard).

### <a name="remove-replicas"></a>Replikák eltávolítása
A nagy lekérdezési kötetek időszakai után a csúszkával csökkentheti a replikákat, miután a keresési lekérdezés terhelései normalizálódtak (például az ünnepi értékesítések befejezése után). Nincs szükség további lépésekre az Ön részéről. A replikaszám csökkentése lemondja a virtuális gépekről az adatközpontban. A lekérdezési és adatbetöltési műveletek mostantól kevesebb virtuális gépen fognak futni, mint korábban. A minimális követelmény egy replika.

### <a name="remove-partitions"></a>Partíciók eltávolítása
A replikák eltávolításával ellentétben, amely nem igényel további erőfeszítést az Ön részéről, előfordulhat, hogy van némi tennivalója, ha több tárhelyet használ, mint amennyit csökkenteni lehet. Ha például a megoldás három partíciót használ, egy vagy két partícióra való leépítés hibát generál, ha az új tárhely kisebb, mint az index üzemeltetéséhez szükséges. Ahogy az várható, a választás az, hogy törölje az indexek vagy dokumentumok egy társított indexen belül, hogy helyet szabadítson fel, vagy tartsa meg az aktuális konfigurációt.

Nincs olyan észlelési módszer, amely megmondja, hogy mely indexszilánkok vannak tárolva adott partíciókon. Minden partíció körülbelül 25 GB-os tárhelyet biztosít, ezért a tárolót olyan méretre kell csökkentenie, amely a partíciók száma szerint elhelyezhető. Ha azt szeretné, hogy egy partíciót, mind a 12 szilánkok kell illeszkednie.

A jövőbeli tervezés érdekében érdemes ellenőrizni a tárolást (a [Tárgymutató-statisztika bekerülése)](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)segítségével, hogy megtudja, ténylegesen mennyit használt. 

<a id="next-steps"></a>

## <a name="next-steps"></a>További lépések
Miután megértette a szolgáltatásfelügyelet mögötti fogalmakat, fontolja meg a [PowerShell](search-manage-powershell.md) használatát a feladatok automatizálásához.

Javasoljuk továbbá a [teljesítmény- és optimalizálási cikk áttekintését.](search-performance-optimization.md)

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



