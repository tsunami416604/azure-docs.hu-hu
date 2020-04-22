---
title: Erőforrások üzembe helyezése az Azure CLI-vel és a sablonnal
description: Az Azure Resource Manager és az Azure CLI segítségével erőforrásokat helyezhet üzembe az Azure-ban. Az erőforrások egy Resource Manager-sablonban vannak meghatározva.
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 8ee15699a085178add05137be895fe6b660b715b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685705"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Erőforrások üzembe helyezése ARM-sablonokkal és Azure CLI-vel

Ez a cikk bemutatja, hogyan használhatja az Azure CLI-t az Azure Resource Manager (ARM) sablonokkal az erőforrások Azure-ba való üzembe helyezéséhez. Ha nem ismeri az Azure-megoldások üzembe helyezésének és kezelésének fogalmait, olvassa el a [sablon üzembe helyezésének áttekintése című témakört.](overview.md)

A központi telepítési parancsok megváltoztak az Azure CLI 2.2.0-s verziójában. Ebben a cikkben szereplő példák az Azure CLI 2.2.0-s vagy újabb verzióját igénylik.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Ha nincs telepítve az Azure CLI, használhatja a [Cloud Shellt.](#deploy-template-from-cloud-shell)

## <a name="deployment-scope"></a>Központi telepítési hatókör

A központi telepítést egy erőforráscsoportra, előfizetésre, felügyeleti csoportra vagy bérlőre célozhatja. A legtöbb esetben egy erőforráscsoportra fogja célozni a központi telepítést. A szabályzatok és a szerepkör-hozzárendelések alkalmazása egy nagyobb hatókörben, használja előfizetés, felügyeleti csoport vagy bérlői központi telepítések. Előfizetésre való üzembe helyezéskor létrehozhat egy erőforráscsoportot, és erőforrásokat helyezhet üzembe.

A központi telepítés hatókörétől függően különböző parancsokat kell használnia.

Ha **erőforráscsoportra**szeretné telepíteni, használja [az üzembe helyezési csoport létrehozását:](/cli/azure/deployment/group?view=azure-cli-latest#az-deployment-group-create)

```azurecli-interactive
az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
```

Ha **előfizetésre**szeretne telepíteni, használja [az az telepítési allétrehozási alcsoportot:](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create)

```azurecli-interactive
az deployment sub create --location <location> --template-file <path-to-template>
```

Az előfizetési szintű központi telepítésekről további információt [az Erőforráscsoportok és -erőforrások létrehozása előfizetési szinten](deploy-to-subscription.md)című témakörben talál.

Ha egy **felügyeleti csoportba**szeretné telepíteni, használja [az üzembe helyezési mg create (Az központi telepítés mg create) használatát:](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create)

```azurecli-interactive
az deployment mg create --location <location> --template-file <path-to-template>
```

A felügyeleticsoport szintű központi telepítésekről további információt [az Erőforrások létrehozása a felügyeleti csoport szintjén című témakörben](deploy-to-management-group.md)talál.

**A bérlőre**való üzembe helyezéshez használja [az üzembe helyezési létrehozási használatát:](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)

```azurecli-interactive
az deployment tenant create --location <location> --template-file <path-to-template>
```

A bérlői szintű központi telepítésekről további információt [az Erőforrások létrehozása bérlői szinten](deploy-to-tenant.md)című témakörben talál.

Ebben a cikkben szereplő példák erőforráscsoport-telepítések használatát használja.

## <a name="deploy-local-template"></a>Helyi sablon telepítése

Amikor erőforrásokat telepít az Azure-ba, a következőket teszi:

1. Jelentkezzen be az Azure-fiókjába
2. Hozzon létre egy erőforráscsoportot, amely az üzembe helyezett erőforrások tárolójaként szolgál. Az erőforráscsoport neve csak alfanumerikus karaktereket, pontokat, aláhúzásjeleket, kötőjeleket és zárójeleket tartalmazhat. Legfeljebb 90 karakter lehet. Nem végződhet egy időszakban.
3. Az erőforráscsoportba való üzembe helyezése a létrehozandó erőforrásokat meghatározó sablon

A sablonok olyan paramétereket tartalmazhatnak, amelyek lehetővé teszik a központi telepítés testreszabását. Például olyan értékeket adhat meg, amelyek egy adott környezethez vannak igazítva (például a fejlesztéshez, a teszteléshez és az éles környezethez). A mintasablon egy paramétert határoz meg a tárfiók termékváltozatához.

A következő példa létrehoz egy erőforráscsoportot, és telepítegy sablont a helyi számítógépről:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, megjelenik egy üzenet, amely tartalmazza az eredményt:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Távoli sablon telepítése

Ahelyett, hogy arm sablonokat tárolna a helyi számítógépen, célszerű lehet azokat egy külső helyen tárolni. A sablonokat egy forrásvezérlő tárházban (például a GitHubon) tárolhatja. Vagy tárolhatja őket egy Azure storage-fiókban a szervezeten belüli megosztott hozzáféréshez.

Külső sablon üzembe helyezéséhez használja a **template-uri** paramétert. Használja az URI-t a példában a mintasablon üzembe helyezéséhez a GitHubról.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

Az előző példa nyilvánosan elérhető URI-t igényel a sablonhoz, amely a legtöbb esetben működik, mert a sablon nem tartalmazhat bizalmas adatokat. Ha bizalmas adatokat (például rendszergazdai jelszót) kell megadnia, adja át ezt az értéket biztonságos paraméterként. Ha azonban nem szeretné, hogy a sablon nyilvánosan elérhető legyen, megvédheti azt egy privát tárolótárolóban való tárolásával. A megosztott hozzáférésű aláírási (SAS) jogkivonatot igénylő sablonok központi telepítéséről a [Privát sablon telepítése SAS-jogkivonattal](secure-template-with-sas-token.md)című témakörben olvashat.

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

A Cloud Shellben használja a következő parancsokat:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az deployment group create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Paraméterek

A paraméterértékek átadására használhat szövegközi paramétereket vagy paraméterfájlt.

### <a name="inline-parameters"></a>Szövegközi paraméterek

A szövegközi paraméterek átadásához adja meg az értékeket a alkalmazásban. `parameters` Ha például egy karakterláncot és egy tömböt egy sablonnak szeretne átadni, használja a bash rendszerhéjat, használja a következőket:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Ha az Azure CLI-t Windows parancssorgal (CMD) vagy PowerShelllel `exampleArray="['value1','value2']"`használja, adja át a tömböt a következő formátumban: .

A fájl tartalmát is beszerezheti, és a tartalmat szövegközi paraméterként is megadhatja.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

A paraméterérték fájlból való beszerzése akkor hasznos, ha konfigurációs értékeket kell megadnia. Például felhőalapú init értékeket adhat meg [egy Linux-alapú virtuális géphez.](../../virtual-machines/linux/using-cloud-init.md)

Az arrayContent.json formátum a következő:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Paraméterfájlok

Ahelyett, hogy a paramétereket szövegközi értékként adná át a parancsfájlban, egyszerűbben használhatja a paraméterértékeket tartalmazó JSON-fájlt. A paraméterfájlnak helyi fájlnak kell lennie. Külső paraméter fájlokat nem támogatott az Azure CLI.

A paraméterfájlról az [Erőforrás-kezelő paraméterfájl létrehozása](parameter-files.md)című témakörben talál további információt.

Helyi paraméterfájl átadására használja `@` a storage.parameters.json nevű helyi fájl megadását.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Hosszabb JSON-formátum lekezelése

Ha többsoros karakterláncokkal vagy megjegyzésekkel rendelkező sablont szeretne telepíteni az Azure CLI 2.3.0-s vagy újabb verziójú használatával, a `--handle-extended-json-format` kapcsolót kell használnia.  Például:

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

Ha a sablont és a paraméterértékeket erőforrások tényleges üzembe helyezése nélkül szeretné tesztelni, használja [az az telepítési csoport ellenőrzése parancsot.](/cli/azure/group/deployment)

```azurecli-interactive
az deployment group validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Ha a rendszer nem észlel hibát, a parancs a teszt központi telepítésével kapcsolatos információkat ad vissza. Különösen figyelje meg, hogy a **hibaérték** null értékű.

```output
{
  "error": null,
  "properties": {
      ...
```

Hiba észlelése esetén a parancs hibaüzenetet ad vissza. Ha például helytelen értéket ad meg a tárfiók Termékváltozatának, a következő hibát adja vissza:

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

Ha a sablon szintaktikai hiba miatt a parancs hibát ad vissza, amely azt jelzi, hogy nem tudta elemezni a sablont. Az üzenet az elemzési hiba sorszámát és pozícióját jelzi.

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

- Ha hiba esetén szeretne visszaáll egy sikeres központi telepítésre, olvassa el [a Hiba visszaállítása a sikeres üzembe helyezéshez.](rollback-on-error.md)
- Ha meg szeretné adni, hogyan kezelje az erőforráscsoportban létező, de a sablonban nem definiált erőforrásokat, olvassa el az Azure Resource Manager telepítési módjai című [témakört.](deployment-modes.md)
- A paraméterek sablonban való definiálásának megértéséhez [olvassa el Az ARM-sablonok szerkezetének és szintaxisának megismerése című témakört.](template-syntax.md)
- A gyakori telepítési hibák elhárításával kapcsolatos tippek az [Azure Resource Manager gyakori Azure-telepítési hibáinak elhárítása című témakörben](common-deployment-errors.md)olvashat.
- A SAS-jogkivonatot igénylő sablonok üzembe helyezéséről a [Privát sablon telepítése SAS-jogkivonattal](secure-template-with-sas-token.md)című témakörben olvashat.
- Ha biztonságosan szeretné kivonni a szolgáltatást egynél több régióra, olvassa el az [Azure Deployment Manager](deployment-manager-overview.md).
