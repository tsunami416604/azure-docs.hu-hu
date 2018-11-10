---
title: Több Előtérrendszer az Azure Load Balancer |} A Microsoft Docs
description: Több Előtérrendszer az Azure Load Balancer áttekintése
services: load-balancer
documentationcenter: na
author: chkuhtz
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: chkuhtz
ms.openlocfilehash: d435d2c491cf17356e96f7bbb05b1e22c8e04aca
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219358"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Az Azure Load Balancer több Előtérrendszer

Az Azure Load Balancer segítségével betöltése kiegyensúlyozottan elosztani a szolgáltatásokat több portra vagy több IP-cím. Használhatja a nyilvános és belső load balancer definíciók virtuális gépek terheléselosztása folyamatok elosztását.

Ez a cikk ismerteti a alapjait mutatja be, ez lehetővé teszi, fontos fogalmakat és korlátozások. Ha csak szeretne egy IP-címet a szolgáltatások, Észreveheti, hogy egyszerűsített utasításokat [nyilvános](load-balancer-get-started-internet-portal.md) vagy [belső](load-balancer-get-started-ilb-arm-portal.md) betölteni a terheléselosztó konfigurációjában. Több előtérrendszer hozzáadása nem növekményes egy egyetlen előtér-konfigurációjához. Ez a cikk az alapfogalmak használ, egy egyszerűsített konfigurációt bármikor bővítheti.

Az Azure Load Balancerhez határozza meg, amikor egy előtér- és a háttérrendszer készletkonfigurációt csatlakoznak a szabályoknak. Az állapotminta-szabály által hivatkozott azt határozza meg, hogy új folyamatok érkeznek a háttérkészlet egyik csomópontja. Az előtér (más néven virtuális IP-CÍMEK) egy 3 rekordos áll (nyilvános vagy belső) IP-cím, egy átviteli protokoll (UDP vagy TCP) és a terheléselosztási szabály a port számát határozza meg. A háttérkészlet virtuális gép IP-konfigurációja (a hálózati erőforrás része) hivatkozik a terheléselosztó háttérkészletének gyűjteménye.

Az alábbi táblázatban néhány példa előtérbeli konfigurációkat tartalmazza:

| Előtér | IP-cím | protokoll | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

A táblázat négy különböző előtérrendszer. Előtérrendszer 1, #2 és a #3 egy egyetlen előtér-több szabály is. Az azonos IP-címet használja, de a port vagy protokoll egyes eltér. Előtérrendszer 1 és a #4 is több előtérrendszer, például ahol a frontend ugyanazt a protokollt és portot újra felhasználhatók több előtérrendszer között.

Az Azure Load Balancer a terheléselosztási szabályok rugalmasságot biztosít. Egy szabály deklarálja, hogy hogyan egy címet és az előtérbeli port le van képezve a cél címe és a háttérbeli portot. E szabályok között háttérportokat újra felhasználhatók attól függ, hogy a szabály típusát. Minden szabály rendelkezik konkrét követelmények, amelyek hatással lehetnek a gazdagép konfigurációja és a mintavételi megtervezése. Szabályok két típusa van:

1. Nincs háttér port újból az alapértelmezett szabály
2. A nem fix IP-szabály, ahol háttérportokat újra felhasználhatók

Az Azure Load Balancer lehetővé teszi az ugyanazon terheléselosztói konfigurációban mindkét szabálytípusokat kombinálhatók. A load balancer használhatja őket egyszerre egy adott virtuális gép, vagy tetszőleges kombinációjának mindaddig, amíg Ön betartja a korlátokat a szabály a. A szabály típusának úgy dönt, az alkalmazás követelményeit és összetettségét, ez a konfiguráció támogató függ. Ki kell értékelni mely szabályok típusai a forgatókönyv esetén ajánlott használni.

További ezekben az esetekben az alapértelmezett viselkedés kezdi tárgyaljuk.

