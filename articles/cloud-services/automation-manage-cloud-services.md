---
title: Azure Cloud Services, Azure Automation használata kezelheti |} A Microsoft Docs
description: Ismerje meg hogyan az Azure Automation szolgáltatás segítségével kezelheti az Azure cloud services ipari méretekben.
services: cloud-services, automation
documentationcenter: ''
author: jodoglevy
manager: timlt
editor: ''
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: b3660901c86dd644369e6d1913e825cbd5ea316b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58918467"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Kezelés az Azure Cloud Services, Azure Automation használatával
Ez az útmutató vezet be, az Azure Automation szolgáltatást, és hogyan használható az Azure cloud Services kezelésének egyszerűsítéséhez.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?
[Az Azure Automation](https://azure.microsoft.com/services/automation/) leegyszerűsíti a felhőfelügyelet a folyamatok automatizálása révén az Azure-szolgáltatások. Az Azure Automationnel, hosszan futó, manuális, sok hibalehetőséget rejtő és gyakran ismételt feladatok automatizálható a megbízhatóság, hatékonyságot és a szervezet teljesítések idejét.

Az Azure Automation segítségével egy rendkívül megbízható és magas rendelkezésre állású munkafolyamat-végző összetevőjével, amely az igényeinek, ha a szervezet növekedésével skálázható. Az Azure Automation szolgáltatás folyamatokat is lehet kezdődjön manuálisan, 3. rendszerekben, vagy rendszeres időközönként, hogy a feladatok fordulhat elő, pontosan akkor, ha szükséges.

Csökkentheti üzemeltetési terheit és szabadítson fel informatikai fejlesztési és üzemeltetési csapatának munkára, így üzleti érték a felhőfelügyeleti feladatokat automatikusan Azure Automation által futtatandó való váltással.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Hogyan segít az Azure Automation kezelése az Azure cloud services?
Az Azure cloud services elérhető PowerShell-parancsmagok segítségével kezelhetők az Azure Automationben a [Azure PowerShell-eszközök](/powershell/). Az Azure Automation a ezek cloud service elérhető PowerShell-parancsmagokkal, beépített rendelkezik, így a szolgáltatásban a felhőszolgáltatás-felügyeleti feladatok is végrehajthatja. Ezek a parancsmagok az Azure Automation parancsmagjaival más Azure-szolgáltatások, az összetett feladatok automatizálása Azure-szolgáltatások és a 3. fél rendszerek párosítása is történik.

Az Azure Cloud Services kezelése az Azure Automation néhány példa használati módjai:

* [Folyamatos üzembe helyezés frissítésekor cscfg vagy cspkg az Azure Blob storage-felhőszolgáltatás](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Párhuzamosan, egyszerre több frissítési tartományt Felhőszolgáltatás-példányok újraindítása](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az alapokat, az Azure Automationben, és hogyan használható az Azure cloud services kezelése, az alábbi hivatkozásokból tudhat meg többet az Azure Automationben.

* [Azure Automation áttekintése](../automation/automation-intro.md)
* [Az első runbookom](../automation/automation-first-runbook-graphical.md)
* [Az Azure Automation tanulási térkép](https://azure.microsoft.com/documentation/learning-paths/automation/)
