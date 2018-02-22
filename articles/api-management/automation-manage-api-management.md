---
title: "Azure API Management használata az Azure Automation kezelése"
description: "További tudnivalók hogyan az Azure Automation szolgáltatás Azure API Management kezelésére használható."
services: api-management, automation
documentationcenter: 
author: vladvino
manager: eamono
editor: 
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 8bd251fa05e8e144471cd577cec607d91e5ab84c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Azure API Management használata az Azure Automation kezelése
Ez az útmutató bemutatja, hogyan használható az Azure API Management felügyeleti leegyszerűsítése érdekében, és az Azure Automation szolgáltatás.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?
[Azure Automation szolgáltatásbeli](https://azure.microsoft.com/services/automation/) egy Azure szolgáltatás felhő kezelésüket folyamatok automatizálása révén. Használja az Azure Automation, manuális, ismétlődik, hosszan futó és hibalehetőséget feladatok automatizálhatók megbízhatóságát, hatékonyságát és a szervezet hamarabb növelése érdekében.

Azure Automation szolgáltatásbeli biztosít egy magas rendelkezésre állású, nagymértékben megbízható munkafolyamat-végrehajtási motorjának, amely méretezi az igényeinek. Az Azure Automationben folyamatok is lehet kezdődött el manuálisan, a 3. fél rendszerek vagy rendszeres időközönként, hogy a feladatok fordulhat elő, pontosan, ha szükséges.

Csökkentheti a működési munkaterhelés és szabadítson fel informatikai és DevOps alkalmazottak munka, amely üzleti értéket hozzáadja a felhőbeli felügyeleti feladatok automatikusan Azure Automation által futtatandó mozgatásával összpontosíthat.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Hogyan segíthet az Azure Automation kezelése az Azure API Management?
Az API Management használatával kezelhető az Azure Automationben a [Azure API Management API Windows PowerShell-parancsmagok](https://azure.microsoft.com/updates/full-set-of-windows-powershell-cmdlets-for-azure-api-management-api/). Azure Automation belül szkripteket PowerShell munkafolyamat a parancsmagokkal API-felügyeleti feladatok elvégzéséhez. Ezek a parancsmagok az Azure Automationben más Azure-szolgáltatások, Azure-szolgáltatások és a 3. fél rendszerek között összetett feladatok automatizálása a parancsmagjaival is párosítható.

Íme néhány példa a automatizálást API Management használata:

* [Az Azure API Management – Using PowerShell biztonsági mentése és visszaállítása](https://blogs.msdn.microsoft.com/katriend/2015/10/02/azure-api-management-using-powershell-for-backup-and-restore/)

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az Azure Automation, és hogyan használható az Azure API Management kezelése alapjait, az alábbi hivatkozásokból további.

* Tekintse meg az Azure Automation [használatába bevezető oktatóanyagot](../automation/automation-first-runbook-graphical.md).

