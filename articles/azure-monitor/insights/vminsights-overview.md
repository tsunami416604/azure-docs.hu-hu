---
title: Mi az Azure Monitor virtuális gépekhez?
description: Az Azure Monitor virtuális gépekhez, amely figyeli az Azure virtuális gépek állapotát és teljesítményét, valamint automatikusan felfigyeli és leképezi az alkalmazás-összetevőket és azok függőségeit.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/11/2020
ms.openlocfilehash: 6f65ba96f768a4b88aa13c073cc66920b8c5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480487"
---
# <a name="what-is-azure-monitor-for-vms"></a>Mi az Azure Monitor virtuális gépekhez?

Az Azure Monitor virtuális gépekfigyeli az Azure virtuális gépek (VM) és a virtuális gép méretezési csoportok nagy méretekben. A szolgáltatás elemzi a Windows és Linux rendszerű virtuális gépek teljesítményét és állapotát, valamint figyeli folyamataikat és a más erőforrásokkal és külső folyamatokkal kapcsolatos függőségeiket. Támogatja a helyszíni vagy egy másik felhőszolgáltatóban üzemeltetett virtuális gépek teljesítményének és alkalmazásfüggőségeinek figyelését. A következő főbb funkciók nyújtnak részletes betekintést:

- **Előre definiált felkapott teljesítménydiagramok:** A vendég virtuális gép operációs rendszer alapvető teljesítménymutatóinak megjelenítése.

- **Függőségi térkép:** Megjeleníti az összekapcsolt összetevőket a virtuális gép különböző erőforráscsoportok ból és előfizetések.  

>[!NOTE]
>Nemrég [jelentettük be](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) az Egészség funkciót a nyilvános előzetes verziós ügyfeleinktől kapott visszajelzések alapján. Tekintettel arra, hogy hány változtatást fogunk végrehajtani, nem kínálunk többé egészségügyi funkciót az új ügyfelek számára. A meglévő ügyfelek továbbra is használhatják az állapotfigyelő szolgáltatást. További részletekért kérjük, olvassa el [az általános elérhetőségi GYIK-et.](vminsights-ga-release-faq.md)  

Az Azure Monitor-naplókkal való integráció hatékony összesítést és szűrést biztosít, lehetővé téve az Azure Monitor virtuális gépekszámára az adatok időbeli alakulásának elemzését. Ezeket az adatokat megtekintheti egyetlen virtuális gép közvetlenül a virtuális gépről, vagy használhatja az Azure Monitor taggregált nézeta a virtuális gépek, ahol a nézet támogatja az Azure-erőforrás-környezet ben vagy a munkaterület-környezet módban. További információt a [Hozzáférési módok – áttekintés című témakörben](../platform/design-logs-deployment.md#access-mode)talál.

![Virtuális gépi betekintési perspektíva az Azure Portalon](media/vminsights-overview/vminsights-azmon-directvm.png)

Az Azure Monitor virtuális gépekhez kiszámítható teljesítményt és a létfontosságú alkalmazások rendelkezésre állását biztosíthatja. Azonosítja a teljesítménybeli szűk keresztmetszeteket és a hálózati problémákat, és segíthet annak megértésében is, hogy egy probléma más függőségekkel kapcsolatos-e.  

## <a name="data-usage"></a>Adathasználat

Amikor üzembe helyezi az Azure Monitor virtuális gépekhez, a virtuális gépek által gyűjtött adatok at az Azure Monitor ban tárolja. Az összegyűjtött teljesítmény- és függőségi adatokat a Log Analytics-munkaterület tárolja. Az [Azure Monitor díjszabási lapján](https://azure.microsoft.com/pricing/details/monitor/)közzétett díjszabás alapján az Azure Monitor virtuális gépekért a következő díjat számítják fel:

- A bevitt és tárolt adatok.
- A létrehozott riasztási szabályok.
- Az elküldött értesítések. 

A napló mérete a teljesítményszámlálók karakterlánchosszától függ, és a virtuális gépszámára lefoglalt logikai lemezek és hálózati adapterek számával növekedhet. Ha már rendelkezik munkaterülettel, és gyűjti ezeket a számlálókat, a rendszer nem számít fel ismétlődő díjakat. Ha már használja a Szolgáltatástérképet, az egyetlen módosítás, amit látni fog, az Azure Monitornak küldött további kapcsolatadatok.

## <a name="next-steps"></a>További lépések

A virtuális gépek figyeléséhez tekintse meg az [Azure Monitor virtuális gépekre való üzembe helyezését.](vminsights-enable-overview.md)
