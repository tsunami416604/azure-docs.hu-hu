---
title: Fürt leírása a Fürterőforrás-kezelő használatával
description: Írja le a Service Fabric-fürtöt tartalék tartományok, frissítési tartományok, csomóponttulajdonságok és csomópontkapacitások megadásával a fürterőforrás-kezelőhöz.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7142e3f9aaa25e7ba327194c04ad6a9b5f4e3ad1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258771"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Service Fabric-fürt leírása a fürterőforrás-kezelő használatával
Az Azure Service Fabric fürterőforrás-kezelő szolgáltatása számos mechanizmust biztosít a fürt leírásához:

* Tartalék tartományok
* Tartományok frissítése
* Csomópont tulajdonságai
* Csomópontkapacitások

Futásidőben a fürterőforrás-kezelő ezt az információt a fürtben futó szolgáltatások magas rendelkezésre állásának biztosítására használja. E fontos szabályok érvényesítése közben megpróbálja optimalizálni a fürtön belüli erőforrás-felhasználást is.

## <a name="fault-domains"></a>Tartalék tartományok
A tartalék tartomány a koordinált hibák bármely területe. Egyetlen gép egy tartalék tartomány. Ez nem a saját különböző okok miatt, a tápegység hibák meghajtó hibák rossz NIC firmware. 

Az ugyanahhoz az Ethernet-kapcsolóhoz csatlakozó gépek ugyanabban a tartalék tartományban vannak. Ahogy azok a gépek is, amelyek egyetlen energiaforrással vagy egyetlen helyen osztoznak. 

Mivel a hardverhibák természetes átfedése, a tartalék tartományok eredendően hierarchikusak. Uri-kként jelennek meg a Service Fabricben.

Fontos, hogy a tartalék tartományok megfelelően vannak beállítva, mert a Service Fabric ezeket az információkat használja a szolgáltatások biztonságos helyéhez. A Service Fabric nem szeretné olyan szolgáltatásokat elhelyezni, hogy a tartalék tartomány (egyes összetevők meghibásodása által okozott) elvesztése miatt a szolgáltatás leáll. 

Az Azure-környezetben a Service Fabric a környezet által biztosított tartalék tartomány adatait használja a fürt csomópontjainak megfelelő konfigurálásához az Ön nevében. A Service Fabric önálló példányai hozlétre a tartalék tartományok a fürt beállításakor vannak definiálva. 

