---
title: Az Azure tevékenységnaplóinak megtekintése az erőforrások figyeléséhez
description: A tevékenységnaplók segítségével tekintse át a felhasználói műveleteket és hibákat. Az Azure Portal PowerShell, az Azure CLI és a REST megjelenítése.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 9511090099894fad6708843f106570ed029f0c1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478135"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Tevékenységnaplók megtekintése az erőforrásokkal kapcsolatos műveletek figyeléséhez

A tevékenységnaplókból a következők állapíthatók meg:

* milyen műveleteket hajtottak végre az előfizetésben lévő erőforrásokon
* aki elindította a műveletet
* amikor a művelet történt
* a művelet állapota
* más tulajdonságok értékeit, amelyek segíthetnek a művelet kutatásában

A tevékenységnapló tartalmazza az erőforrások összes írási műveletét (PUT, POST, DELETE). Olvasási műveleteket (GET) nem tartalmaz. Az erőforrás-műveletek listáját az [Azure Resource Manager erőforrás-szolgáltató műveletei ben található.](../../role-based-access-control/resource-provider-operations.md) A tevékenységnaplókból hibaelhárításkor megkeresheti a hibákat, vagy nyomon követheti, hogy a szervezete felhasználói hogyan módosították az erőforrásokat.

A tevékenységnaplók 90 napig érhetők el. Bármilyen dátumtartományt lekérdezhet, amíg a kezdő dátum legfeljebb 90 nappal korábbra esik.

