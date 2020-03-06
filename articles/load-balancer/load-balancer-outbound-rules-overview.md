---
title: Kimenő szabályok – Azure Load Balancer
description: Ezzel a képzési útvonallal megkezdheti a kimenő hálózati címfordítások meghatározását a kimenő szabályok használatával.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 7/17/2019
ms.author: allensu
ms.openlocfilehash: d419c213b3bcfef3631d68eb9d4cb485291bed31
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304191"
---
# <a name="load-balancer-outbound-rules"></a>A terheléselosztó kimenő szabályok

Az Azure Load Balancer bejövő mellett a virtuális hálózatról kimenő kapcsolatot biztosít.  A kimenő szabályok egyszerűvé teszik a nyilvános [standard Load Balancer](load-balancer-standard-overview.md)kimenő hálózati címfordításának konfigurálását.  Kimenő kapcsolat skálázása és finomhangolása a képességgel, amellyel igény szerinti teljes deklaratív felett van.

![A terheléselosztó kimenő szabályok](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Kimenő szabályok is használhatja, hogy a terheléselosztó: 
- Adja meg a kimenő NAT-előzmények.
- skálázása és finomhangolása a meglévő kimenő NAT viselkedése 

Kimenő szabályok lehetővé teszik a vezérlőhöz:
- virtuális gépek, amelyek nyilvános IP-címek kell fordítani. 
- a [kimenő SNAT portok](load-balancer-outbound-connections.md#snat) kiosztása.
- mely protokollok, adja meg a kimenő fordítását.
- a kimenő kapcsolat üresjárati időkorlátja (4-120 perc) milyen időtartamra van használatban.
- meg kell-e küldeni a TCP alaphelyzetbe állítását üresjárati időkorláton

A kimenő szabályok kibővítik a [2. forgatókönyvet](load-balancer-outbound-connections.md#lb) a [Kimenő kapcsolatok](load-balancer-outbound-connections.md) című cikkben leírtak szerint, és a forgatókönyv elsőbbsége változatlan marad.

## <a name="outbound-rule"></a>Kimenő szabály

Kimenő szabályok összes Load Balancer-szabályt, például hajtsa végre a terheléselosztási és bejövő NAT-szabályok megegyező ismerős szintaxissal:

**frontend** + **Paraméterek** + **háttér-készlet**

Egy kimenő szabály konfigurálja a kimenő NAT-t a _háttér-készlet által azonosított összes virtuális gép_ számára, hogy a rendszer lefordítsa a _felületet_.  A és a _Paraméterek_ további részletes szabályozást biztosítanak a kimenő NAT-algoritmushoz képest.

API-verzió "2018-07-01" lehetővé teszi egy kimenő szabály definícióját strukturált az alábbiak szerint:

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>A hatékony kimenő NAT-konfigurációt az összes kimenő szabályok és terheléselosztási szabályok összetett. Terheléselosztási szabályok kimenő szabályok hozzáadódnak. Tekintse át a [kimenő NAT letiltását egy terheléselosztási szabályhoz](#disablesnat) a kimenő NAT tényleges fordításának kezeléséhez, ha több szabály vonatkozik egy virtuális gépre. A [kimenő SNAT le kell tiltania](#disablesnat) egy olyan Kimenő szabály definiálásakor, amely ugyanazt a nyilvános IP-címet használja terheléselosztási szabályként.

### <a name="scale"></a>Kimenő NAT méretezése több IP-címmel

Kimenő szabály csak egyetlen nyilvános IP-cím is használható, amíg kimenő szabályok terhei konfigurációs kimenő NAT méretezése Több IP-címet is használhat a nagy léptékű forgatókönyvek tervezéséhez, és a kimenő szabályok használatával csökkentheti a [SNAT kimerülésének](load-balancer-outbound-connections.md#snatexhaust) hajlamos mintázatát.  

A frontend által biztosított minden további IP-cím 64 000 ideiglenes portot biztosít Load Balancer számára, SNAT-portként való használatra. Míg a terheléselosztás és a bejövő NAT-szabályokat egyetlen előtér, a kimenő szabály kibővíti az előtér-formátumban, és lehetővé teszi, hogy több előtérrendszer ugyanabban a szabályban.  Több előtérrendszer szabályonként meg kell szorozni a mennyiséget, a rendelkezésre álló SNAT portok minden egyes nyilvános IP-címmel, és nagy forgatókönyveket támogatja.

Emellett a [nyilvános IP-előtagot](https://aka.ms/lbpublicipprefix) közvetlenül is használhatja egy kimenő szabállyal.  Előtag egyszerűbb méretezést és egyszerűsített engedélyezési lista forgalom származik az az Azure-alapú nyilvános IP-cím használatával biztosít. Előtérbeli IP-konfiguráció a terheléselosztó erőforrás közvetlenül hivatkozni egy nyilvános IP-címelőtag belül is beállíthatja.  Ez lehetővé teszi, hogy a terheléselosztó a nyilvános IP-előtag kizárólagos felett, és a kimenő szabály automatikusan fogja használni az összes nyilvános IP-címeire a nyilvános IP-előtag a kimenő kapcsolatok számára.  A nyilvános IP-előtag tartományán belüli IP-címek mindegyike IP-címenként 64 000 ideiglenes portot biztosít Load Balancer SNAT-portként való használatra.   

Nem lehet egyedi nyilvános IP-cím erőforrások a nyilvános IP-előtag alapján létrehozott, ez a beállítás használatakor, mert az kimenő szabályt a nyilvános IP-előtag teljes vezérlési jogosultsággal kell rendelkeznie.  További részletes finomhangolás van szüksége, ha az egyes nyilvános IP-cím erőforrás létrehozása a nyilvános IP-előtag, és több nyilvános IP-címek külön-külön hozzárendelése az előtér egy kimenő szabály.

### <a name="snatports"></a>SNAT-portok kiosztásának hangolása

A kimenő szabályok használatával beállíthatja az [automatikus SNAT-portok kiosztását a háttérbeli készlet mérete alapján](load-balancer-outbound-connections.md#preallocatedports) , és lefoglalhatja az automatikus SNAT-portok kiosztását.

Használja a következő paramétert 10 000 SNAT lefoglalni a portokat a virtuális géphez (hálózati adapter IP-konfiguráció).
 

          "allocatedOutboundPorts": 10000

A kimenő szabályok összes felületének minden nyilvános IP-címe akár 64 000 ideiglenes portot is felhasználhat SNAT-portokként való használatra.  Load Balancer SNAT portot többszörösei 8 foglal le. Ha egy érték nem osztható 8, a konfigurációs műveletet elutasítva.  Ha megpróbálja lefoglalni a portokat további SNAT mint amennyi rendelkezésre áll a nyilvános IP-címek száma alapján, a rendszer elutasítja a konfigurációs műveletet.  Ha például egy virtuális gépenként 10 000 portot foglal le, és a háttérbeli készlet 7 virtuális gépe egyetlen nyilvános IP-címet oszt meg, akkor a rendszer elutasítja a konfigurációt (7 x 10 000 SNAT-port > 64 000 SNAT-portok).  Több nyilvános IP-címeket adhat hozzá az előtér a kimenő szabály engedélyezése a forgatókönyvet.

Az [automatikus SNAT-portok kiosztására a háttérbeli készlet mérete alapján](load-balancer-outbound-connections.md#preallocatedports) , a portok számának 0 értékének megadásával térhet vissza. Ebben az esetben az első 50-es virtuálisgép-példány 1024 portot kap, a 51-100-as virtuálisgép-példányok pedig 512-et kapnak, így a táblázat szerint.

### <a name="idletimeout"></a>A kimenő forgalom üresjárati időkorlátjának szabályozása

Kimenő szabályok adjon meg egy konfigurációs paraméter szabályozza a kimenő folyam üresjárati időkorlát és egyezteti az alkalmazás igényeinek.  4 perces üresjárati időtúllépés kimenő alapértelmezés szerint.  A paraméter 4 és 120 közötti értéket fogad el az adott szabálynak megfelelő folyamatok üresjárati időkorlátja számára.

A következő paraméter segítségével állítsa be a kimenő üresjárati időkorlát 1 óra:

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a><a name="tcpreset"></a> A TCP alaphelyzetbe állításának engedélyezése üresjárati időkorláton

Load Balancer alapértelmezett viselkedését, hogy csendes eldobni a flow, a kimenő üresjárati időkorlátot elérésekor.  A enableTCPReset paraméterrel, engedélyezheti egy kiszámíthatóbb alkalmazások viselkedését, és szabályozhatja, hogy elküldik-e kétirányú TCP (TCP ÜZE) alaphelyzetbe állítása, az időkorlát kimenő üresjárati időtúllépés. 

A következő paraméter használatával engedélyezze a TCP alaphelyzetbe állítása a kimenő szabály:

           "enableTcpReset": true

Tekintse át az [Üresjárat időkorlátjának TCP-visszaállítását](https://aka.ms/lbtcpreset) , beleértve a régió rendelkezésre állását.

### <a name="proto"></a>A TCP-és UDP-átviteli protokollok támogatása egyetlen szabállyal

Valószínűleg érdemes használni az átviteli protokoll a kimenő szabály "Összes", de is alkalmazhat a kimenő szabály egy adott átviteli protokollon, valamint ha ehhez szükség van.

A következő paraméter segítségével állítsa be a protokoll TCP és UDP:

          "protocol": "All"

### <a name="disablesnat"></a>Kimenő NAT letiltása terheléselosztási szabályhoz

Ahogy korábban is hangsúlyoztuk, a terheléselosztási szabályok automatikus programozási kimenő NAT megadása Azonban bizonyos forgatókönyvek előnyeit, vagy szükséges, hogy a terheléselosztási szabály lehetővé teszi a vezérlő vagy finomíthatja a viselkedés szerint le van tiltva az automatikus programozását kimenő NAT.  Kimenő szabályok, fontos, hogy állítsa le az automatikus kimenő NAT-programozási-forgatókönyvekkel rendelkezik.

Ez a paraméter két módon használható:
- Kimenő NAT bejövő IP-címet használja a választható letiltása  Kimenő szabályok hozzáadódnak terheléselosztási szabályok és a paraméterkészletet, a kimenő szabály vezérlőelemben.
  
- Finomhangolja a kimenő NAT-paraméterek egyszerre használni a bejövő és kimenő IP-cím.  Az automatikus kimenő NAT-programozási engedélyezéséhez, hogy átvegye az irányítást az kimenő szabályt le kell tiltani.  Például ahhoz, hogy az SNAT portkiosztással cím is módosíthatja a használt bejövő, állítson be ez a paraméter igaz.  Ha használja az kimenő szabályt definiálja újra megpróbálja a paramétereket is használt IP-cím bejövő és kimenő NAT-programozás a terheléselosztási szabály még nem nyilvánosságra, a kimenő szabály konfigurálása sikertelen lesz.

>[!IMPORTANT]
> A virtuális gép nem rendelkezik kimenő kapcsolattal, ha a paraméter igaz értékre van állítva, és nem rendelkezik kimenő (vagy [példány-szintű nyilvános IP](load-balancer-outbound-connections.md#ilpip) -címmel) a kimenő kapcsolat definiálásához.  Egyes műveletek a virtuális gép vagy az alkalmazás elérhető kimenő kapcsolatok kellene függhet. Ellenőrizze, hogy a forgatókönyv a függőségek megértéséhez, és e módosítás hatásának kell figyelembe venni.

A terheléselosztási szabály a konfigurációs paraméter a kimenő SNAT letilthatja:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

A új választható disableoutboundsnat paraméter értéke false (hamis), ami azt jelenti, hogy a terheléselosztási szabály a terheléselosztási szabály konfigurációjának tükörképe **biztosítja az** automatikus kimenő NAT-t.  

Ha új választható disableOutboundSnat igaz értékre a terheléselosztási szabály, a terheléselosztási szabály kiadja az ellenkező esetben az automatikus kimenő NAT-programozási irányítását.  A terheléselosztási szabály miatt kimenő SNAT le van tiltva.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Újból felhasználhatja a meglévő vagy új háttérkészletek definiálása

Kimenő szabályok vezet be egy új fogalom, amelyre a szabály vonatkozzon a csoportok definiálása.  Ehelyett azokat újból felhasználhatja a háttérkészlet, a terheléselosztási szabályok is használt fogalmát. Ezzel a konfiguráció újbóli használata egy meglévő háttérkiszolgáló-készlet definíciója, vagy hozzon létre egyet a kifejezetten a kimenő szabály egyszerűsítése érdekében.

## <a name="scenarios"></a>Forgatókönyvek

### <a name="groom"></a>A kifelé irányuló kimenő kapcsolatok egy adott nyilvános IP-címhez

Kimenő szabály segítségével karcsúsítása úgy tűnik, hogy az engedélyezési forgatókönyvek megkönnyítése érdekében a nyilvános IP-címek meghatározott készletének származik a kimenő kapcsolatokat.  A nyilvános IP-forráscím lehet ugyanaz, mint egy terheléselosztási szabályt, vagy nyilvános IP-Címek külön készletét, mint a terheléselosztási szabály által használt címek.  

1. [Nyilvános IP-előtag](https://aka.ms/lbpublicipprefix) létrehozása (vagy nyilvános IP-címek nyilvános IP-előtagból)
2. Nyilvános Standard Load Balancer létrehozása
3. Hozzon létre az előtérrendszer hivatkozik a nyilvános IP-előtag (vagy nyilvános IP-címek) kívánja-e használni
4. Újból felhasználhatja a háttérkészlet és a egy nyilvános terheléselosztó háttérkészletének helyezi a virtuális gépek és háttérkészlet létrehozása
5. A nyilvános terheléselosztó kimenő NAT program ezeket az előtérrendszer használó virtuális gépek kimenő szabály konfigurálása
   
Ha nem szeretné, hogy a terheléselosztási szabályt a kimenő forgalomhoz használja, [le kell tiltania a kimenő SNAT](#disablesnat) a terheléselosztási szabályban.

### <a name="modifysnat"></a>SNAT-port foglalásának módosítása

A kimenő szabályok használatával beállíthatja az [automatikus SNAT-portok kiosztását a háttérbeli készlet méretétől függően](load-balancer-outbound-connections.md#preallocatedports).

Például ha két virtuális gépet, egyetlen nyilvános IP-cím megosztása kimenő NAT-hoz, érdemes foglalja le az alapértelmezett 1024 portokat, ha SNAT Erőforrásfogyás SNAT portok számának növeléséhez. Minden nyilvános IP-cím legfeljebb 64 000 ideiglenes portot tud elősegíteni.  Ha egyetlen nyilvános IP-címmel rendelkező kimenő szabályt konfigurál, a háttér-készletben lévő virtuális gépekhez összesen 64 000 SNAT-portot oszthat ki.  Két virtuális gép esetében legfeljebb 32 000 SNAT-portot lehet lefoglalni egy kimenő szabállyal (2x 32 000 = 64 000).

Tekintse át a [kimenő kapcsolatokat](load-balancer-outbound-connections.md) és a [SNAT](load-balancer-outbound-connections.md#snat) portok kiosztásának és felhasználásának részleteit.

### <a name="outboundonly"></a>Csak kimenő engedélyezése

Nyilvános Standard Load Balancer használatával adja meg a kimenő NAT virtuális gépek számára. Ebben a forgatókönyvben az kimenő szabályt használhatja önmagában, nincs szükség további szabályokat.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>Kimenő NAT-beli virtuális gépek csak (nincs bejövő)

Nyilvános Standard Load Balancer meghatározása, a virtuális gépeket helyezi a háttérkészlet és program kimenő NAT és a kimenő kapcsolatokat származnak, egy adott nyilvános IP-cím karcsúsítása kimenő szabály konfigurálása. Is használhatja a nyilvános IP-előtag egyszerűsítése engedélyezési-lista kimenő kapcsolatok forrását.

1. Nyilvános Standard Load Balancer létrehozása.
2. Hozzon létre egy háttérkészlet, és a virtuális gépeket helyezzen egy nyilvános terheléselosztó háttérkészletének.
3. A nyilvános terheléselosztón programhoz kimenő NAT esetében ezek a virtuális gépek kimenő szabály konfigurálása.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Kimenő NAT-forgatókönyvekhez. a Standard Load Balancer belső.

Egy belső Standard Load Balancer használatakor a kimenő NAT nem áll rendelkezésre, amíg a kimenő kapcsolat explicit módon deklarálni. Kimenő kapcsolat kimenő kapcsolat létrehozásához az alábbi lépéseket egy belső Standard terheléselosztó mögötti virtuális gépek kimenő szabály használatával adhatja meg:

1. Nyilvános Standard Load Balancer létrehozása.
2. Hozzon létre egy háttérkészlet és helyezi a virtuális gépek mellett a belső terheléselosztó a nyilvános terheléselosztó háttérkészlet.
3. A nyilvános terheléselosztón programhoz kimenő NAT esetében ezek a virtuális gépek kimenő szabály konfigurálása.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>A kimenő NAT nyilvános Standard Load Balancer a TCP és UDP egyaránt protokollok engedélyezése

- Nyilvános Standard Load Balancer használatakor a megadott automatikus kimenő NAT programozási megfelel az átviteli protokoll a terheléselosztási szabály.  

   1. Tiltsa le a terheléselosztási szabály a kimenő SNAT.
   2. Konfigurálja az kimenő szabályt az azonos terheléselosztóhoz.
   3. Újból felhasználhatja a háttérkészlet, a virtuális gépek által már használt.
   4. Adja meg a "protocol": "All", a kimenő szabály részeként.

- Csak bejövő NAT-szabályok használata esetén nincs kimenő NAT van megadva.

   1. A háttérkészlet virtuális gépeket helyezni.
   2. Egy vagy több előtérbeli IP-konfiguráció a nyilvános IP-cím vagy nyilvános IP-előtag megadása.
   3. Konfigurálja az kimenő szabályt az azonos terheléselosztóhoz.
   4. Adja meg a "protocol": "All", a kimenő szabály részeként

## <a name="limitations"></a>Korlátozások

- A felhasználható ideiglenes portok maximális száma előtér-IP-címenként 64 000.
- A konfigurálható kimenő Üresjárati időkorlát (4 – 120 perc) tartománya (240 – 7200 másodperc).
- Terheléselosztó nem támogatja a ICMP kimenő helyezkedik el.
- A kimenő szabályok csak a hálózati adapter elsődleges IP-konfigurációjához alkalmazhatók.  Több hálózati adapter is támogatott.

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [Kimenő kapcsolatok Load Balancer](load-balancer-outbound-connections.md)használatáról.
- A [standard Load Balancer](load-balancer-standard-overview.md)megismerése.
- Tudnivalók a [kétirányú TCP-visszaállításról üresjárati időkorláton](load-balancer-tcp-reset.md).
- [Állítsa be a kimenő szabályokat az Azure CLI 2,0](configure-load-balancer-outbound-cli.md)-mel.
