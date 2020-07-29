---
title: Mi az Azure Monitor for VMs?
description: A Azure Monitor for VMs áttekintése, amely figyeli az Azure-beli virtuális gépek állapotát és teljesítményét, és automatikusan feltérképezi és leképezi az alkalmazás-összetevőket és azok függőségeit.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: f9ad39b88ad2212ea2cdceb40e61fbc0a2d1a764
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320493"
---
# <a name="what-is-azure-monitor-for-vms"></a>Mi az Azure Monitor for VMs?

Azure Monitor for VMs figyeli a virtuális gépek és a virtuálisgép-méretezési csoportok teljesítményét és állapotát, beleértve a futó folyamatokat és a más erőforrásokra vonatkozó függőségeket. A teljesítménnyel kapcsolatos szűk keresztmetszetek és hálózati problémák azonosításával segítheti a létfontosságú alkalmazások kiszámítható teljesítményét és rendelkezésre állását, és segít megérteni, hogy a probléma más függőségekhez is kapcsolódik-e.

A Azure Monitor for VMs a következő műveleteket támogatja a Windows és Linux operációs rendszereken:

- Azure-beli virtuális gépek
- Azure-beli virtuálisgép-méretezési csoportok
- Az Azure arc-hoz csatlakozó hibrid virtuális gépek
- Helyszíni virtuális gépek
- Más felhőalapú környezetben üzemeltetett virtuális gépek
  


>[!NOTE]
>A közelmúltban bejelentettük, hogy a nyilvános előzetes verzió ügyfeleinktől kapott visszajelzések alapján az állapotfigyelő funkció [bekövetkezett](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) . A változtatások száma miatt a rendszer leállítja az új ügyfelek számára az állapotfigyelő funkciót. A meglévő ügyfelek továbbra is használhatják az állapot funkciót. További részletekért tekintse meg az [általános elérhetőséggel kapcsolatos gyakori kérdéseket](vminsights-ga-release-faq.md).  


A Azure Monitor for VMs Azure Monitor naplókban tárolja az adatait, ami lehetővé teszi, hogy hatékony összesítést és szűrést nyújtson, és az adattrendeket az idő múlásával elemezze. Ezeket az adatokat egyetlen virtuális gépen tekintheti meg közvetlenül a virtuális gépről, vagy a Azure Monitor használatával több virtuális gép összesített nézetét is továbbíthatja.

![Virtuális gépekkel kapcsolatban bepillantást nyerhet a Azure Portal](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Díjszabás
Azure Monitor for VMs nem jár közvetlen költséggel, de a Log Analytics munkaterületen a tevékenységért kell fizetnie. A [Azure monitor díjszabási oldalán](https://azure.microsoft.com/pricing/details/monitor/)közzétett díjszabás alapján Azure monitor for VMS a következő díjat számítjuk fel:

- Az ügynököktől betöltött és a munkaterületen tárolt adatok.
- A riasztási szabályok a naplózási és az állapotadatok alapján.
- Riasztási szabályokból küldött értesítések.

A napló mérete a teljesítményszámlálók hosszának megfelelően változik, és növelheti a virtuális gép számára lefoglalt logikai lemezek és hálózati adapterek számát. Ha már használja a Service Map-t, az egyetlen változás, amelyet a Azure Monitor adattípusnak elküldő további teljesítményadatokat láthat `InsightsMetrics` .


## <a name="configuring-azure-monitor-for-vms"></a>Azure Monitor for VMs konfigurálása
A Azure Monitor for VMs konfigurálásának lépései a következők: Az egyes lépésekhez tartozó részletes útmutatásért kövesse az alábbi hivatkozásokat:

- [Log Analytics munkaterület létrehozása.](vminsights-configure-workspace.md#create-log-analytics-workspace)
- [VMInsights-megoldás hozzáadása a munkaterülethez.](vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [A figyeléshez telepítse az ügynököket a virtuális gépre és a virtuálisgép-méretezési csoportra.](vminsights-enable-overview.md)



## <a name="next-steps"></a>További lépések

- A virtuális gépek figyelésének engedélyezéséhez tekintse meg az [Azure monitor for VMS telepítése](vminsights-enable-overview.md) a követelményekhez és a módszerekhez című témakört.

