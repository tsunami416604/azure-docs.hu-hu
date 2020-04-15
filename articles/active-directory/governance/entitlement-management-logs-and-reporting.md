---
title: Archiválja & jelentést az Azure Monitorral – Az Azure AD-jogosultságok kezelése
description: Megtudhatja, hogyan archiválhatja a naplókat, és hogyan hozhat létre jelentéseket az Azure Directory-jogosultságkezelés azure-figyelőjével.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/14/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d59a508d03730a51e793a5e30e2c99a91af77ce8
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380199"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Archiválási naplók és jelentések az Azure AD-jogosultságok kezeléséről az Azure Monitorban

Az Azure AD legfeljebb 30 napig tárolja a naplózási eseményeket a naplóban. A naplózási adatokat azonban az alapértelmezett megőrzési időszaknál hosszabb ideig őrizheti meg, amelyet a [Hogyan tárol az Azure AD a jelentési adatok?](../reports-monitoring/reference-reports-data-retention.md) Ezután munkafüzeteket, egyéni lekérdezéseket és jelentéseket használhat az adatokról.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Az Azure AD konfigurálása az Azure Monitor használatára
Az Azure Monitor munkafüzetek használata előtt konfigurálnia kell az Azure AD-t, hogy küldje el a naplóinak másolatát az Azure Monitornak.

Az Azure AD-naplónaplók archiválásához az Azure Monitornak rendelkeznie kell egy Azure-előfizetésben. Az Azure Monitor Azure [AD-tevékenységnaplókban](../reports-monitoring/concept-activity-logs-azure-monitor.md)való használatának előfeltételeiről és becsült költségeiről az Azure Monitorban olvashat bővebben.

**Előfeltételi szerepkör**: Globális rendszergazda

1. Jelentkezzen be az Azure Portalon globális rendszergazdaként. Győződjön meg arról, hogy rendelkezik az Azure Monitor munkaterületet tartalmazó erőforráscsoporthoz.
 
1. Válassza az **Azure Active Directory,** majd kattintson **a diagnosztikai beállítások** a bal oldali navigációs menüfigyelés. Ellenőrizze, hogy van-e már olyan beállítás, amely ből a naplónaplókat a munkaterületre kell küldeni.

