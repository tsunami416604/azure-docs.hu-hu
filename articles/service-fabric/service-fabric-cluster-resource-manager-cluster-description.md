---
title: "Fürt erőforrás-kezelő fürt leírása |} Microsoft Docs"
description: "A Service Fabric-fürt leíró tartalék tartományok, a frissítési tartományok, a csomópont tulajdonságait és a csomópont-kapacitás az a fürt erőforrás-kezelő megadásával."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 26ce9e96dd4df170e80c2c61dcc08c70357eec22
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="describing-a-service-fabric-cluster"></a>Ismertető a service fabric-fürt
A Service Fabric fürt erőforrás-kezelő biztosít több fürt leíró mechanizmusok. Futásidőben a fürt erőforrás-kezelő ezt az információt használja a fürtben futó szolgáltatások magas rendelkezésre állásának biztosításához. Miközben a fontos szabályok, is megkísérli a fürtön belül a hálózatierőforrás-fogyasztás optimalizálása.

## <a name="key-concepts"></a>Fő fogalmak
A fürt erőforrás-kezelő fürt leíró több funkciót támogatja:

* Tartalék tartományok
* Frissítési tartományok
* Csomópont tulajdonságai
* Csomópont-kapacitás

## <a name="fault-domains"></a>Tartalék tartományok
Tartalék tartomány bármely terület koordinált hiba. Egyetlen számítógépen egy tartalék tartományt, (mivel meghiúsulhatnak, hogy a meghajtó hibák hibás hálózati belső vezérlőprogramjának áramkimaradások adjon meg a saját a különböző okok miatt). Ugyanazon az Ethernet-kapcsoló csatlakozik gép vagy gépek megosztása áramkimaradás vagy egyetlen helyen egyetlen forrás sem az azonos tartalék tartományban van. Mivel a hardveres hibák átfedésbe hozni természetes, tartalék tartományok eredendően hierarchikus és jelentésekként jelennek meg a Service Fabric URI.

Fontos, hogy tartalék tartományok megfelelően vannak beállítva a Service Fabric ezt az információt használja a szolgáltatások biztonságosan helyezendő óta. A Service Fabric nem szeretné elhelyezni a szolgáltatások úgy, hogy a tartalék tartomány (néhány összetevő hibája által okozott) elvesztését okozza a szolgáltatás leáll. Az Azure-ban a Service Fabric környezetben a tartalék tartomány adatai, a környezet alapján használja a megfelelő konfigurálásához a nevünkben a fürt a csomópontok. Service Fabric önálló, a tartalék tartományok határozza meg, amikor a fürt beállítása 

