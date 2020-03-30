---
title: Klasszikus metrikariasztások létrehozása, megtekintése és kezelése az Azure Monitor használatával
description: Ismerje meg, hogyan hozhat létre, tekinthet meg és kezelhet klasszikus metrikariasztási szabályokat az Azure Portal, a CLI vagy a Powershell használatával.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/18/2018
ms.openlocfilehash: b770b9bd34c8267889db536ec81332de32cb8776
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668315"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Klasszikus metrikariasztások létrehozása, megtekintése és kezelése az Azure Monitor használatával

Klasszikus metrikariasztások az Azure Monitor ban leadott módja annak, hogy értesítést kapjon, ha az egyik metrikák átlépik a küszöbértéket. Klasszikus metrikariasztások egy régebbi funkció, amely lehetővé teszi a riasztást csak a nem dimenziós metrikák. Van egy meglévő újabb funkció, a Metrikariasztások, amely továbbfejlesztett funkciókat a klasszikus metrikariasztások. Az új metrikariasztások funkcióról a [metrikariasztások áttekintésében olvashat bővebben.](../../azure-monitor/platform/alerts-metric-overview.md) Ebben a cikkben bemutatjuk, hogyan hozhat létre, tekinthet meg és kezelhet klasszikus metrikariasztási szabályokat az Azure Portalon, az Azure CLI-n és a Powershellen keresztül.

## <a name="with-azure-portal"></a>Az Azure Portal segítségével

1. A [portálon](https://portal.azure.com/)keresse meg a figyelni kívánt erőforrást, majd jelölje ki.

2. A **MONITORING csoportban** válassza a **Riasztások (Klasszikus)** lehetőséget. A szöveg és az ikon némileg eltérhet a különböző erőforrásoktól. Ha itt nem talál **(Klasszikus) riasztásokat,** előfordulhat, hogy a Riasztások vagy **a Riasztási** **szabályok című részben**találja.

    ![Figyelés](media/alerts-classic-portal/AlertRulesButton.png)

3. Válassza ki a **Metrika hozzáadása riasztás (klasszikus)** parancsot, majd töltse ki a mezőket.

    ![Riasztás hozzáadása](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. **Nevezze el** a riasztási szabályt. Ezután válasszon egy **leírást**, amely az értesítő e-mailekben is megjelenik.

5. Válassza ki a figyelni kívánt **metrikát.** Ezután válasszon egy **feltétel** és **küszöbérték** értéket a metrika. Válassza ki azt az **időtartamot** is, amely alatt a metrikaszabálynak meg kell felelnie a riasztási eseményindítók előtt. Ha például az "Elmúlt 5 perc" időszakot használja, és a riasztás 80% feletti PROCESSZORt keres, a riasztás akkor aktiválódik, ha a PROCESSZOR 5 percig folyamatosan 80% felett van. Az első eseményindító után újra aktiválódik, ha a CPU 5 percig 80% alatt marad. A CPU-metrikus mérés percenként történik.

6. Válassza **az E-mail-tulajdonosok lehetőséget,** ha azt szeretné, hogy a rendszergazdák és a társrendszergazdák e-mailértesítéseket kapjanak, amikor a riasztás aktiválódik.

7. Ha a riasztás kijelzése esetén további e-mail címekre szeretne értesítéseket küldeni, adja hozzá őket a **További rendszergazdai e-mail(ek)** mezőben. Több e-mail elkülönítése pontosvesszővel, a következő formátumban: *e-mail\@contoso.com;email2\@contoso.com*

8. Ha azt szeretné, hogy a rendszer a riasztás bekövetkezténél érvényes URI-t helyezzen el a **Webhook** mezőben.

9. Ha az Azure Automation, kiválaszthatja a runbook futtatásához, amikor a riasztás aktiválódik.

10. A riasztás létrehozásához válassza az **OK gombot.**

Néhány percen belül a riasztás aktív, és a korábban leírt eseményindítók.

A riasztás létrehozása után kijelölheti azt, és elkészítheti az alábbi feladatok egyikét:

* Tekintse meg a grafikont, amely a metrika küszöbértéket és az előző nap tényleges értékeit mutatja.
* Szerkesztheti vagy törölheti.
* **Tiltsa le** vagy **engedélyezze** azt, ha ideiglenesen le szeretné állítani vagy folytatni szeretné a riasztásra vonatkozó értesítések fogadását.

## <a name="with-azure-cli"></a>Az Azure CLI-vel

Az előző szakaszok ismertetik, hogyan hozhat létre, tekintheti meg és kezelheti a metrikariasztási szabályokat az Azure Portal használatával. Ez a szakasz bemutatja, hogyan kell ugyanezt a platformfüggetlen [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)használatával. Az Azure CLI használatának leggyorsabb megkezdésére az Azure Cloud Shell szolgáltatás a [dajka.](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Az összes klasszikus metrikariasztási szabály begyűjtése egy erőforráscsoportban

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Egy adott klasszikus metrikariasztási szabály részleteinek megtekintése

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Klasszikus metrikariasztási szabály létrehozása

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Klasszikus metrikariasztási szabály törlése

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>A PowerShell-lel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a szakasz bemutatja, hogyan használhatja a PowerShell-parancsok létrehozása, megtekintése és kezelése klasszikus metrikariasztások. A példák a cikkben bemutatják, hogyan használhatja az Azure Monitor parancsmagjait a klasszikus metrikariasztások.

1. Ha még nem tette meg, állítsa be a PowerShell t a számítógépen való futtatásra. További információt a [PowerShell telepítése és konfigurálása](/powershell/azure/overview)című témakörben talál. Az Azure Monitor PowerShell-parancsmagok teljes listáját is áttekintheti az [Azure Monitor (Insights) parancsmagjaiban.](https://docs.microsoft.com/powershell/module/az.applicationinsights)

2. Először jelentkezzen be azure-előfizetésbe.

    ```powershell
    Connect-AzAccount
    ```

3. Megjelenik egy bejelentkezési képernyő. Miután bejelentkezett a fiókjába, tenantID és alapértelmezett előfizetési azonosító jelenik meg. Az összes Azure-parancsmag az alapértelmezett előfizetés környezetében működik. Az előfizetések listájának megtekintéséhez használja a következő parancsot:

    ```powershell
    Get-AzSubscription
    ```

4. Ha a munkakörnyezetet másik előfizetésre szeretné módosítani, használja a következő parancsot:

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. Az összes klasszikus metrikariasztási szabályt lekérheti egy erőforráscsoporton:

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. Megtekintheti a klasszikus metrikariasztási szabály részleteit

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. A célerőforráshoz beállított összes riasztási szabályt lekérheti. Például a virtuális gépen beállított összes riasztási szabály.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. Klasszikus riasztási szabályok már nem hozhatók létre a PowerShell-en keresztül. Riasztási szabály létrehozásához az új ["Add-AzMetricAlertRule"](/powershell/module/az.monitor/add-azmetricalertrule) parancsot kell használnia.

## <a name="next-steps"></a>További lépések

- [Klasszikus metrikariasztás létrehozása Erőforrás-kezelő sablonnal.](../../azure-monitor/platform/alerts-enable-template.md)
- [Klasszikus metrikariasztás értesítése egy nem Azure-rendszer egy webhook használatával.](../../azure-monitor/platform/alerts-webhooks.md)
