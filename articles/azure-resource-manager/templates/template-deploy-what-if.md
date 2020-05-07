---
title: Template deployment mi a teendő (előzetes verzió)
description: A Azure Resource Manager-sablon telepítése előtt határozza meg, hogy milyen változások történnek az erőforrásokban.
author: mumian
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: jgao
ms.openlocfilehash: 70023f4fa5d44c74c7ce14f3a2c09ff14c9d2f8c
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82581204"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>ARM-sablon üzembe helyezési művelete (előzetes verzió)

Azure Resource Manager (ARM-) sablon üzembe helyezése előtt megtekintheti a megjelenő módosításokat. A Azure Resource Manager a mi-if művelettel teszi lehetővé, hogy az erőforrások hogyan változnak, ha telepíti a sablont. A mi a teendő, ha a művelet nem módosítja a meglévő erőforrásokat. Ehelyett a megadott sablon központi telepítésekor a módosításokat előre jelezheti.

> [!NOTE]
> A mi-if művelet jelenleg előzetes verzióban érhető el. Előzetes kiadásként előfordulhat, hogy az eredmények azt mutatják, hogy egy erőforrás akkor változik, ha valójában nem történt változás. Dolgozunk ezen problémák csökkentésén, de segítségre van szükségünk. Kérjük, jelentse ezeket a [https://aka.ms/whatifissues](https://aka.ms/whatifissues)problémákat a következő címen:.

Az Azure PowerShell, az Azure CLI vagy a REST API műveletekkel használhatja a mi-if műveletet.

## <a name="install-powershell-module"></a>PowerShell-modul telepítése

A mi-ha a PowerShellben való használatához telepítenie kell az az. Resources modul előzetes verzióját a PowerShell-galériából. A modul telepítése előtt azonban győződjön meg róla, hogy a PowerShell Core (6. x vagy 7. x). Ha a PowerShell 5. x vagy korábbi verziója van telepítve, [frissítse a PowerShell-verzióját](/powershell/scripting/install/installing-powershell). Az előzetes verziójú modul nem telepíthető a PowerShell 5. x vagy régebbi verziójára.

### <a name="install-preview-version"></a>Előzetes verzió telepítése

Az előzetes verziójú modul telepítéséhez használja a következőt:

```powershell
Install-Module Az.Resources -RequiredVersion 1.12.1-preview -AllowPrerelease
```

### <a name="uninstall-alpha-version"></a>Alfa verzió eltávolítása

Ha korábban telepítette a mi-if modul alfa-verzióját, távolítsa el a modult. Az Alpha verziója csak olyan felhasználók számára érhető el, akik regisztráltak egy korai előzetes verzióra. Ha nem telepítette az előnézetet, akkor kihagyhatja ezt a szakaszt.

1. A PowerShell futtatása rendszergazdaként
1. Keresse meg az az. Resources modul telepített verzióit.

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. Ha a telepített verziója **2. x. x-Alpha**formátumú verziószámmal rendelkezik, távolítsa el ezt a verziót.

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. Szüntesse meg az előzetes verzió telepítéséhez használt adattárház regisztrációját.

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

Készen áll a mi-if használatára.

## <a name="install-azure-cli-module"></a>Az Azure CLI-modul telepítése

Ha az Azure CLI-ben szeretné használni, akkor az Azure CLI 2.5.0-t vagy annak újabb verzióját kell használnia. Ha szükséges, [telepítse az Azure CLI legújabb verzióját](/cli/azure/install-azure-cli).

## <a name="see-results"></a>Eredmények megtekintése

Ha a PowerShellben vagy az Azure CLI-ben használ, a kimenet színkódolt eredményeket tartalmaz, amelyek segítségével megtekintheti a különböző típusú módosításokat.

![Resource Manager-sablonok üzembe helyezése – mi a teendő, ha a művelet fullresourcepayload és a változás típusa](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

A szöveg kimenete:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

## <a name="what-if-commands"></a>Mi – if parancsok

### <a name="azure-powershell"></a>Azure PowerShell

A módosítások előnézetéhez a sablon telepítése előtt adja hozzá `-Whatif` a Switch paramétert a telepítési parancshoz.

* `New-AzResourceGroupDeployment -Whatif`erőforráscsoport-telepítések esetén
* `New-AzSubscriptionDeployment -Whatif`és `New-AzDeployment -Whatif` előfizetési szintű központi telepítések esetén

A `-Confirm` switch paraméterrel megtekintheti a módosításokat, és a rendszer kéri, hogy folytassa a telepítést.

* `New-AzResourceGroupDeployment -Confirm`erőforráscsoport-telepítések esetén
* `New-AzSubscriptionDeployment -Confirm`és `New-AzDeployment -Confirm` előfizetési szintű központi telepítések esetén

Az előző parancsok olyan szöveges összegzést adnak vissza, amelyet manuálisan lehet megvizsgálni. Ha olyan objektumot szeretne beolvasni, amelyet programozott módon vizsgálhat a változásokhoz, használja a következőt:

* `$results = Get-AzResourceGroupDeploymentWhatIfResult`erőforráscsoport-telepítések esetén
* `$results = Get-AzSubscriptionDeploymentWhatIfResult`vagy `$results = Get-AzDeploymentWhatIfResult` előfizetési szintű központi telepítések esetén

### <a name="azure-cli"></a>Azure CLI

Ha egy sablon telepítése előtt szeretné megtekinteni a `what-if` módosításokat, használja a parancsot a telepítési paranccsal.

* `az deployment group what-if`erőforráscsoport-telepítések esetén
* `az deployment sub what-if`előfizetési szintű központi telepítések esetén

A `--confirm-with-what-if` kapcsolót (vagy annak rövid formáját `-c`) használva megtekintheti a módosításokat, és a rendszer kéri, hogy folytassa a telepítést.

* `az deployment group create --confirm-with-what-if`vagy `-c` erőforráscsoport-telepítések esetén
* `az deployment sub create --confirm-with-what-if`vagy `-c` előfizetési szintű központi telepítések esetén

Az előző parancsok olyan szöveges összegzést adnak vissza, amelyet manuálisan lehet megvizsgálni. Egy olyan JSON-objektum beszerzéséhez, amelyet programozott módon vizsgálhat a változásokhoz, használja a következőt:

* `az deployment group what-if --no-pretty-print`erőforráscsoport-telepítések esetén
* `az deployment sub what-if --no-pretty-print`előfizetési szintű központi telepítések esetén

### <a name="azure-rest-api"></a>Azure REST API

REST API esetén használja a következőt:

* [Központi telepítések – What if](/rest/api/resources/deployments/whatif) az erőforráscsoportok üzembe helyezéséhez
* [Központi telepítések – What if](/rest/api/resources/deployments/whatifatsubscriptionscope) az előfizetések hatókörében az előfizetés szintjén üzemelő példányok esetében

## <a name="change-types"></a>Típusok módosítása

A mi-if művelet hat különböző típusú változást sorol fel:

- **Létrehozás**: az erőforrás jelenleg nem létezik, de definiálva van a sablonban. A rendszer létrehozza az erőforrást.

- **Delete (Törlés**): Ez a módosítási típus csak akkor érvényes, ha az üzembe helyezéshez [teljes üzemmódot](deployment-modes.md) használ. Az erőforrás létezik, de nincs definiálva a sablonban. A teljes módban az erőforrás törölve lesz. Ebben a változási típusban csak a [teljes módú törlést támogató](complete-mode-deletion.md) erőforrások szerepelnek.

- **Figyelmen kívül hagyva**: az erőforrás létezik, de nincs definiálva a sablonban. Az erőforrás nem lesz telepítve vagy módosítva.

- Nincs **változás**: az erőforrás létezik, és a sablonban van definiálva. A rendszer újratelepíti az erőforrást, de az erőforrás tulajdonságai nem változnak. Ezt a változást adja vissza a [ResultFormat](#result-format) beállításakor `FullResourcePayloads`, amely az alapértelmezett érték.

- **Módosítás**: az erőforrás létezik, és a sablonban van definiálva. Az erőforrás újratelepítése megtörténik, és az erőforrás tulajdonságai megváltoznak. Ezt a változást adja vissza a [ResultFormat](#result-format) beállításakor `FullResourcePayloads`, amely az alapértelmezett érték.

- **Üzembe helyezés**: az erőforrás létezik, és a sablonban van definiálva. Az erőforrás újratelepítése megtörténik. Előfordulhat, hogy az erőforrás tulajdonságai nem változnak. A művelet ezt a módosítási típust adja vissza, ha nem rendelkezik elegendő információval annak megállapításához, hogy a tulajdonságok módosulnak-e. Ezt az állapotot csak akkor [ResultFormat](#result-format) láthatja, ha `ResourceIdOnly`a ResultFormat értéke.

## <a name="result-format"></a>Eredmény formátuma

Szabályozhatja az előre jelzett változásokkal kapcsolatos részletességi szintet. Erre két lehetősége van:

* **FullResourcePayloads** – a módosult erőforrások listáját adja vissza, és megjeleníti a módosítani kívánt tulajdonságok részleteit is
* **ResourceIdOnly** – a módosítandó erőforrások listáját adja vissza.

Az alapértelmezett érték a **FullResourcePayloads**.

PowerShell-telepítési parancsok esetén használja a `-WhatIfResultFormat` paramétert. A programozott objektum parancsaiban használja a `ResultFormat` (z) paramétert.

Az Azure CLI esetén használja a `--result-format` paramétert.
 
A következő eredmények a két különböző kimeneti formátumot mutatják be:

- Teljes erőforrás-tartalom

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Csak erőforrás-azonosító

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Művelet futtatása

### <a name="set-up-environment"></a>Környezet beállítása

Ha szeretné megtekinteni, hogyan működik a megoldás, hozzon néhány tesztet. Először telepítsen egy olyan [sablont, amely létrehoz egy virtuális hálózatot](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). Ezzel a virtuális hálózattal tesztelheti, hogyan történik a módosítások jelentése.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>Teszt módosítása

Az üzembe helyezés befejezése után készen áll a mi-if művelet tesztelésére. Ezúttal olyan [sablont telepít, amely megváltoztatja a virtuális hálózatot](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Hiányzik egy eredeti címke, egy alhálózat el lett távolítva, és megváltoztak a címek előtagja.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

A mi, ha a kimenet a következőhöz hasonlóan jelenik meg:

![Resource Manager-sablon üzembe helyezése – mi a művelet kimenete](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

A szöveg kimenete:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Figyelje meg, hogy a kimenet felső részén a színek határozzák meg a változások típusát.

A kimenet alján látható, hogy a címke tulajdonosa törölve lett. A 10.0.0.0/16 értékről 10.0.0.0/15-re módosult a címtartomány. A subnet001 nevű alhálózat törölve lett. Ne feledje, hogy ezeket a módosításokat ténylegesen nem telepítették. Megjelenik a sablon központi telepítése során megjelenő változtatások előnézete.

A töröltként felsorolt tulajdonságok némelyike valójában nem változik. A tulajdonságokat helytelenül lehet törölni, ha nem a sablonban vannak, de automatikusan be vannak állítva az üzembe helyezés során alapértelmezett értékként. Ez az eredmény "Noise" a mi-if válaszban. A végső központilag telepített erőforrás a tulajdonságoknál beállított értékeket fogja tartalmazni. A mi-if művelet lejáratakor ezek a tulajdonságok kiszűrve lesznek az eredményből.

## <a name="programmatically-evaluate-what-if-results"></a>Az eredmények programozott kiértékelése

Most pedig programozott módon kiértékeljük a mi-if eredményeket úgy, hogy a parancsot egy változóra állítja be.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Megtekintheti az egyes változtatások összegzését.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>Törlés megerősítése

A mi-if művelet támogatja a [telepítési mód](deployment-modes.md)használatát. Ha a befejezési módra van állítva, a sablonban nem szereplő erőforrások törlődnek. A következő példa olyan sablont telepít [, amely nem rendelkezik teljes módban definiált erőforrásokkal](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) .

Ha egy sablon telepítése előtt szeretné előtekinteni a módosításokat, használja a Switch paramétert a telepítési paranccsal. Ha a módosítások a várt módon változnak, fogadja el, hogy a központi telepítés befejezését szeretné elvégezni.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

Mivel a sablonban nincs megadva erőforrás, és a telepítési mód a Befejezés értékre van állítva, a rendszer törli a virtuális hálózatot.

![Resource Manager-sablonok üzembe helyezése – mi a teendő, ha a művelet kimenetének üzembe helyezési módja befejeződött](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

A szöveg kimenete:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

Megtekintheti a várt módosításokat, és ellenőrizheti, hogy szeretné-e futtatni a központi telepítést.

## <a name="next-steps"></a>További lépések

- Ha az előzetes kiadásban helytelen eredményeket észlel, akkor jelentse a hibákat a következő helyen: [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- A sablonok Azure PowerShell használatával történő telepítéséhez lásd: [erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell](deploy-powershell.md).
- A sablonok Azure CLI-vel történő üzembe helyezéséhez lásd: [erőforrások üzembe helyezése ARM-sablonokkal és az Azure CLI-vel](deploy-cli.md).
- A sablonok REST-tel történő üzembe helyezéséhez lásd: [erőforrások üzembe helyezése ARM-sablonokkal és Resource Manager-Rest APIokkal](deploy-rest.md).
