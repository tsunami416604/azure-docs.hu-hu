---
title: Hiba visszaállítása a sikeres telepítésre
description: Adja meg, hogy egy sikertelen központi telepítés visszakell-e állnia egy sikeres központi telepítésre.
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 206c794996f58a4c5b6982c551ae50128ed4f5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460143"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Hiba visszaállítása a sikeres telepítésre

Ha egy központi telepítés sikertelen, automatikusan újratelepítheti a korábbi, sikeres üzembe helyezést a központi telepítési előzményekből. Ez a funkció akkor hasznos, ha van egy ismert jó állapotban az infrastruktúra üzembe helyezését, és szeretné, hogy visszatérjen az állapot. Számos kikötés és korlátozás létezik:

- Az újratelepítés pontosan úgy fut, ahogy korábban futtatták ugyanazzal a paraméterrel. A paraméterek nem módosíthatók.
- Az előző központi telepítés a [teljes módban](./deployment-modes.md#complete-mode)fut. Az előző központi telepítésben nem szereplő erőforrások törlődnek, és az erőforrás-konfigurációk a korábbi állapotukra vannak beállítva. Győződjön meg arról, hogy teljes mértékben megértette a [telepítési módokat.](./deployment-modes.md)
- Az újratelepítés csak az erőforrásokat érinti, az adatok módosításait ez nem érinti.
- Ezt a szolgáltatást csak erőforráscsoport-telepítésekkel használhatja, előfizetési vagy felügyeleti csoportszintű telepítésekkel nem. Az előfizetési szintű telepítésről további információt [az Erőforráscsoportok és -erőforrások létrehozása előfizetési szinten](./deploy-to-subscription.md)című témakörben talál.
- Ezt a beállítást csak gyökérszintű központi telepítésekkel használhatja. A beágyazott sablonból származó központi telepítések nem érhetők el az újratelepítéshez.

A beállítás használatához a központi telepítések egyedi nevekkel kell rendelkezniük, hogy az előzményekben azonosíthatók legyenek. Ha nem rendelkezik egyedi nevekkel, az aktuális sikertelen telepítés felülírhatja a korábban sikeres központi telepítést az előzményekben.

## <a name="powershell"></a>PowerShell

Az utolsó sikeres telepítés újratelepítéséhez adja hozzá a `-RollbackToLastDeployment` paramétert jelzőként.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Adott központi telepítés újratelepítéséhez `-RollBackDeploymentName` használja a paramétert, és adja meg a központi telepítés nevét. A megadott központi telepítésnek sikeresnek kell lennie.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Azure CLI

Az utolsó sikeres telepítés újratelepítéséhez adja hozzá a `--rollback-on-error` paramétert jelzőként.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Adott központi telepítés újratelepítéséhez `--rollback-on-error` használja a paramétert, és adja meg a központi telepítés nevét. A megadott központi telepítésnek sikeresnek kell lennie.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>REST API

Ha az aktuális telepítés sikertelen, az utolsó sikeres telepítés újratelepítéséhez használja a következőket:

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

Ha az aktuális telepítés sikertelen, egy adott központi telepítés újratelepítéséhez használja a következőket:

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

- Ha biztonságosan szeretné kivonni a szolgáltatást egynél több régióra, olvassa el az [Azure Deployment Manager](deployment-manager-overview.md).
- Ha meg szeretné adni, hogyan kezelje az erőforráscsoportban létező, de a sablonban nem definiált erőforrásokat, olvassa el az Azure Resource Manager telepítési módjai című [témakört.](deployment-modes.md)
- Ha tudni szeretné, hogyan definiálhatja a paramétereket a sablonban, [olvassa el az Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése című témakört.](template-syntax.md)
- A SAS-jogkivonatot igénylő sablonok üzembe helyezéséről a [Privát sablon telepítése SAS-jogkivonattal](secure-template-with-sas-token.md)című témakörben olvashat.
