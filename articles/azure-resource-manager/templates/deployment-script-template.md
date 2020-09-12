---
title: Telepítési parancsfájlok használata a sablonokban | Microsoft Docs
description: az üzembe helyezési parancsfájlok használata Azure Resource Manager-sablonokban.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: jgao
ms.openlocfilehash: fb6d1c9e0e2ca545be850af22df15b342cf8d82c
ms.sourcegitcommit: 0194a29a960e3615f96a2d9d8a7e681cf3e8f9ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89667498"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Telepítési parancsfájlok használata a sablonokban (előzetes verzió)

Ismerje meg, hogyan használhatók az üzembe helyezési parancsfájlok az Azure Resource templates szolgáltatásban. A nevű új erőforrástípus használatával a `Microsoft.Resources/deploymentScripts` felhasználók üzembe helyezhetik a telepítési parancsfájlokat a sablonok központi telepítései során, és áttekinthetik a végrehajtás eredményeit. Ezek a parancsfájlok olyan egyéni lépések végrehajtásához használhatók, mint például a következők:

- felhasználók hozzáadása egy címtárhoz
- adatsík műveletek végrehajtása, például a Blobok vagy a vetőmag-adatbázisok másolása
- licenckulcs megkeresése és érvényesítése
- önaláírt tanúsítvány létrehozása
- objektum létrehozása az Azure AD-ben
- IP-címterület kikeresése egyéni rendszerből

Az üzembe helyezési parancsfájl előnyei:

- Egyszerű kód, használat és hibakeresés. Az üzembe helyezési parancsfájlokat kedvenc fejlesztői környezetekben is kifejlesztheti. A szkriptek sablonokba vagy külső parancsfájlokban is beágyazva lehetnek.
- Megadhatja a parancsfájl nyelvét és platformját. Jelenleg a Linux-környezetben a Azure PowerShell és az Azure CLI üzembe helyezési parancsfájljai támogatottak.
- A parancsfájlok végrehajtásához használt identitások megadásának engedélyezése. Jelenleg csak az [Azure-felhasználóhoz rendelt felügyelt identitás](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) támogatott.
- A parancssori argumentumok parancsfájlba való átadásának engedélyezése.
- Megadhatja a parancsfájlok kimeneteit, és visszaküldheti azokat az üzembe helyezéshez.

Az üzembe helyezési parancsfájl erőforrása csak azokon a régiókban érhető el, ahol elérhető az Azure Container instance.  Tekintse [meg az Azure-régiók Azure Container instances erőforrás-elérhetőségét](../../container-instances/container-instances-region-availability.md)ismertető témakört.

