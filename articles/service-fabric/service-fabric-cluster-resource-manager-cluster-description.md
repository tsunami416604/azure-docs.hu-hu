---
title: A fürterőforrás-kezelő fürt leírása |} A Microsoft Docs
description: Ismertető a Service Fabric-fürt tartalék tartományok, a frissítési tartományok, a csomópont tulajdonságait és a fürt(ök) számára a fürterőforrás-kezelő megadásával.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 810388a85e4ad339ff1444d21ac231fe4c00aeac
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120533"
---
# <a name="describing-a-service-fabric-cluster"></a>Ismertető a service fabric-fürt
A Service Fabric fürterőforrás-kezelő több mechanizmust nyújt az fürt leírása. Futásidőben a fürterőforrás-kezelő ezeket az adatokat használja a szolgáltatások a fürtben futó magas rendelkezésre állásának biztosításához. Ezek a szabályok fontos kényszerítése, közben is megkísérli a fürtön belüli erőforrás-használat optimalizálása érdekében.

## <a name="key-concepts"></a>Fő fogalmak
A fürterőforrás-kezelő számos funkciót, amely a fürt leírása támogatja:

* Tartalék tartományok
* Upgrade Domains
* Csomópont tulajdonságai
* Csomópont-kapacitás

## <a name="fault-domains"></a>Tartalék tartományok
A tartalék tartományok az koordinált hiba minden olyan területéhez. Egyetlen gép egy tartalék tartományt, (mivel, sikertelen lehet a kiemelt ellátási hibák esetén a meghajtó esetén hibás hálózati adapter belső vezérlőprogramjának a saját, a különböző okok miatt). Ugyanazon az Ethernet-kapcsoló csatlakozik gépek mindegyike ugyanabban a tartalék tartományban, a virtuális gépek megosztása egy egyetlen forrásból áramkimaradás vagy egyetlen helyen. Mivel természetes hardverhiba esetén átfedésben vannak, a tartalék tartományok természetüknél fogva hierarchikus és jelentésekként jelennek meg a Service Fabric URI-k.

Fontos, hogy a tartalék tartomány megfelelően legyenek beállítva, mivel a Service Fabric ezen információk segítségével biztonságosan helyezze el a szolgáltatásokat. A Service Fabric nem szeretné, hogy egy tartalék tartományt (néhány összetevő hibáját okozta) elvesztését rendelkezik, a szolgáltatás leáll, helyezze el szolgáltatások. Az Azure Service Fabric környezet a tartalék tartomány információk segítségével a környezet által biztosított megfelelően konfigurálja a csomópontok a fürtben az Ön nevében. Service Fabric önálló, a tartalék tartományok határozza meg, amelyek a fürt van beállítva 

