---
title: Sablon létrehozása – Visual Studio-kód
description: A Resource Manager-sablonokon a Visual Studio Code-dal és az Azure Resource Manager-eszközök bővítményeivel dolgozhat.
author: neilpeterson
ms.date: 03/27/2019
ms.topic: quickstart
ms.author: nepeters
ms.openlocfilehash: 4b1ecbf3a1f6083261e87537e20d52e755b77424
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640912"
---
# <a name="quickstart-create-azure-resource-manager-templates-with-visual-studio-code"></a>Rövid útmutató: Azure Resource Manager-sablonok létrehozása a Visual Studio-kóddal

Az Azure Resource Manager Tools for Visual Studio Code nyelvi támogatást, erőforrás-kódrészleteket és erőforrás-automatikus kiegészítést biztosít. Ezek az eszközök segítenek az Azure Resource Manager-sablonok létrehozásában és érvényesítésében. Ebben a rövid útmutatóban a bővítmény segítségével hozzon létre egy Azure Resource Manager-sablont a semmiből. Ennek során a bővítmények olyan képességeit tapasztalhatja, mint az ARM-sablonkódrészletek, az érvényesítés, a kiegészítések és a paraméterfájlok támogatása.

A rövid útmutató befejezéséhez szüksége van a [Visual Studio-kódra,](https://code.visualstudio.com/)és telepítve van az [Azure Resource Manager eszközbővítmény.](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) Az [Azure CLI-t](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) vagy az [Azure PowerShell-modult](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.7.0) is telepítenie és hitelesítenie kell.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="create-an-arm-template"></a>ARM-sablon létrehozása

Hozzon létre és nyisson meg a Visual Studio Code alkalmazással egy *azuredeploy.json*nevű új fájlt. Írja `arm` be a kódszerkesztőbe, amely elindítja az Azure Resource Manager kódrészleteket egy ARM-sablon állványzatához.

Jelölje `arm!` be, ha egy Azure-erőforráscsoport üzembe helyezéséhez sablonhatókört szeretne létrehozni.

![Az Azure Resource Manager állványzatát ábrázoló kép](./media/quickstart-create-templates-use-visual-studio-code/1.png)

Ez a kódrészlet hozza létre az ARM-sablon alapvető építőelemeit.

![Teljesen állványozott ARM sablont ábrázoló kép](./media/quickstart-create-templates-use-visual-studio-code/2.png)

Figyelje meg, hogy a Visual Studio-kód nyelvi módja *JSON-ról* *Azure Resource Manager-sablonra*változott. A bővítmény tartalmaz egy ARM sablonokra jellemző nyelvi kiszolgálót, amely ARM-sablonspecifikus érvényesítést, teljesítést és egyéb nyelvi szolgáltatásokat biztosít.

![Az Azure Resource Manager t a Visual Studio-kód nyelvi üzemmódjaként megjelenítő kép](./media/quickstart-create-templates-use-visual-studio-code/3.png)

## <a name="add-an-azure-resource"></a>Azure-erőforrás hozzáadása

A bővítmény számos Azure-erőforrás kódrészleteket tartalmaz. Ezek a kódrészletek segítségével egyszerűen hozzáadhat erőforrásokat a sablon központi telepítéséhez.

Helyezze a kurzort a sablon `storage`erőforrásblokkjába, írja be a be, és jelölje ki a *kartároló* kódrészletet. **resources**

![Az ARM sablonhoz hozzáadott erőforrást ábrázoló kép](./media/quickstart-create-templates-use-visual-studio-code/4.png)

Ez a művelet tárolási erőforrást ad hozzá a sablonhoz.

![Egy Azure Storage-erőforrást ábrázoló kép ARM-sablonban](./media/quickstart-create-templates-use-visual-studio-code/5.png)

A **lapkulcs** segítségével lapatot a konfigurálható tulajdonságok a tárfiókban.

![A lapbillentyű tabulátorat mutatja be az erőforrás-konfigurációban való navigáláshoz](./media/quickstart-create-templates-use-visual-studio-code/6.png)

## <a name="completion-and-validation"></a>Befejezés és érvényesítés

A bővítmény egyik leghatékonyabb képessége az Azure-sémákkal való integráció. Az Azure-sémák biztosítják a bővítmény ellenőrzési és erőforrás-érzékeny befejezési képességek. Módosítsuk a tárfiókot, hogy működés közben lásd az érvényesítést és a befejezést. 

Először frissítse a tárfiók-fajtát egy `megaStorage`érvénytelen értékre, például . Figyelje meg, hogy ez `megaStorage` a művelet olyan figyelmeztetést hoz létre, amely nem érvényes értéket jelez.

![Érvénytelen tárolási konfigurációt ábrázoló kép](./media/quickstart-create-templates-use-visual-studio-code/7.png)

A befejezési lehetőségek használatához `megaStorage`távolítsa el a kurzort a dupla `ctrl`  +  `space`idézőjelek közé, és nyomja le a billentyűt. Ez a művelet az érvényes értékek befejezési listáját mutatja be.

![A bővítmény automatikus kitöltését ábrázoló kép](./media/quickstart-create-templates-use-visual-studio-code/8.png)

## <a name="add-template-parameters"></a>Sablonparaméterek hozzáadása

Most hozzon létre és használjon egy paramétert a tárfiók nevének megadásához.

Helyezze a kurzort a paraméterek blokk, adjunk `par`hozzá egy `arm-param-value` kocsi vissza, írja be , majd jelölje ki a kódrészletet. Ez a művelet általános paramétert ad a sablonhoz.

![Az ARM sablonhoz hozzáadott paramétert ábrázoló kép](./media/quickstart-create-templates-use-visual-studio-code/9.png)

Frissítse a paraméter nevét `storageAccountName` és leírását a rendszerre. `Storage Account Name`

![A befejezett paramétert ábrázoló kép egy ARM sablonban](./media/quickstart-create-templates-use-visual-studio-code/10.png)

Az Azure storage-fióknevek hossza legalább 3 karakter, és legfeljebb 24. Adja `minLength` hozzá `maxLength` mindkettőt és a paramétert, és adja meg a megfelelő értékeket.

![Az ARM sablonparaméterhez hozzáadott minLength és maxLength képet mutatja](./media/quickstart-create-templates-use-visual-studio-code/11.png)

Most a tárolási erőforrás, frissítse a name tulajdonságot a paraméter használatához. Ehhez távolítsa el az aktuális nevet. Írjon be egy dupla `[`idézőjeleket és egy nyitó szögletes zárójelet, amely az ARM sablonfüggvények listáját hozza létre. Válassza ki a *paramétereket* a listából. 

![Automatikus kiegészítést megjelenítő kép az ARM sablonerőforrások paramétereinek használatakor](./media/quickstart-create-templates-use-visual-studio-code/12.png)

Ha egyetlen `'` idézőjeleket ír be a kerek zárójelek beírásába, megjelenik a sablonban definiált összes paraméter listája, ebben az esetben a *storageAccountName*. Válassza ki a paramétert.

![Befejezett paramétert megjelenítő kép EGY ARM sablonerőforrásban](./media/quickstart-create-templates-use-visual-studio-code/13.png)

## <a name="create-a-parameter-file"></a>Paraméterfájl létrehozása

Az ARM sablon paraméterfájl lehetővé teszi a környezetspecifikus paraméterértékek tárolását, és ezeket az értékeket csoportként való átvitelére a telepítés időpontjában. Előfordulhat például, hogy rendelkezik egy tesztkörnyezetre jellemző értékekkel rendelkező paraméterfájllal, és egy másik kalkulációval egy éles környezetben.

A kiterjesztés megkönnyíti a paraméterfájl létrehozását a meglévő sablonokból. Ehhez kattintson a jobb gombbal a sablonra `Select/Create Parameter File`a kódszerkesztőben, és válassza a lehetőséget.

![A paraméterfájl ARM-sablonból történő létrehozásának jobb gombbal történő kattintási folyamatát ábrázoló kép](./media/quickstart-create-templates-use-visual-studio-code/14.png)

Válassza `New`  >  `All Parameters` > Válassza ki a paraméterfájl nevét és helyét.

![A név és a fájl mentése párbeszédpanelt megjelenítő kép, amikor paraméterfájlt hoz létre ARM sablonból](./media/quickstart-create-templates-use-visual-studio-code/15.png)

Ez a művelet létrehoz egy új paraméterfájlt, és leképezi azt azzal a sablonnal, amelyből létrehozták. Az aktuális sablon-/paraméterfájl-hozzárendelést a Visual Studio kód állapotsorán láthatja és módosíthatja, amíg a sablon ki van jelölve.

![](./media/quickstart-create-templates-use-visual-studio-code/16.png)

Most, hogy a paraméterfájl le lett képezve a sablonhoz, a bővítmény összevonja a sablont és a paraméterfájlt. Ha ezt az ellenőrzést a gyakorlatban is `storageAccountName` látni szeretné, adjon hozzá egy kétkarakteres értéket a paraméterfájl paraméteréhez, és mentse a fájlt.

![A paraméterfájl-probléma miatt érvénytelenített sablont ábrázoló kép](./media/quickstart-create-templates-use-visual-studio-code/17.png)

Lépjen vissza az ARM sablonra, és figyelje meg, hogy hiba történt, jelezve, hogy az érték nem felel meg a paraméterfeltételeknek.

![Érvényes ARM-sablont ábrázoló kép](./media/quickstart-create-templates-use-visual-studio-code/18.png)

Frissítse az értéket valami megfelelőre, mentse a fájlt, és lépjen vissza a sablonra. Figyelje meg, hogy a paraméter hibája megoldódott.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Nyissa meg az integrált Visual `ctrl`  +  ```` ` ```` Studio Code terminált a billentyűkombináció használatával, és használja az Azure CLI vagy az Azure PowerShell modult a sablon üzembe helyezéséhez.

# <a name="cli"></a>[parancssori felület](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.json --parameters azuredeploy.parameters.json
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.json -TemplateParameterFile ./azuredeploy.parameters.json
```
---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az Azure-erőforrások már nincs szükség, használja az Azure CLI vagy az Azure PowerShell modul a rövid útmutató erőforráscsoport törléséhez.

# <a name="cli"></a>[parancssori felület](#tab/CLI)

```azurecli
az group delete --name arm-vscode
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name arm-vscode
```
---

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kezdő szintű oktatóanyagok](./template-tutorial-create-first-template.md)
