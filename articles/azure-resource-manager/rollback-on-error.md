---
title: Visszalépési hiba az Azure üzembe helyezésének sikerességéhez
description: Azt határozza meg, hogy egy sikertelen központi telepítés visszaálljon-e egy sikeres üzembe helyezésre.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: tomfitz
ms.openlocfilehash: 035b684bb9f5fbc10eb13e642c3fd5945b85c561
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71975445"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Hiba visszaállítása a sikeres központi telepítéshez

Ha egy telepítés meghiúsul, automatikusan újratelepítheti a korábbi, sikeres telepítést az üzembe helyezési előzményekből. Ez a funkció akkor hasznos, ha az infrastruktúra központi telepítésének ismert jó állapota van, és ezt az állapotot szeretné visszaállítani. Számos figyelmeztetés és korlátozás létezik:

- Az újratelepítést a rendszer pontosan úgy futtatja, ahogy korábban ugyanazzal a paraméterekkel futtatta. A paraméterek nem módosíthatók.
- Az előző központi telepítés a [teljes móddal](./deployment-modes.md#complete-mode)fut. A rendszer törli az előző üzemelő példányban nem szereplő erőforrásokat, és minden erőforrás-konfiguráció a korábbi állapotukra van beállítva. Győződjön meg arról, hogy teljes mértékben megértette az [üzembe helyezési módokat](./deployment-modes.md).
- Az újratelepítés csak az erőforrásokat befolyásolja, az adatváltozások nincsenek hatással.
- Ezt a funkciót csak az erőforráscsoport-telepítésekhez használhatja, az előfizetés és a felügyeleti csoport szintjén üzemelő példányok nem. Az előfizetési szintű telepítéssel kapcsolatos további információkért lásd: [erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](./deploy-to-subscription.md).
- Ezt a lehetőséget csak gyökérszintű központi telepítések esetén használhatja. A beágyazott sablonból történő központi telepítések nem érhetők el az újratelepítéshez.

Ha ezt a beállítást szeretné használni, a központi telepítéseknek egyedi névvel kell rendelkezniük, hogy az előzményekben azonosíthatók legyenek. Ha nem rendelkezik egyedi névvel, akkor az aktuális sikertelen telepítés felülírhatja a korábban sikeres telepítést az előzményekben.

## <a name="powershell"></a>PowerShell

Az utolsó sikeres központi telepítés újbóli üzembe helyezéséhez adja hozzá a `-RollbackToLastDeployment` paramétert jelzőként.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Egy adott központi telepítés újbóli üzembe helyezéséhez használja a `-RollBackDeploymentName` paramétert, és adja meg a központi telepítés nevét. A megadott központi telepítésnek sikeresnek kell lennie.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Azure CLI

Az utolsó sikeres központi telepítés újbóli üzembe helyezéséhez adja hozzá a `--rollback-on-error` paramétert jelzőként.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Egy adott központi telepítés újbóli üzembe helyezéséhez használja a `--rollback-on-error` paramétert, és adja meg a központi telepítés nevét. A megadott központi telepítésnek sikeresnek kell lennie.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>REST API

Az utolsó sikeres központi telepítés újbóli üzembe helyezéséhez, ha az aktuális telepítés meghiúsul, használja a következőt:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Ha az aktuális telepítés meghiúsulása esetén a megadott központi telepítés újratelepítése sikertelen, használja a következőt:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

A megadott központi telepítésnek sikeresnek kell lennie.

## <a name="next-steps"></a>További lépések

- A szolgáltatás több régióba való biztonságos kivonásához lásd: [Azure Telepítéskezelő](deployment-manager-overview.md).
- Ha meg szeretné adni, hogyan kezelje az erőforráscsoport meglévő erőforrásait, de a sablonban nincs definiálva, tekintse meg a [Azure Resource Manager üzembe helyezési módokat](deployment-modes.md).
- Ha szeretné megtudni, hogyan határozhat meg paramétereket a sablonban, olvassa el [a Azure Resource Manager sablonok struktúrájának és szintaxisának megismerését](resource-group-authoring-templates.md)ismertető témakört.
- A SAS-tokent igénylő sablonok telepítésével kapcsolatos információkért lásd: [privát sablon üzembe helyezése sas-tokenrel](resource-manager-powershell-sas-token.md).
