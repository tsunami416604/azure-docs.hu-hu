---
title: Az Azure Service Fabric-fürt használata a tanúsítvány köznapi nevét frissíteni |} A Microsoft Docs
description: Ismerje meg, hogyan lehet váltani az tanúsítvány-ujjlenyomatok a tanúsítvány köznapi nevét használó Service Fabric-fürtön.
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
ms.date: 01/01/2019
ms.author: ryanwi
ms.openlocfilehash: e1a52aff0890e32ae739285c0380258939f29597
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312871"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>A tanúsítvány-ujjlenyomat fürt módosítása köznapi név
Nincs két tanúsítványt ugyanazzal az ujjlenyomattal, ami megnehezíti a fürt tanúsítványváltás vagy felügyeleti is rendelkezhet. Több tanúsítvány, azonban lehet a ugyanazzal a névvel vagy a tulajdonos.  Egy tanúsítvány-ujjlenyomatok a tanúsítvány köznapi nevek használatával üzembe helyezett fürt közötti váltás révén jóval egyszerűbb tanúsítványkezelés. Ez a cikk ismerteti a tanúsítvány köznapi nevét használja a tanúsítvány-ujjlenyomat helyett a futó Service Fabric-fürt frissítése.

>[!NOTE]
> Ha két ujjlenyomat deklarálva a sablonban, két üzembe helyezés elvégzéséhez szüksége.  Az első üzembe helyezés előtt ebben a cikkben leírt lépések végrehajtásával végezhető el.  Az első üzembe helyezés beállítása az **ujjlenyomat** tulajdonság a sablonban használt tanúsítvány és eltávolítja a **thumbprintSecondary** tulajdonság.  A második központi telepítést kövesse a cikkben ismertetett lépések.
 
## <a name="get-a-certificate"></a>A tanúsítvány beszerzése
Először kérje le a tanúsítványt egy [hitelesítésszolgáltatói (CA)](https://wikipedia.org/wiki/Certificate_authority).  A tanúsítvány köznapi nevével kell lennie a fürt állomásneve.  Például "myclustername.southcentralus.cloudapp.azure.com."  

Tesztelési célokra, ingyenes és nyílt hitelesítésszolgáltatójától lehetett beolvasni egy hitelesítésszolgáltató által aláírt tanúsítvány.

> [!NOTE]
> Önaláírt tanúsítványokat, beleértve a jönnek létre, ha az Azure Portalon, a Service Fabric-fürt üzembe helyezése nem támogatott.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Töltse fel a tanúsítványt, és telepítse a méretezési csoportban
Az Azure-ban egy Service Fabric-fürtöt helyezünk üzembe egy virtuálisgép-méretezési csoportot a.  Töltse fel a tanúsítványt a key vault és a virtuális gép méretezési csoportot, amelyen a fürt fut, majd telepítse.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName `
    -FilePath $certFilename -Password $PasswordSec

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
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

>[!NOTE]
> Scale set titkos kódok támogatja ugyanazt az erőforrás-azonosító két külön titkos adatait, mivel minden titkos kulcsot egy verzióval ellátott, egyedi erőforrást. 

## <a name="download-and-update-the-template-from-the-portal"></a>Töltse le és frissítse a sablont a portálról
A tanúsítvány telepítve van a mögöttes méretezési csoportot, de is frissítenie kell a Service Fabric-fürt használatára, hogy a tanúsítvány és a közös név.  Most töltse le a sablont a fürt üzembe helyezéséhez.  Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg az erőforráscsoport, a fürtöt.  A **beállítások**válassza **központi telepítések**.  Válassza ki a legutóbbi üzembe helyezés, és kattintson a **sablon megtekintése**.

![Sablonok megtekintése][image1]

A sablon és paraméterek JSON-fájlok letöltése a helyi számítógépen.

Először nyissa meg a paramétereket tartalmazó fájlt egy szövegszerkesztőben, és adja hozzá a következő paraméter értékét:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Ezután nyissa meg a sablon fájlt egy szövegszerkesztőben, és három frissítéseinek támogatásához a tanúsítvány köznapi nevét.

1. Az a **paraméterek** területén adjon hozzá egy *certificateCommonName* paramétert:
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Emellett érdemes eltávolítani a *certificateThumbprint*, előfordulhat, hogy már nem lesz szükség.

2. Az a **Microsoft.Compute/virtualMachineScaleSets** erőforrás, a köznapi név használata helyett az ujjlenyomat tanúsítványbeállítások virtuálisgép-bővítmény frissítése.  A **virtualMachineProfile**->**extensionProfile**->**bővítmények**->**tulajdonságok** -> **beállítások**->**tanúsítvány**, adjon hozzá `"commonNames": ["[parameters('certificateCommonName')]"],` , és távolítsa el `"thumbprint": "[parameters('certificateThumbprint')]",`.
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
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        "typeHandlerVersion": "1.0"
                    }
                },
    ```

3.  Az a **Microsoft.ServiceFabric/clusters** erőforrás, a "2018-02-01" frissítés az API verziója.  Is hozzáadhat egy **certificateCommonNames** beállítás a egy **commonNames** tulajdonságot, és távolítsa el a **tanúsítvány** beállítás (a az ujjlenyomat tulajdonság), ahogy az alábbi Példa:
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
A módosítások elvégzése után a frissített sablon újbóli telepítése.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>További lépések
* Ismerje meg [biztonsági fürt](service-fabric-cluster-security.md).
* Ismerje meg, hogyan [fürttanúsítvány váltása](service-fabric-cluster-rollover-cert-cn.md)
* [Frissítse és fürttanúsítványok kezelése](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
