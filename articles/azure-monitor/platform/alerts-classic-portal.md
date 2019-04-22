---
title: Létrehozása, megtekintése és kezelése a klasszikus metrikariasztásokat az Azure Monitor használatával
description: Ismerje meg, hogyan használható az Azure Portalon, a parancssori felület vagy Powershell létrehozása, megtekintése és klasszikus metrikaalapú riasztási szabályok kezelése.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.openlocfilehash: 4a225dbc8e84d65a6ea25f63627599e5bb7d2ced
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785324"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Létrehozása, megtekintése és kezelése a klasszikus metrikariasztásokat az Azure Monitor használatával

Az Azure monitorban klasszikus metrikariasztásokat hardvermódosításainak értesítést kaphat, amikor egy, a mérőszámok átlépnek egy küszöbértéket. Klasszikus metrikariasztásokat egy régebbi funkció, amely lehetővé teszi, hogy csak a nem többdimenziós metrikák riasztás. Egy meglévő újabb szolgáltatásra nevű metrikákhoz kapcsolódó riasztások továbbfejlesztett funkcióval rendelkezik a klasszikus metrikariasztásokat felett van. Az új funkcióiról metrikákhoz kapcsolódó riasztások többet is megtudhat [metrika riasztások – áttekintés](../../azure-monitor/platform/alerts-metric-overview.md). Ebben a cikkben azt azt ismerteti, hogyan hozhat létre, megtekintése és kezelése a klasszikus metrikaalapú riasztási szabályok az Azure Portalon az Azure CLI és PowerShell használatával.

## <a name="with-azure-portal"></a>Az Azure Portalon

1. Az a [portál](https://portal.azure.com/), keresse meg a figyelni kívánt a erőforrást, és válassza ki azt.

2. Az a **figyelés** szakaszban jelölje be **riasztások (klasszikus)**. A szöveg és ikon kis mértékben eltérő lehet a különböző erőforrásokat. Ha nem találja a **riasztások (klasszikus)** Itt láthatja a **riasztások** vagy **riasztási szabályok**.

    ![Figyelés](media/alerts-classic-portal/AlertRulesButton.png)

3. Válassza ki a **metrikariasztás hozzáadása (klasszikus)** parancsot, és töltse ki a mezőket.

    ![Riasztás beállítása](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. **Név** a riasztási szabályt. Majd válassza ki a **leírás**, amely értesítő e-mailek is megjelenik.

5. Válassza ki a **metrika** , amelyet figyelni szeretne. Majd válassza ki a **feltétel** és **küszöbérték** a mérőszám értéke. Is kiválaszthatják a **időszak** , ameddig a metrikaszabály riasztási triggerek előtt teljesülniük kell. Például ha az időszak "keresztül az elmúlt 5 percben" használja, és a riasztás egy CPU 80 % fölötti keres, a riasztást aktivál, ha a CPU volt következetesen fenti 80 %-os 5 perc. Miután az első eseményindító történik, újra aktivál, ha 5 percig 80 % alatt marad a Processzor. A Processzor metrikamérési percenként történik.

6. Válassza ki **E-mail-tulajdonosok...**  Ha azt szeretné, hogy e-mailben értesítést kapni, amikor a riasztás akkor aktiválódik, a rendszergazdák és a társadminisztrátorok.

7. Értesítések küldése további e-mail-címeket, a riasztás akkor aktiválódik, ha szeretné, ha hozzá őket a **további rendszergazdai email(s)** mező. Pontosvesszővel több e-mailek, a következő formátumban: *e-mail\@contoso.com;email2\@contoso.com*

8. Érvényes URI-t a helyezni a **Webhook** mezőben, ha azt szeretné, ha a riasztás akkor aktiválódik, která bude volána.

9. Azure Automation használatakor választhatja futtatja, a riasztás akkor aktiválódik, amikor egy runbookot.

10. Válassza ki **OK** a riasztás létrehozásához.

Néhány percen belül a riasztás aktív, és elindítja a fent leírtaknak megfelelően.

Miután létrehozta a riasztást, válassza ki, és hajtsa végre a következő feladatok közül:

* Tekintse meg a metrika küszöbértéke és a tényleges értékek az előző nap megjelenítő grafikon.
* Szerkesztheti, és törölje azt.
* **Tiltsa le** vagy **engedélyezése** , ha azt szeretné, ideiglenesen leállíthatja, vagy folytathatja a riasztás-mailjeire.

## <a name="with-azure-cli"></a>Az Azure CLI-vel

A fentebbi szakaszokban leírt létrehozása, megtekintése és kezelése a metrikaalapú riasztási szabályok az Azure portal használatával. Ez a szakasz azt ismerteti, hogyan végezze el ugyanezt a platformok közötti átjárhatóságról használatával való [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest). Leggyorsabb módja, hogy megkezdhesse az Azure CLI-vel van keresztül [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).

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

## <a name="with-powershell"></a>A PowerShell-lel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a szakasz azt mutatja be, hogyan használható a PowerShell-parancsok létrehozása, megtekintése és kezelése a klasszikus metrikariasztásokat. A cikkben szereplő példák bemutatják, hogyan használhatja az Azure Monitor-parancsmagok a klasszikus metrikariasztásokat.

1. Ha még nem tette, PowerShell beállítása a futást a számítógépen. További információkért lásd: [telepítése és konfigurálása PowerShell](/powershell/azure/overview). Emellett áttekintheti a teljes listáját, az Azure Monitor PowerShell-parancsmagok [Azure Monitor (elemzés) parancsmagok](https://docs.microsoft.com/powershell/module/az.applicationinsights).

2. Első lépésként jelentkezzen be az Azure-előfizetéshez.

    ```powershell
    Connect-AzAccount
    ```

3. Megjelenik egy bejelentkezési képernyő. Egyszer, jelentkezzen be a fiók a bérlő azonosítója, és alapértelmezett előfizetés-azonosító jelennek meg. Az összes Azure-parancsmagjaival működik az alapértelmezett előfizetést kontextusában. Hozzáfér az előfizetések listájának megtekintéséhez használja a következő parancsot:

    ```powershell
    Get-AzSubscription
    ```

4. A működő környezet másik előfizetésbe való módosításához használja a következő parancsot:

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. Az összes klasszikus metrikaalapú riasztási szabályok az erőforráscsoport kérheti le:

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. Megtekintheti a klasszikus a metrikaalapú riasztási szabály részletei

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. Állítsa be a célerőforrás összes riasztási szabályt kérheti le. Például állítsa be a riasztási szabályok az összes virtuális gépen.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. Klasszikus riasztási szabályok a Powershellen keresztül többé nem hozható létre. Szeretné használni az Új riasztási szabály létrehozása ["Add-AzMetricAlertRule"](/powershell/module/az.monitor/add-azmetricalertrule) parancsot.

## <a name="next-steps"></a>További lépések

- [Klasszikus metrikariasztás létrehozása Resource Manager-sablonnal](../../azure-monitor/platform/alerts-enable-template.md).
- [Rendelkezik egy webhook használatával nem Azure-beli system értesítése klasszikus metrikariasztás](../../azure-monitor/platform/alerts-webhooks.md).
