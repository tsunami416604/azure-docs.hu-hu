---
title: Egy Azure Service Fabric-fürt használatára a tanúsítvány egyszerű neve frissítése |} Microsoft Docs
description: Ismerje meg, hogyan lehet váltani a Service Fabric-fürt a tanúsítvány-ujjlenyomatok tanúsítvány köznapi név megadásával.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: 39dc5800edd743cdc950c7a96f7633fb4c0a7c45
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Tanúsítvány-ujjlenyomat fürt váltson köznapi név
Nincs két tanúsítványt ugyanazzal az ujjlenyomattal, ami megnehezíti a fürt tanúsítványváltást vagy felügyeleti rendelkezhet. Több tanúsítvány azonban rendelkezhet ugyanazzal a névvel vagy tulajdonos.  Váltás a tanúsítvány köznapi nevek használatával tanúsítvány-ujjlenyomatok használatával telepített fürt teszi jóval egyszerűbb Tanúsítványkezelő. Ez a cikk ismerteti, hogyan kell használni a tanúsítvány egyszerű neve helyett a tanúsítvány ujjlenyomata futó Service Fabric-fürt frissítéséhez.
 
## <a name="get-a-certificate"></a>Tanúsítvány beszerzése
Szereznie egy tanúsítványt egy [hitelesítésszolgáltató-tanúsítvány](https://wikipedia.org/wiki/Certificate_authority).  A tanúsítvány köznapi nevének kell lennie a fürt állomásnevét.  Például "myclustername.southcentralus.cloudapp.azure.com."  

Tesztelési célokra egy hitelesítésszolgáltató által aláírt tanúsítvány sikerült szabad vagy nyitott hitelesítésszolgáltatójától.

> [!NOTE]
> Önaláírt tanúsítványokat, beleértve a jönnek létre, ha az Azure portálon, a Service Fabric-fürt üzembe helyezése nem támogatottak.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>A tanúsítvány feltöltése, a méretezési csoportban lévő telepítés
Az Azure Service Fabric-fürt virtuálisgép-méretezési csoport van telepítve.  Töltse fel a tanúsítványt a kulcstároló, és majd telepítse azt a virtuálisgép-méretezési csoport a fürtön futó.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgropu"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

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

## <a name="download-and-update-the-template-from-the-portal"></a>Töltse le, és frissítse a sablon a portálon
A tanúsítvány telepítve van az alapul szolgáló méretezési, de is frissíteni kell a Service Fabric-fürt használatára, hogy a tanúsítványt és annak köznapi név.  Most töltse le a fürt központi telepítés sablonja.  Jelentkezzen be a [Azure-portálon](https://portal.azure.com) , és keresse meg az erőforráscsoportot, a fürtöt.  A **beállítások**, jelölje be **központi telepítések**.  Válassza ki a legutóbbi központi telepítését, és kattintson a **sablon megtekintése**.

![Sablonok megtekintése][image1]

A sablon és a paraméterek JSON-fájlok letöltése a helyi számítógépen.

Első lépésként nyissa meg a paraméterek fájlt egy szövegszerkesztőben, és adja hozzá a következő paraméter értéke:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Ezután nyissa meg a sablon fájlt egy szövegszerkesztőben, és három frissíti a támogatja a tanúsítvány egyszerű neve.

1. Az a **paraméterek** területen írja be a *certificateCommonName* paraméter:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    ```

    Is lehet megvonni a *certificateThumbprint*, előfordulhat, hogy már nem lesz szükség.

2. Az a **Microsoft.Compute/virtualMachineScaleSets** erőforrás, a köznapi név tanúsítvány beállításai helyett az ujjlenyomat használandó virtuálisgép-bővítmény frissítése.  A **virtualMachineProfile**->**extenstionProfile**->**bővítmények**->**tulajdonságai** -> **beállítások**->**tanúsítvány**, adja hozzá `"commonNames": ["[parameters('certificateCommonName')]"],` , és távolítsa el `"thumbprint": "[parameters('certificateThumbprint')]",`.
    ```json
    "virtualMachineProfile": {
              "extensionProfile": {
                "extensions": [
                  {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": true,
                      "protectedSettings": {
                        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                        "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                      },
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[variables('vmNodeType0Name')]",
                        "dataPath": "D:\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "enableParallelJobs": true,
                        "nicPrefixOverride": "[variables('subnet0Prefix')]",
                        "certificate": {
                          "commonNames": ["[parameters('certificateCommonName')]"],                          
                          "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
                      "typeHandlerVersion": "1.0"
                    }
                  },
    ```

3.  Az a **Microsoft.ServiceFabric/clusters** erőforrás, a frissítés az API-verziója "2018-02-01".  Is hozzáadhat egy **certificateCommonNames** beállításnál egy **commonNames** tulajdonság, és távolítsa el a **tanúsítvány** beállítás (a az ujjlenyomat tulajdonság), mint a következő Példa:
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
        "addonFeatures": [
            "DnsService",
            "RepairManager"
        ],        
        "certificateCommonNames": {
            "commonNames": [
            {
                "certificateCommonName": "[parameters('certificateCommonName')]",
                "certificateIssuerThumbprint": ""
            }
            ],
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
        ...
    ```

## <a name="deploy-the-updated-template"></a>A frissített sablon üzembe helyezése
A módosítások elvégzése után a frissített sablon újbóli.

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"

New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" -Verbose
```

## <a name="next-steps"></a>További lépések
* További tudnivalók [fürt biztonsági](service-fabric-cluster-security.md).
* Megtudhatja, hogyan [helyettesítő fürt tanúsítvány](service-fabric-cluster-rollover-cert-cn.md)
* [Fürt tanúsítványok frissítése és kezelése](service-fabric-cluster-security-update-certs-azure.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png