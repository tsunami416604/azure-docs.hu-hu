---
title: Figyelési a tárolt adatokat használják az Azure Stackben |} A Microsoft Docs
description: Az Azure Stack monitorozási adatok felhasználásához lehetőségek ismertetése.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: mabrigg
ms.openlocfilehash: 3d856f4fad845dfdd4d9a30fa176a4c0bfbc875b
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024342"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Hogyan lehet az Azure Stack származó megfigyelési adatokat

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Monitor folyamattal, csak egyetlen helyen történő monitorozási adatok, például az Azure Monitor globális Azure-ban található. De nem az összes globális Azure-ban található a monitorozási adatok nem érhető el az Azure Stackben. Ebben a cikkben találja a különféle módokon, hogy a szolgáltatásból származó monitorozási adatok programozott módon betöltheti összegzését.
 
## <a name="options-for-data-consumption"></a>Adathasználat lehetőségei

| Adattípus | Kategória | Támogatott szolgáltatások | Hozzáférési módok |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Az Azure platform-szintű metrikák figyelése | Mérőszámok | [A támogatott mérőszámok az Azure monitorral, az Azure Stackben](azure-stack-metrics-supported.md) | REST API |
| Vendég operációs rendszer metrikákat (például teljesítményszám) számítási | Mérőszámok | Windows és Linux rendszerű virtuális gépek | - Vagy blob Storage-táblába:<br>Windows vagy Linux rendszerű Azure-diagnosztika <br>Event Hubs:<br>Windows Azure Diagnostics |
| Storage-mérőszámok | Mérőszámok | Azure Storage | Storage-táblához:<br>Storage Analytics |
| Tevékenységnapló | Események | Azure-szolgáltatásokhoz | REST API:<br>Az Azure Monitor Event API |
| Vendég operációs rendszer naplóit (például az IIS, az ETW, rendszerbejegyzéseket) számítási | Események | Windows és Linux rendszerű virtuális gépek | - Vagy blob Storage-táblába:<br>Windows vagy Linux rendszerű Azure-diagnosztika <br>Event Hubs:<br>Windows Azure Diagnostics |
| Storage-naplók | Események | Azure Storage | Storage-táblához:<br>Storage Analytics<br>`Vita: how about hybrid OMS/AppInsights, shall we mention?` |

## <a name="next-steps"></a>További lépések

Tudjon meg többet [Azure monitorozása az Azure Stacken](azure-stack-metrics-azure-data.md).