> [!WARNING]
> Fontos, hogy a tartalék tartomány információkat biztosít a Service Fabric pontos-e. Tételezzük fel például, hogy a Service Fabric-fürt csomópontja öt fizikai állomáson futnak, 10 virtuális gépeken belül futnak. Ebben az esetben, annak ellenére, hogy nincsenek 10 virtuális gépet, nincsenek csak 5 különböző (a legfelső szintű) tartományok fault. Ugyanazon a fizikai gazdagépen megosztása hatására a virtuális gépek megosztani a legfelső szintű tartalék tartománynak, mivel a virtuális gépek koordinált hiba fordulhat elő, ha a fizikai gazdagép meghibásodik.  
>
> A Service Fabric egy csomópont nem az, hogy módosítsa a tartalék tartomány vár. Más mechanizmusok, például a biztosítsa a magas rendelkezésre állású virtuális gépek [magas rendelkezésre ÁLLÁSÚ virtuális gépek](https://technet.microsoft.com/en-us/library/cc967323.aspx) okozhat ütközik a Service Fabric, mivel ezek a virtuális gépek egyik gazdagépről egy másikra átlátszó áttelepítési. Ezek a mechanizmusok ne konfigurálja újra, és értesíti a virtuális Gépen belül futó kód. Így azok **nem támogatott** , környezetekben a Service Fabric rendszert futtató fürtöket. A Service Fabric alkalmazott csak magas rendelkezésre állású technológia kell lennie. Például a virtuális gép élő áttelepítést, a mechanizmusok San hálózatok, vagy mások számára nem szükséges. Ha a Service Fabric, ezek a mechanizmusok együtt használható _csökkentése_ alkalmazás rendelkezésre állásának és megbízhatóságának vezetnek nagyobb fokú összetettségével jár, mert hiba központi adatforrások hozzáadása, és megbízhatósági és rendelkezésre állási stratégiát, amely ütközik a Service Fabric a felhasználását. 
>
>

Az alábbi ábra azt hozzájárulnak a tartalék tartományok és a lista összes a különböző tartalék tartományok, amelyek entitások szín. Ebben a példában tudunk adatközpontok ("DC"), például rackszekrények ("R") és panelen (a "B"). Kapcsolódását Ha minden panel egynél több virtuális gép, lehetnek réteget a tartalék tartomány hierarchiában.

<center>
![Tartalék tartományok keresztül szervezett csomópontja][Image1]
</center>

Futásidőben a Service Fabric fürt erőforrás-kezelő úgy ítéli meg, a tartalék tartományok a fürtben, és elrendezések tervek. Az állapot-nyilvántartó replikák vagy egy adott szolgáltatáshoz állapotmentes példányok kerül terjesztésre, így a külön tartalék tartományok vannak. A szolgáltatás rendelkezésre állását ne legyenek veszélyben Ha tartalék tartomány nem sikerül, a hierarchia bármely szinten osztja el a szolgáltatás tartalék tartományok között biztosítja.

A Service Fabric-fürt erőforrás-kezelő nem fontos a tartalék tartomány hierarchiában nincsenek hány rétegek. Azonban megpróbálja győződjön meg arról, hogy elvész az a hierarchia bármely egy részét nem befolyásolja a benne futó szolgáltatásokat. 

Ez nem ajánlott, ha a tartalék tartomány hierarchia mélységét egyes szintjein a csomópontok azonos számú. Ha a tartalék tartományok "fa" egyenetlen a fürtben, megnehezíti a a fürt erőforrás-kezelő csak mérje fel, a legjobb lefoglalása a szolgáltatások. Imbalanced tartalék tartományok elrendezések jelenti, hogy a tartományok hatással a több, mint a más tartományokban szolgáltatások rendelkezésre állásának elvesztését. Emiatt a fürt erőforrás-kezelő között két célt levágása: a gépek alkalmazza az adott "nehéz" tartományban szolgáltatások helyez, és helyezze el a szolgáltatásokat a más tartományokban található, hogy elvész az egy tartomány nem problémákhoz kíván. 

Mire imbalanced tartományok figyelni például? Az alábbi ábrán két különböző fürt elrendezések megmutatjuk. Az első példában a csomópontok lesznek elosztva a tartalék tartományok között. A második példában egy tartalék tartomány számos további csomópontokat, mint a más tartalék tartományok rendelkezik. 

<center>
![Két különböző fürt elrendezések][Image2]
</center>

Az Azure-ban, amelyek a tartalék tartomány csomópontot tartalmaz lehetősége van kezelve. Azonban a csomópontokra, amelyeket kiépítése számától függően továbbra is fejezheti a tartalék tartományok többinél azokat a további csomópontokkal. Tegyük fel például, öt tartalék tartományok a fürt rendelkezik, de egy adott NodeType hét csomópontok létrehozni. Ebben az esetben az első két tartalék tartományok végül több csomópontot. Ha továbbra is telepítheti további NodeType tulajdonságok értéke csak néhány osztályt, rosszabb lekérdezi a problémát. Ezért azt javasoljuk, hogy minden csomóponton található csomópontok számának típusa legyen tartalék tartományainak számát.

## <a name="upgrade-domains"></a>Frissítési tartományok
Frissítési tartományok, amelyek segítségével a Service Fabric fürt erőforrás-kezelő tisztában azzal, hogy a fürt egy másik szolgáltatás. Frissítési tartományok megadása a csomópontokra, amelyeket egyszerre frissítik-készletek. Frissítési tartományok megértéséhez, valamint a kezelési műveletek, például a frissítések levezényelni a fürt erőforrás-kezelő segítségével.

Frissítési tartományok sokkal vannak, például a tartalék tartományok, de néhány fontosabb különbségeket. Első lépésként koordinált hardverhibák területéhez tartalék tartományok definiálása. Frissítési tartományok, másrészt határozzák meg a házirend. Döntse el, hogy hány azt szeretné, helyett ez alatt meghatározni a környezet elérhetővé. Tetszőleges számú frissítési tartományok csomópontok módon lehet. Egy másik tartalék tartományok és a frissítési tartományok közötti különbség, hogy a frissítési tartományok nincsenek hierarchikus. Ehelyett azok több mint egy egyszerű címke. 

Az alábbi ábrán látható három frissítési tartományok szétteríti a három tartalék tartományok. Emellett egy lehetséges három különböző replikáinak állapotalapú szolgáltatás elhelyezésének, ahol minden egyes fejeződik be a különböző tartalék és frissítési tartományok jelenít meg. Az elhelyezési lehetővé teszi, hogy a szolgáltatás frissítése közepén tartalék tartomány elvesztését, és továbbra is rendelkezik-e a kód és az adatok egy másolatával.  

<center>
![A tartalék és verziófrissítési elhelyezése][Image3]
</center>

Vannak előnyei és hátrányai, hogy nagyszámú tartományok frissítése. További frissítési tartományok azt jelenti, hogy a frissítés egyes lépéseinek részletesebb, és ezért hatással van a csomópont vagy a szolgáltatások kevesebb. Ennek eredményeképpen kevesebb szolgáltatások kell áthelyezni, egyszerre kisebb a forgalom bemutatása a rendszerbe. Ez általában megbízhatóbb, mivel kevesebb, a szolgáltatás bármely rendszerben jelent meg a frissítés során probléma van hatással. Több frissítési tartományt is jelenti, hogy kell-e a többi csomóponton a frissítés hatással kezeli kevesebb elérhető puffer. Például ha öt frissítési tartományok, az egyes csomópontok vannak kezelése nagyjából 20 %-a forgalmat. Ha adott frissítési tartomány frissítés miatt le van szüksége, terhelés általában nyissa meg a fürtnevek kell. Négy fennmaradó frissítési tartományban vannak, mivel minden hely számára a teljes forgalom körülbelül 5 %-át kell rendelkeznie. További frissítési tartományok azt jelenti, hogy a fürt csomópontjain kevesebb puffer van szüksége. Tegyük fel, hogy ha 10 frissítési tartománya volt helyette. Ebben az esetben egyes UD volna csak végző körülbelül 10 %-a teljes forgalom. Ha a frissítési lépéseket a fürt keresztül, minden tartomány csak kellene hely a körülbelül 1.1 %-a teljes forgalom számára. További frissítési tartományok általában lehetővé teszi a csomópontok futtatását, hogy nagyobb mértékű használatot, mivel kevesebb lefoglalt kapacitás szükséges. Ugyanez érvényes a tartalék tartományok.  

A hátránya, hogy hány frissítési tartományok, hogy a frissítések általában a hosszabb időt vesz igénybe. A Service Fabric vár egy rövid idő alatt után egy frissítési tartomány befejeződött, és a frissítés megkezdése előtt ellenőrzi. Ezek a késleltetések engedélyezése a frissítés előtt a frissítés előrehalad bevezetett észlelése problémákat. Mi a fontosabb elfogadható, mivel megakadályozza, hogy rossz módosítások befolyásolják a szolgáltatás túl sok egyszerre.

Túl kevés frissítési tartományok rendelkezik sok negatív hatásai – minden egyes frissítési tartomány nem működik, amíg a frissítés alatt áll egy nagy része a teljes kapacitásának nem érhető el. Például ha csak három frissítési tartományok készítésének körülbelül 1/3 a teljes szolgáltatás vagy a fürt kapacitás le egyszerre. Hogy nagy részét a szolgáltatás le egyszerre nem kívánatos, óta, hogy elegendő kapacitással rendelkeznek a többi a fürt a terhelés kezelésére. Adott puffer azt jelenti, hogy normál működés során azokat a csomópontokat, kisebb, mint egyébként betöltött karbantartása. Ez növeli a szolgáltatást futtató költségét.

Nincs valós egy olyan környezetben, vagy korlátozza a hogyan átfedik hiba vagy a frissítési tartományok száma korlátozva van. Említett, van néhány gyakori minták:

- Tartalék tartományok és a frissítési tartományok leképezése 1:1
- Egy frissítési tartomány-csomópontokban (fizikai vagy virtuális OS példány)
- Ha a tartalék tartományok és a frissítési tartományok alkotják a mátrix általában fut le a átlói gépekkel "csíkozott" vagy "mátrix" modell

<center>
![Hiba és a frissítési tartomány elrendezések][Image4]
</center>

Nem ajánlott mely elrendezés kiválasztása választ, a mindegyike rendelkezik néhány előnyei és hátrányai. Például a 1FD:1UD modell használata egyszerű, állíthatja be. Az 1 csomópont modell frissítése tartományonkénti a legtöbb például milyen személyek szolgálnak. Frissítések során minden egyes csomópont önállóan frissül. Ez hasonlít hogyan rövid gépek készleteinek frissített manuálisan a múltban. 

A leggyakoribb modell a FD/UD mátrix, ahol a FDs és UDs egy táblát, és csomópontok kerülnek, a átlós mentén indítása. Ez az alapértelmezés szerint a Service Fabric-fürtök az Azure-ban használt modell. A sok csomópontokkal rendelkező fürtök mindent fejeződik be például a fenti sűrű mátrix minta keresése.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Hiba és a frissítési tartomány típusmegkötéseket és az eredményül kapott viselkedéstől
A fürt erőforrás-kezelő szolgáltatás között hiba és a frissítési tartományok korlátozásként a tudni kezeli. További információk a korlátozások található [Ez a cikk](service-fabric-cluster-resource-manager-management-integration.md). Hiba és a frissítési tartomány megkötések állapotát: "egy adott szolgáltatáshoz partíció soha nem kell különbséget *nagyobb, mint egy* a szolgáltatás az objektumok számát (állapotmentes szolgáltatások példányok vagy állapotalapú szolgáltatási replikák) két tartomány között." Ez megakadályozza, hogy bizonyos helyezi át, vagy a szabályok, amelyek megszegnek ennél a határértéknél.

Nézzük például. Tegyük fel, hogy rendelkezik-e öt tartalék tartományok és öt frissítési tartományok hat csomóponttal rendelkező fürt.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Most tegyük fel, a szolgáltatás egy TargetReplicaSetSize (vagy, egy állapot nélküli szolgáltatáshoz az InstanceCount) létrehozhatunk öt. A replikák N1-N5 léphet. N6 valójában soha nem használt függetlenül attól, hány szolgáltatások, például a hoz létre. De miért? Vizsgáljuk meg a különbség a jelenlegi elrendezéshez és mi történne N6 van kiválasztva.

Az elrendezés azt kapott és az összes hiba és a frissítési tartomány / replikák itt található:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Ebben az elrendezésben kiegyensúlyozott tartalék tartomány és a frissítési tartományi csomópontok tekintetében. Azt is kiegyensúlyozott hiba és a frissítési tartomány / replikák száma tekintetében. Minden tartományban van, a csomópontok azonos száma és a replikák azonos számú.

Most mi történne helyett N2 kellett használtuk N6 vizsgáljuk meg. Hogyan kellene a replikák terjeszthetők majd?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Ebben az elrendezésben megsérti a definícióját a tartalék tartomány korlátozás. FD0 két replika van, míg FD1 nullát mutat, így a különbség FD0 és FD1 összesen két. A fürt erőforrás-kezelő nem engedélyezi a ezzel az elrendezéssel. Hasonlóképpen, ha azt kivételezett N2 és N6 (helyett N1 és N2) azt visszajelzést kap:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Ebben az elrendezésben kiegyensúlyozott tartalék tartományok tekintetében. Azonban most azt van a szabályt sértő a frissítési tartomány korlátozás. Ennek az az oka UD0 rendelkezik nulla replikákat, míg két UD1 tartozik. Ezért ezt az elrendezést is érvénytelen, és nem tárolható a fürt-kezelő által. 

## <a name="configuring-fault-and-upgrade-domains"></a>Hiba és a frissítési tartományok konfigurálása
Tartalék tartományok és a frissítési tartományok definiálása történik meg automatikusan az Azure Service Fabric központi telepítések üzemeltetett. A Service Fabric szerzi be, és a környezet adatokkal az Azure-ból.

Ha saját fürt létrehozása folyamatban (vagy egy adott topológia lefuttatja a fejlesztési), megadhatja a tartalék tartomány és a frissítési tartomány információkat saját maga. Ebben a példában meghatároztuk a kilenc csomópont helyi fejlesztési fürtök három "adatközpontok" (mindnek három rackszekrények) is. A fürt ezen három üzemeltetésében csíkozott három frissítési tartomány is van. A konfigurációját nem éri el: 

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

az önálló verziója telepítéseinek művelet keresztül

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
> Fürtök keresztül Azure Resource Manager meghatározásakor tartalék tartományok és a frissítési tartományok Azure rendeli hozzá. Az Azure Resource Manager-sablonban a csomóponttípusok és a virtuálisgép-méretezési készlet definíciója, ezért nem tartalmazza a tartalék tartomány és a frissítési tartomány adatait.
>

## <a name="node-properties-and-placement-constraints"></a>Csomópont tulajdonságai és elhelyezési korlátozás
Egyes esetekben (a gyakorlatban, a legtöbbször ennek) fog ahhoz, hogy bizonyos munkaterhelések csak bizonyos típusú, a fürt csomópontjai futtassa. Például bizonyos alkalmazások és szolgáltatások igényelhet Feldolgozóegységekkel vagy SSD-k, míg mások számára nem engedélyezett. A nagy célcsoport-kezelési hardvert, hogy bizonyos munkaterhelések példája ott szinte minden n szintű architektúra. Egyes gépek az előtérben vagy a kiszolgáló oldalán található az alkalmazás API szolgál, és az ügyfelek vagy az interneten vannak kitéve. Különböző gépek, gyakran különböző hardver-erőforrások hajtja végre a számítási és tárolási rétegek. Ezek rendszerint _nem_ közvetlenül érhető el az ügyfelek vagy az interneten. A Service Fabric vár, hogy vannak-e esetben, amikor az adott munkaterhelés adott hardverkonfigurációk futtatni kell. Példa:

* egy meglévő n szintű alkalmazást új "vissza, és vette" a Service Fabric környezetbe
* a munkaterhelés szeretné futtatni a adott hardverekhez, a teljesítmény, a méretezési vagy a biztonsági elkülönítés okokból
* A munkaterhelés különítve a többi munkaterhelését házirend vagy az erőforrás felhasználási okokból kell lennie.

Ezek a konfigurációk rendezi támogatásához a Service Fabric rendelkezik címkéket alkalmazhatja csomópontok első osztályú fogalmát. Ezekkel a címkékkel nevezzük **csomópont tulajdonságai**. **Egy elhelyezési korlátozás** az egyes szolgáltatásokhoz válasszon egy vagy több csomópont tulajdonságai csatolt utasítások. Egy elhelyezési korlátozás határozza meg, ahol szolgáltatásainak futnia kell. Megkötések lesz bővíthető - bármely kulcs/érték pár is működik. 

<center>
![A fürt különböző terhelésekhez elrendezés][Image5]
</center>

### <a name="built-in-node-properties"></a>A beépített csomópont tulajdonságai
A Service Fabric néhány nem adhat meg hozzájuk automatikusan használható alapértelmezett csomópont tulajdonságait határozza meg. Az alapértelmezett tulajdonság adja meg az egyes csomópontok a **NodeType** és a **csomópontnév**. Így például lehet írni, mint egy elhelyezési korlátozás `"(NodeType == NodeType03)"`. Általában találtunk, amelyeknek NodeType kell lennie a legfontosabb általánosan használt tulajdonságokat. Ez a beállítás akkor hasznos, mivel a gép típusú megfelel 1:1. Minden számítógép típusú egy hagyományos n szintű alkalmazásokban típusú felel meg.

<center>
![Korlátozza és a csomópont tulajdonságai][Image6]
</center>

## <a name="placement-constraint-and-node-property-syntax"></a>Elhelyezési korlátozás és a csomópont tulajdonság szintaxis 
A csomópont tulajdonságban megadott értéknek string, bool, lehet, vagy hosszú aláírással. Az utasításban, hely: a szolgáltatás neve egy elhelyezési *megkötés* óta korlátozza, ahol a szolgáltatás a fürtben is futhat. A korlátozás, amely a fürt másik csomópont tulajdonságainak logikai utasítás lehet. A logikai utasításokat a érvényes választók a következők:

1) adott utasítások létrehozásához feltételes ellenőrzése

