---
title: Mi az Azure Monitor-beli virtuális gépek (előzetes verzió)? | Microsoft Docs
description: A virtuális gépek az Azure Monitor az egyik szolgáltatása, amely egyesíti az állapotának és teljesítményének figyelése az Azure virtuális gép operációs rendszerének, valamint alkalmazás-összetevők és az egyéb erőforrások függőségeinek automatikus felderítése, és feltérképezi a kommunikációt az Azure Monitor közöttük. Ez a cikk áttekintést nyújt.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2019
ms.author: magoedte
ms.openlocfilehash: f7a0300619d82f760c0e307601efbd3987eb6067
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004585"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Mi az Azure Monitor-beli virtuális gépek (előzetes verzió)?

A virtuális gépek az Azure Monitor figyeli az Azure-beli virtuális gépek (VM), és a virtuálisgép-méretezési csoportok ipari méretekben. Az eszköz elemzi a teljesítmény és a Windows és Linux rendszerű virtuális gépek állapotát, és a folyamatok és az egyéb erőforrások és a külső folyamatok függőségeit figyeli. 

Az Azure Monitor-beli virtuális gépek a megoldás, a teljesítmény- és alkalmazásfüggőségek figyelés, a virtuális gépek, amelyek üzemeltethető a helyszínen vagy egy másik felhőszolgáltatóval támogatását is tartalmazza. Három főbb funkcióiról részletes betekintést biztosít:

* **Windows és Linux rendszerű Azure virtuális gépek logikai összetevőit**: Előre konfigurált állapotára vonatkozó feltételek, elleni mérése történik, és azok riasztást küld, ha a kiértékelt feltétel nem teljesül.  

* **Előre definiált felkapott teljesítménydiagramok**: A Vendég virtuális gép operációs rendszerről alapvető teljesítmény-mérőszámait jeleníti meg.

* **Függőségi térkép**: A virtuális géppel a különböző erőforráscsoportokban és előfizetésekben összekapcsolt összetevőket jeleníti meg.  

Az funkciók vannak szervezve három szempontok:

* Állapot
* Teljesítmény
* Térkép

>[!NOTE]
>Jelenleg az állapotfigyelő szolgáltatás kínáljuk csak Azure-beli virtuális gépek és virtuálisgép-méretezési csoportok. Teljesítményt és térkép funkciókat támogatja az Azure virtuális gépek és a futó virtuális gépekről a környezet vagy más felhőszolgáltatónál.

Integráció az Azure Monitor naplóira biztosít hatékony összesítő és a szűrés, és idővel elemezheti adatait trendeket. Az ilyen az átfogó munkaterhelés-figyelés nem érhető el az Azure Monitor vagy a Service Map önmagában.  

Megtekintheti az adatok közvetlenül a virtuális gép egyetlen virtuális gépen, vagy használhatja az Azure Monitor, hogy a virtuális gépek összesített nézete. Ebben a nézetben minden egyes szolgáltatás perspektíva alapul:

* **Egészségügyi**: A virtuális gépek egy erőforráscsoporthoz kapcsolódnak.
* **Térkép** és **teljesítmény**: A virtuális gépek a jelentés egy adott Log Analytics-munkaterületet úgy van beállítva.

![Az Azure Portalon a virtuális gép insights perspektíva](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

A virtuális gépek az Azure Monitor továbbítható kiszámítható teljesítményt és a fontos alkalmazások rendelkezésre állását. Az operációs rendszer kritikus események, teljesítmény szűk keresztmetszeteket és hálózati problémák azonosítja. A virtuális gépek az Azure Monitor segítségével megismerheti, hogy más függőségek kapcsolatos probléma.  

## <a name="data-usage"></a>Adathasználat 

Az Azure Monitor-beli virtuális gépek telepítésekor a virtuális gépek által összegyűjtött adatok betöltött és az Azure Monitor tárolja. Feltételek mérőszámok az Azure Monitor egy idősorozat-adatbázisában tárolódnak, a Log Analytics-munkaterületen gyűjtött teljesítmény- és a függőségi adatok tárolódnak. A közzétett díjszabás alapján a [díjszabását ismertető oldalt az Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/), a virtuális gépek az Azure Monitor alapján:

* Az adatokat, amelyeket betöltött tárolja.
* A health feltételek metrikát idősorozat-figyelt száma.
* A riasztási szabályok, amelyek akkor jönnek létre.
* Az értesítéseket küldött. 

A napló mérete teljesítményszámláló-karakterlánc hossza az függően változik, és azt a logikai lemezek és a virtuális géphez hozzárendelt hálózati adapterek száma növelhető. Ha már rendelkezik egy munkaterületet, és ezeket a számlálókat gyűjti, nem ismétlődő díjak érvényesek. A Service Map már használ, az egyetlen változás megjelenik-e a kapcsolat további adatokat az Azure Monitor küldött.

## <a name="next-steps"></a>További lépések
A követelmények és a módszereket, amelyek segítenek a virtuális gépek figyelése céljából, tekintse át [-beli virtuális gépek üzembe helyezése az Azure Monitor](vminsights-onboard.md).
