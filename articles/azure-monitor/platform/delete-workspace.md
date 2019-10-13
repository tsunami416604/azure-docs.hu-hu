---
title: Az Azure Log Analytics munkaterület törlése és visszaállítása | Microsoft Docs
description: Megtudhatja, hogyan törölheti Log Analytics munkaterületét, ha létrehozott egy személyes előfizetést, vagy átalakította a munkaterület modelljét.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: magoedte
ms.openlocfilehash: 4f03fc71a11c1ecb2e96b316efac9249395fc333
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285541"
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
> A telepített megoldások és a társított szolgáltatások (például az Automation-fiók) véglegesen törlődnek a munkaterületről a törlés időpontjában, és nem állíthatók helyre. Ezeket újra kell konfigurálni a helyreállítási művelet után, hogy a munkaterületet a korábbi funkciókhoz vigye. 

A munkaterületeket a [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), a [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)vagy a [Azure Portal](https://portal.azure.com)használatával törölheti.

### <a name="delete-workspace-in-azure-portal"></a>Munkaterület törlése Azure Portal
1. A bejelentkezéshez nyissa meg a [Azure Portal](https://portal.azure.com). 
2. Az Azure Portalon válassza a **Minden szolgáltatás** elemet. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **log Analytics munkaterületek**lehetőséget.
3. A Log Analytics munkaterületek listájában válasszon ki egy munkaterületet, majd kattintson a középső ablaktábla tetején található **Törlés** elemre.
   ![Delete beállítás a munkaterület tulajdonságok paneljén @ no__t-1
4. Amikor megjelenik a megerősítő üzenet ablak, amely a munkaterület törlésének megerősítését kéri, kattintson az **Igen**gombra.
   @no__t – a munkaterület @ no__t-1 0Confirm törlése

## <a name="recover-workspace"></a>Munkaterület helyreállítása
Ha közreműködői engedélyekkel rendelkezik ahhoz az előfizetéshez és erőforráscsoporthoz, ahová a munkaterület hozzá lett rendelve a törlési művelet előtt, akkor helyreállíthatja azt a puha törlési időszakban, beleértve az adatokat, a konfigurációt és a csatlakoztatott ügynököket. A törlési időtartam után a munkaterület nem helyreállítható és végleges törléshez van rendelve.

A munkaterület helyreállításához a munkaterületet a [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) vagy a [REST API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) munkaterület létrehozása módszerekkel hozhatja létre, feltéve, hogy ezek a tulajdonságok a törölt munkaterület részleteivel vannak feltöltve, beleértve a következőket:
1.  Subscription ID (Előfizetés azonosítója)
2.  Erőforráscsoport neve
3.  Munkaterület neve
4.  Region (Régió)

> [!NOTE]
> * A munkaterület helyreállítása nem támogatott a [Azure Portalban](https://portal.azure.com). Egy munkaterület újbóli létrehozása a Soft-delete időszakban azt jelzi, hogy a munkaterület neve már használatban van.
> * A törölt munkaterületek nevei megmaradnak a Soft-delete időszakra, és nem használhatók új munkaterület létrehozásakor.
