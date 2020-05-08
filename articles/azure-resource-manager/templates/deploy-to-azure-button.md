---
title: Üzembe helyezés az Azure-ban gomb
description: Azure Resource Manager-sablonok GitHub-tárházból való üzembe helyezéséhez használja a gombot.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: e403d095f7419ed13742dcfa8e8407a92ad10be0
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744090"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Sablonok üzembe helyezése a GitHub-tárházból a központi telepítés gomb használatával

Ez a cikk bemutatja, hogyan helyezhetők üzembe sablonok egy GitHub-tárházból a **telepítés az Azure** -ban gomb használatával. A gombot közvetlenül a GitHub-adattárban található README.md-fájlhoz, vagy egy olyan weboldalhoz is hozzáadhatja, amely hivatkozik a tárházra. Ez a metódus csak az erőforráscsoport-szintű telepítést támogatja.

## <a name="use-common-image"></a>Közös rendszerkép használata

A gombnak a weboldalához vagy adattárhoz való hozzáadásához használja az alábbi képet:

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

A rendszerkép a következőképpen jelenik meg:

![Üzembe helyezés az Azure-ban gomb](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>URL-cím létrehozása sablon üzembe helyezéséhez

A sablon URL-címének létrehozásához a tárházban található sablon nyers URL-címével Kezdje a következőket:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Ezt követően az URL-cím kódolja. Használhat online kódolót, vagy futtathat egy parancsot. A következő PowerShell-példa azt szemlélteti, hogyan kódolhat egy értéket az URL-cím.

```powershell
[uri]::EscapeDataString($url)
```

Az URL-cím kódolásakor a példában szereplő URL-cím értéke a következő.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Az egyes hivatkozások ugyanazzal az alap URL-címmel kezdődnek:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Adja hozzá az URL-kódolású sablon hivatkozását az alap URL-cím végéhez.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

A hivatkozás teljes URL-címe.

## <a name="create-deploy-to-azure-button"></a>Üzembe helyezés létrehozása az Azure-ban gomb

Végül helyezze össze a hivatkozást és a képet.

Ha a Markdown-t a GitHub-tárházban vagy egy weblapon szeretné hozzáadni a README.md-fájlhoz, használja a következőt:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

HTML esetén használja a következőt:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A teljes megoldás teszteléséhez válassza a következő gombot:

[![Üzembe helyezés az Azure-ban](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

A portálon egy ablaktábla jelenik meg, amely lehetővé teszi a paraméterek értékének egyszerű megadását. A paraméterek előre ki vannak töltve a sablon alapértelmezett értékeivel.

![A portál használata az üzembe helyezéshez](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>További lépések

- További információ a sablonokról: [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](template-syntax.md).
