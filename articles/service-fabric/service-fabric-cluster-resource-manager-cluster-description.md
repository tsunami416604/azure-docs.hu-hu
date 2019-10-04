---
title: Fürt leírása a fürterőforrás-kezelő használatával |} A Microsoft Docs
description: Adja meg a tartalék tartományok, a frissítési tartományok, a csomópont tulajdonságait és a fürt(ök) a fürterőforrás-kezelő ismertetik a Service Fabric-fürtön.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 22ccb21a208bbe8e825bff9f7602bfca05990816
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271641"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>A fürterőforrás-kezelő használatával ismertetik a Service Fabric-fürt
A fürterőforrás-kezelő szolgáltatás az Azure Service Fabric több mechanizmust nyújt az fürt leírása:

* Tartalék tartományok
* Frissítési tartományok
* Csomópont tulajdonságai
* Csomópont-kapacitás

Futásidőben a fürterőforrás-kezelő ezeket az adatokat használja a szolgáltatások a fürtben futó magas rendelkezésre állásának biztosításához. Ezek a szabályok fontos kényszerítése, közben is megkísérli a fürtön belüli erőforrás-használat optimalizálására.

## <a name="fault-domains"></a>Tartalék tartományok
A tartalék tartományok az koordinált hiba minden olyan területéhez. Egyetlen gép egy tartalék tartományt. Azt a saját különböző okok miatt a power ellátási hibákra, hibás hálózati adapter belső vezérlőprogramjának meghajtó hibák és sikertelen lehet. 

Gépek, ugyanazon az Ethernet-kapcsoló csatlakozik, az azonos tartalék tartományban található. Áramkimaradás vagy egyetlen helyen, egyetlen adatforrás gépek úgy vannak. 

Mivel a szolgáltatás hardverhiba esetén fedjék át egymást a természetes, tartalék tartományok hierarchikusak természetüknél fogva. Azok a Service Fabric URI-k még jelöli.

Fontos, hogy a tartalék tartomány megfelelően legyenek beállítva, mert a Service Fabric ezen információk segítségével biztonságosan helyezze el a szolgáltatásokat. A Service Fabric nem szeretné, hogy egy tartalék tartományt (néhány összetevő hibáját okozta) elvesztését rendelkezik, a szolgáltatás leáll, helyezze el szolgáltatások. 

Az Azure-környezetben, a Service Fabric a tartalék tartomány információk segítségével a környezet által biztosított megfelelően konfigurálja a csomópontok a fürtben az Ön nevében. A Service Fabric önálló példányát, a tartalék tartományok határozza meg, amelyek a fürt van beállítva. 

