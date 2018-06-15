---
title: Egy Azure virtuális Gépen a Rendszerfelügyeleti webszolgáltatások hozzáférési beállítása |} Microsoft Docs
description: A telepítő a Rendszerfelügyeleti webszolgáltatások hozzáférési használatra a Resource Manager üzembe helyezési modellel létrehozott Azure virtuális gép a.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/16/2016
ms.author: kasing
ms.openlocfilehash: 5fa82dd4a85ff2e62848df0fdc6006922005a84b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30914545"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>A Rendszerfelügyeleti webszolgáltatások hozzáférés beállítása az Azure Resource Manager virtuális gépekhez
## <a name="winrm-in-azure-service-management-vs-azure-resource-manager"></a>A Rendszerfelügyeleti webszolgáltatások az Azure szolgáltatásfelügyelet vs Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

* Az Azure Resource Manager áttekintéséért lásd: a [cikk](../../azure-resource-manager/resource-group-overview.md)
* Azure Service Management és az Azure Resource Manager közötti különbségek miatt, lásd: a [cikk](../../resource-manager-deployment-model.md)

A kulcs WinRM konfiguráció a két verem közötti különbség hogyan telepíti a tanúsítványt a virtuális Gépen. Az Azure Resource Manager-készletben a tanúsítványok a Key Vault erőforrás-szolgáltató által kezelt erőforrások van modellezve. Ezért a felhasználó adja meg a saját tanúsítványt, és töltse fel azt a kulcstároló virtuális gépen használatához szükséges.

Az alábbiakban a lépéseket kell tennie egy virtuális gép beállítása a Rendszerfelügyeleti webszolgáltatások kapcsolatban

1. Kulcstartó létrehozása
2. Önaláírt tanúsítvány létrehozása
3. Key Vault a önaláírt tanúsítvány feltöltése
4. URL beolvasása a Key Vault az önaláírt tanúsítvány
5. Az önaláírt tanúsítványok URL-CÍMÉRE hivatkozik egy virtuális gép létrehozása közben

## <a name="step-1-create-a-key-vault"></a>1. lépés:, Hozzon létre egy kulcstartót
Használhatja az alábbi parancsot a kulcstároló létrehozásához

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>2. lépés:, Hozzon létre egy önaláírt tanúsítványt
Létrehozhat egy önaláírt tanúsítványt, a PowerShell-parancsfájl használatával

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>3. lépés: A Key Vault a önaláírt tanúsítvány feltöltése
A tanúsítvány feltöltése a Key Vault az 1. lépésben létrehozott, előtt kell a Microsoft.Compute erőforrás-szolgáltató megértenek olyan formátumra alakítja át. Az alábbi PowerShell parancsfájl lehetővé teheti meg, amely

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>4. lépés: Az URL-cím beszerzése a Key Vault az önaláírt tanúsítvány
A Microsoft.Compute erőforrás-szolgáltató a titkos kulcsot belül a Key Vault URL-CÍMÉT kell a virtuális gép kiépítése során. Ez lehetővé teszi a Microsoft.Compute erőforrás-szolgáltató töltse le a titkos kulcsot, és hozzon létre a megfelelő tanúsítványt a virtuális Gépen.

> [!NOTE]
> Az URL-CÍMÉT a titkos kulcsot kell tartalmaznia, valamint a verzió. Egy példa URL-CÍMÉT a következőképpen néz alatt https://contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve
> 
> 

#### <a name="templates"></a>Sablonok
A hivatkozásra kattintva az URL-címet a sablon használatával kaphat az alábbi kód

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Az URL-cím segítségével is ki az alábbi PowerShell-parancs

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>5. lépés: Az önaláírt tanúsítványok URL-CÍMÉRE hivatkozik egy virtuális gép létrehozása közben
#### <a name="azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok
A sablonok használatával virtuális gépek létrehozásakor, a titkos kulcsok és a Rendszerfelügyeleti webszolgáltatások szakaszát, az alábbi a tanúsítvány lekérdezi hivatkozik:

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

A fenti példa sablonját itt található [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Ez a sablon forráskódja található [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>6. lépés: Csatlakozzon a virtuális Géphez
Mielőtt az csatlakozna a virtuális gépre lesz szüksége győződjön meg arról, hogy a gép a WinRM Távfelügyelet van konfigurálva. Indítsa el a Powershellt rendszergazdaként, majd hajtsa végre az alábbi parancs futtatásával ellenőrizze, hogy elkészült a beállítással.

    Enable-PSRemoting -Force

> [!NOTE]
> Szükség lehet győződjön meg arról, hogy a WinRM szolgáltatás fut, ha a fenti nem működik. Megteheti, hogy használatával `Get-Service WinRM`
> 
> 

Ha a telepítő végzett, csatlakozhat a virtuális gép használja az alábbi parancs

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
