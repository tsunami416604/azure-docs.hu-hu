---
title: Erőforrások monitorozása az Azure-Tevékenységnaplók megtekintése |} A Microsoft Docs
description: Használja a tevékenységnaplókat, tekintse át a felhasználói műveletek és hibák. Az Azure portal PowerShell, az Azure CLI és REST mutatja.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tomfitz
ms.openlocfilehash: 7ff45be4eea5c6e8ab83093847164ede0e94579a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65606564"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>A Tevékenységnaplók megtekintése az erőforrásokon műveletek figyelése

A tevékenységnaplókból a következők állapíthatók meg:

* milyen műveletek vették erőforrásokra az előfizetésben
* ki indította el a műveletet
* A művelet végrehajtásának időpontja
* A műveletnek az állapota
* Egyéb tulajdonságokat, amelyek segíthetnek vizsgálja meg a műveletet

A tevékenységnapló tartalmazza az összes írási műveletet (PUT, POST, DELETE) az erőforrások. Olvasási műveleteket (GET) nem tartalmazza. Erőforrás-műveletek listáját lásd: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md). A tevékenységnaplókból hibaelhárításkor megkeresheti a hibákat, vagy nyomon követheti, hogy a szervezete felhasználói hogyan módosították az erőforrásokat.

A Tevékenységnaplók 90 napig őrzi meg. Lekérdezheti, ha bármely dátumtartomány, mindaddig, amíg a kezdő dátum nem az elmúlt 90 napnál hosszabb.

