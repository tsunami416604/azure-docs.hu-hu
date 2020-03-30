---
title: Az Azure Log Analytics munkaterületének törlése és helyreállítása | Microsoft dokumentumok
description: Megtudhatja, hogyan törölheti a Log Analytics-munkaterületet, ha személyes előfizetésben hozott létre egyet, vagy átstrukturálhatja a munkaterületi modellt.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: ead0ac04fbd2244fce97dd043ebd44f24fb0f67f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054921"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Az Azure Log Analytics munkaterületének törlése és helyreállítása

Ez a cikk ismerteti az Azure Log Analytics munkaterület helyreállítható törlés koncepcióját, és a törölt munkaterület helyreállításának módját. 

## <a name="considerations-when-deleting-a-workspace"></a>A munkaterület törlésével kapcsolatos szempontok

A Log Analytics-munkaterület törlésekor a rendszer helyreállítható törlési műveletet hajt végre, amely lehetővé teszi a munkaterület helyreállítását, beleértve az adatokat és a csatlakoztatott ügynököket is 14 napon belül, függetlenül attól, hogy a törlés véletlen vagy szándékos volt-e. A helyreállítható törlési időszak után a munkaterületi erőforrás és annak adatai nem állíthatók helyre – az adatok várólistára kerülnek végleges törlésre, és 30 napon belül teljesen törlődnek. A munkaterület neve "felszabadul", és új munkaterület létrehozására használhatja.

