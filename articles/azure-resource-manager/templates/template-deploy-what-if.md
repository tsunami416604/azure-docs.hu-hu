---
title: Sablon üzembe helyezésének (előzetes verzió)
description: Az Azure Resource Manager-sablon üzembe helyezése előtt határozza meg, hogy milyen módosítások fognak történni az erőforrásokon.
author: mumian
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jgao
ms.openlocfilehash: b8e94d0b4f364e2873dfc21792a67f11c33483bf
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010188"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>ARM-sablon üzembe helyezési művelete (előzetes verzió)

Az Azure Resource Manager (ARM) sablon üzembe helyezése előtt érdemes megtekintenie a változásokat, amelyek bekövetkeznek. Az Azure Resource Manager biztosítja a "ha" műveletet, amely lehetővé teszi, hogy a sablon üzembe helyezésekor hogyan változnak az erőforrások. A "mi lenne, ha" művelet nem módosítja a meglévő erőforrásokat. Ehelyett előre jelzi a módosításokat, ha a megadott sablon telepítve van.

> [!NOTE]
> A "mi lenne, ha" művelet jelenleg előzetes verzióban érhető el. Előzetes verzióként az eredmények néha azt mutatják, hogy egy erőforrás megváltozik, ha valójában nem történik változás. Azon dolgozunk, hogy csökkentsük ezeket a problémákat, de szükségünk van a segítségére. Kérjük, jelentse [https://aka.ms/whatifissues](https://aka.ms/whatifissues)ezeket a problémákat a.

Használhatja a mi lenne, ha művelet et a PowerShell-parancsokkal vagy a REST API-műveletekkel.

## <a name="install-powershell-module"></a>PowerShell-modul telepítése

A PowerShellben a lehetőség-ha használatához telepítse az Az.Resources modul előzetes verzióját a PowerShell-gyűjteményből.

### <a name="install-preview-version"></a>Az előzetes verzió telepítése

Az előnézeti modul telepítéséhez használja a következőket:

```powershell
Install-Module Az.Resources -RequiredVersion 1.12.1-preview -AllowPrerelease
```

### <a name="uninstall-alpha-version"></a>Alfa-verzió eltávolítása

Ha korábban telepítette a lehetőség-lenne, ha modul alfa verzióját, távolítsa el azt a modult. Az alfa verzió csak azok számára volt elérhető, akik korai előzetes verzióra regisztráltak. Ha nem telepítette az előzetes verziót, kihagyhatja ezt a szakaszt.

1. A PowerShell futtatása rendszergazdaként
1. Ellenőrizze az Az.Resources modul telepített verzióit.

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. Ha van egy telepített verziója, amelynek verziószáma **2.x.x-alpha**formátumú, távolítsa el azt a verziót.

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. Törölje az előnézet telepítéséhez használt "mi lett, ha" tárház regisztrációjának regisztrációjának.

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

Készen állsz, hogy használd a "mi lenne, ha"-t.

## <a name="see-results"></a>Eredmények megtekintése

A PowerShellben a kimenet színkódolt eredményeket tartalmaz, amelyek segítenek a különböző típusú módosítások megtekintésében.

![Az Erőforrás-kezelő sablon üzembe helyezésének mi lenne, ha a művelet fullresourcepayload és a változástípusok](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

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

## <a name="what-if-commands"></a>Mi lenne, ha parancsok

Használhatja az Azure PowerShell vagy az Azure REST API-t a mi lenne, ha művelethez.

### <a name="azure-powershell"></a>Azure PowerShell

A módosítások előnézetének megtekintéséhez a sablon üzembe `-Whatif` helyezése előtt adja hozzá a kapcsolóparamétert a központi telepítési parancshoz.

* `New-AzResourceGroupDeployment -Whatif`erőforráscsoport-telepítésekhez
* `New-AzSubscriptionDeployment -Whatif`és `New-AzDeployment -Whatif` előfizetési szintű telepítések esetén

Vagy használhatja a `-Confirm` kapcsoló paramétert a módosítások előnézetének megtekintéséhez, és a rendszer kéri, hogy folytassa a központi telepítést.

* `New-AzResourceGroupDeployment -Confirm`erőforráscsoport-telepítésekhez
* `New-AzSubscriptionDeployment -Confirm`és `New-AzDeployment -Confirm` előfizetési szintű telepítések esetén

Az előző parancsok egy olyan szövegösszegzést adnak vissza, amelyet manuálisan is megvizsgálhat. Ha olyan objektumot szeretne lekérni, amelyet programozott módon ellenőrizhet a módosítások után, használja a következőket:

* `$results = Get-AzResourceGroupDeploymentWhatIfResult`erőforráscsoport-telepítésekhez
* `$results = Get-AzSubscriptionDeploymentWhatIfResult`vagy `$results = Get-AzDeploymentWhatIfResult` előfizetési szintű telepítésekhez

### <a name="azure-rest-api"></a>Azure REST API

REST API esetén használja a következőket:

* [Központi telepítések – Mi történik, ha](/rest/api/resources/deployments/whatif) az erőforráscsoport-telepítések esetében
* [Központi telepítések – Mi a teendő, ha az előfizetési hatókör előfizetési](/rest/api/resources/deployments/whatifatsubscriptionscope) szintű telepítéseknél

## <a name="change-types"></a>Típusok módosítása

A "mi lenne, ha" művelet hat különböző típusú módosítást sorol fel:

- **Létrehozás**: Az erőforrás jelenleg nem létezik, de a sablonban van definiálva. Létrejön az erőforrás.

- **Törlés**: Ez a módosítási típus csak akkor érvényes, ha [teljes módot](deployment-modes.md) használ a telepítéshez. Az erőforrás létezik, de nincs definiálva a sablonban. Teljes módban az erőforrás törlődik. Ez a módosítástípus csak a [teljes módú törlést támogató](complete-mode-deletion.md) erőforrásokat tartalmazza.

- **Figyelmen kívül hagyás**: Az erőforrás létezik, de nincs definiálva a sablonban. Az erőforrás nem lesz telepítve vagy módosítva.

- **NoChange**: Az erőforrás létezik, és a sablonban van definiálva. Az erőforrás újratelepítésre kerül, de az erőforrás tulajdonságai nem változnak. Ez a módosítástípus akkor jelenik `FullResourcePayloads`meg, ha a [ResultFormat](#result-format) értéke a , amely az alapértelmezett érték.

- **Módosítás**: Az erőforrás létezik, és a sablonban van definiálva. Az erőforrás újratelepítésre kerül, és az erőforrás tulajdonságai megváltoznak. Ez a módosítástípus akkor jelenik `FullResourcePayloads`meg, ha a [ResultFormat](#result-format) értéke a , amely az alapértelmezett érték.

- **Telepítés**: Az erőforrás létezik, és a sablonban van definiálva. Az erőforrás újratelepítése lesz. Előfordulhat, hogy az erőforrás tulajdonságai megváltoznak, vagy nem. A művelet akkor adja vissza ezt a módosítási típust, ha nem rendelkezik elegendő információval annak megállapításához, hogy a tulajdonságok megváltoznak-e. Ez a feltétel csak akkor jelenik meg, ha a [ResultFormat](#result-format) beállítása `ResourceIdOnly`a.

## <a name="result-format"></a>Eredményformátum

Szabályozhatja az előre jelzett változásokról visszaadott részletességi szintet. A központi telepítési`New-Az*Deployment`parancsokban ( ) használja a **-WhatIfResultFormat paramétert.** A programozott objektumparancsokban (`Get-Az*DeploymentWhatIf`) használja a **ResultFormat paramétert.**

Állítsa a formátum **paramétert FullResourcePayloads-re,** hogy megkapja a változásokat megmódosító erőforrások listáját és a tulajdonságok részleteit. Állítsa be a formátum paraméter **ResourceIdOnly** az erőforrások listáját, amely meg fog változni. Az alapértelmezett érték a **FullResourcePayloads**.  

A következő eredmények a két különböző kimeneti formátumot mutatják be:

- Teljes erőforrás-rakományok

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

- Csak erőforrásazonosító

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Teendők futtatása

### <a name="set-up-environment"></a>Környezet beállítása

Ha meg szeretnétudni, hogy működik, futtassunk le néhány tesztet. Először telepítsen egy [olyan sablont, amely virtuális hálózatot hoz létre.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json) Ezt a virtuális hálózatot fogja használni annak tesztelésére, hogy a "mi lenne, ha" a változásokat hogyan jelenti.

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>A vizsgálat módosítása

A központi telepítés befejezése után készen áll a "mi lenne, ha" művelet tesztelésére. Ezúttal telepítsen egy [sablont, amely megváltoztatja a virtuális hálózatot](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Hiányzik az eredeti címkék, az alhálózat eltávolításra került, és a címelőtag megváltozott.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

A "mi lenne, ha" kimenet a következőkhöz hasonlónak tűnik:

![Az Erőforrás-kezelő sablon telepítési , ha művelet kimenete](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

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

Figyelje meg a kimenet tetején, hogy a színek a módosítások típusának jelzésére vannak definiálva.

A kimenet alján azt mutatja, hogy a tulajdonos lett törölve. A címelőtag 10.0.0.0/16-ról 10.0.0.0/15-re változott. Az alhálózat névvel ellátott alhálózata törölve lett. Ne feledje, hogy ezek a módosítások valójában nem voltak telepítve. A sablon telepítésekor a módosítások előnézete jelenik meg.

A töröltként felsorolt tulajdonságok némelyike valójában nem változik. A tulajdonságok helytelenül jelenthetők töröltként, ha nem szerepelnek a sablonban, de a telepítés során automatikusan alapértelmezett értékként vannak beállítva. Ez az eredmény tekinthető "zaj" a mi lenne, ha választ. A végleges üzembe helyezett erőforrás a tulajdonságokhoz beállított értékekkel fog rendelkezni. A "mi lenne, ha" művelet érésével, ezek a tulajdonságok kilesznek szűrve az eredményből.

## <a name="programmatically-evaluate-what-if-results"></a>Programozott módon értékeli a "mi lenne, ha" eredményeket

Most programozottmódon értékeljük ki a "mi lenne, ha" eredményeket a parancs változóra állításával.

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Megtekintheti az egyes változások összegzését.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>Törlés megerősítése

A "mi lenne, ha" művelet támogatja a [telepítési mód használatát.](deployment-modes.md) Ha teljes módba van állítva, a sablonban nem lévő erőforrások törlődnek. A következő példa olyan [sablont telepít, amely nem rendelkezik](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) teljes módban definiált erőforrásokkal.

Ha meg szeretné tekinteni a módosításokat a sablon üzembe helyezése előtt, használja a `-Confirm` switch paramétert a központi telepítési paranccsal. Ha a módosítások a várt módon, ellenőrizze, hogy a központi telepítés befejeződött.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Mivel a sablonban nincsenek erőforrások definiálva, és a központi telepítési mód befejeződik, a virtuális hálózat törlődik.

![Az Erőforrás-kezelő sablon üzembe helyezésének üzembe helyezése befejeződött a művelet kimeneti telepítési módja](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

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

Megjelennek a várt módosítások, és megerősítheti, hogy a központi telepítés t szeretne futni.

## <a name="next-steps"></a>További lépések

- Ha helytelen eredményeket észlel a "mi van ha" [https://aka.ms/whatifissues](https://aka.ms/whatifissues)előzetes kiadásában, kérjük, jelentse a problémákat a rendszerben.
- Sablonok üzembe helyezése az Azure PowerShell használatával, olvassa [el az erőforrások üzembe helyezése ARM-sablonokkal és az Azure PowerShell.](deploy-powershell.md)
- Sablonok üzembe helyezéséhez rest, [lásd: Erőforrások telepítése ARM-sablonokkal és Resource Manager REST API.To](deploy-rest.md)deploy templates with REST, see Deploy resources with ARM templates and Resource Manager REST API.
