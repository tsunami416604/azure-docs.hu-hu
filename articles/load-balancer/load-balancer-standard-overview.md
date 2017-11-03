---
title: "Az Azure Load Balancer szabványos áttekintése |} Microsoft Docs"
description: "Azure Load Balancer szabványos funkcióinak áttekintése"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2017
ms.author: kumud
ms.openlocfilehash: 08e4e22ae7e5d6f6efad458b4240a6d57090e865
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="azure-load-balancer-standard-overview-preview"></a>Az Azure Load Balancer szabványos áttekintése (előzetes verzió)

Az Azure Load Balancer Standard Termékváltozat és nyilvános IP-Standard Termékváltozat együtt lehetővé teszik rugalmasan méretezhető, megbízható architektúrák létrehozásához. Alkalmazásokat, amelyek használják a Load Balancer szabványos kihasználhatják új képességeit. Kis késleltetésű, a magas teljesítmény és a skála az összes TCP és UDP-alkalmazás adatfolyamok több millió érhetők el.

>[!NOTE]
> A Load Balancer Standard Termékváltozat jelenleg előzetes verzió. Előzetes a szolgáltatás esetleg nincs azonos szintű rendelkezésre állást és megbízhatóságot, szolgáltatások, amelyek általában a rendelkezésre állási kiadási. További részletekért lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Használja a nyilvánosan elérhető [Load Balancer alapszintű Termékváltozat](load-balancer-overview.md) a termelés számára. Az ebben az előzetes kapcsolódó szolgáltatások [rendelkezésre állási zónák](https://aka.ms/availabilityzones), és [magas rendelkezésre ÁLLÁSÚ portok](https://aka.ms/haports), különálló regisztrációs jelenleg szükséges. A megfelelő utasításokat kövesse ezeket a szolgáltatásokat, regisztrál a Load Balancer mellett az [szabványos előzetes](#preview-sign-up).

## <a name="why-use-load-balancer-standard"></a>Miért használ a Load Balancer szabványos?

Használhat Load Balancer szabványos virtuális adatközpontok teljes skáláját. A nagy és összetett több zóna architektúrák kis méretű telepítések alkalmazás Load Balancer szabványos előnyeit a következő lehetőségeket:

- [Vállalati méretű](#enterprisescale) a Load Balancer Standard érheti el. Ez a szolgáltatás használható, ha bármely virtuális gép (VM) példánya, mely legfeljebb 1000 Virtuálisgép-példányok a virtuális hálózaton belül.

- [Új diagnosztikai insights](#diagnosticinsights) elérhető segítenek megérteni, kezelése és hibáinak elhárítása a virtuális adatközpontok alapvető összetevőjét. Azure-figyelője (előzetes verzió) segítségével megjelenítése, szűrésére és az adatok folyamatos elérési állapotfigyelő mérték új többdimenziós metrikák csoportban. Az adatok az előtér virtuális gépre, a végpont állapota mintavételek menüpontban, az TCP-kapcsolódási kísérletek, és a kimenő kapcsolatok figyelésére.

- [Hálózati biztonsági csoportok](#nsg) van a Virtuálisgép-példány betöltése terheléselosztó szabványos termékváltozatok vagy nyilvános IP-Standard termékváltozat társított most szükséges. Hálózati biztonsági csoportokkal (NSG-k) adja meg a forgatókönyvnek fokozott biztonságot.

- [Magas rendelkezésre állású (HA) portot adja meg a magas megbízhatósági](#highreliability) és hálózati virtuális készülékek (NVAs) skálájának és más alkalmazás-forgatókönyveket. Magas rendelkezésre ÁLLÁSÚ portok terhelésének elosztása minden port a egy Azure belső betölteni a terheléselosztó (ILB) előtér egy Virtuálisgép-példányok készletéhez.

- [Kimenő kapcsolatok](#outboundconnections) most egy új forrás hálózati cím fordítási (SNAT) port foglalási modellt használja, amely nagyobb rugalmasságot és a skála.

- [A rendelkezésre állási zónák szabványos terheléselosztó betölteni](#availabilityzones) zónaredundáns és zonal architektúrák is használható. Mindkét ezek architektúrák tartalmazhatnak terheléselosztás kereszt-zóna. Zóna-redundancia DNS-rekordok függőség nem érhető el. Egyetlen IP-címnek zónaredundáns alapértelmezés szerint.  Egyetlen IP-címnek e virtuális Gépet egy virtuális hálózaton belül az összes rendelkezésre állási zóna között van terület érhető el.


Választhat Load Balancer szabványos nyilvános vagy belső konfiguráció a következő alapvető forgatókönyvek támogatása céljából:

- A terhelést a megfelelő háttér-példányok bejövő forgalmát.
- Port bejövő forgalom háttér-egypéldányos.
- A nyilvános IP-cím egy magánhálózati IP-címet a virtuális hálózaton belül kimenő forgalmát lefordítani.

### <a name = "enterprisescale"></a>Vállalati méretű

 A nagy teljesítményű virtuális adatközpont tervezése, és bármely TCP vagy UDP-alkalmazásokat használni a Load Balancer szabványos. Önálló Virtuálisgép-példány használja, vagy legfeljebb 1000 példányai virtuálisgép-méretezési beállítja a háttér-készletben. Továbbra is használhatja a továbbítási kis késés, a magas teljesítmény teljesítmény és a méretezhetőség adatfolyamok több millió egy teljes körűen felügyelt Azure-szolgáltatáshoz.
 
Load Balancer szabványos továbbíthatja a Virtuálisgép-példány az egy régióban található virtuális hálózat felé irányuló forgalom. Háttér-készlet mérete legfeljebb 1000 példányok a következő virtuális gép forgatókönyvek tetszőleges kombinációja lehet:

- Önálló virtuális gépek rendelkezésre állási készletek nélkül
- Önálló virtuális gépek rendelkezésre állási csoportok
- Virtuálisgép-skálázási készletekben, legfeljebb 1000 példány
- Több virtuálisgép-skálázási készletekben
- A virtuális gépek és virtuálisgép-méretezési csoportok színátmenet

Nincs már nem követelmény a rendelkezésre állási csoportok. Ha szeretné, rendelkezésre állási készletek használata a előnye, hogy biztosítani.

### <a name = "diagnosticinsights"></a>Diagnosztikai insights

Load Balancer szabványos nyilvános és a belső terheléselosztó konfigurációk új többdimenziós diagnosztikai képességeket biztosít. Új metrikákat szolgáltatáson keresztül Azure Monitor (előzetes verzió), és a kapcsolódó funkciók, például az alsóbb rétegbeli fogyasztók integrálása felhasználását.

| Metrika | Leírás |
| --- | --- |
| VIP rendelkezésre állása | Load Balancer szabványos folyamatosan gyakorolja az elérési útja a régión belül egészen a SDN-készlet, amely támogatja a virtuális gép előtér terheléselosztóhoz. Mindaddig, amíg megfelelő példányok marad, a mérési követi az alkalmazás terheléselosztott forgalom az adott elérési úton. Az elérési útja az ügyfelek által használt is van hitelesítve. A mérték nem látható, hogy az alkalmazást, és nem ütközik más műveleteket.|
| DIP rendelkezésre állása | Load Balancer szabványos használ egy elosztott szolgáltatás, amely figyeli a konfigurációs beállítások alapján az alkalmazás végpontjának állapotát probing állapotát. Ez a metrika összesítő biztosít, vagy egy végpont szűrt-nézetben minden egyes példány végpont a terheléselosztó-készlet.  Láthatja, hogyan terheléselosztó megtekinti a mintavételi állapotkonfiguráció jelöli az alkalmazás állapotát.
| Szinkronizálás a mi csomagok | Load Balancer szabványos nem állítsa le a TCP-kapcsolatok vagy TCP vagy UDP-csomagok adatfolyamok kommunikál. Adatfolyamok és azok kézfogások mindig a forrás- és a Virtuálisgép-példány közé esnek. Jobb elhárításában a TCP protokollt, hogy SZIN használatát tudni, hogy hány TCP-kapcsolat csomagok kísérletet tesz. A metrika a fogadott TCP SZIN csomagok számát jelenti. A metrika is előfordulhat, hogy tükrözze az ügyfelek, amelyek a kapcsolatot a service.|
| SNAT kapcsolatok | Load Balancer szabványos jelentések előtérbeli nyilvános IP-címre van masqueraded kimenő kapcsolatok száma. SNAT portjait egy kimeríthető erőforrás. Ez a metrika biztosíthat utalhat, hogy hogyan fokozottan az alkalmazás SNAT kimenő kezdeményezésű kapcsolatok függő van.|
| Bájt számlálók | Load Balancer szabványos jelentések az előtér-feldolgozott adatokat.|
| Csomag számlálók | Load Balancer szabványos jelentések egy előtér-feldolgozott a csomagokat.|

### <a name = "highreliability"></a>Magas megbízhatóság

Terheléselosztási szabályok az alkalmazás méretezési és nagymértékben megbízható kell konfigurálni. Úgy is beállíthatja az egyes portok szabályokat, vagy használhatja a magas rendelkezésre ÁLLÁSÚ portokat minden forgalmat a TCP vagy UDP-portszám függetlenül elosztása érdekében.  

A magas rendelkezésre ÁLLÁSÚ portok szolgáltatását használhatja különféle forgatókönyvekhez, amik, beleértve a magas rendelkezésre állás és méretezhetőség belső NVAs feloldásához. A szolgáltatás akkor hasznos, az egyéb forgatókönyvek, ahol nem praktikus, vagy megadhat egyéni portokat a nemkívánatos. Magas rendelkezésre ÁLLÁSÚ portok biztosítható a redundancia és a skála azáltal, hogy annyi példányok csak szeretne. A konfigurációs rendszer már nem aktív/passzív forgatókönyvek. A rendszerállapot-mintavétel konfigurációk védi meg a szolgáltatás csak megfelelő példányok-forgalom.

NVA szállítók teljes mértékben szállítói támogatott, a refs forgatókönyvek biztosíthat az ügyfelek számára. A rendszer eltávolítja a hibaérzékeny pontok kialakulását, és több aktív példányok méretezési támogatottak. Két vagy több példányát, attól függően, hogy a készülék képességeinek méretezheti. A NVA gyártójától kaphat további útmutatás t forgatókönyvek esetén.

### <a name = "availabilityzones"></a>Rendelkezésre állási zónák

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

Az alkalmazás rugalmasság használatával a rendelkezésre állási zónák támogatott régióban előzetes. Rendelkezésre állási zónák jelenleg előzetes verzióban érhetők meghatározott régióiba, és további részt igényelnek.

### <a name="automatic-zone-redundancy"></a>Automatikus zóna-redundancia

Kiválaszthatja, hogy terheléselosztó biztosítania kell a zónaredundáns vagy zonal előtér-összes az alkalmazáshoz. Akkor is könnyen a Load Balancer Standard zóna-redundancia megteremtése érdekében. Egyetlen előtér-IP-címnek automatikusan zónaredundáns. A zónaredundáns előtér-által kiszolgált összes rendelkezésre állási zónák régióban egyidejűleg. Zónaredundáns adatútvonalat bejövő és kimenő kapcsolatok jön létre. Zóna-redundancia az Azure-ban nem kell több IP-címek és DNS-rekordokat. 

Zóna-redundancia nyilvános vagy belső első végpontok érhető el. A nyilvános IP-cím és a belső terheléselosztó előtérbeli magánhálózati IP-címe lehet zónaredundáns.

A következő parancsfájl segítségével hozzon létre egy zónaredundáns nyilvános IP-címet a belső terheléselosztóhoz. Ha meglévő Resource Manager-sablonok a konfigurációt használ, adja hozzá a **sku** szakasz ezeket a sablonokat.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

A következő parancsfájl segítségével hozzon létre egy zónaredundáns előtér-IP-cím a belső terheléselosztóhoz. Ha meglévő Resource Manager-sablonok a konfigurációt használ, adja hozzá a **sku** szakasz ezeket a sablonokat.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Ha a nyilvános IP-cím előtér-zónaredundáns, a kimenő kapcsolatokhoz a Virtuálisgép-példányok automatikusan zónaredundáns válik. Az előtér-védett a zóna hiba. A SNAT portkiosztással is survives zóna hiba.

#### <a name="cross-zone-load-balancing"></a>Kereszt-zóna terheléselosztás

Kereszt-zóna terheléselosztási érhető el a háttér-készlet régión belül, és a Virtuálisgép-példányok maximális rugalmasságot biztosít. A virtuális gép virtuális hálózatban, függetlenül a rendelkezésre állási zóna a Virtuálisgép-példány egy előtér-kézbesíti adatfolyamok.

Megadhatja, hogy egy adott zónában az előtér- és példányok, akkor igazíthatók az elérési útja és erőforrások eléréséről a megadott zónában.

A zóna soha nem korlátozza a virtuális hálózatok és alhálózatok. A konfigurálása nem fejeződött be, és csak a egy háttér-címkészlet, amely a kívánt Virtuálisgép-példányok megadása.

#### <a name="zonal-deployments"></a>Zonal központi telepítések

Alternatívaként igazíthatja a terheléselosztó előtér-egy adott zónához meghatározhat egy zonal előtér. Egy zonal előtér-által kiszolgált a kijelölt egy rendelkezésre állási zóna csak. Az előtér-együttes zonal Virtuálisgép-példányok, igazíthatja erőforrások zónához.

Egy nyilvános IP-címet, amely jön létre a megadott zónában mindig csak adott zónában van. Nincs lehetőség a zóna egy nyilvános IP-cím módosításához. Nyilvános IP-cím több zóna erőforrásokhoz csatolt hozzon létre helyette zónaredundáns nyilvános IP-cím.

A következő parancsfájl segítségével hozzon létre egy zonal nyilvános IP-címet a rendelkezésre állási zóna 1. Ha meglévő Resource Manager-sablonok a konfigurációt használ, adja hozzá a **sku** szakasz ezeket a sablonokat.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

A következő parancsfájl segítségével hozzon létre egy belső terheléselosztó előtér-a rendelkezésre állási zóna 1.

Ha meglévő Resource Manager-sablonok a konfigurációt használ, adja hozzá a **sku** szakasz ezeket a sablonokat. Továbbá adja meg a **zónák** tulajdonság az előtér-IP-konfigurációban a gyermek-erőforrás.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Adja hozzá a kereszt-zóna terheléselosztást a háttér-készlet és a Virtuálisgép-példány egy virtuális hálózatban, a készletbe.

A Load Balancer szabványos erőforrást, mert mindig regionális és zónaredundáns ahol rendelkezésre állási zónák használata támogatott. Telepítheti a nyilvános IP-cím vagy a belső terheléselosztó előtér-minden régióban egy hozzárendelt zóna nem található. Rendelkezésre állási zónák támogatása nem befolyásolja a telepítési lehetőség. Ha egy régiót később átveszi a rendelkezésre állási zónák, korábban telepített nyilvános IP-cím vagy a belső terheléselosztó első-végpontok automatikusan a zónaredundáns válnak. A zónaredundáns elérési útja nem feltétlenül jelenti azt a 0 % csomagveszteség.

### <a name = "nsg"></a>Hálózati biztonsági csoportok

Terheléselosztó szabványos és nyilvános IP szabványos teljesen bevezetésében betölteni a virtuális hálózathoz, amely hálózati biztonsági csoportokkal (NSG-k) használatát igényli. Az NSG-k lehetővé teszik az engedélyezési lista adatforgalmat. Az NSG-k segítségével forgalom teljes mértékben átveheti az Ön telepítésére. Többé nem kell várnia más adatforgalmakat, hogy végezze el.

NSG-k társítása alhálózathoz vagy a hálózati adapterek (NIC) a Virtuálisgép-példánya a háttér-készlet. Használja ezt a konfigurációt a Load Balancer Standard és a nyilvános IP szabványos, ha olyan példányszintű nyilvános IP-címet használják. Az NSG explicit módon kell ahhoz, hogy a forgalmat engedélyezni szeretné a forgalom engedélyezett.

Az NSG-k és hogyan kell alkalmazni a forgatókönyvnek kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../virtual-network/virtual-networks-nsg.md).

### <a name ="outboundconnections"></a>Kimenő kapcsolatok

Load Balancer szabványos biztosít a virtuális gépekhez, amelyek a virtuális hálózaton belül, ha a terheléselosztó által használt port színleg SNAT kimenő kapcsolatokat. A port színleg SNAT algoritmus nagyobb robusztusság és a skála biztosít.

Egy nyilvános terheléselosztó erőforrás társított Virtuálisgép-példányok esetén a rendszer írni minden kimenő kapcsolat forrása. A forrás a terheléselosztó előtérbeli nyilvános IP-cím a virtuális hálózat privát IP-címtér van írni.

Kimenő kapcsolatok használatakor a zónaredundáns egy előtér-, a kapcsolatok esetén is zónaredundáns és SNAT port lefoglalását zóna hiba képesek túlélni.

Az új algoritmust a Load Balancer szabványos preallocates SNAT minden virtuális gép hálózati adapter által használt portok. Egy hálózati Adaptert a készletben való felvételekor a SNAT portok vannak előzetesen lefoglalt készlet mérete alapján. Az alábbi táblázat a port preallocations háttér-készlet méretű hat rétege számára:

| Készlet mérete (Virtuálisgép-példány) | Előzetesen lefoglalt SNAT port |
| --- | --- |
| 1 - 50 | 1024 |
| 51 - 100 | 512 |
| 101 - 200 | 256 |
| 201 - 400 | 128 |
| 401 - 800 | 64 |
| 801 - 1,000 | 32 |

SNAT portok közvetlenül nem lefordítani a kimenő kapcsolatok száma. Egy SNAT port a több célhoz rendelt egyedi felhasználhatók. További információkért tekintse át a [kimenő kapcsolatok](load-balancer-outbound-connections.md) cikk.

Ha a háttér-készlet mérete növeli, és a magasabb szintű használható közeledik, a lefoglalt portok felének visszaigényelt vannak. Kapcsolatok száma, amelyek regenerált port időtúllépés tartoznak, és létre kell hozni. Új kapcsolódási kísérletek sikeres azonnal. Ha csökkenti a háttér-készlet méretét, és az alacsonyabb szint transitions lehetőségnél SNAT elérhető portok száma növekszik. Ebben az esetben nem érinti a meglévő kapcsolatok.

Load Balancer szabványos rendelkezik olyan további konfigurációs beállítás, amely a szabály alapon is használható. Azt is szabályozhatja, amely előtér használja port színleg SNAT, amikor több első-végpontok érhetők el.

Ha csak Load Balancer szabványos szolgálja ki a Virtuálisgép-példányok, SNAT kifelé irányuló kapcsolatok nem érhetők el. Ez a lehetőség explicit módon visszaállíthatja a Virtuálisgép-példányok is rendel egy nyilvános terheléselosztó. Nyilvános IP-címek közvetlenül is rendelhet hozzá, példányszintű nyilvános IP-címek Virtuálisgép-példányokhoz csatlakoznak. Lehet, hogy ez a beállítás szükséges az egyes operációs rendszer és az alkalmazás-forgatókönyvek. 

### <a name="port-forwarding"></a>Port átirányítása

Egyszerű és szabványos Terheléselosztók biztosít az ügyfélgépek konfigurálására olyan előtér-port hozzárendelése egyéni háttér-példány bejövő NAT-szabályok. Úgy konfigurálja ezeket a szabályokat, a távoli asztal protokoll végpontok és az SSH-végpontok tesz közzé, vagy más alkalmazás-forgatókönyvekre végrehajtani.

Load Balancer szabványos továbbra is a bejövő NAT-szabályok port-továbbítási lehetőséget nyújtanak. Zónaredundáns első-ends elemmel sem használatakor a bejövő NAT-szabályok zónaredundáns válik, és után is megmaradnak a zóna hiba.

### <a name="multiple-front-ends"></a>Több első-végpontok

Az alkalmazásoknak kell tenni, például a TLS-webhelyek vagy az SQL AlwaysOn rendelkezésre állási csoport végpontok több egyedi IP-cím konfigurálása több első-végpontok tervezési rugalmasságot biztosít. 

Load Balancer szabványos továbbra is nyújt több első-végpontok ahol ki kell tenni egy adott alkalmazás végpontja egy egyedi IP-címet.

Több előtér-IP-cím konfigurálásával kapcsolatos további információkért lásd: [több IP-konfiguráció](load-balancer-multivip-overview.md).

## <a name = "sku"></a>Kapcsolatos termékváltozatok

Az Azure Resource Manager üzembe helyezési modellel termékváltozatok csak érhetők el. Az előzetes terheléselosztó és a nyilvános IP-erőforrások két termékváltozatok: Basic és Standard. A termékváltozat nem egyezik a képességek, teljesítményt nyújt, korlátozások és egyes belső viselkedését. Virtuális gépek vagy SKU használható. Mind a Terheléselosztóról, mind a nyilvános IP-cím erőforrások termékváltozatok választható attribútumok marad. SKU forgatókönyv definíciójában nincs megadva érték, ha a konfigurációs alapértelmezés szerint az alapszintű Termékváltozat használja.

>[!IMPORTANT]
>Az erőforrás termékváltozata nem változtatható. A meglévő erőforrás Termékváltozata nem módosítható.  

### <a name="load-balancer"></a>Load Balancer

A [meglévő terheléselosztó erőforrást](load-balancer-overview.md) az alapszintű Termékváltozat válik, és általában elérhető és változatlan marad.

Load Balancer Standard Termékváltozat új, és jelenleg előzetes verzióban érhetők. Az 1. augusztus, 2017, API-verzió Microsoft.Network/loadBalancers hozzáadja a **sku** tulajdonságot az erőforrás-definícióban:

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```
Load Balancer szabványos is automatikusan zóna-lehetséges a rendelkezésre állási zónák kínáló régióban. Ha a terheléselosztó zonal van deklarálva, majd nincs automatikusan zóna rugalmas.

### <a name="public-ip"></a>Nyilvános IP-cím

A [meglévő nyilvános IP-cím erőforrás](../virtual-network/virtual-network-ip-addresses-overview-arm.md) az alapszintű Termékváltozat válik, és az összes, a képességek, a teljesítményt és a korlátozások általánosan elérhető marad.

Nyilvános IP-Standard Termékváltozat új, és jelenleg előzetes verzióban érhetők. Az 1. augusztus, 2017, API-verzió Microsoft.Network/publicIPAddresses hozzáadja a **sku** tulajdonságot az erőforrás-definícióban:

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Nyilvános IP alapvető, amely több elosztási módszert kínál, eltérően nyilvános IP szabványos mindig használja statikus foglalási.

Nyilvános IP szabványos is automatikusan zóna-lehetséges a rendelkezésre állási zónák kínáló régióban. Ha a nyilvános IP-cím zonal van deklarálva, majd nincs automatikusan zóna rugalmas. Egy zonal nyilvános IP-cím nem módosítható egy zónából egy másikra.

## <a name="migration-between-skus"></a>SKU közötti áttelepítés

Termékváltozat nem változtatható. Kövesse az ebben a szakaszban található áthelyezése egy erőforrás SKU másik.

### <a name="migrate-from-basic-to-standard-sku"></a>Az alapvető át Standard Termékváltozat

1. Hozzon létre egy új szabványos erőforrást (a terheléselosztó és a nyilvános IP-címek, szükség esetén). Hozza létre újra a szabályok és a mintavételi definíciókat.

2. Az alapszintű Termékváltozat erőforrások (a terheléselosztó és a megfelelő, nyilvános IP) eltávolítása az összes Virtuálisgép-példányok. Győződjön meg arról, hogy távolítsa el az összes Virtuálisgép-példányok egy rendelkezésre állási készlet.

3. Az összes Virtuálisgép-példányok csatolni az új Standard Termékváltozat-erőforrások.

### <a name="migrate-from-standard-to-basic-sku"></a>Alapszintű Termékváltozat Standard áttelepítése

1. Hozzon létre egy új alapszintű erőforrást (a terheléselosztó és a nyilvános IP-címek, szükség esetén). Hozza létre újra a szabályok és a mintavételi definíciókat. 

2. A Standard Termékváltozat-erőforrások (a terheléselosztó és a megfelelő, nyilvános IP) eltávolítása az összes Virtuálisgép-példányok. Győződjön meg arról, hogy távolítsa el az összes Virtuálisgép-példányok egy rendelkezésre állási készlet.

3. Az összes Virtuálisgép-példányok csatolni az új alapszintű Termékváltozat-erőforrások.

>[!IMPORTANT]
>
>A Basic és Standard termékváltozat általi használatára vonatkozó korlátozások is.
>
>Magas rendelkezésre ÁLLÁSÚ portok és a szabványos metódust diagnosztikai találhatók csak a Standard Termékváltozat. Nem az alapszintű Termékváltozat a Standard Termékváltozat át, és nem is megőrzik, ezeket a szolgáltatásokat.
>
>Megfelelő termékváltozatok terheléselosztó és a nyilvános IP-erőforrások kell használni. Alapszintű Termékváltozat Standard Termékváltozat erőforrásokat és vegyesen tartalmazhat. Nem lehet csatolni a virtuális gép, egy rendelkezésre állási csoportban, virtuális gépek vagy virtuálisgép-méretezési egyidejűleg állítsa mindkét SKU.
>

## <a name="region-availability"></a>Régiónkénti elérhetőség

Load Balancer szabványos már érhető el ezeken a területeken:
- USA 2. keleti régiója
- USA középső régiója
- Észak-Európa
- USA nyugati középső régiója
- Nyugat-Európa
- Délkelet-Ázsia

## <a name="sku-service-limits-and-abilities"></a>Termékváltozat-szolgáltatásra vonatkozó korlátozások és képességek

Azure [szolgáltatásra vonatkozó korlátozások hálózati](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#networking-limits) régiónként alkalmazni. 

Az alábbi táblázat összehasonlítja a korlátozások és a Load Balancer Basic és Standard termékváltozat képességek:

| Load Balancer | Basic | Standard |
| --- | --- | --- |
| Háttér-készlet mérete | legfeljebb 100 | legfeljebb 1000 |
| Háttér-készlet határ | Rendelkezésre állási csoport | virtuális hálózat, a régiót |
| Háttér-készlet kialakítása | Virtuális gépek rendelkezésre állási csoportban, állítsa be a rendelkezésre állási csoport virtuálisgép-méretezési | A Virtuálisgép-példány a virtuális hálózat |
| Magas rendelkezésre ÁLLÁSÚ portok | Nem támogatott | Elérhető |
| Diagnosztika | Korlátozott, nyilvános csak | Elérhető |
| VIP rendelkezésre állása  | Nem támogatott | Elérhető |
| Gyors IP-címes | Nem támogatott | Elérhető |
|Rendelkezésre állási zónák forgatókönyvek | Csak Zonal | Zonal, zónaredundáns, kereszt-zóna terheléselosztás |
| Kimenő SNAT algoritmus | Igény szerinti | Előzetesen lefoglalt |
| Kimenő SNAT előtér-kiválasztása | Nem konfigurálható, több jelöltek | Opcionális konfigurációs jelöltek csökkentése érdekében |
| Hálózati biztonsági csoport | Nem kötelező a hálózati adapter/alhálózatot | Szükséges |

Az alábbi táblázat összehasonlítja a korlátozások és a nyilvános IP Basic és Standard termékváltozat képességek:

| Nyilvános IP-cím | Basic | Standard |
| --- | --- | --- |
| Rendelkezésre állási zónák forgatókönyvek | Csak Zonal | Zónaredundáns (alapértelmezett), zonal (nem kötelező) | 
| Gyors IP-címes | Nem támogatott | Elérhető |
| VIP rendelkezésre állása | Nem támogatott | Elérhető |
| Számlálók | Nem támogatott | Elérhető |
| Hálózati biztonsági csoport | Nem kötelező a hálózati adapter | Szükséges |


## <a name="preview-sign-up"></a>Előnézet-előfizetés

Az előzetes betöltési terheléselosztó Standard Termékváltozat és a kiegészítő nyilvános IP-Standard Termékváltozat részt, az előfizetés regisztrálása  Az előfizetés hozzáférést tud biztosítani, a PowerShell vagy Azure CLI 2.0 regisztrálása. Regisztrálásához hajtsa végre az alábbi lépéseket:

>[!NOTE]
>A Load Balancer szabványos szolgáltatás regisztrációs órát is igénybe vehet egy globálisan hatályba. Ha szeretné használni a Load Balancer szabványosnak [rendelkezésre állási zónák](https://aka.ms/availabilityzones) és [magas rendelkezésre ÁLLÁSÚ portok](https://aka.ms/haports), ezek az előzetes verziójú funkciók előfizetési külön kell. A megfelelő utasításokat kövesse az azokat a funkciókat.

### <a name="sign-up-by-using-azure-cli-20"></a>Jelentkezzen Azure CLI 2.0 használatával

1. A szolgáltatás regisztrálja a szolgáltatót:

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```
    
2. A művelet elvégzéséhez akár 10 percet is igénybe vehet. A állapotának a művelet a következő paranccsal:

    ```cli
    az feature show --name AllowLBPreview --namespace Microsoft.Network
    ```
    
    Folytassa a következő lépés, ha a szolgáltatás regisztrációs állapota "Regisztrált" tér vissza:
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowLBPreview",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
3. Fejezze be az előfizetési előzetes újra az erőforrás-szolgáltató előfizetésének regisztrálása:

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    
### <a name="sign-up-by-using-powershell"></a>Iratkozzon fel a PowerShell használatával

1. A szolgáltatás regisztrálja a szolgáltatót:

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```
    
2. A művelet elvégzéséhez akár 10 percet is igénybe vehet. A állapotának a művelet a következő paranccsal:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

    Folytassa a következő lépés, ha a szolgáltatás regisztrációs állapota "Regisztrált" tér vissza:
   
    ```
    FeatureName      ProviderName        RegistrationState
    -----------      ------------        -----------------
    AllowLBPreview   Microsoft.Network   Registered
    ```
    
3. Fejezze be az előfizetési előzetes újra az erőforrás-szolgáltató előfizetésének regisztrálása:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
 
## <a name="pricing"></a>Díjszabás

Load Balancer Standard Termékváltozat számlázási konfigurált szabályok és a feldolgozott adatok alapul. Nincs díjak sem merülnek fel, a próbaidőszak alatt. További információkért tekintse át a [terheléselosztó](https://aka.ms/lbpreviewpricing) és [nyilvános IP-cím](https://aka.ms/lbpreviewpippricing) lapok árképzési.

Az ügyfelek továbbra is kihasználhatja a Load Balancer alapszintű Termékváltozat díjmentesen előnyeit.

## <a name="limitations"></a>Korlátozások

A következő korlátozások vonatkoznak az előzetes időpontjában alkalmazni, és van változhatnak:

- Load Balancer háttér-példányok nem található a virtuális hálózatok társviszonyban most. Háttér-példányainak ugyanabban a régióban kell lennie.
- Termékváltozat nem változtatható. A meglévő erőforrás Termékváltozata nem módosítható.
- Mindkét termékváltozatok lehet használt különálló virtuális gép, Virtuálisgép-példányok a rendelkezésre állási csoport, vagy a virtuálisgép-méretezési készlet. Virtuális gép kombinációk nem használható mindkét termékváltozatok egyidejűleg. Az SKU vegyesen tartalmazó konfiguráció nem engedélyezett.
- Letiltja a Virtuálisgép-példány (vagy a rendelkezésre állási csoport valamely része) egy belső Load Balancer Standard használó [SNAT kimenő kapcsolatok alapértelmezett](load-balancer-outbound-connections.md). Ez olyan önálló virtuális gép, Virtuálisgép-példány egy rendelkezésre állási csoportban, vagy egy virtuálisgép-méretezési csoport is helyreállíthatja. Kimenő kapcsolatok képesség is állíthatja. Ezeket a funkciókat visszaállításához egyidejűleg rendeljen a Load Balancer szabványos nyilvános vagy nyilvános IP Standard egy példányszintű nyilvános IP-cím, a virtuális gép ugyanazon. A hozzárendelés befejezése után a nyilvános IP-cím, port-színleg SNAT valósul meg újra.
- Virtuálisgép-példányok teljes háttér címkészletet méretezési eléréséhez a rendelkezésre állási készletek csoportosítva lehet szükség. Legfeljebb 150 rendelkezésre állási készletek és önálló virtuális gépek elhelyezhetők legyenek egy háttér-készlet.
- Az IPv6 nem támogatott.
- A rendelkezésre állási zónák a környezetben egy előtér-nem változtatható zonal a zónaredundáns, vagy fordítva. Egy előtér-létrehozása után a zónaredundáns marad zónaredundáns. Egy előtér-létrehozása után a zonal marad zonal.
- A rendelkezésre állási zónák környezetében zonal nyilvános IP-címnek nem lehet áthelyezni egy zónából egy másikra.


## <a name="next-steps"></a>Következő lépések

- További információ [Load Balancer alapvető](load-balancer-overview.md).
- További információ [rendelkezésre állási zónák](../availability-zones/az-overview.md).
- Ismerje meg, azzal kapcsolatban, a másik kulccsal [hálózati lehetőségeket](../networking/networking-overview.md) az Azure-ban.

