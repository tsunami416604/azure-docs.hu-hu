---
title: System Center Operations Manager-, Zabbix-és Nagios-riasztások kezelése Azure Monitor
description: System Center Operations Manager-, Zabbix-és Nagios-riasztások kezelése Azure Monitor
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.author: robb
ms.date: 09/24/2018
ms.openlocfilehash: 0e774633542f9b7f8b7de9f8f187821bfbe6b9cf
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555616"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>System Center Operations Manager-, Zabbix-és Nagios-riasztások kezelése Azure Monitor

Most már megtekintheti a riasztásokat a Nagios, a Zabbix és a System Center Operations Manager [Azure monitorban](https://aka.ms/azure-alerts-overview). Ezek a riasztások a Nagios/Zabbix-kiszolgálókkal való integrációk vagy a Log Analyticsba System Center Operations Manager. 

## <a name="prerequisites"></a>Előfeltételek
A Log Analytics-tárházban lévő minden olyan rekord, amely egy riasztást tartalmaz, a rendszer importálja Azure Monitorba, így a rekordok összegyűjtéséhez szükséges konfigurációt kell végrehajtania.
1. A **Nagios** -és **Zabbix** -riasztások esetében [konfigurálja ezeket a kiszolgálókat](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) a [riasztások log Analyticsba küldéséhez](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) .
1. **System Center Operations Manager** riasztások esetén a [Operations Manager felügyeleti csoportot a log Analytics munkaterülethez kell kötni](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Ezt követően telepítse a [Alert Management](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) megoldást az Azure Solutions Piactérről. Ha elkészült, a rendszer a System Center Operations Managerban létrehozott összes riasztást a Log Analyticsba importálja.

## <a name="view-your-alert-instances"></a>Riasztási példányok megtekintése
Miután konfigurálta az importálást a Log Analyticsba, megtekintheti a riasztási példányok megtekintését a figyelési szolgáltatásokból [Azure monitorokban](https://aka.ms/azure-alerts-overview). Ha már szerepelnek a Azure Monitorban, [kezelheti a riasztási példányokat](https://aka.ms/managing-alert-instances), [kezelheti a riasztások alapján létrehozott intelligens csoportokat](https://aka.ms/managing-smart-groups) , és [módosíthatja a riasztások és az intelligens csoportok állapotát](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Ez a megoldás csak a System Center Operations Manager/Zabbix/Nagios fired riasztási példányok megtekintését teszi lehetővé Azure Monitorokban. A riasztási szabály konfigurációját csak a megfelelő figyelési eszközökben lehet megtekinteni vagy szerkeszteni. 
>  1. Minden elindított riasztási példány elérhető lesz a Azure Monitor és az Azure Log Analyticsban is. Jelenleg nem választhat a kettő közül, vagy csak bizonyos kilőtt riasztásokat.
>  1. A System Center Operations Manager, a Zabbix és a Nagios összes riasztása "Unknown" típusú jelet tartalmaz, mivel a mögöttes telemetria-típus nem érhető el.
>  1. A Nagios-riasztások nem megfelelőek – például a riasztások [figyelési feltétele](https://aka.ms/azure-alerts-overview) nem a "kilőtt" értékről "feloldott" állapotba kerül. Ehelyett a "fired" és a "megoldott" is külön riasztási példányként jelenik meg. 