> [!NOTE]
> Ha felül szeretné írni a törlés helyrehozásának viselkedését, és véglegesen törölni szeretné a munkaterületet, kövesse az [Állandó munkaterület törlése](#permanent-workspace-delete)című szakaszlépéseit.

Ha egy munkaterületet töröl, körültekintően jár el, mert fontos adatok és konfigurációk lehetnek negatív hatással a szolgáltatás működésére. Tekintse át, hogy milyen ügynökök, megoldások és egyéb Azure-szolgáltatások és források tárolják az adataikat a Log Analytics szolgáltatásban, például:

* Felügyeleti megoldások
* Azure Automation
* Windows és Linux rendszerű virtuális gépeken futó ügynökök
* Windows és Linux rendszerű számítógépeken futó ügynökök a környezetben
* System Center Operations Manager

A helyreállítható törlési művelet törli a munkaterületi erőforrást, és a társított felhasználók engedélye megszakad. Ha a felhasználók más munkaterületekkel vannak társítva, akkor továbbra is használhatják a Log Analytics szolgáltatást a többi munkaterülettel.

## <a name="soft-delete-behavior"></a>A törlés viselkedése

A munkaterület törlése művelet eltávolítja a munkaterület Resource Manager erőforrás-kezelő erőforrást, de a konfigurációés az adatok 14 napig megmaradnak, miközben a munkaterület törlésének látszatát adja. A munkaterületre jelentésre konfigurált ügynökök és System Center Operations Manager felügyeleti csoportok a helyreállítható törlési időszak alatt árva állapotban maradnak. A szolgáltatás továbbá lehetővé teszi a törölt munkaterület helyreállítását, beleértve az adatokat és a csatlakoztatott erőforrásokat, lényegében a törlés visszavonása.

> [!NOTE] 
> A telepített megoldások és a kapcsolódó szolgáltatások, például az Azure Automation-fiók véglegesen törlődnek a munkaterületről a törlés időpontjában, és nem lehet visszaőket visszavonni. Ezeket a helyreállítási művelet után újra kell konfigurálni, hogy a munkaterület a korábban konfigurált állapotba kerüljön.

A munkaterületet törölheti a [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)vagy az Azure Portal [használatával.](https://portal.azure.com)

### <a name="azure-portal"></a>Azure portál

1. A bejelentkezéshez nyissa meg az [Azure Portalt.](https://portal.azure.com) 
2. Az Azure Portalon válassza a **Minden szolgáltatás**lehetőséget. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **a Log Analytics-munkaterületek lehetőséget.**
3. A Log Analytics-munkaterületek listájában jelöljön ki egy munkaterületet, majd kattintson a **Törlés** gombra a középső ablaktábla tetején.
   ![Törlés beállítás a Munkaterület tulajdonságai ablaktáblából](media/delete-workspace/log-analytics-delete-workspace.png)
4. Amikor megjelenik a megerősítő üzenetablak, amely a munkaterület törlésének megerősítését kéri, kattintson az **Igen**gombra.
   ![Munkaterület törlésének megerősítése](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>Állandó munkaterület törlése
Előfordulhat, hogy a helyreállítható törlési módszer nem fér el bizonyos esetekben, például a fejlesztés és a tesztelés, ahol meg kell ismételni e központi telepítést ugyanazzal a beállítással és munkaterület nevével. Ilyen esetekben véglegesen törölheti a munkaterületet, és "felülbírálhatja" a helyreállítható törlési időszakot. Az állandó munkaterület-törlési művelet felszabadítja a munkaterület nevét, és ugyanazzal a névvel új munkaterületet hozhat létre.


> [!IMPORTANT]
> Használja az állandó munkaterület-törlési műveletet óvatosan, mivel visszafordíthatatlan, és nem fogja tudni helyreállítani a munkaterületet és annak adatait.

Az állandó munkaterület törlése jelenleg rest API-n keresztül hajtható végre.

> [!NOTE]
> Minden API-kérelemnek tartalmaznia kell egy tulajdonosi engedélyezési jogkivonatot a kérelem fejlécében.
>
> A token a következő használatával szerezhető be:
> - [Alkalmazásregisztrációk](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - Keresse meg az Azure Portalt a fejlesztői konzol (F12) használatával a böngészőben. Keresse meg a hitelesítési karakterlánc egyik **kötegpéldányát** a **Fejlécek kérése területen.** Ez lesz a minta *engedély: bemutatóra <token> *. Másolja és adja hozzá ezt az API-híváshoz a példákban látható módon.
> - Keresse meg az Azure REST dokumentációs webhelyét. Nyomja **meg a Próbálja ki** bármelyik API-n, másolja a tulajdonosi jogkivonatot, és adja hozzá az API-híváshoz.
A munkaterület végleges törléséhez használja a [Workspaces - REST]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) API-hívás törlése kényszerítési címkével:
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```
Ahol az "eyJ0eXAiOiJKV1Qi..." a teljes engedélyezési jogkivonatot jelöli.

## <a name="recover-workspace"></a>Munkaterület helyreállítása

Ha közreműködői engedélyekkel rendelkezik ahhoz az előfizetéshez és erőforráscsoporthoz, amelyhez a munkaterületet a helyreállítható törlési művelet előtt társították, a helyreállítható a helyreállítható törlési időszak alatt, beleértve az adatokat, a konfigurációt és a csatlakoztatott ügynököket. A helyreállítható törlési időszak után a munkaterület nem állítható helyre, és végleges törlésre van rendelve. A törölt munkaterületek nevei megmaradnak a helyreállítható törlési időszakban, és nem használhatók új munkaterület létrehozásakor.  

A munkaterület et a következő munkaterület-létrehozási módszerekkel hozhatja létre újra: [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) vagy [REST API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) mindaddig, amíg a következő tulajdonságok a törölt munkaterületi adatokkal vannak feltöltve:

* Előfizetés azonosítója
* Erőforráscsoport neve
* Munkaterület neve
* Régió

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

A munkaterület és annak összes adata a helyreállítási művelet után kerül vissza. A megoldások és a csatolt szolgáltatások véglegesen törlődtek a munkaterületről, amikor törölték, és ezeket újra kell konfigurálni, hogy a munkaterület et a korábban konfigurált állapotba hozzák. Előfordulhat, hogy az adatok egy része nem érhető el lekérdezésre a munkaterület helyreállítása után, amíg a társított megoldásokat újra nem telepíti, és a sémákat hozzá nem adja a munkaterülethez.

> [!NOTE]
> * A munkaterület-helyreállítás nem támogatott az [Azure Portalon.](https://portal.azure.com) 
> * A munkaterület újbóli létrehozása a helyreállítható törlési időszak alatt azt jelzi, hogy ez a munkaterület neve már használatban van. 
> 
