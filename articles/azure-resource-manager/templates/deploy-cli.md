---
title: Erőforrások üzembe helyezése az Azure CLI-vel és sablonnal
description: Erőforrások üzembe helyezése az Azure-ban a Azure Resource Manager és az Azure CLI használatával. Az erőforrások egy Resource Manager-sablonban vannak meghatározva.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 7e8ae7e8c568f5f0ebb85f434e33f142b5fe94e8
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566160"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Erőforrások üzembe helyezése ARM-sablonokkal és Azure CLI-vel

Ez a cikk azt ismerteti, hogyan használható az Azure CLI Azure Resource Manager-sablonokkal (ARM-sablonokkal) az erőforrások Azure-beli üzembe helyezéséhez. Ha nem ismeri az Azure-megoldások üzembe helyezésével és kezelésével kapcsolatos fogalmakat, tekintse meg a [sablonok üzembe helyezésének áttekintése](overview.md)című témakört.

Az üzembe helyezési parancsok az Azure CLI 2.2.0-as verziójában változtak. A cikkben szereplő példákhoz az Azure CLI 2.2.0 vagy újabb verziójára van szükség.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Ha nincs telepítve az Azure CLI, használhatja a [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Központi telepítés hatóköre

Az üzembe helyezést egy erőforráscsoport, egy előfizetés, egy felügyeleti csoport vagy egy bérlő számára is megcélozhatja. A legtöbb esetben az üzembe helyezést egy erőforráscsoporthoz kell megcélozni. A házirendek és a szerepkör-hozzárendelések nagyobb hatókörön való alkalmazásához használja az előfizetést, a felügyeleti csoportot vagy a bérlő központi telepítését. Az előfizetések telepítésekor létrehozhat egy erőforráscsoportot, és erőforrásokat helyezhet üzembe a szolgáltatásban.

A központi telepítés hatókörének függvényében különböző parancsokat kell használnia.

* Egy **erőforráscsoporthoz**való üzembe helyezéshez használja [az az Deployment Group Create](/cli/azure/deployment/group#az-deployment-group-create):

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
  ```

* Egy **előfizetésre**való üzembe helyezéshez használja az [az Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create):

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template>
  ```

  Az előfizetési szintű központi telepítésekkel kapcsolatos további információkért lásd: [erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](deploy-to-subscription.md).

* Egy **felügyeleti csoportba**való központi telepítéshez használja az [az Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create):

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template>
  ```

  További információ a felügyeleti csoport szintű központi telepítésekről: [erőforrások létrehozása a felügyeleti csoport szintjén](deploy-to-management-group.md).

* Egy **bérlőn**való üzembe helyezéshez használja az [az Deployment bérlő Create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template>
  ```

  A bérlői szintű központi telepítésekkel kapcsolatos további információkért lásd: [erőforrások létrehozása a bérlő szintjén](deploy-to-tenant.md).

A cikkben szereplő példák az erőforráscsoportok központi telepítését használják.

## <a name="deploy-local-template"></a>Helyi sablon üzembe helyezése

Amikor erőforrásokat telepít az Azure-ba, a következőket teheti:

1. Jelentkezzen be az Azure-fiókjába
2. Hozzon létre egy erőforráscsoportot, amely tárolóként szolgál az üzembe helyezett erőforrásokhoz. Az erőforráscsoport neve csak alfanumerikus karaktereket, pontokat, aláhúzásokat, kötőjeleket és zárójeleket tartalmazhat. Legfeljebb 90 karakter hosszú lehet. Nem végződhet ponttal.
3. Telepítse az erőforráscsoportot a létrehozandó erőforrásokat meghatározó sablon alapján.

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

## <a name="deployment-name"></a>Központi telepítés neve

Az előző példában az üzemelő példányt nevezték el `ExampleDeployment` . Ha nem adja meg a központi telepítés nevét, a rendszer a sablonfájl nevét használja. Ha például központilag telepít egy nevű sablont `azuredeploy.json` , és nem ad meg központi telepítési nevet, akkor a központi telepítés neve `azuredeploy` .

Minden alkalommal, amikor futtat egy központi telepítést, a rendszer egy bejegyzést ad hozzá az erőforráscsoport telepítési előzményeihez a központi telepítési névvel. Ha egy másik központi telepítést futtat, és ugyanazt a nevet adja, a korábbi bejegyzést a rendszer a jelenlegi telepítéssel helyettesíti. Ha az üzembe helyezési előzményekben egyedi bejegyzéseket kíván fenntartani, adjon meg minden egyes központi telepítést egyedi nevet.

Egyedi név létrehozásához véletlenszerű számot rendelhet hozzá.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

Vagy adjon hozzá egy dátumérték értéket.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

Ha egyazon erőforráscsoporthoz futtat egyidejű központi telepítéseket ugyanazzal a központi telepítési névvel, akkor a rendszer csak az utolsó telepítést hajtja végre. A rendszer a legutóbbi telepítés helyett minden olyan központi telepítést lecserél, amelynek a neve nem fejeződött be. Ha például egy nevű központi telepítést futtat, amely egy nevű `newStorage` Storage-fiókot telepít `storage1` , és egy másik nevű központi telepítési példányt futtat `newStorage` , amely egy nevű Storage-fiókot helyez üzembe `storage2` , csak egy Storage-fiókot telepít. Az eredményül kapott Storage-fiók neve `storage2` .

Ha azonban egy nevű központi telepítést futtat `newStorage` , amely egy nevű Storage-fiókot helyez üzembe `storage1` , és közvetlenül a befejezése után futtat egy másik nevű központi telepítést `newStorage` , amely telepíti a nevű Storage `storage2` -fiókot, akkor két Storage-fiókkal rendelkezik. Az egyik neve `storage1` , a másik pedig a neve `storage2` . Azonban csak egy bejegyzés szerepel az üzembe helyezési előzményekben.

Amikor egyedi nevet ad meg az egyes központi telepítésekhez, ütközés nélkül is futtathatja őket. Ha olyan nevű központi telepítést futtat `newStorage1` , amely egy nevű Storage-fiókot telepít `storage1` , és egy másik nevű központi telepítési példányt futtat `newStorage2` , amely egy nevű Storage-fiókot telepít `storage2` , akkor két Storage-fiókkal és két bejegyzéssel rendelkezik az üzembe helyezési előzményekben.

Az egyidejű központi telepítésekkel való ütközések elkerülése érdekében, valamint a telepítési előzmények egyedi bejegyzéseinek biztosításához adjon egyedi nevet a központi telepítésnek.

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

## <a name="deploy-template-spec"></a>Sablon üzembe helyezése – spec

Helyi vagy távoli sablon telepítése helyett hozzon létre egy [specifikációt](template-specs.md). A sablon spec egy ARM-sablont tartalmazó Azure-előfizetésben található erőforrás. Megkönnyíti a sablon biztonságos megosztását a szervezetben lévő felhasználókkal. Szerepköralapú hozzáférés-vezérlés (RBAC) használatával biztosít hozzáférést a sablonhoz. Ez a funkció jelenleg előzetes verzióban érhető el.

Az alábbi példák bemutatják, hogyan hozhat létre és helyezhet üzembe egy sablon-specifikációt. Ezek a parancsok csak akkor érhetők el, ha [regisztrált az előzetes](https://aka.ms/templateSpecOnboarding)verzióra.

Először hozza létre a sablon specifikációját az ARM-sablon biztosításával.

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

Ezután megkapja a sablon specifikációjának AZONOSÍTÓját, és telepíti azt.

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

További információ: [Azure Resource Manager template specs (előzetes verzió)](template-specs.md).

## <a name="preview-changes"></a>Előnézeti változások

A sablon üzembe helyezése előtt megtekintheti, hogy a sablon milyen módosításokat hajt végre a környezetben. A [mi-if művelettel](template-deploy-what-if.md) ellenőrizheti, hogy a sablon elvégzi-e a várt módosításokat. Mi a teendő, ha a hibát is ellenőrzi a sablonban.

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

A beágyazott paraméterek átadásához adja meg az értékeket a következőben: `parameters` . Ha például egy karakterláncot és tömböt szeretne átadni egy sablonnak egy bash-rendszerhéjra, használja a következőt:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Ha az Azure CLI-t a Windows parancssorral (CMD) vagy a PowerShell-lel használja, adja át a tömböt a következő formátumban: `exampleArray="['value1','value2']"` .

Emellett beolvashatja a fájl tartalmát, és megadhatja a tartalmat beágyazott paraméterként.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Egy paraméter értékének beolvasása a fájlból hasznos lehet, ha konfigurációs értékeket kell megadnia. Megadhat például [egy Linux rendszerű virtuális gép számára a Cloud-init értékeket](../../virtual-machines/linux/using-cloud-init.md).

A arrayContent.jsformátuma:

```json
[
    "value1",
    "value2"
]
```

Egy objektum átadásához, például címkék beállításához használja a JSON-t. Előfordulhat például, hogy a sablon egy olyan paramétert tartalmaz, amely a következőhöz hasonló:

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

Ebben az esetben egy JSON-karakterláncot adhat át a paraméternek a következő bash-parancsfájlban látható módon történő beállításához:

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

Használjon idézőjeleket a JSON-körben, amelyeket át szeretne adni az objektumba.

### <a name="parameter-files"></a>Paraméter fájljai

Ahelyett, hogy a paramétereket a parancsfájlba beágyazott értékként adja át, előfordulhat, hogy könnyebben használható egy JSON-fájl, amely tartalmazza a paraméter értékeit. A paraméter fájljának helyi fájlnak kell lennie. A külső paraméterek fájljai nem támogatottak az Azure CLI-vel.

További információ a paraméter fájlról: [Resource Manager-paraméter fájljának létrehozása](parameter-files.md).

Helyi paraméterérték `@` átadásához a paranccsal adjon meg egy storage.parameters.jsnevű helyi fájlt.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Kiterjesztett JSON-formátum kezelése

Ha többsoros karakterláncokkal vagy megjegyzésekkel rendelkező sablont szeretne üzembe helyezni az Azure CLI-vel, a 2.3.0 vagy régebbi verziójával, akkor a kapcsolót kell használnia `--handle-extended-json-format` .  Például:

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

## <a name="next-steps"></a>Következő lépések

- Ha hibát tapasztal a sikeres üzembe helyezéshez, olvassa el a [hiba visszaállítása a sikeres központi telepítéshez](rollback-on-error.md)című témakört.
- Ha meg szeretné adni, hogyan kezelje az erőforráscsoport meglévő erőforrásait, de a sablonban nincs definiálva, tekintse meg a [Azure Resource Manager üzembe helyezési módokat](deployment-modes.md).
- Ha meg szeretné tudni, hogyan határozhat meg paramétereket a sablonban, tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerése](template-syntax.md)című részt.
- A gyakori telepítési hibák megoldásával kapcsolatos tippekért lásd: [gyakori Azure-telepítési hibák elhárítása Azure Resource Managerokkal](common-deployment-errors.md).
- A SAS-tokent igénylő sablonok telepítésével kapcsolatos információkért lásd: [privát sablon üzembe helyezése sas-tokenrel](secure-template-with-sas-token.md).
