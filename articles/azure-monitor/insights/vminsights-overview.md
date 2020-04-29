---
title: Mi az Azure Monitor for VMs?
description: Az Azure-beli virtuális gépek állapotát és teljesítményét figyelő Azure Monitor for VMs áttekintése az alkalmazás-összetevők és függőségeik automatikus felfedése és hozzárendelése mellett.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/11/2020
ms.openlocfilehash: 6f65ba96f768a4b88aa13c073cc66920b8c5e049
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480487"
---
# <a name="what-is-azure-monitor-for-vms"></a>Mi az Azure Monitor for VMs?

Azure Monitor for VMs az Azure-beli virtuális gépeket (VM) és a virtuálisgép-méretezési csoportokat nagy méretben figyeli. A szolgáltatás elemzi a Windows és Linux rendszerű virtuális gépek teljesítményét és állapotát, valamint figyeli folyamataikat és a más erőforrásokkal és külső folyamatokkal kapcsolatos függőségeiket. Támogatja a helyszíni vagy más felhőalapú virtuális gépek teljesítményének és alkalmazási függőségeinek figyelését. A következő főbb funkciók részletes elemzést nyújtanak:

- **Előre meghatározott trend Performance-diagramok**: a vendég virtuális gép operációs rendszerének alapteljesítmény-mérőszámait jeleníti meg.

- **Függőségi Térkép**: megjeleníti az összekapcsolt összetevőket a virtuális géppel különböző erőforráscsoportok és előfizetések között.  

>[!NOTE]
>A közelmúltban bejelentettük, hogy a nyilvános előzetes verzió ügyfeleinktől kapott visszajelzések alapján az állapotfigyelő funkció [bekövetkezett](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) . A változtatások száma miatt a rendszer leállítja az új ügyfelek számára az állapotfigyelő funkciót. A meglévő ügyfelek továbbra is használhatják az állapot funkciót. További részletekért tekintse meg az [általános elérhetőséggel kapcsolatos gyakori kérdéseket](vminsights-ga-release-faq.md).  

A Azure Monitor naplókkal való integráció hatékony összesítést és szűrést tesz lehetővé, így az Azure Monitor for VMs az adattrendeket az idő múlásával elemezheti. Ezeket az adatokat közvetlenül a virtuális gépről tekintheti meg egyetlen virtuális gépen, vagy használhatja a Azure Monitort a virtuális gépek összesített nézetének továbbítására, ahol a nézet támogatja az Azure erőforrás-környezet vagy munkaterület-környezet üzemmódot. További információ: [hozzáférési módok áttekintése](../platform/design-logs-deployment.md#access-mode).

![Virtuális gépekkel kapcsolatban bepillantást nyerhet a Azure Portal](media/vminsights-overview/vminsights-azmon-directvm.png)

A Azure Monitor for VMs kiszámítható teljesítményt és rendelkezésre állást biztosíthat a létfontosságú alkalmazások számára. A teljesítmény szűk keresztmetszeteit és hálózati problémáit azonosítja, és az is segít megérteni, hogy a probléma más függőségekhez kapcsolódik-e.  

## <a name="data-usage"></a>Adathasználat

Azure Monitor for VMs telepítésekor a virtuális gépek által összegyűjtött adatok betöltése és tárolása Azure Monitor történik. A begyűjtött teljesítmény-és függőségi adatokat egy Log Analytics munkaterületen tárolja a rendszer. A [Azure monitor díjszabási oldalán](https://azure.microsoft.com/pricing/details/monitor/)közzétett díjszabás alapján Azure monitor for VMS a következő díjat számítjuk fel:

- A betöltött és tárolt adatmennyiség.
- A létrehozott riasztási szabályok.
- Az elküldött értesítések. 

A napló mérete a teljesítményszámlálók hosszának megfelelően változik, és növelheti a virtuális gép számára lefoglalt logikai lemezek és hálózati adapterek számát. Ha már rendelkezik munkaterülettel, és ezeket a számlálókat gyűjti, a rendszer nem alkalmaz duplikált díjat. Ha már használja a Service Map-t, az egyetlen változás, amelyet a Azure Monitor számára továbbított további kapcsolódási információk fognak látni.

## <a name="next-steps"></a>További lépések

A virtuális gépek figyeléséhez szükséges követelmények és módszerek megismeréséhez tekintse át a [Azure monitor for VMS üzembe helyezését ismertető témakört](vminsights-enable-overview.md).
