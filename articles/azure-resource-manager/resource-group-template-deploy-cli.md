---
title: Erőforrások üzembe helyezése az Azure CLI-vel és sablonnal | Microsoft Docs
description: Erőforrások üzembe helyezése az Azure-ban a Azure Resource Manager és az Azure CLI használatával. Az erőforrások egy Resource Manager-sablonban vannak meghatározva.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: tomfitz
ms.openlocfilehash: bd43e919cc0b2bcf1d130c7e616b7da064abcc65
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971030"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure parancssori felületével

Ez a cikk azt ismerteti, hogyan használhatja az Azure CLI-t Resource Manager-sablonokkal az erőforrások Azure-ba történő üzembe helyezéséhez. Ha nem ismeri az Azure-megoldások üzembe helyezésével és kezelésével kapcsolatos fogalmakat, tekintse meg az [Azure Resource Manager áttekintése](resource-group-overview.md)című témakört.  

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Ha nincs telepítve az Azure CLI, használhatja a [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Központi telepítés hatóköre

Az üzembe helyezést egy előfizetésen belül egy Azure-előfizetésre vagy egy erőforráscsoporthoz is megcélozhatja. A legtöbb esetben az üzembe helyezést egy erőforráscsoporthoz kell megcélozni. Az előfizetés központi telepítései használatával házirendek és szerepkör-hozzárendelések alkalmazhatók az előfizetések között. Az előfizetések központi telepítését is használhatja az erőforráscsoport létrehozásához és az erőforrások üzembe helyezéséhez. A központi telepítés hatókörének függvényében különböző parancsokat kell használnia.

Egy **erőforráscsoporthoz**való üzembe helyezéshez használja az [az Group Deployment Create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):

```azurecli
az group deployment create --resource-group <resource-group-name> --template-file <path-to-template>
```

Az előfizetésrevaló központi telepítéshez használja az [az Deployment Create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create):

```azurecli
az deployment create --location <location> --template-file <path-to-template>
```

Jelenleg a felügyeleti csoport központi telepítései csak a REST APIon keresztül támogatottak. Lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és Resource Manager-Rest APIokkal](resource-group-template-deploy-rest.md).

A cikkben szereplő példák az erőforráscsoportok központi telepítését használják. Az előfizetések telepítésével kapcsolatos további információkért lásd: [erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](deploy-to-subscription.md).

## <a name="deploy-local-template"></a>Helyi sablon üzembe helyezése

Amikor erőforrásokat telepít az Azure-ba, a következőket teheti:

1. Jelentkezzen be az Azure-fiókjába
2. Hozzon létre egy erőforráscsoportot, amely tárolóként szolgál az üzembe helyezett erőforrásokhoz. Az erőforráscsoport neve csak alfanumerikus karaktereket, pontokat, aláhúzásokat, kötőjeleket és zárójeleket tartalmazhat. Legfeljebb 90 karakter hosszú lehet. Nem végződhet ponttal.
3. Üzembe helyezés az erőforráscsoporthoz a létrehozandó erőforrásokat definiáló sablon

A sablon tartalmazhat olyan paramétereket, amelyek lehetővé teszik a központi telepítés testreszabását. Megadhat például egy adott környezetre szabott értékeket (például fejlesztési, tesztelési és éles). A minta sablon egy paramétert határoz meg a Storage-fiók SKU-jának. 

A következő példában létrehozunk egy erőforráscsoportot, és üzembe helyezünk egy sablont a helyi gépről:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, megjelenik egy üzenet, amely tartalmazza a következő eredményt:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Távoli sablon üzembe helyezése

A Resource Manager-sablonok helyi gépen való tárolása helyett érdemes lehet őket külső helyen tárolni. A sablonok a verziókövetés adattárában (például a GitHubon) is tárolhatók. Egy Azure Storage-fiókban is tárolhatja őket a szervezet megosztott hozzáféréséhez.

Külső sablon üzembe helyezéséhez használja a **template-URI** paramétert. A példában szereplő URI használatával telepítse a minta sablont a GitHubról.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

Az előző példa egy nyilvánosan elérhető URI-t igényel a sablonhoz, amely a legtöbb forgatókönyv esetében működik, mert a sablon nem tartalmazhat bizalmas adatokat. Ha bizalmas adatokat (például rendszergazdai jelszót) kell megadnia, adja át ezt az értéket biztonságos paraméterként. Ha azonban nem szeretné, hogy a sablon nyilvánosan elérhető legyen, akkor azt egy privát tárolóban tárolhatja. A közös hozzáférésű aláírási (SAS-) tokent igénylő sablonok telepítésével kapcsolatos információkért lásd: [privát sablon telepítése sas](resource-manager-cli-sas-token.md)-tokenrel.

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

A Cloud Shell használja a következő parancsokat:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="redeploy-when-deployment-fails"></a>Újratelepítése, ha a telepítés sikertelen

Ez a szolgáltatás a *hiba miatt visszagörgetés*néven is ismert. Ha egy telepítés meghiúsul, automatikusan újratelepítheti a korábbi, sikeres telepítést az üzembe helyezési előzményekből. Az újratelepítés megadásához használja `--rollback-on-error` a paramétert a telepítési parancsban. Ez a funkció akkor hasznos, ha az infrastruktúra központi telepítésének ismert jó állapota van, és ezt az állapotot szeretné visszaállítani. Számos figyelmeztetés és korlátozás létezik:

