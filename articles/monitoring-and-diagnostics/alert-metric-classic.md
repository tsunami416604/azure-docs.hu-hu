---
title: Létrehozása, megtekintése és kezelése a klasszikus metrikariasztásokat az Azure Monitor használatával
description: Ismerje meg, hogyan használható az Azure Portalon, a parancssori felület vagy Powershell létrehozása, megtekintése és klasszikus metrikaalapú riasztási szabályok kezelése.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: e325335d43ef31c44ac812aca66309132f5372a3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951610"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Létrehozása, megtekintése és használata az Azure Monitor metrika-riasztások kezelése

Az Azure monitorban klasszikus metrikariasztásokat hardvermódosításainak értesítést kaphat, amikor egy, a mérőszámok átlépnek egy küszöbértéket. Klasszikus metrikariasztásokat egy régebbi funkció, amely lehetővé teszi, hogy csak a nem többdimenziós metrikák riasztás. Egy meglévő újabb szolgáltatásra nevű metrikákhoz kapcsolódó riasztások továbbfejlesztett funkcióval rendelkezik a klasszikus metrikariasztásokat felett van. Az új funkcióiról metrikákhoz kapcsolódó riasztások többet is megtudhat [metrika riasztások – áttekintés](alert-metric-overview.md). Ebben a cikkben azt azt ismerteti, hogyan hozhat létre, megtekintése és kezelése a klasszikus metrikaalapú riasztási szabályok az Azure Portalon az Azure CLI és PowerShell használatával.

## <a name="create-a-classic-metric-alert-rule-using-azure-portal"></a>Az Azure Portalon klasszikus a metrikaalapú riasztási szabály létrehozása