> [!WARNING]
> Fontos, hogy a service fabric nek megadott tartalék tartomány adatai pontosak legyenek. Tegyük fel például, hogy a Service Fabric-fürt csomópontjai 10 virtuális gépen belül futnak, és 5 fizikai gazdagépen futnak. Ebben az esetben annak ellenére, hogy 10 virtuális gép van, csak 5 különböző (legfelső szintű) tartalék tartomány van. Ugyanazt a fizikai gazdagépet megosztja, a virtuális gépek ugyanazt a legfelső szintű tartaléktartományt osztják meg, mivel a virtuális gépek koordinált hibát tapasztalnak, ha fizikai állomásuk meghibásodik.  
>
> A Service Fabric elvárja, hogy egy csomópont tartalék tartománya ne változzon. A virtuális gépek magas rendelkezésre állásának biztosításához szükséges egyéb mechanizmusok, például a [HA-virtuális gépek](https://technet.microsoft.com/library/cc967323.aspx)ütközéseket okozhatnak a Service Fabric szolgáltatással. Ezek a mechanizmusok a virtuális gépek transzparens áttelepítését használják egyik állomásról a másikra. Nem konfigurálják újra, és nem értesítik a futó kódot a virtuális gépen belül. Mint ilyen, *nem támogatottak* a Service Fabric-fürtök futtatásához. 
>
> A Service Fabric legyen az egyetlen magas rendelkezésre állású technológia. Mechanizmusok, például az élő virtuális gép áttelepítése és a sans nem szükséges. Ha ezeket a mechanizmusokat a Service Fabric-tel együtt használják, _csökkentik_ az alkalmazások rendelkezésre állását és megbízhatóságát. Ennek az az oka, hogy további összetettséget vezetnek be, központi hibaforrásokat adnak hozzá, és olyan megbízhatósági és rendelkezésre állási stratégiákat használnak, amelyek ütköznek a Service Fabric-ben lévőkkel. 
>
>

A következő ábrán színezünk ki minden olyan entitást, amely hozzájárul a tartalék tartományokhoz, és felsoroljuk az összes különböző tartalék tartományt, amely ez az eredmény. Ebben a példában adatközpontokkal ("DC"), állványokkal ("R") és blades ("B") rendelkezünk. Ha minden panel egynél több virtuális gépet tartalmaz, előfordulhat, hogy egy másik réteg a tartalék tartomány hierarchiájában.

<center>

![Tartalék tartományokon keresztül szervezett csomópontok][Image1]
</center>

Futásidőben a Service Fabric fürterőforrás-kezelő figyelembe veszi a fürt és a tervek elrendezésében lévő tartalék tartományokat. Az állapotalapú replikák vagy állapotmentes példányok egy szolgáltatás elosztott, így azok külön tartalék tartományokban. A szolgáltatás tartalék tartományok közötti terjesztése biztosítja, hogy a szolgáltatás rendelkezésre állása ne sérüljön, ha egy tartalék tartomány a hierarchia bármely szintjén meghibásodik.

A fürterőforrás-kezelőt nem érdekli, hogy hány réteg van a tartalék tartományhierarchiában. Megpróbálja biztosítani, hogy a hierarchia bármely részének elvesztése ne befolyásolja a benne futó szolgáltatásokat. 

A legjobb, ha a csomópontok száma a hibatartomány-hierarchia minden mélységi szintjén van. Ha a tartalék tartományok "fája" kiegyensúlyozatlan a fürtben, a fürterőforrás-kezelő nehezebben találja ki a szolgáltatások legjobb elosztását. A kiegyensúlyozatlan tartaléktartomány-elrendezések azt jelentik, hogy egyes tartományok elvesztése nagyobb hatással van a szolgáltatások rendelkezésre állására, mint más tartományok. Ennek eredményeképpen a fürterőforrás-kezelő két cél között szakad meg: 

* Azt akarja, hogy használja a gépeket, hogy a "nehéz" domain azáltal, hogy szolgáltatásokat rájuk. 
* Azt akarja, hogy a szolgáltatások más tartományokban, hogy a veszteség a domain nem okoz problémát. 

Hogyan néznek ki a kiegyensúlyozatlan tartományok? Az alábbi ábrán két különböző fürtelrendezés látható. Az első példában a csomópontok egyenletesen oszlanak el a tartalék tartományok között. A második példában egy tartalék tartomány sokkal több csomópontot, mint a többi tartalék tartományok. 

<center>

![Két különböző fürtelrendezés][Image2]
</center>

Az Azure-ban a választás, amely tartalék tartomány tartalmaz egy csomópontot az Ön számára. De attól függően, hogy hány csomópontok, amelyek kiépítése, továbbra is a végén a tartalék tartományok, amelyek több csomópont ot, mint másokban. 

Tegyük fel például, hogy öt tartalék tartomány van a fürtben, de hét csomópontot létesít egy csomóponttípushoz (**NodeType**). Ebben az esetben az első két tartalék tartomány több csomót ad. Ha továbbra is több **NodeType** példányt telepít néhány példánnyal, a probléma rosszabbodik. Ezért azt javasoljuk, hogy az egyes csomóponttípusokcsomópont-típusokban lévő csomópontok száma a tartalék tartományok többszöröse.

## <a name="upgrade-domains"></a>Tartományok frissítése
A frissítési tartományok egy másik szolgáltatás, amely segít a Service Fabric-fürterőforrás-kezelőnek megérteni a fürt elrendezését. A frissítési tartományok olyan csomópontkészleteket határoznak meg, amelyek egyidejűleg vannak frissítve. A frissítési tartományok segítségével a fürterőforrás-kezelő megérti és vezényli a felügyeleti műveleteket, például a frissítéseket.

Upgrade tartományok sok, mint a tartalék tartományok, de egy pár kulcsfontosságú különbségeket. Először is az összehangolt hardverhibák területei határozzák meg a tartalék tartományokat. A frissítési tartományokat viszont a házirend határozza meg. Te döntöd el, hogy hányat akarsz, ahelyett, hogy hagynád, hogy a környezet diktálja a számot. Annyi frissítési tartománya lehet, mint ahány csomópontnak van. Egy másik különbség a tartalék tartományok és a frissítési tartományok között az, hogy a frissítési tartományok nem hierarchikusak. Ehelyett, ők több, mint egy egyszerű tag. 

Az alábbi ábrán három, három tartalék tartományban csíkozott frissítési tartomány látható. Azt is mutatja, egy lehetséges elhelyezése három különböző replikák egy állapotalapú szolgáltatás, ahol minden végződik különböző hiba-és frissítési tartományok. Ez az elhelyezés lehetővé teszi a hibatartomány elvesztését a szolgáltatásfrissítés közben, és továbbra is rendelkezik a kód és az adatok egy példányával.  

<center>

![Elhelyezés hibás és frissítési tartományokkal][Image3]
</center>

Vannak előnyei és hátrányai, hogy miután nagy számú frissítési tartományok. Több frissítési tartományok azt jelenti, hogy a frissítés minden egyes lépése részletesebb, és kisebb számú csomópontot vagy szolgáltatást érint. Kevesebb szolgáltatásnak kell egyszerre mozognia, így kevesebb lemorzsolódás torka kerül a rendszerbe. Ez általában javítja a megbízhatóságot, mivel a frissítés során bevezetett bármely probléma kevesebb szolgáltatást érint. A további frissítési tartományok azt is jelentik, hogy a frissítés hatásának kezeléséhez kevesebb rendelkezésre álló pufferre van szükség más csomópontokon. 

Ha például öt frissítési tartománnyal rendelkezik, az egyes csomópontok a forgalom nagyjából 20 százalékát kezelik. Ha egy frissítéshez le kell vennie a frissítési tartományt, akkor a terhelésnek általában el kell mennie valahova. Mivel még négy frissítési tartománnyal rendelkezik, mindegyiknek a teljes forgalom körülbelül 5 százalékának kell helyet vásárolnia. A további frissítési tartományok azt jelentik, hogy kevesebb pufferre van szükség a fürt csomópontjain. 

Fontolja meg, ha 10 frissítési tartománya volt helyette. Ebben az esetben minden frissítési tartomány a teljes forgalomnak csak mintegy 10 százalékát kezelné. Amikor egy frissítés lép a fürtön, minden tartománynak a teljes forgalomnak csak körülbelül 1,1 százalékának kell helyet adnia. Több frissítési tartományok általában lehetővé teszi, hogy futtassa a csomópontokat a magasabb kihasználtság, mert kevesebb fenntartott kapacitásra van szüksége. Ugyanez igaz a tartalék tartományokra is.  

A hátránya, hogy sok frissítési tartományok, hogy a frissítések általában hosszabb időt vesz igénybe. A Service Fabric egy frissítési tartomány befejezése után rövid ideig vár, és ellenőrzéseket hajt végre a következő frissítés megkezdése előtt. Ezek a késések lehetővé teszik a frissítés által a frissítés megkezdése előtt bevezetett problémák észlelését. A kompromisszum elfogadható, mert megakadályozza, hogy a rossz változások egyszerre túl nagy hatással legyenek a szolgáltatásra.

A jelenléte túl kevés frissítési tartományok sok negatív mellékhatása. Amíg minden frissítési tartomány nem működik, és a frissítés alatt áll, a teljes kapacitás nagy része nem érhető el. Ha például csak három frissítési tartománnyal rendelkezik, egyszerre a teljes szolgáltatás vagy fürtkapacitás körülbelül egyharmadát veszi le. Miután a szolgáltatás egyszerre annyi nem kívánatos, mert szüksége van elegendő kapacitása a fürt többi részében a számítási feladatok kezeléséhez. A puffer karbantartása azt jelenti, hogy a normál működés során ezek a csomópontok kevésbé töltődnek be, mint egyébként lennének. Ez növeli a szolgáltatás futtatásának költségét.

A környezetben található hiba- vagy frissítési tartományok teljes száma nincs valós korlátja, illetve az átfedések korlátai. De vannak közös minták:

- 1:1 arányban leképezett tartalék tartományok és frissítési tartományok
- Csomópontonként egy frissítési tartomány (fizikai vagy virtuális operációsrendszer-példány)
- Egy "csíkos" vagy "mátrix" modell, ahol a tartalék tartományok és a frissítési tartományok mátrixot alkotnak, ahol a gépek általában futnak le az átlókon

<center>

![A hiba- és frissítési tartományok elrendezése][Image4]
</center>

Nincs a legjobb válasz, amely elrendezést választani. Mindegyiknek vannak előnyei és hátrányai. Az 1FD:1UD modell például egyszerűen beállítható. A csomópontmodellenként egy frissítési tartomány modellje leginkább olyan, mint amit az emberek használnak. A frissítések során minden csomópont egymástól függetlenül frissül. Ez hasonló ahhoz, ahogyan a kis gépkészleteket manuálisan frissítették a múltban.

A leggyakoribb modell az FD/UD mátrix, ahol a tartalék tartományok és a frissítési tartományok egy táblázatot alkotnak, és a csomópontok az átlós mentén helyezkednek el. Ez a modell alapértelmezés szerint az Azure-beli Service Fabric-fürtökben használatos. A sok csomócsomót kiszolgáló fürtök esetében minden úgy néz ki, mint egy sűrű mátrixminta.

> [!NOTE]
> Az Azure-ban üzemeltetett Service Fabric-fürtök nem támogatják az alapértelmezett stratégia módosítását. Csak az önálló fürtök kínálják ezt a testreszabást.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Hiba- és frissítési tartománymegkötések és az ebből eredő viselkedés
### <a name="default-approach"></a>Alapértelmezett megközelítés
Alapértelmezés szerint a Fürterőforrás-kezelő a szolgáltatásokat a hiba- és frissítési tartományok között egyensúlyban tartja. Ez kényszerként van [modellezve.](service-fabric-cluster-resource-manager-management-integration.md) A tartalék- és frissítési tartományok megkötése a következőket mondja: "Egy adott szolgáltatáspartíció esetében soha nem lehet egynél nagyobb különbség a szolgáltatásobjektumok száma (állapotnélküli szolgáltatáspéldányok vagy állapotalapú szolgáltatásreplikálások) között az ugyanazon a két tartomány között. hierarchia szintjét."

Tegyük fel, hogy ez a korlátozás "maximális különbség" garanciát nyújt. A hiba- és frissítési tartományok ravonatkozó korlátozása megakadályozza, hogy bizonyos áthelyezések vagy elrendezések megsértsék a szabályt.

Tegyük fel például, hogy van egy hat csomópontos fürtünk, amely öt tartalék tartománnyal és öt frissítési tartománnyal van konfigurálva.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 között |N2 | | | |
| **UD2 (UD2)** | | |N3 között | | |
| **Ud3** | | | |N4 | |
| **UD4** | | | | |N5 |

Most tegyük fel, hogy hozzon létre egy szolgáltatást a **TargetReplicaSetSize** (vagy egy állapotnélküli szolgáltatás, **InstanceCount)** értéke öt. A replikák az N1-N5-ön landolnak. Valójában, N6 soha nem használt nem anyag mennyi szolgáltatás mint ez ön teremt. De miért? Nézzük meg a különbséget a jelenlegi elrendezés és mi történne, ha N6 választják.

Itt van az elrendezés van, és a replikák száma egy hiba és frissítési tartomány:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDÖsszes |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2 (UD2)** | | |R3 | | |1 |
| **Ud3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDÖsszes** |1 |1 |1 |1 |1 |- |

Ez az elrendezés a tartalék tartományonkénti és frissítési tartományonkénti csomópontok tekintetében kiegyensúlyozott. Ez is kiegyensúlyozott a hiba- és frissítési tartományonkénti replikák száma tekintetében. Minden tartomány azonos számú csomópontos és azonos számú replikával rendelkezik.

Most nézzük meg, mi történne, ha n6-ot használnánk az N2 helyett. Hogyan terjesztenék a replikákat?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDÖsszes |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2 (UD2)** | | |R2 | | |1 |
| **Ud3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDÖsszes** |2 |0 |1 |1 |1 |- |

Ez az elrendezés sérti a "maximális különbség" garancia definícióját a tartalék tartomány megkötésére. Az FD0 két replikával rendelkezik, míg az FD1-nek nulla. Az FD0 és az FD1 közötti különbség összesen kettő, ami nagyobb, mint egy maximális különbség. Mivel a megkötés sérül, a fürterőforrás-kezelő nem engedélyezi ezt az elrendezést. Hasonlóképpen, ha az N2-t és az N6-ot (n1 és N2 helyett) választanánk ki, akkor:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDÖsszes |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2 (UD2)** | | |R2 | | |1 |
| **Ud3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDÖsszes** |1 |1 |1 |1 |1 |- |

Ez az elrendezés a tartalék tartományok szempontjából kiegyensúlyozott. De most ez megsérti a frissítési tartomány megszorítás, mert UD0 nulla replikák és UD1 két. Ez az elrendezés is érvénytelen, és a Fürterőforrás-kezelő nem veszi ki.

Ez a megközelítés az állapotalapú replikák vagy állapotmentes példányok terjesztésére a lehető legjobb hibatűrést biztosítja. Ha egy tartomány leáll, a replikák/példányok minimális száma elvész. 

Másrészt ez a megközelítés lehet túl szigorú, és nem teszi lehetővé a fürt számára, hogy kihasználja az összes erőforrást. Bizonyos fürtkonfigurációk esetében bizonyos csomópontok nem használhatók. Ez azt eredményezheti, hogy a Service Fabric nem helyezi el a szolgáltatásokat, ami figyelmeztető üzeneteket eredményez. Az előző példában néhány fürtcsomópontok nem használhatók (N6 a példában). Még akkor is, ha csomópontokat adott hozzá a fürthöz (N7-N10), a replikák/példányok csak az N1–N5-re kerülnek a hiba- és frissítési tartományok ra vonatkozó korlátozások miatt. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 között |N2 | | | |
| **UD2 (UD2)** | |N7 |N3 között | | |
| **Ud3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>Alternatív megközelítés

A fürterőforrás-kezelő a tartalék- és frissítési tartományok megkötésének egy másik verzióját támogatja. Lehetővé teszi az elhelyezést, miközben garantálja a minimális biztonsági szintet. Az alternatív megkötés a következőképpen adható meg: "Egy adott szolgáltatáspartíció esetében a replikák tartományok közötti elosztása biztosítja, hogy a partíció ne szenvedjen kvórumveszteséget." Tegyük fel, hogy ez a korlátozás "kvórumszéf" garanciát nyújt. 

> [!NOTE]
> Állapotalapú szolgáltatás esetén *kvórumvesztést* határozunk meg olyan helyzetben, amikor a partícióreplikák többsége egy időben nem működik. Ha például **a TargetReplicaSetSize** öt, akkor a kvórumot három kópia készlete jelöli. Hasonlóképpen, ha **a TargetReplicaSetSize** hat, négy kópia szükséges a kvórumhoz. Mindkét esetben legfeljebb két replikák lehet le egy időben, ha a partíció továbbra is normálisan működik. 
>
> Egy állapotnélküli szolgáltatáshoz nincs olyan, hogy *kvórumveszteség.* Az állapotnélküli szolgáltatások továbbra is normálisan működnek, még akkor is, ha az esetek többsége egyszerre csökken. Tehát, mi fog összpontosítani állapotalapú szolgáltatások a többi ezt a cikket.
>

Térjünk vissza az előző példához. A megkötés "kvórumbiztos" verziójával mindhárom elrendezés érvényes lenne. Még akkor is, ha az FD0 nem sikerült a második elrendezésben, vagy ud1 nem sikerült a harmadik elrendezésben, a partíció továbbra is kvórum. (A replikák többsége még mindig fent lenne.) A megkötés ezen verziójával az N6 szinte mindig használható.

A "kvórumbiztos" megközelítés nagyobb rugalmasságot biztosít, mint a "maximális különbség" megközelítés. Ennek az az oka, hogy könnyebb megtalálni a replika disztribúciók, amelyek szinte minden fürt topológiában érvényes. Ez a megközelítés azonban nem garantálja a legjobb hibatűrési jellemzőket, mert egyes hibák rosszabbak, mint mások. 

A legrosszabb esetben a replikák többsége elveszhet egy tartomány és egy további replika meghibásodása miatt. Például ahelyett, hogy három hiba van szükség, hogy elveszíti kvórumöt öt replikák vagy példányok, most már elveszítheti a többséget mindössze két hiba. 

### <a name="adaptive-approach"></a>Adaptív megközelítés
Mivel mindkét megközelítésnek vannak erősségei és gyengeségei, olyan adaptív megközelítést vezettünk be, amely egyesíti ezt a két stratégiát.

> [!NOTE]
> Ez az alapértelmezett viselkedés a Service Fabric 6.2-es verziójával kezdődően. 
> 
> Az adaptív megközelítés alapértelmezés szerint a "maximális különbség" logikát használja, és csak szükség esetén vált a "kvórumbiztonságos" logikára. A fürterőforrás-kezelő automatikusan kitalálja, hogy melyik stratégia szükséges a fürt és a szolgáltatások konfigurálásának módjáról.
> 
> A fürterőforrás-kezelőnek a "kvórumalapú" logikát kell használnia egy szolgáltatáshoz, mindkét feltétel teljesül:
>
> * A szolgáltatás **TargetReplicaSetSize** mérete egyenletesen osztható a tartalék tartományok és a frissítési tartományok száma szerint.
> * A csomópontok száma kisebb vagy egyenlő a tartalék tartományok számának szorzatával és a frissítési tartományok számával.
>
> Ne feledje, hogy a fürterőforrás-kezelő fogja használni ezt a módszert mind állapotnélküli, mind állapotalapú szolgáltatások, annak ellenére, hogy kvórumveszteség nem releváns az állapotmentes szolgáltatások.

Térjünk vissza az előző példához, és tegyük fel, hogy egy fürt most már nyolc csomóponttal rendelkezik. A fürt továbbra is öt tartalék tartománnyal és öt frissítési tartománnyal van konfigurálva, és az adott fürtön üzemeltetett szolgáltatás **TargetReplicaSetSize** értéke továbbra is öt. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 között |N2 | | | |
| **UD2 (UD2)** | |N7 |N3 között | | |
| **Ud3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Mivel minden szükséges feltétel teljesül, a fürterőforrás-kezelő a "kvórumalapú" logikát fogja használni a szolgáltatás elosztásakor. Ez lehetővé teszi az N6-N8 használatát. Az egyik lehetséges szolgáltatáselosztás ebben az esetben így nézhet ki:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDÖsszes |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2 (UD2)** | |R3 |R4 | | |2 |
| **Ud3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDÖsszes** |2 |1 |1 |0 |1 |- |

Ha a szolgáltatás **TargetReplicaSetSize** értéke négyre csökken (például), a fürterőforrás-kezelő észre fogja venni, hogy a változás. A "maximális különbség" logikát használja, mivel a **TargetReplicaSetSize** már nem osztható a tartalék tartományok és a frissítési tartományok számával. Ennek eredményeképpen bizonyos replikamozgások a fennmaradó négy replika elosztására kerülnek az N1-N5 csomópontokon. Így a tartalék tartomány és a frissítési tartomány logikájának "maximális különbség" verziója nem sérül. 

Az előző elrendezésben, ha a **TargetReplicaSetSize** érték öt, és n1 törlődik a fürtből, a frissítési tartományok száma négylesz. A fürterőforrás-kezelő ismét a "maximális különbség" logikát használja, mivel a frissítési tartományok száma nem osztja el egyenletesen a szolgáltatás **TargetReplicaSetSize** értékét. Ennek eredményeképpen az R1 replikának, amikor újra felvan építve, az N4-en kell leszállnia, hogy a hiba- és frissítési tartomány ra vonatkozó korlátozás ne sérüljen.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDÖsszes |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/A |N/A |N/A |N/A |N/A |N/A |
| **UD1** |R2 | | | | |1 |
| **UD2 (UD2)** | |R3 |R4 | | |2 |
| **Ud3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDÖsszes** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Hiba- és frissítési tartományok konfigurálása
Az Azure-üzemeltetett Service Fabric-telepítések, tartalék tartományok és frissítési tartományok automatikusan definiálva vannak. A Service Fabric felveszi és felhasználja az Azure-ból származó környezeti információkat.

Ha saját fürtöt hoz létre (vagy egy adott topológiát szeretne futtatni a fejlesztés során), megadhatja a tartalék tartományt, és saját maga is frissítheti a tartományadatait. Ebben a példában egy kilenc csomópontos helyi fejlesztési fürtöt határozunk meg, amely három adatközpontra (mindegyik három állványra) terjed ki. Ez a fürt is rendelkezik három frissítési tartományok csíkozott a három adatközpontok között. Íme egy példa a ClusterManifest.xml fájl konfigurációjára:

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one box/one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

Ez a példa a ClusterConfig.json t használja az önálló telepítésekhez:

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Amikor fürtöket definiál az Azure Resource Manageren keresztül, az Azure tartalék tartományokat rendel hozzá, és frissíti a tartományokat. Így a csomóponttípusok és a virtuálisgép-méretezési készletek meghatározása az Azure Resource Manager sablonban nem tartalmaz információt a tartalék tartományról vagy a frissítési tartományról.
>

## <a name="node-properties-and-placement-constraints"></a>Csomóponttulajdonságok és elhelyezési megkötések
Néha (valójában az idő nagy részében) érdemes biztosítani, hogy bizonyos számítási feladatok csak bizonyos típusú csomópontok on a fürtben. Előfordulhat például, hogy egyes számítási feladatokhoz GPU-kat vagy SSD-ket kell igényelni, míg másoknem. 

Egy nagyszerű példa a hardver adott számítási feladatokra történő célzására szinte minden n szintű architektúra. Bizonyos gépek szolgálnak az alkalmazás előtér- vagy API-kiszolgáló oldalán, és ki vannak téve az ügyfelek vagy az internet. Különböző gépek, gyakran különböző hardvererőforrásokkal, kezelik a számítási vagy tárolási rétegek munkáját. Ezek általában _nem_ közvetlenül ki vannak téve az ügyfelek vagy az interneten. 

A Service Fabric arra számít, hogy bizonyos esetekben bizonyos számítási feladatok nak bizonyos hardverkonfigurációkon kell futniuk. Példa:

* Egy meglévő n-szintű alkalmazás "lett "lifted and shifted" egy Service Fabric környezetbe.
* A számítási feladatokat teljesítmény, méretezés vagy biztonsági elkülönítés idáig adott hardveren kell futtatni.
* A számítási feladatokat el kell különíteni a többi számítási feladatoktól házirend- vagy erőforrás-felhasználási okokból.

Az ilyen típusú konfigurációk támogatása érdekében a Service Fabric olyan címkéket tartalmaz, amelyek et a csomópontokra alkalmazhat. Ezeket a címkéket *csomóponttulajdonságoknak*nevezzük. *Az elhelyezési megkötések* az egyes szolgáltatásokhoz csatolt utasítások, amelyeket egy vagy több csomóponttulajdonsághoz választ. Az elhelyezési megkötések határozzák meg, hogy a szolgáltatások nak hol kell futniuk. A kényszerek készlete bővíthető. Bármely kulcs/érték pár működhet. 

<center>

![Különböző munkaterhelések a fürtelrendezéshez][Image5]
</center>

### <a name="built-in-node-properties"></a>Beépített csomóponttulajdonságok
A Service Fabric néhány alapértelmezett csomóponttulajdonságot határoz meg, amelyek automatikusan használhatók, így nem kell definiálnia őket. Az egyes csomópontokon definiált alapértelmezett tulajdonságok a **Következők: NodeType** és **NodeName**. 

Írhatunk például elhelyezési megkötést `"(NodeType == NodeType03)"`a néven. **A NodeType** egy általánosan használt tulajdonság. Ez azért hasznos, mert 1:1 arányban felel meg egy géptípusnak. Minden géptípus egy hagyományos n-szintű alkalmazásban egy számítási feladattípusnak felel meg.

<center>

![Elhelyezési megkötések és csomóponttulajdonságok][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>Elhelyezési megkötések és csomóponttulajdonság szintaxisa 
A csomópont tulajdonságban megadott érték lehet karakterlánc, logikai vagy aláírt hosszú. A szolgáltatásban a kimutatást elhelyezési *megkötésnek nevezzük,* mert korlátozza, hogy a szolgáltatás hol futtatható a fürtben. A megkötés bármely logikai utasítás lehet, amely a fürt csomóponttulajdonságain működik. Az érvényes választók ezekben a logikai utasításokban a következők:

* Feltételes ellenőrzések adott kimutatások létrehozásához:

  | Nyilatkozat | Szintaxis |
  | --- |:---:|
  | "egyenlő" | "==" |
  | "nem egyenlő" | "!=" |
  | "nagyobb, mint" | ">" |
  | "nagyobb vagy egyenlő" | ">=" |
  | "kevesebb, mint" | "<" |
  | "kisebb vagy azzal egyenlő" | "<=" |

* Logikai utasítások csoportosításhoz és logikai műveletekhez:

  | Nyilatkozat | Szintaxis |
  | --- |:---:|
  | "és" | "&&" |
  | "vagy" | "&#124;&#124;" |
  | "Nem" | "!" |
  | "csoport, mint egyetlen utasítás" | "()" |

Íme néhány példa az alapvető megkötési utasításokra:

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Csak azok a csomópontok, ahol a teljes elhelyezési megkötési utasítás kiértékeli a "True" lehet a szolgáltatás helyezett rajta. Azok a csomópontok, amelyek nem rendelkeznek definiált tulajdonsággal, nem egyeznek meg a tulajdonságot tartalmazó elhelyezési megkötéssel.

Tegyük fel, hogy a következő csomóponttulajdonságok vannak definiálva egy csomóponttípushoz a ClusterManifest.xml fájlban:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

A következő példa a ClusterConfig.json által önálló telepítésekhez vagy template.json az Azure által üzemeltetett fürtök sablon.json az Azure által üzemeltetett fürtök csomóponttulajdonságain keresztül definiálva. 

> [!NOTE]
> Az Azure Resource Manager-sablonban a csomópont típusa általában paraméterezett. Úgy néz `"[parameters('vmNodeType1Name')]"` ki, mint NodeType01.
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

A szolgáltatáshoz a következőképpen hozhat létre szolgáltatáselhelyezési *korlátozásokat:*

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// Add other required ServiceDescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Ha a NodeType01 összes csomópontja érvényes, kiválaszthatja azt a csomóponttípust is, amely nek van megkötése. `"(NodeType == NodeType01)"`

A szolgáltatás elhelyezési kényszerei futásidőben dinamikusan frissíthetők. Szükség esetén áthelyezhet egy szolgáltatást a fürtben, hozzáadhat és eltávolíthat követelményeket, és így tovább. A Service Fabric biztosítja, hogy a szolgáltatás akkor is rendelkezésre áll, ha ilyen típusú módosítások történnek.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Az elhelyezési megkötések minden elnevezett szolgáltatáspéldányhoz meg vannak adva. A frissítések mindig a korábban megadott (felülírási) helyet veszik át.

A fürtdefiníció határozza meg a csomópontok tulajdonságait. A csomópont tulajdonságainak módosítása a fürtkonfiguráció frissítését igényli. Egy csomópont tulajdonságainak frissítése szükséges minden érintett csomópont újraindítása az új tulajdonságok jelentéséhez. A Service Fabric kezeli ezeket a működés közbeni frissítéseket.

## <a name="describing-and-managing-cluster-resources"></a>Fürterőforrások leírása és kezelése
Az orchestrator egyik legfontosabb feladata, hogy segítsen kezelni a fürt erőforrás-felhasználását. A fürterőforrások kezelése számos különböző dolgot jelenthet. 

Először is, van annak biztosítása, hogy a gépek nem túlterhelt. Ez azt jelenti, hogy győződjön meg arról, hogy a gépek nem fut nak több szolgáltatást, mint amennyit kezelni tudnak. 

Másodszor, van kiegyensúlyozás és optimalizálás, amelyek kritikus fontosságúak a szolgáltatások hatékony futtatásához. A költséghatékony vagy teljesítményérzékeny szolgáltatásajánlatok nem engedhetik meg, hogy egyes csomópontok forróak legyenek, míg mások hidegek. A forró csomópontok erőforrás-versengéshez és gyenge teljesítményhez vezetnek. A hideg csomópontok elpazarolt erőforrásokat és megnövekedett költségeket jelentenek. 

A Service Fabric az erőforrásokat *metrikaként*jelöli. Metrikák bármely logikai vagy fizikai erőforrás, amely szeretné leírni a Service Fabric. Metrikák például a "WorkQueueDepth" vagy a "MemoryInMb." A Service Fabric által a csomópontokon szabályozható fizikai erőforrásokról az [Erőforrás-szabályozás](service-fabric-resource-governance.md)című témakörben talál további információt. A fürterőforrás-kezelő által használt alapértelmezett metrikákról és az egyéni metrikák konfigurálásáról ebben a [cikkben](service-fabric-cluster-resource-manager-metrics.md)olvashat.

A metrikák eltérnek az elhelyezési megkötésektől és a csomóponttulajdonságoktól. A csomóponttulajdonságok maguk a csomópontok statikus leírói. Metrikák ismertetik a csomópontok erőforrásait, és hogy a szolgáltatások felhasználják, amikor futnak egy csomóponton. Egy csomópont tulajdonság lehet **HasSSD,** és lehet, hogy igaz vagy hamis. Az ssd-n rendelkezésre álló terület és a szolgáltatások által felhasznált terület olyan mérőszám lenne, mint a "DriveSpaceInMb". 

Csakúgy, mint az elhelyezési megkötések és a csomópont tulajdonságai, Service Fabric Cluster Resource Manager nem érti, mit jelentenek a metrikák nevét. A metrikanevek csak karakterláncok. Célszerű az egységekdeklarálása a metrikanevek részeként, amelyeket akkor hoz létre, amikor azok kétértelműek lehetnek.

## <a name="capacity"></a>Kapacitás
Ha kivan kapcsolva az összes *erőforrás-kiegyensúlyozás*, Service Fabric fürterőforrás-kezelő továbbra is biztosítaná, hogy egyetlen csomópont sem megy át a kapacitását. A kapacitástúllépések kezelése csak akkor lehetséges, ha a fürt túl tele van, vagy a munkaterhelés nagyobb, mint bármely csomópont. A kapacitás egy másik *olyan korlátozás,* amelyet a fürterőforrás-kezelő a csomópont erőforrásának megértéséhez használ. A fennmaradó kapacitás tavatot is nyomon követ a fürt egésze. 

Mind a kapacitás, mind a szolgáltatásszintű felhasználás metrikákban van kifejezve. A metrika lehet például "Ügyfélkapcsolat", és egy csomópont kapacitása 32 768 "ClientConnections". Más csomópontok nak más korlátai is lehetnek. Az adott csomóponton futó szolgáltatás azt mondhatja, hogy jelenleg 32 256 "Ügyfélkapcsolat" metrikát fogyaszt.

Futásközben a fürterőforrás-kezelő nyomon követi a fürtben és a csomópontokon fennmaradó kapacitást. A kapacitás nyomon követéséhez a fürterőforrás-kezelő kivonja az egyes szolgáltatások használatát egy csomópont kapacitásából, ahol a szolgáltatás fut. Ezzel az információval a fürterőforrás-kezelő kitudja találni, hogy hová helyezze vagy helyezze át a replikákat, hogy a csomópontok ne menjenek túl a kapacitáson.

<center>

![Fürtcsomópontok és kapacitás][Image7]
</center>

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

A fürtjegyzékben definiált kapacitások láthatók. Íme egy példa a ClusterManifest.xml fájlhoz:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Íme egy példa a ClusterConfig.json által önálló telepítésekhez vagy Template.json az Azure által üzemeltetett fürtökhöz definiált kapacitásokra: 

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

A szolgáltatás terhelése gyakran dinamikusan változik. Tegyük fel, hogy a replika "ClientConnections" terhelése 1024-ről 2048-ra változott. A csomópont, amelyen futott, majd kapacitása csak 512 maradt a metrika. Most, hogy a replika vagy példány elhelyezése érvénytelen, mert nincs elég hely a csomóponton. A fürterőforrás-kezelőnek vissza kell szereznie a csomópontot a kapacitás alá. Csökkenti a terhelést a csomóponton, amely kapacitás feletti azáltal, hogy egy vagy több replikák vagy példányok, hogy a csomópont más csomópontok. 

A fürterőforrás-kezelő megpróbálja minimalizálni a replikák áthelyezésének költségeit. A [mozgásköltségről,](service-fabric-cluster-resource-manager-movement-cost.md) valamint a [stratégiák és szabályok kiegyensúlyozásáról](service-fabric-cluster-resource-manager-metrics.md)többet is megtudhat.

## <a name="cluster-capacity"></a>Fürt kapacitása
Hogyan akadályozza meg a Service Fabric fürterőforrás-kezelő a teljes fürt túl teljes? A dinamikus terhelés, nincs sok, amit tehetünk. A szolgáltatások terhelési csúcsa a fürterőforrás-kezelő által végrehajtott műveletektől függetlenül is megemelkedhet. Ennek eredményeként, a klaszter rengeteg belmagasság ma lehet, hogy underpowered, ha van egy tüske holnap. 

A Fürterőforrás-kezelő vezérlői segítenek megelőzni a problémákat. Az első dolog, amit tehetünk, hogy megakadályozzák az új számítási feladatok, amelyek hatására a fürt teljes.

Tegyük fel, hogy hozzon létre egy állapotmentes szolgáltatást, és van hozzá egy kis terhelés. A szolgáltatás törődik a "DiskSpaceInMb" metrika. A szolgáltatás a szolgáltatás minden példányához öt egység "DiskSpaceInMb"-ot használ fel. A szolgáltatás három példányát szeretné létrehozni. Ez azt jelenti, hogy 15 egység "DiskSpaceInMb"-ra van szükség ahhoz, hogy a fürtben jelen legyen, hogy még ezeket a szolgáltatáspéldányokat is létrehozhatja.

A fürterőforrás-kezelő folyamatosan kiszámítja az egyes metrikák kapacitását és felhasználását, így meghatározhatja a fürt ben fennmaradó kapacitást. Ha nincs elég hely, a Fürterőforrás-kezelő elutasítja a szolgáltatás létrehozásához való hívást.

Mivel a követelmény csak az, hogy 15 egység lesz elérhető, akkor osztja ezt a helyet számos különböző módon. Például előfordulhat, hogy 15 különböző csomóponton egy fennmaradó kapacitásegység, vagy három fennmaradó kapacitásegység öt különböző csomóponton. Ha a fürterőforrás-kezelő át tudja rendezni a dolgokat, így három csomóponton öt egység áll rendelkezésre, akkor elhelyezi a szolgáltatást. A fürt átrendezése általában akkor lehetséges, ha a fürt majdnem megtelt, vagy a meglévő szolgáltatások valamilyen okból nem konszolidálhatók.

## <a name="buffered-capacity"></a>Pufferelt kapacitás
A pufferelt kapacitás a fürterőforrás-kezelő egy másik szolgáltatása. Lehetővé teszi a teljes csomópontkapacitás egy részének lefoglalását. Ez a kapacitáspuffer csak a frissítések és a csomóponthibák során szolgál szolgáltatások elhelyezésére. 

Pufferelt kapacitás globálisan van megadva metrika szerint az összes csomóponthoz. A lefoglalt kapacitáshoz választott érték a fürtben található hiba- és frissítési tartományok számától függ. A további hiba- és frissítési tartományok azt jelentik, hogy a pufferelt kapacitáshoz alacsonyabb számot is választhat. Ha több tartománnyal rendelkezik, a frissítések és a hibák során a fürt kisebb mennyiségére számíthat. Pufferelt kapacitás megadása csak akkor van értelme, ha egy metrika csomópontkapacitását is megadta.

Íme egy példa arra, hogyan adhatók meg pufferelt kapacitás a ClusterManifest.xml fájlban:

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

Íme egy példa arra, hogyan adhatja meg a pufferelt kapacitást a ClusterConfig.json on keresztül az önálló telepítésekhez vagy a Template.json t az Azure által üzemeltetett fürtökhöz:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

Az új szolgáltatások létrehozása sikertelen, ha a fürt nem rendelkezik pufferelt kapacitással egy metrika számára. A puffer megőrzését szolgáló új szolgáltatások létrehozásának megakadályozása biztosítja, hogy a frissítések és a hibák ne eredményezzék a csomópontok kapacitástúlterhelését. Pufferelt kapacitás nem kötelező, de azt javasoljuk, hogy minden olyan fürtben, amely meghatározza a kapacitás tanusított a metrika.

A fürterőforrás-kezelő megjeleníti ezt a terhelési információt. Az egyes mutatók esetében ezek az adatok a következőket tartalmazzák: 
- A pufferelt kapacitás beállításai.
- A teljes kapacitás.
- Az aktuális fogyasztás.
- Azt jelzi, hogy az egyes mutatók kiegyensúlyozottnak minősülnek-e vagy sem.
- A szórásra vonatkozó statisztikák.
- Azok a csomópontok, amelyek a legnagyobb és legkevésbé terhelést rendelkeznek.  
  
A következő kód egy példát mutat be erre a kimenetre:

```PowerShell
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>További lépések
* A fürterőforrás-kezelőarchitektúrájáról és információáramlásáról a [Fürterőforrás-kezelő architektúrájának áttekintése című témakörben olvashat.](service-fabric-cluster-resource-manager-architecture.md)
* A töredezettségmentesítési metrikák definiálása az egyik módja a csomópontok terhelésének konszolidálásának, ahelyett, hogy szétosztana azt. A töredezettségmentesítés konfigurálásáról a [metrikák töredezettségmentesítése és a Service Fabric terhelése](service-fabric-cluster-resource-manager-defragmentation-metrics.md)című témakörben olvashat.
* Kezdje az elejétől, és [vezesse be a Service Fabric fürterőforrás-kezelőjét.](service-fabric-cluster-resource-manager-introduction.md)
* A fürterőforrás-kezelő kezeléséről és a fürt terhelésének kiegyensúlyozásáról [a Service Fabric-fürt kiegyensúlyozása](service-fabric-cluster-resource-manager-balancing.md)című témakörben olvashat.

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