> [!WARNING]
> Fontos, hogy a Service Fabric tartalék tartomány információk helyességét. Például tegyük fel, hogy a Service Fabric-fürt csomópontjai öt fizikai gazdagépeken futó, 10 virtuális gépeken belül futnak. Ebben az esetben, annak ellenére, hogy 10 virtuális gépek vannak, nincsenek csak 5 különböző (a legfelső szintű) tartalék tartományokat. Virtuális gépek ugyanabban a legfelső szintű tartalék tartományban, megosztás, mivel a virtuális gépek koordinált hiba fordulhat elő, ha a fizikai gazdagép meghibásodik az ugyanazon fizikai gazdagéphez okoz.  
>
> A Service Fabric a csomópont nem az, hogy módosítsa a tartalék tartomány vár. Más mechanizmusok, hiszen a magas rendelkezésre állású virtuális gépek például [magas rendelkezésre ÁLLÁSÚ virtuális gépek](https://technet.microsoft.com/library/cc967323.aspx) okozhat ütközik a Service Fabric mint transzparens áttelepítés virtuális gépek egyik gazdagépről a másikra. Ezek a mechanizmusok ne konfigurálja újra, és értesíti a kód futtatása a virtuális gép. Mint ilyen, azok **nem támogatott** , környezetekben futó Service Fabric-fürtök. A Service Fabric a alkalmazni csak magas rendelkezésre állású technológia kell lennie. Például a virtuális gép élő áttelepítést, mechanizmusok San-okkal, vagy mások pedig nem szükséges. Ha a Service Fabric, ezek a mechanizmusok együtt használható _csökkentheti_ rendelkezésre állása és megbízhatósága, vezessen be a további összetettséget, mert hiba központi adatforrások hozzáadása és megbízhatóság igénybevételéhez és rendelkezésre állási stratégiát, amely ütközik a Service fabricben. 
>
>

Az alábbi ábra az entitásokat, amelyek hozzájárulnak a tartalék tartományok és azon tartományok megjelenítése, különböző tartalék eredményező azt szín. Ebben a példában van adatközpontok ("DC"), állványokon ("R") és a többi panelen (a "B"). Is ha minden panelen egynél több virtuális gép rendelkezik, lehetnek réteget a tartalék tartomány hierarchiában.

<center>

![Tartalék tartományok keresztül rendezve csomópontok][Image1]
</center>

Futásidőben a Service Fabric fürterőforrás-kezelő figyelembe veszi a tartalék tartományok a fürtben, és a csomagok elrendezések. Az állapot-nyilvántartó replikák vagy állapotmentes példányok egy adott szolgáltatáshoz, így azok külön tartalék tartományokban vannak osztva. A szolgáltatás terjesztése tartalék tartomány között biztosítja, hogy a szolgáltatás rendelkezésre állásának nem sérül, amikor egy tartalék tartomány meghibásodna, a hierarchia minden szintjén.

A Service Fabric a fürterőforrás-kezelő hány rétegek léteznek, a tartalék tartomány hierarchia nem gondoskodik. Azonban megkísérli annak biztosítása érdekében, hogy a hierarchia bármely egy részének elvesztését nem érinti, a futó szolgáltatásokat. 

Ez nem ajánlott, ha minden szinten, a tartalék tartomány hierarchia mélységét csomópontok azonos számú. Ha a tartalék tartományok "fa" egyenetlen a fürtben, megnehezíti a a fürterőforrás-kezelő, döntse el, a legjobb lefoglalt szolgáltatások. Tartalék tartományok imbalanced elrendezések azt jelenti, az egyes tartományok hatása, mint a más tartományokban további szolgáltatások rendelkezésre állásának elvesztését. Ennek eredményeképpen a fürterőforrás-kezelő levágása között két célt: Szeretné, hogy a gépek használja a "nagy" tartomány például azok a szolgáltatások, és szeretné, hogy helyezze el a szolgáltatások más tartományok, hogy a tartomány elvesztését nem problémákhoz. 

Hogyan tegye imbalanced tartományok meg? Az alábbi ábrán két különböző fürt elrendezések mutatjuk be. Az első példában a csomópontok egyenlően vannak elosztva a tartalék tartományok. A második példa egy tartalék tartomány számos további csomópontokat, mint a többi tartalék tartományban van. 

<center>

![Két különböző fürt elrendezések][Image2]
</center>

Az Azure-ban a választás, amelyek tartalék tartomány tartalmaz egy csomópont van kezelve. Azonban kiosztott csomópontok számától függően, is továbbra is megtörténhet a tartalék tartományok, mint a többi őket a több csomópontot. Tegyük fel például, öt tartalék tartományok a fürt rendelkezik, de a megadott NodeType hét csomópontok kiépítésére. Ebben az esetben az első két tartalék tartomány a kialakított több csomópontot. Ha folytatja, csak néhány példányaival további NodeType üzembe helyezéséhez, a probléma rosszabb beolvasása. Ezért azt javasoljuk, hogy a csomópontok az egyes csomópontok számát a típus egy több, a tartalék tartományok számát.

## <a name="upgrade-domains"></a>Frissítési tartományok
Frissítési tartományok egy másik szolgáltatás, amely segít a Service Fabric fürterőforrás-kezelő a fürt elrendezésének ismertetése. Frissítési tartományok, amelyek egy időben vannak frissítve csomópontok készleteinek határozza meg. Frissítési tartományok segítenek a fürterőforrás-kezelő ismertetése és felügyeleti műveleteket, például frissítések vezényli.

Frissítési tartományok sokkal vannak, például a tartalék tartományok, de néhány fontos különbség. Első lépésként koordinált hardver-meghibásodásokkal területeit tartalék tartományok definiálása. Frissítési tartományok, másrészt határozzák meg a házirend. Eldöntheti, hogy hány, helyett ez alatt a környezet során kap. Annyi frissítési tartományok csomópontok módon lehet. Egy másik tartalék tartományokban és frissítési tartományok közötti különbség az, hogy a frissítési tartományok ne legyenek hierarchikus. Ehelyett azok több mint egy egyszerű címkét. 

Az alábbi ábrán látható, három frissítési tartományt szétteríti a három tartalék tartományt. Azt is bemutatja egy lehetséges a három különböző replikába állapotalapú szolgáltatás elhelyezésének, ahol minden egyes említi különböző hibatűrési és frissítési tartományok. Az elhelyezés lehetővé teszi, hogy egy szolgáltatás frissítése közepén tartalék tartomány elvesztését, és a egy példányát a kód és az adatok továbbra is fennáll.  

<center>

![Tartalék és frissítési tartományokba, az elhelyezési][Image3]
</center>

Vannak előnyei és hátrányai, hogy nagy számú frissítési tartomány. Több frissítési tartomány azt jelenti, a frissítés lépéseinek részletesebb, és ezért a csomópontok vagy a szolgáltatások kisebb számú érinti. Ennek eredményeképpen kevesebb szolgáltatások át kell helyeznie egyszerre, a rendszer kevesebb lemorzsolódási bemutatása. Ez általában a megbízhatóság javításához, mivel kisebb, a szolgáltatás hatással van minden olyan problémát jelent meg a frissítés során. Több frissítési tartományt is jelenti, hogy kell-e a többi csomóponton a frissítés hatásainak kezeléséhez kevesebb a rendelkezésre álló pufferbe. Például ha öt frissítési tartományok, a csomópontok az egyes kezelik nagyjából 20 %-a forgalmat. Megnőtt a frissítés frissítési tartományban van szüksége, ha a terhelés általában kell valahol meg. Négy fennmaradó frissítési tartomány van, mivel minden hely számára a teljes forgalom körülbelül 5 %-át kell rendelkeznie. Több frissítési tartomány azt jelenti, hogy a fürt csomópontjain kisebb puffer van szüksége. Vegyük példaként 10 frissítési tartományok inkább rendelkezett. Ebben az esetben minden UD volna csak kell kezelése körülbelül 10 %-a teljes forgalom. Ha egy frissítési útmutató részletesen bemutatja a fürt, minden egyes tartományhoz csak kell készül a teljes forgalom 1.1 % elegendő hellyel rendelkezik. További frissítési tartományok általában lehetővé teszi a csomópontok futtassa magasabb kihasználtságát, mivel kevesebb fenntartott kapacitásra van szüksége. Ugyanez igaz a tartalék tartományok.  

A hátránya, hogy hány frissítési tartományok, hogy a frissítések általában hosszabb időt vesz igénybe. Egy rövid idő alatt után egy frissítési tartományban befejeződött, és a következő frissítése előtt ellenőrzi a Service Fabric várakozik. Ezek az késleltetések bevezetett a frissítés előtt a frissítés előrehalad észlelését hibák engedélyezése. Az egyensúlyt a fogadható el, mert megakadályozza, hogy rossz módosítások befolyásolják a szolgáltatás túl sok egyszerre.

Túl kevés frissítési tartományok számos negatív hatásai rendelkezik – Bár minden egyes frissítési tartomány nem működik, és a frissítés alatt áll egy nagy része a teljes kapacitás nem érhető el. Például ha csak három frissítési tartományt készítésének körülbelül 1/3 fürtkapacitás vagy általános szolgáltatás le egyszerre. Kellene nagy részét a szolgáltatás le egyszerre nem kívánatos, mivel elegendő kapacitással rendelkeznek a fürt a terhelés kezeléséhez további részében található. A puffer azt jelenti, hogy normál működés során ezen csomópontok kisebb, mint egyébként betöltött karbantartása. Ez növeli a szolgáltatás futtatásával járó költségeket.

Nincs valós egy környezetben, vagy átfedési módjának korlátozásaihoz tartalék és frissítési tartományok száma korlátozva van. Mindemellett nincsenek számos gyakori minták:

- Tartalék tartományok és a frissítési tartományok leképezése 1:1
- Több frissítési tartományt csomópontonként (fizikai vagy virtuális operációsrendszer-példányként)
- Ha a tartalék tartományokban és frissítési tartományok alkotnak mátrix le a átlói általában futtató géppel "csíkozott" vagy "mátrixban" modell

<center>

![Tartalék és frissítési tartomány elrendezések][Image4]
</center>

Nincs a nem a legjobb választ válassza a kívánt elrendezést, mindegyik rendelkezik, és néhány hátrányai. Ha például a 1FD:1UD modellje könnyen beállítható. Az 1 csomópont modell frissítési tartományonként a legtöbb például milyen személyek szolgálnak. Frissítések során minden egyes csomópont frissítése egymástól függetlenül. Ez a rövid hogyan gépek csoportok lettek frissítve manuálisan az elmúlt hasonló. 

A leggyakrabban használt modell az FD/UD mátrix, ahol a tartalék és frissítési tartománnyal űrlap-tábla és a csomópontok kerülnek a átlós mentén indítása. Ez az alapértelmezés szerint a Service Fabric-fürtök az Azure-ban használt modell. Sok csomóponttal rendelkező fürtök esetében minden említi hasonló sűrű mátrix keresi.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Tartalék és frissítési tartomány korlátozások és az eredményül kapott viselkedés
### <a name="default-approach"></a>*Alapértelmezett módszer*
Alapértelmezés szerint a fürterőforrás-kezelő megőrzi a szolgáltatások tartalék és frissítési tartományok között. Ez van modellezve a [megkötés](service-fabric-cluster-resource-manager-management-integration.md). A tartalék és frissítési tartomány korlátozás állapotok: "Egy adott szolgáltatás partíció soha nem kell különbséget nagyobb, mint egy szolgáltatási objektumok (állapotmentes szolgáltatás példányainak vagy állapotalapú szolgáltatás replikák) ugyanazon a szinten hierarchia két tartomány közötti számú". Tegyük fel, ez a megkötés "legnagyobb különbség a" garantálja. A tartalék és frissítési tartomány korlátozás megakadályozza, hogy bizonyos áthelyezését vagy a szabályok a fenti szabályt megsértő. 

Lássunk erre egy példát. Tegyük fel, hogy van-e a hat csomópont révén az öt tartalék tartományokkal és öt frissítési tartományok konfigurált egy fürtöt.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

*Konfiguráció 1.*

Most tegyük fel, öt hozunk létre egy szolgáltatás együtt egy TargetReplicaSetSize (vagy egy InstanceCount állapotmentes szolgáltatás). A replikák N1-N5 megjelenni. N6 valójában soha nem használatos függetlenül attól, hogy hány szolgáltatások, például ez hoz létre. De miért? Tekintsük át a jelenlegi elrendezéshez, és hogy mi történne, ha ki van választva N6 közötti különbség.

A következő fájt az elrendezést és a tartalék és frissítési tartomány replikák száma:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Elrendezés 1*


Ez az elrendezés csomópontok maximális száma a tartalék és frissítési tartomány szempontjából kiegyensúlyozott. Azt is kiegyensúlyozott tekintetében a tartalék és frissítési tartomány replikák száma. Minden tartományban a csomópontok azonos számú és replikák azonos számú rendelkezik.

Most nézzük, mi történne N6 helyett N2 kellett használtuk. Hogyan szeretné a replikák osztják majd?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

*Elrendezés 2*


Ez az elrendezés megsérti a definíció "legnagyobb különbség a" garancia a tartalék tartomány megkötés. FD0 két replika van, míg a FD1 nullát mutat, így a FD0 és FD1 közötti különbséget két, összesen nagyobb, mint a legnagyobb különbség az egyik. A korlátozás sérül, mivel a fürterőforrás-kezelő nem engedélyezi az ezzel az elrendezéssel fokozott. Hasonlóképpen, ha azt kivételezett N2 és N6 (helyett N1 és N2) kapunk:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Elrendezés 3*


Ez az elrendezés tartalék tartományok szempontjából kiegyensúlyozott. Azonban most azt van megsértése a frissítési tartomány korlátozás mert UD0 nulla replikák közben UD1 két tartalmaz. Ezért ebben az elrendezésben is érvénytelen, és nem tárolható a fürt Resource Manager által.

Ez a módszer az állapot-nyilvántartó replikák vagy állapotmentes példányok elosztása a legjobb lehetséges hibatűrést biztosít. Olyan helyzetekben, amikor egy tartományt leáll, a replikák és példányok minimális száma elvész. 

Másrészről ezt a módszert kell túl szigorú és a fürt összes erőforrásainak használatát teszi lehetővé. Bizonyos fürtkonfigurációkat az egyes csomópontok nem használható. Ez a Service Fabric nem helyezi el a szolgáltatásokat a figyelmeztető üzenetek eredményez vezethet. Az előző példában a fürt csomópontjainak egy része nem lehet (a megadott példa N6) használt. Akkor is, ha a csomópontok a létrehozott fürtre (N7 – N10) jelentene, replikák és példányok csak kerül az N1 – N5 tartalék és frissítési tartomány megkötések miatt. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

*Konfiguráció 2.*


### <a name="alternative-approach"></a>*Alternatív megoldás*

A fürterőforrás-kezelő támogatja a tartalék és frissítési tartomány korlátozás, amely lehetővé teszi, hogy az Elhelyezés során is teheti a minimális szintű biztonság egy másik verziója. Az alternatív tartalék és frissítési tartomány korlátozás is meg kell adni a következő: "Egy adott szolgáltatás partícióhoz tartozó replika elosztása a tartományok biztosítania kell, hogy a partíció nem érinti a kvórum elvesztése". Tegyük fel, ez a megkötés "csökkentett kvórum" garantálja. 

> [!NOTE]
>Egy állapotalapú szolgáltatás meghatározzuk *kvórum elvesztése* olyan helyzetekben, amikor a partíciók replikáit többsége nem működik egy időben. Például ha TargetReplicaSetSize öt, bármely három replika készletét kvórum jelöli. Hasonlóképpen ha TargetReplicaSetSize 6, négy replika kvóruma számára szükséges. Mindkét esetben legfeljebb két replika is nem működik egyszerre, ha a partíció szeretné a szokásos módon működhet tovább. Az állapotmentes szolgáltatás, nem nincs *kvórum elvesztése* állapotmentes szolgáltatások továbbra is működnek megfelelően, akkor is, ha egyszerre leáll példányok többsége szerint. Ezért fogunk dolgozni a szöveg többi állapotalapú szolgáltatások.
>

Lépjen vissza az előző példában. A korlátozás "csökkentett kvórum" verziójával összes három adott elrendezések lesz érvényes. Ennek oka az, akkor is, ha a hiba FD0, a második elrendezést vagy UD1 a harmadik elrendezésben lenne, a partíció továbbra is fennáll a kvórum (a hozzá tartozó replikák többsége továbbra is lenne mentése). Ez a korlátozás verziójával N6 sikerült szinte mindig használhatók fel.

A "biztonságos kvórum" megközelítés rugalmasabb, mint a "legnagyobb különbség a" módszer egyszerűbb, amelyek érvényesek a szinte bármilyen fürtjének topológiája replika disztribúciókat találhat, biztosít. Azonban ez a megközelítés nem garantálja a legjobb tartalék tolerancia jellemzők mivel bizonyos hibák rosszabb, mint mások. A legrosszabb esetben a nagyobb részét, a replikák elveszhetnek egy tartomány és a egy további replika hibával. Például ahelyett, hogy 5 replikák és példányok kvórum elvesztése 3 hibák, most elveszhet csak két hibák többségét. 

### <a name="adaptive-approach"></a>*Az adaptív módszer*
Mivel mind a módszerek előnyeiről és hátrányairól, jelentettük adaptív megközelítés, amely egyesíti e két stratégia szerint.

> [!NOTE]
> Ez lesz a Service Fabric verziója 6.2 kezdve alapértelmezett viselkedését. 
> 
> Az adaptív módszer alapértelmezés szerint a "legnagyobb különbség a" logikai használ, és, csak szükség esetén a "biztonságos kvórum" logikai kapcsolók. A fürterőforrás-kezelő automatikusan kitalálja, hogy mely stratégia szükség, megnézzük, hogyan vannak konfigurálva a fürt és a szolgáltatásokat. Egy adott szolgáltatáshoz: *Ha a TargetReplicaSetSize egyenlően osztható fel a frissítési tartományok száma és a tartalék tartományok száma **és** a csomópontok számát a kisebb vagy egyenlő a (a tartalék tartományok száma) * (a frissítési tartományok száma), a fürt Erőforrás-kezelő, hogy a szolgáltatás a "kvórum alapján" logikát kell használni.* Hogy a fürterőforrás-kezelő ezt a módszert használja a is állapot nélküli és állapotalapú szolgáltatások esetében annak ellenére, hogy a kvórum elvesztése nem releváns, az állapotmentes szolgáltatások esetében figyelembe kell vennie.

Lépjen vissza az előző példával, és feltételezik, hogy egy fürt most már rendelkezik-e (a fürt továbbra is lehet konfigurálni az öt tartalék tartományok és öt frissítési tartományok és az adott fürt marad az öt üzemeltetett szolgáltatás TargetReplicaSetSize) 8 csomópont. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

*3. konfiguráció*

Az összes szükséges feltételek teljesülnek, mert a fürterőforrás-kezelő a "kvórum alapján" logikai elosztása a szolgáltatás a fog használni. Ez lehetővé teszi a N6 – N8 használatát. Egy lehetséges szolgáltatás terjesztési ebben az esetben nézhet:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

*Elrendezés 4*

Ha a szolgáltatás TargetReplicaSetSize csökkenti, négy (például) fürterőforrás-kezelő figyelje meg, hogy ezt a módosítást, és a "legnagyobb különbség a" logikai kapcsolattal, mert többé tartalék és frissítési tartománnyal számának oszthatónak TargetReplicaSetSize nem folytatódik. Ennek eredményeképpen egyes replika áthelyezések száma – elosztásához a fennmaradó négy replikák N1-N5 csomóponton, úgy, hogy a tartalék tartomány és a frissítési logika "legnagyobb különbség a" verziója nem sérül akkor jön létre. 

A negyedik elrendezés és az öt TargetReplicaSetSize szeretnének vissza. N1 a rendszer eltávolítja a fürtről, a frissítési tartományok száma négy válik. A fürterőforrás-kezelő ismét elindul, frissítési tartománnyal száma nem egyenletes felosztása a szolgáltatás TargetReplicaSetSize többé "legnagyobb különbség a" logic használatával. Ennek eredményeképpen a replika R1, beépített újra, ha rendelkezik N4 megjelenni, hogy a tartalék és frissítési tartomány megkötés nem sérül.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |– |N/A |N/A |N/A |N/A |– |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Elrendezés 5*

## <a name="configuring-fault-and-upgrade-domains"></a>Tartalék és frissítési tartományok konfigurálása
Tartalék tartományokban és frissítési tartományok történik meg automatikusan az Azure-ban üzemeltetett Service Fabric-telepítések. A Service Fabric szerzi be, és a környezeti adatokkal, az Azure-ból.

Ha saját fürtöt hoz létre (vagy szeretne futtatni egy adott topológia fejlesztői), megadhatja a tartalék és frissítési tartomány információkat saját maga. Ebben a példában egy kilenc csomópont helyi fejlesztési fürtöt három "adatközpontok" (mindegyik három állványt) is meghatározzuk. Ez a fürt ezen három adatközpontok szétteríti három frissítési tartományt is tartalmaz. Egy példa a konfiguráció alatt van: 

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
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

az önálló verziója telepítéseinek ClusterConfig.json keresztül

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
> Fürtök az Azure Resource Manageren keresztül meghatározásakor tartalék tartomány és frissítési tartományokban vannak rendelve az Azure-ban. A csomóponttípusok és virtuálisgép-méretezési csoportok az Azure Resource Manager-sablonban definíciója, ezért nem tartalmazza a tartalék tartomány vagy a frissítési tartomány adatait.
>

## <a name="node-properties-and-placement-constraints"></a>Csomópont tulajdonságai és elhelyezési korlátozások
Néha (valójában a legtöbbször) fog győződjön meg arról, hogy bizonyos munkaterhelések csak bizonyos típusú, a fürtben található csomópontok futtassa. Például néhány számítási feladat lehet szükség gpu-kat vagy SSD-k, míg mások nem. Egy remek példa célzó hardvert, hogy a számítási feladatok, szinte minden n szintű architektúra is elérhető. Egyes gépek állnak az előtér- vagy API-t szolgálja ki az alkalmazás oldalán, és ki vannak téve az ügyfelek és az internet. Különböző gépek, gyakran különböző a hardveres erőforrások hajtja végre a számítási és tárolási rétegeket. Ezek általában olyan _nem_ el közvetlenül ügyfeleknek, illetve az interneten. A Service Fabric vár, hogy nincsenek-e esetekben, ahol adott számítási feladatok adott hardverkonfigurációk futtatnia kell. Példa:

* meglévő n szintű alkalmazáshoz volt "vissza, és hogy áttért" a Service Fabric környezetbe
* a munkaterhelés szeretné a teljesítményt, méretezési és biztonsági elkülönítés okokból adott hardveren futtatott
* Számítási feladatok más számítási feladatok a házirend vagy az erőforrás felhasználási okokból elkülönítve kell lennie.

Ezen konfigurációk számos támogatása érdekében a Service Fabric rendelkezik csomópontokra alkalmazható címkék első osztályú fogalma. Ezekkel a címkékkel nevezzük **csomópont tulajdonságai**. **Elhelyezési korlátozások** az egyes szolgáltatásai, válassza ki egy vagy több csomópont-tulajdonságok csatolt utasításokat. Elhelyezési korlátozások határozza meg, ahol szolgáltatásainak futnia kell. Az olyan korlátozások, bővíthető – minden kulcs-érték pár is dolgozhat. 

<center>

![A fürt elrendezés különböző számítási feladatok][Image5]
</center>

### <a name="built-in-node-properties"></a>A beépített csomópont tulajdonságai
A Service Fabric néhány anélkül, hogy a felhasználónak meg kellene adhat meg hozzájuk automatikusan használható alapértelmezett csomópont tulajdonságait határozza meg. Az alapértelmezett tulajdonság meg van határozva a csomópontok a **NodeType** és a **csomópontnév**. Így például, egy elhelyezési korlátozás is írható `"(NodeType == NodeType03)"`. Általában található NodeType csomóponttípust kell a legfontosabb gyakran használt tulajdonságokat. Ez akkor hasznos, mivel az egy gép egy típusú megfelel 1:1. Egyes típusú gépek olyan típusú számítási feladatok egy hagyományos n szintű alkalmazás felel meg.

<center>

![Elhelyezési korlátozások és a csomópont tulajdonságait][Image6]
</center>

## <a name="placement-constraint-and-node-property-syntax"></a>Elhelyezési korlátozás és a csomópont tulajdonság szintaxis 
A csomópont tulajdonságban megadott érték lehet egy string, bool, vagy aláírt hosszú. Az utasítás a szolgáltatás-elhelyezési nevezzük *megkötés* óta korlátozza, ahol a szolgáltatás futtatható a fürtben. A korlátozás logikai utasításnak sem működik, a fürt másik csomópont-tulajdonságok is lehet. A következő logikai utasításokat a érvényes választók a következők:

1) adott kimutatások létrehozása feltételes ellenőrzése

