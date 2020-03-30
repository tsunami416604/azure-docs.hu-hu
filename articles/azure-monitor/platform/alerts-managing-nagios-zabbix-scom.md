---
title: A System Center Operations Manager, a Zabbix és a Nagios riasztásainak kezelése az Azure Monitorban
description: A System Center Operations Manager, a Zabbix és a Nagios riasztásainak kezelése az Azure Monitorban
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 5ef03f522fb91302bfdec985f1e3c5cf8138ae97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667448"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>A System Center Operations Manager, a Zabbix és a Nagios riasztásainak kezelése az Azure Monitorban

Most már megtekintheti a nagiosi, zabbixi és system center-műveleti kezelőből érkező riasztásokat az [Azure Monitorban.](https://aka.ms/azure-alerts-overview) Ezek a riasztások a Nagios/Zabbix kiszolgálókkal vagy a System Center Operations Manager rel a Log Analytics szolgáltatásba való integrációból származnak. 

## <a name="prerequisites"></a>Előfeltételek
A Log Analytics-tárházban egy riasztástípussal rendelkező rekordok importálása az Azure Monitorba, így el kell végeznie a rekordok gyűjtéséhez szükséges konfigurációt.
1. **Nagios** és **Zabbix** riasztások esetén [konfigurálja ezeket a kiszolgálókat](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) úgy, hogy [riasztásokat küldjenek](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) a Log Analytics szolgáltatásnak.
1. A **System Center Operations Manager** riasztásai esetén csatlakoztassa az [Operations Manager felügyeleti csoportját a Log Analytics-munkaterülethez.](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents) Ezt követően telepítse a [riasztáskezelési](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) megoldást az Azure-megoldások piactérről. Miután elkészült, a System Center Operations Manager ben létrehozott riasztások importálása a Log Analytics.

## <a name="view-your-alert-instances"></a>A riasztási példányok megtekintése
Miután konfigurálta az importálást a Log Analytics szolgáltatásba, megkezdheti a riasztási példányok megtekintését ezekből a figyelési szolgáltatásokból az [Azure Monitorban.](https://aka.ms/azure-alerts-overview) Miután megvannak jelen az Azure Monitorban, [kezelheti a riasztási példányokat,](https://aka.ms/managing-alert-instances) [kezelheti az ezeken a riasztásokon létrehozott intelligens csoportokat,](https://aka.ms/managing-smart-groups) és [módosíthatja a riasztások és az intelligens csoportok állapotát.](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  1. Ez a megoldás csak lehetővé teszi a System Center Operations Manager/Zabbix/Nagios által indított riasztási példányok megtekintését az Azure Monitorban. A riasztási szabályok konfigurációja csak a megfelelő figyelési eszközökben tekinthető meg/szerkeszthető. 
>  1. Az összes tüzelésű riasztási példány elérhető lesz az Azure Monitorban és az Azure Log Analytics-ben is. Jelenleg nincs mód a kettő közötti választás, vagy csak a konkrét tüzelésű riasztások betöltése.
>  1. A System Center Operations Manager, a Zabbix és a Nagios összes riasztása "Ismeretlen" jeltípussal rendelkezik, mivel az alapul szolgáló telemetriai típus nem érhető el.
>  1. A Nagios-riasztások nem állapotalapúak – például egy riasztás [figyelőállapota](https://aka.ms/azure-alerts-overview) nem "Tüzelt" és "Megoldott" lesz. Ehelyett mind a "Fired" és a "Resolved" külön riasztási példányként jelenik meg. 