| Utasítás | Szintaxis |
| --- |:---:|
| "egyenlő" | "==" |
| "nem egyenlő" | "!=" |
| "nagyobb, mint" | ">" |
| "nagyobb, mint vagy egyenlő" | ">=" |
| "kisebb, mint" | "<" |
| "kisebb vagy egyenlő, mint" | "<=" |

2) logikai utasításokat a csoportosítási és logikai műveletek

| Utasítás | Szintaxis |
| --- |:---:|
| "és" | "&&" |
| "vagy" | "&#124;&#124;" |
| "nem" | "!" |
| "csoportot egyetlen utasításként" | "()" |

Az alábbiakban néhány olyan alapvető típusmegkötésein utasításokat.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Csak olyan csomópontok, ahol az általános elhelyezési korlátozás utasítás értéke "True" lehet a szolgáltatás rá. Csomópontot, amely nincs meghatározva tulajdonsága nem egyezik a tartalmazó tulajdonság elhelyezési korlátozás.

Tegyük fel, hogy egy adott csomópont típushoz definiálva a következő csomópont tulajdonságai:

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

az önálló verziója telepítéseinek művelet vagy az Azure-Template.json üzemeltetett fürtök. 

> [!NOTE]
> Az Azure Resource Manager-sablonban a csomóponttípus általában paraméteres. Az alábbihoz hasonlóan fog kinézni "[parameters('vmNodeType1Name')]" helyett "NodeType01".
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

