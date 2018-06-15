---
title: Azure Automation használatával Azure Cloud Services kezelése |} Microsoft Docs
description: További tudnivalók hogyan az Azure Automation szolgáltatás léptékű Azure felhőszolgáltatások kezelésére használható.
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
ms.openlocfilehash: 6b5acac1b8647c324988c316cd5602b3dba98a1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23842936"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Azure Automation használata Azure Cloud Services kezelése
Az útmutatóból megismerheti az Azure Automation szolgáltatás, és hogyan használható az Azure felhőalapú szolgáltatások felügyelete leegyszerűsítése érdekében.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?
[Azure Automation szolgáltatásbeli](https://azure.microsoft.com/services/automation/) egy Azure szolgáltatás felhő kezelésüket folyamatok automatizálása révén. Azure Automation használ, hosszan futó, manuális, hibákhoz vezethet, és gyakran ismétlődő feladatok automatizálhatók megbízhatóságát, hatékonyságát és a szervezet hamarabb növelése érdekében.

Azure Automation szolgáltatásbeli egy magas rendelkezésre állású és nagymértékben megbízható munkafolyamat-végrehajtási motorjának, amely az igényeinek, ha a szervezet növekedésének megfelelően méretezi biztosít. Az Azure Automationben folyamatok is lehet kezdődött el manuálisan, a 3. fél rendszerek vagy rendszeres időközönként, hogy a feladatok fordulhat elő, pontosan, ha szükséges.

Működési munkaterhelés csökkentése és az szabadítson fel informatikai / azáltal, hogy a felhő felügyeleti feladatok automatikusan Azure Automation által futtatandó value DevOps személyzet üzleti hozzáadó munkahelyi összpontosíthat.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Hogyan segíthet az Azure Automation Azure cloud services kezelése?
Azure felhőszolgáltatások által biztosított PowerShell-parancsmagok használatával kezelhető az Azure Automationben a [Azure PowerShell eszközök](https://msdn.microsoft.com/library/azure/jj156055.aspx). Azure Automation a felhőalapú szolgáltatás PowerShell parancsmagok használatával elérhető alapesetben rendelkezik, így a felhőalapú szolgáltatás felügyeleti feladatokat a szolgáltatáson belül végezheti el. Ezek a parancsmagok az Azure Automationben más Azure-szolgáltatások, Azure-szolgáltatások és a 3. fél rendszerek között összetett feladatok automatizálása a parancsmagjaival is párosítható.

Néhány példa használati Azure Automation Azure Cloud Services kezelése, többek között:

* [Egy Felhőszolgáltatás, amikor az Azure Blob storage frissítése a szolgáltatáskonfigurációs séma vagy cspkg folyamatos telepítése](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [A felhőalapú szolgáltatás példányok párhuzamosan, egyszerre több frissítési tartományt újraindítása](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte az Azure Automation, és hogyan használat Azure cloud services kezelése alapjait, az alábbi hivatkozásokból tudhat meg többet az Azure Automation.

* [Azure Automation – áttekintés](../automation/automation-intro.md)
* [Az első runbookom](../automation/automation-first-runbook-graphical.md)
* [Azure Automation tanulási térkép](https://azure.microsoft.com/documentation/learning-paths/automation/)
