---
title: Központi telepítési parancsfájlok használata sablonokban | Microsoft dokumentumok
description: központi telepítési parancsfájlok használata az Azure Resource Manager-sablonokban.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: jgao
ms.openlocfilehash: f84707adfa406011989c8f9bfdf1e8d9270698a6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984793"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Központi telepítési parancsfájlok használata sablonokban (előzetes verzió)

Ismerje meg, hogyan használhatja a központi telepítési parancsfájlok az Azure Resource-sablonokban. A felhasználó a `Microsoft.Resources/deploymentScripts`sablontelepítések ben futtathatja a központi telepítési parancsfájlokat, és áttekintheti a végrehajtási eredményeket egy új erőforrástípussal. Ezek a parancsfájlok egyéni lépések végrehajtásához használhatók, például:

- felhasználók hozzáadása könyvtárhoz
- alkalmazásregisztráció létrehozása
- adatsík-műveleteket hajt végre, például blobok másolása vagy magadatbázis
- licenckulcs felkelő és érvényesítése
- önaláírt tanúsítvány létrehozása
- objektum létrehozása az Azure AD-ben
- IP-címblokkok felnézése egyéni rendszerből

A központi telepítési parancsfájl előnyei:

- Könnyen kódolható, használható és hibakeresés. Központi telepítési parancsfájlok a kedvenc fejlesztői környezetekben. A parancsfájlok beágyazhatók sablonokba vagy külső parancsfájlokba.
- Megadhatja a parancsfájl nyelvét és platformját. Jelenleg az Azure PowerShell és az Azure CLI központi telepítési parancsfájlok a Linux-környezetben támogatottak.
- A parancsfájlok végrehajtásához használt identitások megadásának engedélyezése. Jelenleg csak [az Azure-felhasználó által hozzárendelt felügyelt identitás](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) támogatott.
- A parancssori argumentumok átadásának engedélyezése a parancsfájlba.
- Megadhatja a parancsfájl kimenetek, és adja vissza őket a központi telepítéshez.

A központi telepítési parancsfájl-erőforrás csak azokban a régiókban érhető el, ahol az Azure Container Instance érhető el.  Lásd: [Az Azure Container-példányok erőforrás-elérhetősége az Azure-régiókban.](../../container-instances/container-instances-region-availability.md)