| Utasítás | Szintaxis |
| --- |:---:|
| "egyenlő" | "==" |
| "nem egyenlő" | "!=" |
| "nagyobb, mint" | ">" |
| "nagyobb, mint vagy egyenlő" | ">=" |
| "kisebb, mint" | "<" |
| "kisebb vagy egyenlő" | "<=" |

2) Csoportosítás és logikai műveletekhez logikai utasítások

| Utasítás | Szintaxis |
| --- |:---:|
| "és" | "&&" |
| "vagy" | "&#124;&#124;" |
| "nem" | "!" |
| "egyetlen utasítás csoportot" | "()" |

Az alábbiakban néhány példa az alapvető utasításokat.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Ha "True" való kiértékelése által az általános elhelyezési korlátozás utasítás csak a csomópontok lehet rá a szolgáltatás. Csomópontok, amelyek nem rendelkeznek a megadott tulajdonság nem egyezik meg a bármely a tulajdonságot tartalmazó elhelyezési korlátozás.

Tegyük fel, hogy a megadott csomóponttípus definiálva a következő csomópont-tulajdonságok:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

az önálló verziója telepítéseinek ClusterConfig.json vagy Template.json az Azure-ban üzemeltetett fürtök. 

> [!NOTE]
> Az Azure Resource Manager-sablonban a csomópont típusa általában paraméterezni. Akkor jelenne meg "[parameters('vmNodeType1Name')]" helyett "NodeType01".
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

