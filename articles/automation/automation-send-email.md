---
title: E-mail küldése az Azure Automation-runbook
description: Ismerje meg, hogyan egy runbookon belüli származó e-mail küldése a SendGrid használatával.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 07/15/2019
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: ce05aadb53cc3ad24ed65ea139594010e1aef047
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234982"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Oktatóanyag: E-mail küldése az Azure Automation-runbook

E-mailt küldhet a runbook [SendGrid](https://sendgrid.com/solutions) PowerShell használatával. Ez az oktatóanyag bemutatja, hogyan hozhat létre, amely küld egy e-mailt a tárolt API-kulcs használatával újrafelhasználható runbookok [Azure KeyVault](/azure/key-vault/).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Hozzon létre egy Azure KeyVault
> * A KeyVault Store a SendGrid API-kulcsát
> * Hozzon létre egy runbookot, amely lekéri az API-kulcsot, és a egy e-mailt küld

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei:

* Azure-előfizetés: Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [A SendGrid-fiók létrehozása](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Automation-fiók](automation-offering-get-started.md) a **Az** modulokat, és [futtató kapcsolatot](automation-create-runas-account.md)tárolja, és végrehajtja a forgatókönyvet.

## <a name="create-an-azure-keyvault"></a>Hozzon létre egy Azure KeyVault

Az Azure KeyVault a következő PowerShell-parancsfájl használatával hozhat létre. Adott környezetre jellemző értékeket cserélje le a változó értéke. A beágyazott Azure Cloud Shell-t használja a <kbd>Kipróbálom</kbd> gombra, a kódblokk jobb felső sarkában található. Is másolhatja, és futtassa a kódot, helyileg, ha rendelkezik a [Azure PowerShell-modul](/powershell/azure/install-az-ps) telepítve a helyi gépen.

> [!NOTE]
> Az API-kulcs lekéréséhez kövesse a lépéseket található [a SendGrid API-kulcs megkeresése](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

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

# Grant access to the KeyVault to the Automation RunAs account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Hozzon létre egy Azure KeyVault és titkos kulcsokat tárolhat egyéb módjai, lásd: [KeyVault útmutatóink](/azure/key-vault/).

## <a name="import-required-modules-to-your-automation-account"></a>Az Automation-fiókhoz a szükséges modulok importálásához

Az Azure KeyVault használata egy runbookon belüli, az Automation-fiókjába a következő modulok lesz szüksége:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile).
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault).

Kattintson a <kbd>üzembe helyezés az Azure Automation</kbd> az Azure Automation a telepítési beállítások lapon. Ez a művelet megnyitja az Azure Portalt. Az importálás oldalon válassza ki az Automation-fiókját, és kattintson <kbd>OK</kbd>.

A szükséges modulok hozzáadása a további módjaival kapcsolatban lásd: [importálás modulok](/azure/automation/shared-resources/modules#import-modules).

## <a name="create-the-runbook-to-send-an-email"></a>E-mail küldése a runbook létrehozása

Miután létrehozott egy KeyVault és tárolja a SendGrid API-kulcsát, ideje hozza létre a runbookot, amely betölti az API-kulcsot, és e-mailt küldeni.

Ez a runbook használja a AzureRunAsConnection [Futtatás mint fiók](automation-create-runas-account.md) a titkos kulcs lekérése az Azure KeyVault Azure-hitelesítésre.

Ez a példa használatával hozza létre a nevű runbook **küldési-GridMailMessage**. Módosítása a PowerShell-parancsfájlt, és újra felhasználhatja a különböző helyzetekhez.

1. Nyissa meg az Azure Automation-fiók.
2. A **Folyamatautomatizálás**válassza **Runbookok**.
3. A runbookok listája tetején válassza **+ hozzon létre egy runbookot**.
4. Az a **Runbook hozzáadása** lap, adja meg **küldési-GridMailMessage** a runbook neve. A forgatókönyv típusának kiválasztása **PowerShell**. Ezután kattintson a **Létrehozás** elemre.
   ![Runbook létrehozása](./media/automation-send-email/automation-send-email-runbook.png)
5. Létrejön a runbook, és megnyílik a **PowerShell-runbook szerkesztése** oldal.
   ![A Runbook szerkesztése](./media/automation-send-email/automation-send-email-edit.png)
6. Másolja be a következő PowerShell-példa az **szerkesztése** lapot. Ügyeljen arra, hogy a `$VaultName` a kulcstartó létrehozásakor megadott nevét.

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

7. Válassza ki **közzététel** menti és közzéteszi a runbookot.

Győződjön meg arról, hogy a runbook végrehajtása sikeresen megtörtént kövesse a lépéseket a [egy runbook tesztelése](manage-runbooks.md#test-a-runbook) vagy [elindít egy runbookot](start-runbooks.md).
Ha kezdetben nem látható a teszt e-mailt, ellenőrizze a **levélszemét** és **levélszemét** mappákat.

## <a name="clean-up"></a>Tisztítása

Ha már nincs rá szükség, törölje a runbookot. Ehhez válassza ki a runbook listából a runbookot, és kattintson a **Törlés** elemre.

Törölje a key vault használatával a [Remove-AzureRMKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps) parancsmagot.

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>További lépések

* Tekintse meg a probléma létrehozása vagy a runbook indítása [forgatókönyvekkel kapcsolatos hibák elhárítása](./troubleshoot/runbooks.md).
* -Modulokat az Automation-fiók frissítéséhez lásd [frissítése az Azure PowerShell-modulok az Azure Automationben](automation-update-azure-modules.md)].
* Runbook végrehajtása figyeléséről lásd: [feladat állapotát és a feladatstreamek továbbítja automatizálást az Azure Monitor naplóira](automation-manage-send-joblogs-log-analytics.md).
* Riasztás runbookot indít, lásd: [használjon olyan riasztást, egy Azure Automation-runbook elindítása](automation-create-alert-triggered-runbook.md).