## <a name="rule-type-1-no-backend-port-reuse"></a>Szabály típusa #1: nincs háttér port újrafelhasználása

![Az előtéri zöld és a lila több előtér-ábra](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

Ebben a forgatókönyvben az előtérrendszer az alábbiak szerint vannak konfigurálva:

| Előtér | IP-cím | protokoll | port |
| --- | --- | --- | --- |
| ![előtéri zöld](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![lila frontend](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

A dedikált IP-CÍMMEL az a hely a bejövő forgalom. A háttérkészlet minden egyes virtuális Géphez a kívánt szolgáltatás egy dedikált IP-CÍMMEL az egyedi port mutatja meg. Ez a szolgáltatás az előtér egy szabálydefiníciót keresztül kapcsolódik.

Azt adja meg a két szabályt:

| Szabály | Térkép frontend | A háttérkészlet |
| --- | --- | --- |
| 1 |![előtéri zöld](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![Háttér](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![Háttér](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![Háttér](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![Háttér](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Az Azure Load balancerben teljes leképezés most a következőképpen történik:

| Szabály | Előtérbeli IP-cím | protokoll | port | Cél | port |
| --- | --- | --- | --- | --- | --- |
| ![zöld szabály](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |Dedikált IP-CÍMMEL IP-cím |80 |
| ![lila szabály](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |Dedikált IP-CÍMMEL IP-cím |81 |

Minden egyes szabály egy folyamatot egy cél IP-cím és céloldali port egyedi kombinációja révén kell dolgoznia. A folyamat célport vonalszínnel több szabály a különböző portokat azonos DIP folyamatok közvetíti.

Állapot-mintavételei mindig irányítja a virtuális gépek a dedikált IP-CÍMMEL. Bizonyosodjon meg, hogy a mintavétel tükrözi a virtuális gép állapotát.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Szabály típusa #2: a nem fix IP-háttérrendszer port újbóli

Az Azure Load Balancer felhasználhatja a frontend-port több előtérrendszer, függetlenül a használt szabály típusát különböző biztosít. Bizonyos esetekben emellett igény szerint, vagy ugyanazt a portot, a háttérkészletben egyetlen virtuális géphez több alkalmazáspéldány által használandó szükséges. Port újbóli néhány gyakori példa tartalmazza a fürtszolgáltatás magas rendelkezésre állás, a hálózati virtuális berendezések, és több TLS végpont újbóli titkosítás nélkül is közzéteheti.

Ha szeretné újra felhasználhatja a háttérport között több szabály, engedélyeznie kell nem fix IP-a szabály-definícióban.

"Nem fix IP-címe" az Azure-terminológia esetén, a közvetlen kiszolgálói adja vissza (DSR) néven ismert. DSR két részből áll: egy flow-topológia és a egy IP-cím hozzárendelést sémát. A platform szintjén Azure Load Balancer mindig működik egy DSR-folyamat topológia, függetlenül attól, hogy a nem fix IP engedélyezve van-e vagy sem. Ez azt jelenti, hogy a flow a kimenő részét mindig megfelelően feladatátvitelt térjen vissza a forrás közvetlen áramlását.

Az alapértelmezett szabály típusa, az Azure hagyományos terheléselosztási IP cím hozzárendelési séma a használat megkönnyítése érdekében tesz elérhetővé. Floating IP engedélyezése módosítja az IP cím leképezés sémát, és lehetővé teszik a rugalmasságra, kövesse az alábbi utasításokat.

A következő ábra szemlélteti ezt a konfigurációt:

![A DSR-zöld és a lila előtérbeli több előtérbeli ábra](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Ebben a forgatókönyvben a háttérkészletben lévő összes virtuális Géphez a három hálózati adapterrel rendelkezik:

* Dedikált IP-CÍMMEL: egy virtuális hálózati Adaptert a virtuális gép (IP-konfigurációja, az Azure-hálózati erőforrás) társított
* : 1 egy visszacsatolási ifindex belül a vendég operációs rendszer, amely 1 előtérbeli IP-címmel van konfigurálva
* : 2 egy visszacsatolási ifindex belül a vendég operációs rendszer, amely 2-es előtérbeli IP-címmel van konfigurálva

> [!IMPORTANT]
> A visszacsatolás felületek konfigurációját a vendég operációs rendszeren belül történik. Ez a konfiguráció nem végzett vagy az Azure által felügyelt. Ez a konfiguráció nélkül a szabályok nem fognak működni. Egészségügyi mintavételi definíciókat a DIP DSR előtér jelölő visszacsatolási helyett a virtuális gép használja. Ezért a szolgáltatás adjon meg mintavételi válaszok egy DIP portot, amely a DSR előtérbeli jelölő visszacsatolási a kínált szolgáltatás állapotát mutatja.

Tegyük fel, mint az előző forgatókönyvben ugyanazt a frontend konfigurációt:

| Előtér | IP-cím | protokoll | port |
| --- | --- | --- | --- |
| ![előtéri zöld](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![lila frontend](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Azt adja meg a két szabályt:

| Szabály | Előtér | Háttérkészlet leképezése |
| --- | --- | --- |
| 1 |![szabály](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![Háttér](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) (A VM1 és VM2) Frontend1:80 |
| 2 |![szabály](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![Háttér](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) (A VM1 és VM2) Frontend2:80 |

Az alábbi táblázatban látható a teljes leképezés a terheléselosztó:

| Szabály | Előtérbeli IP-cím | protokoll | port | Cél | port |
| --- | --- | --- | --- | --- | --- |
| ![zöld szabály](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |ugyanaz, mint a frontend (65.52.0.1) |ugyanaz, mint a frontend (80-as) |
| ![lila szabály](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |ugyanaz, mint a frontend (65.52.0.2) |ugyanaz, mint a frontend (80-as) |

A bejövő forgalom és az előtérbeli IP-cím a virtuális gép visszacsatolási vonatkozó. Minden egyes szabály egy folyamatot egy cél IP-cím és céloldali port egyedi kombinációja révén kell dolgoznia. A folyamat a cél IP-cím, port újbóli ugyanarról a virtuális gépről lehetőség. A szolgáltatás által az előtérbeli IP-cím és port megfelelő visszacsatolási vonatkozó való kötés elérhetővé a terheléselosztóhoz.

Figyelje meg, hogy ebben a példában nem változtatja meg a célport. Annak ellenére, hogy ez a nem fix IP-forgatókönyv, az Azure Load Balancer is támogatja cél háttérport újraírási és, hogy az előtérbeli célport eltérő szabályt.

A nem fix IP-szabály alapját a több load balancer konfigurációs minták esetében. Jelenleg elérhető egyik példája a [több Kérésfigyelőt az SQL AlwaysOn](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) konfigurációja. Idővel azt fogja a dokumentum több ezekben a forgatókönyvekben.

## <a name="limitations"></a>Korlátozások

* Több előtér-konfiguráció csak IaaS virtuális gépek használata támogatott.
* A nem fix IP-szabályt az alkalmazás a kimenő forgalom kell használnia az elsődleges IP-konfiguráció. Ha az alkalmazás az előtérbeli IP-cím konfigurálva van kötve a visszacsatolási a vendég operációs rendszer, az Azure az SNAT felület nem érhető el a kimenő folyam újraírási, és a folyamatnak nem sikerül.
* Nyilvános IP-címek számlázási hatással. További információkért lásd: [IP-címek díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Előfizetési korlátok vonatkoznak. További információkért lásd: [szolgáltatási korlátozásaival](../azure-subscription-service-limits.md#networking-limits) részleteiről.

## <a name="next-steps"></a>További lépések

- Felülvizsgálat [kimenő kapcsolatok](load-balancer-outbound-connections.md) a kimenő kapcsolatok viselkedését több előtérrendszer hatásának megértéséhez.
