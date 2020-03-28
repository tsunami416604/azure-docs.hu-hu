---
title: Sablontelepítési parancsfájlok használata | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja a központi telepítési parancsfájlokat az Azure Resource Manager-sablonokban.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 94b351ddb18ca596f47e8ef40cff8229c838d7bd
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239211"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>Oktatóanyag: Üzembe helyezési parancsfájlok használatával önaláírt tanúsítványt hozhat létre (előzetes verzió)

Ismerje meg, hogyan használhatja a központi telepítési parancsfájlokat az Azure Resource Manage (ARM) sablonokban. A központi telepítési parancsfájlok olyan egyéni lépések végrehajtására használhatók, amelyeket az ARM-sablonok nem hajthatnak végre. Például önaláírt tanúsítvány létrehozása.  Ebben az oktatóanyagban hozzon létre egy sablont egy `Microsoft.Resources/deploymentScripts` Azure-kulcstartó üzembe helyezéséhez, majd ugyanabban a sablonban lévő erőforrás használatával hozzon létre egy tanúsítványt, majd adja hozzá a tanúsítványt a key vaulthoz. A központi telepítési parancsfájlról a [Központi telepítési parancsfájlok használata ARM-sablonokban (Arm-sablonok) (Központi telepítési parancsfájlok használata ARM-sablonokban) témakörben olvashat bővebben.](./deployment-script-template.md)

