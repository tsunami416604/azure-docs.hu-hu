---
title: Erőforrások monitorozása az Azure-Tevékenységnaplók megtekintése |} A Microsoft Docs
description: Használja a tevékenységnaplókat, tekintse át a felhasználói műveletek és hibák. Az Azure Portal PowerShell, az Azure CLI és REST jeleníti meg.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: tomfitz
ms.openlocfilehash: e7bcfce4e9d23839c623be3b54e97d931e6454cb
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268294"
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>Tevékenységnaplók megtekintése az erőforrásokon végzett műveletek naplózásához

A tevékenységnaplókból a következők állapíthatók meg:

* milyen műveletek vették erőforrásokra az előfizetésben
* ki indította el a műveletet
* A művelet végrehajtásának időpontja
* A műveletnek az állapota
* Egyéb tulajdonságokat, amelyek segíthetnek vizsgálja meg a műveletet

A tevékenységnapló tartalmazza az erőforrásokon végrehajtott összes írási műveletet (PUT, POST, DELETE). Olvasási műveleteket (GET) nem tartalmazza. Erőforrás-műveletek listáját lásd: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md). A vizsgálati naplók keresse meg a hiba elhárításához vagy egy a szervezet felhasználói hogyan módosították az erőforrásokat figyelésére használható.

A Tevékenységnaplók 90 napig őrzi meg. Lekérdezheti, ha bármely dátumtartomány, mindaddig, amíg a kezdő dátum nem az elmúlt 90 napnál hosszabb.

Információ a vizsgálati naplók a portálon keresztül, PowerShell, az Azure CLI-vel, az Insights REST API-t lehet lekérdezni vagy [Insights .NET-kódtár](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="the-azure-portal"></a>Az Azure Portal

1. A portálon keresztül a vizsgálati naplók megtekintéséhez jelölje ki **figyelő**.

    ![Válassza ki a figyelő](./media/resource-group-audit/select-monitor.png)

1. Válassza ki **tevékenységnapló**.

    ![Válassza ki a tevékenységnaplóban](./media/resource-group-audit/select-activity-log.png)

1. A legutóbbi műveletek összegzése látható. Szűrők alapértelmezett készletét alkalmazza a rendszer a műveleteket.

    ![Legutóbbi tevékenységek összegzésének megtekintése](./media/resource-group-audit/audit-summary.png)

1. Szűrők egy előre meghatározott készletével gyorsan futtatásához válassza **gyors elemzések** , és válasszon a lehetőségek közül.

    ![lekérdezés kiválasztása](./media/resource-group-audit/quick-insights.png)

1. Meghatározott műveleteket összpontosíthat, módosítsa a szűrőket, vagy újakat a alkalmazni. Például az alábbi képen látható, új érték a **Timespan** és **erőforrástípus** tárfiókok értékre van állítva. 

    ![szűrő beállításainak megadása](./media/resource-group-audit/set-filter.png)

1. Ha szeretné újra futtatni a lekérdezést, válassza ki a **PIN-kód aktuális szűrők szerinti**.

    ![PIN-kód szűrőkkel](./media/resource-group-audit/pin-filters.png)

1. Nevezze el a szűrőt.

    ![Szűrő neve](./media/resource-group-audit/name-filters.png)

1. A szűrő érhető el az irányítópulton.

    ![Szűrő megjelenítése az irányítópulton](./media/resource-group-audit/show-dashboard.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Naplóbejegyzés lekéréséhez futtassa a **Get-AzLog** parancsot. Azt adja meg a további paramétereket bejegyzések listájának szűrése. Ha nem ad meg egy kezdő és záró idő, a rendszer az elmúlt hét nap bejegyzéseinek adja vissza.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup
  ```

    Az alábbi példa bemutatja, hogyan használhatja egy adott időszakban végrehajtott kutatási műveletek a tevékenységnaplóban. A kezdő és záró dátuma meg van adva a dátumformátum.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-01-09T06:00 -EndTime 2019-01-15T06:00
  ```

    Másik lehetőségként dátumfüggvények segítségével megadhatja a dátumtartományt, például az elmúlt 14 napban.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

* Egy adott, még egy erőforráscsoportot, amely már nem létezik a felhasználó által végrehajtott műveletek kereshet.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

* A sikertelen műveleteket végezhet.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -Status Failed
  ```

* Egy hiba történt az állapotüzenet tartalmazza az adott bejegyzés megnézzük összpontosíthat.

  ```azurepowershell-interactive
  ((Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties[0].Content.statusMessage | ConvertFrom-Json).error
  ```

* Visszaadott adatok a konkrét értékekre is választhat.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
  ```

* A megadott kezdési időpont, attól függően az előző parancsokkal az erőforráscsoporthoz tartozó műveletek számos régóta adhat vissza. Az eredmények mit keresett keresési feltételek megadásával szűrheti. Például szűrhet a művelet típusát.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
  ```

## <a name="azure-cli"></a>Azure CLI

* Naplóbejegyzés lekéréséhez futtassa a [az monitor tevékenységnapló lista](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) időtartomány jelzésére eltolás parancsot.

  ```azurecli-interactive
  az monitor activity-log list --resource-group ExampleGroup --offset 7d
  ```

  Az alábbi példa bemutatja, hogyan használhatja egy adott időszakban végrehajtott kutatási műveletek a tevékenységnaplóban. A kezdő és záró dátuma meg van adva a dátumformátum.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --start-time 2019-01-01 --end-time 2019-01-15
  ```

* Egy adott, még egy erőforráscsoportot, amely már nem létezik a felhasználó által végrehajtott műveletek kereshet.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
  ```

* A sikertelen műveleteket végezhet.

  ```azurecli-interactive
  az monitor activity-log list -g demoRG --status Failed --offset 1d
  ```

* Egy hiba történt az állapotüzenet tartalmazza az adott bejegyzés megnézzük összpontosíthat.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
  ```

* Visszaadott adatok a konkrét értékekre is választhat.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
  ```

* A megadott kezdési időpont, attól függően az előző parancsokkal az erőforráscsoporthoz tartozó műveletek számos régóta adhat vissza. Az eredmények mit keresett keresési feltételek megadásával szűrheti. Például szűrhet a művelet típusát.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
  ```

## <a name="rest-api"></a>REST API

A tevékenységnapló használata REST-műveleteinek részét képezik a [Insights REST API-JÁNAK](https://msdn.microsoft.com/library/azure/dn931943.aspx). Tevékenységnapló-események lekéréséhez lásd: [egy adott előfizetés felügyeleti eseményeinek listázása](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## <a name="next-steps"></a>További lépések

* Azure Tevékenységnaplók segítségével a Power bi-JAL nagyobb információkhoz juthat a műveletek az előfizetésében. Lásd: [megtekintése és elemzése a Power BI és egyéb Azure-tevékenységnaplóinak](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Biztonsági szabályzatok beállítása kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md).
* Üzembehelyezési műveletek megtekintése a parancsokkal kapcsolatos tudnivalókért lásd: [üzembehelyezési műveletek megtekintése](resource-manager-deployment-operations.md).
* Egy erőforrás az összes felhasználó számára a törlések megelőzése érdekében, lásd: [zárolhat erőforrásokat az Azure Resource Manager](resource-group-lock-resources.md).
* Minden Microsoft Azure erőforrás-kezelő szolgáltató számára elérhető műveletek listájának megtekintéséhez lásd: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md)
