---
title: Üzembe helyezés az Azure-ban gomb
description: Az Azure Resource Manager-sablonok github-tárházból történő üzembe helyezéséhez gomb használatával.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 88436eac970b252d7b0bc7bccee4131e06e9e0cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77109050"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>A GitHub-tárházból származó sablonok üzembe helyezéséhez egy központi telepítési gomb használata

Ez a cikk ismerteti, hogyan használhatja a Központi telepítés az **Azure-ba** gombot a github-tárházból sablonok üzembe helyezéséhez. A gombot közvetlenül hozzáadhatja a GitHub-tárházban lévő README.md fájlhoz vagy egy olyan weboldalhoz, amely a tárházra hivatkozik.

## <a name="use-common-image"></a>Közös kép használata

Ha hozzá szeretné adni a gombot a weboldalhoz vagy a tárházhoz, használja az alábbi képet:

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

A kép a következőképpen jelenik meg:

![Üzembe helyezés az Azure-ban gomb](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>URL létrehozása sablon telepítéséhez

A sablon URL-címének létrehozásához kezdje a tárházban lévő sablon nyers URL-jével:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Ezután url kódolni. Használhat online kódolót vagy futtathat egy parancsot. A következő PowerShell-példa bemutatja, hogyan URL-cím kódolása egy értéket.

```powershell
[uri]::EscapeDataString($url)
```

A példa URL-címe a következő értékkel rendelkezik, ha URL-t kódolt.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Minden hivatkozás ugyanazzal az alap URL-címmel kezdődik:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Adja hozzá az URL-kódolású sablonhivatkozást az alap URL-cím végéhez.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Megvan a teljes URL-cím a linkhez.

## <a name="create-deploy-to-azure-button"></a>Üzembe helyezés létrehozása az Azure-ban gomb

Végül, tedd a linket és a képet együtt.

Ha hozzá szeretné adni a gombot a Markdown-nal a GitHub-tárházban vagy egy weblapon lévő README.md fájlban, használja a következőt:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

HTML-kód esetén használja a következőket:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A teljes megoldás teszteléséhez válassza a következő gombot:

[![Üzembe helyezés az Azure-ban](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

A portál egy ablaktáblát jelenít meg, amely lehetővé teszi a paraméterértékek egyszerű megadását. A paraméterek előre ki vannak töltve a sablon alapértelmezett értékeivel.

![Üzembe helyezés a portál használatával](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>További lépések

- A sablonokról az [Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése című témakörben olvashat bővebben.](template-syntax.md)
