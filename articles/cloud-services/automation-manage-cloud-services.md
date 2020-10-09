---
title: Azure-Cloud Services kezelése Azure Automation használatával | Microsoft Docs
description: Ismerje meg, hogyan használható a Azure Automation szolgáltatás az Azure Cloud Services nagy léptékű kezeléséhez.
services: cloud-services, automation
author: jodoglevy
manager: timlt
editor: ''
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: 482fcf7d100a90d9527f510382c5dafb4f67adfa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "72439075"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Az Azure Cloud Services kezelése Azure Automation használatával
Ez az útmutató bemutatja a Azure Automation szolgáltatást, valamint azt, hogy miként használható az Azure Cloud Services kezelésének egyszerűbbé tételéhez.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) egy Azure-szolgáltatás, amely egyszerűbbé teszi a felhőalapú felügyeletet a folyamatok automatizálásával. A Azure Automation, a hosszan futó, a manuális, a hibákra hajlamos és a gyakran ismétlődő feladatok használatával automatizálható a szervezet megbízhatóságának, hatékonyságának és időpontjának növelése.

A Azure Automation egy nagyon megbízható és jól elérhető munkafolyamat-végrehajtó motort biztosít, amely a szervezet növekedésének megfelelően méretezhető. Azure Automation a folyamatokat manuálisan, harmadik féltől származó rendszerek vagy ütemezett időközök alapján lehet kiváltani, hogy a feladatok a szükségesnél pontosan megtörténjenek.

Csökkentse a működési terhelést, és szabadítson fel informatikai/DevOps-személyzetet, hogy az üzleti értékeket felhasználva a Felhőbeli felügyeleti feladatok automatikus futtatásával Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Hogyan segíthet Azure Automation az Azure Cloud Services kezelése?
Az Azure Cloud Services Azure Automation a [Azure PowerShell eszközökben](/powershell/)elérhető PowerShell-parancsmagok használatával kezelhetők. Azure Automation a Cloud Service PowerShell-parancsmagok elérhetők a dobozból, így az összes felhőalapú szolgáltatás-felügyeleti feladatot elvégezheti a szolgáltatáson belül. Ezeket a parancsmagokat Azure Automation is párosíthatja más Azure-szolgáltatások parancsmagokkal, így összetett feladatokat automatizálhat az Azure-szolgáltatások és a külső gyártók rendszerei között.

Néhány példa a Azure Automation használatára az Azure Cloud Services kezeléséhez:

* [Felhőalapú szolgáltatás folyamatos üzembe helyezése, amikor a cscfg vagy a cspkg frissül az Azure Blob Storage-ban](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Cloud Service-példányok újraindítása párhuzamosan, egyszerre egy frissítési tartomány](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a Azure Automation alapjait és az Azure Cloud Services felügyeletének módját, az alábbi hivatkozásokat követve további információkat tudhat meg a Azure Automationról.

* [Az Azure Automation áttekintése](../automation/automation-intro.md)
* [Az első runbookom](../automation/automation-first-runbook-graphical.md)
