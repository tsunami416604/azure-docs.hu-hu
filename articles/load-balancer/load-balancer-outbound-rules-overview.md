---
title: Kimenő szabályok – Azure-terheléselosztó
description: Ezzel a tanulási útvonallal a kimenő szabályok használatával definiálhatja a kimenő hálózati címfordításokat.
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
# <a name="load-balancer-outbound-rules"></a>Terheléselosztó kimenő szabályai

Az Azure Load Balancer a bejövő mellett kimenő kapcsolatot is biztosít egy virtuális hálózatról.  A kimenő szabályok egyszerűvé teszik a nyilvános [standard terheléselosztó](load-balancer-standard-overview.md)kimenő hálózati címfordításának konfigurálását.  Teljes deklaratív vezérléssel rendelkezik a kimenő kapcsolat felett, hogy ezt a képességet az adott igényekhez méretezheti és finomíthassa.

![Terheléselosztó kimenő szabályai](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

A kimenő szabályok kal a terheléselosztóval a következőket használhatja: 
- határozza meg a kimenő NAT-t a nulláról.
- méretezte és hangolja be a meglévő kimenő NAT viselkedését. 

A kimenő szabályok lehetővé teszik a következők vezérlését:
- mely virtuális gépeket kell lefordítani, amelyekre nyilvános IP-címeket kell lefordítani. 
- hogyan kell [lefoglalni a kimenő SNAT-portokat.](load-balancer-outbound-connections.md#snat)
- milyen protokollok, hogy a kimenő fordítás.
- milyen időtartamot kell használni a kimenő kapcsolat tétlen időtúllépéséhez (4-120 perc).
- a TCP-alaphelyzetbe állítás küldése tétlen időkitöltéskor

Kimenő szabályok kibontják a [outbound connections](load-balancer-outbound-connections.md) [2.](load-balancer-outbound-connections.md#lb)

## <a name="outbound-rule"></a>Kimenő szabály

Mint minden terheléselosztó szabály, a kimenő szabályok is ugyanazt a jól ismert szintaxist követik, mint a terheléselosztás és a bejövő NAT-szabályok:

**előtér-paraméterek** + **parameters** + **háttérkészlete**

A kimenő szabály konfigurálja a kimenő hálózati fordítást _a háttérkészlet által azonosított összes virtuális géphez,_ amelyet az _előtérre_kell fordítani.  A paraméterek pedig további részletes vezérlést _biztosítanak_ a kimenő NAT algoritmus felett.

Az "2018-07-01" API-verzió a következőképpen strukturál:

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
>A tényleges kimenő NAT-konfiguráció az összes kimenő szabály és terheléselosztási szabály összetett. A kimenő szabályok növekményesek a terheléselosztási szabályokhoz. Tekintse át [a kimenő NAT letiltását egy terheléselosztási szabályhoz,](#disablesnat) hogy kezelje a tényleges kimenő NAT-fordítást, ha több szabály vonatkozik egy virtuális gépre. Le kell [tiltania a kimenő SNAT-ot,](#disablesnat) amikor olyan kimenő szabályt határoz meg, amely ugyanazt a nyilvános IP-címet használja terheléselosztási szabályként.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>Kimenő NAT méretezése több IP-címmel

Míg a kimenő szabály csak egyetlen nyilvános IP-címmel használható, a kimenő szabályok megkönnyítik a kimenő hálózati címcím skálázásának konfigurációs terhét. Több IP-cím használatával tervezheti meg a nagyméretű forgatókönyveket, és a kimenő szabályok segítségével enyhítheti [az SNAT-kimerülési](load-balancer-outbound-connections.md#snatexhaust) hajlammintákat.  

Az előtér által biztosított minden további IP-cím 64 000 ideiglenes portot biztosít a terheléselosztó számára SNAT-portként való használatra. Míg a terheléselosztás vagy a bejövő NAT-szabályok egyetlen előtér-szabályok, a kimenő szabály kibontja az előtér-fogalom, és lehetővé teszi, hogy több előtér szabályonként.  Szabályonként több előtér-végződéssel a rendelkezésre álló SNAT-portok mennyisége megszorozza az egyes nyilvános IP-címekkel, és nagy forgatókönyvek is támogatottak.

Emellett használhat [nyilvános IP-előtagot](https://aka.ms/lbpublicipprefix) közvetlenül egy kimenő szabállyal.  A nyilvános IP-előtag használatával egyszerűbb skálázást és az Azure-telepítésből származó folyamatok egyszerűsített fehér listázását teszi lehetővé. A terheléselosztó erőforráson belül beállíthatja az előtér IP-konfigurációját, hogy közvetlenül hivatkozzon egy nyilvános IP-címelőtagra.  Ez lehetővé teszi a terheléselosztó kizárólagos vezérlését a nyilvános IP-előtag felett, és a kimenő szabály automatikusan a nyilvános IP-előtagban található összes nyilvános IP-címet használja a kimenő kapcsolatokhoz.  A nyilvános IP-előtag tartományán belüli IP-címek mindegyike 64 000 ideiglenes portot biztosít IP-címenként a terheléselosztó Számára, amelyet SNAT-portként használhat.   

A nyilvános IP-előtagból létrehozott egyes nyilvános IP-címerőforrások nem rendelkezhetnek, ha ezt a beállítást használja, mivel a kimenő szabálynak teljes körűen ellenőriznie kell a nyilvános IP-előtagot.  Ha több részletes vezérlésre van szüksége, létrehozhat egyéni nyilvános IP-címerőforrást a nyilvános IP-előtagból, és több nyilvános IP-címet rendelhet külön-külön egy kimenő szabály előtéréhez.

### <a name="tune-snat-port-allocation"></a><a name="snatports"></a>SNAT-portkiosztás finomhangolása

A kimenő szabályok segítségével finomhangolhatja az [automatikus SNAT-portfoglalást a háttérkészlet mérete alapján,](load-balancer-outbound-connections.md#preallocatedports) és az automatikus SNAT-portfoglalásnál többet vagy kevesebbet foglalhat le.

A következő paraméterrel 10 000 SNAT-portot foglalhat le virtuális gépenként (NIC IP-konfiguráció).
 

          "allocatedOutboundPorts": 10000

A kimenő szabály minden előtér-címéről származó nyilvános IP-cím legfeljebb 64 000 ideiglenes portot ad hozzá SNAT-portként való használatra.  A terheléselosztó 8 többszöröseként osztja ki az SNAT-portokat. Ha 8-val nem osztható értéket ad meg, a konfigurációs művelet elutasításra kerül.  Ha a nyilvános IP-címek száma alapján a rendelkezésre állónál több SNAT-portot próbál lefoglalni, a rendszer elutasítja a konfigurációs műveletet.  Ha például virtuális gépenként 10 000 portot és 7 virtuális gépet foglal le egy háttérkészletben, akkor a rendszer egyetlen nyilvános IP-címet oszt meg, a konfiguráció elutasításra kerül (7 x 10 000 SNAT-port > 64 000 SNAT-port).  A forgatókönyv engedélyezéséhez további nyilvános IP-címeket adhat hozzá a kimenő szabály előteréhez.

A háttérkészlet mérete alapján visszaállíthatja [az automatikus SNAT-portfoglalást](load-balancer-outbound-connections.md#preallocatedports) a portok számához megadott 0 megadásával. Ebben az esetben az első 50 virtuálisgép-példány oka 1024 port lesz, 51-100 virtuálisgép-példány oka 512 és így tovább a táblázat szerint.

### <a name="control-outbound-flow-idle-timeout"></a><a name="idletimeout"></a>Kimenő folyamat tétlen időhamnak szabályozása

A kimenő szabályok konfigurációs paramétert biztosítanak a kimenő folyamat üresjárati időtúljáratának szabályozásához, és az alkalmazás igényeinek megfelelően.  A kimenő tétlen időoutok alapértelmezés szerint 4 perc.  A paraméter 4 és 120 között fogadja el az adott szabálynak megfelelő folyamatok üresjárati időtúlszámára vonatkozó percek számát.

A következő paraméterrel a kimenő üresjárati időoutot 1 órára állíthatja be:

          "idleTimeoutInMinutes": 60

### <a name="enable-tcp-reset-on-idle-timeout"></a><a name="tcprst"></a><a name="tcpreset"></a> TCP-alaphelyzetbe állítás engedélyezése tétlen időkiíráskor

A terheléselosztó alapértelmezett viselkedése az, hogy a kimenő tétlen időtúllépés elérésekor csendben eldobja a folyamatot.  Az enableTCPReset paraméterrel kiszámíthatóbb alkalmazásviselkedést engedélyezhet, és szabályozhatja, hogy a kimenő üresjárati időadat-számon való kivezetéskor küldjön-e kétirányú TCP reset (TCP RST). 

A következő paraméterrel engedélyezheti a TCP resetet egy kimenő szabályon:

           "enableTcpReset": true

Tekintse át [a TCP reset az alapjárati időmegtücő](https://aka.ms/lbtcpreset) a részleteket, beleértve a régió rendelkezésre állását.

### <a name="support-both-tcp-and-udp-transport-protocols-with-a-single-rule"></a><a name="proto"></a>A TCP és az UDP átviteli protokollok támogatása egyetlen szabállyal

Valószínűleg az "Összes" szót szeretné használni a kimenő szabály átviteli protokolljában, de a kimenő szabályt is alkalmazhatja egy adott átviteli protokollra, ha erre szükség van.

A protokoll tcp-re és UDP-re való beállításához használja a következő paramétert:

          "protocol": "All"

### <a name="disable-outbound-nat-for-a-load-balancing-rule"></a><a name="disablesnat"></a>Kimenő nat letiltása terheléselosztási szabály esetén

Ahogy azt korábban említették, a terheléselosztási szabályok a kimenő NAT automatikus programozását biztosítják. Egyes esetekben azonban előnyös, vagy megköveteli, hogy tiltsa le az automatikus programozás kimenő NAT a terheléselosztási szabály lehetővé teszi, hogy ellenőrizzék vagy finomítsák a viselkedést.  A kimenő szabályok olyan forgatókönyvekkel rendelkeznek, amelyekben fontos az automatikus kimenő NAT-programozás leállítása.

Ezt a paramétert kétféleképpen használhatja:
- A bejövő IP-cím használatának nem kötelező a kimenő hálózati címcímhez.  A kimenő szabályok növekményesek a terheléselosztási szabályokhoz, és ezzel a paraméterkészlettel a kimenő szabály van irányítva.
  
- Hangolja be a bejövő és kimenő IP-cím kimenő NAT-paramétereit egyszerre.  Az automatikus kimenő NAT-programozást le kell tiltani, hogy a kimenő szabály átvehesse az irányítást.  Például a bejövő cím SNAT-portfoglalásának módosításához ezt a paramétert igaz értékre kell állítani.  Ha egy kimenő szabály használatával próbálja újradefiniálni a bejövő ként használt IP-cím paramétereit, és nem adta ki a terheléselosztási szabály kimenő NAT-programozását, a kimenő szabály konfigurálására irányuló művelet sikertelen lesz.

>[!IMPORTANT]
> A virtuális gép nem lesz kimenő kapcsolat, ha ezt a paramétert igaz értékre állítja, és nem rendelkezik kimenő szabállyal (vagy [példányszintű nyilvános IP-forgatókönyvvel](load-balancer-outbound-connections.md#ilpip) a kimenő kapcsolat meghatározásához.  A virtuális gép vagy az alkalmazás egyes műveletei függhetnek a kimenő kapcsolat rendelkezésre álló. Győződjön meg arról, hogy megértette a forgatókönyv függőségeit, és figyelembe vette a módosítás hatását.

Ezzel a konfigurációs paraméterrel letilthatja a kimenő SNAT-t a terheléselosztási szabályban:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

A disableOutboundSNAT paraméter alapértelmezés szerint hamis, ami azt jelenti, hogy a terheléselosztási **szabály** automatikus kimenő Hálózati címt biztosít a terheléselosztási szabály konfigurációjának tükörképeként.  

Ha a disableOutboundSnat beállítását true értékre állítja a terheléselosztási szabályban, a terheléselosztási szabály felszabadítja az egyébként automatikus kimenő NAT-programozás vezérlését.  A terheléselosztási szabály miatt kimenő SNAT le van tiltva.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Meglévő háttérkészletek újrafelhasználása vagy definiálása

A kimenő szabályok nem vezetnek be új koncepciót a virtuális gépek azon csoportjának meghatározására, amelyre a szabályt alkalmazni kell.  Ehelyett újra felhasználják a háttérkészlet fogalmát, amely terheléselosztási szabályokhoz is használható. Ezzel egyszerűsítheti a konfigurációt egy meglévő háttérkészlet-definíció újbóli felhasználásával, vagy egy kifejezetten egy kimenő szabályhoz való létrehozásával.

## <a name="scenarios"></a>Forgatókönyvek

### <a name="groom-outbound-connections-to-a-specific-set-of-public-ip-addresses"></a><a name="groom"></a>Kimenő kapcsolatok vőlegénye nyilvános IP-címek adott készletéhez

Egy kimenő szabály segítségével ápolhatja a kimenő kapcsolatokat, hogy úgy tűnjön, hogy egy adott nyilvános IP-címek ből származnak az engedélyezési forgatókönyvek megkönnyítése érdekében.  Ez a forrás nyilvános IP-cím lehet ugyanaz, mint egy terheléselosztási szabály vagy egy másik nyilvános IP-címek, mint egy terheléselosztási szabály által használt.  

1. [Nyilvános IP-előtag](https://aka.ms/lbpublicipprefix) (vagy nyilvános IP-címek nyilvános IP-előtagból) létrehozása
2. Nyilvános Standard Load Balancer létrehozása
3. Előcímek létrehozása a használni kívánt nyilvános IP-előtagra (vagy nyilvános IP-címekre) hivatkozva
4. Háttérkészlet újbóli felhasználása vagy háttérkészlet létrehozása, és a virtuális gépek áthelyezése a nyilvános terheléselosztó háttérkészletébe
5. A nyilvános terheléselosztó kimenő hálózati hálózati használatára vonatkozó kimenő hálózati att a virtuális gépek kimenő hálózati attagai az előtér-kiszolgálók használatával történő programozásához
   
Ha nem szeretné, hogy a terheléselosztási szabály legyen használva a kimenő, le kell tiltania a [kimenő SNAT-ot](#disablesnat) a terheléselosztási szabályon.

### <a name="modify-snat-port-allocation"></a><a name="modifysnat"></a>SNAT-portfoglalás módosítása

A kimenő szabályok segítségével a háttérkészlet mérete alapján beállíthatja az [automatikus SNAT-portfoglalást.](load-balancer-outbound-connections.md#preallocatedports)

Ha például két virtuális gép osztozik egy nyilvános IP-címmel a kimenő Hálózati címcímhez, érdemes lehet növelni az alapértelmezett 1024-es portokból lefoglalt SNAT-portok számát, ha Az SNAT-kimerültség tapasztalható. Minden nyilvános IP-cím akár 64 000 ideiglenes portot is hozzátud járulni.  Ha egyetlen nyilvános IP-címelőlap-előtérrel konfigurál egy kimenő szabályt, összesen 64 000 SNAT-portot terjeszthet a háttérkészletben lévő virtuális gépek között.  Két virtuális gép esetén legfeljebb 32 000 SNAT-port foglalható le egy kimenő szabállyal (2x 32 000 = 64 000).

Tekintse át a [kimenő kapcsolatokat,](load-balancer-outbound-connections.md) valamint az [SNAT-portok](load-balancer-outbound-connections.md#snat) lefoglalásának és felhasználásának részleteit.

### <a name="enable-outbound-only"></a><a name="outboundonly"></a>Kimenő csak engedélyezése

Nyilvános standard terheléselosztó használatával kimenő nat-t biztosíthat a virtuális gépek egy csoportjához. Ebben a forgatókönyvben önmagában is használhat kimenő szabályt, anélkül, hogy további szabályokra lenne szükség.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>Kimenő nat csak virtuális gépekhez (nincs bejövő)

Definiáljon egy nyilvános standard terheléselosztót, helyezze a virtuális gépeket a háttérkészletbe, és konfiguráljon egy kimenő szabályt a kimenő hálózati címzet programozásához, és a kimenő kapcsolatokat egy adott nyilvános IP-címről való származtatáshoz. A nyilvános IP-előtag leegyszerűsítheti a kimenő kapcsolatok forrásának fehér listázását is.

1. Hozzon létre egy nyilvános standard terheléselosztót.
2. Hozzon létre egy háttérkészletet, és helyezze a virtuális gépeket a nyilvános terheléselosztó háttérkészletébe.
3. Konfiguráljon egy kimenő szabályt a nyilvános terheléselosztón a kimenő hálózati hálózati tetsz-kapcsolat programozásához ezekhez a virtuális gépekhez.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Kimenő NAT belső standard terheléselosztó forgatókönyvekhez

Belső standard terheléselosztó használata esetén a kimenő hálózati hálózati tetszési jognem érhető el, amíg a kimenő kapcsolat explicit módon nincs deklarálva. A kimenő kapcsolatot egy kimenő szabály használatával határozhatja meg, hogy a belső standard terheléselosztó mögött imázsú virtuális gépek kimenő kapcsolatát hozza létre a következő lépésekkel:

1. Hozzon létre egy nyilvános standard terheléselosztót.
2. Hozzon létre egy háttérkészletet, és helyezze a virtuális gépeket a nyilvános terheléselosztó háttérkészletébe a belső terheléselosztó mellett.
3. Konfiguráljon egy kimenő szabályt a nyilvános terheléselosztón a kimenő hálózati hálózati tetsz-kapcsolat programozásához ezekhez a virtuális gépekhez.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>A tcp-& UDP protokollok engedélyezése nyilvános szabványos terheléselosztóval rendelkező kimenő hálózati felhasználói hálózati átkötéshez

- Nyilvános standard terheléselosztó használata esetén a megadott automatikus kimenő NAT-programozás megegyezik a terheléselosztási szabály átviteli protokolljával.  

   1. Tiltsa le a kimenő SNAT-t a terheléselosztási szabályon.
   2. Konfiguráljon egy kimenő szabályt ugyanazon a terheléselosztón.
   3. A virtuális gépek által már használt háttérkészlet újrafelhasználása.
   4. Adja meg a "protokoll": "Mind" a kimenő szabály részeként.

- Ha csak bejövő NAT-szabályokat használ, nem biztosít kimenő nat.When only inbound NAT rules are used, no outbound NAT are provided.

   1. Virtuális gépek helye egy háttérkészletben.
   2. Definiáljon egy vagy több előtér-IP-konfigurációt nyilvános IP-címekkel vagy nyilvános IP-előtaggal.
   3. Konfiguráljon egy kimenő szabályt ugyanazon a terheléselosztón.
   4. Adja meg a "protokoll": "Minden" a kimenő szabály részeként

## <a name="limitations"></a>Korlátozások

- Előtér-IP-címenként legfeljebb 64 000 használható ideiglenes port használható.
- A konfigurálható kimenő tétlen időköztartomány 4–120 perc (240–7200 másodperc).
- A terheléselosztó nem támogatja az ICMP-t a kimenő NAT-hez.
- A kimenő szabályok csak a hálózati adapter elsődleges IP-konfigurációjára alkalmazhatók.  Több hálózati adapter ek támogatottak.

## <a name="next-steps"></a>További lépések

- További információ a terheléselosztó használatáról [a kimenő kapcsolatokhoz.](load-balancer-outbound-connections.md)
- További információ a [Standard Load Balancer](load-balancer-standard-overview.md)- összegről.
- További információ a [kétirányú TCP-alaphelyzetbe állításról az alapjárati időhosszabbításkor.](load-balancer-tcp-reset.md)
- [Konfigurálja a kimenő szabályokat az Azure CLI 2.0-s beállításával.](configure-load-balancer-outbound-cli.md)