> [!WARNING]
> Fontos, hogy a Service Fabric tartalék tartomány információk helyességét. Például tegyük fel, hogy a Service Fabric-fürt csomópontjai 5 fizikai gazdagépeken futó, 10 virtuális gépeken belül futnak. Ebben az esetben, annak ellenére, hogy 10 virtuális gépek vannak, nincsenek csak 5 különböző (a legfelső szintű) tartalék tartományokat. Megosztásához ugyanabban a legfelső szintű tartalék tartományban, virtuális gépek ugyanazon fizikai gazdagéphez okoz, mivel a virtuális gépek koordinált hiba fordulhat elő, ha a fizikai gazdagép meghibásodik.  
>
> A Service Fabric a csomópont nem az, hogy módosítsa a tartalék tartomány vár. Más mechanizmusok, hiszen a magas rendelkezésre állású virtuális gépek, mint például [magas rendelkezésre ÁLLÁSÚ virtuális gépek](https://technet.microsoft.com/library/cc967323.aspx), ütközéseket okozhat a Service Fabric. Ezek a mechanizmusok használata virtuális gépek transzparens áttelepítése az egyik gazdagépről a másikra. Ezeket a nem konfigurálja újra, vagy értesítik a kód futtatása a virtuális gép. Emiatt a számukra *nem támogatott* , környezetekben futó Service Fabric-fürtök. 
>
> A Service Fabric a alkalmazni csak magas rendelkezésre állású technológia kell lennie. Például élő virtuális gépek migrálása és San-OK mechanizmusok nem szükségesek. Ezek a mechanizmusok használata a Service Fabric, együtt azok _csökkentheti_ rendelkezésre állását és megbízhatóságát. Az oka, hogy azok vezeti be a további összetettséget, hozzáadása sikertelen központi forrásai és megbízhatóságot és rendelkezésre állási stratégiát, amely ütközik használata a Service Fabric. 
>
>

A következő ábrán az entitásokat, amelyek hozzájárulnak a tartalék tartományok és az összes, amelyek különböző tartalék tartományok megjelenítése, hogy szín. Ebben a példában van adatközpontok ("DC"), állványokon ("R") és a többi panelen (a "B"). Ha minden panelen egynél több virtuális gép rendelkezik, lehet réteget a tartalék tartomány hierarchiában.

<center>

![Tartalék tartományok keresztül rendezve csomópontok][Image1]
</center>

Futásidőben a Service Fabric a fürterőforrás-kezelő figyelembe veszi a tartalék tartományok a fürtben, és elrendezések tervez. Az állapot-nyilvántartó replikák vagy állapotmentes szolgáltatás példányai vannak osztva, hogy azok külön tartalék tartományokban. A szolgáltatás terjesztése tartalék tartomány között biztosítja, hogy a szolgáltatás rendelkezésre állásának nem sérül, amikor egy tartalék tartomány meghibásodna, a hierarchia minden szintjén.

A fürterőforrás-kezelő nem gondoskodik a tartalék tartomány hierarchiában nincsenek hány rétegek. Megkísérli győződjön meg arról, hogy a hierarchia bármely egy részének elvesztését nem érinti, a futó szolgáltatásokat. 

Emellett akkor ajánlott, ha a csomópontok azonos számú van, minden szinten, a tartalék tartomány hierarchia mélységét. Ha a tartalék tartományok "fa" van a fürtben kiegyensúlyozatlan, ezért jóval nehezebb a fürt Resource Manager felderíthesse a legjobb lefoglalt szolgáltatások. Imbalanced tartalék tartomány elrendezések jelenti azt, hogy az egyes tartományokat érinti a több, mint más tartományok szolgáltatások rendelkezésre állását. Ennek eredményeképpen a fürterőforrás-kezelő levágása között két célt: 

* A gépek használja a "nagy" tartomány például azok a szolgáltatások szeretné. 
* Szeretné helyezi el a szolgáltatások más tartományok, hogy a tartomány elvesztését nem problémákat okozhat. 

Hogyan tegye imbalanced tartományok meg? Az alábbi ábrán látható két különböző fürt elrendezését. Az első példában a csomópontok egyenlően vannak elosztva a tartalék tartományok. A második példa egy tartalék tartomány számos további csomópontokat, mint a többi tartalék tartományban van. 

<center>

![Két különböző fürt elrendezések][Image2]
</center>

Az Azure-ban a választás, mely a tartalék tartomány tartalmaz egy csomópont van kezelve. De kiosztott csomópontok számától függően akkor is továbbra is megtörténhet, amely több csomóponttal rendelkezik, azokat, mint a többi tartalék tartományok. 

Tegyük fel például, a öt tartalék tartományok a fürt rendelkezik, de a csomópont típusa hét csomópont kiépítése (**NodeType**). Ebben az esetben az első két tartalék tartományban kialakított több csomópontot. Ha továbbra is Továbbiak üzembe helyezéséhez **NodeType** példányok csak néhány példányok, a probléma rosszabb beolvasása. Ezért azt javasoljuk, hogy az egyes csomópontok-e a tartalék tartományok száma többszöröse.

## <a name="upgrade-domains"></a>Frissítési tartományok
Frissítési tartományok egy másik szolgáltatás, amely segít a Service Fabric a fürterőforrás-kezelő a fürt elrendezésének ismertetése. Frissítési tartományok, amelyek egy időben vannak frissítve csomópontok készleteinek határozza meg. Frissítési tartományok segítenek a fürterőforrás-kezelő ismertetése és felügyeleti műveleteket, például frissítések vezényli.

Frissítési tartományok sokkal vannak, például a tartalék tartományok, de néhány fontos különbség. Első lépésként koordinált hardver-meghibásodásokkal területeit tartalék tartományok definiálása. Frissítési tartományok, másrészt határozzák meg a házirend. Eldöntheti, hogy hány, ahelyett, hogy a környezet diktálni a számot kap. Tetszőleges számú frissítési tartományok, mint Önnek csomópontok is rendelkezhet. Egy másik tartalék tartományokban és frissítési tartományok közötti különbség az, hogy a frissítési tartományok ne legyenek hierarchikus. Ehelyett azok több mint egy egyszerű címkét. 

Az alábbi ábrán látható három frissítési tartományok szétteríti a három tartalék tartományt. Azt is bemutatja egy lehetséges a három különböző replikába állapotalapú szolgáltatás elhelyezésének, ahol minden egyes említi különböző hibatűrési és frissítési tartományok. Az elhelyezés lehetővé teszi, hogy egy szolgáltatás frissítése közepén tartalék tartomány elvesztését, és a egy példányát a kód és az adatok továbbra is fennáll.  

<center>

![Elhelyezését a tartalék és frissítési tartományok][Image3]
</center>

Vannak, és a frissítési tartományok nagy számú kellene hátrányai. Több frissítési tartományt jelenti azt, hogy a frissítés lépéseinek részletesebb, és kevesebb csomópont vagy szolgáltatásokat érinti. Kevesebb szolgáltatások át kell helyeznie egy időben, a rendszer kevesebb lemorzsolódási bemutatása. Ez általában a megbízhatóság javításához, mert kevesebb, a szolgáltatás bármely jelent meg a frissítés során probléma által érintett. Több frissítési tartományt is jelentheti, hogy szüksége van-e a többi csomóponton a frissítés hatásainak kezeléséhez kevesebb a rendelkezésre álló pufferbe. 

Például ha öt frissítési tartományok, a csomópontok az egyes kezelik körülbelül 20 százalékkal a forgalom. Megnőtt frissítés szempontjából, frissítési tartomány van szüksége, ha a terhelés általában kell valahol meg. Négy van hátra a frissítési tartományok van, mert minden hely számára a teljes forgalom körülbelül 5 %-os kell rendelkeznie. Több frissítési tartományt jelenti azt, hogy kell-e a fürt csomópontjain kisebb puffer. 

Vegye figyelembe, hogy 10 frissítési tartományok inkább rendelkezett. Ebben az esetben mindegyik frissítési tartományon kellene lennie kezelése a teljes forgalom csak körülbelül 10 százaléka. Amikor keresztül a fürt frissítési lépések, minden egyes tartományhoz kell a teljes forgalom csak körülbelül 1.1 százaléka számára elegendő hellyel rendelkezik. Több frissítési tartományt általában lehetővé teszi a csomópontok futtassa magasabb kihasználtságát, mert kevesebb fenntartott kapacitásra van szüksége. Ugyanez igaz a tartalék tartományok.  

A hátránya, hogy hány frissítési tartományt kell, hogy a frissítések általában hosszabb időt vesz igénybe. Rövid idő után a frissítési tartomány befejeződött, és a következő frissítése előtt ellenőrzi a Service Fabric várakozik. Ezek az késleltetések bevezetett a frissítés előtt a frissítés előrehalad észlelését hibák engedélyezése. Az egyensúlyt a fogadható el, mert megakadályozza, hogy rossz módosítások befolyásolják a szolgáltatás túl sok egyszerre.

Túl kevés frissítési tartományok jelenlétének számos negatív hatásai rendelkezik. Minden frissítési tartomány pedig lefelé, és a frissítés alatt áll, a teljes kapacitás egy nagy része nem érhető el. Például ha rendelkezik frissítési tartományok csak három, készíti fel készül egyharmada az általános szolgáltatás vagy a fürt kapacitásának egyszerre. Kellene nagy részét a szolgáltatás le egyszerre nem kívánatos, mert a számítási feladatok kezelésére a fürt többi elegendő kapacitásra van szüksége. Karbantartása, a puffer azt jelenti, hogy normál működés során, azokat a csomópontokat is, kisebb, mint egyébként betöltve. Ez növeli a szolgáltatás futtatásával járó költségeket.

Nincs valós egy környezetet, vagy korlátozza a átfedési módjának tartalék vagy a frissítési tartományok száma korlátozva van. Azonban gyakori minták:

- Tartalék tartományokban és frissítési tartományokba, 1:1 leképezve
- Több frissítési tartományt csomópontonként (fizikai vagy virtuális operációsrendszer-példány)
- Ha a tartalék tartományokban és frissítési tartományok alkotnak mátrix le a átlói általában futtató géppel "csíkozott" vagy "mátrixban" modell

<center>

![A tartalék és frissítési tartományok elrendezések][Image4]
</center>

Nincs elrendezést, válassza ki a legjobb válasz. Minden egyes vannak előnyei és hátrányai. Ha például a 1FD:1UD modellje könnyen beállítható. / Csomópont modell több frissítési tartományt modellje legtöbb milyen személyek hasonlóan használhatók. Rendszerre minden egyes csomópont frissítése egymástól függetlenül. Ez a rövid hogyan gépek csoportok lettek frissítve manuálisan az elmúlt hasonló.

A leggyakrabban használt modell az FD/UD mátrix, ahol tartalék tartományokban és frissítési tartományokba, az űrlap-tábla és a csomópontok kerülnek a átlós mentén indítása. Ez az alapértelmezés szerint a Service Fabric-fürtök az Azure-ban használt modell. A sok csomóponttal rendelkező fürtök esetén minden említi hasonló sűrű mátrix mintát.

> [!NOTE]
> Az Azure-ban üzemeltetett Service Fabric-fürtök nem támogatják a alapértelmezett stratégiát. Csak önálló fürtök a testreszabási kínálnak.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Hibatűrési és frissítési tartomány korlátozások és az eredményül kapott viselkedés
### <a name="default-approach"></a>Alapértelmezett módszer
Alapértelmezés szerint a fürterőforrás-kezelő megőrzi a szolgáltatások hibatűrési és frissítési tartományok között. Ez van modellezve a [megkötés](service-fabric-cluster-resource-manager-management-integration.md). A tartalék és frissítési tartományok államokra vonatkozó korlátozás: "Egy adott szolgáltatás partíció soha nem kell különbséget szolgáltatási objektumok (állapotmentes szolgáltatás példányainak vagy állapotalapú szolgáltatás replikák) ugyanazon a szinten hierarchia két tartomány közötti száma egynél nagyobb."

Tegyük fel, hogy ezt a korlátozást garantálja "legnagyobb különbség a". A tartalék és frissítési tartományokba korlátozását megakadályozza, hogy bizonyos áthelyezését vagy a szabályt megsértő szabályokat.

Például tegyük fel, hogy van-e egy fürtbe, hat csomópont konfigurálva öt tartalék tartományok és öt frissítési tartománnyal.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Most tegyük fel, hogy létrehozzuk a szolgáltatás, amely egy **TargetReplicaSetSize** (vagy egy állapotmentes szolgáltatás **InstanceCount**) értékét öt. A replikák N1-N5 megjelenni. N6 valójában soha nem használatos függetlenül attól, hogy hány szolgáltatások, például ez hoz létre. De miért? Tekintsük át a jelenlegi elrendezéshez, és hogy mi történne, ha ki van választva N6 közötti különbség.

A következő fájt az elrendezést és a tartalék és frissítési tartományonként replikák száma:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Ez az elrendezés csomópontok maximális száma a tartalék és frissítési tartomány szempontjából kiegyensúlyozott. Azt is hibatűrési és frissítési tartományonként replikák száma szempontjából kiegyensúlyozott. Minden tartományban a csomópontok azonos számú és replikák azonos számú rendelkezik.

Most nézzük, mi történne N6 helyett N2 kellett használtuk. Hogyan szeretné a replikák osztják majd?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Ez az elrendezés megsérti a definíció "legnagyobb különbség a" garancia a tartalék tartomány megkötés. FD0 két replika van, mivel a FD1 nulla. FD0 és FD1 közötti különbség a két, összesen nagyobb, mint az egyik legnagyobb különbség. A korlátozás sérül, mert a fürterőforrás-kezelő nem engedélyezi az ezzel az elrendezéssel fokozott. Hasonlóképpen ha azt kivételezett N2 és N6 (helyett N1 és N2), kapunk:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Ez az elrendezés tartalék tartományok szempontjából kiegyensúlyozott. De most a frissítési tartomány korlátozás megsértése, van, mert UD0 nulla replikák pedig UD1 két. Ez az elrendezés is érvénytelen, és nem tárolható a fürt Resource Manager által.

Ez a módszer az állapot-nyilvántartó replikák vagy állapotmentes példányok elosztása a legjobb lehetséges hibatűrést biztosít. Ha egy tartományi leáll, a replikák és példányok minimális száma elvész. 

Másrészről ezt a módszert kell túl szigorú és a fürt összes erőforrásainak használatát teszi lehetővé. Bizonyos fürtkonfigurációkat az egyes csomópontok nem használható. Emiatt a Service Fabric nem helyezhető el a szolgáltatásokat, a figyelmeztető üzenetek eredményez. Az előző példában a fürt csomópontjainak egy része nem lehet (a példában N6) használt. Akkor is, ha a létrehozott fürtre (N7-N10) hozzáadott csomópontokat, replikák és példányok hibatűrési és frissítési tartományokban megkötések miatt csak az N1 – N5 szeretné elhelyezni. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>Alternatív megoldás

A fürterőforrás-kezelő hibatűrési és frissítési tartományok a korlátozás egy másik verziója támogatja. Elhelyezés során is teheti a minimális szintű biztonság lehetővé teszi. Az alternatív korlátozás is meg kell adni a következő: "Egy adott szolgáltatás partícióhoz tartozó replika elosztása a tartományok biztosítania kell, hogy a partíció nem érinti a kvórum elvesztése." Tegyük fel, hogy ezt a korlátozást garantálja "csökkentett kvórum". 

> [!NOTE]
> Egy állapotalapú szolgáltatás meghatározzuk *kvórum elvesztése* olyan helyzetekben, amikor a partíciók replikáit többsége nem működik egy időben. Például ha **TargetReplicaSetSize** öt, bármely három replika készletét kvórum jelöli. Hasonlóképpen ha **TargetReplicaSetSize** van hat, négy replikák kvóruma számára szükségesek. Mindkét esetben legfeljebb két replika is nem működik egyszerre, ha a partíció szeretné a szokásos módon működhet tovább. 
>
> Az állapotmentes szolgáltatás, nem nincs *kvórum elvesztése*. Állapotmentes szolgáltatások továbbra is megfelelően működik, akkor is, ha egyszerre leáll példányok többségét. Tehát az állapotalapú szolgáltatások Ez a cikk további részében fogunk összpontosítani.
>

Lépjen vissza az előző példában. A korlátozás "csökkentett kvórum" verziójával összes három elrendezések lesz érvényes. Még akkor is, ha FD0 sikertelen volt, a második elrendezés, vagy UD1 sikertelen volt, a harmadik elrendezés, a partíció továbbra is kellene kvórum. (A replikák többsége még mindig lenne fel.) Ez a korlátozás verziójával N6 szinte mindig használhatók fel.

A "biztonságos kvórum" megközelítést, mint a "legnagyobb különbség a" megközelítés nagyobb rugalmasságot nyújt. A hiba oka, hogy egyszerűbb legyen a replika disztribúciókat, amelyek érvényesek a szinte bármilyen fürtjének topológiája találhat. Azonban ez a megközelítés nem garantálja a legjobb tartalék tolerancia jellemzők mivel bizonyos hibák rosszabb, mint mások. 

A legrosszabb esetben a replikák többsége lehet egy tartomány és a egy további replika hibával elveszett. Például ahelyett, hogy elveszíti a kvórumot öt replikákat vagy a példányok három hibák, most elveszhet csak két hibák többségét. 

### <a name="adaptive-approach"></a>Az adaptív módszer
Mindkét módszerénél előnyeiről és hátrányairól rendelkezik, mert jelentettük adaptív megközelítés, amely egyesíti e két stratégia szerint.

> [!NOTE]
> Ez az az alapértelmezett viselkedést, a Service Fabric verziója 6.2 kezdve. 
> 
> Az adaptív módszer alapértelmezés szerint a "legnagyobb különbség a" logikai használ, és, csak szükség esetén a "biztonságos kvórum" logikai kapcsolók. A fürterőforrás-kezelő automatikusan kitalálja, hogy mely stratégia szükség, megnézzük, hogyan vannak konfigurálva a fürt és a szolgáltatásokat.
> 
> A fürterőforrás-kezelő kell használnia a "kvórum alapján" logika, a szolgáltatás mindkét feltétel teljesül:
>
> * **TargetReplicaSetSize** egyenlően osztható fel a frissítési tartományok száma és a tartalék tartományok száma a szolgáltatás számára.
> * A csomópontok számát kisebb vagy egyenlő a frissítési tartományok száma szorozva tartalék tartományokat.
>
> Hogy a fürterőforrás-kezelő ezt a módszert használja a is állapot nélküli és állapotalapú szolgáltatások esetében figyelembe kell vennie annak ellenére, hogy a kvórum elvesztése nem kell figyelembe venni az állapotmentes szolgáltatások esetében.

Lépjen vissza az előző példával, és feltételezik, hogy a fürt most már rendelkezik-e a nyolc csomópontok. A fürt továbbra is van konfigurálva a öt tartalék tartományok és öt frissítési tartománnyal, és a **TargetReplicaSetSize** öt továbbra is, hogy a fürt által futtatott szolgáltatás értéket. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Az összes szükséges feltételek teljesülnek, mert a fürterőforrás-kezelő a "kvórum alapján" logika terjesztése a szolgáltatást fogja használni. Ez lehetővé teszi a N6-N8 használatát. Egy lehetséges szolgáltatás terjesztési ebben az esetben ehhez hasonló lehet:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Ha a szolgáltatás **TargetReplicaSetSize** érték csökken négyre (például), a fürterőforrás-kezelő megfigyelheti, hogy a módosítás. A "legnagyobb különbség a" logikai kapcsolattal, mert folytatódik **TargetReplicaSetSize** nincs többé oszthatónak tartalék tartományokban és frissítési tartományok száma alapján. Ennek eredményeképpen egyes replika típusú áthelyezések N1-N5 csomóponton a fennmaradó négy replikák terjeszteni fog előfordulni. Ezzel a módszerrel a tartalék tartomány és a frissítési logika "legnagyobb különbség a" verziója nem sérül. 

Az előző elrendezésben Ha a **TargetReplicaSetSize** érték öt és N1 eltávolítása a fürtből, a frissítési tartományok száma négy válik. Újra, a fürterőforrás-kezelő "legnagyobb különbség a" logic használatával, mert a frissítési tartományok száma nem egyenletes felosztása a szolgáltatás elindul-e **TargetReplicaSetSize** többé értékét. Ennek eredményeképpen a replika R1, beépített újra, ha rendelkezik N4 megjelenni, hogy a tartalék és frissítési tartomány a korlátozás nem sérül.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |– |N/A |N/A |N/A |N/A |– |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Tartalék és frissítési tartományok konfigurálása
Az Azure-ban tárolt Service Fabric-telepítések tartalék tartományokban és frissítési tartományok definiálása automatikusan történik. A Service Fabric szerzi be, és a környezeti adatokkal, az Azure-ból.

Ha saját fürtöt hoz létre (vagy szeretne futtatni egy adott topológia fejlesztői), adja meg a tartalék tartományt, és frissítési tartomány információ saját magának. Ebben a példában egy kilenc csomópontos helyi fejlesztési fürtöt három adatközpontok (mindegyik három állványt) is meghatározzuk. Ez a fürt ezen három adatközpontok szétteríti három frissítési tartományt is tartalmaz. Íme egy példa a konfigurációjának ClusterManifest.xml:

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

Ebben a példában az önálló verziója telepítéseinek ClusterConfig.json használja:

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
> Definiálásakor fürtök az Azure Resource Manageren keresztül, az Azure engedményeseire tartalék tartományt, és frissítési tartományokba. A csomóponttípusok és virtuálisgép-méretezési csoport definíciójának állít be, az Azure Resource Manager-sablon nem tartalmazza a tartalék tartomány és frissítési tartomány.
>

## <a name="node-properties-and-placement-constraints"></a>Csomópont tulajdonságai és elhelyezési korlátozások
Néha (valójában a legtöbbször) érdemes győződjön meg arról, hogy bizonyos munkaterhelések csak bizonyos típusú, a fürtben található csomópontok futtassa. Például előfordulhat, hogy egyes számítási feladatokhoz gpu-kat vagy SSD-k, és nem lehet másokkal. 

Egy remek példa célzó hardvert, hogy a számítási feladatok, szinte minden n szintű architektúrához. Egyes gépek állnak az az előtérben vagy a kérelem API-kiszolgáló oldalára, és ki vannak téve az ügyfelek és az internet. Különböző gépek, gyakran különböző a hardveres erőforrások hajtja végre a számítási és tárolási rétegeket. Ezek általában olyan _nem_ el közvetlenül ügyfeleknek, illetve az interneten. 

A Service Fabric vár, hogy bizonyos esetekben számítási feladatok esetleg futtatnia kell az adott hardverkonfigurációk. Példa:

* Egy meglévő n szintű alkalmazás lett "vissza, és hogy áttért" a Service Fabric-környezetbe.
* Egy számítási feladat teljesítményét, a méretezési csoport vagy a biztonsági elkülönítés okokból adott hardveren kell futtatni.
* Számítási feladatok más számítási feladatok a házirend vagy az erőforrás felhasználási okokból elkülönítve kell lennie.

Ezek számos különféle konfigurációk támogatása érdekében a Service Fabric csomópontokra alkalmazható-címkét tartalmaz. Ezekkel a címkékkel nevezzük *csomópont tulajdonságai*. *Elhelyezési korlátozások* az egyes szolgáltatásai, a választott ki egy vagy több csomópont-tulajdonságok csatolt utasításokat. Elhelyezési korlátozások határozza meg, ahol szolgáltatásainak futnia kell. A korlátozások rendkívül bővíthető. Minden olyan kulcs-érték pár is működik. 

<center>

![Fürt elrendezés különböző számítási feladatok][Image5]
</center>

### <a name="built-in-node-properties"></a>Beépített csomópont tulajdonságai
A Service Fabric néhány használható automatikusan így megadásukhoz nem szükséges alapértelmezett csomópont tulajdonságait határozza meg. Az alapértelmezett tulajdonság meg van határozva a csomópontok **NodeType** és **csomópontnév**. 

Ha például egy elhelyezési korlátozás, írhat `"(NodeType == NodeType03)"`. **NodeType** a gyakran használt tulajdonságot. Ez akkor hasznos, mert a gép egy típusú megfelel 1:1. Egyes típusú gépek olyan típusú számítási feladatok egy hagyományos n szintű alkalmazás felel meg.

<center>

![Elhelyezési korlátozások és a csomópont tulajdonságait][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>Elhelyezési korlátozások és a csomópont tulajdonság szintaxis 
A csomópont tulajdonságban megadott érték lehet egy karakterláncot, logikai érték, vagy hosszú ideig aláírással. Az utasítás a szolgáltatás-elhelyezési nevezzük *megkötés* , mert azt korlátozza, ahol a szolgáltatás futtatható a fürtben. A korlátozás a csomópont-tulajdonságok a fürtben működő logikai utasítás lehet. A következő logikai utasításokat a érvényes választók a következők:

* Feltételes ellenőrzi az adott utasítások létrehozásához:

  | Utasítás | Szintaxis |
  | --- |:---:|
  | "egyenlő" | "==" |
  | "nem egyenlő" | "!=" |
  | "nagyobb, mint" | ">" |
  | "nagyobb, mint vagy egyenlő" | ">=" |
  | "kisebb, mint" | "<" |
  | "kisebb vagy egyenlő" | "<=" |

* Logikai utasítások csoportosítási és logikai műveletekhez:

  | Utasítás | Szintaxis |
  | --- |:---:|
  | "és" | "&&" |
  | "vagy" | "&#124;&#124;" |
  | "nem" | "!" |
  | "egyetlen utasítás csoportot" | "()" |

Íme néhány példa az alapvető utasításokat:

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Ha "True" való kiértékelése által az általános elhelyezési korlátozás utasítás csak a csomópontok lehet rá a szolgáltatás. A definiált tulajdonsággal nem rendelkező csomópontok bármilyen elhelyezési korlátozás, amely tartalmazza a tulajdonság nem egyezik.

Tegyük fel, hogy a következő csomópont-tulajdonságok lettek definiálva ClusterManifest.xml a csomópont típusa:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

Az alábbi példa bemutatja a csomópont tulajdonságait keresztül ClusterConfig.json önálló verziója telepítéseinek vagy meghatározott Template.json Azure-ban üzemeltetett fürtök esetén. 

> [!NOTE]
> Az Azure Resource Manager-sablonban a csomópont típusa általában paraméterezni. Ehhez hasonlóan néz ki `"[parameters('vmNodeType1Name')]"` NodeType01 helyett.
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

Szolgáltatás-elhelyezési hozhat létre *megkötések* egy szolgáltatáshoz, az alábbiak szerint:

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

Ha NodeType01 összes csomópontja érvényes, választhatja a korlátozás az adott csomóponttípus `"(NodeType == NodeType01)"`.

Egy szolgáltatás-elhelyezési megkötések dinamikusan futásidőben lehet frissíteni. Kell, ha egy szolgáltatás Navigálás a fürtben, adja hozzá, és távolítsa el a követelmények, és így tovább. A Service Fabric biztosítja, hogy a szolgáltatás marad felfelé és a rendelkezésre álló akkor is, ha az ilyen típusú módosításokat végzett.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Minden elnevezett szolgáltatáspéldányokban elhelyezési korlátozások vannak megadva. Frissítések mindig is a hely (felülírása) milyen előzőleg meg volt adva.

A fürt definíciója tulajdonságok meghatározása egy csomóponton. A csomópont tulajdonságainak módosítása a fürtkonfiguráció frissíteni kell. Minden érintett csomópont újraindítása új tulajdonságainak jelentéséhez a csomópont-tulajdonságok frissítése szükséges. A Service Fabric kezeli a működés közbeni frissítése.

## <a name="describing-and-managing-cluster-resources"></a>Kivizsgáljuk a problémát, és a fürt erőforrásainak kezelése
A legfontosabb feladatok bármely orchestrator egyik erőforrás-használat, a fürt kezeléséhez. Fürterőforrások kezelése jelenti pár másik dolgot. 

Először is van annak ellenőrzése, hogy a gépek nem állnak túlterhelt. Ez azt jelenti, hogy a gépek nem futnak, mint azok képes kezelni a további szolgáltatások biztosításához. 

A második nincs terheléselosztási és optimalizálás, amelyek kritikus fontosságúak a hatékony szolgáltatások futtatása. Költséghatékony, vagy a teljesítmény-és nagybetűket szolgáltatásajánlatok néhány gyakori elérésű kell, míg mások ritkán használt csomópontok nem engedélyezheti. Gyakori elérésű csomópontok erőforrás-versengéshez és gyenge teljesítménnyel vezethet. Ritkán használt csomópontok csökken az elpazarolt erőforrások és a nagyobb költségek képviseli. 

A Service Fabric az erőforrásokhoz, mint egy *metrikák*. Mérőszám játszik bármely ismertetik a Service Fabric kívánt logikai és fizikai erőforrás. Metrikák példák "WorkQueueDepth" vagy "MemoryInMb." A fizikai erőforrásokat, amelyek képesek felügyelni a Service Fabric csomópontokon információ: [erőforrás-szabályozás](service-fabric-resource-governance.md). Egyéni metrikák és a használatukat konfigurálásával kapcsolatos további információkért lásd: [Ez a cikk](service-fabric-cluster-resource-manager-metrics.md).

Metrikák elhelyezési korlátozások és csomópont-tulajdonságok eltérnek. Csomópont tulajdonságai statikus leírójára magukat a csomópontokat. Metrikák csomópontokat rendelkező erőforrásokat és, hogy a szolgáltatások felhasználásához, amikor egy csomóponton futnak ismertetik. Lehet, hogy a csomópont-tulajdonságok **HasSSD** , és IGAZ vagy hamis be lehet állítani. Szabad terület mennyisége a SSD és a szolgáltatások mennyi felhasznált lenne egy metrikát, például a "DriveSpaceInMb." 

Ugyanúgy, mint az elhelyezési korlátozások és a csomópontok tulajdonságai mellett a Service Fabric-fürt Resource Manager nem ismeri a metrikák középérték milyen nevét. Metrikák nevei csak karakterláncok. Tanácsos egységek deklarálja a metrikák nevei nem egyértelmű lehetnek létrehozott részeként.

## <a name="capacity"></a>Kapacitás
Ha kikapcsolt minden erőforrás *terheléselosztási*, Service Fabric a fürterőforrás-kezelő továbbra is biztosítja, hogy nem csomópont feletti a maximális kapacitást kerül. Kapacitás meghaladása kezelése, lehetséges, hacsak nem a fürthöz nincs elég hely, vagy a számítási feladatok nagyobb, mint bármely csomópont. Kapacitás egy másik *megkötés* , hogy használja-e a fürterőforrás-kezelő megérteni, milyen egy erőforrást egy csomópont van. Fennmaradó kapacitás is nyomon követi a fürt teljes. 

A kapacitás és a szolgáltatási szintű a használat metrikák vannak kifejezve. Például lehet, hogy a metrika "ClientConnections", és a egy csomópont lehet egy 32 768 "ClientConnections" kapacitást. Más csomópontok egyéb korlátozások is rendelkeznek. Ezen a csomóponton futó szolgáltatásban mondhatjuk azt jelenleg használja a 32,256 mérőszám "ClientConnections."

A fürterőforrás-kezelő futásidőben, nyomon követi a fennmaradó kapacitás, a fürt és a csomópontokon. Kapacitás nyomon követéséhez a fürterőforrás-kezelő kivonja a minden szolgáltatás használata a csomópont kapacitását, ahol a szolgáltatás fut-e. Ezekkel az információkkal a fürterőforrás-kezelő ismerhetik fel a hol helyezze el vagy helyezhetik át a replikák úgy, hogy a csomópont feletti kapacitás nem lépjen.

<center>

![Fürtcsomópontok és a kapacitás][Image7]
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

A fürtjegyzékben definiált kapacitások látható. Íme egy példa a ClusterManifest.xml:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Íme egy példa kapacitások Azure-ban üzemeltetett fürtök önálló verziója telepítéseinek vagy a Template.json számára meghatározott ClusterConfig.json keresztül: 

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

Egy szolgáltatás gyakran módosítások dinamikusan betölteni. Tegyük fel, hogy egy replika terhelése "ClientConnections" beállítás változása: 1024, 2048. A csomópont, amely futott a majd kellett van hátra a metrika csak 512 kapacitását. Most már a, hogy a replika és példány elhelyezése érvénytelen, mert nem elég hely a csomóponton. A fürterőforrás-kezelő beolvasni a csomópont vissza a kapacitás alatt van. Csökkenti a terhelést a csomóponton, amely szerint egy vagy több replika vagy példányok adott csomópontról kerüljön át más csomópontokra kapacitás felett van. 

A fürterőforrás-kezelő próbálja meg minimálisra csökkentheti a replikák helyezi át. További információ [a mozgás költsége](service-fabric-cluster-resource-manager-movement-cost.md) és [stratégiákat és szabályok újraegyensúlyozása](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Fürt kapacitása
Hogyan a Service Fabric fürterőforrás-kezelő a túl megtelt a teljes fürtöt megtarthatja? Dinamikus terhelésjelentés-nem áll sok végezhet. Szolgáltatások a terhelés kiugrás, amely a fürterőforrás-kezelő műveletek függetlenül is rendelkezhet. Ennek eredményeképpen a fürtön a rengeteg hely maradt még ma underpowered holnap ugrásszerű Ha lehet. 

A fürterőforrás-kezelő vezérlők problémák megelőzése érdekében. Az első dolog, amit az megakadályozza az új számítási feladatok, amelyek miatt a fürt teljes válik létrehozását.

Tegyük fel, hogy az állapotmentes szolgáltatás létrehozása, és néhány terhelés társítva van. A szolgáltatás a "DiskSpaceInMb" metrika ügyel. A szolgáltatás "DiskSpaceInMb" a szolgáltatás minden példányát öt egységet fog felhasználni. Szeretne létrehozni a szolgáltatás három példányban. Ez azt jelenti, hogy "DiskSpaceInMb" megtalálható a fürtöt hozhat létre akár a szolgáltatáspéldányok 15 egysége van szüksége.

A fürterőforrás-kezelő folyamatosan számítja ki a kapacitást, és mindegyik metrikát fogyasztásának így meg tudja határozni a fennmaradó kapacitás a fürtben. Nincs elég hely, ha a fürterőforrás-kezelő elutasítja a hívást a szolgáltatás létrehozása.

Mivel az a követelmény csak 15 egységek lesz elérhető, számos különböző módon foglalhat ezen a területen. Ha például van egy fennmaradó kapacitási egység, 15 különböző csomópontokon, vagy öt különböző csomópontokon három további kapacitásegységek. A fürterőforrás-kezelő is átrendezheti dolog, ezért érhetők el öt egység három csomóponton, ha a szolgáltatás helyezi. A fürt átrendezése általában lehetőség, kivéve ha a fürt majdnem megtelt vagy valamilyen okból kifolyólag nem lehet összevont a meglévő szolgáltatások.

## <a name="buffered-capacity"></a>Pufferelt kapacitás
Pufferelt kapacitása a fürterőforrás-kezelő egy másik szolgáltatás. Lehetővé teszi bizonyos része a teljes csomópont-kapacitás foglalása. Ez a kapacitás puffer csak szolgáltatások elhelyezése során csomóponthibáknak frissítések és verziófrissítések szolgál. 

Pufferelt kapacitás globálisan az összes csomópont metrikánként van megadva. A fenntartott kapacitás a választott érték, amely a fürt rendelkezik hibatűrési és frissítési tartományok száma függvénye. Több tartalék és frissítési tartományokba jelenti azt, hogy kevesebb választhat a pufferelt kapacitás. Ha több tartományom van, a fürt nem érhető el, frissítések és a rendszerhibák kisebb mennyiségű várható. Adja meg a pufferelt kapacitás értelme csak akkor, ha egy metrika a csomópont kapacitását is megadott.

A következő példa bemutatja, hogyan ClusterManifest.xml pufferelt kapacitás megadása:

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

A következő példa bemutatja, hogyan keresztül ClusterConfig.json pufferelt kapacitás Azure-ban üzemeltetett fürtök önálló verziója telepítéseinek vagy Template.json megadásához:

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

Új szolgáltatás létrehozása sikertelen lesz, amikor a fürt egy metrika pufferelt kapacitása. Az új szolgáltatások megőrzése érdekében a puffer megelőzése biztosítja a frissítéseket és a hibák haladnak át a kapacitás-csomópontokat nem okoznak. Pufferelt kapacitás nem kötelező, de javasoljuk, hogy azt minden olyan fürtben, amely meghatározza egy metrika a kapacitása.

A fürterőforrás-kezelő a betöltési információk tesz elérhetővé. Mindegyik metrikát ezeket az információkat tartalmazza: 
- A pufferelt kapacitás beállításait.
- A teljes kapacitás.
- A jelenlegi felhasználás.
- Elosztott terhelésű e mindegyik metrikát számít, vagy sem.
- A szórás statisztikája.
- A csomópontokat, amelyeken a legtöbb és legalább terhelés.  
  
A következő kód bemutatja egy példa a kimenetre:

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
* Az architektúra és információk folyamat belül a fürterőforrás-kezelő kapcsolatos tudnivalókat lásd: [architektúrájának áttekintése a fürterőforrás-kezelő](service-fabric-cluster-resource-manager-architecture.md).
* Lemeztöredezettség-mentesítés metrikák meghatározása módja egy konszolidálhatja helyett ezzel azt csomópontok terhelése. Töredezettségmentesítési konfigurálásával kapcsolatban lásd: [töredezettségmentesítésével, metrikák és a Service Fabric terhelés](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Elölről kezdődik, és [Ismerkedjen meg a Service Fabric a fürterőforrás-kezelő](service-fabric-cluster-resource-manager-introduction.md).
* Című cikk nyújt tájékoztatást, hogyan kezeli a fürterőforrás-kezelő, és elosztja a terhelést a fürtben, [terheléselosztás a Service Fabric-fürt](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
