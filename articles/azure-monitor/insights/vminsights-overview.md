---
title: Mi az Azure Monitor-beli virtuális gépek (előzetes verzió)? | Microsoft Docs
description: Az Azure-beli virtuális gépek állapotát és teljesítményét figyelő Azure Monitor for VMs áttekintése az alkalmazás-összetevők és függőségeik automatikus felfedése és hozzárendelése mellett.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/14/2019
ms.openlocfilehash: e9a3df0172f276800a3049675f6e858db7f0370c
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307300"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Mi az Azure Monitor-beli virtuális gépek (előzetes verzió)?

A virtuális gépek az Azure Monitor figyeli az Azure-beli virtuális gépek (VM), és a virtuálisgép-méretezési csoportok ipari méretekben. A szolgáltatás elemzi a Windows és Linux rendszerű virtuális gépek teljesítményét és állapotát, valamint figyeli folyamataikat és a más erőforrásokkal és külső folyamatokkal kapcsolatos függőségeiket. 

Támogatja a helyszíni vagy más felhőalapú virtuális gépek teljesítményének és alkalmazási függőségeinek figyelését. A következő főbb funkciók részletes elemzést nyújtanak:

- **Előre meghatározott trend Performance-diagramok**: a vendég virtuális gép operációs rendszerének alapteljesítmény-mérőszámait jeleníti meg.

- **Függőségi térkép**: a virtuális géppel a különböző erőforráscsoportokban és előfizetésekben összekapcsolt összetevőket jeleníti meg.  

>[!NOTE]
>A közelmúltban bejelentettük, hogy a nyilvános előzetes verzió ügyfeleinktől kapott visszajelzések alapján az állapotfigyelő funkció [bekövetkezett](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) . A változtatások száma miatt a rendszer leállítja az új ügyfelek számára az állapotfigyelő funkciót. A meglévő ügyfelek továbbra is használhatják az állapot funkciót. További részletekért tekintse meg az [általános elérhetőséggel kapcsolatos gyakori kérdéseket](vminsights-ga-release-faq.md).  

A Azure Monitor naplókkal való integráció hatékony összesítést és szűrést tesz lehetővé, és képes elemezni az adattrendeket az idő múlásával. Az ilyen átfogó számítási feladatok figyelése nem érhető el Azure Monitor vagy Service Map önmagában.  

Ezeket az adatokat közvetlenül a virtuális gépről tekintheti meg egyetlen virtuális gépen, vagy használhatja a Azure Monitort a virtuális gépek összesített nézetének továbbítására, ahol a nézet támogatja az Azure erőforrás-környezet vagy munkaterület-környezet üzemmódot. További információ: [hozzáférési módok áttekintése](../platform/design-logs-deployment.md#access-mode).

![Az Azure Portalon a virtuális gép insights perspektíva](./media/vminsights-overview/vminsights-azmon-directvm.png)

A Azure Monitor for VMs kiszámítható teljesítményt és rendelkezésre állást biztosíthat a létfontosságú alkalmazások számára. A teljesítmény szűk keresztmetszeteit és a hálózati problémákat azonosítja. A Azure Monitor for VMs segíthet megérteni, hogy a probléma más függőségekhez kapcsolódik-e.  

## <a name="data-usage"></a>Adathasználat

Az Azure Monitor-beli virtuális gépek telepítésekor a virtuális gépek által összegyűjtött adatok betöltött és az Azure Monitor tárolja. A begyűjtött teljesítmény-és függőségi adatokat egy Log Analytics munkaterületen tárolja a rendszer. A közzétett díjszabás alapján a [díjszabását ismertető oldalt az Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/), a virtuális gépek az Azure Monitor alapján:

- Az adatokat, amelyeket betöltött tárolja.
- A riasztási szabályok, amelyek akkor jönnek létre.
- Az értesítéseket küldött. 

A napló mérete a teljesítményszámlálók hosszának megfelelően változik, és növelheti a virtuális gép számára lefoglalt logikai lemezek és hálózati adapterek számát. Ha már rendelkezik egy munkaterületet, és ezeket a számlálókat gyűjti, nem ismétlődő díjak érvényesek. A Service Map már használ, az egyetlen változás megjelenik-e a kapcsolat további adatokat az Azure Monitor küldött.

## <a name="next-steps"></a>Következő lépések

A követelmények és a módszereket, amelyek segítenek a virtuális gépek figyelése céljából, tekintse át [-beli virtuális gépek üzembe helyezése az Azure Monitor](vminsights-enable-overview.md).