1. Az a [portál](https://portal.azure.com/), keresse meg a figyelni kívánt a erőforrást, és válassza ki azt.

2. Az a **figyelés** szakaszban jelölje be **riasztások (klasszikus)**. A szöveg és ikon kis mértékben eltérő lehet a különböző erőforrásokat. Ha nem találja a **riasztások (klasszikus)** Itt láthatja a **riasztások** vagy **riasztási szabályok**.

    ![Figyelés](./media/alerts-metric-classic/AlertRulesButton.png)

3. Válassza ki a **metrikariasztás hozzáadása (klasszikus)** parancsot, és töltse ki a mezőket.

    ![Riasztás beállítása](./media/alerts-metric-classic/AddAlertOnlyParamsPage.png)

4. **Név** a riasztási szabályt. Majd válassza ki a **leírás**, amely értesítő e-mailek is megjelenik.

5. Válassza ki a **metrika** , amelyet figyelni szeretne. Majd válassza ki a **feltétel** és **küszöbérték** a mérőszám értéke. Is kiválaszthatják a **időszak** , ameddig a metrikaszabály riasztási triggerek előtt teljesülniük kell. Például ha az időszak "keresztül az elmúlt 5 percben" használja, és a riasztás egy CPU 80 % fölötti keres, a riasztást aktivál, ha a CPU volt következetesen fenti 80 %-os 5 perc. Miután az első eseményindító történik, újra aktivál, ha 5 percig 80 % alatt marad a Processzor. A Processzor metrikamérési percenként történik.

6. Válassza ki **E-mail-tulajdonosok...**  Ha azt szeretné, hogy e-mailben értesítést kapni, amikor a riasztás akkor aktiválódik, a rendszergazdák és a társadminisztrátorok.

7. Értesítések küldése további e-mail-címeket, a riasztás akkor aktiválódik, ha szeretné, ha hozzá őket a **további rendszergazdai email(s)** mező. Pontosvesszővel több e-mailek, a következő formátumban:  *email@contoso.com; email2@contoso.com*

8. Érvényes URI-t a helyezni a **Webhook** mezőben, ha azt szeretné, ha a riasztás akkor aktiválódik, která bude volána.

9. Azure Automation használatakor választhatja futtatja, a riasztás akkor aktiválódik, amikor egy runbookot.

10. Válassza ki **OK** a riasztás létrehozásához.

Néhány percen belül a riasztás aktív, és elindítja a fent leírtaknak megfelelően.

## <a name="manage-your-classic-metric-alert-rules-using-azure-portal"></a>Az Azure Portalon klasszikus metrikaalapú riasztási szabályok kezelése

Miután létrehozta a riasztást, válassza ki, és hajtsa végre a következő feladatok közül:

* Tekintse meg a metrika küszöbértéke és a tényleges értékek az előző nap megjelenítő grafikon.
* Szerkesztheti, és törölje azt.
* **Tiltsa le** vagy **engedélyezése** , ha azt szeretné, ideiglenesen leállíthatja, vagy folytathatja a riasztás-mailjeire.

## <a name="creating-and-managing-classic-metric-alert-rule-using-azure-cli"></a>Létrehozásába és kezelésébe az Azure CLI-vel klasszikus a metrikaalapú riasztási szabály

A fentebbi szakaszokban leírt létrehozása, megtekintése és kezelése a metrikaalapú riasztási szabályok az Azure portal használatával. Ez a szakasz azt ismerteti, hogyan végezze el ugyanezt a platformok közötti átjárhatóságról használatával való [Azure CLI-vel](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli?view=azure-cli-latest). Leggyorsabb módja, hogy megkezdhesse az Azure CLI-vel van keresztül [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview?view=azure-cli-latest).

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Egy erőforráscsoportba tartozó összes klasszikus metrikaalapú riasztási szabályok beolvasása

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Tekintse meg az adott klasszikus a metrikaalapú riasztási szabály részletei

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Klasszikus a metrikaalapú riasztási szabály létrehozása

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Klasszikus a metrikaalapú riasztási szabály törlése

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

### <a name="create-view-and-manage-classic-metric-alerts-using-powershell"></a>Létrehozása, megtekintése és kezelése a klasszikus metrikariasztásokat PowerShell-lel

Ez a szakasz azt mutatja be, hogyan használható a PowerShell-parancsok létrehozása, megtekintése és kezelése a klasszikus metrikariasztásokat. A cikkben szereplő példák bemutatják, hogyan használhatja az Azure Monitor-parancsmagok a klasszikus metrikariasztásokat.

1. Ha még nem tette, PowerShell beállítása a futást a számítógépen. További információkért lásd: [telepítése és konfigurálása PowerShell](/powershell/azure/overview). Emellett áttekintheti a teljes listáját, az Azure Monitor PowerShell-parancsmagok [Azure Monitor (elemzés) parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.insights).

2. Első lépésként jelentkezzen be az Azure-előfizetéshez.

    ```PowerShell
    Connect-AzureRmAccount
    ```

3. Megjelenik egy bejelentkezési képernyő. Egyszer, jelentkezzen be a fiók a bérlő azonosítója, és alapértelmezett előfizetés-azonosító jelennek meg. Az összes Azure-parancsmagjaival működik az alapértelmezett előfizetést kontextusában. Hozzáfér az előfizetések listájának megtekintéséhez használja a következő parancsot:

    ```PowerShell
    Get-AzureRmSubscription
    ```

4. A működő környezet másik előfizetésbe való módosításához használja a következő parancsot:

    ```PowerShell
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

5. Az összes klasszikus metrikaalapú riasztási szabályok az erőforráscsoport kérheti le:

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup montest
    ```

6. Megtekintheti a klasszikus a metrikaalapú riasztási szabály részletei

    ```PowerShell
    Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. Állítsa be a célerőforrás összes riasztási szabályt kérheti le. Például állítsa be a riasztási szabályok az összes virtuális gépen.

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig

8. You can use the `Add-AlertRule` cmdlet to create, update, or disable an alert rule. You can create email and webhook properties using  `New-AzureRmAlertRuleEmail` and `New-AzureRmAlertRuleWebhook`, respectively. In the Alert rule cmdlet, assign these properties as actions to the **Actions** property of the Alert Rule. The following table describes the parameters and values used to create an alert using a metric.

    | parameter | value |
    | --- | --- |
    | Name |simpletestdiskwrite |
    | Location of this alert rule |East US |
    | ResourceGroup |montest |
    | TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
    | MetricName of the alert that is created |\PhysicalDisk(_Total)\Disk Writes/sec. See the `Get-MetricDefinitions` cmdlet about how to retrieve the exact metric names |
    | operator |GreaterThan |
    | Threshold value (count/sec in for this metric) |1 |
    | WindowSize (hh:mm:ss format) |00:05:00 |
    | aggregator (statistic of the metric, which uses Average count, in this case) |Average |
    | custom emails (string array) |'foo@example.com','bar@example.com' |
    | send email to owners, contributors and readers |-SendToServiceOwners |

9. Create an Email action

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    ```

10. Hozzon létre egy Webhook művelettel

    ```PowerShell
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
    ```

## <a name="next-steps"></a>További lépések

- [Klasszikus metrikariasztás létrehozása Resource Manager-sablonnal](monitoring-enable-alerts-using-template.md).
- [Rendelkezik egy webhook használatával nem Azure-beli system értesítése klasszikus metrikariasztás](insights-webhooks-alerts.md).

