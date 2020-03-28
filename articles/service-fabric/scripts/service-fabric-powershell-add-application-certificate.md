---
title: Alkalmazástanúsítvány hozzáadása fürthöz a Powershellben
description: Azure PowerShell-példaszkript – Alkalmazástanúsítvány hozzáadása egy Service Fabric-fürthöz.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 61c22a3949008d61bbe3472f601d2d0dd597a0ac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77114344"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Alkalmazástanúsítvány hozzáadása egy Service Fabric-fürthöz

Ez a mintaparancsfájl bemutatja, hogyan hozhat létre tanúsítványt a Key Vaultban, majd üzembe helyezheti azt a fürt által futtatott virtuálisgép-méretezési beállítások egyikére. Ez a forgatókönyv nem használja a Service Fabric közvetlenül, hanem függ a Key Vault és a virtuális gép méretezési csoportok.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral. 

## <a name="create-a-certificate-in-key-vault"></a>Tanúsítvány létrehozása a Key Vaultban

```powershell
$VaultName = ""
$CertName = ""
$SubjectName = "CN="

$policy = New-AzKeyVaultCertificatePolicy -SubjectName $SubjectName -IssuerName Self -ValidityInMonths 12
Add-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName -CertificatePolicy $policy
```

## <a name="or-upload-an-existing-certificate-into-key-vault"></a>Vagy töltsön fel egy meglévő tanúsítványt a Key Vaultba

```powershell
$VaultName= ""
$CertName= ""
$CertPassword= ""
$PathToPFX= ""

$Cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2 $PathToPFX, $CertPassword

$bytes = [System.IO.File]::ReadAllBytes($ExistingPfxFilePath)
$base64 = [System.Convert]::ToBase64String($bytes)
$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $CertPassword
   } | ConvertTo-Json
$contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
$content = [System.Convert]::ToBase64String($contentbytes)

$SecretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

# Upload the certificate to the key vault as a secret
$Secret = Set-AzKeyVaultSecret -VaultName $VaultName -Name $CertName -SecretValue $SecretValue

```

## <a name="update-virtual-machine-scale-sets-profile-with-certificate"></a>Virtuálisgép-méretezési csoport profiljának frissítése tanúsítvánnyal

```powershell
$ResourceGroupName = ""
$VMSSName = ""
$CertStore = "My" # Update this with the store you want your certificate placed in, this is LocalMachine\My

$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName).SecretId -CertificateStore $CertStore
$VMSS = Get-AzVmss -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMSSName

# If this KeyVault is already known by the virtual machine scale set, for example if the cluster certificate is deployed from this keyvault, use
$VMSS.virtualmachineprofile.osProfile.secrets[0].vaultCertificates.Add($certConfig)

# Otherwise use
$VMSS = Add-AzVmssSecret -VirtualMachineScaleSet $VMSS -SourceVaultId (Get-AzKeyVault -VaultName $VaultName).ResourceId  -VaultCertificate $CertConfig
```

## <a name="update-the-virtual-machine-scale-set"></a>A virtuálisgép-méretezési csoport frissítése
```powershell
Update-AzVmss -ResourceGroupName $ResourceGroupName -VirtualMachineScaleSet $VMSS -VMScaleSetName $VMSSName
```

> Ha azt szeretné, hogy a tanúsítvány a fürt több csomóponttípusára kerüljön, a parancsfájl második és harmadik részét meg kell ismételni minden olyan csomóponttípusnál, amelynek rendelkeznie kell a tanúsítvánnyal.

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja: A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Új-AzKeyVaultCertificatePolicy](/powershell/module/az.keyvault/New-AzKeyVaultCertificatePolicy) | A tanúsítványt képviselő memórián belüli házirend létrehozása |
| [Add-AzkeyVaultCertificate](/powershell/module/az.keyvault/Add-AzKeyVaultCertificate)| A házirend központi telepítése a Key Vaultba |
| [Új-AzVmssVaultCertificateConfig](/powershell/module/az.compute/New-AzVmssVaultCertificateConfig) | A tanúsítványt a virtuális gépben ábrázoló memórián belüli konfigurációlétrehozása |
| [Get-AzVmss](/powershell/module/az.compute/Get-AzVmss) |  |
| [Add-AzVmssSecret](/powershell/module/az.compute/Add-AzVmssSecret) | Hozzáadja a tanúsítványt a virtuálisgép-méretezési csoport memórián belüli definíciójához |
| [Update-AzVmss](/powershell/module/az.compute/Update-AzVmss) | Telepíti a virtuálisgép-méretezési csoport új definícióját |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.
