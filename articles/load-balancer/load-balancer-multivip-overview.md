---
title: Több előtér - Azure Load Balancer
description: Ezzel a tanulási útvonallal ismerkedje meg az Azure Load Balancer több előtétszolgáltatásának áttekintésével
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 0a54416a70a8561edfad5915944100e0ce686bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771257"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Több előtér-szolgáltatás az Azure Load Balancer számára

Az Azure Load Balancer lehetővé teszi, hogy több porton, több IP-címen vagy mindkettőn terheléselosztási szolgáltatásokat töltsön be. Nyilvános és belső terheléselosztó definíciók segítségével a virtuális gépek egy készletén keresztül terheléselosztási folyamatok.

Ez a cikk ismerteti ennek a képességnek az alapjait, a fontos fogalmakat és a korlátokat. Ha csak egy IP-címen kívánja elérhetővé tenni a szolgáltatásokat, egyszerűsített utasításokat találhat a [nyilvános](load-balancer-get-started-internet-portal.md) vagy [belső](load-balancer-get-started-ilb-arm-portal.md) terheléselosztó konfigurációkhoz. Több előtér-szolgáltatás hozzáadása növekményes egyetlen előtér-konfigurációhoz. A cikkben szereplő fogalmak használatával bármikor kibonthatja az egyszerűsített konfigurációt.

Az Azure Load Balancer definiálásakor az előtér és a háttérkészlet-konfiguráció szabályokhoz kapcsolódik. A szabály által hivatkozott állapotminta határozza meg, hogyan új folyamatok küldése a háttérkészlet csomópontjára. Az előtér (más néven VIP) egy 3-t, amely egy IP-címből (nyilvános vagy belső), egy átviteli protokollból (UDP vagy TCP) és egy terheléselosztási szabályból származó portszámból áll. A háttérkészlet a virtuális gép IP-konfigurációinak (a hálózati adapter erőforrás része) gyűjteménye, amelyek a terheléselosztó háttérkészletre hivatkoznak.

Az alábbi táblázat néhány példa előtér-konfigurációt tartalmaz:

| Előtér | IP-cím | Protokoll | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

A táblázat négy különböző előtorékot mutat. Az előtér-#1, #2 és #3 egy több szabályból rendelkező előtér- rendszer. Ugyanazt az IP-címet használja, de a port vagy protokoll minden előtér-hoz eltérő. Előtér-#1 és #4 egy példa több előtér-rendszerek, ahol ugyanazt az előtér-protokollt és portot több előtér-rendszerek újrafelhasználják.

Az Azure Load Balancer rugalmasságot biztosít a terheléselosztási szabályok meghatározásában. A szabály deklarálja, hogy az előtér-cím és -port hogyan van leképezve a háttér-célcímre és portra. Az, hogy a háttérportokat újra felhasználják-e a szabályok között, a szabály típusától függ. Minden szabálytípusnak vannak olyan speciális követelményei, amelyek hatással lehetnek az állomás konfigurációjára és a mintavételi tervre. Kétféle szabály létezik:

1. A háttérport újrafelhasználása nélküli alapértelmezett szabály
2. A lebegő IP-szabály, ahol a háttérportokat újra felhasználják

Az Azure Load Balancer lehetővé teszi, hogy mindkét szabálytípust azonos terheléselosztó-konfigurációval keverje. A terheléselosztó használhatja őket egyszerre egy adott virtuális gép, vagy bármilyen kombináció, mindaddig, amíg betartja a korlátozásokat a szabály. A választott szabálytípus az alkalmazás követelményeitől és a konfiguráció támogatásának összetettségétől függ. Ki kell értékelnie, hogy mely szabálytípusok a legjobbak a forgatókönyvhöz.

Ezeket a forgatókönyveket az alapértelmezett viselkedéssel kezdve tovább vizsgáljuk.

## <a name="rule-type-1-no-backend-port-reuse"></a>Szabálytípus #1: Nincs háttérablak újrafelhasználása

