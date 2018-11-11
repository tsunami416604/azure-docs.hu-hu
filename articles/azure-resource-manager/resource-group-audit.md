---
title: Erőforrások monitorozása az Azure-Tevékenységnaplók megtekintése |} A Microsoft Docs
description: Használja a tevékenységnaplókat, tekintse át a felhasználói műveletek és hibák. Az Azure Portal PowerShell, az Azure CLI és REST jeleníti meg.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: tomfitz
ms.openlocfilehash: 09f7fba2b8ae3b3ccc8710ffe9302d02d311c74c
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2018
ms.locfileid: "51514332"
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>Tevékenységnaplók megtekintése az erőforrásokon végzett műveletek naplózásához

A tevékenységnaplókból a következők állapíthatók meg:

* milyen műveletek vették erőforrásokra az előfizetésben
* a művelet kezdeményezője (bár a háttérszolgáltatás által kezdeményezett műveletek nem adott vissza a hívónak felhasználók)
* A művelet végrehajtásának időpontja
* A műveletnek az állapota
* Egyéb tulajdonságokat, amelyek segíthetnek vizsgálja meg a műveletet

A tevékenységnapló tartalmazza az erőforrásokon végrehajtott összes írási műveletet (PUT, POST, DELETE). Olvasási műveleteket (GET) nem tartalmaz. Erőforrás-műveletek listáját lásd: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md). A vizsgálati naplók keresse meg a hiba elhárításához vagy egy a szervezet felhasználói hogyan módosították az erőforrásokat figyelésére használható.

A tevékenységnaplók 90 napon keresztül érhetők el. Bármilyen dátumtartományt lekérdezhet, amíg a kezdő dátum legfeljebb 90 nappal korábbra esik.

Információ a vizsgálati naplók a portálon keresztül, PowerShell, az Azure CLI-vel, az Insights REST API-t lehet lekérdezni vagy [Insights .NET-kódtár](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="portal"></a>Portál

1. A portálon keresztül a vizsgálati naplók megtekintéséhez jelölje ki **figyelő**.

    ![Válassza ki a tevékenységeket tartalmazó naplók](./media/resource-group-audit/select-monitor.png)

   Vagy, a tevékenységnapló egy adott erőforrás vagy erőforráscsoport automatikusan szűréséhez válasszon **tevékenységnapló**. Figyelje meg, hogy a tevékenységnapló automatikusan szűri a kijelölt erőforráshoz.

    ![Szűrés erőforráscsoport alapján](./media/resource-group-audit/filtered-by-resource.png)
2. Az a **tevékenységnapló**, a legutóbbi műveletek összegzése látható.

    ![műveletek megjelenítése](./media/resource-group-audit/audit-summary.png)
3. A megjelenített műveletek számának korlátozására, különböző feltételek kiválasztása. Az alábbi képen például a **Timespan** és **esemény kezdeményezője** mezők módosítani egy adott felhasználó vagy alkalmazás számára az elmúlt hónapban által végrehajtott műveletek megtekintéséhez. Válassza ki **alkalmaz** a lekérdezési eredmények megtekintéséhez.

    ![szűrő beállításainak megadása](./media/resource-group-audit/set-filter.png)

4. Ha szeretné újra futtatni a lekérdezést, válassza ki a **mentése** , és adja meg a lekérdezés nevét.

    ![Lekérdezés mentése](./media/resource-group-audit/save-query.png)
5. Gyorsan futtatni a lekérdezést, kiválaszthatja az egyik beépített lekérdezésre, például a sikertelen üzembe helyezés.

    ![lekérdezés kiválasztása](./media/resource-group-audit/select-quick-query.png)

   A kijelölt lekérdezés automatikusan beállítja a szűrő szükséges értékeket.

    ![üzembe helyezési hibák megtekintése](./media/resource-group-audit/view-failed-deployment.png)

6. Válasszon ki egy megtekintheti az esemény összegzése a műveletek.

    ![a művelet megtekintése](./media/resource-group-audit/view-operation.png)  

## <a name="powershell"></a>PowerShell

1. Naplóbejegyzés lekéréséhez futtassa a **Get-AzureRmLog** parancsot. Azt adja meg a további paramétereket bejegyzések listájának szűrése. Ha nem ad meg egy kezdő és záró idő, a rendszer az elmúlt egy óra bejegyzések adja vissza. Ha például az elmúlt egy órában futtatása során a műveletek esetében egy erőforráscsoport lekéréséhez:

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup
  ```

    Az alábbi példa bemutatja, hogyan használhatja egy adott időszakban végrehajtott kutatási műveletek a tevékenységnaplóban. A kezdő és záró dátuma meg van adva a dátumformátum.

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00
  ```

    Másik lehetőségként dátumfüggvények segítségével megadhatja a dátumtartományt, például az elmúlt 14 napban.

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

2. A megadott kezdési időpont, attól függően az előző parancsokkal az erőforráscsoporthoz tartozó műveletek számos régóta adhat vissza. Az eredmények mit keresett keresési feltételek megadásával szűrheti. Például ha kutatási hogyan webalkalmazás le lett állítva, futtathatja a következő parancsot:

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
  ```

    Amely ebben a példában látható, hogy egy leállítási művelet által végrehajtott someone@contoso.com.

  ```powershell
  Authorization     :
  Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Action    : Microsoft.Web/sites/stop/action
  Role      : Subscription Admin
  Condition :
  Caller            : someone@contoso.com
  CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
  EventSource       : Administrative
  EventTimestamp    : 8/28/2015 4:08:18 PM
  OperationName     : Microsoft.Web/sites/stop/action
  ResourceGroupName : ExampleGroup
  ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Status            : Succeeded
  SubscriptionId    : xxxxx
  SubStatus         : OK
  ```

3. Egy adott, még egy erőforráscsoportot, amely már nem létezik a felhasználó által végrehajtott műveletek kereshet.

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

4. A sikertelen műveleteket végezhet.

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed
  ```

5. Egy hiba történt az állapotüzenet tartalmazza az adott bejegyzés megnézzük összpontosíthat.

  ```azurepowershell-interactive
  ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
  ```

    Amely értéket ad vissza:

        code           message
        ----           -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP.

## <a name="azure-cli"></a>Azure CLI

Naplóbejegyzés lekéréséhez futtassa a [az monitor tevékenységnapló lista](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) parancsot.

  ```azurecli
  az monitor activity-log list --resource-group <group name>
  ```

## <a name="rest-api"></a>REST API

A tevékenységnapló használata REST-műveleteinek részét képezik a [Insights REST API-JÁNAK](https://msdn.microsoft.com/library/azure/dn931943.aspx). Tevékenységnapló-események lekéréséhez lásd: [egy adott előfizetés felügyeleti eseményeinek listázása](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## <a name="next-steps"></a>További lépések

* Azure Tevékenységnaplók segítségével a Power bi-JAL nagyobb információkhoz juthat a műveletek az előfizetésében. Lásd: [megtekintése és elemzése a Power BI és egyéb Azure-tevékenységnaplóinak](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Biztonsági szabályzatok beállítása kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md).
* Üzembehelyezési műveletek megtekintése a parancsokkal kapcsolatos tudnivalókért lásd: [üzembehelyezési műveletek megtekintése](resource-manager-deployment-operations.md).
* Egy erőforrás az összes felhasználó számára a törlések megelőzése érdekében, lásd: [zárolhat erőforrásokat az Azure Resource Manager](resource-group-lock-resources.md).
* Minden Microsoft Azure erőforrás-kezelő szolgáltató számára elérhető műveletek listájának megtekintéséhez lásd: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md)
