---
title: Service Administration Azure-Cognitive Search a portálon
titleSuffix: Azure Cognitive Search
description: Felügyelheti az Azure Cognitive Search szolgáltatást, amely egy üzemeltetett felhőalapú keresési szolgáltatás a Microsoft Azureon a Azure Portal használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 052d772c8ef885d594146d456ebb3cdcbbc0e383
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793502"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Szolgáltatás-felügyelet az Azure Cognitive Search a Azure Portal
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Az Azure Cognitive Search egy teljes körűen felügyelt, felhőalapú keresési szolgáltatás, amellyel gazdag keresési élményt hozhat létre egyéni alkalmazásokba. Ez a cikk ismerteti azokat a szolgáltatás-felügyeleti feladatokat, amelyeket elvégezhet a [Azure Portalban](https://portal.azure.com) egy már üzembe helyezett keresési szolgáltatáshoz. A szolgáltatás felügyeletét könnyű megtervezni, a következő feladatokra korlátozódik:

> [!div class="checklist"]
> * A szolgáltatáshoz való olvasási vagy írási hozzáféréshez használt *API-kulcsok* elérésének kezelése.
> * A szolgáltatás kapacitása a partíciók és a replikák kiosztásának módosításával állítható be.
> * Az erőforrás-használat figyelése a szolgáltatási szintek maximális korlátaihoz képest.

Figyelje meg, hogy a *frissítés* nem rendszergazdai feladatként van felsorolva. Mivel a szolgáltatás kiosztásakor az erőforrások le vannak foglalva, a másik szintjére való áttéréshez új szolgáltatásra van szükség. További információ: [Azure Cognitive Search szolgáltatás létrehozása](search-create-service-portal.md).

Megfigyelheti a lekérdezési köteteket és egyéb mérőszámokat, és ezekkel az adatokkal módosíthatja a szolgáltatást a gyorsabb válaszidő érdekében. További információ: a [használat és a lekérdezés metrikáinak](search-monitor-usage.md) és [teljesítményének és optimalizálásának](search-performance-optimization.md)figyelése.

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Rendszergazdai jogosultságok
A szolgáltatás kiépítés vagy leszerelése egy Azure-előfizetés rendszergazdája vagy egy társ-rendszergazda által végezhető el.

A szolgáltatáson belül bárki, aki hozzáféréssel rendelkezik a szolgáltatás URL-címéhez, és a felügyeleti API-kulcs írási-olvasási hozzáféréssel rendelkezik a szolgáltatáshoz. Az írási és olvasási hozzáférés lehetővé teszi a kiszolgálói objektumok hozzáadását, törlését és módosítását, beleértve az API-kulcsokat, az indexeket, az indexelő, az adatforrásokat, az ütemterveket és a szerepkör-hozzárendeléseket, amelyeket a [RBAC-definiált szerepkörökkel](search-security-rbac.md)implementáltak

Az Azure Cognitive Search összes felhasználói interakciója a következő módok egyikén esik: írási-olvasási hozzáférés a szolgáltatáshoz (rendszergazdai jogosultságok), vagy csak olvasási hozzáférés a szolgáltatáshoz (lekérdezési jogosultságok). További információ: [az API-kulcsok kezelése](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Naplózási és rendszerinformációk
Az Azure Cognitive Search a portálon vagy a programozott felületen keresztül nem teszi elérhetővé az egyes szolgáltatások naplófájljait. Az alapszintű és újabb verziók esetében a Microsoft minden Azure Cognitive Search-szolgáltatást az 99,9%-os rendelkezésre állást figyeli a szolgáltatói szerződések (SLA) esetében. Ha a szolgáltatás lassú, vagy az átviteli sebesség az SLA-küszöbérték alá esik, a támogatási csapatoknak tekintse át a rendelkezésre álló naplófájlokat, és foglalkozzon a probléma megoldásával.

A szolgáltatással kapcsolatos általános információk tekintetében a következő módokon szerezhet be információkat:

* A portálon, a szolgáltatás irányítópultján, az értesítések, a tulajdonságok és az állapotüzenetek használatával.
* A [PowerShell](search-manage-powershell.md) vagy a [felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/) használatával [lekérheti a szolgáltatás tulajdonságait](https://docs.microsoft.com/rest/api/searchmanagement/services), vagy megtekintheti az indexelés erőforrás-használat állapotát.


<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Erőforrás-használat figyelése
Az irányítópulton az erőforrás-figyelés a szolgáltatás irányítópultján megjelenő információkra korlátozódik, és néhány mérőszámot, amelyet a szolgáltatás lekérdezésével érhet el. A szolgáltatás irányítópultjának használat szakaszában gyorsan meghatározhatja, hogy a partíciós erőforrások szintje megfelelő-e az alkalmazáshoz. Ha a naplózott eseményeket szeretné rögzíteni és megőrizni, külső erőforrásokat (például az Azure-figyelést) is kiépítheti. További információ: az [Azure Cognitive Search figyelése](search-monitor-usage.md).

A Search szolgáltatás REST API használatával a dokumentumok és indexek száma programozott módon is elvégezhető: 

* [Index statisztikáinak beolvasása](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Dokumentumok számlálása](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Vész-helyreállítási és szolgáltatás-kimaradások

Bár az adatai megmentését is lehetővé teszi, az Azure Cognitive Search nem biztosítja a szolgáltatás azonnali feladatátvételét, ha a fürt vagy az adatközpont szintjén áramkimaradás történik. Ha egy fürt meghibásodik az adatközpontban, az operatív csapat felismeri és a szolgáltatás visszaállítását végzi. A szolgáltatás visszaállítása során állásidőt tapasztalhat, de a szolgáltatási kreditek igénylésével kompenzálhatja a szolgáltatás nem rendelkezésre állását a [szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Ha a Microsoft általi ellenőrzésen kívüli katasztrofális hibák esetén folyamatos szolgáltatásra van szükség, egy másik régióban is [kiépítheti a további szolgáltatásokat](search-create-service-portal.md) , és egy geo-replikációs stratégiát valósíthat meg annak biztosításához, hogy az indexek teljesen redundánsak legyenek. az összes szolgáltatáson keresztül.

Azok az ügyfelek, akik [Indexelő](search-indexer-overview.md) adatokat használnak az indexek feltöltéséhez és frissítéséhez, az azonos adatforrást használó geo-specifikus indexelő segítségével kezelhetik a vész-helyreállítást. A különböző régiókban található két szolgáltatás, amelyek mindegyike indexelt, indexelheti ugyanazt az adatforrást a Geo-redundancia eléréséhez. Ha olyan adatforrásokból származó indexelést is használ, amelyek földrajzilag redundánsak, vegye figyelembe, hogy az Azure Cognitive Search indexelő csak az elsődleges replikák növekményes indexelését hajthatja végre. Feladatátvételi esemény esetén ügyeljen arra, hogy az indexelő újra az új elsődleges replikára irányítsa. 

Ha nem használ indexelő funkciót, az alkalmazás kódjával párhuzamosan küldheti el az objektumokat és az adatait a különböző keresési szolgáltatásoknak. További információ: [teljesítmény és optimalizálás az Azure Cognitive Searchban](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

Mivel az Azure Cognitive Search nem elsődleges adattárolási megoldás, nem biztosítunk formális mechanizmust az önkiszolgáló biztonsági mentéshez és visszaállításhoz. Az [Azure Cognitive Search .net minta](https://github.com/Azure-Samples/azure-search-dotnet-samples) -tárházban található **index-Backup-Restore** mintakód használatával azonban az index definícióját és a pillanatképet egy sor JSON-fájlra is felhasználhatja, majd ezekkel a fájlokkal visszaállíthatja az indexet, ha szükséges. Ez az eszköz az indexeket is át tudja helyezni a szolgáltatási szintek között.

Ellenkező esetben az index létrehozásához és feltöltéséhez használt alkalmazás kódja a de facto Visszaállítási lehetőség, ha tévedésből töröl egy indexet. Az indexek újraépítéséhez törölnie kell azt (feltéve, hogy létezik), újra létre kell hoznia az indexet a szolgáltatásban, majd újra kell töltenie az adatok elsődleges adattárból való beolvasásával.

<a id="scale"></a>

## <a name="scale-up-or-down"></a>Felfelé és lefelé skálázás
Minden keresési szolgáltatás legalább egy replikával és egy partícióval indul el. Ha olyan platformra regisztrált, [amely dedikált erőforrásokat biztosít](search-limits-quotas-capacity.md), a szolgáltatás Irányítópultján kattintson a **Méretezés** csempére az erőforrás-használat módosításához.

Ha erőforráson keresztül ad hozzá kapacitást, a szolgáltatás automatikusan ezeket használja. Az Ön részéről nincs szükség további műveletre, de az új erőforrás hatásának megvalósulása némi késéssel jár. További erőforrások kiépítéséhez akár 15 percet is igénybe vehet.

 ![][10]

### <a name="add-replicas"></a>Replikák hozzáadása
A lekérdezések másodpercenkénti száma (QPS) vagy a magas rendelkezésre állás elérése a replikák hozzáadásával történik. Minden replika egyetlen másolattal rendelkezik egy indexből, így a szolgáltatás lekérdezési kéréseinek kiszolgálásához még egy replika hozzáadására van lehetőség. A magas rendelkezésre álláshoz legalább 3 replika szükséges (lásd a [kapacitás megtervezését](search-capacity-planning.md) a részletekért).

A több replikát tartalmazó keresési szolgáltatás a lekérdezési kérelmeket nagyobb számú indexnél tudja betölteni. A lekérdezési mennyiség szintje miatt a lekérdezési átviteli sebesség gyorsabb lesz, ha a kérés kiszolgálásához több példány is rendelkezésre áll az indexben. Ha a lekérdezés késését tapasztalja, pozitív hatással lehet a teljesítményre, ha a további replikák online állapotban vannak.

Bár a lekérdezési átviteli sebesség a replikák hozzáadásakor leáll, a replikák szolgáltatáshoz való hozzáadásakor nem lehet pontosan dupla vagy tripla. Az összes keresési alkalmazásra olyan külső tényezők vonatkoznak, amelyek hatással lehetnek a lekérdezési teljesítményre. Az összetett lekérdezések és a hálózati késés két tényező, amelyek hozzájárulnak a lekérdezési válaszidő változásaihoz.

### <a name="add-partitions"></a>Partíciók hozzáadása
A legtöbb szolgáltatásalkalmazás több replikát igényel a partíciók helyett. Azokban az esetekben, amikor a megnövelt dokumentumok száma kötelező, partíciókat adhat hozzá, ha regisztrált a standard szolgáltatásra. Az alapszintű csomag nem biztosít további partíciókat.

A standard szinten a partíciók a 12 (pontosabb, 1, 2, 3, 4, 6 vagy 12) többszörösében lesznek hozzáadva. Ez egy horizontális skálázási összetevő. Az indexek 12 szegmensben jönnek létre, amelyek mindegyike 1 partíción tárolható, vagy egyenlően 2, 3, 4, 6 vagy 12 partícióra osztható (egy-egy szegmens/partíció).

### <a name="remove-replicas"></a>Replikák eltávolítása
A nagy mennyiségű lekérdezési kötetek után a csúszka segítségével csökkentheti a replikákat a keresési lekérdezés terhelésének normalizálása után (például az üdülési értékesítések felett). Az Ön részéről nincs szükség további lépésekre. A replikák számának csökkentése lemond az adatközpontban található virtuális gépekről. A lekérdezési és az adatfeldolgozási műveletek mostantól kevesebb virtuális gépen futnak, mint korábban. A minimális követelmény egy replika.

### <a name="remove-partitions"></a>Partíciók eltávolítása
A replikák eltávolításával szemben, amelyhez nincs szükség további erőfeszítésre, előfordulhat, hogy néhány teendője van, ha több tárterületet használ, mint amennyit csökkenteni lehet. Ha például a megoldás három partíciót használ, az egy vagy két partícióra való leépítés hibát eredményez, ha az új tárolóhely kisebb, mint az index üzemeltetéséhez szükséges. Az elvárásoknak megfelelően lehetőség van az indexek vagy dokumentumok törlésére egy társított indexen belül, hogy felszabadítsa a helyet, vagy megtartja a jelenlegi konfigurációt.

Nincs olyan észlelési módszer, amely közli, hogy mely indexek vannak tárolva egy adott partíción. Az egyes partíciók körülbelül 25 GB tárhelyet biztosítanak, ezért a tárterületet a szükséges partíciók számával kell csökkenteni. Ha egy partícióra kíván visszaállítani, akkor mind a 12 szegmensnek el kell férnie.

Ha segítségre van szüksége a jövőbeli tervezéssel kapcsolatban, érdemes lehet megtekinteni a tárterületet (az [indexek statisztikájának lekérése](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)használatával), hogy megtekintse, mennyit használt 

<a id="next-steps"></a>

## <a name="next-steps"></a>Következő lépések
Ha megértette a Service Administration mögötti fogalmakat, érdemes lehet a [PowerShell](search-manage-powershell.md) használatával automatizálni a feladatokat.

Javasoljuk továbbá a [teljesítmény-és optimalizálási cikk](search-performance-optimization.md)áttekintését.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



