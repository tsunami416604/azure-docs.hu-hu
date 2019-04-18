---
title: Váltás az Azure Service Fabric-fürt tanúsítványt |} A Microsoft Docs
description: Ismerje meg, hogyan váltani egy Service Fabric-fürt tanúsítvány által azonosított a tanúsítvány köznapi nevét.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: aljo
ms.openlocfilehash: dd4b6026772a20c522532e1ba65c6846addfa161
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046359"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Manuálisan vihetők át a Service Fabric-fürt tanúsítvány
Ha egy Service Fabric-fürt tanúsítvány hamarosan lejár, akkor frissítenie kell a tanúsítványt.  Tanúsítványváltás az egyszerű, ha a fürt [beállított tanúsítvány köznapi név alapján](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (helyett ujjlenyomat).  Egy új tanúsítvány beszerzése egy hitelesítésszolgáltatótól, új lejárati dátummal.  Önaláírt tanúsítványok nem termelési Service Fabric-fürtök az Azure portal fürt létrehozásának munkafolyamata során létrehozott tanúsítványokat támogatása el. Az új tanúsítvány köznapi neve megegyezik a régebbi tanúsítványt kell rendelkeznie. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Service Fabric-fürt automatikusan a deklarált tanúsítványt használja, és a egy további, a jövőbeli lejárati dátumot; Ha egynél több ellenőrzése a tanúsítvány telepítve van a gazdagépen. Ajánlott eljárás, hogy az Azure-erőforrások kiépítése egy Resource Manager-sablon. Nem éles környezetben a következő parancsfájl töltse fel egy új tanúsítványt a key vault segítségével, és ezután telepíti a tanúsítványt a virtuálisgép-méretezési csoportot: 

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

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Kiszámítja a virtuális gép méretezési beállítása titkos kulcsok nem támogatják az erőforrás-azonosító a két külön titkos adatait, mivel minden titkos kulcsot egy verzióval ellátott egyedi erőforrást. 

További tudnivalókért olvassa el a következőket:
* Ismerje meg [biztonsági fürt](service-fabric-cluster-security.md).
* [Frissítse és fürttanúsítványok kezelése](service-fabric-cluster-security-update-certs-azure.md)

