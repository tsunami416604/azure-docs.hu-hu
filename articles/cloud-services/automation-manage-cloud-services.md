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
ms.openlocfilehash: 40e91b4ba7ee62976b49975769bd1d1e656525f2
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54328868"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Kezelés az Azure Cloud Services, Azure Automation használatával
Ez az útmutató vezet be, az Azure Automation szolgáltatást, és hogyan használható az Azure cloud Services kezelésének egyszerűsítéséhez.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?
[Az Azure Automation](https://azure.microsoft.com/services/automation/) leegyszerűsíti a felhőfelügyelet a folyamatok automatizálása révén az Azure-szolgáltatások. Az Azure Automationnel, hosszan futó, manuális, sok hibalehetőséget rejtő és gyakran ismételt feladatok automatizálható a megbízhatóság, hatékonyságot és a szervezet teljesítések idejét.

Az Azure Automation segítségével egy rendkívül megbízható és magas rendelkezésre állású munkafolyamat-végző összetevőjével, amely az igényeinek, ha a szervezet növekedésével skálázható. Az Azure Automation szolgáltatás folyamatokat is lehet kezdődjön manuálisan, 3. rendszerekben, vagy rendszeres időközönként, hogy a feladatok fordulhat elő, pontosan akkor, ha szükséges.

Csökkentheti üzemeltetési terheit és szabadítson fel informatikai fejlesztési és üzemeltetési csapatának munkára, így üzleti érték a felhőfelügyeleti feladatokat automatikusan Azure Automation által futtatandó való váltással.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Hogyan segít az Azure Automation kezelése az Azure cloud services?
Az Azure cloud services elérhető PowerShell-parancsmagok segítségével kezelhetők az Azure Automationben a [Azure PowerShell-eszközök](https://msdn.microsoft.com/library/azure/jj156055.aspx). Az Azure Automation a ezek cloud service elérhető PowerShell-parancsmagokkal, beépített rendelkezik, így a szolgáltatásban a felhőszolgáltatás-felügyeleti feladatok is végrehajthatja. Ezek a parancsmagok az Azure Automation parancsmagjaival más Azure-szolgáltatások, az összetett feladatok automatizálása Azure-szolgáltatások és a 3. fél rendszerek párosítása is történik.

Az Azure Cloud Services kezelése az Azure Automation néhány példa használati módjai:

* [Folyamatos üzembe helyezés frissítésekor cscfg vagy cspkg az Azure Blob storage-felhőszolgáltatás](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Párhuzamosan, egyszerre több frissítési tartományt Felhőszolgáltatás-példányok újraindítása](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az alapokat, az Azure Automationben, és hogyan használható az Azure cloud services kezelése, az alábbi hivatkozásokból tudhat meg többet az Azure Automationben.

* [Azure Automation áttekintése](../automation/automation-intro.md)
* [Az első runbookom](../automation/automation-first-runbook-graphical.md)
* [Az Azure Automation tanulási térkép](https://azure.microsoft.com/documentation/learning-paths/automation/)
