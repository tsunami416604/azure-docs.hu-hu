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
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: 69aa2cbcaa6861b1d5c5c71769be2fb8046d9ea5
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53188505"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Mi az Azure Monitor-beli virtuális gépek (előzetes verzió)?

A virtuális gépek az Azure Monitor figyeli az Azure-beli virtuális gépek (VM), és a virtuálisgép-méretezési csoportok ipari méretekben. A szolgáltatás elemzi a teljesítmény és a Windows és Linux rendszerű virtuális gépek, a folyamatok és az egyéb erőforrások és a külső folyamatok függőségi figyelési állapotát. 

Az Azure Monitor-beli virtuális gépek a megoldás, a teljesítmény- és alkalmazásfüggőségek figyelés, a virtuális gépek, amelyek üzemeltethető a helyszínen vagy egy másik felhőszolgáltatóval támogatását is tartalmazza. Három főbb funkcióiról részletes betekintést biztosít:

* **Windows és Linux rendszerű Azure virtuális gépek logikai összetevőit**: Előre konfigurált állapotára vonatkozó feltételek, elleni mérése történik, és azok riasztást küld, ha a kiértékelt feltétel nem teljesül.  

* **Előre meghatározott, népszerű teljesítménydiagramok**: A Vendég virtuális gép operációs rendszerről alapvető teljesítmény-mérőszámait jeleníti meg.

* **Függőségi térkép**: A virtuális géppel a különböző erőforráscsoportokban és előfizetésekben összekapcsolt összetevőket jeleníti meg.  

Az funkciók vannak szervezve három szempontok:

* Állapot
* Teljesítmény
* Térkép

>[!NOTE]
>Jelenleg az állapotfigyelő szolgáltatás kínáljuk csak Azure-beli virtuális gépek és virtuálisgép-méretezési csoportok. A teljesítmény és a térkép funkciója a környezet vagy a más felhőszolgáltatók támogatja az Azure virtuális gépek és a futó virtuális gépekről.

A Log Analytics-integráció biztosít hatékony összesítési és szűrés, és idővel elemezheti adatait trendeket. Az ilyen az átfogó munkaterhelés-figyelés nem érhető el az Azure Monitor, a Service Map vagy önálló Log Analytics.  

Megtekintheti az adatok közvetlenül a virtuális gép egyetlen virtuális gépen, vagy használhatja az Azure Monitor, hogy a virtuális gépek összesített nézete. Ebben a nézetben minden egyes szolgáltatás perspektíva alapul:

* **Egészségügyi**: A virtuális gépek egy erőforráscsoporthoz kapcsolódnak.
* **Térkép** és **teljesítmény**: A virtuális gépek a jelentés egy adott Log Analytics-munkaterületet úgy van beállítva.

![Az Azure Portalon a virtuális gép insights perspektíva](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Az Azure DevOps is biztosít, kiszámítható teljesítményt és a fontos alkalmazások rendelkezésre állását. Az operációs rendszer kritikus események, teljesítmény szűk keresztmetszeteket és hálózati problémák azonosítja. Az Azure DevOps segítségével megismerheti, hogy más függőségek kapcsolatos probléma.  

## <a name="data-usage"></a>Adathasználat 

Az Azure Monitor-beli virtuális gépek telepítésekor a virtuális gépek által összegyűjtött adatok betöltött és az Azure Monitor tárolja. A közzétett díjszabás alapján a [díjszabását ismertető oldalt az Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/), a virtuális gépek az Azure Monitor alapján:
* Az adatokat, amelyeket betöltött tárolja.
* A health feltételek metrikát idősorozat-figyelt száma.
* A riasztási szabályok, amelyek akkor jönnek létre.
* Az értesítéseket küldött. 

A karakterlánc hosszának számlálók eltérő a napló méretét, és azt a logikai lemezek és hálózati adapterek száma növelhető. Ha már rendelkezik egy munkaterületet, és ezeket a számlálókat gyűjti, nem ismétlődő díjak érvényesek. A Service Map már használ, az egyetlen változás megjelenik-e a kapcsolat további adatokat az Azure Monitor küldött.

## <a name="next-steps"></a>További lépések
A követelmények és a módszereket, amelyek segítenek a virtuális gépek figyelése céljából, tekintse át [-beli virtuális gépek üzembe helyezése az Azure Monitor](vminsights-onboard.md).
