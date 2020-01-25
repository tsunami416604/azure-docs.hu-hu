---
title: Sablon-telepítési parancsfájlok használata | Microsoft Docs
description: Ismerje meg, hogyan használhatók a központi telepítési parancsfájlok Azure Resource Manager-sablonokban.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/09/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 450ebf0ce05d11e27639dff1cf9da821b10ab6a1
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710689"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>Oktatóanyag: az üzembe helyezési parancsfájlok használata önaláírt tanúsítvány létrehozásához (előzetes verzió)

Megtudhatja, hogyan használhatja az üzembe helyezési parancsfájlokat az Azure-erőforrások kezelése sablonban. Az üzembe helyezési parancsfájlok olyan egyéni lépések elvégzésére használhatók, amelyek nem hajthatók végre Resource Manager-sablonok használatával. Például hozzon létre egy önaláírt tanúsítványt.  Ebben az oktatóanyagban létrehoz egy sablont az Azure Key Vault üzembe helyezéséhez, majd egy sablonban egy `Microsoft.Resources/deploymentScripts` erőforrást használ egy tanúsítvány létrehozásához, majd hozzáadja a tanúsítványt a kulcstartóhoz. További információ az üzembe helyezési parancsfájlról: [telepítési parancsfájlok használata Azure Resource Manager-sablonokban](./deployment-script-template.md).

