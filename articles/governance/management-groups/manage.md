---
title: Hogyan működik együtt a felügyeleti csoportokkal - Azure Governance
description: További információ a felügyeleti csoporthierarchiájának megtekintéséről, karbantartásáról, frissítéséről és törléséről.
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: 94df67888c0ed0ea532844a92a362a181621d3d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78267936"
---
# <a name="manage-your-resources-with-management-groups"></a>Az erőforrások kezelése felügyeleti csoportokkal

Ha a vállalatnak sok előfizetése van, jól jöhet egy módszer, hogy hatékonyan kezelje az előfizetésekhez való hozzáférést, a szabályzatokat és a megfelelőséget. Az Azure Management Groups előfizetések fölötti hatókörszintet biztosít. Az előfizetéseket „felügyeleti csoportok” nevű tárolókba rendezheti, és az irányítási feltételeket alkalmazhatja a felügyeleti csoportokra. A felügyeleti csoporton belüli összes előfizetés automatikusan örökli a felügyeleti csoportra alkalmazott feltételeket.

A felügyeleti csoportok nagy léptékű, nagyvállalati szintű felügyeletet tesznek lehetővé, függetlenül az előfizetése típusától.  Ha többet szeretne megtudni a felügyeleti csoportokról, [olvassa el az Erőforrások rendszerezése az Azure felügyeleti csoportokkal](overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

>[!IMPORTANT]
>Az Azure Resource Manager felhasználói jogkivonatok és felügyeleti csoport gyorsítótára 30 percig tart, mielőtt frissítésre kényszerülnek.  Bármilyen művelet elvégzése után, például egy felügyeleti csoport vagy előfizetés áthelyezése után akár 30 percet is igénybe vehet a megjelenítés.  
>A frissítések hamarabb történő megtekintéséhez frissítenie kell a tokent a böngésző frissítésével, a be- és kijelentkezéssel, vagy egy új jogkivonat kérésével.  



## <a name="change-the-name-of-a-management-group"></a>Felügyeleti csoport nevének módosítása

Módosíthatja a felügyeleti csoport nevét a portál, a PowerShell vagy az Azure CLI használatával.

### <a name="change-the-name-in-the-portal"></a>A név módosítása a portálon

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Válassza az **Összes szolgáltatásfelügyeleti** > **csoport lehetőséget.**

1. Válassza ki az átnevezni kívánt felügyeleti csoportot.

1. Válassza ki **a részleteket**.

1. Válassza a **Csoport átnevezése** lehetőséget a lap tetején.

   ![Csoport átnevezése beállítás a felügyeleti csoport lapján](./media/detail_action_small.png)

1. Amikor megnyílik a menü, írja be a megjeleníteni kívánt új nevet.

   ![Csoport átnevezése a felügyeleti csoport átnevezéséhez](./media/rename_context.png)

1. Kattintson a **Mentés** gombra.

### <a name="change-the-name-in-powershell"></a>A név módosítása a PowerShellben

A megjelenítendő név frissítéséhez használja az **Update-AzManagementGroup szolgáltatást.** Ha például egy felügyeleti csoportok megjelenítendő nevét "Contoso IT" névről "Contoso Group" névre szeretné módosítani, futtassa a következő parancsot:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>A név módosítása az Azure CLI-ben

Az Azure CLI esetében használja a frissítési parancsot.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Felügyeleti csoport törlése

A felügyeleti csoport törléséhez a következő követelményeknek kell megfelelni:

1. A felügyeleti csoport ban nincsenek gyermekfelügyeleti csoportok vagy előfizetések.

   - Ha egy előfizetést vagy felügyeleti csoportot át szeretne helyezni egy másik felügyeleti csoportba, olvassa el [a Felügyeleti csoportok és előfizetések áthelyezése a hierarchiában című témakört.](#moving-management-groups-and-subscriptions)

1. Írási engedélyeket kell írnia a felügyeleti csoporthoz ("Tulajdonos", "Közreműködő" vagy "Felügyeleti csoport közreműködője"). A jogosultságok megtekintéséhez jelölje ki a felügyeleti csoportot, majd válassza az **IAM**lehetőséget. Az RBAC-szerepkörökről a [Hozzáférés és engedélyek kezelése az RBAC-mal című témakörben](../../role-based-access-control/overview.md)olvashat bővebben.  

### <a name="delete-in-the-portal"></a>Törlés a portálon

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Válassza az **Összes szolgáltatásfelügyeleti** > **csoport lehetőséget.**

1. Válassza ki a törölni kívánt felügyeleti csoportot.

1. Válassza ki **a részleteket**.

1. **Törlés kijelölése**

    > [!TIP]
    > Ha az ikon le van tiltva, az egérválasztó nak az ikon fölé mutatása megmutatja az okát.

   ![Csoport törlése beállítás](./media/delete.png)

1. Van egy ablak, amely megerősíti, hogy törölni szeretné a felügyeleti csoportot.

   ![Csoport visszaigazolási ablakának törlése](./media/delete_confirm.png)

1. Válassza az **Igen** lehetőséget.

### <a name="delete-in-powershell"></a>Törlés a PowerShellben

Felügyeleti csoportok törlése a PowerShell en belüli **Remove-AzManagementGroup** paranccsal.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Törlés az Azure CLI felületen

Az Azure CLI, használja a parancsot az fiókfelügyeleti csoport törlése.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Felügyeleti csoportok megtekintése

Megtekintheti bármelyik felügyeleti csoport rendelkezik közvetlen vagy örökölt RBAC szerepkör.  

### <a name="view-in-the-portal"></a>Megtekintés a portálon

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Válassza az **Összes szolgáltatásfelügyeleti** > **csoport lehetőséget.**

1. A felügyeleti csoport hierarchia lapja betöltődik. Ezen az oldalon fedezheti fel az összes felügyeleti csoportot és előfizetést, amelyhez hozzáférése van. A csoportnév kijelölése a hierarchia egy szintjével lefelé vezet. A navigáció ugyanúgy működik, mint a fájlkezelő.

1. A felügyeleti csoport részleteinek megtekintéséhez válassza a **(részletek)** hivatkozást a felügyeleti csoport címe mellett. Ha ez a hivatkozás nem érhető el, nincs engedélye az adott felügyeleti csoport megtekintéséhez.

   ![Fő](./media/main.png)

### <a name="view-in-powershell"></a>Megtekintés a PowerShellben

A Get-AzManagementGroup paranccsal az összes csoportot beszeretné olvasni.  A GET PowerShell-parancsok felügyeleti csoport teljes listáját az [Az.Resources](/powershell/module/az.resources/Get-AzManagementGroup) modulok című témakörben található.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Egyetlen felügyeleti csoport adataihoz használja a -GroupName paramétert

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Adott felügyeleti csoport és az alatta lévő hierarchia összes szintjének visszaadására használja a **-Expand** and **-Recurse** paramétereket.  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>Megtekintés az Azure CLI-ben

A listaparanccsal az összes csoportot bekeresheti.  

```azurecli-interactive
az account management-group list
```

Egyetlen felügyeleti csoport információihoz használja a show parancsot

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Adott felügyeleti csoport és az alatta lévő hierarchia összes szintjének visszaadására használja a **-Expand** and **-Recurse** paramétereket.

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="moving-management-groups-and-subscriptions"></a>Felügyeleti csoportok és előfizetések áthelyezése   

Felügyeleti csoport létrehozásának egyik oka az előfizetések összekötegelése. Csak felügyeleti csoportok és előfizetések lehet egy másik felügyeleti csoport gyermekei. Egy felügyeleti csoportba áthelyező előfizetés örökli az összes felhasználói hozzáférést és házirendet a fölérendelt felügyeleti csoporttól

Amikor egy felügyeleti csoport vagy előfizetés áthelyezése egy másik felügyeleti csoport három szabály gyermekének áthelyezése kor igazként kell kiértékelni.

Ha a költözési műveletet végzi, a következőkre van szüksége: 

-  Felügyeleti csoport írási és szerepkör-hozzárendelés írási engedélyei a gyermek-előfizetésvagy felügyeleti csoport.
    - Beépített szerepkör példa **tulajdonos**
- A felügyeleti csoport írási hozzáférése a célszülő felügyeleti csoporthoz.
    - Példa beépített szerepkörre: **Tulajdonos,** **Közreműködő**, **Felügyeleti csoport közreműködője**
- A felügyeleti csoport írási hozzáférése a meglévő szülőfelügyeleti csoporthoz.
    - Példa beépített szerepkörre: **Tulajdonos,** **Közreműködő**, **Felügyeleti csoport közreműködője**

**Kivétel**: Ha a cél vagy a meglévő szülő felügyeleti csoport a legfelső szintű felügyeleti csoport, az engedélykövetelmények nem érvényesek. Mivel a gyökérfelügyeleti csoport az összes új felügyeleti csoport és előfizetés alapértelmezett leszállási helye, nincs szüksége engedélyekre egy elem áthelyezéséhez.

Ha az előfizetés tulajdonosi szerepköre az aktuális felügyeleti csoporttól öröklődik, az áthelyezési célok korlátozottak. Az előfizetést csak egy másik felügyeleti csoportba helyezheti át, ahol a Tulajdonos szerepkör van. Nem helyezheti át egy felügyeleti csoportba, ahol közreműködő, mert elveszítené az előfizetés tulajdonjogát. Ha közvetlenül hozzá van rendelve az előfizetés tulajdonosi szerepköréhez (nem a felügyeleti csoporttól örökölt), áthelyezheti azt bármely olyan felügyeleti csoportba, ahol közreműködő. 

Ha meg szeretné tekinteni, hogy milyen engedélyekkel rendelkezik az Azure Portalon, válassza ki a felügyeleti csoportot, majd az **IAM**lehetőséget. Az RBAC-szerepkörökről a [Hozzáférés és engedélyek kezelése az RBAC-mal című témakörben](../../role-based-access-control/overview.md)olvashat bővebben.


## <a name="move-subscriptions"></a>Előfizetések áthelyezése 

#### <a name="add-an-existing-subscription-to-a-management-group-in-the-portal"></a>Meglévő előfizetés hozzáadása egy felügyeleti csoporthoz a portálon

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Válassza az **Összes szolgáltatásfelügyeleti** > **csoport lehetőséget.**

1. Válassza ki azt a felügyeleti csoportot, amelyet szülőnek kíván tekinteni.

1. A lap tetején válassza az **Előfizetés hozzáadása lehetőséget.**

1. Válassza ki a megfelelő azonosítóval rendelkező előfizetést a listában.

   ![Felügyeleti csoporthoz hozzáadandó elérhető előfizetések](./media/add_context_sub.png)

1. Válassza a "Mentés" lehetőséget.

#### <a name="remove-a-subscription-from-a-management-group-in-the-portal"></a>Előfizetés eltávolítása egy felügyeleti csoportból a portálon

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Válassza az **Összes szolgáltatásfelügyeleti** > **csoport lehetőséget.**

1. Válassza ki azt a felügyeleti csoportot, amelyet az aktuális szülőnek tervez.  

1. Jelölje ki az áthelyezni kívánt listában az áthelyezni kívánt előfizetés sorának végén lévő ellipszist.

   ![Áthelyezési beállítás egy felügyeleti csoportban](./media/move_small.png)

1. Válassza az **Áthelyezés**lehetőséget.

1. A megnyíló menüben válassza a **Szülő felügyeleti csoport lehetőséget.**

   ![Ablaktábla áthelyezése a szülőcsoport módosításához](./media/move_small_context.png)

1. Kattintson a **Mentés** gombra.

### <a name="move-subscriptions-in-powershell"></a>Előfizetések áthelyezése a PowerShellben

Előfizetés áthelyezéséhez a PowerShellben a New-AzManagementGroupSubscription parancsot használja.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Az Előfizetés és a felügyeleti csoport közötti kapcsolat eltávolításához használja az Remove-AzManagementGroupSubscription parancsot.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Előfizetések áthelyezése az Azure CLI-ben

Ha egy előfizetést át szeretne helyezni a CLI-ben, használja a hozzáadás parancsot.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Az előfizetés felügyeleti csoportból való eltávolításához használja az előfizetés eltávolítási parancsát.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups"></a>Felügyeleti csoportok áthelyezése 

### <a name="move-management-groups-in-the-portal"></a>Felügyeleti csoportok áthelyezése a portálon

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Válassza az **Összes szolgáltatásfelügyeleti** > **csoport lehetőséget.**

1. Válassza ki azt a felügyeleti csoportot, amelyet szülőnek kíván tekinteni.

1. A lap tetején válassza a **Felügyeleti csoport hozzáadása lehetőséget.**

1. A megnyíló menüben válassza ki, hogy újat szeretne-e, vagy meglévő felügyeleti csoportot szeretne használni.

   - Az új kiválasztása új felügyeleti csoportot hoz létre.
   - Egy meglévő kiválasztása megmutatja az összes felügyeleti csoport legördülő legördülő menüt, amelyet áthelyezhet ebbe a felügyeleti csoportba.  

   ![Felügyeleti csoport áthelyezése új vagy meglévő csoportba](./media/add_context_MG.png)

1. Kattintson a **Mentés** gombra.

### <a name="move-management-groups-in-powershell"></a>Felügyeleti csoportok áthelyezése a PowerShellben

A PowerShell Update-AzManagementGroup parancsával helyezzen át egy felügyeleti csoportot egy másik csoportba.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Felügyeleti csoportok áthelyezése az Azure CLI-ben

A frissítési paranccsal áthelyezheti a felügyeleti csoportot az Azure CLI-vel.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>Felügyeleti csoportok naplózása tevékenységnaplókkal

A felügyeleti csoportok támogatottak az [Azure-tevékenységnaplóban](../../azure-monitor/platform/platform-logs-overview.md). Lekérdezheti az összes eseményt, amely egy felügyeleti csoport ugyanazon a központi helyen történik, mint más Azure-erőforrások.  Például megtekintheti egy adott felügyeleti csoporthoz tartozó összes szerepkör-hozzárendelés vagy szabályzat-hozzárendelés módosításait.

![Tevékenységnaplók felügyeleti csoportokkal](media/al-mg.png)

Az Azure Portalon kívüli felügyeleti csoportok lekérdezésekor a felügyeleti csoportok célhatóköre a következőhöz hasonlóan néz ki: **"/ providers/Microsoft.Management/managementGroups/{yourMgID}"**.

## <a name="referencing-management-groups-from-other-resource-providers"></a>Hivatkozás más erőforrás-szolgáltatók felügyeleti csoportjaira

Ha más erőforrás-szolgáltató idomaiból származó felügyeleti csoportokra hivatkozik, használja a következő elérési utat hatókörként. Ez az elérési út a PowerShell, az Azure CLI és a REST API-k használatakor használatos.  

>"/providers/Microsoft.Management/managementGroups/{yourMgID}"

Ennek az elérési útnak a használatára példa, amikor új szerepkör-hozzárendelést rendel egy felügyeleti csoporthoz a PowerShellben

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Ugyanaz takörelérési út használatos egy felügyeleti csoport házirend-definíciójának beolvasásakor.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>További lépések

A felügyeleti csoportokkal kapcsolatos további tudnivalókért lásd:

- [Felügyeleti csoportok létrehozása az Azure-erőforrások rendszerezéséhez](create.md)
- [Felügyeleti csoportok módosítása, törlése és kezelése](manage.md)
- [Felügyeleti csoportok áttekintése az Azure PowerShell Erőforrások moduljában](/powershell/module/az.resources#resources)
- [Felügyeleti csoportok áttekintése a REST API-ban](/rest/api/resources/managementgroups)
- [Felügyeleti csoportok áttekintése az Azure CLI-ben](/cli/azure/account/management-group)