---
title: Lehetővé teszik az alkalmazások Azure verem Key Vault titkos kulcsok beolvasása |} Microsoft Docs
description: Az Azure verem Key Vault működéséhez egy mintaalkalmazást használ
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/11/2018
ms.author: mabrigg
ms.openlocfilehash: 39bce286c756660cd8755358cf98f2c8d35ce351
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807380"
---
# <a name="a-sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Kulcsok és titkos key vaultban tárolt használó mintaalkalmazás

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Kövesse a cikk futtassa a mintaalkalmazást (HelloKeyVault) kiolvassa a kulcsok és titkos kulcsok Azure verem kulcstároló.

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek telepítheti az Azure-veremből [szoftverfejlesztői készlet](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), vagy egy Windows-alapú külső ügyfél Ha [VPN-en keresztül csatlakozó](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Telepítés [Azure verem-kompatibilis Azure PowerShell-modulok](azure-stack-powershell-install.md).
* Töltse le a [az Azure veremnek megfelelő működéséhez szükséges eszközök](azure-stack-powershell-download.md).

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Hozzon létre, és a kulcstartót és az alkalmazásbeállítások beolvasása

A mintaalkalmazás előkészítése:

* Hozzon létre egy kulcstartót Azure-készletben.
* Alkalmazás regisztrálása az Azure Active Directory (Azure AD).

Az Azure portálon vagy a PowerShell segítségével készítse elő a mintaalkalmazáshoz. Ez a cikk bemutatja, hogyan hozzon létre egy kulcstartót és egy alkalmazás regisztrálása a PowerShell használatával.

>[!NOTE]
>Alapértelmezés szerint a PowerShell parancsfájl létrehoz egy új alkalmazást az Active Directoryban. Azonban regisztrálhatja a meglévő alkalmazások közül.

 A következő parancsfájl futtatása előtt győződjön meg arról, a értékeket ad meg a `aadTenantName` és `applicationPassword` változókat. Ha nem ad meg értéket `applicationPassword`, ezt a parancsfájlt hoz létre egy véletlenszerű jelszót.

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
$aadTenantName = "PLEASE FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

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

A következő képernyőfelvétel-készítés a kulcstároló létrehozásához használt parancsfájl kimenetében láthatók:

![Tároló billentyűt a hívóbetűk](media/azure-stack-kv-sample-app/settingsoutput.png)

Jegyezze fel a **VaultUrl**, **AuthClientId**, és **AuthClientSecret** az előző parancsfájl által visszaadott értékek. Ezeket az értékeket használja a HelloKeyVault alkalmazás futtatásához.

## <a name="download-and-configure-the-sample-application"></a>Töltse le és konfigurálja a mintaalkalmazás

Töltse le a kulcstartót minta az Azure-ból [Key Vault ügyfél minták](https://www.microsoft.com/en-us/download/details.aspx?id=45343) lap. Bontsa ki a .zip-fájlt a fejlesztő munkaállomás tartalmát. A minták mappában két alkalmazás, ebben a cikkben az HelloKeyVault.

A HelloKeyVault minta betöltése:

* Keresse meg a **Microsoft.Azure.KeyVault.Samples** > **minták** > **HelloKeyVault** mappa.
* Indítsa el a HelloKeyVault alkalmazást a Visual Studióban.

### <a name="configure-the-sample-application"></a>A mintaalkalmazás konfigurálása

A Visual Studio:

* Nyissa meg a HelloKeyVault\App.config fájlt, és tallózással keresse meg a Keresés a &lt; **appSettings** &gt; elemet.
* Frissítés a **VaultUrl**, **AuthClientId**, és **AuthClientSecret** a kulcstároló létrehozásához használt által visszaadott értékek kulcsok. (Alapértelmezés szerint az App.config fájlban van helyőrzője *AuthCertThumbprint*. Cserélje le a helyőrzőt *AuthClientSecret*.)

  ![Alkalmazásbeállítások](media/azure-stack-kv-sample-app/appconfig.png)

* A megoldás újbóli létrehozása.

## <a name="run-the-application"></a>Az alkalmazás futtatása

HelloKeyVault futtatásakor az alkalmazás az Azure AD-szal, és a AuthClientSecret jogkivonat használatával hitelesítik magukat a key vault Azure-készletben.

HelloKeyVault minta használhatja:

* Hajtsa végre az alapvető műveleteket, például hozzon létre, titkosítására, burkolja, és törölje a kulcsok és titkos kulcsok.
* Paraméterekkel, mint *titkosítása* és *visszafejtéséhez* való HelloKeyVault, és a megadott módosítások alkalmazásához kulcstároló.

## <a name="next-steps"></a>További lépések

[Virtuális gép üzembe helyezése Key Vault-jelszóval](azure-stack-kv-deploy-vm-with-secret.md)

[A Key Vault tanúsítvánnyal egy virtuális gép üzembe helyezése](azure-stack-kv-push-secret-into-vm.md)
