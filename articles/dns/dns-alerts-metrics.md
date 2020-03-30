---
title: Metrikák és riasztások – Azure DNS
description: Ezzel a tanulási útvonallal ismerkedje meg az Azure DNS-mérőszámokkal és riasztásokkal.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: rohink
ms.openlocfilehash: 42acbc0d32b3ce5de4befcf112b68f611ad70542
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937455"
---
# <a name="azure-dns-metrics-and-alerts"></a>Azure DNS-metrikák és riasztások
Az Azure DNS egy olyan DNS-tartományok üzemeltetési szolgáltatása, amely a Microsoft Azure-infrastruktúra használatával névfeloldást biztosít. Ez a cikk az Azure DNS-szolgáltatás metrikáit és riasztásait ismerteti.

## <a name="azure-dns-metrics"></a>Azure DNS-metrikák

Az Azure DNS metrikákat biztosít az ügyfelek számára, amelyek lehetővé teszik számukra a szolgáltatásban üzemeltetett DNS-zónáik bizonyos aspektusainak figyeléséhez. Emellett az Azure DNS-metrikák, konfigurálhatja és fogadhat riasztásokat az érdeklődési feltételek alapján. A metrikák az [Azure Monitor szolgáltatáson](../azure-monitor/index.yml)keresztül vannak biztosítva. Az Azure DNS a következő mérőszámokat biztosítja az Azure Monitoron keresztül a DNS-zónákhoz:

-   QueryVolume (Lekérdezési kötet)
-   Rekordsor száma
-   RecordSetCapacityKihasználtság

Ezek a [metrikák definícióját](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) is megtekintheti az Azure Monitor dokumentációs oldalán.
>[!NOTE]
> Jelenleg ezek a metrikák csak az Azure DNS-ben üzemeltetett nyilvános DNS-zónákhoz érhetők el. Ha az Azure DNS-ben tárolt privát zónák, ezek a metrikák nem ad nak adatokat az adott zónák. Emellett a metrikák és a riasztási funkció csak az Azure Public cloud támogatott. A szuverén felhők támogatása egy későbbi időpontban következik. 

A legrészletesebb elem, amely a metrikák at láthatja, egy DNS-zóna. Jelenleg nem látható a zónákon belüli egyes erőforrásrekordok metrikákszáma.

### <a name="query-volume"></a>Lekérdezési kötet

Az Azure *DNS-ben* a Lekérdezési kötet metrika az Azure DNS-zónához kapott DNS-lekérdezések (lekérdezési forgalom) mennyiségét jeleníti meg. A mértékegység a Darabszám, és az összesítés az egy adott időszakban kapott összes lekérdezés összege. 

A metrika megtekintéséhez válassza a Metrikák (előzetes) explorer-élmény az Azure Portal Figyelő lapján. Válassza ki a DNS-zónát az Erőforrás legördülő menüből, válassza ki a Lekérdezési kötet metrikát, és válassza az Összeg összesítésként lehetőséget. A képernyőkép alatt egy példa látható.  A Metrikakezelő felületével és a diagramkészítéssel kapcsolatos további információkért lásd: [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md).

![Lekérdezési kötet](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Ábra: Azure DNS-lekérdezési kötet metrikák*

### <a name="record-set-count"></a>Rekordkészlet száma
A *Rekordkészlet-számláló* metrika az Azure DNS-ben a DNS-zónában lévő rekordhalmazok számát mutatja. A zónában definiált összes rekordhalmaz tszámít. A mértékegység a Darabszám, az összesítés pedig az összes rekordhalmaz maximális száma. A metrika megtekintéséhez válassza **a Metrikák (előzetes)** explorer-élmény az Azure Portal **Figyelő** lapján. Válassza ki a DNS-zónát az **Erőforrás** legördülő menüből, válassza a **Rekordkészlet száma** mérőszámot, majd válassza a **Max** **választógombot összesítésként.** A Metrikakezelő felületével és a diagramkészítéssel kapcsolatos további információkért lásd: [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md). 

![Rekordkészlet száma](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Ábra: Az Azure DNS-rekordok készletszám-mérőszámai*


### <a name="record-set-capacity-utilization"></a>Rekordkészlet kapacitáskihasználtsága
Az Azure *DNS-ben a rekordkészlet kapacitáskihasználási* metrikája a rekordkészlet-kapacitás kihasználtságának százalékos arányát mutatja. Az Azure DNS minden DNS-zónájára olyan Recordset-korlát vonatkozik, amely meghatározza a zóna számára engedélyezett rekordhalmazok maximális számát (lásd: [DNS-korlátok).](dns-zones-records.md#limits) Ezért ez a mérőszám megmutatja, hogy milyen közel van a Rekordhalmaz-korlát elérése. Ha például 500 rekordhalmaz van konfigurálva a DNS-zónához, és a zóna alapértelmezett Recordset limitje 5000, a RecordSetCapacityIntilization metrika 10%-os értéket jelenít meg (amelyet az 500 és 5000 arány felosztásával kapunk). A mértékegység **százalék,** az **Összesítés** típusa pedig **Maximum.** A metrika megtekintéséhez válassza a Metrikák (előzetes) explorer-élmény az Azure Portal Figyelő lapján. Válassza ki a DNS-zónát az Erőforrás legördülő menüből, válassza ki a Rekordkészlet kapacitáskihasználtsági mérőszámát, és válassza a Max választógombot összesítésként. A képernyőkép alatt egy példa látható. A Metrikakezelő felületével és a diagramkészítéssel kapcsolatos további információkért lásd: [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md). 

![Rekordkészlet száma](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Ábra: Azure DNS-rekordkészlet kapacitáskihasználtsági metrikák*

## <a name="alerts-in-azure-dns"></a>Riasztások az Azure DNS-ben
Az Azure Monitor lehetővé teszi a rendelkezésre álló metrikaértékek riasztását. A DNS-metrikák az új riasztási konfigurációs élményben érhetők el. Az Azure Monitor [riasztási dokumentációjában](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)részletesen leírtak szerint kiválaszthatja a DNS-zónát erőforrásként, kiválaszthatja a Metrika jeltípusát, és konfigurálhatja a riasztási logikát és más paramétereket, például **az időszakot** és **a gyakoriságot.** További cselekvési [csoportot](../azure-monitor/platform/action-groups.md) határozhat meg arra az időpontra, amikor a riasztási feltétel teljesül, és amelynek során a riasztás a kiválasztott műveleteken keresztül lesz kézbesítve. Az Azure Monitor metrikák riasztásának konfigurálásáról a [Riasztások létrehozása, megtekintése és kezelése az Azure Monitor használatával című](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)témakörben talál további információt. 

## <a name="next-steps"></a>További lépések
- További információ az [Azure DNS-ről.](dns-overview.md)