Szolgáltatás elhelyezésének hozhat létre *megkötések* egy szolgáltatás, például az alábbiak szerint:

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

Ha NodeType01 valamennyi csomópontja érvényes, igény szerint kiválaszthatja, hogy a megkötés csomóponttípus "(NodeType == NodeType01)".

A ritkán használt adatok dolgot a szolgáltatás egy elhelyezési korlátozás kapcsolatos egyik, hogy azok dinamikusan frissíthető futásidőben. Ezért ha kell, egy szolgáltatás Navigálás a fürtben, hozzáadhat és eltávolítja a követelmények, stb. A Service Fabric gondoskodik arról, hogy a szolgáltatás továbbra is naprakész és elérhető akkor is, ha az ilyen jellegű módosítások történik.

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

Minden elnevezett példány különböző Placement Constraints korlátozásokat vannak megadva. Frissítések mindig szükség van a hely (felülírása) mi korábban lett megadva.

A fürt definition a tulajdonságainak meghatározása a csomóponton. A csomópont tulajdonságainak módosításához szükséges konfigurációs Fürtfrissítés. Egy csomópont tulajdonságainak frissítéséhez indítsa újra a jelentést az új tulajdonságok minden érintett csomópont. Ezek a működés közbeni frissítés a Service Fabric kezeli.

