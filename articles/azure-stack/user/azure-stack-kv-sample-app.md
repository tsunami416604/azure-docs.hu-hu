---
title: "Lehetővé teszik az alkalmazások Azure verem Key Vault titkos kulcsok beolvasása |} Microsoft Docs"
description: "Az Azure verem Key Vault működéséhez egy mintaalkalmazást használ"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2017
ms.author: sngun
ms.openlocfilehash: 7cfb78cc5219d4adab5ceddc9d7eb8d1fc71b678
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Kulcsok és titkos key vaultban tárolt használó mintaalkalmazás

Ez a cikk azt mutatja be futtassa a mintaalkalmazást (HelloKeyVault) kiolvassa a kulcsok és titkos kulcsok Azure verem kulcstároló.

## <a name="prerequisites"></a>Előfeltételek 

Futtassa a következő előfeltételek származhatnak a [szoftverfejlesztői készlet](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), vagy egy Windows-alapú külső ügyfél Ha [VPN-en keresztül csatlakozó](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Telepítés [Azure verem-kompatibilis Azure PowerShell-modulok](azure-stack-powershell-install.md).  
* Töltse le a [az Azure veremnek megfelelő működéséhez szükséges eszközök](azure-stack-powershell-download.md). 

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Hozzon létre, és a kulcstartót és az alkalmazásbeállítások beolvasása

Először inkább hozzon létre egy kulcstartót Azure-készletben, és egy alkalmazás regisztrálása az Azure Active Directory (Azure AD). Hozzon létre, és regisztrálja a kulcstárolók az Azure-portálon vagy a PowerShell használatával. Ez a cikk bemutatja a PowerShell módja a feladatokat. Alapértelmezés szerint a PowerShell parancsfájl létrehoz egy új alkalmazást az Active Directoryban. Azonban is használhatja a meglévő alkalmazások közül. Ügyeljen arra, hogy adjon meg egy értéket a `aadTenantName` és `applicationPassword` változókat. Ha nem ad meg értéket a `applicationPassword` változó, ezt a parancsfájlt hoz létre egy véletlenszerű jelszót. 

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
Login-AzureRmAccount `
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

# Create a new resource group and a key vault within that resource group.
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

Az alábbi képernyőfelvételen látható az előző parancsfájl:

![Az alkalmazás konfigurációja](media/azure-stack-kv-sample-app/settingsoutput.png)

Jegyezze fel a **VaultUrl**, **AuthClientId**, és **AuthClientSecret** az előző parancsfájl által visszaadott értékek. Ezeket az értékeket használja a HelloKeyVault alkalmazás futtatásához.

## <a name="download-and-run-the-sample-application"></a>Töltse le és futtassa a mintaalkalmazást

Töltse le a kulcstartót minta az Azure-ból [Key Vault ügyfél minták](https://www.microsoft.com/en-us/download/details.aspx?id=45343) lap. Bontsa ki a .zip fájlt a fejlesztő munkaállomás tartalmát. Nincsenek két minta a minták mappában található. A HellpKeyVault mintát használjuk ebben a témakörben. Keresse meg a **Microsoft.Azure.KeyVault.Samples** > **minták** > **HelloKeyVault** mappa és a HelloKeyVault alkalmazás megnyitása a Visual Studióban. 

Nyissa meg a HelloKeyVault\App.config fájlt, és cserélje le az értékeket a <appSettings> rendelkező elemet a **VaultUrl**, **AuthClientId**, és **AuthClientSecret** értékek az előző parancsfájl által visszaadott. Alapértelmezés szerint az App.config tartalmaz helyőrzője *AuthCertThumbprint*, de *AuthClientSecret* helyette. A beállítások cseréje után építse újra a megoldás, és indítsa el az alkalmazást.

![Alkalmazásbeállítások](media/azure-stack-kv-sample-app/appconfig.png)
 
Az alkalmazás az Azure ad Szolgáltatásba bejelentkezik, és majd használja a verem Azure key vault felé történő hitelesítésre. Az alkalmazás létrehozása, titkosítására, burkolja, és a kulcsok és titkos kulcsokat a kulcstároló törlése műveleteket hajtja végre. Paraméterekkel is átadhatja például *titkosítása* és *visszafejtéséhez* az alkalmazásnak, amely gondoskodik arról, hogy az alkalmazás végrehajtja-e a tároló elleni műveletek. 


## <a name="next-steps"></a>Következő lépések
[Virtuális gép üzembe helyezése Key Vault-jelszóval](azure-stack-kv-deploy-vm-with-secret.md)

[A Key Vault tanúsítvánnyal egy virtuális gép üzembe helyezése](azure-stack-kv-push-secret-into-vm.md)