A tevékenységnaplókból a portálon, a PowerShellen, az Azure CLI-n, az Insights REST API-n vagy az [Insights .NET Library-en](https://www.nuget.org/packages/Microsoft.Azure.Insights/)keresztül kérhet be adatokat.

## <a name="azure-portal"></a>Azure portál

A tevékenységnaplók portálon keresztültörténő megtekintéséhez kövesse az alábbi lépéseket:

1. Az Azure Portal menüben válassza a **Monitor**lehetőséget, vagy keressen rá, és válassza a **Monitor** lehetőséget bármelyik lapon.

    ![Monitor kiválasztása](./media/view-activity-logs/select-monitor-from-menu.png)

1. Válassza a **Tevékenységnapló lehetőséget.**

    ![Tevékenységnapló kiválasztása](./media/view-activity-logs/select-activity-log.png)

1. Megjelenik a legutóbbi műveletek összegzése. A műveletekre a rendszer alapértelmezett szűrőkészletet alkalmaz. Figyelje meg, hogy az összefoglalóban szereplő információk tartalmazzák, hogy ki indította el a műveletet, és mikor történt.

    ![Legutóbbi műveletek összegzésének megtekintése](./media/view-activity-logs/audit-summary.png)

1. Előre definiált szűrőkészlet gyors futtatásához válassza a **Gyorselemzések**lehetőséget.

    ![Gyors elemzések kiválasztása](./media/view-activity-logs/select-quick-insights.png)

1. Válasszon a lehetőségek közül. Válassza például **a Sikertelen központi telepítések** lehetőséget a központi telepítések hibáinak megtekintéséhez.

    ![Sikertelen telepítések kiválasztása](./media/view-activity-logs/select-failed-deployments.png)

1. Figyelje meg, hogy a szűrők módosultak, hogy az elmúlt 24 órában a telepítési hibákra összpontosítsanak. Csak a szűrőknek megfelelő műveletek jelennek meg.

    ![Szűrők megtekintése](./media/view-activity-logs/view-filters.png)

1. Ha bizonyos műveletekre szeretne összpontosítani, módosítsa a szűrőket, vagy újakat alkalmazzon. Az alábbi képen például egy új érték jelenik meg a **Timespan** és **az erőforrás típus** készlet-hoz tárfiókok.

    ![Szűrőbeállítások megadása](./media/view-activity-logs/set-filter.png)

1. Ha később újra kell futtatnia a lekérdezést, jelölje be **az Aktuális szűrők rögzítése**jelölőnégyzetet.

    ![Szűrők rögzítése](./media/view-activity-logs/pin-filters.png)

1. Adjon nevet a szűrőnek.

    ![Névszűrők](./media/view-activity-logs/name-filters.png)

1. A szűrő elérhető az irányítópulton. Az Azure Portal menüjében válassza az **Irányítópult** lehetőséget.

    ![Szűrő megjelenítése az irányítópulton](./media/view-activity-logs/activity-log-on-dashboard.png)

1. A portálon megtekintheti egy erőforrás módosításait. Lépjen vissza a Figyelő alapértelmezett nézetére, és jelöljön ki egy olyan műveletet, amely egy erőforrás módosításával járt.

    ![Művelet kiválasztása](./media/view-activity-logs/select-operation.png)

1. Válassza **az Előzmények módosítása (előnézet)** lehetőséget, és válasszon egyet a rendelkezésre álló műveletek közül.

    ![Módosítási előzmények kiválasztása](./media/view-activity-logs/select-change-history.png)

1. Megjelennek az erőforrás változásai.

    ![Változások megjelenítése](./media/view-activity-logs/show-changes.png)

Ha többet szeretne megtudni a változási előzményekről, olvassa el az [Erőforrás-módosítások beszerezni című témakört.](../../governance/resource-graph/how-to/get-resource-changes.md)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A naplóbejegyzések beolvasásához futtassa a **Get-AzLog** parancsot. A bejegyzések listájának szűréséhez további paramétereket adhat meg. Ha nem ad meg kezdési és befejezési időpontot, a rendszer az elmúlt hét nap bejegyzéseit adja vissza.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

A következő példa bemutatja, hogyan használhatja a tevékenységnaplót egy adott idő alatt végrehajtott kutatási műveletekhez. A kezdő és záró dátumok dátumformátumban vannak megadva.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

Vagy dátumfüggvények segítségével megadhatja a dátumtartományt, például az elmúlt 14 napot.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

Megkeresheti egy adott felhasználó által végrehajtott műveleteket.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

Szűrheti a sikertelen műveleteket.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

Egy hibára összpontosíthat, ha megnézi a bejegyzés állapotüzenetét.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

Adott értékeket kiválaszthat a visszaadott adatok korlátozásához.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

A megadott kezdési időponttól függően az előző parancsok az erőforráscsoport műveleteinek hosszú listáját adhatják vissza. Keresési feltételek megadásával szűrheti a keresett eredményeket. Szűrhet például a művelet típusa szerint.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Az Erőforrásgráf segítségével megtekintheti egy erőforrás módosítási előzményeit. További információt az [Erőforrás-módosítások beszedése](../../governance/resource-graph/how-to/get-resource-changes.md)című témakörben talál.

## <a name="azure-cli"></a>Azure CLI

A naplóbejegyzések beolvasásához futtassa az [az monitor tevékenységnapló-lista](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) parancsát eltolással az időtartam jelzésére.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

A következő példa bemutatja, hogyan használhatja a tevékenységnaplót egy adott idő alatt végrehajtott kutatási műveletekhez. A kezdő és záró dátumok dátumformátumban vannak megadva.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

Megkeresheti egy adott felhasználó által végrehajtott műveleteket, még egy már nem létező erőforráscsoport esetében is.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

Szűrheti a sikertelen műveleteket.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

Egy hibára összpontosíthat, ha megnézi a bejegyzés állapotüzenetét.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

Adott értékeket kiválaszthat a visszaadott adatok korlátozásához.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

A megadott kezdési időponttól függően az előző parancsok az erőforráscsoport műveleteinek hosszú listáját adhatják vissza. Keresési feltételek megadásával szűrheti a keresett eredményeket. Szűrhet például a művelet típusa szerint.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Az Erőforrásgráf segítségével megtekintheti egy erőforrás módosítási előzményeit. További információt az [Erőforrás-módosítások beszedése](../../governance/resource-graph/how-to/get-resource-changes.md)című témakörben talál.

## <a name="rest-api"></a>REST API

A tevékenységnaplóval végzett munka REST-műveletei az [Insights REST API](/rest/api/monitor/)részét képezik. A tevékenységnapló-események beolvasásához olvassa [el A felügyeleti események listázása egy előfizetésben című témakört.](/rest/api/monitor/activitylogs)

## <a name="next-steps"></a>További lépések

* Az Azure-tevékenységnaplók a Power BI-val nagyobb betekintést nyerhetnek az előfizetésben végrehajtott műveletekbe. Lásd: [Az Azure-tevékenységnaplók megtekintése és elemzése a Power BI-ban és még sok más.](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)
* A biztonsági házirendek beállításáról az [Azure szerepköralapú hozzáférés-vezérlés című témakörben](../../role-based-access-control/role-assignments-portal.md)olvashat.
* Ha további részleteket szeretne megtudni az alkalmazások változásairól az infrastruktúrarétegből egészen az alkalmazás üzembe helyezéséig, olvassa el [az Alkalmazásváltozáselemzés használata az Azure Monitorban című témakört.](../../azure-monitor/app/change-analysis.md)
* A telepítési műveletek megtekintésére vonatkozó parancsokról a [Telepítési műveletek megtekintése című témakörben](../templates/deployment-history.md)olvashat.
* Ha tudni szeretné, hogyan előzheti meg egy erőforrás törlését az összes felhasználó számára, olvassa el az [Erőforrások zárolása az Azure Resource Manager használatával (Erőforrások zárolása az Azure Resource Managerrel) (Erőforrások zárolása az Azure Resource Managerrel) (Erőforrások zárolása az Azure Resource Managerrel) (Erőforrások zárolása az Azure Resource Managerrel)](lock-resources.md)témakört.
* Az egyes Microsoft Azure Resource Manager-szolgáltatók számára elérhető műveletek listájának megtekintéséhez olvassa el az [Azure Resource Manager erőforrás-szolgáltató műveleteicímű témakört.](../../role-based-access-control/resource-provider-operations.md)