> [!IMPORTANT]
> A parancsfájlok végrehajtásához és a hibaelhárításhoz szükség van egy Storage-fiókra és egy tároló példányra. Lehetősége van meglévő Storage-fiók megadására, máskülönben a Storage-fiókkal együtt automatikusan létrejön a tároló-példány. A szkript szolgáltatás általában törli a két automatikusan létrehozott erőforrást, amikor a telepítési parancsfájl végrehajtása egy terminál állapotba kerül. Az erőforrások számlázása az erőforrások törlése után történik. További információért lásd: [telepítési parancsfájl-erőforrások tisztítása](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Előfeltételek

- **Felhasználó által hozzárendelt felügyelt identitás a közreműködői szerepkörrel a célként megadott erőforrás-csoport számára**. Ez az identitás az üzembe helyezési parancsfájlok végrehajtásához használatos. Az erőforráscsoporton kívüli műveletek elvégzéséhez további engedélyeket kell megadnia. Rendelje hozzá például az identitást az előfizetés szintjéhez, ha új erőforráscsoportot szeretne létrehozni.

  > [!NOTE]
  > A parancsfájl-szolgáltatás létrehoz egy Storage-fiókot (kivéve, ha megad egy meglévő Storage-fiókot) és a háttérben található Container-példányt.  Az előfizetési szinten a közreműködő szerepkörrel rendelkező felhasználóhoz rendelt felügyelt identitás megadása kötelező, ha az előfizetés nem regisztrálta az Azure Storage-fiókot (Microsoft. Storage) és az Azure Container instance (Microsoft. ContainerInstance) erőforrás-szolgáltatót.

  Identitás létrehozásához tekintse meg a [felhasználó által hozzárendelt felügyelt identitás létrehozása a Azure Portal használatával](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)vagy az [Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)használatával vagy a [Azure PowerShell használatával](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)című témakört. A sablon üzembe helyezésekor szüksége lesz az azonosító AZONOSÍTÓra. Az identitás formátuma:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Az alábbi CLI-vagy PowerShell-parancsfájl használatával szerezze be az azonosítót az erőforráscsoport nevének és az identitás nevének megadásával.

  # <a name="cli"></a>[Parancssori felület](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g $resourceGroupName -n $idName --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure PowerShell** vagy az **Azure CLI**. Tekintse meg a [támogatott Azure PowerShell verziók](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)listáját. Tekintse meg a [támogatott Azure CLI-verziók](https://mcr.microsoft.com/v2/azure-cli/tags/list)listáját.

    >[!IMPORTANT]
    > A telepítési parancsfájl a Microsoft Container Registry (MCR) által elérhető CLI-rendszerképeket használja. Egy hónapot vesz igénybe, hogy az üzembe helyezési parancsfájlhoz tartozó CLI-rendszerképet hitelesítse. Ne használja a 30 napon belül kiadott CLI-verziókat. A képek kiadási dátumait az [Azure CLI kibocsátási megjegyzései](/cli/azure/release-notes-azure-cli?view=azure-cli-latest)című témakörben találja. Ha nem támogatott verziót használ, a hibaüzenet felsorolja a támogatott verziókat.

    A sablonok telepítéséhez nincs szükség ezekre a verziókra. Ezek a verziók azonban az üzembe helyezési parancsfájlok helyi teszteléséhez szükségesek. Lásd: [a Azure PowerShell modul telepítése](/powershell/azure/install-az-ps). Előre konfigurált Docker-rendszerképet használhat.  Lásd: a [fejlesztési környezet konfigurálása](#configure-development-environment).

## <a name="sample-templates"></a>Példasablonok

A következő JSON egy példa.  A sablon legújabb sémája [itt](/azure/templates/microsoft.resources/deploymentscripts)található.

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "-name \\\"John Dole\\\"",
    "environmentVariables": [
      {
        "name": "someSecret",
        "secureValue": "if this is really a secret, don't put it here... in plain text..."
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> A példa demonstrációs célra szolgál.  a **scriptContent** és a **primaryScriptUri** nem létezhet egyetlen sablonban sem.

Tulajdonság értékének részletei:

- **Identitás**: az üzembe helyezési parancsfájl szolgáltatás felhasználó által hozzárendelt felügyelt identitást használ a parancsfájlok végrehajtásához. Jelenleg csak a felhasználó által hozzárendelt felügyelt identitás támogatott.
- **Típus: adja**meg a parancsfájl típusát. Jelenleg a Azure PowerShell és az Azure CLI-parancsfájlok támogatottak. Az értékek a következők: **AzurePowerShell** és **AzureCLI**.
- **forceUpdateTag**: ennek az értéknek a módosítása a sablon központi telepítései között kényszeríti a telepítési parancsfájl ismételt végrehajtását. Használja a newGuid () vagy a utcNow () függvényt, amelyet a paraméter defaultValue értékeként kell beállítani. További információ: [parancsfájl futtatása](#run-script-more-than-once)többször.
- **containerSettings**: határozza meg az Azure Container instance testreszabásához szükséges beállításokat.  a **containerGroupName** a tároló csoport nevének megadására szolgál.  Ha nincs megadva, a rendszer automatikusan létrehozza a csoport nevét.
- **storageAccountSettings**: meglévő Storage-fiók használatára vonatkozó beállítások megadása. Ha nincs megadva, a rendszer automatikusan létrehozza a Storage-fiókot. Lásd: [meglévő Storage-fiók használata](#use-existing-storage-account).
- **azPowerShellVersion** / **azCliVersion**: Itt adhatja meg a használni kívánt modul verzióját. A PowerShell és a parancssori felület támogatott verzióinak listájáért lásd: [Előfeltételek](#prerequisites).
- **argumentumok**: határozza meg a paraméterek értékeit. Az értékeket szóközök választják el egymástól.

    Az üzembe helyezési parancsfájlok karakterláncok tömbje szerint osztják el az argumentumokat a [CommandLineToArgvW ](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) rendszerhívás meghívásával. Erre azért van szükség, mert az argumentumok [parancs tulajdonságként](/rest/api/container-instances/containergroups/createorupdate#containerexec) lesznek átadva az Azure Container instance számára, a Command tulajdonság pedig sztring.

    Ha az argumentumok Escape-karaktereket tartalmaznak, a [JsonEscaper](https://www.jsonescaper.com/) használatával megduplázhatja a karaktereket. Illessze be az eredeti Escape-karakterláncot az eszközbe, majd válassza a **Escape**lehetőséget.  Az eszköz egy dupla Escape-karakterláncot ad vissza. Az előző minta sablonban például a következő argumentum: **-name \\ "John Dole \\ "**.  Az Escape **-karakterlánc neve \\ \\ \\ "John Dole \\ \\ \\ "**.

    Ha argumentumként egy ARM-sablon típusú paramétert szeretne átadni, alakítsa át az objektumot egy sztringre a [karakterlánc ()](./template-functions-string.md#string) függvény használatával, majd a [replace ()](./template-functions-string.md#replace) függvény használatával cserélje le a ** \\ "** into ** \\ \\ \\ "** karakterláncot. Például:

    ```json
    replace(string(parameters('tables')), '\"', '\\\"')
    ```

    A minta sablon megtekintéséhez válassza az [itt](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json)lehetőséget.

- **environmentVariables**: adja meg azokat a környezeti változókat, amelyeket át kell adni a parancsfájlnak. További információ: [telepítési parancsfájlok fejlesztése](#develop-deployment-scripts).
- **scriptContent**: adja meg a parancsfájl tartalmát. Külső parancsfájl futtatásához használja `primaryScriptUri` helyette a parancsot. Példák: [beágyazott parancsfájl használata](#use-inline-scripts) és [külső parancsfájl használata](#use-external-scripts).
- **primaryScriptUri**: adjon meg egy nyilvánosan elérhető URL-címet az elsődleges telepítési parancsfájl számára a támogatott fájlkiterjesztések használatával.
- **supportingScriptUris**: adja meg a nyilvánosan elérhető URL-címek tömbjét, amely támogatja a vagy a által meghívott fájlokat `ScriptContent` `PrimaryScriptUri` .
- **időtúllépés**: adja meg az [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601)megadott maximálisan engedélyezett parancsfájl-végrehajtási időt. Az alapértelmezett érték a **P1D**.
- **cleanupPreference**. Adja meg a telepítési erőforrások törlésének előnyét, ha a parancsfájl végrehajtása terminál állapotba kerül. Az alapértelmezett beállítás **mindig**, ami azt jelenti, hogy a rendszer a terminál állapota (sikeres, sikertelen, megszakított) ellenére törli az erőforrásokat. További információ: [üzembe helyezési parancsfájl erőforrásainak tisztítása](#clean-up-deployment-script-resources).
- **retentionInterval**: adja meg azt az időközt, ameddig a szolgáltatás megtartja a telepítési parancsfájl erőforrásait, miután a telepítési parancsfájl végrehajtása eléri a terminál állapotát. Az üzembe helyezési parancsfájl erőforrásai törlődnek, ha ez az időtartam lejár. Az időtartam az [ISO 8601 minta](https://en.wikipedia.org/wiki/ISO_8601)alapján történik. Az alapértelmezett érték a **P1D**, vagyis egy nap. Ezt a tulajdonságot akkor használja a rendszer, ha a cleanupPreference értéke *OnExpiration*. A *OnExpiration* tulajdonság jelenleg nincs engedélyezve. További információ: [üzembe helyezési parancsfájl erőforrásainak tisztítása](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>További minták

- [1. példa](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json): kulcstartó létrehozása és üzembe helyezési parancsfájl használata tanúsítvány hozzárendeléséhez a kulcstartóhoz.
- [2. minta](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json): hozzon létre egy erőforráscsoportot az előfizetési szinten, hozzon létre egy kulcstartót az erőforráscsoporthoz, majd az üzembehelyezési parancsfájl használatával rendeljen hozzá egy tanúsítványt a kulcstartóhoz.
- [3. példa](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json): hozzon létre egy felhasználó által hozzárendelt felügyelt identitást, rendelje hozzá a közreműködői szerepkört az identitáshoz az erőforráscsoport szintjén, hozzon létre egy kulcstartót, majd a telepítési parancsfájl használatával rendeljen hozzá egy tanúsítványt a kulcstartóhoz.

> [!NOTE]
> Javasoljuk, hogy hozzon létre egy felhasználó által hozzárendelt identitást, és adja meg előre az engedélyeket. Ha az identitást hozza létre, és engedélyeket ad a telepítési parancsfájlok futtatásához, akkor előfordulhat, hogy be kell jelentkeznie a bejelentkezési és az engedélyekkel kapcsolatos hibákba. Időbe telik, amíg az engedélyek érvénybe lépnek.

## <a name="use-inline-scripts"></a>Beágyazott parancsfájlok használata

A következő sablon egyetlen erőforrással van definiálva a `Microsoft.Resources/deploymentScripts` típussal. A kiemelt rész a beágyazott parancsfájl.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Mivel a beágyazott üzembe helyezési parancsfájlok idézőjelek közé vannak ágyazva, az üzembe helyezési parancsfájlokban lévő sztringeket **&#92;** vagy szimpla idézőjelek közé kell tenni. Azt is megteheti, hogy a karakterlánc-helyettesítést használja az előző JSON-mintában látható módon.

A parancsfájl egy paramétert fogad, és kiírja a paraméter értékét. A **DeploymentScriptOutputs** a kimenetek tárolására szolgál.  A kimenetek szakaszban az **érték** sorban látható, hogyan férhet hozzá a tárolt értékekhez. `Write-Output` hibakeresési célra szolgál. A kimeneti fájl elérésének megismeréséhez lásd: [telepítési parancsfájlok figyelése és hibáinak megoldása](#monitor-and-troubleshoot-deployment-scripts).  A tulajdonságok leírását lásd: [példák a sablonokra](#sample-templates).

A parancsfájl futtatásához válassza a **kipróbálás** lehetőséget a Cloud Shell megnyitásához, majd illessze be a következő kódot a rendszerhéj ablaktáblába.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

A kimenet a következőképpen fog kinézni:

![Resource Manager-sablon üzembe helyezési parancsfájl Hello World output](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Külső parancsfájlok használata

A beágyazott parancsfájlok mellett külső parancsfájlokat is használhat. Csak a **ps1** fájlnévkiterjesztéssel rendelkező elsődleges PowerShell-parancsfájlok támogatottak. A CLI-parancsfájlok esetében az elsődleges parancsfájlok rendelkezhetnek kiterjesztéssel (vagy kiterjesztés nélkül), feltéve, hogy a parancsfájlok érvényes bash-parancsfájlok. A külső parancsfájlok használatához cserélje le a parancsot a következőre: `scriptContent` `primaryScriptUri` . Például:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Ha példát szeretne látni, válassza az [itt](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)lehetőséget.

A külső parancsfájloknak elérhetőnek kell lenniük.  Az Azure Storage-fiókokban tárolt parancsfájlok biztonságossá tételéhez lásd: [Private ARM-sablon üzembe helyezése sas-token](./secure-template-with-sas-token.md)használatával.

Ön felelős az üzembe helyezési parancsfájl által hivatkozott parancsfájlok integritásának biztosításáért, vagy **PrimaryScriptUri** vagy **SupportingScriptUris**.  Csak a megbízható parancsfájlokra hivatkozzon.

## <a name="use-supporting-scripts"></a>Támogató parancsfájlok használata

A bonyolult logikai műveleteket egy vagy több támogató parancsfájlba is elkülönítheti. A `supportingScriptUris` tulajdonság lehetővé teszi, hogy szükség esetén az URI-k tömbjét adja meg a támogató parancsfájl-fájlokhoz:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

A támogató parancsfájlok a beágyazott parancsfájlokból és az elsődleges parancsfájlokból is meghívhatók. A támogató parancsfájlok nem rendelkeznek korlátozásokkal a fájlkiterjesztés esetében.

A rendszer a támogató fájlokat a futtatókörnyezet azscripts/azscriptinput másolja. Relatív elérési út használatával hivatkozhat a beágyazott parancsfájlokból és az elsődleges parancsfájlokból származó támogató fájlokra.

## <a name="work-with-outputs-from-powershell-script"></a>Kimenetek használata PowerShell-parancsfájlból

A következő sablon bemutatja, hogyan adhat át értékeket két deploymentScripts-erőforrás között:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

Az első erőforrásban definiál egy **$DeploymentScriptOutputs**nevű változót, és a kimeneti értékek tárolására használja azt. A sablonban lévő másik erőforrás kimeneti értékének eléréséhez használja a következőt:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Kimenetek használata a CLI-szkriptből

Eltér a PowerShell telepítési parancsfájltól, a CLI/bash-támogatás nem tesz elérhetővé egy közös változót a parancsfájlok kimenetének tárolásához, hanem egy **AZ_SCRIPTS_OUTPUT_PATH** nevű környezeti változót, amely a parancsfájl kimeneti fájljának helyét tárolja. Ha egy üzembe helyezési parancsfájl egy Resource Manager-sablonból fut, akkor a bash rendszerhéj automatikusan beállítja ezt a környezeti változót.

Az üzembe helyezési parancsfájl kimeneteit a AZ_SCRIPTS_OUTPUT_PATH helyre kell menteni, és a kimeneteknek érvényes JSON karakterlánc-objektumnak kell lenniük. A fájl tartalmát kulcs-érték párokként kell menteni. A karakterláncok tömbjét például {"MyResult": ["foo", "Bar"]} tárolja.  A csak a tömb eredményeinek tárolása (például ["foo", "Bar"]) érvénytelen.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

a [jQ](https://stedolan.github.io/jq/) az előző mintában van használatban. A tároló lemezképeit tartalmazza. Lásd: a [fejlesztési környezet konfigurálása](#configure-development-environment).

## <a name="use-existing-storage-account"></a>Meglévő Storage-fiók használata

A parancsfájlok végrehajtásához és a hibaelhárításhoz szükség van egy Storage-fiókra és egy tároló példányra. Lehetősége van meglévő Storage-fiók megadására, máskülönben a Storage-fiókkal együtt automatikusan létrejön a tároló-példány. Meglévő Storage-fiók használatára vonatkozó követelmények:

- A támogatott Storage-fiókok a következők:

    | Termékváltozat             | Támogatott típus     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | Tárolás, StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Tárolás, StorageV2 |
    | Standard_RAGRS  | Tárolás, StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    Ezek a kombinációk támogatják a fájlmegosztást.  További információ: Azure- [fájlmegosztás](../../storage/files/storage-how-to-create-file-share.md) és- [típusú tárolási fiókok](../../storage/common/storage-account-overview.md)létrehozása.
- A Storage-fiók tűzfalszabályok még nem támogatottak. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../../storage/common/storage-network-security.md).
- A telepítési parancsfájl felhasználó által hozzárendelt felügyelt identitásának engedélyekkel kell rendelkeznie a Storage-fiók kezeléséhez, beleértve az olvasás, a létrehozás és a fájlmegosztás törlését.

Meglévő Storage-fiók megadásához adja hozzá a következő JSON-t a tulajdonság eleméhez `Microsoft.Resources/deploymentScripts` :

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- **storageAccountName**: adja meg a Storage-fiók nevét.
- **storageAccountKey "**: a Storage-fiók kulcsainak egyikét kell megadnia. A [`listKeys()`](./template-functions-resource.md#listkeys) kulcs lekéréséhez használhatja a függvényt. Például:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Tekintse meg a teljes definíciós minta [sablonjait](#sample-templates) `Microsoft.Resources/deploymentScripts` .

Meglévő Storage-fiók használatakor a parancsfájl-szolgáltatás egy egyedi névvel rendelkező fájlmegosztást hoz létre. Tekintse meg a [telepítési parancsfájl-erőforrások tisztítása](#clean-up-deployment-script-resources) című témakört, amely azt ismerteti, hogyan törli a parancsfájl-szolgáltatás a fájlmegosztást.

## <a name="develop-deployment-scripts"></a>Üzembe helyezési parancsfájlok fejlesztése

### <a name="handle-non-terminating-errors"></a>Nem megszakítást okozó hibák kezelése

Az üzembe helyezési parancsfájl **$ErrorActionPreference** változójának használatával szabályozhatja, hogyan válaszol a PowerShell a nem megszakítást okozó hibákra. Ha a változó nincs beállítva a telepítési parancsfájlban, a parancsfájl-szolgáltatás az **alapértelmezett értéket fogja**használni.

A parancsfájl-szolgáltatás az erőforrás-kiépítési állapotot úgy állítja be, hogy **sikertelen** legyen, ha a parancsfájl hibát észlel a $ErrorActionPreference beállítása ellenére.

### <a name="pass-secured-strings-to-deployment-script"></a>Biztonságos karakterláncok továbbítása a telepítési parancsfájlba

A környezeti változók (EnvironmentVariable) beállítása a Container instances szolgáltatásban lehetővé teszi a tároló által futtatott alkalmazás vagy parancsfájl dinamikus konfigurációját. Az üzembe helyezési parancsfájl ugyanúgy kezeli a nem védett és a biztonságos környezeti változókat, mint az Azure Container instance. További információ: [környezeti változók beállítása a Container instances](../../container-instances/container-instances-environment-variables.md#secure-values)szolgáltatásban.

A környezeti változók maximálisan megengedett mérete 64 kb.

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>Üzembe helyezési parancsfájlok figyelése és hibáinak megoldása

A parancsfájl-szolgáltatás létrehoz egy [Storage-fiókot](../../storage/common/storage-account-overview.md) (kivéve, ha megad egy meglévő Storage-fiókot) és egy tároló- [példányt](../../container-instances/container-instances-overview.md) a parancsfájlok végrehajtásához. Ha ezeket az erőforrásokat a script Service automatikusan hozza létre, mindkét erőforráshoz az **azscripts** utótag tartozik.

![Resource Manager-sablon telepítési parancsfájljának erőforrásainak nevei](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

A felhasználói parancsfájl, a végrehajtás eredményei és az stdout-fájl a Storage-fiók Files megosztásában tárolódik. Van egy **azscripts**nevű mappa. A mappában két további mappa található a bemeneti és a kimeneti fájlokhoz: **azscriptinput** és **azscriptoutput**.

A kimeneti mappa tartalmaz egy **executionresult.jst** és a parancsfájl kimeneti fájlját. Megtekintheti a parancsfájl-végrehajtási hibaüzenetet **executionresult.json**. A kimeneti fájl csak akkor jön létre, ha a parancsfájl végrehajtása sikeresen megtörtént. A bemeneti mappa egy PowerShell-parancsfájlt és a felhasználói telepítési parancsfájlokat tartalmaz. A felhasználói telepítési parancsfájlt lecserélheti egy módosítottra, majd újra futtathatja az üzembe helyezési parancsfájlt az Azure Container instanceból.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Az üzembe helyezési parancsfájl erőforrásának üzembe helyezése után az erőforrás a Azure Portal erőforráscsoport alatt jelenik meg. Az alábbi képernyőfelvételen a telepítési parancsfájl erőforrásának Áttekintés lapja látható:

![Resource Manager-sablonok üzembe helyezési parancsfájl-portálja – áttekintés](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

Az Áttekintés oldalon az erőforrás néhány fontos információja látható, például a **kiépítési állapot**, a **Storage-fiók**, a **Container instance**és a **naplók**.

A bal oldali menüben megtekintheti a telepítési parancsfájl tartalmát, a parancsfájlnak átadott argumentumokat, valamint a kimenetet.  A telepítési parancsfájlhoz is exportálhat sablont, beleértve az üzembehelyezési parancsfájlt is.

### <a name="use-powershell"></a>A PowerShell használata

A Azure PowerShell használatával a telepítési parancsfájlokat az előfizetés vagy az erőforráscsoport hatóköre szerint kezelheti:

- [Get-AzDeploymentScript](/powershell/module/az.resources/get-azdeploymentscript): üzembe helyezési parancsfájlok beolvasása vagy felsorolása.
- [Get-AzDeploymentScriptLog](/powershell/module/az.resources/get-azdeploymentscriptlog): lekéri a telepítési parancsfájl végrehajtásának naplóját.
- [Remove-AzDeploymentScript](/powershell/module/az.resources/remove-azdeploymentscript): eltávolítja a telepítési parancsfájlt és a hozzá tartozó erőforrásokat.
- [Save-AzDeploymentScriptLog](/powershell/module/az.resources/save-azdeploymentscriptlog): menti a telepítési parancsfájl naplóját a lemezre.

A Get-AzDeploymentScript kimenet a következőhöz hasonló:

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

Az Azure CLI használatával a telepítési parancsfájlokat az előfizetés vagy az erőforráscsoport hatóköre szerint kezelheti:

- [az Deployment-Scripts delete](/cli/azure/deployment-scripts?view=azure-cli-latest#az-deployment-scripts-delete): telepítési parancsfájl törlése.
- [az Deployment-Scripts List](/cli/azure/deployment-scripts?view=azure-cli-latest#az-deployment-scripts-list): az összes üzembehelyezési parancsfájl listázása.
- [az Deployment-Scripts show](/cli/azure/deployment-scripts?view=azure-cli-latest#az-deployment-scripts-show): telepítési parancsfájl beolvasása.
- [az Deployment-Scripts show-log: az](/cli/azure/deployment-scripts?view=azure-cli-latest#az-deployment-scripts-show-log)üzembe helyezési parancsfájl naplófájljainak megjelenítése.

A LIST parancs kimenete a következőhöz hasonló:

```json
[
  {
    "arguments": "-name \\\"John Dole\\\"",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200625T025902Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0624rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "endTime": "2020-06-25T03:00:16.796923+00:00",
      "error": null,
      "expirationTime": "2020-06-26T03:00:16.796923+00:00",
      "startTime": "2020-06-25T02:59:07.595140+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts"
    },
    "storageAccountSettings": null,
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-25T02:59:04.750195+00:00",
      "createdBy": "someone@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-25T02:59:04.750195+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  }
]
```

### <a name="use-rest-api"></a>REST API használata

Az üzembe helyezési parancsfájl erőforrás-telepítési információit az erőforráscsoport szintjén és az előfizetés szintjén szerezheti be REST API használatával:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

A következő példa a [ARMClient](https://github.com/projectkudu/ARMClient)használja:

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

Az eredmény az alábbihoz hasonlóan fog kinézni:

```json
{
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
        "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF",
        "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF"
      }
    }
  },
  "location": "centralus",
  "systemData": {
    "createdBy": "someone@contoso.com",
    "createdByType": "User",
    "createdAt": "2020-06-25T02:59:04.7501955Z",
    "lastModifiedBy": "someone@contoso.com",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2020-06-25T02:59:04.7501955Z"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "forceUpdateTag": "20200625T025902Z",
    "azPowerShellVersion": "3.0",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "arguments": "-name \\\"John Dole\\\"",
    "retentionInterval": "P1D",
    "timeout": "PT1H",
    "containerSettings": {},
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts",
      "startTime": "2020-06-25T02:59:07.5951401Z",
      "endTime": "2020-06-25T03:00:16.7969234Z",
      "expirationTime": "2020-06-26T03:00:16.7969234Z"
    },
    "outputs": {
      "text": "Hello John Dole"
    },
    "cleanupPreference": "OnSuccess"
  },
  "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
  "type": "Microsoft.Resources/deploymentScripts",
  "name": "runPowerShellInlineWithOutput"
}

```

A következő REST API a naplót adja vissza:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Ez csak az üzembe helyezési parancsfájl erőforrásainak törlése előtt működik.

Ha meg szeretné tekinteni a deploymentScripts-erőforrást a portálon, válassza a **rejtett típusok megjelenítése**lehetőséget:

![Resource Manager-sablon telepítési parancsfájlja, rejtett típusok megjelenítése, portál](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Üzembe helyezési parancsfájl erőforrásainak karbantartása

A parancsfájlok végrehajtásához és a hibaelhárításhoz szükség van egy Storage-fiókra és egy tároló példányra. Lehetősége van megadnia egy meglévő Storage-fiókot, ellenkező esetben a rendszer automatikusan létrehozza a Storage-fiókot a tároló-példánnyal együtt. A parancsfájl-szolgáltatás két automatikusan létrehozott erőforrást töröl, amikor a telepítési parancsfájl végrehajtása egy terminál állapotba kerül. Az erőforrások számlázása az erőforrások törlése után történik. Az árakra vonatkozó információkért lásd: [Container instances díjszabás](https://azure.microsoft.com/pricing/details/container-instances/) és az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).

Ezeknek az erőforrásoknak a életciklusát a sablon következő tulajdonságai vezérlik:

- **cleanupPreference**: Ha a parancsfájl végrehajtása egy terminál állapotba kerül, a rendszer törli a beállítást. A támogatott értékek a következők:

  - **Mindig**: törölje az automatikusan létrehozott erőforrásokat, ha a parancsfájl végrehajtása terminál állapotba kerül. Ha egy meglévő Storage-fiókot használ, a parancsfájl-szolgáltatás törli a Storage-fiókban létrehozott fájlmegosztást. Mivel a deploymentScripts-erőforrás továbbra is megtalálható az erőforrások tisztítása után, a parancsfájl-szolgáltatás megőrzi a parancsfájlok végrehajtásának eredményét, például az stdout, a kimenetek, a visszatérési érték stb. az erőforrások törlése előtt.
  - **OnSuccess**: csak akkor törölje az automatikusan létrehozott erőforrásokat, ha a parancsfájl végrehajtása sikeres. Ha egy meglévő Storage-fiókot használ, a parancsfájl-szolgáltatás csak akkor távolítja el a fájlmegosztást, ha a parancsfájl végrehajtása sikeres. A hibakeresési adatok megkereséséhez továbbra is hozzáférhet az erőforrásokhoz.
  - **OnExpiration**: csak akkor törölje az automatikusan létrehozott erőforrásokat, ha a **retentionInterval** -beállítás lejár. Ha meglévő Storage-fiókot használ, a parancsfájl-szolgáltatás eltávolítja a fájlmegosztást, de megtartja a Storage-fiókot.

- **retentionInterval**: adja meg azt az időintervallumot, ameddig a rendszer megőrzi a parancsfájl-erőforrást, majd azután, hogy lejárt és törölve lesz.

> [!NOTE]
> A Storage-fiók és a parancsfájl-szolgáltatás által más célra létrehozott tároló-példány használata nem ajánlott. Előfordulhat, hogy a parancsfájl életciklusa alapján a két erőforrás el lesz távolítva.

## <a name="run-script-more-than-once"></a>Parancsfájl többszöri futtatása

A telepítési parancsfájl végrehajtása egy idempotens művelet. Ha a deploymentScripts erőforrás-tulajdonságok egyike sincs (beleértve a beágyazott parancsfájlt is), a parancsfájl nem lesz végrehajtva a sablon újbóli telepítésekor. Az üzembe helyezési parancsfájl szolgáltatás összehasonlítja a sablonban lévő erőforrás-neveket az ugyanabban az erőforráscsoport meglévő erőforrásaival. Két lehetőség közül választhat, ha ugyanazt az üzembe helyezési parancsfájlt többször szeretné végrehajtani:

- Módosítsa a deploymentScripts-erőforrás nevét. Használja például az [utcNow](./template-functions-date.md#utcnow) -sablon függvényt az erőforrás neve vagy az erőforrás neve részeként. Az erőforrás nevének módosítása új deploymentScripts-erőforrást hoz létre. A parancsfájl-végrehajtás előzményeinek megőrzése jó.

    > [!NOTE]
    > A utcNow függvény csak a paraméter alapértelmezett értékében használható.

- Egy másik értéket kell megadnia a `forceUpdateTag` sablon tulajdonságban.  Használja például a utcNow értéket.

> [!NOTE]
> Írja be a idempotens telepítési parancsfájlokat. Ez biztosítja, hogy ha véletlenül újra futnak, a rendszer nem fog változásokat okozni. Ha például az üzembe helyezési parancsfájl egy Azure-erőforrás létrehozásához használatos, ellenőrizze, hogy az erőforrás nem létezik-e a létrehozása előtt, így a parancsfájl sikeres lesz, vagy nem hozza létre újra az erőforrást.

## <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

Egy előre konfigurált tároló-rendszerképet is használhat a telepítési parancsfájl fejlesztési környezete számára. További információ: a [fejlesztői környezet konfigurálása a sablonokban történő üzembe helyezési parancsfájlokhoz](./deployment-script-template-configure-dev.md).

A parancsfájl sikeres tesztelése után a sablonban használható üzembe helyezési parancsfájlként.

## <a name="deployment-script-error-codes"></a>Üzembehelyezési parancsfájl hibakódai

| Hibakód | Description |
|------------|-------------|
| DeploymentScriptInvalidOperation | Az üzembehelyezési parancsfájl erőforrás-definíciója a sablonban érvénytelen tulajdonságokat tartalmaz. |
| DeploymentScriptResourceConflict | Nem terminálos állapotú központi telepítési parancsfájl-erőforrás nem törölhető, és a végrehajtás nem haladja meg az 1 órát. Vagy nem futtathatja újra ugyanazt az üzembe helyezési parancsfájlt ugyanazzal az erőforrás-azonosítóval (az előfizetés, az erőforráscsoport neve és az erőforrás neve), de a parancsfájl törzse egyszerre több tartalmat is tartalmaz. |
| DeploymentScriptOperationFailed | Az üzembe helyezési parancsfájl művelete belső hiba miatt meghiúsult. Forduljon a Microsoft ügyfélszolgálatához. |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | Nincs megadva a hozzáférési kulcs a meglévő Storage-fiókhoz.|
| DeploymentScriptContainerGroupContainsInvalidContainers | Az üzembe helyezési parancsfájl szolgáltatás által létrehozott tároló csoport külsőleg módosítva lett, és érvénytelen tárolók lettek hozzáadva. |
| DeploymentScriptContainerGroupInNonterminalState | Két vagy több üzembe helyezési parancsfájl erőforrás ugyanazt az Azure Container instance-nevet használja ugyanabban az erőforráscsoportban, és egyikük még nem fejezte be a végrehajtást. |
| DeploymentScriptStorageAccountInvalidKind | A BlobBlobStorage vagy BlobStorage-típus meglévő Storage-fiókja nem támogatja a fájlmegosztás használatát, és nem használható. |
| DeploymentScriptStorageAccountInvalidKindAndSku | A meglévő Storage-fiók nem támogatja a fájlmegosztást. A támogatott Storage-fiókok listáját a [meglévő Storage-fiók használata](#use-existing-storage-account)című témakörben tekintheti meg. |
| DeploymentScriptStorageAccountNotFound | A Storage-fiók nem létezik, vagy egy külső folyamat vagy eszköz törölte. |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | A megadott Storage-fiók rendelkezik szolgáltatási végponttal. A szolgáltatási végponttal rendelkező Storage-fiókok nem támogatottak. |
| DeploymentScriptStorageAccountInvalidAccessKey | A meglévő Storage-fiókhoz megadott hozzáférési kulcs érvénytelen. |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | A Storage-fiók kulcsának formátuma érvénytelen. Lásd: a [Storage-fiók elérési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md). |
| DeploymentScriptExceededMaxAllowedTime | A telepítési parancsfájl végrehajtási ideje túllépte a telepítési parancsfájl erőforrás-definíciójában megadott időtúllépési értéket. |
| DeploymentScriptInvalidOutputs | A telepítési parancsfájl kimenete nem érvényes JSON-objektum. |
| DeploymentScriptContainerInstancesServiceLoginFailure | A felhasználó által hozzárendelt felügyelt identitás nem tudott bejelentkezni az 1 perces intervallummal rendelkező 10 próbálkozás után. |
| DeploymentScriptContainerGroupNotFound | Az üzembe helyezési parancsfájl szolgáltatás által létrehozott tároló csoportot egy külső eszköz vagy folyamat törölte. |
| DeploymentScriptDownloadFailure | Nem sikerült letölteni egy támogató parancsfájlt. Lásd: [támogató parancsfájl használata](#use-supporting-scripts).|
| DeploymentScriptError | A felhasználói parancsfájl hibát jelzett. |
| DeploymentScriptBootstrapScriptExecutionFailed | A rendszerindítási parancsfájl hibát jelzett. A rendszerindítási parancsfájl a telepítési parancsfájl végrehajtásának előkészítésére szolgáló rendszerparancsfájl. |
| DeploymentScriptExecutionFailed | Ismeretlen hiba történt a telepítési parancsfájl végrehajtása során. |
| DeploymentScriptContainerInstancesServiceUnavailable | Az Azure Container instance (ACI) létrehozásakor az ACI a szolgáltatás nem érhető el hibát váltott ki. |
| DeploymentScriptContainerGroupInNonterminalState | Az Azure Container instance (ACI) létrehozásakor egy másik telepítési parancsfájl ugyanazt az ACI-nevet használja ugyanabban a hatókörben (az előfizetés, az erőforráscsoport neve és az erőforrás neve). |
| DeploymentScriptContainerGroupNameInvalid | Az Azure Container instance megadott neve (ACI) nem felel meg az ACI-követelményeknek. Lásd: [Azure Container instances gyakori problémáinak elhárítása](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment).|

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan használhatja a telepítési parancsfájlokat. Útmutató az üzembe helyezési parancsfájlhoz:

> [!div class="nextstepaction"]
> [Oktatóanyag: telepítési parancsfájlok használata Azure Resource Manager-sablonokban](./template-tutorial-deployment-script.md)
