---
title: Adatgyűjtés konfigurálása a Azure Monitor-ügynökhöz (előzetes verzió)
description: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: 3cd2ed692f3a34223675da69efd92e78c2ba9504
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88083068"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Adatgyűjtés konfigurálása a Azure Monitor-ügynökhöz (előzetes verzió)
Az adatgyűjtési szabályok (DCR) a Azure Monitorba érkező és a küldendő adatforrásokat határozzák meg. Ez a cikk azt ismerteti, hogyan lehet adatgyűjtési szabályt létrehozni a virtuális gépekről az Azure Monitor ügynök használatával történő adatgyűjtéshez.

Az adatgyűjtési szabályok teljes leírását lásd: [adatgyűjtési szabályok a Azure monitorban (előzetes verzió)](data-collection-rule-overview.md).

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan konfigurálhatja a virtuális gépeket a jelenleg előzetes verzióban elérhető Azure Monitor ügynökkel. Az általánosan elérhető ügynökök leírását, valamint az adatok gyűjtését az [Azure monitor ügynökök áttekintését](agents-overview.md) ismertető cikkben talál.


## <a name="dcr-associations"></a>DCR-társítások
A DCR virtuális gépre való alkalmazásához létre kell hoznia egy társítást a virtuális géphez. Előfordulhat, hogy egy virtuális gép több DCR is társítható, és a DCR-hez több virtuális gép is tartozhat. Ez lehetővé teszi, hogy Definiáljon egy adott követelménynek megfelelő DCR-készletet, és azokat csak azokra a virtuális gépekre alkalmazza, amelyekre érvényesek. 

Vegyünk például egy olyan környezetet, amely egy üzletági alkalmazást futtató virtuális gépeket és a SQL Servert futtató többi alkalmazást futtat. Lehet, hogy egy alapértelmezett adatgyűjtési szabály vonatkozik az összes virtuális gépre, és elkülöníti az adatgyűjtési szabályokat, amelyek kifejezetten az üzletági alkalmazásra és a SQL Serverre vonatkozó adatokat gyűjtenek. Az adatgyűjtési szabályokhoz tartozó virtuális gépek társításai az alábbi ábrához hasonlóan jelennek meg.

![Szövetségek](media/data-collection-rule-azure-monitor-agent/associations.png)

## <a name="create-using-the-azure-portal"></a>Létrehozás az Azure Portal használatával
A Azure Portal használatával létrehozhat egy adatgyűjtési szabályt, és az előfizetésében lévő virtuális gépeket hozzárendelheti a szabályhoz. A rendszer automatikusan telepíti a Azure Monitor ügynököt, és felügyelt identitást hozott létre minden olyan virtuális géphez, amelyen még nincs telepítve.

A Azure Portal **Azure monitor** menüjében válassza az **adatgyűjtési szabályok** lehetőséget a **Beállítások** szakaszban. Új adatgyűjtési szabály és hozzárendelés hozzáadásához kattintson a **Hozzáadás** gombra.

[![Adatgyűjtés szabályai](media/azure-monitor-agent/data-collection-rules.png)](media/azure-monitor-agent/data-collection-rules.png#lightbox)

Új szabály és társítások létrehozásához kattintson a **Hozzáadás** gombra. Adja meg a **szabály nevét** , és adja meg az **előfizetést** és az **erőforráscsoportot**. Itt adható meg, hogy a rendszer hová hozza létre a DCR-t. A virtuális gépek és azok társításai a bérlő bármelyik előfizetésében vagy erőforráscsoporthoz tartozhatnak.

[![Az adatgyűjtési szabály alapjai](media/azure-monitor-agent/data-collection-rule-basics.png)](media/azure-monitor-agent/data-collection-rule-basics.png#lightbox)

A **Virtual Machines (virtuális gépek** ) lapon adja hozzá azokat a virtuális gépeket, amelyeknek alkalmaznia kell az adatgyűjtési szabályt. A Azure Monitor ügynök a még nem telepített virtuális gépekre lesz telepítve.

[![Adatgyűjtési szabály virtuális gépei](media/azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

Az adatgyűjtés **és** küldés lapon kattintson az **adatforrás hozzáadása** lehetőségre egy adatforrás és egy célhely hozzáadásához. Válasszon ki egy **adatforrást**, és válassza ki a megfelelő adatokat a kiválasztáshoz. A teljesítményszámlálók esetében kiválaszthatja az objektumok előre meghatározott készletét és mintavételi sebességét. Eseményeknél naplókat vagy létesítményeket, valamint a súlyossági szintet is kiválaszthatja. 

[![Alapszintű adatforrás](media/azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


Más naplók és teljesítményszámlálók megadásához válassza az **Egyéni**lehetőséget. Ezután megadhat egy [XPath](https://www.w3schools.com/xml/xpath_syntax.asp) -értéket a gyűjtött értékek bármelyikéhez. Példák: példa a [DCR](data-collection-rule-overview.md#sample-data-collection-rule) -re.

[![Adatforrás egyéni](media/azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

A **cél** lapon adjon hozzá egy vagy több célhelyet az adatforrás számára. A Windows-események és a syslog-adatforrások csak Azure Monitor naplókba küldhetők el. A teljesítményszámlálók mindkét Azure Monitor metrikára és Azure Monitor naplókra is küldhetnek.

[![Cél](media/azure-monitor-agent/data-collection-rule-destination.png)](media/azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Kattintson az **adatforrás hozzáadása** lehetőségre, majd **tekintse át a + létrehozás** lehetőséget, és tekintse át az adatgyűjtési szabály és a virtuális gépek készletével való társítás részleteit. Kattintson a **Létrehozás** elemre a létrehozásához.

> [!NOTE]
> Az adatgyűjtési szabály és a társítások létrehozása után akár 5 percet is igénybe vehet, amíg a célhelyek el nem jutnak.


## <a name="next-steps"></a>További lépések

- További információ a [Azure monitor-ügynökről](azure-monitor-agent-overview.md).
- További információ az [adatgyűjtési szabályokról](data-collection-rule-overview.md).