Információ a vizsgálati naplók a portálon keresztül, PowerShell, az Azure CLI-vel, az Insights REST API-t lehet lekérdezni vagy [Insights .NET-kódtár](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="azure-portal"></a>Azure Portal

1. A portálon keresztül a vizsgálati naplók megtekintéséhez jelölje ki **figyelő**.

    ![Válassza ki a figyelő](./media/resource-group-audit/select-monitor.png)

1. Válassza ki **tevékenységnapló**.

    ![Válassza ki a tevékenységnaplóban](./media/resource-group-audit/select-activity-log.png)

1. A legutóbbi műveletek összegzése látható. Szűrők alapértelmezett készletét alkalmazza a rendszer a műveleteket. Figyelje meg, az összefoglaló információkat tartalmaz, ki indította el a műveletet, és ez mikor történt.

    ![Legutóbbi tevékenységek összegzésének megtekintése](./media/resource-group-audit/audit-summary.png)

1. Szűrők egy előre meghatározott készletével gyorsan futtatásához válassza **gyors elemzések**.

    ![Válassza ki a gyors elemzések](./media/resource-group-audit/select-quick-insights.png)

1. Válassza ki a beállítások. Válassza ki például **sikertelen üzembe helyezés** hibákat az üzemelő példányok megtekintéséhez.

    ![Válassza ki a sikertelen üzembe helyezés](./media/resource-group-audit/select-failed-deployments.png)

1. Figyelje meg, a szűrőket úgy módosítottuk, hogy a központi telepítési hibák fókusz az elmúlt 24 órában. Csak a szűrőknek megfelelő műveletek jelennek meg.

    ![Szűrők megtekintése](./media/resource-group-audit/view-filters.png)

1. Meghatározott műveleteket összpontosíthat, módosítsa a szűrőket, vagy újakat a alkalmazni. Például az alábbi képen látható, új érték a **Timespan** és **erőforrástípus** tárfiókok értékre van állítva. 

    ![szűrő beállításainak megadása](./media/resource-group-audit/set-filter.png)

1. Ha szeretné újra futtatni a lekérdezést, válassza ki a **PIN-kód aktuális szűrők szerinti**.

    ![PIN-kód szűrőkkel](./media/resource-group-audit/pin-filters.png)

1. Nevezze el a szűrőt.

    ![Szűrő neve](./media/resource-group-audit/name-filters.png)

1. A szűrő érhető el az irányítópulton.

    ![Szűrő megjelenítése az irányítópulton](./media/resource-group-audit/show-dashboard.png)

1. A portálról megtekintheti az erőforrásai. Lépjen vissza az alapértelmezett tekintse meg a figyelő, és válasszon ki egy műveletet, amely az érintett erőforrás módosítása.

    ![Művelet kiválasztása](./media/resource-group-audit/select-operation.png)

1. Válassza ki **módosítási előzmények (előzetes verzió)** , és válassza ki az elérhető műveletek egyikét.

    ![Válassza ki a változások nyomon követése](./media/resource-group-audit/select-change-history.png)

1. A módosítások az erőforrásban jelennek meg.

    ![Módosítások megjelenítése](./media/resource-group-audit/show-changes.png)

Változások nyomon követése kapcsolatos további információkért lásd: [beolvasása az erőforrás-módosítások](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Naplóbejegyzés lekéréséhez futtassa a **Get-AzLog** parancsot. Azt adja meg a további paramétereket bejegyzések listájának szűrése. Ha nem ad meg egy kezdő és záró idő, a rendszer az elmúlt hét nap bejegyzéseinek adja vissza.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

Az alábbi példa bemutatja, hogyan használhatja egy adott időszakban végrehajtott kutatási műveletek a tevékenységnaplóban. A kezdő és záró dátuma meg van adva a dátumformátum.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

Másik lehetőségként dátumfüggvények segítségével megadhatja a dátumtartományt, például az elmúlt 14 napban.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

Egy adott felhasználó által végrehajtott műveletek kereshet.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

A sikertelen műveleteket végezhet.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

Egy hiba történt az állapotüzenet tartalmazza az adott bejegyzés megnézzük összpontosíthat.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

Visszaadott adatok a konkrét értékekre is választhat.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

A megadott kezdési időpont, attól függően az előző parancsokkal az erőforráscsoporthoz tartozó műveletek számos régóta adhat vissza. Az eredmények mit keresett keresési feltételek megadásával szűrheti. Például szűrhet a művelet típusát.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Erőforrás Graph segítségével erőforrás módosítási előzményeit láthatja. További információkért lásd: [beolvasása az erőforrás-módosítások](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="azure-cli"></a>Azure CLI

Naplóbejegyzés lekéréséhez futtassa a [az monitor tevékenységnapló lista](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) időtartomány jelzésére eltolás parancsot.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

Az alábbi példa bemutatja, hogyan használhatja egy adott időszakban végrehajtott kutatási műveletek a tevékenységnaplóban. A kezdő és záró dátuma meg van adva a dátumformátum.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

Egy adott, még egy erőforráscsoportot, amely már nem létezik a felhasználó által végrehajtott műveletek kereshet.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

A sikertelen műveleteket végezhet.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

Egy hiba történt az állapotüzenet tartalmazza az adott bejegyzés megnézzük összpontosíthat.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

Visszaadott adatok a konkrét értékekre is választhat.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

A megadott kezdési időpont, attól függően az előző parancsokkal az erőforráscsoporthoz tartozó műveletek számos régóta adhat vissza. Az eredmények mit keresett keresési feltételek megadásával szűrheti. Például szűrhet a művelet típusát.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Erőforrás Graph segítségével erőforrás módosítási előzményeit láthatja. További információkért lásd: [beolvasása az erőforrás-módosítások](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="rest-api"></a>REST API

A tevékenységnapló használata REST-műveleteinek részét képezik a [Insights REST API-JÁNAK](/rest/api/monitor/). Tevékenységnapló-események lekéréséhez lásd: [egy adott előfizetés felügyeleti eseményeinek listázása](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>További lépések

* Azure Tevékenységnaplók segítségével a Power bi-JAL nagyobb információkhoz juthat a műveletek az előfizetésében. Lásd: [megtekintése és elemzése a Power BI és egyéb Azure-tevékenységnaplóinak](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Biztonsági szabályzatok beállítása kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md).
* Üzembehelyezési műveletek megtekintése a parancsokkal kapcsolatos tudnivalókért lásd: [üzembehelyezési műveletek megtekintése](resource-manager-deployment-operations.md).
* Egy erőforrás az összes felhasználó számára a törlések megelőzése érdekében, lásd: [zárolhat erőforrásokat az Azure Resource Manager](resource-group-lock-resources.md).
* Minden Microsoft Azure erőforrás-kezelő szolgáltató számára elérhető műveletek listájának megtekintéséhez lásd: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md)
