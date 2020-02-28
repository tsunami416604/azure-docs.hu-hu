---
title: Mi az Azure Monitor-beli virtuális gépek (előzetes verzió)? | Microsoft Docs
description: Az Azure-beli virtuális gépek állapotát és teljesítményét figyelő Azure Monitor for VMs áttekintése az alkalmazás-összetevők és függőségeik automatikus felfedése és hozzárendelése mellett.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 1dcce3ab9f975fcf5910c382df3489d5d4ed425a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670665"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Mi az Azure Monitor-beli virtuális gépek (előzetes verzió)?

A virtuális gépek az Azure Monitor figyeli az Azure-beli virtuális gépek (VM), és a virtuálisgép-méretezési csoportok ipari méretekben. A szolgáltatás elemzi a Windows és Linux rendszerű virtuális gépek teljesítményét és állapotát, valamint figyeli folyamataikat és a más erőforrásokkal és külső folyamatokkal kapcsolatos függőségeiket. 

Támogatja a helyszíni vagy más felhőalapú virtuális gépek teljesítményének és alkalmazási függőségeinek figyelését. A következő főbb funkciók részletes elemzést nyújtanak:

- **Előre meghatározott trend Performance-diagramok**: a vendég virtuális gép operációs rendszerének alapteljesítmény-mérőszámait jeleníti meg.

- **Függőségi Térkép**: megjeleníti az összekapcsolt összetevőket a virtuális géppel különböző erőforráscsoportok és előfizetések között.  

>[!NOTE]
>A közelmúltban bejelentettük, hogy a nyilvános előzetes verzió ügyfeleinktől kapott visszajelzések alapján az állapotfigyelő funkció [bekövetkezett](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) . A változtatások száma miatt a rendszer leállítja az új ügyfelek számára az állapotfigyelő funkciót. A meglévő ügyfelek továbbra is használhatják az állapot funkciót. További részletekért tekintse meg az [általános elérhetőséggel kapcsolatos gyakori kérdéseket](vminsights-ga-release-faq.md).  

A Azure Monitor naplókkal való integráció hatékony összesítést és szűrést tesz lehetővé, és képes elemezni az adattrendeket az idő múlásával. Az ilyen átfogó számítási feladatok figyelése nem érhető el Azure Monitor vagy Service Map önmagában.  

Ezeket az adatokat közvetlenül a virtuális gépről tekintheti meg egyetlen virtuális gépen, vagy használhatja a Azure Monitort a virtuális gépek összesített nézetének továbbítására, ahol a nézet támogatja az Azure erőforrás-környezet vagy munkaterület-környezet üzemmódot. További információ: [hozzáférési módok áttekintése](../platform/design-logs-deployment.md#access-mode).

![Az Azure Portalon a virtuális gép insights perspektíva](./media/vminsights-overview/vminsights-azmon-directvm.png)

A Azure Monitor for VMs kiszámítható teljesítményt és rendelkezésre állást biztosíthat a létfontosságú alkalmazások számára. A teljesítmény szűk keresztmetszeteit és a hálózati problémákat azonosítja. A Azure Monitor for VMs segíthet megérteni, hogy a probléma más függőségekhez kapcsolódik-e.  

## <a name="data-usage"></a>Adathasználat

Az Azure Monitor-beli virtuális gépek telepítésekor a virtuális gépek által összegyűjtött adatok betöltött és az Azure Monitor tárolja. A begyűjtött teljesítmény-és függőségi adatokat egy Log Analytics munkaterületen tárolja a rendszer. A [Azure monitor díjszabási oldalán](https://azure.microsoft.com/pricing/details/monitor/)közzétett díjszabás alapján Azure monitor for VMS a következő díjat számítjuk fel:

- Az adatokat, amelyeket betöltött tárolja.
- A riasztási szabályok, amelyek akkor jönnek létre.
- Az értesítéseket küldött. 

A napló mérete a teljesítményszámlálók hosszának megfelelően változik, és növelheti a virtuális gép számára lefoglalt logikai lemezek és hálózati adapterek számát. Ha már rendelkezik egy munkaterületet, és ezeket a számlálókat gyűjti, nem ismétlődő díjak érvényesek. A Service Map már használ, az egyetlen változás megjelenik-e a kapcsolat további adatokat az Azure Monitor küldött.

## <a name="next-steps"></a>Következő lépések

A virtuális gépek figyeléséhez szükséges követelmények és módszerek megismeréséhez tekintse át a [Azure monitor for VMS üzembe helyezését ismertető témakört](vminsights-enable-overview.md).
