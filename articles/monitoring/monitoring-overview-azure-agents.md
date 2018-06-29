---
title: Az Azure-figyelő ügynökök áttekintése |} Microsoft Docs
description: Ez a cikk az Azure ügynökök elérhető monitoring Azure virtuális gépeket támogató részletes áttekintést nyújt.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: magoedte
ms.openlocfilehash: a399c3968e5ee1e2d1f6d623a68dbb1e15cef212
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37088644"
---
# <a name="overview-of-the-azure-agents-to-monitor-azure-virtual-machines"></a>Az Azure virtuális gépek figyelése céljából Azure ügynökök áttekintése
A Microsoft Azure különféle típusú adatokat gyűjteni az Azure vagy más Microsoft Windows és Linux rendszerű szolgáltatók futtatott virtuális gépek több lehetőséget nyújt.  Ez a cikk segít ahhoz, hogy segítségével meghatározhatja, melyik támogatja a kezelő ügynököket érhető el vagy általános követelmények figyelési képességek és a különbségeket ismerteti.  

## <a name="comparing-azure-diagnostic-and-log-analytics-agent"></a>Azure diagnosztikai és Naplóelemzési ügynök összehasonlítása
Ma az Azure két típusa van ügynökök egy Azure virtuális gép - figyelhető a Azure Diagnostics bővítményt, és a Linux és a Windows Log Analytics Agent.  Alapvetően ügynökök készültek, metrikákat és a naplók összegyűjtésére és a tárház továbbítja. Azonban, hogy, ahol azok Hasonlóságok végén.  

A [Azure Diagnostics bővítmény](../monitoring-and-diagnostics/azure-diagnostics.md), amely adtak meg Azure-szolgáltatásokhoz 2010, elérhetővé vált, mivel egy olyan ügynök, egy Azure IaaS erőforrás, például egy virtuális Gépet, az letölti a egyszerű diagnosztikai adatok gyűjtésére és megőrizni az Azure storage.  Ha a tároló úgy döntött, hogy egyike a számos elérhető eszköz, például megtekintheti [a Visual Studio Server Explorer](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md) és [Azure Tártallózó](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Ha szeretné gyűjteni:

* Operációs rendszer teljesítményszámlálók és Eseménynapló, vagy előre definiált is adja meg, hogy gyűjtéséhez. 
* Kérelmek, illetve a sikertelen kérelmek egy IIS webkiszolgálón
* Nyomkövetési naplók kimeneti .NET-alkalmazás
* Esemény-nyomkövetés Windows (ETW)-események 
* Syslog alkalmazásnapló-események gyűjtése  
* összeomlási memóriaképek, 

Adatok alternatív továbbítható [Application Insights](../application-insights/app-insights-cloudservices.md), [Naplóelemzési](../log-analytics/log-analytics-overview.md), vagy az-Azure szolgáltatások használatával [Eseményközpont](../event-hubs/event-hubs-what-is-event-hubs.md). 

Speciális figyelésére több mint gyűjtését metrikák és a naplók részhalmazát kell, a Windows és Linux Log Analyticshez ügynök megadása kötelező.  Ez az ügynök az Azure-szolgáltatások például az automatizálás és Naplóelemzési, beleértve a teljes készletét kínálnak, képes biztosítani az Azure virtuális gépeken, a teljes életciklusuk átfogó felügyeleti funkciókat használatára képesek az. Az érintett műveletek közé tartoznak az alábbiak:

* [Azure Automation frissítéskezelés](../automation/automation-update-management.md) az operációs rendszer frissítése
* [Azure Automation kívánt állapot konfigurációs](../automation/automation-dsc-overview.md) konzisztens konfigurációs-állapot karbantartásához
* A konfigurációs változások követése [Azure Automation változások követése és szoftverleltár](../automation/automation-change-tracking.md)
* Operációs rendszer és az üzemeltetett alkalmazások, mint például az egyéni naplókat gyűjteménye [FluentD](../log-analytics/log-analytics-data-sources-json.md), [egyéni naplókat](../log-analytics/log-analytics-data-sources-custom-logs.md), [MySQL és Apache](../log-analytics/log-analytics-data-sources-linux-applications.md) a Naplóelemzési
* Azure-szolgáltatások, mint [Application Insights](https://docs.microsoft.com/azure/application-insights/) és [az Azure Security Center](https://docs.microsoft.com/azure/security-center/) natív módon tárolja az adatokat közvetlenül a Naplóelemzési.  

## <a name="next-steps"></a>További lépések

- Lásd: [adatokat gyűjteni a Log Analyticshez környezetében lévő számítógépek](../log-analytics/log-analytics-concept-hybrid.md) tekintse át a rendszerkövetelményeket és a rendelkezésre álló metódusok telepítse az ügynököt az adatközpontból vagy egyéb felhőalapú környezetben lévő számítógépek számára.
- Az adatgyűjtés Azure-beli virtuális gépekről való konfigurálásáról lásd: [Adatgyűjtés Azure-beli virtuális gépekről](../log-analytics/log-analytics-quick-collect-azurevm.md). 