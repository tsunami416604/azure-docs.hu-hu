---
title: A portálban – Azure Search szolgáltatás Azure Search felügyelete
description: Az Azure Search szolgáltatást, egy üzemeltetett felhőalapú keresési szolgáltatás a Microsoft Azure, az Azure portal használatával kezelheti.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d5820c927b88eba37eaf092dfd4b209180bfc8eb
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775594"
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Az Azure Portalon az Azure Search szolgáltatás-felügyelet
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portál](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Az Azure Search egy olyan teljes körűen felügyelt, felhőalapú keresési szolgáltatás, amely fejlett keresési funkciókat beépítés egyéni alkalmazásokba. Ez a cikk ismerteti a szolgáltatás felügyeleti feladatokat hajthat végre a a [az Azure portal](https://portal.azure.com) már kiépített egy keresési szolgáltatás. Szolgáltatásfelügyeleti egyszerűsített kialakításból fakadóan csak a következő feladatokat:

> [!div class="checklist"]
> * Való hozzáférés kezelése a *api-kulcsainak* olvasási vagy írási hozzáférés a szolgáltatáshoz használt.
> * Módosítsa a szolgáltatáskapacitás partíciókat és -replikákat a lefoglalt módosításával.
> * Megfigyelheti az erőforrások felhasználását, a szolgáltatási szintben maximálisan korlátait viszonyítva.

Figyelje meg, hogy *frissítése* egy felügyeleti feladat nem szerepel. Ha a szolgáltatás ki van építve az erőforrások kiosztásakor, mert egy új szolgáltatás áthelyezése másik tarifacsomagra igényel. További információkért lásd: [Azure Search szolgáltatás létrehozása](search-create-service-portal.md).

> [!Tip]
> Súgó a keresési forgalom és a lekérdezések teljesítményének elemzése keres? Lekérdezés kötet, amely feltételek keresése, és hogyan sikerült keresési eredmények útmutatás az ügyfelek számára meghatározott dokumentumok indexben azok, akik követheti nyomon. További információkért lásd: [forgalmi elemzések keresése az Azure Search](search-traffic-analytics.md), [figyelheti a használatát, és a lekérdezés mérőszámokat](search-monitor-usage.md), és [teljesítmény és optimalizálás](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Rendszergazdai jogosultságok
Kiépítés, vagy maga a szolgáltatás leszerelése hajtható végre egy Azure-előfizetés rendszergazdájának vagy társrendszergazdájának.

A szolgáltatáson belül a szolgáltatás URL-címe és a egy felügyeleti api-kulcs segítségével bárki a szolgáltatás olvasási és írási hozzáférése van. Olvasási és írási hozzáférés lehetővé teszi a hozzáadása, törlése és módosítása kiszolgálóobjektumok, beleértve az api-kulcsok, indexek, indexelők, adatforrásokat, ütemezések és szerepkör-hozzárendelések keresztül megvalósított módon [RBAC által definiált szerepkörök](search-security-rbac.md).

Minden felhasználói interakció az Azure Search szolgáltatással csökken az alábbi módok egyikében: olvasási és írási hozzáférése a szolgáltatáshoz (rendszergazdai jogosultságokkal), vagy csak olvasási hozzáférés a szolgáltatáshoz (lekérdezés jogok). További információkért lásd: [api-kulcsok kezelése](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Naplózás és a rendszer-információ
Az Azure Search nem biztosít egy adott szolgáltatás vagy a portálon vagy a programozási felületen keresztül a rendszernapló fájljaiban. Az alapszintű csomag és a Microsoft a fenti figyeli az összes Azure Search szolgáltatás 99,9 %-os rendelkezésre állás érdekében egy szolgáltatásiszint-szerződések (SLA). A szolgáltatás lassú vagy átviteli sebesség SLA-küszöbértékek alá csökken, ha a támogatási csapatuk tekintse át a naplófájlok számára elérhető, és a probléma.

A szolgáltatással kapcsolatos általános információkért tekintetében a következőképpen szerezheti be információt:

* A portálon, a szolgáltatás irányítópultján, értesítések, a tulajdonságok és a hibaállapot-üzenetek.
* Használatával [PowerShell](search-manage-powershell.md) vagy a [felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/) való [szolgáltatás tulajdonságainak lekérése](https://docs.microsoft.com/rest/api/searchmanagement/services), vagy az index erőforrás-használati állapotát.
* Keresztül [forgalmi elemzések keresése](search-traffic-analytics.md), ahogy korábban említettük.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Erőforrás-használat monitorozása
Az irányítópult erőforrások monitorozása korlátozódik a szolgáltatás irányítópultját és a szolgáltatás lekérdezésével szerezheti be néhány metrikák látható információk. A szolgáltatás irányítópultján, a használati szakaszban gyorsan meghatározhatja e partíció erőforrásszintek megfelelőek-e az alkalmazás. Telepíthet külső erőforrások, például az Azure monitoring, ha azt szeretné, rögzítése és a naplózott események megőrzése. További információkért lásd: [figyelése az Azure Search](search-monitor-usage.md).

A Search szolgáltatás REST API használatával, kérheti a száma a dokumentumokban és indexekben programozott módon: 

* [Index statisztikájának beolvasása](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Dokumentumok száma](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Vész-helyreállítási és szolgáltatás leállások

Bár azt is maradványérték az adatokat, az Azure Search nem biztosít azonnali feladatátvételt a szolgáltatás Ha kimaradás a fürt és a data center szinten van. Ha egy fürtöt az Adatközpont meghibásodik, az üzemeltetési csapat érzékeli, és állítsa vissza a szolgáltatás azt. Szolgáltatás visszaállítás során tapasztalható üzemszünet, de kérheti a szolgáltatási jóváírások / szolgáltatás elérhetetlensége kompenzálják a [szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Ha szolgáltatás kívül a Microsoft érdekeltségébe végzetes hibák esetén szükséges, sikerült [további szolgáltatás kiépítése](search-create-service-portal.md) egy másik régióba, és annak biztosítása érdekében az indexek georeplikációs stratégia vannak megvalósítása Teljesen redundáns összes szolgáltatásban.

Ügyfeleink, akik [indexelők](search-indexer-overview.md) , és az indexek frissítése a vész-helyreállítási ugyanazon az adatforráson kihasználva geo-specifikus indexelők révén képes kezelni. Két szolgáltatás különböző régiókban, az indexelők futtatása sikerült indexelésével megkönnyíti a geo-redundancia érdekében ugyanazt az adatforrást. Ha meg vannak, amelyek szintén georedundáns adatforrásokból származó indexelést, vegye figyelembe, hogy az Azure Search-indexelők csak hajthat végre növekményes indexelő az elsődleges replikára. A feladatátadási esemény mindenképpen újra az indexelő átirányítása az új elsődleges replika. 

Ha nem használja az indexelők, használja az alkalmazáskódban leküldéses objektumokat és adatokat a különböző keresési szolgáltatások párhuzamosan. További információkért lásd: [teljesítmény és optimalizálás az Azure Search](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

Mivel nem egy elsődleges tárolási megoldás az Azure Search, egy hivatalos önkiszolgáló biztonsági mentési és visszaállítási mechanizmust nem biztosítunk. Az alkalmazás kódjában, létrehozására és a egy index feltöltése, a tényleges visszaállítás lehetőség, ha véletlenül törli az indexet. 

Az index újraépítése, lenne törli azt (feltéve, hogy létezik), hozza létre újból az indexet a szolgáltatás a és töltse be újra az elsődleges adattárral az adatok lekérésével.


<a id="scale"></a>

## <a name="scale-up-or-down"></a>Felfelé és lefelé skálázás
Minden keresési szolgáltatás legalább egy replika és a egy partíció kezdődik. Ha a regisztrált egy [dedikált erőforrásokkal kínál](search-limits-quotas-capacity.md), kattintson a **MÉRETEZÉSI** erőforrás-használat módosíthatja a szolgáltatás irányítópultjának csempéje.

Kapacitás keresztül vagy az erőforrás hozzáadásakor a szolgáltatás ezeket automatikusan használja. Nincsenek további Önnek nem kell az Ön részéről, de van egy kis idő előtt, hogy az új erőforrás hatása van. 15 percet vagy többet a további erőforrások kiosztása is igénybe vehet.

 ![][10]

### <a name="add-replicas"></a>Replikák hozzáadásával
Növelje a lekérdezések másodpercenkénti (lekérdezési QPS) vagy a magas rendelkezésre állás elérésének replikák hozzáadásával történik. Minden egyes replikának egy példányát az index rendelkezik, így a egy több replika hozzáadása a rendszer lefordítja arra az egyik több index kezelésére lekérdezési kérelmeket. Magas rendelkezésre állás érdekében szükség-e legalább 3 replika (lásd: [kapacitástervezés](search-capacity-planning.md) részletekért).

Egy keresési szolgáltatás több replika kellene lekérdezési érkező kérések elosztása betöltheti keresztül nagy számú indexet. Adja meg a lekérdezés kötet szintű, lekérdezések átviteli sebességére fog gyorsabb lehet, ha az index a kérelem kiszolgálására elérhető több példányt. Ha a lekérdezés késést tapasztal, várható pozitív hatással lehet a teljesítményre követően a további replikák online állapotban.

Bár a lekérdezések átviteli sebességére megnő, replikák hozzáadásakor, ez nem pontosan duplán vagy megháromszorozzuk a replikákat a szolgáltatáshoz való hozzáadása során. Alkalmazások keresése az összes külső tényezőkkel, amelyek is hatással vannak a lekérdezési teljesítmény vonatkozik. Összetett lekérdezéseket és a hálózati késés két tényező befolyásolja a lekérdezések válaszidejét változata létezik.

### <a name="add-partitions"></a>Partíciók hozzáadásával
A legtöbb szolgáltatásalkalmazások nincs beépített szüksége további replikák helyett partíciókat. Azokban az esetekben, ahol egy magasabb dokumentumszám szükség, a partíciókat is hozzáadhat, ha Standard szolgáltatáshoz regisztrált. Alapszintű csomag nem biztosít további partíciókkal.

A Standard szintű, partíciók kerülnek, a 12 többszörösei (pontosabban 1, 2, 3, 4, 6 vagy 12). Ez a horizontális skálázási-összetevője. Az index jön 12 szegmenseket, az összes partíción 1 vagy 2, 3, 4, 6 vagy 12 partícióra (partíciónként egyik adatszilánkba író) egyenlően oszlik.

### <a name="remove-replicas"></a>Replika eltávolítása
A lekérdezési kötetek időszakok, miután a csúszka segítségével csökkentheti a replikák követően a keresési lekérdezésekkel van normalizálva, (például után szünnap értékesítési van). Nincsenek nem kell további lépésekre. A replika száma lowering átadja az adatközpontban lévő virtuális gépek. A lekérdezési és adatfeldolgozási műveletek előtt-nál kevesebb virtuális gépek már futni fog. A minimális követelmény, egy replikát.

### <a name="remove-partitions"></a>Partíciók eltávolítása
Ellentétben eltávolítása replikákat, amely az Ön részéről nincs extra erőfeszítést igényel, lehetséges, hogy néhány dolgot a teendő, ha csökkenteni lehet, mint további tárhelyet használ. Például ha a megoldás három partíció használ, egy vagy két partícióra downsizing generál hiba-e az új tárolóhelyre kisebb, mint az index üzemeltetéséhez szükséges. Ahogy, a választható lehetőségek: indexek vagy dokumentumok belül társított index szabadítson fel lemezterületet, vagy hagyja a jelenlegi konfiguráció törlése.

Nincs tájékoztatja, hogy melyik index szegmenseket tárolja az adott partíciók észlelési módszer. Mindegyik partíció a storage szolgáltatással, körülbelül 25 GB nyújt, így a csökkentése érdekében a tároló, amely rendelkezik a partíciók száma alapján is elhelyezkedhetnek méretre kell. Ha vissza szeretné állítani egy partícióhoz, minden 12 szegmensre kell megfelelően.

Annak érdekében, a későbbi tervezést, előfordulhat, hogy ellenőrizni kívánt tárolási (használatával [Indexstatisztikáit első](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) mennyi ténylegesen használt megtekintéséhez. 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>Ajánlott eljárások a méretezési és üzembe helyezés
A 30 perces videót felülvizsgálatok gyakorlati tanácsok a speciális telepítési forgatókönyvek, beleértve a földrajzilag elosztott számítási feladatok. Emellett megtekintheti [teljesítmény és optimalizálás az Azure Search](search-performance-optimization.md) súgóoldalak, mind a azonos pontok számára.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>További lépések
Miután megismerkedett a szolgáltatások felügyeletével kapcsolatos fogalmakat, érdemes [PowerShell](search-manage-powershell.md) automatizálhatja a feladatokat.

Emellett ajánlott áttekinteni a [teljesítmény és optimalizálás a cikk](search-performance-optimization.md).

Egy másik javaslat, hogy az előző szakaszban feljegyzett a videó megtekintése. Ebben a szakaszban ismertetett technikák mélyebb lefedettséget biztosít.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