> [!IMPORTANT]
> Két központi telepítési parancsfájl-erőforrás, egy tárfiók és egy tárolópéldány jön létre ugyanabban az erőforráscsoportban a parancsfájlok végrehajtásához és hibaelhárításához. Ezeket az erőforrásokat általában törli a parancsfájlszolgáltatás, amikor a központi telepítési parancsfájl végrehajtása terminálállapotba kerül. Az erőforrásokért az erőforrások törléséig számlázunk. További információ: [Karbantartás központi telepítési parancsfájl-erőforrások](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Előfeltételek

- **A felhasználó által hozzárendelt felügyelt identitás a közreműködő szerepkörét a cél erőforráscsoport.** Ez az identitás központi telepítési parancsfájlok végrehajtásához használatos. Az erőforráscsoporton kívüli műveletek végrehajtásához további engedélyeket kell megadnia. Ha például új erőforráscsoportot szeretne létrehozni, rendelje hozzá az identitást az előfizetési szinthez.

  > [!NOTE]
  > A központi telepítési parancsfájl motor létrehoz egy tárfiókot, és egy tárolópéldányt a háttérben.  A felhasználó által hozzárendelt felügyelt identitás a közreműködő szerepkör előfizetési szinten van szükség, ha az előfizetés nem regisztráltaz Azure storage-fiók (Microsoft.Storage) és az Azure-tárolópéldány (Microsoft.ContainerInstance) erőforrás-szolgáltatók.

  Identitás létrehozásához [lásd: Felhasználó által hozzárendelt felügyelt identitás létrehozása az Azure Portalon](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)keresztül , vagy az [Azure CLI használatával,](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)vagy [az Azure PowerShell használatával.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) A sablon telepítésekor szüksége van az identitásazonosítóra. Az identitás formátuma:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Használja a következő CLI- vagy PowerShell-parancsfájlt az azonosító leküzdéséhez az erőforráscsoport nevének és az identitás nevének megadásával.

  # <a name="cli"></a>[parancssori felület](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g jgaoidentity1008rg -n jgaouami --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure PowerShell** vagy **Azure CLI**. A támogatott Azure PowerShell-verziók listáját [itt](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)olvashatja; a támogatott Azure CLI-verziók listáját [itt](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)olvashatja.

    >[!IMPORTANT]
    > A központi telepítési parancsfájl a Microsoft Container Registry(MCR) rendszerből származó elérhető CLI-lemezképeket használja. Körülbelül egy hónapig tart a CLI-lemezkép központi telepítési parancsfájlhoz való hitelesítése. Ne használja a 30 napon belül kiadott CLI-verziókat. A képek megjelenési dátumának megkereséséhez olvassa el az [Azure CLI kiadási megjegyzések című témakört.](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest) Ha nem támogatott verziót használ, a hibaüzenet felsorolja a támogatott verziókat.

    A sablonok üzembe helyezéséhez nincs szükség ezekre a verziókra. De ezek a verziók szükségesek a központi telepítési parancsfájlok helyi teszteléséhez. Lásd: [Az Azure PowerShell-modul telepítése.](/powershell/azure/install-az-ps) Használhat egy előre konfigurált Docker-lemezképet.  Lásd: [Fejlesztői környezet konfigurálása](#configure-development-environment).

## <a name="sample-templates"></a>Példasablonok

A következő json egy példa.  A legújabb sablonséma [itt](/azure/templates/microsoft.resources/deploymentscripts)található.

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
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "[concat('-name ', parameters('name'))]",
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
> A példa demonstrációs céllal szolgál.  **scriptContent** és **primaryScriptUris** nem létezhet egymás mellett egy sablonban.

Az ingatlan értékének részletei:

- **Identitás**: A központi telepítési parancsfájlszolgáltatás egy felhasználó által hozzárendelt felügyelt identitást használ a parancsfájlok végrehajtásához. Jelenleg csak a felhasználó által hozzárendelt felügyelt identitás támogatott.
- **kind**: Adja meg a parancsfájl típusát. Jelenleg az Azure PowerShell és az Azure CLI-parancsfájlok támogatottak. Az értékek **az AzurePowerShell** és az **AzureCLI.**
- **forceUpdateTag**: A sablontelepítések közötti érték módosítása a központi telepítési parancsfájl újbóli végrehajtását kényszeríti. Használja a newGuid() vagy utcNow() függvényt, amelyet egy paraméter alapértelmezettértékeként kell beállítani. További információért olvassa el a [Parancsfájl többszöri futtatása témakört.](#run-script-more-than-once)
- **azPowerShellVersion**/**azCliVersion**: Adja meg a használni kívánt modulverziót. A támogatott PowerShell- és CLI-verziók listáját az Előfeltételek című témakörben [tetszetős ekkel egészül ki.](#prerequisites)
- **argumentumok**: Adja meg a paraméterértékeket. Az értékeket szóközök választják el egymástól.
- **environmentVariables**: Adja meg a környezeti változók átad a parancsfájlnak. További információt a Telepítési parancsfájlok fejlesztése című [témakörben talál.](#develop-deployment-scripts)
- **scriptContent**: Adja meg a parancsfájl tartalmát. Külső parancsfájl futtatásához `primaryScriptUri` használja helyette. Példák: [Szövegközi parancsfájl használata](#use-inline-scripts) és [Külső parancsfájl használata.](#use-external-scripts)
- **primaryScriptUri**: Adjon meg egy nyilvánosan elérhető URL-címet az elsődleges központi telepítési parancsfájl támogatott fájlkiterjesztésekkel.
- **supportingScriptUris**: Adjon meg egy tömbnyi nyilvánosan elérhető URL-t a vagy `ScriptContent` a programban `PrimaryScriptUri`megnevezett támogató fájlokhoz.
- **időtúltöltés**: Adja meg az [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601)megadott maximálisan engedélyezett parancsfájl-végrehajtási időt. Az alapértelmezett **érték: P1D**.
- **tisztításI pszt.** Adja meg a központi telepítési erőforrások tisztításának preferenciáját, amikor a parancsfájl végrehajtása terminálállapotba kerül. Az alapértelmezett beállítás **mindig**, ami azt jelenti, hogy a terminálállapot ellenére törli az erőforrásokat (sikeres, sikertelen, megszakítva). További információ: [Karbantartás központi telepítési parancsfájl-erőforrások](#clean-up-deployment-script-resources).
- **retentionInterval**: Adja meg azt az időközt, amelyalatt a szolgáltatás megtartja a központi telepítési parancsfájl erőforrásait, miután a központi telepítési parancsfájl végrehajtása elérte a terminálállapotot. A központi telepítési parancsfájl erőforrásai törlődnek, amikor ez az időtartam lejár. Az időtartam az [ISO 8601 mintán](https://en.wikipedia.org/wiki/ISO_8601)alapul. Az alapértelmezett érték **a P1D**, ami hét napot jelent. Ez a tulajdonság akkor használatos, ha a cleanupPreference beállítása *OnExpiration*. Az *OnExpiration* tulajdonság jelenleg nincs engedélyezve. További információ: [Karbantartás központi telepítési parancsfájl-erőforrások](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>További minták

- [tanúsítvány létrehozása és hozzárendelése a kulcstartóhoz](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [hozzon létre és rendeljen hozzá egy felhasználó által hozzárendelt felügyelt identitást egy erőforráscsoporthoz, és futtasson egy központi telepítési parancsfájlt.](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json)

> [!NOTE]
> Javasoljuk, hogy hozzon létre egy felhasználó által hozzárendelt identitást, és adja meg az engedélyeket előre. Előfordulhat, hogy bejelentkezési és engedélyekkel kapcsolatos hibákat kap, ha az identitást és a engedélyeket ugyanabban a sablonban hozza létre, ahol a központi telepítési parancsfájlokat futtatja. Beletelik egy kis időbe, amíg az engedélyek hatályba lépnek.

## <a name="use-inline-scripts"></a>Szövegközi parancsfájlok használata

A következő sablon hoz egy `Microsoft.Resources/deploymentScripts` erőforrást a típussal. A kiemelt rész a szövegközi parancsfájl.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Mivel a szövegközi központi telepítési parancsfájlok idézőjelek közé vannak zárva, a központi telepítési parancsfájlokban lévő karakterláncokat inkább idézőjelek közé kell tenni. A PowerShell escape karaktere **&#92;. ** A karakterlánc-helyettesítést is érdemes használnia, ahogy az az előző JSON-mintában látható. Tekintse meg a névparaméter alapértelmezett értékét.

A parancsfájl egy paramétert vesz igénybe, és adja ki a paraméter értékét. **A DeploymentScriptOutputs** a kimenetek tárolására szolgál.  A kimenetek szakaszban az **értéksor** bemutatja, hogyan lehet elérni a tárolt értékeket. `Write-Output`hibakeresésre szolgál. A kimeneti fájl eléréséről a [Debug telepítési parancsfájlok](#debug-deployment-scripts)című témakörben olvashat.  A tulajdonságleírások a [Mintasablonok című témakörben találhatók.](#sample-templates)

A parancsfájl futtatásához válassza a **Próbálja ki** a Cloud Shell megnyitásához lehetőséget, majd illessze be a következő kódot a rendszerhéj ablaktáblájába.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

A kimenet a következőképpen fog kinézni:

![Erőforrás-kezelő sablon telepítési parancsfájlhello világ kimenete](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Külső parancsfájlok használata

A szövegközi parancsfájlok mellett külső parancsfájlokat is használhat. Csak a **ps1** fájlkiterjesztéssel rendelkező elsődleges PowerShell-parancsfájlok támogatottak. Cli-parancsfájlok esetén az elsődleges parancsfájlok bármilyen kiterjesztéssel rendelkezhetnek (vagy kiterjesztés nélkül), feltéve, hogy a parancsfájlok érvényes bash parancsfájlok. A külső parancsfájlok `scriptContent` használatához `primaryScriptUri`cserélje le a programra. Például:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Egy példa megtekintéséhez válassza [az itt](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)lehetőséget.

A külső parancsfájloknak elérhetőnek kell lenniük.  Az Azure storage-fiókokban tárolt parancsfájlok védelméről az [Oktatóanyag: Biztonságos összetevők az Azure Resource Manager-sablontelepítésekben.](./template-tutorial-secure-artifacts.md)

Ön felelős a központi telepítési parancsfájl által hivatkozott parancsfájlok integritásának biztosításáért, akár **PrimaryScriptUri,** akár **SupportingScriptUris.**  Csak olyan parancsfájlokra hivatkozzon, amelyekben megbízik.

## <a name="use-supporting-scripts"></a>Támogató parancsfájlok használata

A bonyolult logika egy vagy több segédparancsfájlra bontható. A `supportingScriptURI` tulajdonság lehetővé teszi, hogy szükség esetén uri-k tömbjét biztosítsa a támogató parancsfájlok számára:

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

A támogató parancsfájlok szövegközi és elsődleges parancsfájlokból is meghívhatók. A támogató parancsfájlok nem korlátozzák a fájlkiterjesztést.

A segédfájlok at a hogyscripts/azscriptinput futásidőben. Relatív elérési úttal hivatkozhat a szövegközi parancsfájlokból és az elsődleges parancsfájlokból származó segédfájlokra.

## <a name="work-with-outputs-from-powershell-script"></a>PowerShell-parancsfájl kimeneteinek munkája

A következő sablon bemutatja, hogyan lehet értékeket átadni két deploymentScripts-erőforrás között:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

Az első erőforrásban definiáljon egy **$DeploymentScriptOutputs**nevű változót, és a kimeneti értékek tárolására használja. A sablonon belüli másik erőforrás kimeneti értékének eléréséhez használja a következőket:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Cli-parancsfájl kimeneteivel dolgozhat

A PowerShell központi telepítési parancsfájljától eltérően a CLI/bash támogatás nem tesz elérhetővé egy közös változót a parancsfájlkimenetek tárolásához, hanem van egy **AZ_SCRIPTS_OUTPUT_PATH** nevű környezeti változó, amely tárolja a helyet, ahol a parancsfájl kimenetek fájl található. Ha egy központi telepítési parancsfájl t egy Resource Manager-sablonból futtat, ezt a környezeti változót a Bash rendszerhéj automatikusan beállítja.

A központi telepítési parancsfájlkimeneteket a AZ_SCRIPTS_OUTPUT_PATH helyre kell menteni, és a kimeneteknek érvényes JSON-karakterlánc-objektumnak kell lenniük. A fájl tartalmát kulcs-érték párként kell menteni. A karakterláncok tömbje például { "MyResult" néven van tárolva: [ "foo", "bar"] }.  Csak a tömberedmények tárolása , például [ "foo", "bar" ], érvénytelen.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[jq-t](https://stedolan.github.io/jq/) használnak az előző mintában. Jön a konténer képeket. Lásd: [Fejlesztői környezet konfigurálása](#configure-development-environment).

## <a name="develop-deployment-scripts"></a>Központi telepítési parancsfájlok fejlesztése

### <a name="handle-non-terminating-errors"></a>A nem megszüntetési hibák kezelése

Szabályozhatja, hogy a PowerShell hogyan reagáljon a nem végződő hibákra a [**$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) változó használatával a központi telepítési parancsfájlban. A központi telepítési parancsfájl motor javunkra nem állítja be/módosítja az értéket.  Annak ellenére, hogy a $ErrorActionPreference beállított érték, a központi telepítési parancsfájl az erőforrás-kiépítési állapotsikertelenre *állítja,* ha a parancsfájl hibát észlel.

### <a name="pass-secured-strings-to-deployment-script"></a>Biztonságos karakterláncok áthelyezése a központi telepítési parancsfájlnak

Környezeti változók (EnvironmentVariable) beállítása a tárolópéldányokban lehetővé teszi, hogy a tároló által futtatott alkalmazás vagy parancsfájl dinamikus konfigurációját biztosítsa. A központi telepítési parancsfájl ugyanúgy kezeli a nem biztonságos és biztonságos környezeti változókat, mint az Azure Container Instance. További információ: [Környezeti változók beállítása tárolópéldányokban.](../../container-instances/container-instances-environment-variables.md#secure-values)

## <a name="debug-deployment-scripts"></a>Telepítési parancsfájlok hibakeresése

A parancsfájlszolgáltatás létrehoz egy [tárfiókot](../../storage/common/storage-account-overview.md) és egy [tárolópéldányt](../../container-instances/container-instances-overview.md) a parancsfájl-végrehajtáshoz. Mindkét erőforrás rendelkezik az **azscriptek** utótag az erőforrás nevét.

![Az Erőforrás-kezelő sablon telepítési parancsfájljának erőforrásnevei](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

A felhasználói parancsfájl, a végrehajtási eredmények és a stdout fájl a tárfiók fájlmegosztásaiban tárolódnak. Van egy **azscripts**nevű mappa. A mappában még két mappa található a bemeneti és a kimeneti fájlokhoz: **azscriptinput** és **azscriptoutput**.

A kimeneti mappa **egy executionresult.json** fájlt és a parancsfájl kimeneti fájlját tartalmazza. Láthatjuk a parancsfájl végrehajtási hibaüzenet **et executionresult.json**. A kimeneti fájl csak akkor jön létre, ha a parancsfájl végrehajtása sikeres. A bemeneti mappa egy rendszer PowerShell-parancsfájlt és a felhasználói központi telepítési parancsfájlokat tartalmazza. Lecserélheti a felhasználói központi telepítési parancsfájlt egy módosítottra, és újrafuttathatja a központi telepítési parancsfájlt az Azure-tárolópéldányból.

A központi telepítési parancsfájl erőforrás-telepítési információit az erőforráscsoport és az előfizetés szintjén szerezheti be a REST API használatával:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

A következő példa [az ARMClient programot](https://github.com/projectkudu/ARMClient)használja:

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

Az eredmény az alábbihoz hasonlóan fog kinézni:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-status.json" range="1-37" highlight="15,34":::

A kimenet a központi telepítési állapotot és a központi telepítési parancsfájl erőforrás-azonosítóit jeleníti meg.

A következő REST API a naplót adja vissza:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Csak a központi telepítési parancsfájl-erőforrások törlése előtt működik.

A deploymentScripts erőforrás t a portálon való megtekintéséhez válassza a **Rejtett típusok megjelenítése**lehetőséget:

![Erőforrás-kezelő sablon telepítési parancsfájlja, rejtett típusok megjelenítése, portál](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Központi telepítési parancsfájl-erőforrások karbantartása

A központi telepítési parancsfájl létrehoz egy tárfiókot és egy tárolópéldányt, amely központi telepítési parancsfájlok végrehajtásához és a hibakeresési adatok tárolásához használatos. Ez a két erőforrás ugyanabban az erőforráscsoportban jön létre, mint a kiépített erőforrások, és a parancsfájl lejáratakor a parancsfájl törli őket. Ezeknek az erőforrásoknak az életciklusát szabályozhatja.  Amíg nem törli őket, mindkét erőforrásért díjat kell fizetnie. Az árra vonatkozó információkért lásd: [Tárolópéldányok díjszabása](https://azure.microsoft.com/pricing/details/container-instances/) és [az Azure Storage díjszabása.](https://azure.microsoft.com/pricing/details/storage/)

Ezeknek az erőforrásoknak az életciklusát a sablonban a következő tulajdonságok szabályozzák:

- **cleanupPreference**: Törlési preferencia, amikor a parancsfájl végrehajtása terminálállapotba kerül.  A támogatott értékek a következők:

  - **Mindig**: Törölje az erőforrásokat, ha a parancsfájl végrehajtása terminálállapotba kerül. Mivel a deploymentScripts erőforrás továbbra is jelen lehet az erőforrások törlése után, a rendszerparancsfájl másolni fogja a parancsfájl végrehajtási eredményeit, például stdout, outputok, visszatérési érték, stb.
  - **OnSuccess**: Az erőforrások törlése csak akkor, ha a parancsfájl végrehajtása sikeres. Továbbra is elérheti az erőforrásokat, hogy megtalálja a hibakeresési információkat.
  - **OnExpiration**: Csak akkor törölje az erőforrásokat, ha a **megőrzési intervallum** beállítás lejárt. Ez a tulajdonság jelenleg le van tiltva.

- **retentionInterval**: Adja meg azt az időtartamot, amely alatt egy parancsfájl-erőforrás megmarad, majd ezután lejár és törlődik.

> [!NOTE]
> Nem ajánlott a központi telepítési parancsfájl erőforrásait más célokra használni.

## <a name="run-script-more-than-once"></a>Parancsfájl többszöri futtatása

A központi telepítési parancsfájl végrehajtása idempotens művelet. Ha a deploymentScripts erőforrás-tulajdonságai közül egyik sem módosul (beleértve a szövegközi parancsfájlt is), a parancsfájl nem lesz végrehajtva a sablon újratelepítésekor. A központi telepítési parancsfájlszolgáltatás összehasonlítja a sablonban lévő erőforrásneveket az ugyanabban az erőforráscsoportban lévő meglévő erőforrásokkal. Két lehetőség van, ha ugyanazt a központi telepítési parancsfájlt többször szeretné végrehajtani:

- Módosítsa a deploymentScripts erőforrás nevét. Például használja az [utcNow](./template-functions-date.md#utcnow) sablon függvényt erőforrásnévként vagy az erőforrás nevének részeként. Az erőforrásnév módosítása új deploymentScripts erőforrást hoz létre. Ez jó vezetése története script végrehajtás.

    > [!NOTE]
    > Az utcNow függvény csak egy paraméter alapértelmezett értékében használható.

- Adjon meg egy `forceUpdateTag` másik értéket a sablon tulajdonságban.  Például használja utcNow értékként.

> [!NOTE]
> Írja meg az idempotens központi telepítési parancsfájlokat. Ez biztosítja, hogy ha véletlenül újra futnak, az ne okozzon rendszerváltozásokat. Ha például a központi telepítési parancsfájlt egy Azure-erőforrás létrehozásához használják, ellenőrizze, hogy az erőforrás nem létezik-e a létrehozása előtt, így a parancsfájl sikeres lesz, vagy nem hozza létre újra az erőforrást.

## <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

A központi telepítési parancsfájl-fejlesztési környezetben egy előre konfigurált docker-tárolórendszerképet használhat. Az alábbi eljárás bemutatja, hogyan konfigurálhatja a docker-lemezképet Windows rendszeren. A Linux és a Mac, megtalálja az információkat az interneten.

1. Telepítse a [Docker Desktop-ot](https://www.docker.com/products/docker-desktop) a fejlesztői számítógépre.
1. Nyissa meg a Docker Desktop alkalmazást.
1. Válassza a Feladatsorok Docker Desktop ikonját, majd a **Beállítások**lehetőséget.
1. Válassza **a Megosztott meghajtók**lehetőséget, jelölje ki azt a helyi meghajtót, amelyet elérhetővé szeretne tenni a tárolók számára, majd válassza **az Alkalmaz lehetőséget.**

    ![Az Erőforrás-kezelő sablon telepítési parancsfájljának docker-meghajtója](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Adja meg a Windows hitelesítő adatait a parancssorba.
1. Nyisson meg egy terminálablakot, vagy parancssort vagy Windows PowerShell (Ne használja a PowerShell ISE-t).
1. A központi telepítési parancsfájl tárolórendszerképének lekérése a helyi számítógépre:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    A példa a PowerShell 2.7.0-s verzióját használja.

    CLI-lemezkép lekérése Microsoft Container Registry (MCR) rendszerből:

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    Ez a példa a CLI 2.0.80 verziót használja. A központi telepítési parancsfájl az [itt](https://hub.docker.com/_/microsoft-azure-cli)található alapértelmezett CLI-tárolórendszerképeket használja.

1. Futtassa a docker-lemezképet helyileg.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Cserélje le ** &lt;az állomásillesztőprogram>** és ** &lt;a gazdakönyvtár nevét>** egy meglévő mappára a megosztott meghajtón.  A mappát a tárolóban lévő **/data** mappához rendeli hozzá. Példák: D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **- ez azt jelenti, hogy** életben kell tartani a konténer képét.

    Példa a CLI-re:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. A kérdés kérésekor válassza **a Megosztás lehetőséget.**
1. A következő képernyőkép bemutatja, hogyan futtatható a PowerShell-parancsfájl, mivel van egy helloworld.ps1 fájl d:\docker mappában.

    ![Az Erőforrás-kezelő sablon központi telepítési parancsfájljának docker-cmd-je](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

A parancsfájl sikeres tesztelése után használhatja központi telepítési parancsfájlként.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan használhatja a központi telepítési parancsfájlokat. Útmutató egy központi telepítési parancsfájl oktatóanyagát:

> [!div class="nextstepaction"]
> [Oktatóanyag: Központi telepítési parancsfájlok használata az Azure Resource Manager-sablonokban](./template-tutorial-deployment-script.md)