Szolgáltatás-elhelyezési hozhat létre *megkötések* egy szolgáltatáshoz, például az alábbiak szerint:

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Ha NodeType01 összes csomópontja érvényes, választhatja a korlátozás az adott csomóponttípus "(NodeType == NodeType01)".

A ritkán használt adatok egy szolgáltatás-elhelyezési megkötések kapcsolatos dolog, hogy azok frissíthetők dinamikusan futásidőben. Tehát ha kell, egy szolgáltatás Navigálás a fürtben, hozzáadhat és eltávolíthat követelményeknek, és így tovább. A Service Fabric gondoskodik arról, hogy a szolgáltatás marad felfelé és a rendelkezésre álló akkor is, ha az ilyen típusú módosításokat végzett.

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

PowerShell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Minden más elnevezett szolgáltatáspéldányokban elhelyezési korlátozások vannak megadva. Frissítések mindig is a hely (felülírása) milyen előzőleg meg volt adva.

A fürt definíciója tulajdonságok meghatározása egy csomóponton. Egy csomópont tulajdonságainak módosítása a fürtfrissítések konfigurációs van szükség. Minden érintett csomópont újraindítása új tulajdonságainak jelentéséhez a csomópont-tulajdonságok frissítése szükséges. Ezek a működés közbeni frissítés a Service Fabric által felügyelt.

