---
title: E-mail küldése egy Azure Automation runbook
description: Ismerje meg, hogyan küldhet e-mailt a SendGrid egy runbook belülről.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: 6acb68b7bbaa54db2e4143a42e43aede2caed35f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75420709"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Oktatóanyag: e-mail küldése egy Azure Automation runbook

E-mailt küldhet egy runbook a [SendGrid](https://sendgrid.com/solutions) a PowerShell használatával. Ez az oktatóanyag bemutatja, hogyan hozhat létre újrafelhasználható runbook, amely az [Azure](/azure/key-vault/)kulcstartóban tárolt API-kulccsal küld e-mailt.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Azure-kulcstartó létrehozása
> * A SendGrid API-kulcs tárolása a kulcstartóban
> * Hozzon létre egy runbook, amely lekéri az API-kulcsot, és küldjön egy e-mailt

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei:

* Azure-előfizetés: Ha még nem rendelkezik ilyennel, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Hozzon létre egy SendGrid-fiókot](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Automation](automation-offering-get-started.md) -fiók **az az** modules és a [futtató kapcsolódási](automation-create-runas-account.md)szolgáltatással a runbook tárolásához és végrehajtásához.

## <a name="create-an-azure-keyvault"></a>Azure-kulcstartó létrehozása

Azure kulcstartót a következő PowerShell-parancsfájl használatával hozhat létre. Cserélje le a változó értékeket a környezetre jellemző értékekre. Használja a beágyazott Azure Cloud Shell a kód jobb felső sarkában található <kbd>TRY IT (kipróbálás</kbd> ) gomb segítségével. Emellett helyileg is másolhatja és futtathatja a kódot, ha a [Azure PowerShell modul](/powershell/azure/install-az-ps) telepítve van a helyi gépen.

> [!NOTE]
> Az API-kulcs lekéréséhez kövesse a [SENDGRID API-kulcs megkeresése](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key)című cikkben található lépéseket.

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

Az Azure kulcstartó létrehozásának és a titkos kulcs tárolásának egyéb módjairól a következő [témakörben talál további információt](/azure/key-vault/): kulcstartók.

## <a name="import-required-modules-to-your-automation-account"></a>Szükséges modulok importálása az Automation-fiókba

Az Azure kulcstartó egy runbook való használatához az Automation-fióknak a következő modulokra lesz szüksége:

* [Az az. profil](https://www.powershellgallery.com/packages/Az.Profile).
* [Az.](https://www.powershellgallery.com/packages/Az.KeyVault)kulcstartó.

Kattintson a telepítés gombra a Azure Automation lapon a telepítési beállítások alatt <kbd>Azure Automation</kbd> . Ez a művelet megnyitja a Azure Portal. Az importálás lapon válassza ki az Automation-fiókját, és kattintson az <kbd>OK</kbd>gombra.

A szükséges modulok hozzáadásával kapcsolatos további módszerekről lásd: [modulok importálása](/azure/automation/shared-resources/modules#import-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Runbook létrehozása e-mailek küldéséhez

Miután létrehozott egy kulcstartót, és tárolta a SendGrid API-kulcsot, itt az ideje, hogy létrehozza a runbook, amely lekéri az API-kulcsot, és küldjön egy e-mailt.

Ez a runbook a Azurerunasconnection elemet [futtató fiók](automation-create-runas-account.md) használatával hitelesíti az Azure-t, hogy lekérje a titkos kulcsot az Azure kulcstartóból.

Ez a példa egy **Send-GridMailMessage**nevű runbook létrehozására használható. Módosíthatja a PowerShell-parancsfájlt, és újra felhasználhatja azt különböző forgatókönyvek esetén.

1. Nyissa meg Azure Automation-fiókját.
2. A **folyamat automatizálása**területen válassza a **runbookok**lehetőséget.
3. A runbookok listájának tetején válassza a **+ Runbook létrehozása**lehetőséget.
4. A **Runbook hozzáadása** lapon adja meg a **Send-GridMailMessage** nevet a Runbook nevéhez. A runbook típusnál válassza a **PowerShell**lehetőséget. Ezután kattintson a **Létrehozás** elemre.
   ![Runbook létrehozása](./media/automation-send-email/automation-send-email-runbook.png)
5. Létrejön a runbook, és megnyílik a **PowerShell-runbook szerkesztése** oldal.
   ![a Runbook szerkesztése](./media/automation-send-email/automation-send-email-edit.png)
6. Másolja a következő PowerShell-példát a **Szerkesztés** lapra. Győződjön meg arról, hogy a `$VaultName` a kulcstartó létrehozásakor megadott név.

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

Annak ellenőrzéséhez, hogy a runbook sikeresen végrehajtja-e a lépéseket, kövesse az [Runbook tesztelése](manage-runbooks.md#test-a-runbook) vagy a [runbook elindítása](start-runbooks.md)című szakasz lépéseit.
Ha nem látja először a teszt e-mail-címét, ellenőrizze a **Levélszemét** és a **Levélszemét** mappáját.

## <a name="clean-up"></a>Tisztítás

Ha már nincs rá szükség, törölje a runbookot. Ehhez válassza ki a runbook listából a runbookot, és kattintson a **Törlés** elemre.

Törölje a Key vaultot a [Remove-AzureRMKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps) parancsmag használatával.

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Következő lépések

* A runbook létrehozásával vagy indításával kapcsolatos problémákért lásd: [hibák elhárítása a runbookok](./troubleshoot/runbooks.md).
* Az Automation-fiókban található modulok frissítését lásd: [Azure PowerShell modulok frissítése Azure Automation](automation-update-azure-modules.md)].
* A runbook végrehajtásának figyeléséhez tekintse [meg a feladatok állapotának és a feladatoknak az automatizálásból Azure monitor a naplókba való továbbítását](automation-manage-send-joblogs-log-analytics.md)ismertető
* Ha riasztást használó runbook szeretne elindítani, tekintse meg a [riasztások használata Azure Automation runbook elindításához](automation-create-alert-triggered-runbook.md)című témakört.
