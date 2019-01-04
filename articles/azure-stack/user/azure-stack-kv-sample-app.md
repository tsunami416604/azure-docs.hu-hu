---
title: Lehetővé teszik az alkalmazások az Azure Stack a Key Vault titkos kódjainak beolvasására |} A Microsoft Docs
description: Mintaalkalmazás használata az Azure Stack Key Vault használata
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/27/2018
ms.author: sethm
ms.openlocfilehash: b17f6301a41dbb1f64edf9d027dff0f57c09282c
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2018
ms.locfileid: "53808774"
---
# <a name="a-sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Használja a kulcsok és titkos kulcsok tárolása a key vault-mintaalkalmazás

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Kövesse az ebben a cikkben futtathat egy mintaalkalmazást nevű **HelloKeyVault** , amely lekéri a kulcsok és titkos kulcs-tároló az Azure Stackben.

## <a name="prerequisites"></a>Előfeltételek

Az alábbi előfeltételek lehet telepíteni az Azure Stack [Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), vagy egy Windows-alapú külső ügyfél Ha [VPN-kapcsolaton keresztül csatlakozó](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Telepítés [Azure Stack-kompatibilis Azure PowerShell-modulok](azure-stack-powershell-install.md).
* Töltse le a [az Azure Stack működéséhez szükséges eszközök](azure-stack-powershell-download.md).

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Hozzon létre, és a key vaulttal és Alkalmazásbeállítások beolvasása

A mintaalkalmazás előkészítése:

* Kulcstartó létrehozása az Azure Stackben.
* Alkalmazás regisztrálása az Azure Active Directoryban (Azure AD).

Az Azure portal vagy a PowerShell segítségével készítse elő a mintaalkalmazáshoz. Ez a cikk bemutatja, hogyan hozzon létre egy kulcstartót, és a egy alkalmazás regisztrálása a PowerShell használatával.

>[!NOTE]
>Alapértelmezés szerint a PowerShell-parancsfájlt az Active Directoryban egy új alkalmazást hoz létre. Azonban regisztrálhat egyet a meglévő alkalmazásokat.

A következő szkript futtatása előtt győződjön meg arról, az értékeket ad meg a `aadTenantName` és `applicationPassword` változókat. Ha nem ad meg értéket `applicationPassword`, ez a szkript létrehoz egy véletlenszerű jelszó.

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local'

# Password for the application. If not specified, this script will generate a random password during app creation.
$applicationPassword = ''

# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience "https://graph.windows.net/"

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Add-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "http://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host
```

Az alábbi képen látható a kulcstároló létrehozásához használt a szkript kimenetében:

![Kulcstartó-hozzáférési kulcsok](media/azure-stack-kv-sample-app/settingsoutput.png)

Jegyezze fel a **VaultUrl**, **AuthClientId**, és **AuthClientSecret** az előző parancsfájl által visszaadott értékek. Ezeket az értékeket használja a HelloKeyVault alkalmazás futtatásához.

## <a name="download-and-configure-the-sample-application"></a>Töltse le és a mintaalkalmazás konfigurálása

A key vault-minta letöltése az Azure [Key Vault-ügyfélnek minták](https://www.microsoft.com/download/details.aspx?id=45343) lapot. Bontsa ki a .zip fájlt a fejlesztő munkaállomás tartalmát. A minták mappában két alkalmazások is vannak. Ez a cikk **HelloKeyVault**.

Betölteni a **HelloKeyVault** minta:

* Keresse meg a **Microsoft.Azure.KeyVault.Samples** > **minták** > **HelloKeyVault** mappát.
* Nyissa meg a **HelloKeyVault** alkalmazáshoz a Visual Studióban.

### <a name="configure-the-sample-application"></a>A mintaalkalmazás konfigurálása

A Visual Studióban:

* Nyissa meg a HelloKeyVault\App.config fájlt, és keresse meg a &lt; **appSettings** &gt; elemet.
* Frissítés a **VaultUrl**, **AuthClientId**, és **AuthClientSecret** kulcsok a key vault létrehozása használtakon által visszaadott értékekkel. Alapértelmezés szerint az App.config fájl rendelkezik egy helyőrző `AuthCertThumbprint`. Cserélje le a helyőrző a `AuthClientSecret`.

  ![Alkalmazásbeállítások](media/azure-stack-kv-sample-app/appconfig.png)

* Építse újra a megoldást.

## <a name="run-the-application"></a>Az alkalmazás futtatása

HelloKeyVault futtatásakor az alkalmazás jelentkezik be az Azure ad-hez, és ezután a AuthClientSecret jogkivonat használatával hitelesítik magukat a key vault az Azure Stackben.

Használhatja a HelloKeyVault mintát:

* Hajtsa végre alapszintű műveleteket, például létrehozhat, titkosítása, wrap, és törölje a kulcsok és titkos kulcsok.
* Például adja át a paramétereket `encrypt` és `decrypt` való HelloKeyVault, és alkalmazza a megadott módosításokat, egy kulcstárolóba.

## <a name="next-steps"></a>További lépések

- [Virtuális gép üzembe helyezése Key Vault-jelszóval](azure-stack-kv-deploy-vm-with-secret.md)
- [Virtuális gép létrehozása Key Vault-tanúsítvánnyal](azure-stack-kv-push-secret-into-vm.md)
