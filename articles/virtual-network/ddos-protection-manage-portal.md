---
title: "Kezelése Azure DDoS védelem szabványos az Azure portál használatával |} Microsoft Docs"
description: "Útmutató: Azure figyelő Azure DDoS védelem szabványos telemetria segítségével a támadás elhárítása érdekében."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 019d4ba9124173a7de555c46d32881ecf639a34c
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Kezelése Azure DDoS védelem szabványos az Azure portál használatával

Megtudhatja, hogyan engedélyezése és elosztott szolgáltatásmegtagadásos (DDoS-) szolgáltatás védelem letiltásához, és telemetriai adatokat a Azure DDoS védelem standard DDoS-támadások mérséklése. DDoS védelem szabványos védelmet nyújt az Azure erőforrások, például a virtuális gépek terheléselosztók és alkalmazásátjárót, amelyek egy Azure [nyilvános IP-cím](virtual-network-public-ip-address.md) rendelve. DDoS védelem szabványos és platformképességei kapcsolatos további információkért lásd: [DDoS védelem szabványos áttekintése](ddos-protection-overview.md). 

>[!IMPORTANT]
>Az Azure DDoS védelem szabványos (DDoS-védelem) jelenleg előzetes verzió. Azure-erőforrások korlátozott számú DDoS-védelem támogatása, és azt el csak select több régióban. Elérhető régiók listáját lásd: [DDoS védelem szabványos áttekintése](ddos-protection-overview.md). Kell [a szolgáltatás regisztrálása](http://aka.ms/ddosprotection) a korlátozott előzetes DDoS-védelem engedélyezve van az előfizetéshez tartozó eléréséhez. A regisztrálás után végigvezeti Önt az engedélyezési folyamat Azure DDoS csoport által próbál kapcsolódni. 

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>DDoS védelem szabványos – új virtuális hálózat engedélyezése

1. Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
2. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.
3. Válassza ki **hálózati**, majd válassza ki **virtuális hálózati**.
4. Virtuális hálózat létrehozása a kiválasztott beállításokkal. Virtuális hálózatok létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy virtuális hálózatot](virtual-networks-create-vnet-arm-pportal.md). A *DDoS-védelem*, kattintson a **engedélyezve**, és kattintson a **létrehozása**.

    ![Virtuális hálózat létrehozása](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

    > [!WARNING]
    > A régió kiválasztásakor válassza a listából egy támogatott régióban [Azure DDoS védelem szabványos áttekintése](ddos-protection-overview.md).

    Figyelmeztetés jelzi, hogy DDoS-védelem engedélyezése terhel. Nincsenek terhelések a DDoS védelem előzetes felmerülő. Díjak általánosan rendelkezésre álló gyakorisága. 30 napos értesítés díjakat és általánosan rendelkezésre álló elindítása előtt fog kapni.

## <a name="enable-ddos-protection-standard---existing-virtual-network"></a>DDoS védelem Standard - meglévő virtuális hálózat engedélyezése 

1. Kattintson a **virtuális hálózatok** az az Azure-portál menüjében, majd válassza ki a virtuális hálózat.
2. Kattintson a **DDoS-védelem**, kattintson a **engedélyezve** a a *DDoS-védelem* képernyőn, és kattintson a **mentése**. 

    > [!WARNING]
    > A virtuális hálózati léteznie kell egy támogatott régióban. A támogatott régiók listáját lásd: [Azure DDoS védelem szabványos áttekintése](ddos-protection-overview.md).

    Figyelmeztetés jelzi, hogy DDoS-védelem engedélyezése terhel. Nincsenek terhelések a DDoS védelem előzetes felmerülő. Díjak általánosan rendelkezésre álló gyakorisága. 30 napos értesítés díjakat és általánosan rendelkezésre álló elindítása előtt fog kapni.

## <a name="disable-ddos-protection-on-a-virtual-network"></a>DDoS-védelem letiltása a virtuális hálózaton

1. Kattintson a **virtuális hálózatok** az az Azure-portál menüjében, majd válassza ki a virtuális hálózat.
2. Kattintson a **DDoS-védelem**, kattintson a **letiltott** a a *DDoS-védelem* képernyőn, és kattintson a **mentése**.

## <a name="configure-alerts-on-ddos-protection-metrics"></a>DDoS-védelem mérőszámokat riasztások konfigurálása

Riasztást küld, ha egy aktív megoldás az Azure-figyelő riasztási konfigurációt használja a támadás során elérhető DDoS-védelem metrikai bármelyikét kiválaszthatja. A feltételek teljesülnek, a megadott cím figyelmeztető e-mailt kapja.

1. Kattintson a **figyelő**, és kattintson a **metrikák**.
2. Az a *metrikák* képernyőn, válassza ki az erőforráscsoportot, erőforrás típusa **nyilvános IP-cím**, és az Azure nyilvános IP-címe.
3. Az e-mail riasztások olyan metrikajelentés konfigurálásához kattintson **metrika riasztás hozzáadása**. Az e-mail riasztások bármely metrika hozható létre, de a legnyilvánvalóbb mérték **alatt DDoS támadás, vagy nem**. Ez az egy logikai érték 1 vagy 0. A **1** azt jelenti, hogy támadás alatt áll. A **0** azt jelenti, hogy nem támadás alatt áll.
4. E-mailben támadás alatt, állítsa a metrikát **alatt DDoS támadás, vagy nem** és **nagyobb, mint nulla (0) az elmúlt 5 percben feltételt**. Az egyéb hasonló riasztások is beállítható.

    ![Metrikáinak megadása](./media/ddos-protection-manage-portal/ddos-metrics.png)

    Észlelt támadás, néhány percen belül értesítést kap Azure figyelő metrikák használatával.

    ![A támadás riasztás](./media/ddos-protection-manage-portal/ddos-alert.png) 

Még többet is megtudhat arról [konfigurálása webhookokkal](../monitoring-and-diagnostics/insights-webhooks-alerts.md) és [a logic apps](../logic-apps/logic-apps-what-are-logic-apps.md) riasztások létrehozásához.

## <a name="configure-logging-on-ddos-protection-standard-metrics"></a>DDoS védelem szabványos mérőszámokat naplózás konfigurálása

1. Kattintson a **figyelő**, és kattintson a **diagnosztikai beállítások**.
2. Az a *metrikák* képernyőn, válassza ki az erőforráscsoportot, erőforrás típusa **nyilvános IP-cím**, és az Azure nyilvános IP-címe.
3. Kattintson a **kapcsolja be a következő adatok gyűjtéséhez diagnosztika**.

Három lehetőség áll rendelkezésre a naplózás:

- **Archív tárfiókba**: tárfiókba menti el a naplókat.
- **Az eseményközpontok felé adatfolyam**: lehetővé teszi, hogy a napló fogadó átvételéhez eseményközpont használatával a naplókat. Ez lehetővé teszi az integrációt a Splunk vagy más SIEM-rendszerekről.
- **Naplóelemzési küldése**: naplók ír az Azure MOBILE Naplóelemzés szolgáltatás.

## <a name="use-ddos-protection-telemetry"></a>DDoS-védelem telemetriai adatok használata

A támadás telemetriai valós idejű figyelése Azure keresztül valósul meg. A telemetriai adatok csak a megoldás alatt álló egy nyilvános IP-cím időtartamára érhető el. Telemetria előtt vagy után a támadás elhárítására nem látják.

1. Kattintson a **figyelő**, és kattintson a **metrikák**. 
2. Az a *metrikák* képernyőn, válassza ki az erőforráscsoportot, erőforrás típusa **nyilvános IP-cím**, és az Azure nyilvános IP-címe. Több **elérhető** a képernyő bal oldalán megjelenik. A metrikák kiválasztásakor van grafikon a a **Azure figyelő metrikák diagram** áttekintése képernyőn. 

A mérték nevének jelenlegi különböző csomagtípusok és bájt csomagok, és mindegyik metrikát a címkenevek az alapvető szerkezet az alábbiak szerint:

- **Eldobott címke nevét (pl. bejövő csomagok eldobott DDoS-)**: a DDoS védelem rendszer által eldobott/törlődik csomagok száma.
- **Továbbított címke nevét (pl.: bejövő továbbított csomagok DDoS)**: a DDoS rendszer, hogy a cél VIP – nem szűrt forgalmat továbbított csomagok száma.
- **Nincs címke nevét (például: bejövő csomagok DDoS):** a háttértisztítás rendszerbe – a csomagok összege képviselő kapott csomagok száma dobva, és továbbítja.

## <a name="next-steps"></a>Következő lépések

- [További tudnivalók az Azure diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [A Naplóelemzési az Azure storage naplóinak elemzése](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)