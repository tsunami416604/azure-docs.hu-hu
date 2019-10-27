---
title: Az Azure Log Analytics munkaterület törlése és visszaállítása | Microsoft Docs
description: Megtudhatja, hogyan törölheti Log Analytics munkaterületét, ha létrehozott egy személyes előfizetést, vagy átalakította a munkaterület modelljét.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 10/11/2019
ms.openlocfilehash: f15e9c2a5980c8fb6d98f7bf9187b030e6910523
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932371"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>Az Azure Log Analytics munkaterület törlése és visszaállítása

Ez a cikk ismerteti az Azure Log Analytics Workspace Soft-delete fogalmát, valamint a törölt munkaterület helyreállításának módját. 

## <a name="considerations-when-deleting-a-workspace"></a>Munkaterületek törlésekor megfontolandó szempontok

Ha töröl egy Log Analytics munkaterületet, a rendszer egy törlési műveletet hajt végre, amely lehetővé teszi a munkaterület helyreállítását 14 napon belül, beleértve az adatmennyiséget és a csatlakoztatott ügynököket is, függetlenül attól, hogy a törlés véletlen vagy szándékos volt-e. A Soft-delete időszak után a munkaterület és az azokhoz tartozó adattárolók nem állíthatók vissza, és 30 napon belül állandó törlésre vannak várólistán.

Fontos, hogy körültekintően járjon el, amikor töröl egy munkaterületet, mert olyan fontos adat és konfiguráció lehet, amely negatív hatással lehet a szolgáltatási műveletre. Tekintse át, hogy milyen ügynökök, megoldások és más Azure-szolgáltatások és-források tárolják az adataikat Log Analyticsban, például:

* Felügyeleti megoldások
* Azure Automation
* Windows-és Linux-alapú virtuális gépeken futó ügynökök
* A környezetben található Windows és Linux rendszerű számítógépeken futó ügynökök
* System Center Operations Manager

A törlési művelet törli a munkaterület-erőforrást, és a társított felhasználók engedélyei megszakadnak. Ha a felhasználók más munkaterületekhez vannak társítva, akkor továbbra is használhatják a Log Analyticst ezekkel a munkaterületekkel.

## <a name="soft-delete-behavior"></a>Helyreállítható törlési viselkedés

A munkaterület-törlési művelet eltávolítja a munkaterület Resource Manager-erőforrását, de a konfigurációját és az adatait 14 napig őrzi meg a rendszer, miközben megadja a munkaterület törlésének a megjelenését. A munkaterületre való jelentésre konfigurált ügynökök és System Center Operations Manager-felügyeleti csoportok árva állapotban maradnak a törlési időszak alatt. A szolgáltatás továbbra is lehetővé teszi a törölt munkaterület helyreállítását, beleértve az adatforrásokat és a csatlakoztatott erőforrásokat, lényegében a törlést.

> [!NOTE] 
> A telepített megoldásokat és a társított szolgáltatásokat, például a Azure Automation fiókját véglegesen eltávolítja a munkaterületről a törlés időpontjában, és nem állítható helyre. Ezeket úgy kell konfigurálni, hogy a helyreállítási művelet után a munkaterület a korábban konfigurált állapotba kerüljön.

A munkaterületeket a [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), a [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)vagy a [Azure Portal](https://portal.azure.com)használatával törölheti.

### <a name="delete-workspace-in-azure-portal"></a>Munkaterület törlése Azure Portal

1. A bejelentkezéshez nyissa meg a [Azure Portal](https://portal.azure.com). 
2. Az Azure Portalon válassza a **Minden szolgáltatás** elemet. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **log Analytics munkaterületek**lehetőséget.
3. A Log Analytics munkaterületek listájában válasszon ki egy munkaterületet, majd kattintson a középső ablaktábla tetején található **Törlés** elemre.
   ![törlési lehetőség a munkaterület tulajdonságai ablaktáblán](media/delete-workspace/log-analytics-delete-workspace.png)
4. Amikor megjelenik a megerősítő üzenet ablak, amely a munkaterület törlésének megerősítését kéri, kattintson az **Igen**gombra.
   ![a munkaterület törlésének megerősítése](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

## <a name="recover-workspace"></a>Munkaterület helyreállítása

Ha közreműködői engedélyekkel rendelkezik ahhoz az előfizetéshez és erőforráscsoporthoz, amelyben a munkaterület a törlési művelet előtt hozzá volt rendelve, akkor helyreállíthatja azt a puha törlési időszakban, beleértve az adatokat, a konfigurációt és a csatlakoztatott ügynököket. A törlési időtartam után a munkaterület nem helyreállítható és végleges törléshez van rendelve. A törölt munkaterületek nevei a Soft-delete időszakban megmaradnak, és nem használhatók új munkaterületek létrehozására tett kísérlet során.  

A munkaterületet úgy állíthatja helyre, hogy a munkaterület Create Methods [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) vagy a [REST API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) használatával újra létrehozza a munkaterületet, feltéve, hogy ezek a tulajdonságok a törölt munkaterület részleteivel vannak feltöltve, beleértve a következőket:

* Subscription ID (Előfizetés azonosítója)
* Erőforráscsoport neve
* Munkaterület neve
* Region (Régió)

> [!NOTE]
> * A munkaterület helyreállítása nem támogatott a [Azure Portalban](https://portal.azure.com). 
> * Egy munkaterület újbóli létrehozása a Soft-delete időszakban azt jelzi, hogy a munkaterület neve már használatban van. 
> 
