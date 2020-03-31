---
title: Az Azure Cloud Services kezelése az Azure Automation használatával | Microsoft dokumentumok
description: Ismerje meg, hogyan használható az Azure Automation szolgáltatás az Azure felhőszolgáltatások nagy méretekben történő kezelésére.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72439075"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Az Azure Cloud Services kezelése az Azure Automation használatával
Ez az útmutató bemutatja az Azure Automation szolgáltatást, és hogyan használható az Azure felhőszolgáltatásai kezelésének egyszerűsítésére.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?
Az [Azure Automation](https://azure.microsoft.com/services/automation/) egy Azure-szolgáltatás, amely leegyszerűsíti a felhőkezelést a folyamatautomatizálás révén. Az Azure Automation használatával a hosszú ideig futó, manuális, hibalehetőségeket rejtő és gyakran ismétlődő feladatok automatizálhatók a megbízhatóság, a hatékonyság és a szervezet értékének növelése érdekében.

Az Azure Automation rendkívül megbízható és magas rendelkezésre állású munkafolyamat-végrehajtási motort biztosít, amely a szervezet növekedésével az igényeinek megfelelően méretezhető. Az Azure Automationben a folyamatok manuálisan, harmadik féltől származó rendszerekkel vagy ütemezett időközönként is elrúghatók, így a feladatok szükség esetén pontosan megtörténnek.

Alacsonyabb működési többletterhelés, és szabadítsa fel az IT / DevOps személyzetet, hogy olyan munkára összpontosíthassanak, amely növeli az üzleti értéket azáltal, hogy a felhőkezelési feladatokat az Azure Automation automatikusan futtatja.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Hogyan segíthet az Azure Automation az Azure felhőszolgáltatásainak kezelésében?
Az Azure felhőszolgáltatásai az Azure [PowerShell-eszközökben](/powershell/)elérhető PowerShell-parancsmagok használatával kezelhetők az Azure Automationben. Az Azure Automation rendelkezik ezekkel a felhőszolgáltatáspowerShell-parancsmagokkal, amelyek a dobozból kivehetők, így a szolgáltatáson belül elvégezheti az összes felhőszolgáltatás-kezelési feladatot. Ezeket a parancsmagokat az Azure Automationben más Azure-szolgáltatások parancsmagjaival is párosíthatja, így automatizálhatja az összetett feladatokat az Azure-szolgáltatásokban és a külső rendszerekben.

Néhány példa az Azure Automation használatával az Azure Cloud Services kezeléséhez:

* [Felhőszolgáltatás folyamatos üzembe helyezése, amikor cscfg vagy cspkg frissül az Azure Blob storage-ban](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [A Cloud Service-példányok újraindítása párhuzamosan, egyszerre egy frissítési tartomány](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Következő lépések
Most, hogy megtanulta az Azure Automation alapjait, és hogy hogyan használható az Azure felhőszolgáltatásainak kezeléséhez, kövesse ezeket a hivatkozásokat, hogy többet tudjon meg az Azure Automationről.

* [Az Azure Automation áttekintése](../automation/automation-intro.md)
* [Az első runbookom](../automation/automation-first-runbook-graphical.md)