1. Ha még nincs beállítás, kattintson **a Diagnosztikai beállítás hozzáadása gombra.** Az [Azure AD-naplók integrálása az Azure Monitor naplóival](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) című cikkben található utasításokat az Azure AD-napló elküldéséhez az Azure Monitor munkaterületére.

    ![Diagnosztikai beállítások ablaktábla](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Miután a naplót elküldte az Azure Monitornak, válassza a **Log Analytics-munkaterületeket,** és válassza ki az Azure AD naplózási naplóit tartalmazó munkaterületet.

1. Válassza **a Felhasználás és a becsült költségek** lehetőséget, és kattintson az **Adatmegőrzés gombra.** Módosítsa a csúszkát arra a napokra, amoráink számát, hogy az adatok megfeleljenek a naplózási követelményeknek.

    ![A Log Analytics munkaterületeinek ablaktáblája](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. Később az Archivált naplódátumtartomány munkafüzetét az *Archivált naplódátumtartomány* munkafüzetét használhatja:  
    
    1. Válassza az **Azure Active Directory** lehetőséget, majd kattintson a **Munkafüzetek gombra.** 
    
    1. Bontsa ki az **Azure Active Directory hibaelhárításszakaszát,** és kattintson az **Archivált naplódátumtartomány ra.** 


## <a name="view-events-for-an-access-package"></a>Hozzáférési csomag eseményeinek megtekintése  

Egy hozzáférési csomag eseményeinek megtekintéséhez hozzáféréssel kell rendelkeznie az alapul szolgáló Azure-figyelő munkaterületéhez (az [Azure Monitor naplóadataihoz és munkaterületeihez való hozzáférés kezelése](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) az alábbi szerepkörök egyikében) és az alábbi szerepkörök egyikében: 

- Globális rendszergazda  
- Biztonsági rendszergazda  
- Biztonsági olvasó  
- Jelentésolvasó  
- Alkalmazás-rendszergazda  

Az események megtekintéséhez kövesse az alábbi eljárást: 

1. Az Azure Portalon válassza az **Azure Active Directory** lehetőséget, majd kattintson a **Munkafüzetek**elemre. Ha csak egy előfizetéssel rendelkezik, folytassa a 3. 

1. Ha több előfizetéssel rendelkezik, válassza ki a munkaterületet tartalmazó előfizetést.  

1. Jelölje ki az Access Package Activity nevű *munkafüzetet.* 

1. Ebben a munkafüzetben válasszon ki egy időtartományt (módosítsa **az Összeset,** ha nem biztos benne), és válasszon ki egy hozzáférési csomagazonosítót az adott időtartományban tevékenységet folytató összes hozzáférési csomag legördülő listájából. A kijelölt időtartomány ban történt hozzáférési csomaghoz kapcsolódó események jelennek meg.  

    ![Hozzáférési csomag eseményeinek megtekintése](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Minden sor tartalmazza a műveletet elindító felhasználó idejét, hozzáférési csomagazonosítóját, nevét, az objektumazonosítót, az upn-t és a műveletet elindító felhasználó megjelenítendő nevét.  További részleteket a JSON tartalmaz.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Egyéni Azure Monitor-lekérdezések létrehozása az Azure Portal használatával
Saját lekérdezéseket hozhat létre az Azure AD naplózási eseményein, beleértve a jogosultságkezelési eseményeket is.  

1. Az Azure Active Directory az Azure Portalon, kattintson a **Naplók** a figyelési szakasz a bal oldali navigációs menüben egy új lekérdezési lap létrehozásához.

1. A munkaterületnek a lekérdezéslap bal felső részén kell lennie. Ha több Azure Monitor-munkaterülettel rendelkezik, és az Azure AD naplózási eseményeinek tárolására használt munkaterület nem jelenik meg, kattintson **a Hatókör kiválasztása gombra.** Ezután válassza ki a megfelelő előfizetést és munkaterületet.

1. Ezután a lekérdezés szövegterületén törölje a "search *" karakterláncot, és cserélje le a következő lekérdezésre:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Ezután kattintson a **Futtatás**gombra. 

    ![A lekérdezés indításához kattintson a Futtatás gombra](./media/entitlement-management-logs-and-reporting/run-query.png)

A tábla alapértelmezés szerint az utolsó órában a jogosultságkezelés naplózási eseményeit jeleníti meg. A régebbi események megtekintéséhez módosíthatja az "Időtartomány" beállítást. A beállítás módosítása azonban csak azazure AD-t az Azure Monitornak való küldésre konfigurált eseményeket jeleníti meg.

Ha szeretné tudni az Azure Monitorban tartott legrégebbi és legújabb naplózási eseményeket, használja a következő lekérdezést:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Az Azure Monitor naplózási eseményeihez tárolt oszlopokról [az Azure AD naplózási naplóinak sémájának értelmezése az Azure Monitorban](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)című témakörben talál további információt.

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Egyéni Azure Monitor-lekérdezések létrehozása az Azure PowerShell használatával

A PowerShellen keresztül is elérheti a naplókat, miután konfigurálta az Azure AD-t, hogy naplókat küldjön az Azure Monitornak. Ezután parancsfájlok vagy a PowerShell parancssorból küldjön lekérdezéseket anélkül, hogy globális rendszergazdának kellene lennie a bérlőben. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Annak ellenőrzése, hogy a felhasználó vagy a szolgáltatásnév megfelelő szerepkör-hozzárendelést kapott-e

Győződjön meg arról, hogy az Azure AD-ben hitelesítést végző felhasználó vagy szolgáltatásnév a Megfelelő Azure-szerepkörben van a Log Analytics-munkaterületen. A szerepkör-beállítások vagy a Log Analytics-olvasó vagy a Log Analytics közreműködője. Ha már az egyik ilyen szerepkörben van, ugorjon [a Log Analytics-azonosító lekérése egyetlen Azure-előfizetéssel című részre.](#retrieve-log-analytics-id-with-one-azure-subscription)

A szerepkör-hozzárendelés beállításához és lekérdezés létrehozásához tegye a következő lépéseket:

1. Az Azure Portalon keresse meg a [Log Analytics-munkaterületet.](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)

1. Válassza **a Hozzáférés-vezérlés (IAM) lehetőséget.**

1. Ezután a **Hozzáadás** gombra kattintva vegyen fel szerepkör-hozzárendelést.

    ![Szerepkör-hozzárendelés hozzáadása](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Az Azure PowerShell-modul telepítése

Miután rendelkezik a megfelelő szerepkör-hozzárendeléssel, indítsa el a PowerShellt, és [telepítse az Azure PowerShell-modult](/powershell/azure/install-az-ps?view=azps-3.3.0) (ha még nem tette meg), a következő beírással:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Most már készen áll az Azure AD-n való hitelesítésre, és lekérheti a lekérdező Log Analytics-munkaterület azonosítóját.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Log Analytics-azonosító lekérése egyetlen Azure-előfizetéssel
Ha csak egyetlen Azure-előfizetéssel és egyetlen Log Analytics-munkaterülettel rendelkezik, írja be a következőt az Azure AD-n való hitelesítéshez, csatlakozzon az előfizetéshez, és olvassa be a munkaterületet:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Log Analytics-azonosító lekérése több Azure-előfizetéssel

 [A Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) egyszerre egy előfizetésben működik. Így ha több Azure-előfizetéssel rendelkezik, győződjön meg arról, hogy csatlakozik ahhoz, amelyik rendelkezik a Log Analytics munkaterülettel az Azure AD-naplókkal. 
 
 A következő parancsmagok megjelenítik az előfizetések listáját, és megkeresik a Log Analytics-munkaterületet tartalmazó előfizetés azonosítóját:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
A PowerShell-munkamenetet újra hitelesítheti és társíthatja `Connect-AzAccount –Subscription $subs[0].id`az adott előfizetéshez egy paranccsal, például . Ha többet szeretne megtudni arról, hogyan hitelesítheti magát az Azure-ba a PowerShellből, beleértve a nem interaktív adatokat is, olvassa el [a Bejelentkezés az Azure PowerShellhasználatával témakört.](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
)

Ha több Log Analytics-munkaterület van az adott előfizetésben, akkor a [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) parancsmag visszaadja a munkaterületek listáját. Ezután megtalálhatja azt, amelyik rendelkezik az Azure AD-naplók. A `CustomerId` parancsmag által visszaadott mező megegyezik a "Workspace-azonosító" értékével, amely az Azure Portalon jelenik meg a Log Analytics munkaterület áttekintése során.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>A lekérdezés küldése a Log Analytics-munkaterületre
Végül, ha már egy munkaterület et azonosított, [használhatja Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
) küldeni egy Kusto lekérdezést, hogy a munkaterületet. Ezek a lekérdezések [kusto lekérdezési nyelven](https://docs.microsoft.com/azure/kusto/query/)íródott.
 
A naplózási eseményrekordok dátumtartományát például lekérheti a Log Analytics-munkaterületről, a PowerShell-parancsmagokkal pedig a következőhöz hasonló lekérdezést küldhet:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

A jogosultságkezelési eseményeket a következő lekérdezéssel is lekérheti:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Következő lépések:
- [Interaktív jelentések létrehozása az Azure Monitor munkafüzeteivel](../../azure-monitor/app/usage-workbooks.md) 

