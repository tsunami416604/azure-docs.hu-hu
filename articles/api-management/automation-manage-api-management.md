---
title: Az Azure API-kezelés kezelése az Azure Automation használatával
description: Ismerje meg, hogyan használható az Azure Automation szolgáltatás az Azure API Management kezeléséhez.
services: api-management, automation
documentationcenter: ''
author: vladvino
manager: eamono
editor: ''
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: a472e00f9ecab8a5ffa6b19e4fe9a5f8b5ee5b95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70072059"
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Az Azure API Management felügyelete az Azure Automationnel
Ez az útmutató bemutatja az Azure Automation szolgáltatást, és hogyan használható az Azure API-kezelés felügyeletének egyszerűsítésére.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?
Az [Azure Automation](https://azure.microsoft.com/services/automation/) egy Azure-szolgáltatás, amely leegyszerűsíti a felhőkezelést a folyamatautomatizálás révén. Az Azure Automation használatával manuális, ismétlődő, hosszan futó és hibalehetőségeket rejtő feladatok automatizálhatók a megbízhatóság, a hatékonyság és a szervezet értékének növelése érdekében.

Az Azure Automation rendkívül megbízható, magas rendelkezésre állású munkafolyamat-végrehajtási motort biztosít, amely az igényeinek megfelelően méretezhető. Az Azure Automationben a folyamatok manuálisan, harmadik féltől származó rendszerekkel vagy ütemezett időközönként is elrúghatók, így a feladatok szükség esetén pontosan megtörténnek.

Csökkentse a működési többletterhelést, és szabadítsa fel az informatikai és devops-munkatársakat, hogy olyan munkára összpontosíthassanak, amely üzleti értéket ad az üzleti értéket növelő munkához azáltal, hogy a felhőkezelési feladatokat automatikusan futtatja az Azure Automation által.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Hogyan segíthet az Azure Automation az Azure API Management kezelésében?
Az API Management az [Azure API Management API Windows PowerShell-parancsmagjainak](https://docs.microsoft.com/powershell/module/az.apimanagement)használatával kezelhető az Azure Automationben. Az Azure Automation en belül PowerShell-munkafolyamat-parancsfájlok írása számos API-felügyeleti feladat végrehajtásához a parancsmagok használatával. Ezeket a parancsmagokat az Azure Automationben más Azure-szolgáltatások parancsmagjaival is párosíthatja, így automatizálhatja az összetett feladatokat az Azure-szolgáltatásokban és a külső rendszerekben.

Íme néhány példa az API Management powershell használatával:

* [Azure PowerShell-minták az API Managementhez](https://docs.microsoft.com/azure/api-management/powershell-samples)

## <a name="next-steps"></a>Következő lépések
Most, hogy megtanulta az Azure Automation alapjait, és hogy hogyan használható az Azure API Management kezeléséhez, kövesse ezeket a hivatkozásokat, hogy többet tudjon meg.

* Tekintse meg az Azure Automation [első lépéseit bemutató.](../automation/automation-first-runbook-graphical.md)

