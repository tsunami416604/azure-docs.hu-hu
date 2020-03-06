---
title: Template deployment mi a teendő (előzetes verzió)
description: A Azure Resource Manager-sablon telepítése előtt határozza meg, hogy milyen változások történnek az erőforrásokban.
author: mumian
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: jgao
ms.openlocfilehash: b9d4150779842614a5dc284a2b3a489593fabfe1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388522"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Resource Manager-sablonok üzembe helyezése – mi a művelet (előzetes verzió)

A sablon telepítése előtt érdemes megtekinteni a megjelenő módosításokat. A Azure Resource Manager a mi-if művelettel teszi lehetővé, hogy az erőforrások hogyan változnak, ha telepíti a sablont. A mi a teendő, ha a művelet nem módosítja a meglévő erőforrásokat. Ehelyett a megadott sablon központi telepítésekor a módosításokat előre jelezheti.

> [!NOTE]
> A mi-if művelet jelenleg előzetes verzióban érhető el. A használatához regisztrálnia kell [az előzetes](https://aka.ms/armtemplatepreviews)verzióra. Előzetes kiadásként előfordulhat, hogy az eredmények azt mutatják, hogy egy erőforrás akkor változik, ha valójában nem történt változás. Dolgozunk ezen problémák csökkentésén, de segítségre van szükségünk. Kérjük, jelentse ezeket a problémákat [https://aka.ms/whatifissues](https://aka.ms/whatifissues)címen.

A mi legyen a művelet a PowerShell-parancsokkal vagy a REST API műveletekkel.

A PowerShellben a kimenet színkódolt eredményeket tartalmaz, amelyek segítségével megtekintheti a különböző típusú módosításokat.

![Resource Manager-sablonok üzembe helyezése – mi a teendő, ha a művelet fullresourcepayload és a változás típusa](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

A szöveg ouptput:

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

A mi-if művelethez Azure PowerShell vagy Azure REST API is használhatja.

### <a name="azure-powershell"></a>Azure PowerShell

Ha meg szeretné tekinteni a módosítások előnézetét a sablon telepítése előtt, adja hozzá a `-Whatif` switch paramétert a telepítési parancshoz.

* `New-AzResourceGroupDeployment -Whatif` erőforráscsoport-telepítésekhez
* az előfizetési szintű központi telepítések `New-AzSubscriptionDeployment -Whatif` és `New-AzDeployment -Whatif`

Vagy a `-Confirm` switch paraméterrel megtekintheti a módosításokat, és a rendszer kérni fogja, hogy folytassa a telepítést.

* `New-AzResourceGroupDeployment -Confirm` erőforráscsoport-telepítésekhez
* az előfizetési szintű központi telepítések `New-AzSubscriptionDeployment -Confirm` és `New-AzDeployment -Confirm`

Az előző parancsok olyan szöveges összegzést adnak vissza, amelyet manuálisan lehet megvizsgálni. Ha olyan objektumot szeretne beolvasni, amelyet programozott módon vizsgálhat a változásokhoz, használja a következőt:

* `$results = Get-AzResourceGroupDeploymentWhatIf` erőforráscsoport-telepítésekhez
* előfizetési szintű központi telepítések `$results = Get-AzSubscriptionDeploymentWhatIf` vagy `$results = Get-AzDeploymentWhatIf`

> [!NOTE]
> Az 2.0.1-alpha5 verziójának megjelenése előtt a `New-AzDeploymentWhatIf` parancsot használta. Ezt a parancsot a `Get-AzDeploymentWhatIf`, `Get-AzResourceGroupDeploymentWhatIf`és `Get-AzSubscriptionDeploymentWhatIf` parancs váltotta fel. Ha korábban már használta a verziót, frissítenie kell ezt a szintaxist. A `-ScopeType` paraméter el lett távolítva.

### <a name="azure-rest-api"></a>Azure-REST API

REST API esetén használja a következőt:

* [Központi telepítések – What if](/rest/api/resources/deployments/whatif) az erőforráscsoportok üzembe helyezéséhez
* [Központi telepítések – What if](/rest/api/resources/deployments/whatifatsubscriptionscope) az előfizetések hatókörében az előfizetés szintjén üzemelő példányok esetében

## <a name="change-types"></a>Típusok módosítása

A mi-if művelet hat különböző típusú változást sorol fel:

- **Létrehozás**: az erőforrás jelenleg nem létezik, de definiálva van a sablonban. A rendszer létrehozza az erőforrást.

- **Delete (Törlés**): Ez a módosítási típus csak akkor érvényes, ha az üzembe helyezéshez [teljes üzemmódot](deployment-modes.md) használ. Az erőforrás létezik, de nincs definiálva a sablonban. A teljes módban az erőforrás törölve lesz. Ebben a változási típusban csak a [teljes módú törlést támogató](complete-mode-deletion.md) erőforrások szerepelnek.

- **Figyelmen kívül hagyva**: az erőforrás létezik, de nincs definiálva a sablonban. Az erőforrás nem lesz telepítve vagy módosítva.

- Nincs **változás**: az erőforrás létezik, és a sablonban van definiálva. A rendszer újratelepíti az erőforrást, de az erőforrás tulajdonságai nem változnak. Ezt a változást akkor adja vissza a rendszer, ha a [ResultFormat](#result-format) `FullResourcePayloads`értékre van állítva, amely az alapértelmezett érték.

- **Módosítás**: az erőforrás létezik, és a sablonban van definiálva. Az erőforrás újratelepítése megtörténik, és az erőforrás tulajdonságai megváltoznak. Ezt a változást akkor adja vissza a rendszer, ha a [ResultFormat](#result-format) `FullResourcePayloads`értékre van állítva, amely az alapértelmezett érték.

- **Üzembe helyezés**: az erőforrás létezik, és a sablonban van definiálva. Az erőforrás újratelepítése megtörténik. Előfordulhat, hogy az erőforrás tulajdonságai nem változnak. A művelet ezt a módosítási típust adja vissza, ha nem rendelkezik elegendő információval annak megállapításához, hogy a tulajdonságok módosulnak-e. Ezt az állapotot csak akkor láthatja, ha a [ResultFormat](#result-format) beállítása `ResourceIdOnly`.

## <a name="result-format"></a>Eredmény formátuma

Megadhatja az előre jelzett változásokkal kapcsolatos részletességi szintet. A központi telepítési parancsokban (`New-Az*Deployment`) használja a **-WhatIfResultFormat** paramétert. A programozott objektum parancsaiban (`Get-Az*DeploymentWhatIf`) használja a **ResultFormat** paramétert.

Állítsa a Format paramétert a **FullResourcePayloads** értékre, hogy lekérje a módosítani kívánt erőforrások listáját és a módosítani kívánt tulajdonságok részleteit. Állítsa a Format paramétert a **ResourceIdOnly** értékre, hogy lekérje a módosítani kívánt erőforrások listáját. Az alapértelmezett érték a **FullResourcePayloads**.  

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

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>Teszt módosítása

Az üzembe helyezés befejezése után készen áll a mi-if művelet tesztelésére. Ezúttal telepítsen egy [sablont, amely megváltoztatja a virtuális hálózatot](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Hiányzik egy eredeti címke, egy alhálózat el lett távolítva, és a rendszer megváltoztatta a címnek megfelelő előtagot.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

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

A kimenet alján látható, hogy a címke tulajdonosa törölve lett. A 10.0.0.0/16 értékről 10.0.0.0/15-re módosult a címtartomány. A subnet001 nevű alhálózat törölve lett. Ne feledje, hogy a módosítások valójában nem lettek központilag telepítve. Megjelenik a sablon központi telepítése során megjelenő változtatások előnézete.

A töröltként felsorolt tulajdonságok némelyike valójában nem változik. A tulajdonságokat helytelenül lehet törölni, ha nem a sablonban vannak, de automatikusan be vannak állítva az üzembe helyezés során alapértelmezett értékként. Ez az eredmény "Noise" a mi-if válaszban. A végső központilag telepített erőforrás a tulajdonságoknál beállított értékeket fogja tartalmazni. A mi-if művelet lejáratakor ezek a tulajdonságok kiszűrve lesznek az eredményből.

## <a name="programmatically-evaluate-what-if-results"></a>Az eredmények programozott kiértékelése

Most pedig programozott módon kiértékeljük a mi-if eredményeket úgy, hogy a parancsot egy változóra állítja be.

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIf `
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

## <a name="confirm-deletion"></a>Törlés megerősítése

A mi-if művelet támogatja a [telepítési mód](deployment-modes.md)használatát. Ha a befejezési módra van állítva, a sablonban nem szereplő erőforrások törlődnek. A következő példa olyan sablont telepít [, amely nem rendelkezik teljes módban definiált erőforrásokkal](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) .

Ha egy sablon telepítése előtt szeretné előtekinteni a módosításokat, használja a `-Confirm` switch paramétert az üzembehelyezési paranccsal. Ha a módosítások a várt módon történtek, erősítse meg, hogy a központi telepítés befejezésére van szükség.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

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

## <a name="next-steps"></a>Következő lépések

- Ha az előzetes kiadásban helytelen eredményeket észlel, akkor a következő címen tekintheti meg a problémákat: [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- A sablonok Azure PowerShell használatával történő üzembe helyezésével kapcsolatban lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és-Azure PowerShellokkal](deploy-powershell.md).
- A sablonok REST-tel történő üzembe helyezéséhez lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és Resource Manager-Rest APIokkal](deploy-rest.md).
