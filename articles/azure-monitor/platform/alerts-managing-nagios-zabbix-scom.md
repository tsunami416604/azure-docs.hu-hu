---
title: Riasztások kezelése az Azure monitorban más figyelési szolgáltatások
description: Az Azure monitorban Nagios, Zabbix és SCOM riasztások kezelése
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: bc597d42fe89c0e03c4af1db3a935031b9043a98
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53346304"
---
# <a name="manage-alerts-from-other-monitoring-services"></a>Egyéb monitorozási szolgáltatások riasztásainak kezelése

Most már megtekintheti a Nagios Zabbix és a System Center Operations Manager riasztásait a [egységes riasztások kezelőfelület](https://aka.ms/azure-alerts-overview). Ezek a riasztások származhat integrációt Nagios-és Zabbix-kiszolgálók vagy a System Center Operations Manager a Log Analytics. 

## <a name="prerequisites"></a>Előfeltételek
A Log Analytics-tárházban riasztás típusú rekordokat a a riasztások egységes élményt importálni fogja lekérése, az ezeket a rekordokat összegyűjtéséhez szükséges konfigurációt kell végrehajtania.
1. A **Nagios** és **Zabbix** riasztások [konfigurálja az ezeken a kiszolgálókon](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) riasztások a Log Analyticshez való küldéséhez.
1. A **System Center Operations Manager** riasztások [az Operations Manager felügyeleti csoport csatlakozni a Log Analytics-munkaterület](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). A System Center Operations Managerben létrehozott riasztásokat is importálja a Log Analytics.

## <a name="view-your-alert-instances"></a>A riasztási példányok megtekintése
Miután konfigurálta a Log Analytics-ba való importálásuk, ezeket a szolgáltatásokat a figyelési riasztás példányok megtekintése elindíthatja a [egységes riasztások kezelőfelület](https://aka.ms/azure-alerts-overview). Után azok szerepelnek a riasztások egységes felhasználói élményt, [felügyelhető a riasztási](https://aka.ms/managing-alert-instances), [intelligens, ezek a riasztások létrehozott csoportok kezelése](https://aka.ms/managing-smart-groups) és [módosítsa a riasztások állapotát, és intelligens csoportok](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  Nagios-riasztások az egyesített riasztásainak nem állnak állapot-nyilvántartó – például a [feltétel figyelése](https://aka.ms/azure-alerts-overview) riasztás nem kerül majd "Fired" a "Megoldva". Ehelyett a "Fired" és "Megoldva" jelennek meg külön riasztás példány. 