## <a name="describing-and-managing-cluster-resources"></a>Leíró, és a fürt erőforrások kezelése
A legfontosabb feladatok bármely orchestrator egyik erőforrás-felhasználás a fürt kezeléséhez. Fürt-erőforrások kezelése azt több különböző fogalom. Először is hiba van biztosításához, hogy a gépek nem túlterhelt. Ez azt jelenti, hogy meggyőződött arról, hogy, hogy gépek kezelni tud, mint szolgáltatás nem fut-e. Második nincs terheléselosztási és optimalizálás, ami kritikus fontosságú szolgáltatások hatékony működését. Költség hatályos vagy a teljesítmény-és nagybetűket szolgáltatásajánlatok nem engedélyezheti a működés közbeni kell, míg mások cold egyes csomópontok. Működés közbeni csomópontok előfordulhat, hogy Erőforrásverseny és a gyenge teljesítményt, és cold csomópontok jelölik a feleslegesen erőforrások és a megnövekedett költségeit. 

A Service Fabric jelöli az erőforrásokhoz, mint `Metrics`. Adatok gyűjtése le a logikai és fizikai erőforrás írják le, hogy a Service Fabric kívánt. Metrikák Példák többek között a "WorkQueueDepth" vagy "MemoryInMb". A fizikai erőforrásokat, amelyek képesek felügyelni a Service Fabric csomópontján információ: [erőforrás irányítás](service-fabric-resource-governance.md). Egyéni metrikák és a használatukat konfigurálásával kapcsolatos további információkért lásd: [Ez a cikk](service-fabric-cluster-resource-manager-metrics.md)

