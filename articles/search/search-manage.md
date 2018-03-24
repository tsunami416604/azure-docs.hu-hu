---
title: Az Azure-portálon az Azure Search szolgáltatás adminisztrációs
description: Kezelheti az Azure Search, egy üzemeltetett felhőalapú keresőszolgáltatás, a Microsoft Azure-ban az Azure portál használatával.
services: search
documentationcenter: ''
author: HeidiSteen
manager: jhubbard
editor: ''
tags: azure-portal
ms.assetid: c87d1fdd-b3b8-4702-a753-6d7e29dbe0a2
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: d19683291e001c3c3f2a7bfc5c203b5121a8a418
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Az Azure-portálon az Azure Search szolgáltatás adminisztrációs
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Az Azure Search egy teljes körűen felügyelt, felhőalapú keresőszolgáltatás gazdag keresésekhez egyéni alkalmazásokba való létrehozására használt. Ez a cikk ismerteti a *felügyeleti szolgáltatás* elvégezhető feladatokhoz a [Azure-portálon](https://portal.azure.com) már kiépített egy keresési szolgáltatáshoz. *Felügyeleti szolgáltatás* egyszerűsített úgy lett kialakítva, korlátozódik, a következő feladatokat:

* Kezelését és biztonságossá tételét a hozzáférést a *api-kulcsokat* olvasási vagy írási hozzáféréssel a szolgáltatás használatos.
* Módosítsa a szolgáltatás kapacitás lefoglalása a partíciók és replikák módosításával.
* Erőforrás-használat, a szolgáltatási réteg maximális határértékeinek viszonyítva figyelése.

Figyelje meg, hogy *frissítése* nem szerepel, mint a felügyeleti feladatot. -Erőforrásokat foglal le, ha a szolgáltatás ki van építve, mert egy másik réteghez kell egy új szolgáltatás. További információkért lásd: [Azure Search szolgáltatás létrehozása](search-create-service-portal.md).

> [!Tip]
> Súgó a keresési forgalom vagy a lekérdezés teljesítményének elemzése keres? Lekérdezés kötet, amely feltételek személyek keres, és hogyan sikeres találatok nyereség betekintést szerepelnek, az ügyfelek irányítása az indexben egyes dokumentumokhoz. Útmutatásért lásd: [keresési forgalom Analytics az Azure Search](search-traffic-analytics.md), [használati és a lekérdezés metrikát](search-monitor-usage.md), és [teljesítmény- és optimalizálási](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Rendszergazdai jogosultságok
Kiépítés, vagy a szolgáltatás leállítására végezhető el az Azure-előfizetéshez rendszergazdai vagy társadminisztrátori.

A szolgáltatáson belül bárki, aki hozzáféréssel rendelkezik a szolgáltatás URL-címet és egy adminisztrációs api-kulcsot a szolgáltatás olvasási és írási hozzáférése van. Olvasási és írási hozzáférés lehetővé teszi a hozzáadásához, törléséhez vagy kiszolgálói objektumok, például az api-kulcsokat, indexek, indexelők, adatforrások, ütemezéseihez és szerepkör-hozzárendelések keresztül megvalósított módon módosítására [RBAC által definiált szerepkörök](search-security-rbac.md).

Az Azure Search minden felhasználói beavatkozás esik e két beállítás közül: olvasási és írási hozzáférése a szolgáltatáshoz (rendszergazdai jogosultságok), vagy csak olvasási hozzáféréssel a szolgáltatáshoz (lekérdezés jogosultságok). További információkért lásd: [az api-kulcsok kezelése](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Naplózás és a rendszer
Az Azure Search nem fed fel naplófájlokat egy adott szolgáltatás vagy a portál vagy programozott felületek használatával. Alapszintű rétegben és újabb verzióiban Microsoft figyeli az összes Azure Search szolgáltatás a szolgáltatásszint-szerződések (SLA) / 99,9 %-os rendelkezésre állás érdekében. Ha a szolgáltatás lassú vagy átviteli sebesség SLA-küszöbértékek alá csökken, a támogatási csoportokkal tekintse át a naplófájlok számára elérhető, és a probléma megoldásához.

A szolgáltatás általános információinak tekintetében Itt kaphat információt a következő módon:

* A portálon, a szolgáltatás irányítópultján, értesítések, a tulajdonságok és az állapotüzenetek keresztül.
* Használatával [PowerShell](search-manage-powershell.md) vagy a [felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/) való [szolgáltatás tulajdonságait](https://docs.microsoft.com/rest/api/searchmanagement/services), vagy az index Erőforrás kihasználtsága állapotát.
* Keresztül [keresési forgalom analytics](search-traffic-analytics.md), ahogy azt már korábban említettük.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>A figyelő Erőforrás kihasználtsága
Az irányítópult erőforrás figyelési korlátozódik a szolgáltatás irányítópultját és néhány metrikák szerezheti be a szolgáltatás lekérdezi a megjelenő információkat. A szolgáltatás irányítópultján, a használati területen segítségével gyorsan megállapítható, hogy partíció erőforrás szintek megfelelőek-e az alkalmazás.

A Search szolgáltatás REST API használatával, kaphat a dokumentumok és indexek száma programozott módon: 

* [Megtekintheti a statisztikákat Index](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [A dokumentumok száma](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Katasztrófa utáni helyreállítás és a szolgáltatás leállások esetén

Bár az adatok azt is maradványérték, Azure Search nem biztosítanak az azonnali a szolgáltatás esetén kimaradás a fürt vagy data center szinten. A fürt meghibásodik, az adatközpontban, ha a műveleti csapata azonosítja, és állítsa vissza a szolgáltatás hogyan működik. Állásidő fog tapasztalni szolgáltatás visszaállítás során. Szolgáltatási jóváírások kiegyensúlyozása érdekében szolgáltatás elérhetetlensége / kérhet a [szolgáltatási szint szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Ha folyamatos szolgáltatás kívül a Microsoft végzetes hibák esetén szükséges, hogy sikerült [további szolgáltatás kiépítése](search-create-service-portal.md) egy másik régióban található, és indexek biztosításához georeplikáció stratégia vannak megvalósítása az összes szolgáltatásban teljesen redundáns.

Felhasználói [indexelők](search-indexer-overview.md) , és frissítse az indexek kezelni tud a vész-helyreállítási kihasználva ugyanarra az adatforrásra vonatkozó földrajzi indexelők keresztül. Különböző régiókban, amelyeken indexelőt, két szolgáltatást sikerült felületindexét-georedundancia eléréséhez ugyanazon az adatforráson. Ha az adatforrásokat, amelyek is georedundáns vannak indexelő, vegye figyelembe, hogy Azure keresési indexelő csak hajthat végre növekményes indexelő az elsődleges replikára változott. A feladatátadási esemény lehet arra, hogy az indexelő újra mutasson az új elsődleges replika. 

Ha indexelő nem használja, használja az alkalmazás kódjában leküldéses objektumok és adatok különböző keresési szolgáltatások párhuzamosan. További információkért lásd: [teljesítmény- és az Azure Search optimalizálási](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

Azure Search nincs olyan elsődleges adatok tárolási megoldást, mert a Microsoft nem vállal formális mechanizmusát önkiszolgáló biztonsági mentését és helyreállítását. Az alkalmazás kódjában létrehozására és az index feltöltése használható a tényleges visszaállítás beállítás, ha véletlenül törli az index. 

Index újraépítésére, ehhez töltse be újra az elsődleges adattárolóból adatok lekérésével, hozza létre újból az indexet a szolgáltatás a és törli azt (feltéve, hogy létezik-e) el. Azt is megteheti, hogy is elérhetők a [ügyfél-támogatási]() helyreállítási indexben regionális kimaradás esetén.


<a id="scale"></a>

## <a name="scale-up-or-down"></a>Felfelé és lefelé skálázás
Minden keresési szolgáltatás kezdődik-e legalább egy másodpéldány és egy partíciót. Ha az egy [réteghez, amelynek biztosít a dedikált erőforrások](search-limits-quotas-capacity.md), kattintson a **MÉRETEZÉSI** Erőforrás kihasználtsága úgy, hogy a szolgáltatás irányítópultján csempére.

Amikor bármelyik erőforrás keresztül, a szolgáltatás őket automatikusan használja. Semmilyen további műveletet van szükség, de nincs egy kis idő, mielőtt az új erőforrást a hatását létrejött. 15 perc vagy több további erőforrások biztosításához is igénybe vehet.

 ![][10]

### <a name="add-replicas"></a>Adja hozzá a replikák
Lekérdezések / másodperc (QPS) megnövelni, vagy magas rendelkezésre állás elérése érdekében a replikák hozzáadásával történik. Minden egyes replikának rendelkezik index, egy másolatát, így egy további replika hozzáadása az eszköz egy további lekérdezés szolgáltatáskérések kezelése érhető el index. Legalább 3 replikák szükségesek a magas rendelkezésre állású (lásd: [kapacitástervezés](search-capacity-planning.md) részletekért).

A keresési szolgáltatást több replikák indexek nagyobb számú betöltheti lekérdezés érkező kérések elosztása. Mivel a lekérdezés kötet, lekérdezés átviteli lesz gyorsabb lehet, ha a kérelem kiszolgálására szolgáló érhető el index több példánya. Ha a lekérdezés késést tapasztal, számíthat pozitív hatása a teljesítményre Ha a további replikák online.

Lekérdezés átviteli következik be, replikák hozzáadásakor, bár ez nem pontosan duplán vagy háromszoros replikák a szolgáltatáshoz való hozzáadásakor. Az összes keresési alkalmazások is hatással vannak a lekérdezési teljesítményt érintő külső tényezőket vonatkoznak. Összetett lekérdezések és a hálózati késés is két tényező befolyásolja a lekérdezési válaszidőt változata.

### <a name="add-partitions"></a>Adja hozzá a partíciók
A legtöbb szolgáltatásalkalmazás nincs további replikák helyett partíciók beépített szüksége. Azokban az esetekben, ahol szükség-e egy nagyobb dokumentumok száma, a partíciók adhat hozzá, ha szabványos szolgáltatáshoz regisztrált. Az alapszintű csomag két további partíció nem ad meg.

A Standard csomagot, a partíciók jelentek meg 12 Többszörösök (pontosabban, 1, 2, 3, 4, 6 vagy 12). Ez az összetevő a horizontális. Az index összes partíción 1 vagy 2, 3, 4, 6 vagy 12 partíciók (egy shard partíciónként) egyenlően osztható 12 szilánkok jön létre.

### <a name="remove-replicas"></a>Távolítsa el a replikák
Kötetek a lekérdezési időszak után csökkentése érdekében replikák után keresési lekérdezésekkel rendelkezik normalizált (például követően szünnap értékesítési keresztül).

Ehhez a csúszkával replika vissza egy alacsonyabb értékre. Nincs szükség további lépésekre van. A replika számának csökkentésével elhagyja az adatközpontban lévő virtuális gépek. A lekérdezés- és adatfeldolgozást műveletei most a virtuális gépeken kevesebb mint előtt fog futni. A minimális korlát egy replikát.

### <a name="remove-partitions"></a>Partíciók törlése
Ellentétben eltávolítása replikákat, amely nincs meg a extra erőfeszítést igényel, lehetséges, hogy néhány munka a teendő, ha csökkenteni lehet, mint további tárhelyet használ. Például ha a megoldás három használ, egy vagy két partícióra downsizing hoz létre hiba esetén az új tárolóhelyre kisebb, mint a szükséges. Várt, a választott során a rendszer törli az indexeket, vagy szabadítson fel lemezterületet, vagy tartani a jelenlegi konfigurációt társított index dokumentumokon.

Nincs jelzi, hogy melyik index szilánkok tárolt adott partíciókra észlelési módszer. Mindegyik partíció körülbelül 25 GB tárhely, nyújt, így a tárhely csökkentésére, amely rendelkezik partíciók száma elhelyezhetők méretűre kell. Ha vissza szeretné állítani egy partícióhoz, minden 12 szilánkok megfelelően kell.

A későbbi tervezést érdekében előfordulhat, hogy ellenőrizni kívánt tárolási (használatával [beolvasása Index statisztika](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) mennyi ténylegesen használt megjelenítéséhez. 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>Gyakorlati tanácsok a méretezés és a központi telepítés
A 30 perces videó ellenőrzi, hogy gyakorlati tanácsok a speciális telepítési forgatókönyvek, beleértve a földrajzilag elosztott munkaterhelések. Azt is láthatja, [teljesítmény- és az Azure Search optimalizálási](search-performance-optimization.md) fedik le az azonos pontok súgó-lapok.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>További lépések
Miután megismerte a szolgáltatás felügyeleti fogalmakat, érdemes lehet [PowerShell](search-manage-powershell.md) feladatok automatizálására.

Javasoljuk továbbá tekintse át a [teljesítmény- és optimalizálási cikk](search-performance-optimization.md).

Egy másik javasoljuk, hogy az előző szakaszban az áttelepítés előtt feljegyzett videót. Ebben a szakaszban említett eljárások mélyebb körét biztosít.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



