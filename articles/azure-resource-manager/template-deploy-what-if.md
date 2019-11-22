---
title: Template deployment mi a teendő (előzetes verzió)
description: A Azure Resource Manager-sablon telepítése előtt határozza meg, hogy milyen változások történnek az erőforrásokban.
author: mumian
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jgao
ms.openlocfilehash: 19cb674ca7a2dfefc11c7646b23427c722f6e671
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278303"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Resource Manager-sablonok üzembe helyezése – mi a művelet (előzetes verzió)

A sablon telepítése előtt érdemes megtekinteni a megjelenő módosításokat. A Azure Resource Manager a mi-if művelettel teszi lehetővé, hogy az erőforrások hogyan változnak, ha telepíti a sablont. A mi a teendő, ha a művelet nem módosítja a meglévő erőforrásokat. Ehelyett a megadott sablon központi telepítésekor a módosításokat előre jelezheti.

> [!NOTE]
> A mi-if művelet jelenleg előzetes verzióban érhető el. A használatához regisztrálnia kell [az előzetes](https://aka.ms/armtemplatepreviews)verzióra. Előzetes kiadásként előfordulhat, hogy az eredmények azt mutatják, hogy egy erőforrás akkor változik, ha valójában nem történt változás. Dolgozunk ezen problémák csökkentésén, de segítségre van szükségünk. Kérjük, jelentse ezeket a problémákat [https://aka.ms/whatifissues](https://aka.ms/whatifissues)címen.

A mi legyen a művelet a `New-AzDeploymentWhatIf` PowerShell-paranccsal vagy az [üzembe helyezések – What if](/rest/api/resources/deployments/whatif) Rest művelettel.

A PowerShellben a kimenet a következőképpen néz ki:

![Resource Manager-sablonok üzembe helyezése – mi a teendő, ha a művelet fullresourcepayload és a változás típusa](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

## <a name="change-types"></a>Típusok módosítása

A mi-if művelet hat különböző típusú változást sorol fel:

- **Létrehozás**: az erőforrás jelenleg nem létezik, de definiálva van a sablonban. A rendszer létrehozza az erőforrást.

- **Delete (Törlés**): Ez a módosítási típus csak akkor érvényes, ha az üzembe helyezéshez [teljes üzemmódot](deployment-modes.md) használ. Az erőforrás létezik, de nincs definiálva a sablonban. A teljes módban az erőforrás törölve lesz. Ebben a változási típusban csak a [teljes módú törlést támogató](complete-mode-deletion.md) erőforrások szerepelnek.

- **Figyelmen kívül hagyva**: az erőforrás létezik, de nincs definiálva a sablonban. Az erőforrás nem lesz telepítve vagy módosítva.

- Nincs **változás**: az erőforrás létezik, és a sablonban van definiálva. A rendszer újratelepíti az erőforrást, de az erőforrás tulajdonságai nem változnak. Ezt a változást akkor adja vissza a rendszer, ha a [ResultFormat](#result-format) `FullResourcePayloads`értékre van állítva, amely az alapértelmezett érték.

- **Módosítás**: az erőforrás létezik, és a sablonban van definiálva. Az erőforrás újratelepítése megtörténik, és az erőforrás tulajdonságai megváltoznak. Ezt a változást akkor adja vissza a rendszer, ha a [ResultFormat](#result-format) `FullResourcePayloads`értékre van állítva, amely az alapértelmezett érték.

- **Üzembe helyezés**: az erőforrás létezik, és a sablonban van definiálva. Az erőforrás újratelepítése megtörténik. Előfordulhat, hogy az erőforrás tulajdonságai nem változnak. A művelet ezt a módosítási típust adja vissza, ha nem rendelkezik elegendő információval annak megállapításához, hogy a tulajdonságok módosulnak-e. Ezt az állapotot csak akkor láthatja, ha a [ResultFormat](#result-format) beállítása `ResourceIdOnly`.

## <a name="deployment-scope"></a>Központi telepítés hatóköre

Az előfizetések és az erőforráscsoport szintjén is használhatja az üzembe helyezésekhez szükséges műveleteket. A központi telepítési hatókört a `-ScopeType` paraméterrel állíthatja be. Az elfogadott értékek `Subscription` és `ResourceGroup`. Ez a cikk az erőforráscsoportok központi telepítését mutatja be.

Az előfizetési szintű központi telepítések megismeréséhez lásd: [erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](deploy-to-subscription.md#).

## <a name="result-format"></a>Eredmény formátuma

Megadhatja az előre jelzett változásokkal kapcsolatos részletességi szintet. Állítsa be a `ResultFormat` paramétert `FullResourcePayloads`re, hogy lekérje a megjelenő erőforrások listáját, valamint a módosítandó tulajdonságok részleteit. Állítsa a `ResultFormat` paramétert `ResourceIdOnly`re, hogy lekérje a módosítani kívánt erőforrások listáját. Az alapértelmezett érték `FullResourcePayloads`.  

A következő képernyőképek két különböző kimeneti formátumot mutatnak be:

- Teljes erőforrás-tartalom

    ![Resource Manager-sablon üzembe helyezése – mi a művelet fullresourcepayloads kimenete](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-fullresourcepayload.png)

- Csak erőforrás-azonosító

    ![Resource Manager-sablon üzembe helyezése – mi a művelet resourceidonly kimenete](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-resourceidonly.png)

## <a name="run-what-if-operation"></a>Művelet futtatása

### <a name="set-up-environment"></a>Környezet beállítása

Ha szeretné megtekinteni, hogyan működik a megoldás, hozzon néhány tesztet. Először telepítsen egy sablont az [Azure Gyorsindítás sablonjaiból, amely létrehoz egy Storage-fiókot](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json). A Storage-fiók alapértelmezett típusa `Standard_LRS`. Ezt a Storage-fiókot fogja használni annak tesztelésére, hogy a mi – if hogyan jelentse a módosításokat.

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

### <a name="test-modification"></a>Teszt módosítása

Az üzembe helyezés befejezése után készen áll a mi-if művelet tesztelésére. Futtassa a What-if parancsot, de módosítsa a Storage-fiók típusát `Standard_GRS`ra.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" `
  -storageAccountType Standard_GRS
```

A mi – if kimenet a következőhöz hasonló:

![Resource Manager-sablon üzembe helyezése – mi a művelet kimenete](./media/template-deploy-what-if/resource-manager-deployment-whatif-output.png)

Figyelje meg, hogy a kimenet felső részén a színek határozzák meg a változások típusát.

A kimenet alján látható, hogy az SKU neve (Storage-fiók típusa) **Standard_LRSról** **Standard_GRSra**módosul.

A töröltként felsorolt tulajdonságok némelyike valójában nem változik. Az előző képen ezek a tulajdonságok a accessTier, a titkosítás. a forrás és a szakasz többi része. A tulajdonságokat helytelenül lehet törölni, ha nem a sablonban vannak, de automatikusan be vannak állítva az üzembe helyezés során alapértelmezett értékként. Ez az eredmény "Noise" a mi-if válaszban. A végső központilag telepített erőforrás a tulajdonságoknál beállított értékeket fogja tartalmazni. A mi-if művelet lejáratakor ezek a tulajdonságok kiszűrve lesznek az eredményből.

### <a name="test-deletion"></a>Teszt törlése

A mi-if művelet támogatja a [telepítési mód](deployment-modes.md)használatát. Ha a befejezési módra van állítva, a sablonban nem szereplő erőforrások törlődnek. A következő példa olyan sablont telepít [, amely nem rendelkezik teljes módban definiált erőforrásokkal](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) .

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Mivel nincs megadva erőforrás a sablonban, és a telepítési mód a Befejezés értékre van állítva, a rendszer törli a Storage-fiókot.

![Resource Manager-sablonok üzembe helyezése – mi a teendő, ha a művelet kimenetének üzembe helyezési módja befejeződött](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Fontos megjegyezni, hogy mi a teendő, ha nem végez tényleges módosításokat. A Storage-fiók még létezik az erőforráscsoporthoz.

## <a name="next-steps"></a>Következő lépések

- Ha az előzetes kiadásban helytelen eredményeket észlel, akkor a következő címen tekintheti meg a problémákat: [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- A sablonok Azure PowerShell használatával történő üzembe helyezésével kapcsolatban lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és-Azure PowerShellokkal](resource-group-template-deploy.md).
- A sablonok REST-tel történő üzembe helyezéséhez lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és Resource Manager-Rest APIokkal](resource-group-template-deploy-rest.md).
- Ha hibát tapasztal a sikeres üzembe helyezéshez, olvassa el a [hiba visszaállítása a sikeres központi telepítéshez](rollback-on-error.md)című témakört.
