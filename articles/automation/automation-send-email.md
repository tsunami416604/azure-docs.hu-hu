---
title: E-mail küldése egy Azure Automation runbook
description: Ez a cikk azt ismerteti, hogyan küldhet e-mailt egy runbook belülről.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: conceptual
ms.openlocfilehash: 1fa270907c96cb341f6ce2cbaeb91dfa323c4431
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85855214"
---
# <a name="send-an-email-from-a-runbook"></a>E-mail küldése runbookból

E-mailt küldhet egy runbook a [SendGrid](https://sendgrid.com/solutions) a PowerShell használatával. 

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik ilyennel, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Egy SendGrid-fiók](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Automation-fiók](automation-offering-get-started.md) az **az** moduljaival.
* [Futtató fiók](automation-create-runas-account.md) a runbook tárolásához és végrehajtásához.

## <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása;

Azure Key Vault a következő PowerShell-parancsfájl használatával hozhat létre. Cserélje le a változó értékeket a környezetre jellemző értékekre. Használja a beágyazott Azure Cloud Shell a kód jobb felső sarkában található **TRY IT (kipróbálás** ) gomb segítségével. Emellett helyileg is másolhatja és futtathatja a kódot, ha az az [modulok](/powershell/azure/install-az-ps) telepítve vannak a helyi gépen.

> [!NOTE]
> Az API-kulcs lekéréséhez kövesse a [SENDGRID API-kulcs megkeresése](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key)című témakör lépéseit.

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

A Azure Key Vault létrehozásának és a titkos kulcs tárolásának egyéb módjaiért lásd: [Key Vault](/azure/key-vault/)gyors útmutató.

## <a name="import-required-modules-into-your-automation-account"></a>Szükséges modulok importálása az Automation-fiókba

Ha Azure Key Vaultt szeretne használni a runbook belül, a következő modulokat kell importálnia az Automation-fiókjába:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Útmutatásért lásd: [Importálás az modulok](shared-resources/modules.md#import-az-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Runbook létrehozása e-mailek küldéséhez

Miután létrehozott egy Key Vault és tárolta az `SendGrid` API-kulcsot, ideje létrehozni azt a runbook, amely lekéri az API-kulcsot, és elküld egy e-mailt. Egy olyan runbook használjon, amely `AzureRunAsConnection` [futtató fiókként](automation-create-runas-account.md) hitelesíti az Azure-t a titkos kulcs lekéréséhez Azure Key Vault. Meghívjuk a **Send-GridMailMessage**runbook. Módosíthatja a példaként használt PowerShell-szkriptet, és felhasználhatja azt különböző forgatókönyvek esetén.

1. Nyissa meg Azure Automation-fiókját.
2. A **folyamat automatizálása**területen válassza a **runbookok**lehetőséget.
3. A runbookok listájának tetején válassza a **+ Runbook létrehozása**lehetőséget.
4. A Runbook hozzáadása lapon adja meg a **Send-GridMailMessage** nevet a Runbook nevéhez. A runbook típusnál válassza a **PowerShell**lehetőséget. Ezután válassza a **Létrehozás**lehetőséget.
   ![Runbook létrehozása](./media/automation-send-email/automation-send-email-runbook.png)
5. Létrejön a runbook, és megnyílik a PowerShell-runbook szerkesztése oldal.
   ![A Runbook szerkesztése](./media/automation-send-email/automation-send-email-edit.png)
6. Másolja a következő PowerShell-példát a Szerkesztés lapra. Győződjön meg arról, hogy a `VaultName` Key Vault kiválasztott nevet adja meg.

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

7. Válassza a **Közzététel** lehetőséget a runbook mentéséhez és közzétételéhez.

Annak ellenőrzéséhez, hogy a runbook sikeresen végrehajtja-e a műveleteket, követheti a [Runbook tesztelése](manage-runbooks.md#test-a-runbook) vagy a [runbook elindítása](start-runbooks.md)című szakasz lépéseit.

Ha kezdetben nem látja a teszt e-mail-címét, ellenőrizze a **Levélszemét** és a **Levélszemét** mappáját.

## <a name="clean-up-resources-after-the-email-operation"></a>Erőforrások tisztítása az e-mail művelet után

1. Ha a runbook már nincs rá szükség, válassza ki a runbook listában, és kattintson a **Törlés**gombra.

2. Törölje a Key Vault a [Remove-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0) parancsmag használatával.

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>További lépések

* Ha runbook szeretne küldeni a Log Analytics munkaterületre, tekintse meg a [Azure Automation-feladatok továbbítása Azure monitor naplókhoz](automation-manage-send-joblogs-log-analytics.md)című témakört.
* Az alapszintű mérőszámok és naplók figyeléséhez lásd: [riasztás használata Azure Automation runbook elindításához](automation-create-alert-triggered-runbook.md).
* A runbook műveletek során felmerülő problémák megoldásához tekintse meg a [runbook kapcsolatos problémák elhárítása](./troubleshoot/runbooks.md)című témakört.