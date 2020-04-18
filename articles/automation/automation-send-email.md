---
title: E-mail küldése egy Azure Automation-runbookból
description: Ismerje meg, hogyan küldhet e-mailt a SendGrid segítségével egy runbookon belülről.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: d4b35458c76da82b33dfcb530cfdc71ee3da3bb6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604789"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Oktatóanyag: E-mail küldése egy Azure Automation-runbookból

E-mailt küldhet egy runbookról a [SendGrid](https://sendgrid.com/solutions) használatával a PowerShell használatával. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Hozzon létre egy Azure Key Vault.
> * Az `SendGrid` API-kulcs a key vaultban tárolja.
> * Hozzon létre egy újrafelhasználható runbookot, amely lekéri az API-kulcsot, és e-mailt küld az [Azure Key Vaultban](/azure/key-vault/)tárolt API-kulcs használatával.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei:

* Azure-előfizetés: Ha még nem rendelkezik ilyensel, [aktiválhatja az MSDN-előfizetői előnyöket,](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) vagy regisztrálhat egy [ingyenes fiókra.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [SendGrid-fiók létrehozása](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Automation-fiók](automation-offering-get-started.md) **az Az** modulokkal, és a [Futtatás másként kapcsolattal](automation-create-runas-account.md)a runbook tárolásához és végrehajtásához.

## <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása;

Azure Key Vault a következő PowerShell-parancsfájl használatával hozhat létre. Cserélje le a változóértékeket a környezetére jellemző értékekre. Használja a beágyazott Azure Cloud Shell segítségével a **Try It** gomb, található a jobb felső sarokban a kódblokk. A kódot is másolhatja és futtathatja helyileg, ha az [Azure PowerShell-modul](/powershell/azure/install-az-ps) telepítve van a helyi gépen.

> [!NOTE]
> Az API-kulcs beolvasásához kövesse a [SendGrid API-kulcs megkeresése](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key)című részben található lépéseket.

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Az Azure Key Vault létrehozásának és titkos fájlok tárolásának egyéb módjaiért tekintse meg a [Key Vault rövid útmutatói című témakört.](/azure/key-vault/)

## <a name="import-required-modules-to-your-automation-account"></a>A szükséges modulok importálása az Automation-fiókba

Az Azure Key Vault runbookon belüli használatához az Automation-fióknak a következő modulokra van szüksége:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Kattintson a **Telepítés az Azure Automation szolgáltatásba** elemre az Azure Automation lapon a Telepítési beállítások **csoportban.** Ez a művelet megnyitja az Azure Portalon. Az Importálás lapon jelölje ki az Automation-fiókot, és kattintson **az OK**gombra.

A szükséges modulok hozzáadásának további módszereit a Modulok importálása című [témakörben téssze](/azure/automation/shared-resources/modules#importing-modules)el.

## <a name="create-the-runbook-to-send-an-email"></a>A runbook létrehozása e-mail küldéséhez

Miután létrehozott egy key vaultot, és tárolta az `SendGrid` API-kulcsot, itt az ideje, hogy létrehozza a runbookot, amely lekéri az API-kulcsot, és e-mailt küld.

Ez a `AzureRunAsConnection` runbook [futfuttatási fiókként](automation-create-runas-account.md) használja a hitelesítést az Azure-ban az Azure Key Vault titkos kulcsának lekéréséhez.

Ebben a példában a **Send-GridMailMessage**nevű runbookot hozhat létre. Módosíthatja a PowerShell-parancsfájlt, és újra felhasználhatja azt a különböző forgatókönyvekhez.

1. Nyissa meg az Azure Automation-fiókját.
2. A **Folyamatautomatizálás**csoportban válassza a **Runbooks (Runbooks)** lehetőséget.
3. A runbookok listájának tetején válassza a **+ Runbook létrehozása**lehetőséget.
4. A **Runbook hozzáadása** lapon adja meg a **Send-GridMailMessage** értéket a runbook nevéhez. A runbook-típushoz válassza a **PowerShell**lehetőséget. Ezután kattintson a **Létrehozás** elemre.
   ![Runbook létrehozása](./media/automation-send-email/automation-send-email-runbook.png)
5. Létrejön a runbook, és megnyílik a **PowerShell-runbook szerkesztése** oldal.
   ![A Runbook szerkesztése](./media/automation-send-email/automation-send-email-edit.png)
6. Másolja a következő PowerShell-példát a **Szerkesztés** lapra. Győződjön `$VaultName` meg arról, hogy a kulcstartó létrehozásakor megadott név.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. A **Runbook** mentéséhez és közzétételéhez válassza a Közzététel lehetőséget.

Annak ellenőrzéséhez, hogy a runbook végrehajtása sikeresen befejeződött-e, kövesse a [Runbook tesztelése](manage-runbooks.md#testing-a-runbook) vagy [a Runbook indítása](start-runbooks.md)című csoport lépéseit.
Ha kezdetben nem látja a teszte-maileket, ellenőrizze a **Levélszemét** és **a Levélszemét** mappát.

## <a name="clean-up"></a>Takarítás

Ha már nincs rá szükség, törölje a runbookot. Ehhez válassza ki a runbook listából a runbookot, és kattintson a **Törlés** elemre.

Törölje a key vault az [Remove-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0) parancsmag használatával.

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>További lépések

* A runbook oka a [Runbookokkal kapcsolatos hibák elhárítása](./troubleshoot/runbooks.md)című témakörben látható.
* A modulok frissítése az Automation-fiókban, olvassa el [az Azure PowerShell-modulok frissítése az Azure Automationben!](automation-update-azure-modules.md)
* A runbook-végrehajtás figyelése, [lásd: Feladat állapotának továbbítása és feladatstreamek az Automation az Azure Monitor naplók.](automation-manage-send-joblogs-log-analytics.md)
* Ha egy riasztást használó runbookot szeretne előidézni, olvassa el a [Riasztás használata az Azure Automation-runbook aktiválásához című témakört.](automation-create-alert-triggered-runbook.md)
