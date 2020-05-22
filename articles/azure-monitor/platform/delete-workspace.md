---
title: Az Azure Log Analytics munkaterület törlése és helyreállítása | Microsoft Docs
description: Megtudhatja, hogyan törölheti Log Analytics munkaterületét, ha létrehozott egy személyes előfizetést, vagy átalakította a munkaterület modelljét.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/19/2020
ms.openlocfilehash: 5ab71ee67b66cacbcd1b23fa35d6f424021fa9cc
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83757521"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Az Azure Log Analytics munkaterület törlése és helyreállítása

Ez a cikk ismerteti az Azure Log Analytics Workspace Soft-delete fogalmát, valamint a törölt munkaterület helyreállításának módját.

## <a name="considerations-when-deleting-a-workspace"></a>Munkaterületek törlésekor megfontolandó szempontok

Ha töröl egy Log Analytics munkaterületet, a rendszer egy törlési műveletet hajt végre, amely lehetővé teszi a munkaterület helyreállítását 14 napon belül, beleértve az adatmennyiséget és a csatlakoztatott ügynököket is, függetlenül attól, hogy a törlés véletlen vagy szándékos volt-e. A Soft-delete időszak után a munkaterület-erőforrás és az azokhoz tartozó adattárolók nem állíthatók vissza. a rendszer az adatsorokat állandó törlés céljából várólistára helyezi, és 30 napon belül teljesen el lesz távolítva. A munkaterület neve "Released", és használható egy új munkaterület létrehozásához.

