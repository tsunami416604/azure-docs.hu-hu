---
title: Az Azure Service Fabric-fürttanúsítvány átütemezése
description: Ismerje meg, hogyan kell átgörgetni a Service Fabric fürttanúsítványt, amelyet a tanúsítvány köznapi neve azonosít.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 94cc6841886b1b0eb4271ac0f727a2e3561e0081
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451962"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Service Fabric-fürttanúsítvány manuális görgetése
Ha egy Service Fabric-fürttanúsítvány közel jár le, frissítenie kell a tanúsítványt.  A tanúsítványváltás egyszerű, ha a fürt [köznapi néven alapuló tanúsítványok használatára](service-fabric-cluster-change-cert-thumbprint-to-cn.md) lett beállítva (ujjlenyomat helyett).  Új tanúsítvány beszereznie egy új lejárati dátummal rendelkező hitelesítésszolgáltatótól.  Önaláírt tanúsítványok nem támogatják az éles Service Fabric-fürtök, az Azure portal fürt létrehozásának munkafolyamata során létrehozott tanúsítványokat. Az új tanúsítványnak a régebbi tanúsítvány nevével megegyező névvel kell rendelkeznie. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A Service Fabric-fürt automatikusan a deklarált tanúsítványt használja a jövőbeli lejárati dátummal; ha egynél több érvényesítési tanúsítvány van telepítve az állomásra. Ajánlott eljárás, hogy egy Erőforrás-kezelő sablon azure-erőforrások kiépítése. Nem éles környezetben a következő parancsfájl segítségével tölthet fel egy új tanúsítványt egy key vaultba, majd telepíti a tanúsítványt a virtuálisgép-méretezési csoportra: 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

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

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Számítási virtuálisgép-méretezési készlet titkos titkait nem támogatja ugyanazt az erőforrás-azonosítót két külön titkos kulcsok, mivel minden titkos kulcsot egy verzióval ellátott egyedi erőforrás. 

## <a name="next-steps"></a>Következő lépések

* További információ a [fürtbiztonságról](service-fabric-cluster-security.md).
* [Fürttanúsítványok frissítése és kezelése](service-fabric-cluster-security-update-certs-azure.md)
