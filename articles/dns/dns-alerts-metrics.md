---
title: Az Azure DNS metrikák és riasztások |} Microsoft Docs
description: Tudnivalók az Azure DNS metrikák és a riasztásokat.
services: dns
documentationcenter: na
author: KumudD
manager: jennoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: kumud
ms.openlocfilehash: 54c4df446ee5c1bf8d29dd6c33b304f39ce8f1b8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="azure-dns-metrics-and-alerts"></a>Az Azure DNS-metrikák és riasztások
Az Azure DNS egy olyan üzemeltetési szolgáltatás DNS-tartományok, amely a névfeloldáshoz a Microsoft Azure infrastruktúra biztosít. Ez a cikk ismerteti a metrikák és a riasztások az Azure DNS-szolgáltatás.

## <a name="azure-dns-metrics"></a>Az Azure DNS-metrikák

Az Azure DNS biztosít az ügyfelek számára lehetővé teszi a DNS megfelelően a szolgáltatásban meghatározott aspektusait figyelheti metrikákat. Emellett az Azure DNS metrikák is konfigurálni és értesítéseket érdeklő feltételek alapján. A metrikák keresztül biztosított a [Azure figyelőszolgáltatás](../monitoring-and-diagnostics/index.yml). Az Azure DNS biztosít a DNS-zónák figyelése Azure keresztül a következő metrikákat:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

Azt is láthatja a [metrikákat meghatározását](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkdnszones) az Azure-figyelő dokumentáció oldalon.
>[!NOTE]
> Jelenleg a metrikák csak érhetők el az Azure DNS-ben üzemeltetett nyilvános DNS-zónák. Ha az Azure DNS-zónák saját vannak, ezeket a mérési nem adatot szolgáltatja majd azokat a zónákat. Emellett a metrikák és a riasztási szolgáltatás csak akkor támogatott a nyilvános Azure felhőbe. Szuverén felhők támogatása egy későbbi időpontban követi. 

A dimenzió a következő metrikák tekintetében részletes szintje DNS-zónát.

### <a name="query-volume"></a>Lekérdezés kötet

A *lekérdezés kötet* az Azure DNS-metrika DNS-lekérdezések (lekérdezés forgalom) a DNS-zóna Azure DNS által fogadott mennyiségét jeleníti meg. A mértékegységet száma és az összesítés nem haladhatja meg az összes egy meghatározott időtartamra vonatkozóan a lekérdezések. Ez a metrika megtekintéséhez jelölje ki az Azure portálon figyelő lapján (előzetes verzió) felületen. A erőforrás legördülő listán válassza ki a DNS-zónát, válassza ki a lekérdezés kötet, és válassza ki a Sum összesítésére az. Képernyőkép alább látható.  További információ a Metrikaböngésző észlelnek, és diagramkészítési, lásd: [Azure figyelő Metrikaböngésző](../monitoring-and-diagnostics/monitoring-metric-charts.md).

![Lekérdezés kötet](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Ábra: Az Azure DNS-lekérdezés kötet metrikák*

### <a name="record-set-count"></a>Set rekordszám
A *beállítása rekordszám* metrika Azure DNS rekordhalmazok számát mutatja a DNS-zónához. A rekordhalmazok, amelyek a zónához definiált összes bájtjai számítanak. A mértékegységet száma és az összesítés a rekordhalmazok maximális. Válassza ki, ha ez a metrika **metrikák (előzetes verzió)** explorer tapasztalatainak a **figyelő** fülre az Azure portálon. Válassza ki a DNS-zónát a a **erőforrás** legördülő listából válassza ki a **beállítása rekordszám** mérték, és adja **maximális** , a **összesítési** . További információ a Metrikaböngésző észlelnek, és diagramkészítési, lásd: [Azure figyelő Metrikaböngésző](../monitoring-and-diagnostics/monitoring-metric-charts.md). 

![Set rekordszám](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Ábra: Az Azure DNS beállítása rekordszám metrikák*


### <a name="record-set-capacity-utilization"></a>A rekordhalmaz kapacitáskihasználás
A *rekord beállítása tárolókapacitás kihasználtságát* metrika az Azure DNS-kihasználtsága a rekordhalmaz kapacitás, a DNS-zónák arányát tartalmazza. Minden DNS-zónát az Azure DNS rekordhalmazok a zóna számára engedélyezett maximális számát meghatározó rekordhalmaz korlátozni van (lásd: [DNS korlátok](dns-zones-records.md#limits)). Ezért ez a metrika elsajátíthatja, hogy milyen közel vannak a rekordhalmaz korlátot elérte-e. Például ha 500 rekordkészlet a DNS-zóna konfigurálva van, és a zóna 5000 alapértelmezett rekordhalmaz korlátot, a RecordSetCapacityUtilization metrika (amely 5000 elválasztó 500 által előállított) 10 %-os értékét fogja megjeleníteni. A Mértékegység **százalékos** és a **összesítési** típus **maximális**. Ez a metrika megtekintéséhez jelölje ki az Azure portálon figyelő lapján (előzetes verzió) felületen. A erőforrás legördülő listán válassza ki a DNS-zónát, válassza ki a rekord beállítása tárolókapacitás használatát, és jelölje ki az összesítés maximális. Képernyőkép alább látható. További információ a Metrikaböngésző észlelnek, és diagramkészítési, lásd: [Azure figyelő Metrikaböngésző](../monitoring-and-diagnostics/monitoring-metric-charts.md). 

![Set rekordszám](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Ábra: Az Azure DNS rekord beállítása tárolókapacitás kihasználtságát metrikák*

## <a name="alerts-in-azure-dns"></a>Az Azure DNS riasztások
Az Azure figyelő lehetővé teszi a riasztás elérhető metrika értékekkel. A DNS-metrikák érhetők el az Új riasztási konfigurációs felületet. Ennek a részletes ismertetését lásd a [Azure a figyelő riasztást küld a dokumentáció](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md), válassza ki a DNS-zóna erőforrásként, válassza ki a metrika jel és a riasztási logika és más paramétereket konfigurálása, mint **időszak**és **gyakoriság**. További definiálhat egy [művelet csoport](../monitoring-and-diagnostics/monitoring-action-groups.md) számára, amikor a riasztási feltétel teljesül, amelynek során a riasztás kézbesíti a rendszer a kiválasztott műveletek keresztül. Azure figyelő metrikák vonatkozó riasztások elküldésére konfigurálásával kapcsolatos további információkért lásd: [létrehozása, megtekintése és kezelése az Azure-figyelővel riasztások](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>További lépések
- További információ [Azure DNS](dns-overview.md).
