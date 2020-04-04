---
title: WinRM-hozzáférés beállítása Azure-beli virtuális géphez
description: WinRM-hozzáférés beállítása az Erőforrás-kezelő telepítési modelljében létrehozott Azure virtuális géppel való használatra.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/16/2016
ms.author: kasing
ms.openlocfilehash: 3dd56682022bfb5dc620beb06d401910726894b9
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631544"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>A WinRM-hozzáférés beállítása virtuális gépekhez az Azure Resource Managerben

Az alábbiakban azokkal a lépésekkel kell megtelnie a WinRM-kapcsolattal rendelkező virtuális gép beállításához

1. Kulcstartó létrehozása
2. Önaláírt tanúsítvány létrehozása
3. Töltse fel önaláírt tanúsítványát a Key Vaultba
4. Az önaláírt tanúsítvány URL-címének beszereznie a Key Vaultban
5. Hivatkozzon az önaláírt tanúsítványok URL-címére virtuális gép létrehozása közben

 

## <a name="step-1-create-a-key-vault"></a>1. lépés: Key Vault létrehozása
Az alábbi paranccsal létrehozhatja a Key Vaultot

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>2. lépés: Önaláírt tanúsítvány létrehozása
Ezzel a PowerShell-parancsfájllal önaláírt tanúsítványt hozhat létre

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>3. lépés: Töltse fel önaláírt tanúsítványát a Key Vaultba
Mielőtt feltöltené a tanúsítványt az 1. Az alábbi PowerShell-parancsfájl lehetővé teszi, hogy ezt

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
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>4. lépés: Az önaláírt tanúsítvány URL-címének beszereznie a Key Vaultban
A Microsoft.Compute erőforrás-szolgáltatónak szüksége van egy URL-címet a kulcs a Key Vault ban, miközben a virtuális gép kiépítése. Ez lehetővé teszi, hogy a Microsoft.Compute erőforrás-szolgáltató töltse le a titkos kulcsot, és hozza létre az egyenértékű tanúsítványt a virtuális gépen.

> [!NOTE]
> Az URL-t a titkos tartalmaznia kell a verziót is. Egy példa URL-cím\/az alábbi https: /contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Sablonok
Az alábbi kód használatával a sablonban található URL-re mutató hivatkozást

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Ezt az URL-címet az alábbi PowerShell-paranccsal szerezheti be

    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>5. lépés: Hivatkozzon az önaláírt tanúsítványok URL-címére a virtuális gép létrehozása közben
#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok
A virtuális gép sablonokon keresztüli létrehozása során a tanúsítvány a titkos kulcsok és a winRM szakaszban hivatkozik az alábbiak szerint:

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

A minta sablon a fenti megtalálható itt [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

A sablon forráskódja megtalálható a [GitHubon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -ProvisionVMAgent -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>6. lépés: Csatlakozás a virtuális géphez
A virtuális géphez való csatlakozás előtt meg kell győződnie arról, hogy a számítógép a WinRM távoli kezelése konfigurálva van. Indítsa el a PowerShellt rendszergazdaként, és hajtsa végre az alábbi parancsot, hogy megbizonyosodjon arról, hogy be van állítva.

    Enable-PSRemoting -Force

> [!NOTE]
> Ha a fentiek nem működnek, előfordulhat, hogy meg kell győződnie arról, hogy a Rendszerfelügyeleti webszolgáltatások szolgáltatás a szolgáltatás fut. Ezt megteheti a`Get-Service WinRM`
> 
> 

Miután a telepítés befejeződött, csatlakozhat a virtuális géphez az alábbi paranccsal

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
