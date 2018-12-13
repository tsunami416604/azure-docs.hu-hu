---
title: Az Azure Load Balancerben kimenő szabályok
titlesuffix: Azure Load Balancer
description: Kimenő szabályok használatával határozza meg a kimenő hálózati cím fordítása
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2018
ms.author: kumud
ms.openlocfilehash: 3848e2caefbc8fdfb30f36272f1b13e120312a7c
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185018"
---
# <a name="load-balancer-outbound-rules"></a>A terheléselosztó kimenő szabályok

Az Azure Load Balancer bejövő mellett a virtuális hálózatról kimenő kapcsolatot biztosít.  Kimenő szabályok révén egyszerűen konfigurálása nyilvános [Standard Load Balancer](load-balancer-standard-overview.md)a kimenő hálózati címfordítást.  Kimenő kapcsolat skálázása és finomhangolása a képességgel, amellyel igény szerinti teljes deklaratív felett van.

![A terheléselosztó kimenő szabályok](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Kimenő szabályok is használhatja, hogy a terheléselosztó: 
- Adja meg a kimenő NAT-előzmények.
- skálázása és finomhangolása a meglévő kimenő NAT viselkedése 

Kimenő szabályok lehetővé teszik a vezérlőhöz:
- virtuális gépek, amelyek nyilvános IP-címek kell fordítani. 
- hogyan [kimenő SNAT-portok](load-balancer-outbound-connections.md#snat) kell kiosztani.
- mely protokollok, adja meg a kimenő fordítását.
- milyen kimenő kapcsolat üresjárati időkorlát használandó időtartama.
- e elküldeni a egy új TCP üresjárati időkorlát (nyilvános előzetes verzió). 

Bontsa ki a kimenő szabályok [2. forgatókönyv](load-balancer-outbound-connections.md#lb) a leírt a [kimenő kapcsolatok](load-balancer-outbound-connections.md) marad a cikk és a forgatókönyv elsőbbséget-van.

## <a name="outbound-rule"></a>Kimenő szabály

Kimenő szabályok összes Load Balancer-szabályt, például hajtsa végre a terheléselosztási és bejövő NAT-szabályok megegyező ismerős szintaxissal:

**előtérbeli** + **paraméterek** + **háttérkészlet**

Az kimenő szabályt konfigurálja a kimenő NAT _háttérkészlet által azonosított összes virtuális gép_ fordítandó a _előtérbeli_.  És _paraméterek_ adja meg a további szabályozásához a kimenő NAT-algoritmus.

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
>A hatékony kimenő NAT-konfigurációt az összes kimenő szabályok és terheléselosztási szabályok összetett. Terheléselosztási szabályok kimenő szabályok hozzáadódnak. Felülvizsgálat [kimenő NAT a terheléselosztási szabály letiltása](#disablesnat) a hatékony kimenő NAT-fordítás felügyeletét, amikor egy virtuális gép több szabályok érvényesek lesznek. Meg kell [tiltsa le a kimenő SNAT](#disablesnat) azonos nyilvános IP-címet használó terheléselosztási szabály, kimenő szabály meghatározásakor.

### <a name="scale"></a> Több IP-címmel rendelkező méretezési csoport a kimenő NAT

Kimenő szabály csak egyetlen nyilvános IP-cím is használható, amíg kimenő szabályok terhei konfigurációs kimenő NAT méretezése Több IP-cím használatával nagy méretű forgatókönyvek tervezése és a kimenő szabályok használatával beállíthatja csökkentése [SNAT Erőforrásfogyás](load-balancer-outbound-connections.md#snatexhaust) rejtő mintákat.  

Minden további IP-cím, egy előtérbeli által biztosított 51,200 elmúló port SNAT portokat használja, hogy a terheléselosztó számára biztosít. Míg a terheléselosztás és a bejövő NAT-szabályokat egyetlen előtér, a kimenő szabály kibővíti az előtér-formátumban, és lehetővé teszi, hogy több előtérrendszer ugyanabban a szabályban.  Több előtérrendszer szabályonként meg kell szorozni a mennyiséget, a rendelkezésre álló SNAT portok minden egyes nyilvános IP-címmel, és nagy forgatókönyveket támogatja.

Ezenkívül használhatja a [nyilvános IP-előtag](https://aka.ms/lbpublicipprefix) közvetlenül a kimenő szabály.  Előtag egyszerűbb méretezést és egyszerűsített engedélyezési lista forgalom származik az az Azure-alapú nyilvános IP-cím használatával biztosít. Előtérbeli IP-konfiguráció a terheléselosztó erőforrás közvetlenül hivatkozni egy nyilvános IP-címelőtag belül is beállíthatja.  Ez lehetővé teszi, hogy a terheléselosztó a nyilvános IP-előtag kizárólagos felett, és a kimenő szabály automatikusan fogja használni az összes nyilvános IP-címeire a nyilvános IP-előtag a kimenő kapcsolatok számára.  Minden egyes nyilvános IP-előtag a tartományon belüli IP-címet adja meg az SNAT portokat használja, hogy a terheléselosztó IP-címenként 51,200 elmúló port.   

Nem lehet egyedi nyilvános IP-cím erőforrások a nyilvános IP-előtag alapján létrehozott, ez a beállítás használatakor, mert az kimenő szabályt a nyilvános IP-előtag teljes vezérlési jogosultsággal kell rendelkeznie.  További részletes finomhangolás van szüksége, ha az egyes nyilvános IP-cím erőforrás létrehozása a nyilvános IP-előtag, és több nyilvános IP-címek külön-külön hozzárendelése az előtér egy kimenő szabály.

### <a name="snatports"></a> SNAT portkiosztással hangolása

Kimenő szabályok használatával beállíthatja finomhangolása a [háttérkiszolgáló-készlet mérete alapján automatikus SNAT portkiosztással](load-balancer-outbound-connections.md#preallocatedports) és foglaljon le több vagy kevesebb, mint az automatikus SNAT portkiosztással biztosít.

Használja a következő paramétert 10 000 SNAT lefoglalni a portokat a virtuális géphez (hálózati adapter IP-konfiguráció).
 

          "allocatedOutboundPorts": 10000

Minden egyes nyilvános IP-címét az kimenő szabályt, minden előtérrendszer, SNAT portok járul hozzá akár 51,200 elmúló port használatra.  Load Balancer SNAT portot többszörösei 8 foglal le. Ha egy érték nem osztható 8, a konfigurációs műveletet elutasítva.  Ha megpróbálja lefoglalni a portokat további SNAT mint amennyi rendelkezésre áll a nyilvános IP-címek száma alapján, a rendszer elutasítja a konfigurációs műveletet.  Például ha 10 000 portok száma a virtuális gép és a 7 virtuális gép foglal le a háttérrendszernek a készlet egyetlen nyilvános IP-cím megosztásához, a konfiguráció visszautasított (7 x 10,0000 SNAT portok > 51,200 SNAT portok).  Több nyilvános IP-címeket adhat hozzá az előtér a kimenő szabály engedélyezése a forgatókönyvet.

Térhet vissza a [háttérkiszolgáló-készlet mérete alapján automatikus SNAT portkiosztással](load-balancer-outbound-connections.md#preallocatedports) 0-portok számának megadásával.

### <a name="idletimeout"></a> Ellenőrző kimenő folyam üresjárat időkorlátja

Kimenő szabályok adjon meg egy konfigurációs paraméter szabályozza a kimenő folyam üresjárati időkorlát és egyezteti az alkalmazás igényeinek.  4 perces üresjárati időtúllépés kimenő alapértelmezés szerint.  A paraméter egy adott Ez a szabály megfelelő folyamatok az üresjárati időkorlát percben 66 és 4 közötti értéket fogad el.

A következő paraméter segítségével állítsa be a kimenő üresjárati időkorlát 1 óra:

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a> <a name="tcpreset"></a> Engedélyezze a TCP alaphelyzetbe állítása a üresjárat időkorlátja (előzetes verzió)

Load Balancer alapértelmezett viselkedését, hogy csendes eldobni a flow, a kimenő üresjárati időkorlátot elérésekor.  A enableTCPReset paraméterrel, engedélyezheti egy kiszámíthatóbb alkalmazások viselkedését, és szabályozhatja, hogy elküldik-e kétirányú TCP (TCP ÜZE) alaphelyzetbe állítása, az időkorlát kimenő üresjárati időtúllépés. 

A következő paraméter használatával engedélyezze a TCP alaphelyzetbe állítása a kimenő szabály:

           "enableTcpReset": true

Felülvizsgálat [TCP üresjárati időkorlát (előzetes verzió) a alaphelyzetbe](https://aka.ms/lbtcpreset) részletekért, többek között a régiók rendelkezésre állása.

### <a name="proto"></a> Protokoly TCP és UDP egyaránt átviteli egyetlen szabállyal

Valószínűleg érdemes használni az átviteli protokoll a kimenő szabály "Összes", de is alkalmazhat a kimenő szabály egy adott átviteli protokollon, valamint ha ehhez szükség van.

A következő paraméter segítségével állítsa be a protokoll TCP és UDP:

          "protocol": "All"

### <a name="disablesnat"></a> Kimenő NAT a terheléselosztási szabály letiltása

Ahogy korábban is hangsúlyoztuk, a terheléselosztási szabályok automatikus programozási kimenő NAT megadása Azonban bizonyos forgatókönyvek előnyeit, vagy szükséges, hogy a terheléselosztási szabály lehetővé teszi a vezérlő vagy finomíthatja a viselkedés szerint le van tiltva az automatikus programozását kimenő NAT.  Kimenő szabályok, fontos, hogy állítsa le az automatikus kimenő NAT-programozási-forgatókönyvekkel rendelkezik.

Ez a paraméter két módon használható:
- Kimenő NAT bejövő IP-címet használja a választható letiltása  Kimenő szabályok hozzáadódnak terheléselosztási szabályok és a paraméterkészletet, a kimenő szabály vezérlőelemben.
  
- Finomhangolja a kimenő NAT-paraméterek egyszerre használni a bejövő és kimenő IP-cím.  Az automatikus kimenő NAT-programozási engedélyezéséhez, hogy átvegye az irányítást az kimenő szabályt le kell tiltani.  Például ahhoz, hogy az SNAT portkiosztással cím is módosíthatja a használt bejövő, állítson be ez a paraméter igaz.  Ha használja az kimenő szabályt definiálja újra megpróbálja a paramétereket is használt IP-cím bejövő és kimenő NAT-programozás a terheléselosztási szabály még nem nyilvánosságra, a kimenő szabály konfigurálása sikertelen lesz.

>[!IMPORTANT]
> A virtuális gép nem rendelkezik kimenő kapcsolat, ha ez a paraméter értéke true, és nem rendelkezik az kimenő szabályt (vagy [példányszintű nyilvános IP-forgatókönyv](load-balancer-outbound-connections.md#ilpip) kimenő kapcsolatok meghatározásához.  Egyes műveletek a virtuális gép vagy az alkalmazás elérhető kimenő kapcsolatok kellene függhet. Ellenőrizze, hogy a forgatókönyv a függőségek megértéséhez, és e módosítás hatásának kell figyelembe venni.

A terheléselosztási szabály a konfigurációs paraméter a kimenő SNAT letilthatja:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

Az új választható disableOutboundSNAT paraméter alapértelmezett értéke false, ami azt jelenti, hogy a terheléselosztási szabály **does** automatikus kimenő NAT biztosít a terheléselosztási szabály konfigurálása egy tükrözött lemezképét.  

Ha új választható disableOutboundSnat igaz értékre a terheléselosztási szabály, a terheléselosztási szabály kiadja az ellenkező esetben az automatikus kimenő NAT-programozási irányítását.  A terheléselosztási szabály miatt kimenő SNAT le van tiltva.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Újból felhasználhatja a meglévő vagy új háttérkészletek definiálása

Kimenő szabályok vezet be egy új fogalom, amelyre a szabály vonatkozzon a csoportok definiálása.  Ehelyett azokat újból felhasználhatja a háttérkészlet, a terheléselosztási szabályok is használt fogalmát. Ezzel a konfiguráció újbóli használata egy meglévő háttérkiszolgáló-készlet definíciója, vagy hozzon létre egyet a kifejezetten a kimenő szabály egyszerűsítése érdekében.

## <a name="scenarios"></a>Forgatókönyvek

### <a name="groom"></a> Nyilvános IP-címek meghatározott készletének a kimenő kapcsolatokat karcsúsítása

Kimenő szabály segítségével karcsúsítása úgy tűnik, hogy az engedélyezési forgatókönyvek megkönnyítése érdekében a nyilvános IP-címek meghatározott készletének származik a kimenő kapcsolatokat.  A nyilvános IP-forráscím lehet ugyanaz, mint egy terheléselosztási szabályt, vagy nyilvános IP-Címek külön készletét, mint a terheléselosztási szabály által használt címek.  

1. Hozzon létre [nyilvános IP-előtag](https://aka.ms/lbpublicipprefix) (vagy nyilvános IP-címeket a nyilvános IP-előtag)
2. Nyilvános Standard Load Balancer létrehozása
3. Hozzon létre az előtérrendszer hivatkozik a nyilvános IP-előtag (vagy nyilvános IP-címek) kívánja-e használni
4. Újból felhasználhatja a háttérkészlet és a egy nyilvános terheléselosztó háttérkészletének helyezi a virtuális gépek és háttérkészlet létrehozása
5. A nyilvános terheléselosztó kimenő NAT program ezeket az előtérrendszer használó virtuális gépek kimenő szabály konfigurálása
   
Ha nem szeretné a terheléselosztási szabály használható kimenő kell [tiltsa le a kimenő SNAT](#disablesnat) a terheléselosztási szabály.

### <a name="modifysnat"></a> Módosítsa a Címfordítást portkiosztással

Kimenő szabályok használatával beállíthatja finomhangolása a [háttérkiszolgáló-készlet mérete alapján automatikus SNAT portkiosztással](load-balancer-outbound-connections.md#preallocatedports).

Például ha két virtuális gépet, egyetlen nyilvános IP-cím megosztása kimenő NAT-hoz, érdemes foglalja le az alapértelmezett 1024 portokat, ha SNAT Erőforrásfogyás SNAT portok számának növeléséhez. Minden egyes nyilvános IP-cím legfeljebb 51,200 elmúló port közreműködhet.  Ha konfigurál egy egyetlen nyilvános IP-cím előtérbeli az kimenő szabályt, a háttérkészletben lévő virtuális gépek 51,200 SNAT portok összesen juttathatja el.  A két virtuális gépek esetében legfeljebb 25 600 SNAT port a kimenő szabály (2 x 25,600 = 51,200) lehet hozzárendelni.

Felülvizsgálat [kimenő kapcsolatok](load-balancer-outbound-connections.md) és a részleteket az [SNAT](load-balancer-outbound-connections.md#snat) portok vannak lefoglalva, és használja.

### <a name="outboundonly"></a> Csak kimenő engedélyezése

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
   4. Adja meg a "protocol": "All" a kimenő szabály részeként.

- Csak bejövő NAT-szabályok használata esetén nincs kimenő NAT van megadva.

   1. A háttérkészlet virtuális gépeket helyezni.
   2. Egy vagy több előtérbeli IP-konfiguráció a nyilvános IP-cím vagy nyilvános IP-előtag megadása.
   3. Konfigurálja az kimenő szabályt az azonos terheléselosztóhoz.
   4. Adja meg a "protocol": A kimenő szabály részeként "All"

## <a name="limitations"></a>Korlátozások

- Az előtérbeli IP-címenként használható elmúló port maximális száma: 51,200.
- A konfigurálható kimenő üresjárati időkorlát tartománya 4-66 perc (240 a 4000-es másodperc).
- Terheléselosztó nem támogatja a ICMP kimenő helyezkedik el.
- Portál konfigurálása, illetve megtekintheti a kimenő szabályok nem használható.  Ehelyett használja a sablonok, REST API-t, Az CLI 2.0-val vagy PowerShell.

## <a name="next-steps"></a>További lépések

- A megismerése [terheléselosztó kimenő kapcsolatok](load-balancer-outbound-connections.md).
- Ismerje meg [a Standard Load Balancer](load-balancer-standard-overview.md).
- Ismerje meg [kétirányú alaphelyzetbe állítása a TCP üresjárati időtúllépés miatt](load-balancer-tcp-reset.md).
- [Kimenő szabályok konfigurálása az Azure CLI 2.0](configure-load-balancer-outbound-cli.md).
