---
title: Riasztások kezelése a más monitorozási szolgáltatásokkal
description: Az Azure monitorban Nagios, Zabbix és SCOM riasztások kezelése
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 0a3e0f1ecc40213aca37e37e80c9ba35abedb3d6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961213"
---
# <a name="manage-alerts-from-other-monitoring-services"></a>Riasztások kezelése a más monitorozási szolgáltatásokkal

Most már megtekintheti a Nagios Zabbix és a System Center Operations Manager riasztásait a [egységes riasztások kezelőfelület](https://aka.ms/azure-alerts-overview). Ezek a riasztások származhat integrációt Nagios-és Zabbix-kiszolgálók vagy a System Center Operations Manager a Log Analytics. 

## <a name="prerequisites"></a>Előfeltételek
A Log Analytics-tárházban riasztás típusú rekordokat a a riasztások egységes élményt importálni fogja lekérése, az ezeket a rekordokat összegyűjtéséhez szükséges konfigurációt kell végrehajtania.
1. A **Nagios** és **Zabbix** riasztások [konfigurálja az ezeken a kiszolgálókon](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-linux-agents) riasztások a Log Analyticshez való küldéséhez.
1. A **System Center Operations Manager** riasztások [az Operations Manager felügyeleti csoport csatlakozni a Log Analytics-munkaterület](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-om-agents). A System Center Operations Managerben létrehozott riasztásokat is importálja a Log Analytics.

## <a name="view-your-alert-instances"></a>A riasztási példányok megtekintése
Miután konfigurálta a Log Analytics-ba való importálásuk, youd megkezdheti a ezeket a szolgáltatásokat a figyelési riasztás példányok megtekintése a [egységes riasztások kezelőfelület](https://aka.ms/azure-alerts-overview). Után azok szerepelnek a riasztások egységes felhasználói élményt, [felügyelhető a riasztási](https://aka.ms/managing-alert-instances), [intelligens, ezek a riasztások létrehozott csoportok kezelése](https://aka.ms/managing-smart-groups) és [módosítsa a riasztások állapotát, és intelligens csoportok](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  Nagios-riasztások az egyesített riasztásainak nem állnak állapot-nyilvántartó – például a [feltétel figyelése](https://aka.ms/azure-alerts-overview) riasztás nem kerül majd "Fired" a "Megoldva". Ehelyett a "Fired" és "Megoldva" jelennek meg külön riasztás példány. 
