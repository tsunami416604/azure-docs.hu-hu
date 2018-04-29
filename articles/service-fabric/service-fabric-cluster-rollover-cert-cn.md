---
title: Egy Azure Service Fabric-fürt tanúsítvány helyettesítő |} Microsoft Docs
description: Ismerje meg, hogyan helyettesítő a Service Fabric-fürt tanúsítvány által azonosított a tanúsítvány egyszerű neve.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi;aljo
ms.openlocfilehash: a471e1aa8b68e8b94094dc79c8ae71f24cf290cb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>A Service Fabric-fürt tanúsítvány manuális váltása
Amikor a Service Fabric-fürt tanúsítvány hamarosan lejár, meg kell a tanúsítványt.  Tanúsítványok leváltása használata egyszerű, ha a fürt [állítsa be a tanúsítvány köznapi név alapján akár](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (helyett ujjlenyomat).  Egy új tanúsítvány kérése a hitelesítésszolgáltatótól, új lejárati dátummal.  Önaláírt tanúsítványokat, beleértve a jönnek létre, ha az Azure portálon, a Service Fabric-fürt üzembe helyezése nem támogatottak.  Az új tanúsítvány köznapi neve megegyezik a régebbi tanúsítvánnyal kell rendelkeznie. 

A következő parancsfájl feltölt egy új tanúsítvány kulcstároló, és ezután telepíti a tanúsítványt a virtuálisgép-méretezési csoport.  A Service Fabric-fürt automatikusan a legújabb lejárati dátummal a tanúsítványt használja.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzureRmVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

További tudnivalókért olvassa el a következő:
* További tudnivalók [fürt biztonsági](service-fabric-cluster-security.md).
* [Fürt tanúsítványok frissítése és kezelése](service-fabric-cluster-security-update-certs-azure.md)