Adatok gyűjtése le eltérő adatközpontokon korlátozások és a csomópont tulajdonságait. Csomópont tulajdonságai statikus leírójára magukat a csomópontokat. Metrikák csomópontok rendelkező erőforrásokat és, hogy a szolgáltatások felhasználásához csomóponton futtatásakor ismertetik. A csomópont-tulajdonságok "HasSSD" lehet, és IGAZ vagy hamis állítható be. Az adott SSD és a szolgáltatások mennyi felhasznált lemezterület mennyisége például a "DriveSpaceInMb" metrika lenne. 

Fontos megjegyezni, hogy csakúgy, mint egy elhelyezési korlátozás és a csomópont tulajdonságait, a Service Fabric fürt erőforrás-kezelő nem ismeri a metrikák nevét jelenti. Metrika nevében csak karakterláncok számítanak. Tanácsos egységek deklarálja a metrika neve nem egyértelmű lehet létrehozott részeként.

## <a name="capacity"></a>Kapacitás
Ha kikapcsolt minden erőforrás *terheléselosztási*, a Service Fabric-fürt erőforrás-kezelő továbbra is biztosítja, hogy nincs csomópont véget ért a kapacitás keresztül. Kapacitás meghaladása kezelése használata lehetséges, kivéve, ha a fürt tele, vagy a munkaterhelés nagyobb, mint egyetlen csomópont. Egy másik *megkötés* , hogy használja-e a fürt erőforrás-kezelő megérteni, hogy milyen egy erőforrást egy csomópont van. Fennmaradó kapacitás is nyomon követi a fürt egésze. A kapacitás és a szolgáltatási szinten a fogyasztás mérőszámok vannak kifejezve. Így például a metrika "ClientConnections" lehet, és egy adott csomópont lehet egy "ClientConnections" 32768 tartozó kapacitás. Más csomópontok rendelkezhetnek más néhány szolgáltatás fut az adott csomópont is mondja ki, nem jelenleg használ-e a "ClientConnections" metrika 32256 korlátok.