## <a name="describing-and-managing-cluster-resources"></a>Kivizsgáljuk a problémát, és a fürt erőforrásainak kezelése
A legfontosabb feladatok bármely orchestrator egyik erőforrás-használat, a fürt kezeléséhez. Fürterőforrások kezelése jelenti pár másik dolgot. Először is van annak ellenőrzése, hogy a gépek nem állnak túlterhelt. Ez azt jelenti, hogy a gépek nem futnak, mint azok képes kezelni a további szolgáltatások biztosításához. A második nincs terheléselosztási és optimalizálás, ami létfontosságú szolgáltatások hatékony működését. A Cost effective vagy a teljesítmény-és nagybetűket szolgáltatásajánlatok néhány gyakori elérésű kell, míg mások ritkán használt csomópontok nem engedélyezheti. Gyakori elérésű csomópontok érdeklődő erőforrás-versengéshez és gyenge teljesítményt és a ritkán használt csomópontok jelölik az elpazarolt erőforrások mennyisége és a nagyobb költségek. 

A Service Fabric az erőforrásokhoz, mint egy `Metrics`. Mérőszám játszik bármely ismertetik a Service Fabric kívánt logikai és fizikai erőforrás. Metrikák Példák többek között a "WorkQueueDepth" vagy "MemoryInMb". A fizikai erőforrásokat, amelyek képesek felügyelni a Service Fabric csomópontokon információ: [erőforrás-szabályozás](service-fabric-resource-governance.md). Egyéni metrikák és a használatukat konfigurálásával kapcsolatos további információkért lásd: [Ez a cikk](service-fabric-cluster-resource-manager-metrics.md)