- Az újratelepítést a rendszer pontosan úgy futtatja, ahogy korábban ugyanazzal a paraméterekkel futtatta. A paraméterek nem módosíthatók.
- Az előző központi telepítés a [teljes móddal](./deployment-modes.md#complete-mode)fut. A rendszer törli az előző üzemelő példányban nem szereplő erőforrásokat, és minden erőforrás-konfiguráció a korábbi állapotukra van beállítva. Győződjön meg arról, hogy teljes mértékben megértette az [üzembe helyezési módokat](./deployment-modes.md).
- Az újratelepítés csak az erőforrásokat befolyásolja, az adatváltozások nincsenek hatással.
- Ez a funkció csak az erőforráscsoport-telepítések esetében támogatott, az előfizetés szintjén üzemelő példányok nem. Az előfizetési szintű telepítéssel kapcsolatos további információkért lásd: [erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](./deploy-to-subscription.md).

Ha ezt a beállítást szeretné használni, a központi telepítéseknek egyedi névvel kell rendelkezniük, hogy az előzményekben azonosíthatók legyenek. Ha nem rendelkezik egyedi névvel, akkor az aktuális sikertelen telepítés felülírhatja a korábban sikeres telepítést az előzményekben. Ezt a lehetőséget csak gyökérszintű központi telepítések esetén használhatja. A beágyazott sablonból történő központi telepítések nem érhetők el az újratelepítéshez.

Az utolsó sikeres központi telepítés újbóli üzembe helyezéséhez `--rollback-on-error` adja hozzá a paramétert jelzőként.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Egy adott központi telepítés újbóli üzembe helyezéséhez `--rollback-on-error` használja a paramétert, és adja meg a központi telepítés nevét.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

A megadott központi telepítésnek sikeresnek kell lennie.

## <a name="parameters"></a>Paraméterek

A paraméterek értékének átadásához használhat beágyazott paramétereket vagy egy paraméter-fájlt.

### <a name="inline-parameters"></a>Beágyazott paraméterek

A beágyazott paraméterek átadásához adja meg az értékeket `parameters`a következőben:. Ha például egy karakterláncot és tömböt szeretne átadni egy sablonnak egy bash-rendszerhéjra, használja a következőt:

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Ha az Azure CLI-t a Windows parancssorral (CMD) vagy a PowerShell-lel használja, adja át a `exampleArray="['value1','value2']"`tömböt a következő formátumban:.

Emellett beolvashatja a fájl tartalmát, és megadhatja a tartalmat beágyazott paraméterként.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Egy paraméter értékének beolvasása a fájlból hasznos lehet, ha konfigurációs értékeket kell megadnia. Megadhat például [egy Linux rendszerű virtuális gép számára a Cloud-init értékeket](../virtual-machines/linux/using-cloud-init.md).

A arrayContent. JSON formátum:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Paraméter fájljai

Ahelyett, hogy a paramétereket a parancsfájlba beágyazott értékként adja át, előfordulhat, hogy könnyebben használható egy JSON-fájl, amely tartalmazza a paraméter értékeit. A paraméter fájljának helyi fájlnak kell lennie. A külső paraméterek fájljai nem támogatottak az Azure CLI-vel.

További információ a paraméter fájlról: [Resource Manager-paraméter fájljának létrehozása](resource-manager-parameter-files.md).

Helyi paraméterérték átadásához a paranccsal `@` adjon meg egy Storage. Parameters. JSON nevű helyi fájlt.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="test-a-template-deployment"></a>Sablon központi telepítésének tesztelése

A sablon és a paraméterek értékének az erőforrások tényleges üzembe helyezése nélküli teszteléséhez használja [az az Group Deployment validate](/cli/azure/group/deployment#az-group-deployment-validate). 

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Ha a rendszer nem észlel hibát, a parancs adatokat ad vissza a tesztelési telepítésről. Fontos megjegyezni, hogy a **hiba** értéke null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Ha a rendszer hibát észlel, a parancs hibaüzenetet ad vissza. Ha például helytelen értéket ad meg a Storage-fiók SKU-jának, a a következő hibaüzenetet adja vissza:

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Ha a sablon szintaktikai hibát tartalmaz, a parancs egy hibaüzenetet ad vissza, amely azt jelzi, hogy nem tudta elemezni a sablont. Az üzenet a sorszámot és az elemzési hiba pozícióját jelzi.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="next-steps"></a>További lépések

- A cikkben szereplő példák az erőforrásokat az alapértelmezett előfizetésben lévő erőforráscsoporthoz telepítik. Ha másik előfizetést szeretne használni, tekintse meg a [több Azure-előfizetés kezelése](/cli/azure/manage-azure-subscriptions-azure-cli)című témakört.
- Ha meg szeretné adni, hogyan kezelje az erőforráscsoport meglévő erőforrásait, de a sablonban nincs definiálva, tekintse meg a [Azure Resource Manager üzembe helyezési módokat](deployment-modes.md).
- Ha szeretné megtudni, hogyan határozhat meg paramétereket a sablonban, olvassa el [a Azure Resource Manager sablonok struktúrájának és szintaxisának megismerését](resource-group-authoring-templates.md)ismertető témakört.
- A gyakori telepítési hibák megoldásával kapcsolatos tippekért lásd: [gyakori Azure-telepítési hibák elhárítása Azure Resource Managerokkal](resource-manager-common-deployment-errors.md).
- A SAS-tokent igénylő sablonok telepítésével kapcsolatos információkért lásd: [privát sablon üzembe helyezése sas](resource-manager-cli-sas-token.md)-tokenrel.
- A szolgáltatás több régióba való biztonságos kivonásához lásd: [Azure Telepítéskezelő](deployment-manager-overview.md).