A fürt erőforrás-kezelő futásidőben, nyomon követi a fennmaradó kapacitás, a fürt és a csomóponton. Kapacitás nyomon követheti a fürt erőforrás-kezelő kivonja a csomópont-kapacitás, a szolgáltatást futtató minden egyes szolgáltatás használati. Az információ a Service Fabric fürt erőforrás-kezelő ismerhetik hol helyezze el, vagy helyezze át a replikákat, hogy a csomópontok kapacitása nem ismerteti.

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

A fürtjegyzékben megadott kapacitások látható:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

az önálló verziója telepítéseinek művelet vagy az Azure-Template.json üzemeltetett fürtök. 

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

Általában az adott szolgáltatás módosításának betöltése dinamikusan. Tegyük fel például, hogy a replika betöltése "ClientConnections" beállítás változása: 1024 2048, de a csomópont működő állapotban volt a majd csak kellett, hogy a metrika a fennmaradó 512 kapacitás. Most már a adott másodpéldány vagy a példány elhelyezési érvénytelen, mert nincs elég hely a csomóponton. A fürt erőforrás-kezelő rendelkezik indítsa, és vissza a kapacitás alatt a csomópont. Csökkenti a replikák és a példányok közül az adott csomópont át más csomópontokra kapacitás felett van a csomópont terhelése. Replikák áthelyezésekor a fürt erőforrás-kezelő megpróbálja minimálisra csökkenthető az ilyen mozgást költségét. A mozgás költsége ismertet [Ez a cikk](service-fabric-cluster-resource-manager-movement-cost.md) és további információ a fürt erőforrás-kezelő által újraelosztás stratégiák és szabályok leírt [Itt](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Fürt kapacitás
Igen, hogyan a Service Fabric fürt erőforrás-kezelő túl megtelt a teljes fürt megtartani? A dinamikus terhelés nincs sokkal lehet hasznos. Szolgáltatások lehet a terhelés csúcs, függetlenül a fürt-kezelő által végrehajtott műveleteket. Emiatt a fürt bőven ma belső magasságnak lehet underpowered válásának Ön famous holnap. Említett, vannak bizonyos vezérlők, a problémák megelőzése érdekében a rendszer bővíthetőség. A legfontosabb tehetünk ennek, amelyek a fürt teljes válik új típusoktól létrehozásának megakadályozása.

Tegyük fel például, hogy állapot nélküli szolgáltatás, és néhány terhelés társítva van. Tegyük fel, hogy a szolgáltatás ügyel "DiskSpaceInMb" metrikát. Tételezzük is fel, hogy megszakítja a "DiskSpaceInMb" a szolgáltatás minden példányának öt egységei felhasználását. Szeretne létrehozni a szolgáltatás három példányát. Remek! Úgy, hogy azt jelenti, hogy meg kell lennie ahhoz, hogy akkor is igaz, a fürt "DiskSpaceInMb" 15 egységei hozhatja létre a szolgáltatáspéldány. A fürt erőforrás-kezelő folyamatosan kiszámítja a kapacitás és mindegyik metrikát fogyasztásának az alapján határozza meg a fürt kapacitása. Ha nincs elég hely, a fürt erőforrás-kezelő elutasítja a létrehozási hívás.

Mivel a követelmény csak az, hogy nincs 15 egység érhető el, ez a terület rendelhető számos különböző módja. Például lehet egy fennmaradó egység 15 különböző csomópontokon kapacitás vagy három fennmaradó egységek öt különböző csomópontokon kapacitást. Ha a fürt erőforrás-kezelő is átrendezéséhez dolgot úgy érhető el öt egység három csomóponton helyezi a szolgáltatást. A fürt átrendezése használata általában lehetséges, kivéve, ha a fürt majdnem megtelt, vagy a meglévő szolgáltatások valamilyen okból kifolyólag nem lehet összevont.

## <a name="buffered-capacity"></a>A pufferelt kapacitás
A pufferelt kapacitása a fürt Resource Manager egy másik szolgáltatás. Lehetővé teszi a Foglalás a csomópont teljes kapacitásának egy részét. A kapacitás puffer csak akkor helyezhető el a szolgáltatások frissítéseket és a csomóponthibák során használatos. A metrika az összes csomópont pufferelt kapacitás globálisan van meghatározva. A lefoglalt kapacitás a választott érték függvényében hiba és a fürt rendelkezik frissítési tartományok száma. Több hiba és frissítési tartományok azt jelenti, hogy kevesebb kiválaszthatja a pufferelt kapacitást. Ha több tartományban vannak, várhatóan kisebb adatmennyiségek a fürt nem érhető el, frissítések és a hibák alatt. Kapacitás pufferelt megadása csak értelme, ha a csomópont-kapacitás olyan metrikajelentés is megadott.

Íme egy példa bemutatja, hogyan adhatja meg a pufferelt kapacitás:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

az önálló verziója telepítéseinek művelet vagy az Azure-Template.json üzemeltetett fürtök:

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

Az új szolgáltatások a létrehozás sikertelen lesz, amikor a fürt olyan metrikajelentés pufferelt kapacitás kívül esik. A puffer megőrzése érdekében új szolgáltatások megelőzése biztosítja, frissítése és a hibák kapacitás áthaladhat csomópontok nem okoznak. A pufferelt kapacitás nem kötelező, de ajánlott a fürt, amely meghatározza egy olyan metrikajelentés kapacitás.

A fürt erőforrás-kezelő teszi közzé a terhelés kapcsolatos információkat. Mindegyik metrikát az alábbiakat: 
  - a pufferelt beállításait
  - a teljes kapacitás
  - a jelenlegi felhasználás
  - vagy nem mindegyik metrikát tekinthető e rendszerrel
  - a szórás statisztikája
  - a csomópontokat, amelyek a legtöbb és a legalacsonyabb  
  
Az alábbiakban egy példa kimenet látható:

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

## <a name="next-steps"></a>Következő lépések
* Az architektúra és információk folyamat belül a fürt erőforrás-kezelő információkért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-architecture.md)
* Lemeztöredezettség-mentesítés metrikák meghatározása az összevonni helyett ezzel azt csomópontok terhelése egyik módja. Lemeztöredezettség-mentesítés konfigurálásáról további tudnivalókért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Indítsa el az elejétől és [Bevezetés a Service Fabric fürt Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
* Hogyan kezeli a fürt erőforrás-kezelő, és elosztja a terhelést a fürt kapcsolatos további tudnivalókért tekintse meg a cikk a [terheléselosztás](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
