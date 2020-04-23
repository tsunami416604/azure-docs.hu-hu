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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304191"
---
# <a name="load-balancer-outbound-rules"></a>Load Balancer kimenő szabályok

Azure Load Balancer kimenő kapcsolatot biztosít egy virtuális hálózattól a bejövő adatok mellett.  A kimenő szabályok egyszerűvé teszik a nyilvános [standard Load Balancer](load-balancer-standard-overview.md)kimenő hálózati címfordításának konfigurálását.  A kimenő kapcsolat teljes deklaratív vezérlése lehetővé teszi az adott igényeknek megfelelő méretezést és finomhangolást.

![Load Balancer kimenő szabályok](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

A kimenő szabályok használata esetén a Load Balancer a következőket teheti: 
- adja meg a kimenő NAT-t a semmiből.
- a meglévő kimenő NAT működésének skálázása és finomhangolása. 

A kimenő szabályok segítségével a következőket vezérelheti:
- mely virtuális gépeket kell lefordítani a nyilvános IP-címekre. 
- a [kimenő SNAT portok](load-balancer-outbound-connections.md#snat) kiosztása.
- a kimenő fordítását biztosító protokollok.
- a kimenő kapcsolat üresjárati időkorlátja (4-120 perc) milyen időtartamra van használatban.
- meg kell-e küldeni a TCP alaphelyzetbe állítását üresjárati időkorláton

A kimenő szabályok kibővítik a [2. forgatókönyvet](load-balancer-outbound-connections.md#lb) a [Kimenő kapcsolatok](load-balancer-outbound-connections.md) című cikkben leírtak szerint, és a forgatókönyv elsőbbsége változatlan marad.

## <a name="outbound-rule"></a>Kimenő szabály

Az összes Load Balancer-szabályhoz hasonlóan a kimenő szabályok ugyanazt a szintaxist követik, mint a terheléselosztás és a bejövő NAT-szabályok:

**frontend**előtér-**Paraméterek** + **háttér-készlete**  + 

Egy kimenő szabály konfigurálja a kimenő NAT-t a _háttér-készlet által azonosított összes virtuális gép_ számára, hogy a rendszer lefordítsa a _felületet_.  A és a _Paraméterek_ további részletes szabályozást biztosítanak a kimenő NAT-algoritmushoz képest.

Az "2018-07-01" API-verzió lehetővé teszi, hogy a kimenő szabályok definíciója a következőképpen legyen strukturálva:

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
>A tényleges kimenő NAT-konfiguráció az összes kimenő szabály és a terheléselosztási szabályok összetett változata. A kimenő szabályok növekményes terheléselosztási szabályok. Tekintse át a [kimenő NAT letiltását egy terheléselosztási szabályhoz](#disablesnat) a kimenő NAT tényleges fordításának kezeléséhez, ha több szabály vonatkozik egy virtuális gépre. A [kimenő SNAT le kell tiltania](#disablesnat) egy olyan Kimenő szabály definiálásakor, amely ugyanazt a nyilvános IP-címet használja terheléselosztási szabályként.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>Kimenő NAT méretezése több IP-címmel

Míg egy kimenő szabály csak egyetlen nyilvános IP-címmel használható, a kimenő szabályok megkönnyítik a kimenő NAT skálázásának konfigurációs terhelését. Több IP-címet is használhat a nagy léptékű forgatókönyvek tervezéséhez, és a kimenő szabályok használatával csökkentheti a [SNAT kimerülésének](load-balancer-outbound-connections.md#snatexhaust) hajlamos mintázatát.  

A frontend által biztosított minden további IP-cím 64 000 ideiglenes portot biztosít Load Balancer számára, SNAT-portként való használatra. Míg a terheléselosztás vagy a bejövő NAT-szabályok egyetlen előtérrel rendelkeznek, a Kimenő szabály kibővíti a előtér-fogalmat, és lehetővé teszi a több előtér-szabály használatát.  A több előtérbeli felülettel rendelkező SNAT a rendelkezésre álló portok mennyisége megszorozva minden nyilvános IP-címmel, és a nagyméretű forgatókönyvek is támogatottak.

Emellett a [nyilvános IP-előtagot](https://aka.ms/lbpublicipprefix) közvetlenül is használhatja egy kimenő szabállyal.  A nyilvános IP-előtag használatával egyszerűbben méretezhető és egyszerűsített, az Azure-környezetből származó folyamatokat tartalmazó lista is elérhető. A Load Balancer erőforráson belül konfigurálhat egy előtérbeli IP-konfigurációt, amely közvetlenül a nyilvános IP-cím előtagra hivatkozik.  Ez lehetővé teszi a nyilvános IP-előtag Load Balancer kizárólagos felügyeletét, és a kimenő kapcsolatok esetében a rendszer automatikusan a nyilvános IP-előtagon belül található összes nyilvános IP-címet fogja használni a kimenő kapcsolatokhoz.  A nyilvános IP-előtag tartományán belüli IP-címek mindegyike IP-címenként 64 000 ideiglenes portot biztosít Load Balancer SNAT-portként való használatra.   

Ha ezt a beállítást használja, nem rendelkezhet a nyilvános IP-előtagból létrehozott egyedi nyilvános IP-cím-erőforrásokkal, mert a kimenő szabálynak teljes mértékben ellenőriznie kell a nyilvános IP-előtagot.  Ha részletesebb szabályozásra van szüksége, hozzon létre egyedi nyilvános IP-cím-erőforrást a nyilvános IP-címről, és rendeljen hozzá több nyilvános IP-címet egy kimenő szabály előtérbeli felületéhez.

### <a name="tune-snat-port-allocation"></a><a name="snatports"></a>SNAT-portok kiosztásának hangolása

A kimenő szabályok használatával beállíthatja az [automatikus SNAT-portok kiosztását a háttérbeli készlet mérete alapján](load-balancer-outbound-connections.md#preallocatedports) , és lefoglalhatja az automatikus SNAT-portok kiosztását.

A következő paraméter használatával 10 000 SNAT-portot foglalhat le virtuális gépenként (hálózati adapter IP-konfigurációja).
 

          "allocatedOutboundPorts": 10000

A kimenő szabályok összes felületének minden nyilvános IP-címe akár 64 000 ideiglenes portot is felhasználhat SNAT-portokként való használatra.  Load Balancer SNAT-portokat foglal le a 8 többszörösében. Ha a 8 értékkel nem osztható értéket ad meg, a rendszer elutasítja a konfigurációs műveletet.  Ha a nyilvános IP-címek száma alapján több SNAT-portot próbál kiosztani, akkor a rendszer elutasítja a konfigurációs műveletet.  Ha például egy virtuális gépenként 10 000 portot foglal le, és a háttérbeli készlet 7 virtuális gépe egyetlen nyilvános IP-címet oszt meg, akkor a rendszer elutasítja a konfigurációt (7 x 10 000 SNAT-port > 64 000 SNAT-portok).  A forgatókönyv engedélyezéséhez több nyilvános IP-címet is hozzáadhat a Kimenő szabály előtérbeli felületéhez.

Az [automatikus SNAT-portok kiosztására a háttérbeli készlet mérete alapján](load-balancer-outbound-connections.md#preallocatedports) , a portok számának 0 értékének megadásával térhet vissza. Ebben az esetben az első 50-es virtuálisgép-példány 1024 portot kap, a 51-100-as virtuálisgép-példányok pedig 512-et kapnak, így a táblázat szerint.

### <a name="control-outbound-flow-idle-timeout"></a><a name="idletimeout"></a>A kimenő forgalom üresjárati időkorlátjának szabályozása

A kimenő szabályok egy konfigurációs paramétert biztosítanak a kimenő folyamat üresjárati időkorlátjának szabályozására, és az alkalmazás igényeinek megfelelően illeszkednek.  A kimenő üresjárati időtúllépések alapértelmezett értéke 4 perc.  A paraméter 4 és 120 közötti értéket fogad el az adott szabálynak megfelelő folyamatok üresjárati időkorlátja számára.

A következő paraméterrel állíthatja be a kimenő üresjárati időkorlátot 1 órára:

          "idleTimeoutInMinutes": 60

### <a name="enable-tcp-reset-on-idle-timeout"></a><a name="tcprst"></a><a name="tcpreset"></a> A TCP alaphelyzetbe állításának engedélyezése üresjárati időkorláton

Load Balancer alapértelmezett viselkedése a folyamat csendes eldobása a kimenő Üresjárati időkorlát elérésekor.  A enableTCPReset paraméterrel előre jelezhető alkalmazások viselkedését engedélyezheti, és szabályozhatja, hogy a kimenő Üresjárati időkorlát időtúllépése esetén a kétirányú TCP-visszaállítást (TCP-t) kell-e küldeni. 

A következő paraméter használatával engedélyezheti a TCP-visszaállítást egy kimenő szabályon:

           "enableTcpReset": true

Tekintse át az [Üresjárat időkorlátjának TCP-visszaállítását](https://aka.ms/lbtcpreset) , beleértve a régió rendelkezésre állását.

### <a name="support-both-tcp-and-udp-transport-protocols-with-a-single-rule"></a><a name="proto"></a>A TCP-és UDP-átviteli protokollok támogatása egyetlen szabállyal

Valószínű, hogy az "összes" lehetőséget szeretné használni a Kimenő szabály átviteli protokollján, de a kimenő szabályt egy adott átviteli protokollra is alkalmazhatja, valamint ha erre van szükség.

A TCP és az UDP protokoll beállításához használja a következő paramétert:

          "protocol": "All"

### <a name="disable-outbound-nat-for-a-load-balancing-rule"></a><a name="disablesnat"></a>Kimenő NAT letiltása terheléselosztási szabályhoz

Ahogy korábban már említettük, a terheléselosztási szabályok biztosítják a kimenő NAT automatikus programozását. Bizonyos esetekben azonban a terheléselosztási szabály segítségével letilthatja a kimenő NAT automatikus programozását, hogy lehetővé tegye a viselkedés szabályozását vagy pontosítását.  A kimenő szabályok olyan forgatókönyvekkel rendelkeznek, amelyek fontosak az automatikus kimenő NAT-programozás leállításához.

Ezt a paramétert kétféleképpen is használhatja:
- A kimenő NAT bejövő IP-címének használata nem kötelező.  A kimenő szabályok növekményes terheléselosztási szabályokkal és ezzel a paraméterrel állíthatók be, a Kimenő szabály a vezérlőben van.
  
- A bejövő és kimenő IP-címek kimenő NAT-paramétereinek hangolása párhuzamosan.  Az automatikus kimenő NAT-programozást le kell tiltani ahhoz, hogy a kimenő szabályok szabályozni tudják a vezérlést.  Ha például meg szeretné változtatni egy SNAT-port lefoglalását is, akkor ezt a paramétert igaz értékre kell állítani.  Ha egy kimenő szabályt próbál használni a bejövő IP-cím paramétereinek újradefiniálásához, és nem adta meg a terheléselosztási szabály kimenő NAT-programozását, akkor a kimenő szabályok konfigurálásának művelete sikertelen lesz.

>[!IMPORTANT]
> A virtuális gép nem rendelkezik kimenő kapcsolattal, ha a paraméter igaz értékre van állítva, és nem rendelkezik kimenő (vagy [példány-szintű nyilvános IP](load-balancer-outbound-connections.md#ilpip) -címmel) a kimenő kapcsolat definiálásához.  A virtuális gép vagy az alkalmazás néhány művelete attól függ, hogy a kimenő kapcsolat elérhető-e. Győződjön meg arról, hogy tisztában van a forgatókönyv függőségeivel, és befolyásolta ennek a változásnak a hatását.

A kimenő SNAT a terheléselosztási szabályban a következő konfigurációs paraméterrel tilthatja le:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

A új választható disableoutboundsnat paraméter értéke false (hamis), ami azt jelenti, hogy a terheléselosztási szabály a terheléselosztási szabály konfigurációjának tükörképe **biztosítja az** automatikus kimenő NAT-t.  

Ha a terheléselosztási szabályban a új választható disableoutboundsnat igaz értékre állítja, a terheléselosztási szabály az egyébként automatikusan kimenő NAT-programozás vezérlését is felszabadítja.  A terheléselosztási szabály eredményeképpen a kimenő SNAT le van tiltva.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Meglévő újbóli felhasználása vagy új háttérbeli készletek definiálása

A kimenő szabályok nem vezetnek be új fogalmat azon virtuális gépek csoportjának definiálásához, amelyekre a szabály vonatkozik.  Ehelyett a rendszer újrahasznosítja a háttér-készlet fogalmát, amely a terheléselosztási szabályokhoz is használatos. Ezzel leegyszerűsítheti a konfigurációt egy meglévő háttérbeli készlet definíciójának újbóli használatával, vagy egy konkrét Kimenő szabály létrehozásával.

## <a name="scenarios"></a>Forgatókönyvek

### <a name="groom-outbound-connections-to-a-specific-set-of-public-ip-addresses"></a><a name="groom"></a>A kifelé irányuló kimenő kapcsolatok egy adott nyilvános IP-címhez

A kifelé irányuló kimenő kapcsolatok esetében egy kimenő szabályt is használhat, hogy a rendszer egy adott nyilvános IP-címről hozzon létre, hogy megkönnyítse az engedélyezési forgatókönyvek használatát.  Ez a forrás nyilvános IP-cím ugyanaz lehet, mint a terheléselosztási szabály által használt terheléselosztási szabályok vagy a nyilvános IP-címek különböző készletei.  

1. [Nyilvános IP-előtag](https://aka.ms/lbpublicipprefix) létrehozása (vagy nyilvános IP-címek nyilvános IP-előtagból)
2. Nyilvános Standard Load Balancer létrehozása
3. Hozza létre a használni kívánt nyilvános IP-előtagra (vagy nyilvános IP-címekre) hivatkozó előtérbeli felületet
4. Felhasználhatja a háttér-készletet, vagy létrehozhat egy háttér-készletet, és elhelyezheti a virtuális gépeket a nyilvános Load Balancer háttérbeli készletéből.
5. Egy kimenő szabály konfigurálása a nyilvános Load Balancer a virtuális gépek kimenő NAT-ának programjának kiprogramozásához a frontendek használatával
   
Ha nem szeretné, hogy a terheléselosztási szabályt a kimenő forgalomhoz használja, [le kell tiltania a kimenő SNAT](#disablesnat) a terheléselosztási szabályban.

### <a name="modify-snat-port-allocation"></a><a name="modifysnat"></a>SNAT-port foglalásának módosítása

A kimenő szabályok használatával beállíthatja az [automatikus SNAT-portok kiosztását a háttérbeli készlet méretétől függően](load-balancer-outbound-connections.md#preallocatedports).

Ha például két virtuális gép egyetlen nyilvános IP-címet oszt ki a kimenő NAT-hoz, akkor érdemes lehet megnövelni az alapértelmezett 1024-as portokból lefoglalt SNAT-portok számát, ha SNAT-kimerülést tapasztal. Minden nyilvános IP-cím legfeljebb 64 000 ideiglenes portot tud elősegíteni.  Ha egyetlen nyilvános IP-címmel rendelkező kimenő szabályt konfigurál, a háttér-készletben lévő virtuális gépekhez összesen 64 000 SNAT-portot oszthat ki.  Két virtuális gép esetében legfeljebb 32 000 SNAT-portot lehet lefoglalni egy kimenő szabállyal (2x 32 000 = 64 000).

Tekintse át a [kimenő kapcsolatokat](load-balancer-outbound-connections.md) és a [SNAT](load-balancer-outbound-connections.md#snat) portok kiosztásának és felhasználásának részleteit.

### <a name="enable-outbound-only"></a><a name="outboundonly"></a>Csak kimenő engedélyezése

Egy nyilvános standard Load Balancer használatával biztosíthatja a kimenő NAT-t a virtuális gépek csoportjai számára. Ebben a forgatókönyvben saját maga is használhat kimenő szabályt anélkül, hogy további szabályokat kellene megadnia.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>Kimenő NAT csak virtuális gépekhez (nincs bejövő)

Definiáljon egy nyilvános standard Load Balancer, helyezze a virtuális gépeket a háttér-készletbe, és állítson be egy kimenő szabályt a kimenő NAT programba, és a kimenő kapcsolatok a megadott nyilvános IP-címről származnak. A nyilvános IP-előtagot is használhatja, amely leegyszerűsíti a kimenő kapcsolatok forrásának listázását.

1. Hozzon létre egy nyilvános standard Load Balancer.
2. Hozzon létre egy háttér-készletet, és helyezze a virtuális gépeket a nyilvános Load Balancer háttér-készletéből.
3. Konfiguráljon egy kimenő szabályt a nyilvános Load Balanceron a kimenő NAT programhoz a virtuális gépek számára.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Kimenő NAT belső standard Load Balancer-forgatókönyvekhez

Belső standard Load Balancer használata esetén a kimenő NAT nem érhető el, amíg explicit módon be nem jelenti a kimenő kapcsolatot. A kimenő kapcsolatokat egy kimenő szabállyal definiálhatja a belső standard Load Balancer mögötti virtuális gépek kimenő kapcsolatának létrehozásához a következő lépésekkel:

1. Hozzon létre egy nyilvános standard Load Balancer.
2. Hozzon létre egy háttér-készletet, és helyezze a virtuális gépeket a nyilvános Load Balancer háttér-készletéből a belső Load Balancer mellett.
3. Konfiguráljon egy kimenő szabályt a nyilvános Load Balanceron a kimenő NAT programhoz a virtuális gépek számára.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>TCP-& UDP-protokollok engedélyezése a kimenő NAT nyilvános standard Load Balancer

- Nyilvános standard Load Balancer használata esetén a megadott automatikus kimenő NAT-programozás megfelel a terheléselosztási szabály átviteli protokolljának.  

   1. Tiltsa le a kimenő SNAT a terheléselosztási szabályban.
   2. Konfiguráljon egy kimenő szabályt ugyanarra a Load Balancer.
   3. Használja újra a virtuális gépek által már használt háttér-készletet.
   4. A Kimenő szabály részeként a "protokoll": "all" érték adható meg.

- Ha csak a bejövő NAT-szabályok vannak használatban, a rendszer nem biztosít kimenő NAT-szabályokat.

   1. Helyezze a virtuális gépeket egy háttérbeli készletbe.
   2. Adjon meg egy vagy több előtér-IP-konfigurációt nyilvános IP-címmel vagy nyilvános IP-előtaggal.
   3. Konfiguráljon egy kimenő szabályt ugyanarra a Load Balancer.
   4. A "protokoll": "all" érték meghatározása a Kimenő szabály részeként

## <a name="limitations"></a>Korlátozások

- A felhasználható ideiglenes portok maximális száma előtér-IP-címenként 64 000.
- A konfigurálható kimenő Üresjárati időkorlát (4 – 120 perc) tartománya (240 – 7200 másodperc).
- A Load Balancer nem támogatja az ICMP protokollt a kimenő NAT-hoz.
- A kimenő szabályok csak a hálózati adapter elsődleges IP-konfigurációjához alkalmazhatók.  Több hálózati adapter is támogatott.

## <a name="next-steps"></a>További lépések

- Tudnivalók a [Kimenő kapcsolatok Load Balancer](load-balancer-outbound-connections.md)használatáról.
- A [standard Load Balancer](load-balancer-standard-overview.md)megismerése.
- Tudnivalók a [kétirányú TCP-visszaállításról üresjárati időkorláton](load-balancer-tcp-reset.md).
- [Állítsa be a kimenő szabályokat az Azure CLI 2,0](configure-load-balancer-outbound-cli.md)-mel.