![Több előtér-illusztráció zöld és lila előtérrel](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

Ebben az esetben az előtér-rendszerek a következőképpen vannak konfigurálva:

| Előtér | IP-cím | Protokoll | port |
| --- | --- | --- | --- |
| ![zöld előtér](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![lila előtér](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

A DIP a bejövő folyamat célja. A háttérkészletben minden virtuális gép a kívánt szolgáltatást egy DIP-en egy egyedi porton teszi elérhetővé. Ez a szolgáltatás egy szabálydefiníción keresztül van társítva az előtérhez.

Két szabályt határozunk meg:

| Szabály | Térkép előtér | Háttérkészlet |
| --- | --- | --- |
| 1 |![zöld előtér](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Előtér1:80 |![háttér](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![háttér](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Előtér2:80 |![háttér](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![háttér](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Az Azure Load Balancer teljes leképezése mostantól a következő:

| Szabály | Előtérbeli IP-cím | Protokoll | port | Cél | port |
| --- | --- | --- | --- | --- | --- |
| ![zöld szabály](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |DIP IP-cím |80 |
| ![lila szabály](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |DIP IP-cím |81 |

Minden szabálynak létre kell hoznia egy folyamatot a cél IP-címének és a célportnak egyedi kombinációjával. A folyamat célportjának eldifferentezésével több szabály is biztosíthatja a folyamatokat ugyanabba a DIP-be a különböző portokon.

Az állapotpróbák mindig a virtuális gép DIP-jéhez irányulnak. Győződjön meg arról, hogy a mintavétel tükrözi a virtuális gép állapotát.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Szabálytípus #2: háttérport újrafelhasználása lebegő IP használatával

Az Azure Load Balancer rugalmasságot biztosít az előtér-port több előtér-porton keresztül, függetlenül a használt szabálytípusától. Emellett egyes alkalmazás-forgatókönyvek előnyben részesítik, vagy megkövetelik ugyanazt a portot kell használni több alkalmazáspéldány egyetlen virtuális gép a háttérkészletben. A portok újrafelhasználásának gyakori példái közé tartozik a magas rendelkezésre állású fürtözés, a hálózati virtuális készülékek, valamint több TLS-végpont újbóli titkosítás nélküli kivonása.

Ha a háttérportot több szabályban szeretné újra felhasználni, engedélyeznie kell a lebegő IP-címet a szabálydefinícióban.

A "lebegő IP-cím" az Azure terminológiája az úgynevezett Direct Server Return (DSR) egy részében. A DSR két részből áll: egy folyamattopológiából és egy IP-címleképezési sémából. Platform szinten az Azure Load Balancer mindig egy DSR-folyamat topológiában működik, függetlenül attól, hogy a lebegő IP engedélyezve van-e vagy sem. Ez azt jelenti, hogy a folyamat kimenő része mindig helyesen lesz újraírva, hogy közvetlenül az origóba folyjon vissza.

Az alapértelmezett szabálytípussal az Azure egy hagyományos terheléselosztási IP-címleképezési sémát tesz elérhetővé a könnyű használat érdekében. A lebegő IP engedélyezése módosítja az IP-címleképezési sémát, hogy az alább leírtak szerint további rugalmasságot biztosítson.

Az alábbi ábra ezt a konfigurációt mutatja be:

![Több előtér-illusztráció zöld és lila előlapral DSR-rel](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Ebben a forgatókönyvben a háttérkészletben lévő minden virtuális gép három hálózati csatolmányt rendelkezik:

* DIP: a virtuális virtuális hálózathoz társított virtuális hálózati adapter (az Azure hálózati adaptererőforrásÁNAK IP-konfigurációja)
* Előtér-1: a vendég operációs rendszeren belül az 1.
* 2. előtér: a vendég operációs rendszeren belüli visszacsatolási felület, amely az Előtér 2 IP-címével van konfigurálva

A háttérkészletben lévő minden virtuális gép esetén futtassa a következő parancsokat a Windows parancssorában.

A virtuális gépen található kapcsolatnevek listájának leéséhez írja be ezt a parancsot:

    netsh interface show interface 

A virtuális gép hálózati adapter (Azure felügyelt) írja be ezt a parancsot:

    netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled
   (cserélje le az összeköttetés nevét ennek a kapcsolatnak a nevére)

Minden hozzáadott visszacsatolási felülethez ismételje meg az alábbi parancsokat:

    netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled 
   (cserélje le az összeköttetés nevét ennek a visszacsatolási felületnek a nevére)
     
    netsh interface ipv4 set interface “interfacename” weakhostsend=enabled 
   (cserélje le az összeköttetés nevét ennek a visszacsatolási felületnek a nevére)

> [!IMPORTANT]
> A visszacsatolási felületek konfigurálása a vendég operációs rendszeren belül történik. Ezt a konfigurációt az Azure nem hajtja végre és nem kezeli. E konfiguráció nélkül a szabályok nem fognak működni. Az állapotminta-definíciók a virtuális gép DIP-ját használják a DSR előtér-felületet képviselő visszacsatolási felület helyett. Ezért a szolgáltatásnak mintavételi válaszokat kell biztosítania egy DIP-porton, amely a DSR előtér-felületet képviselő visszacsatolási felületen kínált szolgáltatás állapotát tükrözi.


Tegyük fel, hogy ugyanazt az előtér-konfigurációt, mint az előző forgatókönyvben:

| Előtér | IP-cím | Protokoll | port |
| --- | --- | --- | --- |
| ![zöld előtér](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![lila előtér](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Két szabályt határozunk meg:

| Szabály | Előtér | Leképezés háttérkészlethez |
| --- | --- | --- |
| 1 |![Szabály](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Előtér1:80 |![háttér](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Előtér1:80 (VM1 és VM2) |
| 2 |![Szabály](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Előtér2:80 |![háttér](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Előtér2:80 (VM1 és VM2) |

Az alábbi táblázat a teljes hozzárendelést mutatja a terheléselosztóban:

| Szabály | Előtérbeli IP-cím | Protokoll | port | Cél | port |
| --- | --- | --- | --- | --- | --- |
| ![zöld szabály](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |megegyezik az előtér -val (65.52.0.1) |ugyanaz, mint a frontend (80) |
| ![lila szabály](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |megegyezik az előtér -val (65.52.0.2) |ugyanaz, mint a frontend (80) |

A bejövő folyamat célja az előtér IP-címe a virtuális gép visszacsatolási összeköttetésében. Minden szabálynak létre kell hoznia egy folyamatot a cél IP-címének és a célportnak egyedi kombinációjával. A folyamat cél IP-címének változó, port újrafelhasználása ugyanazon a virtuális gépen lehetséges. A szolgáltatás ki van téve a terheléselosztó nak az előtér IP-címéhez és portjához való kötésével.

Figyelje meg, hogy ez a példa nem módosítja a célportot. Annak ellenére, hogy ez egy lebegő IP-forgatókönyv, az Azure Load Balancer is támogatja a szabály definiálását a háttér-célport újraírásához, és az előtér-célporttól.

A lebegő IP-szabály típusa több terheléselosztó konfigurációs minta alapja. Egy példa, amely jelenleg elérhető az [SQL AlwaysOn több hallgató konfiguráció.](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) Idővel több ilyen forgatókönyvet dokumentálunk.

## <a name="limitations"></a>Korlátozások

* Több előtér-konfigurációk csak iaaS virtuális gépek támogatottak.
* A lebegő IP-szabály, az alkalmazás nak az elsődleges IP-konfigurációt kell használnia a kimenő SNAT-folyamatok. Ha az alkalmazás a vendég operációs rendszer visszacsatolási felületén konfigurált előtér-IP-címhez kötődik, az Azure kimenő SNAT-címe nem érhető el a kimenő folyamat újraírásához, és a folyamat sikertelen lesz.  Tekintse át [a kimenő forgatókönyveket](load-balancer-outbound-connections.md).
* A nyilvános IP-címek hatással vannak a számlázásra. További információ: [IP-cím árképzése](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Az előfizetési korlátok érvényesek. További információ: [Szolgáltatáskorlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) a részletekért.

## <a name="next-steps"></a>További lépések

- Tekintse át [a kimenő kapcsolatokat,](load-balancer-outbound-connections.md) hogy megismerjék a több előtér-kapcsolat viselkedésére gyakorolt hatását.
