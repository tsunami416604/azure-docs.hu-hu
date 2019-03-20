---
title: A WinRM-elérés beállítása egy Azure virtuális gép |} A Microsoft Docs
description: A telepítő a Rendszerfelügyeleti webszolgáltatások hozzáférési használatra az Azure Resource Manager-alapú üzemi modellben létrehozott virtuális gép.
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
ms.openlocfilehash: 77b575de3a84d582c11fb31952120e1079e23ffb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58091103"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>A WinRM-elérés beállítása a virtuális gépek az Azure Resource Manager

A következő lépéseket kell tennie a WinRM-kapcsolattal rendelkező virtuális gép beállítása

1. Kulcstartó létrehozása
2. Önaláírt tanúsítvány létrehozása
3. Töltse fel az önaláírt tanúsítványt a Key Vaulthoz
4. Az URL-cím lekérése az önaláírt tanúsítvány a Key vaultban
5. Az önaláírt tanúsítványok URL-cím hivatkozhat egy virtuális gép létrehozása közben

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="step-1-create-a-key-vault"></a>1. lépés: Kulcstartó létrehozása
Használhatja az alábbi parancsot a Key Vault létrehozása

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>2. lépés: Önaláírt tanúsítvány létrehozása
Létrehozhat egy önaláírt tanúsítványt, a PowerShell-parancsfájl használatával

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>3. lépés: Töltse fel az önaláírt tanúsítványt a Key Vault
Mielőtt feltöltené a tanúsítványt a Key vaultba; 1. lépésben létrehozott, kell tisztában van a Microsoft.Compute erőforrás-szolgáltató formátumra konvertálja. Az alábbi PowerShell parancsfájl lehetővé teszi az benne

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

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>4. lépés: Az URL-cím lekérése az önaláírt tanúsítvány a Key vaultban
A Microsoft.Compute erőforrás-szolgáltató URL-CÍMÉT a titkos kulcsot a Key vaultban kell a virtuális gép kiépítése során. Ez lehetővé teszi a Microsoft.Compute erőforrás-szolgáltatót, töltse le a titkos kulcsot, és hozza létre a megfelelő tanúsítványt a virtuális gépen.

> [!NOTE]
> Az URL-címét a titkos kulcsot kell tartalmaznia, valamint a verzió. Alább néz ki egy példa URL-címe https://contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Sablonok
A hivatkozásra kattintva az URL-címet a sablon használatával megtekintheti az alábbi kód

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Az URL-cím használatával lekérheti az alábbi PowerShell-parancs

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>5. lépés: Az önaláírt tanúsítványok URL-cím hivatkozhat egy virtuális gép létrehozása közben
#### <a name="azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok
Sablonok virtuális gép létrehozásakor a tanúsítványt a titkos kulcsok és a Rendszerfelügyeleti webszolgáltatások szakaszban, az alábbi lekérdezi hivatkozott:

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

A fenti mintasablon itt található [201-es vm-winrm-keyvault – windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Ez a sablon forráskódja találhatók [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>6. lépés: Csatlakozzon a virtuális Géphez
Mielőtt az csatlakozna a virtuális gépre lesz szüksége, hogy a gép a WinRM Távfelügyelet van konfigurálva. Indítsa el a Powershellt rendszergazdaként, és hajtsa végre az alábbi parancs használatával győződjön meg arról, hogy beállítása.

    Enable-PSRemoting -Force

> [!NOTE]
> Szükség lehet, hogy a WinRM szolgáltatás fut, ha a fenti nem működik. Megteheti, hogy az `Get-Service WinRM`
> 
> 

Ha a telepítő végzett, csatlakozhat a virtuális Gépet az az alábbi parancs

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
