---
title: "Kezelése Azure DDoS védelem szabványos az Azure portál használatával |} Microsoft Docs"
description: "Útmutató: Azure figyelő Azure DDoS védelem szabványos telemetria segítségével a támadás elhárítása érdekében."
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.openlocfilehash: 5c599b4cc867dbc9a081af3a081195b998f63954
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Kezelése Azure DDoS védelem szabványos az Azure portál használatával

>[!IMPORTANT]
>Az Azure DDoS védelem szabványos (DDoS-védelem) jelenleg előzetes verzió. Korlátozott számú Azure-erőforrások támogatása DDoS-védelem, valamint a select több régióban. Kell [a szolgáltatás regisztrálása](http://aka.ms/ddosprotection) a korlátozott előzetes DDoS-védelem engedélyezve van az előfizetéshez tartozó eléréséhez. Próbál kapcsolódni, az Azure DDoS csoport regisztráláskor végigvezeti az engedélyezési folyamat által. Amerikai keleti, Velünk nyugati és nyugati középső Régiójában régiók DDoS-védelem nem áll rendelkezésre. Előzetes akkor nem számolnak szolgáltatásunkat használja.

Ez a cikk bemutatja, hogyan használható az Azure-portálon DDoS-védelem engedélyezése, DDoS-védelem letiltásához, és telemetriai adatokat a támadás elhárítása érdekében. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="enable-ddos-protection"></a>DDoS-védelem engedélyezése

DDoS-védelem engedélyezése az új vagy meglévő virtuális hálózatot.

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>Hozzon létre egy új virtuális hálózatot és DDoS-védelem engedélyezése

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.
2. Válassza ki **hálózati**, majd válassza ki **virtuális hálózati**.
3. Adja meg a virtuális hálózati adatokat. A *DDoS-védelem*, kattintson a **engedélyezve**, és kattintson a **létrehozása**.

    ![Virtuális hálózat létrehozása](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

Figyelmeztetés jelzi, hogy DDoS-védelem engedélyezése terhel. Nincsenek terhelések a DDoS védelem előzetes felmerülő. Díjak sem merülnek fel a következő általános rendelkezésre állási (GA) és az ügyfelek díjakat és GA elindítása előtt 30 nappal értesítést fog kapni.

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>DDoS-védelem engedélyezése az egy meglévő virtuális hálózathoz 

1. Kattintson a **virtuális hálózatok** az az Azure-portál menüjében, majd válassza ki a virtuális hálózat.
2. Kattintson a **DDoS-védelem**, kattintson a **engedélyezve** a a *DDoS-védelem* képernyőn, és kattintson a **mentése**. 

Figyelmeztetés jelzi, hogy DDoS-védelem engedélyezése terhel. Nincsenek terhelések a DDoS védelem előzetes felmerülő. Díjak sem merülnek fel a következő általános rendelkezésre állási (GA) és az ügyfelek díjakat és GA elindítása előtt 30 nappal értesítést fog kapni.

## <a name="disable-ddos-protection"></a>DDoS-védelem letiltása

1. Kattintson a **virtuális hálózatok** az az Azure-portál menüjében, majd válassza ki a virtuális hálózat.
2. Kattintson a **DDoS-védelem**, kattintson a **letiltott** a a *DDoS-védelem* képernyőn, és kattintson a **mentése**.

## <a name="configure-alerts-on-ddos-protection-metrics"></a>DDoS-védelem mérőszámokat riasztások konfigurálása

Használhatja az Azure-figyelő riasztás konfigurálásában, kiválaszthatja bármelyik elérhető DDoS-védelem metrikai riasztást küld, ha egy aktív megoldás a támadás során. Ha a feltételek teljesülnek, figyelmeztető e-mailt a megadott címet kap.

1. Kattintson a **figyelő**, és kattintson a **metrikák**.
2. Az a *metrikák* képernyőn, válassza ki az erőforráscsoportot, erőforrás típusa **nyilvános IP-cím**, és az Azure nyilvános IP-cím.
3. Az e-mail riasztások olyan metrikajelentés konfigurálásához kattintson **riasztást hozzáadásához kattintson ide**. Az e-mail riasztások bármely metrika hozható létre, de a legnyilvánvalóbb mérték **alatt DDoS támadás, vagy nem**. Ez az egy logikai érték 1 vagy 0. A **1** azt jelenti, hogy támadás alatt áll. A **0** azt jelenti, hogy nem támadás alatt áll.
4. E-mailben támadás alatt, állítsa a metrikát **alatt DDoS támadás, vagy nem** és **nagyobb, mint nulla (0) az elmúlt 5 percben feltételt**. Az egyéb hasonló riasztások is beállítható.

    ![Metrikáinak megadása](./media/ddos-protection-manage-portal/ddos-metrics.png)

    Észlelt támadás, néhány percen belül értesítést kap Azure figyelő metrikák használatával.

    ![A támadás riasztás](./media/ddos-protection-manage-portal/ddos-alert.png) 

Még többet is megtudhat arról [konfigurálása webhookokkal](../monitoring-and-diagnostics/insights-webhooks-alerts.md) és [a logic apps](../logic-apps/logic-apps-what-are-logic-apps.md) riasztások létrehozásához.

## <a name="configure-logging-on-ddos-protection-metrics"></a>DDoS-védelem mérőszámokat naplózás konfigurálása

1. Kattintson a **figyelő**, és kattintson a **diagnosztikai beállítások**.
2. Az a *metrikák* képernyőn, válassza ki az erőforráscsoportot, erőforrás típusa **nyilvános IP-cím**, és az Azure nyilvános IP-cím.
3. Kattintson a **kapcsolja be a következő adatok gyűjtéséhez diagnosztika**.

Három lehetőség áll rendelkezésre a naplózás:

- **Archív tárfiókba** – tárfiókba menti el a naplókat.
- **Az eseményközpontok felé adatfolyam** – lehetővé teszi, hogy a napló fogadó átvételéhez eseményközpont használatával a naplókat. Ez lehetővé teszi az integrációt a Splunk vagy más SIEM-rendszerekről.
- **Naplóelemzési küldése** – naplók ír Azure OMS Naplóelemzés szolgáltatás.

## <a name="use-ddos-protection-telemetry"></a>DDoS-védelem telemetriai adatok használata

A támadás telemetriai valós idejű figyelése Azure keresztül valósul meg. A telemetriai adatok csak a megoldás alatt álló egy nyilvános IP-cím időtartamára érhető el. Telemetria előtt vagy után a támadás elhárítására nem látják.

1. Kattintson a **figyelő**, és kattintson a **metrikák**. 
2. Az a *metrikák* képernyőn, válassza ki az erőforráscsoportot, erőforrás típusa **nyilvános IP-cím**, és az Azure nyilvános IP-cím. Elérhető egy sorozatát a képernyő bal oldalán megjelenik. A metrikák kiválasztásakor van grafikon az Áttekintés képernyő Azure figyelő metrikák diagramban. 

A mérték nevének jelenlegi különböző csomagtípusok és bájt csomagok, és mindegyik metrikát a címkenevek az alapvető szerkezet az alábbiak szerint:

- **Eldobott címke nevét (pl. bejövő csomagok eldobott DDoS-):** a DDoS védelem rendszer által eldobott/törlődik csomagok száma.
- **Továbbított címke nevét (például: csomagokat továbbítani DDoS bejövő):** a DDoS rendszer, hogy a cél VIP – nem szűrt forgalmat továbbított csomagok száma.
- **Nincs címke nevét (például: bejövő csomagok DDoS):** a háttértisztítás rendszerbe – a csomagok összege képviselő kapott csomagok száma dobva, és továbbítja.

## <a name="next-steps"></a>Következő lépések

- [További tudnivalók az Azure diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [A Naplóelemzési az Azure storage naplóinak elemzése](../log-analytics/log-analytics-azure-storage.md)
- [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)