---
title: A System Center Operations Manager, a Zabbix és a Nagios riasztásainak kezelése az Azure Monitorban
description: A System Center Operations Manager, a Zabbix és a Nagios riasztásainak kezelése az Azure Monitorban
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 367a7020e56db7105da7624c7911ae5b59f1365a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92105889"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>A System Center Operations Manager, a Zabbix és a Nagios riasztásainak kezelése az Azure Monitorban

Most már megtekintheti a riasztásokat a Nagios, a Zabbix és a System Center Operations Manager [Azure monitorban](./alerts-overview.md). Ezek a riasztások a Nagios/Zabbix-kiszolgálókkal való integrációk vagy a Log Analyticsba System Center Operations Manager. 

## <a name="prerequisites"></a>Előfeltételek
A Log Analytics-tárházban lévő minden olyan rekord, amely egy riasztást tartalmaz, a rendszer importálja Azure Monitorba, így a rekordok összegyűjtéséhez szükséges konfigurációt kell végrehajtania.
1. A **Nagios** -és **Zabbix** -riasztások esetében [konfigurálja ezeket a kiszolgálókat](../learn/quick-collect-linux-computer.md) a [riasztások log Analyticsba küldéséhez](./data-sources-custom-logs.md?toc=/azure/azure-monitor/toc.json) .
1. **System Center Operations Manager** riasztások esetén a [Operations Manager felügyeleti csoportot a log Analytics munkaterülethez kell kötni](./om-agents.md). Ezt követően telepítse a [Alert Management](./alert-management-solution.md) megoldást az Azure Solutions Piactérről. Ha elkészült, a rendszer a System Center Operations Managerban létrehozott összes riasztást a Log Analyticsba importálja.

## <a name="view-your-alert-instances"></a>Riasztási példányok megtekintése
Miután konfigurálta az importálást a Log Analyticsba, megtekintheti a riasztási példányok megtekintését a figyelési szolgáltatásokból [Azure monitorokban](./alerts-overview.md). Ha már szerepelnek a Azure Monitorban, [kezelheti a riasztási példányokat](./alerts-managing-alert-instances.md?toc=%252fazure%252fazure-monitor%252ftoc.json), [kezelheti a riasztások alapján létrehozott intelligens csoportokat](./alerts-managing-smart-groups.md?toc=%252fazure%252fazure-monitor%252ftoc.json) , és [módosíthatja a riasztások és az intelligens csoportok állapotát](./alerts-managing-alert-states.md?toc=%252fazure%252fazure-monitor%252ftoc.json).

> [!NOTE]
>  1. Ez a megoldás csak a System Center Operations Manager/Zabbix/Nagios fired riasztási példányok megtekintését teszi lehetővé Azure Monitorokban. A riasztási szabály konfigurációját csak a megfelelő figyelési eszközökben lehet megtekinteni vagy szerkeszteni. 
>  1. Minden elindított riasztási példány elérhető lesz a Azure Monitor és az Azure Log Analyticsban is. Jelenleg nem választhat a kettő közül, vagy csak bizonyos kilőtt riasztásokat.
>  1. A System Center Operations Manager, a Zabbix és a Nagios összes riasztása "Unknown" típusú jelet tartalmaz, mivel a mögöttes telemetria-típus nem érhető el.
>  1. A Nagios-riasztások nem megfelelőek – például a riasztások [figyelési feltétele](./alerts-overview.md) nem a "kilőtt" értékről "feloldott" állapotba kerül. Ehelyett a "fired" és a "megoldott" is külön riasztási példányként jelenik meg.