Metrikák Elhelyezés a korlátozások és csomópont-tulajdonságok eltérnek. Csomópont tulajdonságai statikus leírójára magukat a csomópontokat. Metrikák csomópontokat rendelkező erőforrásokat és, hogy a szolgáltatások felhasználásához egy csomóponton futtatásakor ismertetik. A csomópont-tulajdonságok "HasSSD" lehet, és IGAZ vagy hamis értéket kell beállítani. Szabad terület mennyisége a SSD és mennyi által felhasznált szolgáltatások "DriveSpaceInMb" mint metrika lenne. 

Fontos megjegyezni, hogy hasonlóan elhelyezési korlátozások és a csomópontok tulajdonságai mellett a Service Fabric-fürt Resource Manager nem ismeri a metrikák nevei jelenti. Metrikák nevei csak karakterláncok. Tanácsos egységek deklarálja a metrikák nevei, előfordulhat, hogy nem egyértelmű létrehozott részeként.

## <a name="capacity"></a>Kapacitás
Ha kikapcsolt minden erőforrás *terheléselosztási*, Service Fabric a fürterőforrás-kezelő továbbra is biztosítja, hogy nem csomópont időpontban fejeződött a kapacitás fölé. Kapacitás meghaladása kezelése, lehetséges, hacsak nem a fürthöz nincs elég hely, vagy a számítási feladatok nagyobb, mint bármely csomópont. Kapacitás egy másik *megkötés* , hogy a fürterőforrás-kezelő segítségével megismerheti, milyen egy erőforrást egy csomópont van. Fennmaradó kapacitás is nyomon követi a fürt teljes. A kapacitás és a szolgáltatási szintű a használat metrikák vannak kifejezve. Így például lehet, hogy a metrika "ClientConnections", és előfordulhat, hogy egy adott csomópont egy "ClientConnections" 32768 kapacitást. Más csomópontok egyéb korlátok néhány szolgáltatás fut az adott csomópont is tegyük fel, jelenleg fogyassza 32256 mérőszám "ClientConnections" lehet.

