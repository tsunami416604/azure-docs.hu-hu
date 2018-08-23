---
title: Hozzon létre egy Azure Service Fabric-fürt tanúsítvány köznapi nevét használó |} A Microsoft Docs
description: Ismerje meg, hogyan hozzon létre egy olyan sablon alapján a tanúsítvány köznapi nevét használó Service Fabric-fürtön.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: c4c60cccb890c883e9e57c9f146cc93aae99f224
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42058431"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Ujjlenyomat helyett a tanúsítvány köznapi nevét használó Service Fabric-fürt üzembe helyezése
Nincs két tanúsítványt ugyanazzal az ujjlenyomattal, ami megnehezíti a fürt tanúsítványváltás vagy felügyeleti is rendelkezhet. Több tanúsítvány, azonban lehet a ugyanazzal a névvel vagy a tulajdonos.  Tanúsítvány köznapi nevek a fürtök teszi tanúsítványok kezelése sokkal egyszerűbb. Ez a cikk ismerteti, hogyan helyezhet üzembe Service Fabric-fürt helyett a tanúsítvány ujjlenyomata a tanúsítvány köznapi nevét használni.
 
## <a name="get-a-certificate"></a>A tanúsítvány beszerzése
Először kérje le a tanúsítványt egy [hitelesítésszolgáltatói (CA)](https://wikipedia.org/wiki/Certificate_authority).  A tanúsítvány köznapi nevével kell lennie a fürt állomásneve.  Például "myclustername.southcentralus.cloudapp.azure.com."  

Tesztelési célokra, ingyenes és nyílt hitelesítésszolgáltatójától lehetett beolvasni egy hitelesítésszolgáltató által aláírt tanúsítvány.

> [!NOTE]
> Önaláírt tanúsítványokat, beleértve a jönnek létre, ha az Azure Portalon, a Service Fabric-fürt üzembe helyezése nem támogatott.

## <a name="upload-the-certificate-to-a-key-vault"></a>Töltse fel a tanúsítványt a key vault
Az Azure-ban egy Service Fabric-fürtöt helyezünk üzembe egy virtuálisgép-méretezési csoportot a.  Töltse fel a tanúsítványt a key vault.  Amikor üzembe helyezi a fürtöt, a tanúsítvány telepítése a virtuális gép méretezési csoportot, amely a fürt fut, a.

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
```

## <a name="download-and-update-a-sample-template"></a>Töltse le és a egy minta-sablon frissítéséhez
Ez a cikk a [biztonságos fürt 5 csomópontos példa](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) sablon és a sablon paramétereit. Töltse le a *azuredeploy.json* és *azuredeploy.parameters.json* fájlokat a számítógépre.

### <a name="update-parameters-file"></a>Frissítse a paramétereket tartalmazó fájlt
Először nyissa meg a *azuredeploy.parameters.json* fájlt egy szövegszerkesztőben, és adja hozzá a következő paraméter értékét:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Következő lépésként állítsa a *certificateCommonName*, *sourceVaultValue*, és *certificateUrlValue* azokat a fenti parancsfájl által visszaadott paraméterértékeket:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>A sablon fájl frissítése
Ezután nyissa meg a *azuredeploy.json* fájlt egy szövegszerkesztőben, és három frissítéseinek támogatásához a tanúsítvány köznapi nevét.

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

2. Az értékét állítsa be a *sfrpApiVersion* "2018-02-01" változó:
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. Az a **Microsoft.Compute/virtualMachineScaleSets** erőforrás, a köznapi név használata helyett az ujjlenyomat tanúsítványbeállítások virtuálisgép-bővítmény frissítése.  A **virtualMachineProfile**->**extenstionProfile**->**bővítmények**->**tulajdonságai** -> **beállítások**->**tanúsítvány**, hozzáadása 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    Távolítsa el `"thumbprint": "[parameters('certificateThumbprint')]",`.

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

4.  Az a **Microsoft.ServiceFabric/clusters** erőforrás, a "2018-02-01" frissítés az API verziója.  Is hozzáadhat egy **certificateCommonNames** beállítás a egy **commonNames** tulajdonságot, és távolítsa el a **tanúsítvány** beállítás (a az ujjlenyomat tulajdonság), ahogy az alábbi Példa:
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
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>További lépések
* Ismerje meg [biztonsági fürt](service-fabric-cluster-security.md).
* Ismerje meg, hogyan [fürttanúsítvány váltása](service-fabric-cluster-rollover-cert-cn.md)
* [Frissítse és fürttanúsítványok kezelése](service-fabric-cluster-security-update-certs-azure.md)
* A tanúsítvány kezelésének leegyszerűsítése [tanúsítvány ujjlenyomatát a fürt módosítása köznapi név](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
