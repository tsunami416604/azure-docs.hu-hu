---
title: Mi az Azure Monitor for VMs (előzetes verzió)? | Microsoft Docs
description: A Azure Monitor for VMs Azure Monitor, amely az Azure VM operációs rendszer állapotát és teljesítményét ötvözi, valamint az alkalmazások összetevőinek és függőségeinek automatikus felfedését más erőforrásokkal, és a kommunikációt leképezi közöttük. Ez a cikk áttekintést nyújt.
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
ms.date: 10/15/2019
ms.author: magoedte
ms.openlocfilehash: 3569247ad560f985c041eeb11a7d274e15bfeb4c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515373"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Mi az Azure Monitor for VMs (előzetes verzió)?

Azure Monitor for VMs az Azure-beli virtuális gépeket (VM) és a virtuálisgép-méretezési csoportokat nagy méretben figyeli. A szolgáltatás elemzi a Windows és Linux rendszerű virtuális gépek teljesítményét és állapotát, valamint figyeli folyamataikat és a más erőforrásokkal és külső folyamatokkal kapcsolatos függőségeiket. 

Támogatja a helyszíni vagy más felhőalapú virtuális gépek teljesítményének és alkalmazási függőségeinek figyelését. A három fő funkció részletes elemzést nyújt:

- Az Azure-beli **virtuális gépek és a Windows és Linux rendszerű virtuálisgép-méretezési csoportok logikai összetevői**az előre konfigurált állapotra vonatkozó feltételek alapján vannak meghatározva, és riasztást küldenek a kiértékelt feltétel teljesülése esetén.  

- **Előre meghatározott trend Performance-diagramok**: a vendég virtuális gép operációs rendszerének alapteljesítmény-mérőszámait jeleníti meg.

- **Függőségi Térkép**: megjeleníti az összekapcsolt összetevőket a virtuális géppel különböző erőforráscsoportok és előfizetések között.  

A funkciók három perspektívába vannak rendezve:

- Egészségügy
- Teljesítmény
- Térkép

>[!NOTE]
>Nemrég [bejelentettük](https://azure.microsoft.com/updates/upcoming-changes-for-azure-monitor-for-vms-as-we-prepare-for-ga) , hogy a nyilvános előzetes verzió ügyfeleinktől kapott visszajelzések alapján módosítjuk az állapotfigyelő funkciót. A változtatások száma miatt a rendszer leállítja az új ügyfelek számára az állapotfigyelő funkciót. A meglévő ügyfelek továbbra is használhatják az állapot funkciót. További részletekért tekintse meg az [általános elérhetőséggel kapcsolatos gyakori kérdéseket](vminsights-ga-release-faq.md).  

A Azure Monitor naplókkal való integráció hatékony összesítést és szűrést tesz lehetővé, és képes elemezni az adattrendeket az idő múlásával. Az ilyen átfogó számítási feladatok figyelése nem érhető el Azure Monitor vagy Service Map önmagában.  

Ezeket az adatokat egyetlen virtuális gépen tekintheti meg közvetlenül a virtuális gépről, vagy használhatja a Azure Monitort a virtuális gépek összesített nézetének továbbítására. Ez a nézet az egyes funkciók perspektíváján alapul:

- **Állapot**: a virtuális gépek egy erőforráscsoporthoz kapcsolódnak.
- **Térkép** és **teljesítmény**: a virtuális gépek úgy vannak konfigurálva, hogy egy adott log Analytics munkaterületre jelentsenek.

![Virtuális gépekkel kapcsolatban bepillantást nyerhet a Azure Portal](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

A Azure Monitor for VMs kiszámítható teljesítményt és rendelkezésre állást biztosíthat a létfontosságú alkalmazások számára. Azonosítja az operációs rendszer kritikus fontosságú eseményeit, a teljesítmény szűk keresztmetszeteit és a hálózati problémákat. A Azure Monitor for VMs segíthet megérteni, hogy a probléma más függőségekhez kapcsolódik-e.  

## <a name="data-usage"></a>Adathasználat

Azure Monitor for VMs telepítésekor a virtuális gépek által összegyűjtött adatok betöltése és tárolása Azure Monitor történik. Az állapotra vonatkozó feltételek metrikái a Azure Monitor egy idősorozat-adatbázisban vannak tárolva, az összegyűjtött teljesítmény-és függőségi adatok egy Log Analytics munkaterületen tárolódnak. A [Azure monitor díjszabási oldalán](https://azure.microsoft.com/pricing/details/monitor/)közzétett díjszabás alapján Azure monitor for VMS a következő díjat számítjuk fel:

- A betöltött és tárolt adatmennyiség.
- A figyelt állapotra vonatkozó feltételek metrikai idősorozatának száma.
- A létrehozott riasztási szabályok.
- Az elküldött értesítések. 

A napló mérete a teljesítményszámlálók hosszának megfelelően változik, és növelheti a virtuális gép számára lefoglalt logikai lemezek és hálózati adapterek számát. Ha már rendelkezik munkaterülettel, és ezeket a számlálókat gyűjti, a rendszer nem alkalmaz duplikált díjat. Ha már használja a Service Map-t, az egyetlen változás, amelyet a Azure Monitor számára továbbított további kapcsolódási információk fognak látni.

## <a name="next-steps"></a>Következő lépések

A virtuális gépek figyeléséhez szükséges követelmények és módszerek megismeréséhez tekintse át a [Azure monitor for VMS üzembe helyezését ismertető témakört](vminsights-enable-overview.md).