A fürterőforrás-kezelő futásidőben, nyomon követi a fennmaradó kapacitás, a fürt és a csomópontokon. Nyomon követheti a kapacitás a fürterőforrás-kezelő kivonja a minden szolgáltatás használata a csomópont kapacitását, ahol a szolgáltatás fut-e. Ezekkel az információkkal a Service Fabric fürterőforrás-kezelő ismerhetik fel a hol helyezze el vagy helyezhetik át a replikák úgy, hogy a csomópont feletti kapacitás nem lépjen.

<center>

![Fürtcsomópontok és a kapacitás][Image7]
</center>

C#:

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

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

A fürtjegyzékben definiált kapacitások tekintheti meg:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

az önálló verziója telepítéseinek ClusterConfig.json vagy Template.json az Azure-ban üzemeltetett fürtök. 

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

Általában az adott szolgáltatás betöltése módosítások dinamikusan. Tegyük fel, hogy "ClientConnections" terheléséről egy replika beállítás változása: 1024, 2048, de a csomópont futott a majd csak ennek a mutatónak a fennmaradó 512 kapacitás rendelkezett. Most már a, hogy a replika és példány elhelyezése érvénytelen, mert nem áll elég hely a csomóponton. A fürterőforrás-kezelő jelentkezik, és vissza a kapacitás alatt a csomópont rendelkezik. Csökkenti a terhelést a csomóponton, amely szerint egy vagy több replika vagy példányok adott csomópontról kerüljön át más csomópontokra kapacitás felett van. Replikák áthelyezésekor a fürterőforrás-kezelő megpróbálja ezen áthelyezések száma – költségek. A mozgás költsége a következő cikkben [Ez a cikk](service-fabric-cluster-resource-manager-movement-cost.md) és további információ a fürterőforrás-kezelő felhasználói újraegyensúlyozása stratégiák és szabályok leírt [Itt](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Fürt kapacitása
Hogyan segít tehát a Service Fabric fürterőforrás-kezelő túl megtelt a a teljes fürtöt megtarthatja? A dinamikus terheléselosztási nem áll sokkal azt is megteheti. Szolgáltatások a terhelés kiugrás függetlenül a fürt Resource Manager által végrehajtott műveleteket is rendelkezhet. A fürtön a rengeteg hely maradt még ma, ezért előfordulhat, hogy lehet underpowered, ha Ön híres holnap. Mindemellett vannak bizonyos vezérlők, amely a problémák megelőzése érdekében számlázásnak vannak. Az első lépésben is, megakadályozza okozna a fürt teljes válik új számítási feladatok létrehozását.

Tegyük fel, hogy az állapotmentes szolgáltatás létrehozása, és néhány terhelés társítva van. Tegyük fel, hogy a szolgáltatás ügyel a "DiskSpaceInMb" metrikát. Is tegyük fel, hogy fogja "DiskSpaceInMb" a szolgáltatás minden példányát öt egységek felhasználását. Szeretne létrehozni a szolgáltatás három példányban. Remek! Tehát, hogy azt jelenti, hogy kell lennie ahhoz, hogy akkor is igaz, hogy a fürt "DiskSpaceInMb" 15 egységet létre lehessen szolgáltatáspéldányok. A fürterőforrás-kezelő folyamatosan számítja ki a kapacitás és mindegyik metrikát fogyasztását, ennek alapján határozza meg a fennmaradó kapacitás a fürtben. Ha nincs elég hely, a fürterőforrás-kezelő elutasítja a létrehozás hívások.

A követelmény, amely csak 15 egységek érhető el, mivel ez a terület rendelhető számos különböző módon. Ha például lehetnek egy fennmaradó kapacitási egység, 15 különböző csomópontokon, vagy öt különböző csomópontokon három további kapacitásegységek. A fürterőforrás-kezelő is átrendezheti dolog, ezért van öt egységek használható három csomóponttal, ha a szolgáltatás helyezi. A fürt átrendezése általában lehetőség, kivéve ha a fürt majdnem megtelt vagy valamilyen okból kifolyólag nem lehet összevont a meglévő szolgáltatások.

## <a name="buffered-capacity"></a>Pufferelt kapacitás
Pufferelt kapacitása a fürt Resource Manager egy másik szolgáltatás. Lehetővé teszi bizonyos része a teljes csomópont-kapacitás foglalása. Ez a kapacitás puffer szolgáltatások elhelyezése során csomóponthibáknak frissítések és verziófrissítések csak használatos. Pufferelt kapacitás globálisan az összes csomópont metrikánként van megadva. A lefoglalt kapacitás a választott érték egy függvényt a tartalék és frissítési tartományokba, a fürt számát. Több tartalék és frissítési tartományok azt jelenti, hogy kevesebb választhat a pufferelt kapacitás. Ha több tartományom van, a fürt nem érhető el, frissítések és a rendszerhibák kisebb mennyiségű várható. Adja meg a pufferelt kapacitás csak értelme, ha egy metrika a csomópont kapacitását is megadott.

Íme egy példa, hogyan adható meg a pufferelt kapacitás:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

az önálló verziója telepítéseinek ClusterConfig.json vagy Template.json az Azure-ban futó fürtök:

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

Új szolgáltatás létrehozása sikertelen lesz, amikor a fürt egy metrika pufferelt kapacitása. Az új szolgáltatások megőrzése érdekében a puffer megelőzése biztosítja a frissítéseket és a hibák haladnak át a kapacitás-csomópontokat nem okoznak. Pufferelt kapacitás nem kötelező, de ajánlott minden olyan fürtben, amely meghatározza egy metrika a kapacitása.

A fürterőforrás-kezelő a betöltési információk tesz elérhetővé. Mindegyik metrikát ezeket az információkat tartalmazza: 
  - a pufferelt kapacitásbeállítások
  - a teljes kapacitás
  - a jelenlegi felhasználás
  - e mindegyik metrikát számít elosztott terhelésű, vagy sem
  - a szórás statisztikája
  - a csomópontok, amely rendelkezik a legtöbb és legalább betöltése  
  
Az alábbiakban látható egy példa a kimenetre:

```posh
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
* Az architektúra és információk folyamat belül a fürterőforrás-kezelő kapcsolatos információkért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-architecture.md)
* Lemeztöredezettség-mentesítés metrikák meghatározása módja egy konszolidálhatja helyett ezzel azt csomópontok terhelése. Lemeztöredezettség-mentesítés konfigurálása, lásd: [Ez a cikk](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Elölről kezdődik, és [, a Service Fabric fürterőforrás-kezelő bemutatása](service-fabric-cluster-resource-manager-introduction.md)
* Ismerje meg hogyan a fürterőforrás-kezelő felügyeli, és elosztja a terhelést a fürtben, tekintse meg a cikk a [terheléselosztás](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
