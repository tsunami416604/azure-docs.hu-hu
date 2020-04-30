---
title: Erőforrások üzembe helyezése az Azure CLI-vel és sablonnal
description: Erőforrások üzembe helyezése az Azure-ban a Azure Resource Manager és az Azure CLI használatával. Az erőforrások egy Resource Manager-sablonban vannak meghatározva.
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 8ee15699a085178add05137be895fe6b660b715b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81685705"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Erőforrások üzembe helyezése ARM-sablonokkal és Azure CLI-vel

Ez a cikk azt ismerteti, hogyan használható az Azure CLI Azure Resource Manager (ARM) sablonokkal az erőforrások Azure-beli üzembe helyezéséhez. Ha nem ismeri az Azure-megoldások üzembe helyezésével és kezelésével kapcsolatos fogalmakat, tekintse meg a [sablonok üzembe helyezésének áttekintése](overview.md)című témakört.

Az üzembe helyezési parancsok az Azure CLI 2.2.0-as verziójában változtak. A cikkben szereplő példákhoz az Azure CLI 2.2.0 vagy újabb verziójára van szükség.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Ha nincs telepítve az Azure CLI, használhatja a [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Központi telepítés hatóköre

Az üzembe helyezést egy erőforráscsoport, egy előfizetés, egy felügyeleti csoport vagy egy bérlő számára is megcélozhatja. A legtöbb esetben az üzembe helyezést egy erőforráscsoporthoz kell megcélozni. A házirendek és a szerepkör-hozzárendelések nagyobb hatókörön való alkalmazásához használja az előfizetést, a felügyeleti csoportot vagy a bérlő központi telepítését. Az előfizetések telepítésekor létrehozhat egy erőforráscsoportot, és erőforrásokat helyezhet üzembe a szolgáltatásban.

A központi telepítés hatókörének függvényében különböző parancsokat kell használnia.

Egy **erőforráscsoporthoz**való üzembe helyezéshez használja [az az Deployment Group Create](/cli/azure/deployment/group?view=azure-cli-latest#az-deployment-group-create):

```azurecli-interactive
az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
```

Egy **előfizetésre**való üzembe helyezéshez használja az [az Deployment sub Create](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create):

```azurecli-interactive
az deployment sub create --location <location> --template-file <path-to-template>
```

Az előfizetési szintű központi telepítésekkel kapcsolatos további információkért lásd: [erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](deploy-to-subscription.md).

Egy **felügyeleti csoportba**való központi telepítéshez használja az [az Deployment mg Create](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create --location <location> --template-file <path-to-template>
```

További információ a felügyeleti csoport szintű központi telepítésekről: [erőforrások létrehozása a felügyeleti csoport szintjén](deploy-to-management-group.md).

Egy **bérlőn**való üzembe helyezéshez használja az [az Deployment bérlő Create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create --location <location> --template-file <path-to-template>
```

A bérlői szintű központi telepítésekkel kapcsolatos további információkért lásd: [erőforrások létrehozása a bérlő szintjén](deploy-to-tenant.md).

A cikkben szereplő példák az erőforráscsoportok központi telepítését használják.

## <a name="deploy-local-template"></a>Helyi sablon üzembe helyezése

Amikor erőforrásokat telepít az Azure-ba, a következőket teheti:

1. Jelentkezzen be az Azure-fiókjába
2. Hozzon létre egy erőforráscsoportot, amely tárolóként szolgál az üzembe helyezett erőforrásokhoz. Az erőforráscsoport neve csak alfanumerikus karaktereket, pontokat, aláhúzásokat, kötőjeleket és zárójeleket tartalmazhat. Legfeljebb 90 karakter hosszú lehet. Nem végződhet ponttal.
3. Üzembe helyezés az erőforráscsoporthoz a létrehozandó erőforrásokat definiáló sablon

A sablon tartalmazhat olyan paramétereket, amelyek lehetővé teszik a központi telepítés testreszabását. Megadhat például egy adott környezetre szabott értékeket (például fejlesztési, tesztelési és éles). A minta sablon egy paramétert határoz meg a Storage-fiók SKU-jának.

A következő példában létrehozunk egy erőforráscsoportot, és üzembe helyezünk egy sablont a helyi gépről:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, megjelenik egy üzenet, amely tartalmazza a következő eredményt:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Távoli sablon üzembe helyezése

Az ARM-sablonok helyi gépen való tárolása helyett érdemes lehet őket külső helyen tárolni. A sablonok a verziókövetés adattárában (például a GitHubon) is tárolhatók. Egy Azure Storage-fiókban is tárolhatja őket a szervezet megosztott hozzáféréséhez.

Külső sablon üzembe helyezéséhez használja a **template-URI** paramétert. A példában szereplő URI használatával telepítse a minta sablont a GitHubról.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

Az előző példa egy nyilvánosan elérhető URI-t igényel a sablonhoz, amely a legtöbb forgatókönyv esetében működik, mert a sablon nem tartalmazhat bizalmas adatokat. Ha bizalmas adatokat (például rendszergazdai jelszót) kell megadnia, adja át ezt az értéket biztonságos paraméterként. Ha azonban nem szeretné, hogy a sablon nyilvánosan elérhető legyen, akkor azt egy privát tárolóban tárolhatja. A közös hozzáférésű aláírási (SAS-) tokent igénylő sablonok telepítésével kapcsolatos információkért lásd: [privát sablon telepítése sas-tokenrel](secure-template-with-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

A Cloud Shell használja a következő parancsokat:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az deployment group create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Paraméterek

A paraméterek értékének átadásához használhat beágyazott paramétereket vagy egy paraméter-fájlt.

### <a name="inline-parameters"></a>Beágyazott paraméterek

A beágyazott paraméterek átadásához adja meg az értékeket `parameters`a következőben:. Ha például egy karakterláncot és tömböt szeretne átadni egy sablonnak egy bash-rendszerhéjra, használja a következőt:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Ha az Azure CLI-t a Windows parancssorral (CMD) vagy a PowerShell-lel használja, adja át a `exampleArray="['value1','value2']"`tömböt a következő formátumban:.

Emellett beolvashatja a fájl tartalmát, és megadhatja a tartalmat beágyazott paraméterként.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Egy paraméter értékének beolvasása a fájlból hasznos lehet, ha konfigurációs értékeket kell megadnia. Megadhat például [egy Linux rendszerű virtuális gép számára a Cloud-init értékeket](../../virtual-machines/linux/using-cloud-init.md).

A arrayContent. JSON formátum:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Paraméter fájljai

Ahelyett, hogy a paramétereket a parancsfájlba beágyazott értékként adja át, előfordulhat, hogy könnyebben használható egy JSON-fájl, amely tartalmazza a paraméter értékeit. A paraméter fájljának helyi fájlnak kell lennie. A külső paraméterek fájljai nem támogatottak az Azure CLI-vel.

További információ a paraméter fájlról: [Resource Manager-paraméter fájljának létrehozása](parameter-files.md).

Helyi paraméterérték átadásához a paranccsal `@` adjon meg egy Storage. Parameters. JSON nevű helyi fájlt.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Kiterjesztett JSON-formátum kezelése

Ha többsoros karakterláncokkal vagy megjegyzésekkel rendelkező sablont szeretne üzembe helyezni az Azure CLI-vel, a 2.3.0 vagy régebbi verziójával, akkor a `--handle-extended-json-format` kapcsolót kell használnia.  Például:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="test-a-template-deployment"></a>Sablon központi telepítésének tesztelése

A sablon és a paraméterek értékének egy erőforrás tényleges üzembe helyezése nélküli teszteléséhez használja [az az Deployment Group validate](/cli/azure/group/deployment)lehetőséget.

```azurecli-interactive
az deployment group validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Ha a rendszer nem észlel hibát, a parancs adatokat ad vissza a tesztelési telepítésről. Fontos megjegyezni, hogy a **hiba** értéke null.

```output
{
  "error": null,
  "properties": {
      ...
```

Ha a rendszer hibát észlel, a parancs hibaüzenetet ad vissza. Ha például helytelen értéket ad meg a Storage-fiók SKU-jának, a a következő hibaüzenetet adja vissza:

```output
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

```output
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

- Ha hibát tapasztal a sikeres üzembe helyezéshez, olvassa el a [hiba visszaállítása a sikeres központi telepítéshez](rollback-on-error.md)című témakört.
- Ha meg szeretné adni, hogyan kezelje az erőforráscsoport meglévő erőforrásait, de a sablonban nincs definiálva, tekintse meg a [Azure Resource Manager üzembe helyezési módokat](deployment-modes.md).
- Ha meg szeretné tudni, hogyan határozhat meg paramétereket a sablonban, tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerése](template-syntax.md)című részt.
- A gyakori telepítési hibák megoldásával kapcsolatos tippekért lásd: [gyakori Azure-telepítési hibák elhárítása Azure Resource Managerokkal](common-deployment-errors.md).
- A SAS-tokent igénylő sablonok telepítésével kapcsolatos információkért lásd: [privát sablon üzembe helyezése sas-tokenrel](secure-template-with-sas-token.md).
- A szolgáltatás több régióba való biztonságos kivonásához lásd: [Azure Telepítéskezelő](deployment-manager-overview.md).