> [!NOTE]
> Az üzembe helyezési parancsfájl jelenleg előzetes verzióban érhető el. A használatához regisztrálnia kell [az előzetes](https://aka.ms/armtemplatepreviews)verzióra.

> [!IMPORTANT]
> A parancsfájlok végrehajtásához és a hibaelhárításhoz két üzembe helyezési parancsfájl-erőforrás, egy Storage-fiók és egy tároló-példány jön létre ugyanabban az erőforráscsoporthoz. Ezeket az erőforrásokat általában a parancsfájl-szolgáltatás törli, ha a parancsfájl végrehajtása terminál állapotba kerül. Az erőforrások számlázása az erőforrások törlése után történik. További információ: [üzembe helyezési parancsfájl erőforrásainak tisztítása](./deployment-script-template.md#clean-up-deployment-script-resources).

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * A sablon szerkesztése
> * A sablon üzembe helyezése
> * A sikertelen parancsfájl hibakeresése
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* **[Visual Studio Code](https://code.visualstudio.com/) a Resource Manager-eszközök bővítménnyel**. További információ: [Azure Resource Manager sablonok létrehozása a Visual Studio Code használatával](./use-vs-code-to-create-template.md).

* **Felhasználó által hozzárendelt felügyelt identitás a közreműködő szerepkörrel az előfizetés szintjén**. Ez az identitás az üzembe helyezési parancsfájlok végrehajtásához használatos. A létrehozáshoz tekintse meg a [felhasználó által hozzárendelt felügyelt identitást](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity). A sablon üzembe helyezésekor szüksége lesz az azonosító AZONOSÍTÓra. Az identitás formátuma:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Használja az alábbi PowerShell-szkriptet az azonosító beszerzéséhez az erőforráscsoport nevének és az identitás nevének megadásával.

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Teljesen új sablon létrehozása helyett megnyithat egy sablont az [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) közül. Az Azure Gyorsindítás sablonjai a Resource Manager-sablonok tárháza.

Az ebben a rövid útmutatóban használt sablon [létrehozásához Azure Key Vault és titkos kulcsot](https://azure.microsoft.com/resources/templates/101-key-vault-create/)kell meghívni. A sablon létrehoz egy kulcstartót, majd hozzáadja a titkos kulcsot a kulcstartóhoz.

1. A Visual Studio Code-ban válassza a **File** (Fájl) >**Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. A **Fájl**>**Mentés másként** elem kiválasztásával mentse a fájlt a helyi számítógépre **azuredeploy.json** néven.

## <a name="edit-the-template"></a>A sablon szerkesztése

Végezze el a következő módosításokat a sablonon:

### <a name="clean-up-the-template-optional"></a>A sablon törlése (nem kötelező)

Az eredeti sablon egy titkos kulcsot helyez el a kulcstartóhoz.  Az oktatóanyag leegyszerűsítése érdekében távolítsa el a következő erőforrást:

* **Microsoft. kulcstartó/tárolók/titkos kódok**

Távolítsa el a következő két paraméter-definíciót:

* **secretName**
* **secretValue**

Ha úgy dönt, hogy nem távolítja el ezeket a definíciókat, meg kell adnia a paraméter értékeit a telepítés során.

### <a name="configure-the-key-vault-access-policies"></a>A Key Vault hozzáférési házirendjeinek konfigurálása

Az üzembe helyezési parancsfájl hozzáadja a tanúsítványt a kulcstartóhoz. Konfigurálja a Key Vault hozzáférési szabályzatait, hogy engedélyt adjanak a felügyelt identitásnak:

1. Adjon hozzá egy paramétert a felügyelt azonosító AZONOSÍTÓjának beolvasásához:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > A Visual Studio Code Resource Manager-sablon bővítménye nem képes még a telepítési parancsfájlok formázására. Ne használja a [SHIFT] + [ALT] + F billentyűkombinációt a deploymentScripts-erőforrások formázásához, például a következőhöz.

1. Adjon hozzá egy paramétert a Key Vault hozzáférési házirendjeinek konfigurálásához, hogy a felügyelt identitás hozzá tudja adni a tanúsítványokat a kulcstartóhoz.

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

1. A meglévő Key Vault-hozzáférési házirendek frissítése a következőre:

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

    Két házirend van definiálva, egyet a bejelentkezett felhasználó számára, a másik pedig a felügyelt identitáshoz.  A bejelentkezett felhasználónak csak a *listázási* engedélyre van szüksége az üzemelő példány ellenőrzéséhez.  Az oktatóanyag leegyszerűsítése érdekében ugyanaz a tanúsítvány van hozzárendelve a felügyelt identitáshoz és a bejelentkezett felhasználókhoz is.

### <a name="add-the-deployment-script"></a>Az üzembe helyezési parancsfájl hozzáadása

1. Adja hozzá az üzembehelyezési parancsfájl által használt három paramétert.

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

1. Add a deploymentScripts resource:

    > [!NOTE]
    > Because the inline deployment scripts are enclosed in double quotes, the strings inside the deployment scripts need to be enclosed in single quotes instead. The escape character for PowerShell is **&#92;**.

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
        "azPowerShellVersion": "2.8",
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

    A `deploymentScripts` erőforrás a Key Vault erőforrástól és a szerepkör-hozzárendelési erőforrástól függ.  Ezek a tulajdonságok a következő tulajdonságokkal rendelkeznek:

    * **identitás**: az üzembe helyezési parancsfájl felhasználó által hozzárendelt felügyelt identitást használ a parancsfájlok végrehajtásához.
    * **Típus: adja**meg a parancsfájl típusát. Jelenleg csak a PowerShell-szkriptek támogatottak.
    * **forceUpdateTag**: annak meghatározása, hogy a telepítési parancsfájlt akkor is kell-e végrehajtani, ha a parancsfájl forrása nem módosult. Lehet aktuális időbélyeg vagy GUID. További információ: [parancsfájl futtatása](./deployment-script-template.md#run-script-more-than-once)többször.
    * **azPowerShellVersion**: a használni kívánt Azure PowerShell modul verziószámát adja meg. Az üzembe helyezési parancsfájl jelenleg a 2.7.0, a 2.8.0 és a 3.0.0 verziót támogatja.
    * **időtúllépés**: adja meg az [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601)megadott maximálisan engedélyezett parancsfájl-végrehajtási időt. Az alapértelmezett érték a **P1D**.
    * **argumentumok**: határozza meg a paraméterek értékeit. Az értékeket szóközök választják el egymástól.
    * **scriptContent**: adja meg a parancsfájl tartalmát. Külső parancsfájl futtatásához használja helyette a **primaryScriptURI** . További információ: [külső parancsfájl használata](./deployment-script-template.md#use-external-scripts).
        **$DeploymentScriptOutputs** deklarálása csak akkor szükséges, ha a parancsfájlt egy helyi gépen teszteli. A változó deklarálása lehetővé teszi, hogy a parancsfájl egy helyi gépen és egy deploymentScript-erőforráson fusson anélkül, hogy módosítani kellene. Az $DeploymentScriptOutputshoz rendelt érték az üzemelő példányokban kimenetként érhető el. További információ: [az üzembehelyezési szkriptek kimenetének használata](./deployment-script-template.md#work-with-outputs-from-deployment-scripts).
    * **cleanupPreference**: adja meg a telepítési parancsfájl erőforrásainak törlésére vonatkozó beállítást.  Az alapértelmezett érték **mindig**, ami azt jelenti, hogy az üzembe helyezési parancsfájl erőforrásai törlődnek a terminál állapota (sikeres, sikertelen, megszakított) ellenére. Ebben az oktatóanyagban a **OnSuccess** -t használjuk, így a szkriptek végrehajtási eredményei is megtekinthetők.
    * **retentionInterval**: adja meg azt az intervallumot, ameddig a szolgáltatás megőrzi a parancsfájl erőforrásait, miután elérte a terminál állapotát. Az erőforrások az adott időtartam lejárta után törlődnek. Az időtartam az ISO 8601 mintán alapul. Ez az oktatóanyag P1D használ, ami egy napot jelent.  Ezt a tulajdonságot akkor használja a rendszer, ha a **cleanupPreference** értéke **OnExpiration**. Ez a tulajdonság jelenleg nincs engedélyezve.

    Az üzembe helyezési parancsfájl három paramétert fogad: a kulcstároló nevét, a tanúsítvány nevét és a tulajdonos nevét.  Létrehoz egy tanúsítványt, majd hozzáadja a tanúsítványt a kulcstartóhoz.

    A **$DeploymentScriptOutputs** a kimeneti érték tárolására szolgál.  További információért lásd: [az üzembehelyezési szkriptek kimenetének használata](./deployment-script-template.md#work-with-outputs-from-deployment-scripts).

    Az elkészült sablon [itt](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)található.

1. A hibakeresési folyamat megtekintéséhez helyezzen el egy hibát a kódban a következő sor hozzáadásával az üzembe helyezési parancsfájlba:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    A megfelelő parancs a Write- **Output1**helyett az **írási kimenet** .

1. A fájl mentéséhez válassza a **Fájl**>**Mentés** lehetőséget.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A Cloud Shell megnyitásához és a sablon fájljának a rendszerhéjba való feltöltéséhez tekintse meg a [sablon telepítése](./quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template) című szakaszt a Visual Studio Code-ban. Ezután futtassa a következő PowerShell-parancsfájlt:

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

A telepítési parancsfájl-szolgáltatásnak további üzembe helyezési parancsfájl-erőforrásokat kell létrehoznia a parancsfájlok végrehajtásához. Az előkészítés és a tisztítás folyamata akár egy percet is igénybe vehet a parancsfájl végrehajtásának tényleges végrehajtási ideje mellett.

Az üzembe helyezés nem sikerült, mert érvénytelen parancs, a **Write-Output1** a parancsfájlban van használatban. A következő hibaüzenetet kapja:

```error
The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
```

A telepítési parancsfájl végrehajtási eredménye a hibaelhárítási célból a telepítési parancsfájl erőforrásaiban tárolódik.

## <a name="debug-the-failed-script"></a>A sikertelen parancsfájl hibakeresése

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Nyissa meg az erőforráscsoportot. Ez a projekt neve, **RG** hozzáfűzéssel. Az erőforráscsoport két további erőforrást fog látni. Ezeket az erőforrásokat *telepítési parancsfájl-erőforrásoknak*nevezzük.

    ![Resource Manager-sablon üzembe helyezési parancsfájl erőforrásai](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Mindkét fájl **azscripts** utótaggal rendelkezik. Az egyik a Storage-fiók, a másik pedig egy tároló példány.

    Válassza a **rejtett típusok megjelenítése** lehetőséget a deploymentScripts-erőforrás listázásához.

1. Válassza ki a **azscripts** utótaggal rendelkező Storage-fiókot.
1. Válassza ki a **fájlmegosztás** csempét. Egy **azscripts** mappát kell látnia.  A mappa tartalmazza a telepítési parancsfájl végrehajtási fájljait.
1. Válassza a **azscripts**lehetőséget. Két **azscriptinput** és **azscriptoutput**kell látnia.  A bemeneti mappa egy PowerShell-parancsfájlt és a felhasználói telepítési parancsfájlokat tartalmaz. A kimeneti mappa egy **executionresult. JSON** fájlt és a parancsfájl kimeneti fájlját tartalmazza. A hibaüzenetet a **executionresult. JSON**fájlban tekintheti meg. A kimeneti fájl nem létezik, mert a végrehajtás nem sikerült.

Távolítsa el a **Write-Output1** sort, és telepítse újra a sablont.

A második központi telepítés sikeres futtatásakor a szkript szolgáltatás eltávolítja a telepítési parancsfájl erőforrásait, mivel a **cleanupPreference** tulajdonság értéke **OnSuccess**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja az üzembe helyezési parancsfájlt Azure Resource Manager-sablonokban. Az Azure-erőforrások feltételek alapján való üzembe helyezésével kapcsolatban lásd:

> [!div class="nextstepaction"]
> [Feltételek használata](./template-tutorial-use-conditions.md)