> [!NOTE]
> Ha szeretné felülbírálni a törlési viselkedést és véglegesen törölni a munkaterületet, kövesse az [állandó munkaterület törlésének](#permanent-workspace-delete)lépéseit.

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

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. A Azure Portal válassza a **minden szolgáltatás**lehetőséget. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **log Analytics munkaterületek**lehetőséget.
3. A Log Analytics munkaterületek listájában válasszon ki egy munkaterületet, majd kattintson a középső ablaktábla tetején található **Törlés** elemre.
4. Megjelenik egy megerősítő lap, amely megjeleníti az adatfeldolgozást a munkaterületre az elmúlt héten. A megerősítéshez írja be a munkaterület nevét, majd kattintson a **Törlés**gombra.

   ![Munkaterület törlésének megerősítése](media/delete-workspace/workspace-delete.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

### <a name="troubleshooting"></a>Hibaelhárítás

A munkaterület törléséhez legalább *log Analytics közreműködői* engedélyekkel kell rendelkeznie.<br>
Ha hibaüzenet jelenik meg, akkor *a munkaterület neve már használatban van* , vagy *ütközést* okoz a munkaterület létrehozásakor, az a következő lehet:
* A munkaterület neve nem érhető el, és a szervezete vagy más ügyfél által használt személy használja.
* A munkaterületet az elmúlt 14 napban törölték, és a neve a törlési időszak számára fenntartott marad. Ha felül szeretné bírálni a munkaterületet, hogy az azonos nevű új munkaterületet hozzon létre, az alábbi lépéseket követve állítsa helyre a munkaterületet, és végezzen végleges törlést:<br>
   1. [Állítsa helyre](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace) a munkaterületet.
   2. A munkaterület [végleges törlése](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete) .
   3. Hozzon létre egy új munkaterületet ugyanazzal a munkaterület-névvel.

## <a name="permanent-workspace-delete"></a>Állandó munkaterület törlése
Előfordulhat, hogy a Soft-Delete metódus nem fér el bizonyos helyzetekben, például a fejlesztéshez és a teszteléshez, ahol meg kell ismételnie egy központi telepítést ugyanazzal a beállításokkal és a munkaterület nevével. Ilyen esetekben véglegesen törölheti a munkaterületet, és "felülbírálhatja" a Soft-delete időszakot. Az állandó munkaterület-törlési művelet kiadja a munkaterület nevét, és létrehoz egy új munkaterületet ugyanazzal a névvel.


> [!IMPORTANT]
> Az állandó munkaterület törlési műveletét körültekintően, a visszafordíthatatlan óta használhatja, és nem fogja tudni helyreállítani a munkaterületet és annak adatait.

Az állandó munkaterület törlése jelenleg REST APIon keresztül végezhető el.

> [!NOTE]
> Minden API-kérésnek tartalmaznia kell egy tulajdonosi engedélyezési jogkivonatot a kérelem fejlécében.
>
> A jogkivonat beszerzéséhez használja a következőket:
> - [Alkalmazásregisztrációk](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - Navigáljon a Azure Portal a fejlesztői konzolján (F12) a böngészőben. Megkeresi a **Batch?** példányokat a hitelesítési karakterlánchoz a **kérelmek fejléce**alatt. Ez lesz a minta *engedélyezése: tulajdonos <token> *. Másolja és adja hozzá ezt az API-híváshoz, ahogy az a példákban is látható.
> - Navigáljon az Azure REST dokumentációs webhelyére. kattintson a **kipróbálás** bármely API-ra lehetőségre, másolja a tulajdonosi jogkivonatot, és adja hozzá az API-híváshoz.
A munkaterület végleges törléséhez használja a [munkaterületeket – törölje a REST API-]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) hívást egy kényszerítő címkével:
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```
Hol "eyJ0eXAiOiJKV1Qi..." a teljes engedélyezési tokent jelöli.

## <a name="recover-workspace"></a>Munkaterület helyreállítása
Ha véletlenül vagy szándékosan töröl egy Log Analytics munkaterületet, a szolgáltatás a munkaterületet egy olyan törlési állapotba helyezi, amely elérhetetlenné teszi a műveletet. A törölt munkaterület neve a törlési időszakban megmarad, és nem használható új munkaterület létrehozásához. A törlést követően a munkaterület nem állítható vissza, ezért a rendszer végleges törlésre és a hozzá tartozó névre ütemezi, és felhasználható egy új munkaterület létrehozásához.

A munkaterületet helyreállíthatja a Soft-delete időszakban, beleértve az adatait, a konfigurációját és a csatlakoztatott ügynököket is. Az előfizetés és az erőforráscsoport közreműködői engedélyekkel kell rendelkeznie, ahol a munkaterület a törlési művelet előtt található. A munkaterület helyreállítását egy Log Analytics munkaterület létrehozásával végezheti el a törölt munkaterület részleteivel együtt, beleértve a következőket:

- Előfizetés azonosítója
- Erőforráscsoport neve
- Munkaterület neve
- Régió

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. A Azure Portal válassza a **minden szolgáltatás**lehetőséget. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **log Analytics munkaterületek**lehetőséget. Megjelenik a kiválasztott hatókörben lévő munkaterületek listája.
3. Kattintson a bal felső menü **helyreállítás** elemére egy olyan oldal megnyitásához, amelyben munkaterületek állíthatók helyre a helyreállítható törlési állapotban.

   ![Munkaterület helyreállítása](media/delete-workspace/recover-menu.png)

4. Válassza ki a munkaterületet, majd kattintson a **helyreállítás** elemre a munkaterület helyreállításához.

   ![Munkaterület helyreállítása](media/delete-workspace/recover-workspace.png)


### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

A rendszer a helyreállítási művelet után visszaküldi a munkaterületet és az összes adatforrást. A megoldások és a társított szolgáltatások véglegesen el lettek távolítva a munkaterületről a törlés után, és ezeket újra kell konfigurálni, hogy a munkaterület a korábban konfigurált állapotba kerüljön. Előfordulhat, hogy egyes adatmennyiségek nem állnak rendelkezésre a lekérdezéshez a munkaterület helyreállítása után, amíg a társított megoldások újra nem települnek, és a sémáik hozzá lettek adva a munkaterülethez.

> [!NOTE]
> * A munkaterület helyreállítása nem támogatott a [Azure Portalban](https://portal.azure.com). 
> * Egy munkaterület újbóli létrehozása a Soft-delete időszakban azt jelzi, hogy a munkaterület neve már használatban van. 
> 