> [!IMPORTANT]
> Két központi telepítési parancsfájl-erőforrás, egy tárfiók és egy tárolópéldány jön létre ugyanabban az erőforráscsoportban a parancsfájlok végrehajtásához és hibaelhárításához. Ezeket az erőforrásokat általában törli a parancsfájlszolgáltatás, amikor a parancsfájl végrehajtása terminálállapotba kerül. Az erőforrásokért az erőforrások törléséig számlázunk. További információ: [Karbantartás központi telepítési parancsfájl-erőforrások](./deployment-script-template.md#clean-up-deployment-script-resources).

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * A sablon szerkesztése
> * A sablon üzembe helyezése
> * Hibakeresés a sikertelen parancsfájl
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* ** [Visual Studio-kód](https://code.visualstudio.com/) a Resource Manager Tools kiterjesztéssel.** Lásd: [A Visual Studio-kód használata ARM-sablonok létrehozásához.](./use-vs-code-to-create-template.md)

* **A felhasználó által hozzárendelt felügyelt identitás a közreműködő szerepkör előfizetési szinten**. Ez az identitás központi telepítési parancsfájlok végrehajtásához használatos. A létrehozáshoz olvassa el [a Felhasználó által hozzárendelt felügyelt identitás című témakört.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity) A sablon telepítésekor szüksége van az identitásazonosítóra. Az identitás formátuma:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Használja a következő PowerShell-parancsfájlt az azonosító leküzdéséhez az erőforráscsoport nevének és az identitás nevének megadásával.

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Teljesen új sablon létrehozása helyett megnyithat egy sablont az [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) közül. Az Azure quickstart sablonok egy tárház ARM sablonok.

A rövid útmutatóban használt sablon neve [Létrehoz egy Azure Key Vault és egy titkos.](https://azure.microsoft.com/resources/templates/101-key-vault-create/) A sablon létrehoz egy key vault, majd hozzáad egy titkos kulcsot a key vaulthoz.

1. A Visual Studio-kódból válassza a **Fájlmegnyitása**>**fájl**lehetőséget.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. Válassza a **Fájlmentés**>**másként** lehetőséget a fájl **azuredeploy.json néven** a helyi számítógépre történő mentéséhez.

## <a name="edit-the-template"></a>A sablon szerkesztése

Hajtsa végre a következő módosításokat a sablonon:

### <a name="clean-up-the-template-optional"></a>A sablon karbantartása (nem kötelező)

Az eredeti sablon egy titkos kulcsot ad a key vaulthoz.  Az oktatóanyag egyszerűsítése érdekében távolítsa el a következő erőforrást:

* **Microsoft.KeyVault/vaults/secrets**

Távolítsa el a következő két paraméterdefiníciót:

* **titkosnév**
* **titkosÉrték**

Ha úgy dönt, hogy nem távolítja el ezeket a definíciókat, meg kell adnia a paraméterértékeket a központi telepítés során.

### <a name="configure-the-key-vault-access-policies"></a>A key vault-hozzáférési házirendek konfigurálása

A központi telepítési parancsfájl egy tanúsítványt ad hozzá a key vaulthoz. Konfigurálja a key vault hozzáférési szabályzatokat, hogy a felügyelt identitás engedélyt adjon:

1. Adjon hozzá egy paramétert a felügyelt identitásazonosító leéséhez:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > A Visual Studio-kód Resource Manager-sablonbővítménye még nem képes a központi telepítési parancsfájlok formázására. Ne használja a [SHIFT]+[ALT]+F parancsot a deploymentScripts erőforrások formázásához, például az alábbiakat.

1. Adjon hozzá egy paramétert a key vault hozzáférési szabályzatok konfigurálásához, hogy a felügyelt identitás tanúsítványokat adhat hozzá a key vaulthoz.

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. Frissítse a meglévő kulcstartó-hozzáférési házirendeket:

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    Két házirend van definiálva, az egyik a bejelentkezett felhasználó, a másik pedig a felügyelt identitás.  A bejelentkezett felhasználónak csak a *lista* engedélyére van szüksége a központi telepítés ellenőrzéséhez.  Az oktatóanyag egyszerűsítése érdekében ugyanaz a tanúsítvány van hozzárendelve mind a felügyelt identitáshoz, mind a bejelentkezett felhasználókhoz.

### <a name="add-the-deployment-script"></a>A központi telepítési parancsfájl hozzáadása

1. Adjon hozzá három paramétert, amelyeket a központi telepítési parancsfájl használ.

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }
    ```

1. DeploymentScript-erőforrás hozzáadása:

    > [!NOTE]
    > Mivel a szövegközi központi telepítési parancsfájlok idézőjelek közé vannak zárva, a központi telepítési parancsfájlokban lévő karakterláncokat inkább idézőjelek közé kell tenni. A PowerShell escape karaktere **&#92;. **

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2019-10-01-preview",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "3.0",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an argument string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    Az `deploymentScripts` erőforrás a key vault-erőforrástól és a szerepkör-hozzárendelési erőforrástól függ.  Ez a következő tulajdonságokkal rendelkezik:

    * **identity**: A központi telepítési parancsfájl egy felhasználó által hozzárendelt felügyelt identitást használ a parancsfájlok végrehajtásához.
    * **kind**: Adja meg a parancsfájl típusát. Jelenleg csak a PowerShell-parancsfájl támogatott.
    * **forceUpdateTag**: Határozza meg, hogy a központi telepítési parancsfájlt végre kell-e hajtani, még akkor is, ha a parancsfájl forrása nem változott. Lehet aktuális időbélyegző vagy GUID. További információért olvassa el a [Parancsfájl többszöri futtatása témakört.](./deployment-script-template.md#run-script-more-than-once)
    * **azPowerShellVersion:** Megadja az Azure PowerShell-modul használandó verzióját. A központi telepítési parancsfájl jelenleg a 2.7.0- s, a 2.8.0-s és a 3.0.0-s verziót támogatja.
    * **időtúltöltés**: Adja meg az [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601)megadott maximálisan engedélyezett parancsfájl-végrehajtási időt. Az alapértelmezett **érték: P1D**.
    * **argumentumok**: Adja meg a paraméterértékeket. Az értékeket szóközök választják el egymástól.
    * **scriptContent**: Adja meg a parancsfájl tartalmát. Külső parancsfájl futtatásához használja inkább **a primaryScriptURI-t.** További információ: [Külső parancsfájl használata](./deployment-script-template.md#use-external-scripts).
        Deklarálása **$DeploymentScriptOutputs** csak akkor szükséges, ha a parancsfájl tesztelése a helyi számítógépen. A változó deklarálása lehetővé teszi, hogy a parancsfájl egy helyi gépen és egy deploymentScript-erőforrásban fusson, anélkül, hogy módosításokat kellene végrehajtania. A $DeploymentScriptOutputs-hez rendelt érték kimenetként érhető el a központi telepítésekben. További információ: [A PowerShell központi telepítési parancsfájljaiból származó kimenetek megmunkálása](./deployment-script-template.md#work-with-outputs-from-powershell-script) vagy [a CLI központi telepítési parancsfájljaiból származó kimenetek alkalmazása.](./deployment-script-template.md#work-with-outputs-from-cli-script)
    * **cleanupPreference**: Adja meg a központi telepítési parancsfájl-erőforrások törlésének beállítását.  Az alapértelmezett érték **mindig**, ami azt jelenti, hogy a központi telepítési parancsfájl erőforrásai törlődnek a terminálállapot ellenére (Sikeres, Sikertelen, Megszakítva). Ebben az oktatóanyagban az **OnSuccess** használatos, így lehetősége van a parancsfájl végrehajtási eredményeinek megtekintésére.
    * **retentionInterval**: Adja meg azt az időközt, amelyalatt a szolgáltatás megtartja a parancsfájl-erőforrásokat, miután elérte a terminálállapotot. Az erőforrások törlődnek, amikor ez az időtartam lejár. Az időtartam az ISO 8601 mintán alapul. Ez az oktatóanyag p1D-t használ, ami egy napot jelent.  Ez a tulajdonság akkor használatos, ha a **cleanupPreference** beállítása **OnExpiration**. Ez a tulajdonság jelenleg nincs engedélyezve.

    A központi telepítési parancsfájl három paramétert vesz igénybe: a kulcstároló nevét, a tanúsítvány nevét és a tulajdonos nevét.  Létrehoz egy tanúsítványt, majd hozzáadja a tanúsítványt a key vaulthoz.

    **$DeploymentScriptOutputs** a kimeneti érték tárolására szolgál.  További információ: [A PowerShell központi telepítési parancsfájljaiból származó kimenetek alkalmazása](./deployment-script-template.md#work-with-outputs-from-powershell-script) vagy [a CLI központi telepítési parancsfájljaiból származó kimenetek alkalmazása.](./deployment-script-template.md#work-with-outputs-from-cli-script)

    Az elkészült sablon [itt](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)található.

1. A hibakeresési folyamat megtekintéséhez helyezzen el egy hibát a kódban a következő sor hozzáadásával a központi telepítési parancsfájlhoz:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    A helyes parancs a **Write-Output1** helyett a **Write-Output1**.

1. A fájl mentéséhez válassza a>**Fájlmentés** lehetőséget. **File**

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Tekintse meg [a sablon telepítése](./quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template) szakasza a Visual Studio-kód rövid útmutató a felhőbeli rendszerhéj megnyitásához, és töltse fel a sablonfájlt a rendszerhéjba. Ezután futtassa a következő PowerShell-parancsfájlt:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
$identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$resourceGroupName = "${projectName}rg"
$keyVaultName = "${projectName}kv"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

Write-Host "Press [ENTER] to continue ..."
```

A központi telepítési parancsfájlszolgáltatásnak további központi telepítési parancsfájl-erőforrásokat kell létrehoznia a parancsfájlok végrehajtásához. Az előkészítés és a tisztítási folyamat a parancsfájl tényleges végrehajtási ideje mellett akár egy percet is igénybe vehet.

A központi telepítés sikertelen, mert az érvénytelen parancs, **Write-Output1** a parancsfájlban használatos. Meg kell kapnia egy hiba mondván:

```error
The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
```

A központi telepítési parancsfájl végrehajtási eredménye a központi telepítési parancsfájl erőforrásaiban tárolja a hibaelhárításcéljából.

## <a name="debug-the-failed-script"></a>Hibakeresés a sikertelen parancsfájl

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Nyissa meg az erőforráscsoportot. Ez a projekt neve **rg** csatolt. Az erőforráscsoportban két további erőforrás jelenik meg. Ezeket az erőforrásokat *központi telepítési parancsfájl-erőforrásoknak nevezzük.*

    ![Az Erőforrás-kezelő sablon telepítési parancsfájljának erőforrásai](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Mindkét fájl rendelkezik az **azscripts** utótaggal. Az egyik egy tárfiók, a másik pedig egy tárolópéldány.

    Válassza **a Rejtett típusok megjelenítése** lehetőséget a deploymentScripts erőforrás listázásához.

1. Válassza ki a tárfiókot az **azscriptek** utótag.
1. Válassza a **Fájlmegosztáscsempe** lehetőséget. Látni fog egy **azscripts mappát.**  A mappa tartalmazza a központi telepítési parancsfájl-végrehajtási fájlokat.
1. Válassza az **azscripts**lehetőséget . Két mappaJelenik megL **azscriptinput** és **azscriptoutput**.  A bemeneti mappa egy rendszer PowerShell-parancsfájlt és a felhasználói központi telepítési parancsfájlokat tartalmazza. A kimeneti mappa **egy executionresult.json** fájlt és a parancsfájl kimeneti fájlját tartalmazza. A hibaüzenet a **executionresult.json**. A kimeneti fájl nincs ott, mert a végrehajtás nem sikerült.

Távolítsa el a **Write-Output1** sort, és telepítse újra a sablont.

A második központi telepítés sikeres futtatásakor a parancsfájlszolgáltatás nak el kell távolítania a központi telepítési parancsfájl erőforrásait, mert a **cleanupPreference** tulajdonság **OnSuccess**lesz állítva.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a központi telepítési parancsfájlt az ARM-sablonokban. Az Azure-erőforrások feltételek alapján való üzembe helyezésével kapcsolatban lásd:

> [!div class="nextstepaction"]
> [Feltételek használata](./template-tutorial-use-conditions.md)
