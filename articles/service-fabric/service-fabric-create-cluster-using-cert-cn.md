---
title: Hozzon létre egy Azure Service Fabric-fürt használata a tanúsítvány egyszerű neve |} Microsoft Docs
description: 'Útmutató: a tanúsítvány köznapi neve a sablon használatával a Service Fabric-fürt létrehozása.'
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi;aljo
ms.openlocfilehash: 84d9ae29840841d930fa70dc30dcda800a1b46c6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>A Service Fabric-fürt által használt tanúsítvány egyszerű neve helyett ujjlenyomat telepítése
Nincs két tanúsítványt ugyanazzal az ujjlenyomattal, ami megnehezíti a fürt tanúsítványváltást vagy felügyeleti rendelkezhet. Több tanúsítvány azonban rendelkezhet ugyanazzal a névvel vagy tulajdonos.  A tanúsítvány köznapi nevek fürt teszi Tanúsítványkezelő jóval egyszerűbbé válik. Ez a cikk ismerteti, hogyan kell használni a tanúsítvány egyszerű neve helyett a tanúsítvány ujjlenyomatát a Service Fabric-fürt központi telepítése.
 
## <a name="get-a-certificate"></a>Tanúsítvány beszerzése
Szereznie egy tanúsítványt egy [hitelesítésszolgáltató-tanúsítvány](https://wikipedia.org/wiki/Certificate_authority).  A tanúsítvány köznapi nevének kell lennie a fürt állomásnevét.  Például "myclustername.southcentralus.cloudapp.azure.com."  

Tesztelési célokra egy hitelesítésszolgáltató által aláírt tanúsítvány sikerült szabad vagy nyitott hitelesítésszolgáltatójától.

> [!NOTE]
> Önaláírt tanúsítványokat, beleértve a jönnek létre, ha az Azure portálon, a Service Fabric-fürt üzembe helyezése nem támogatottak.

## <a name="upload-the-certificate-to-a-key-vault"></a>Töltse fel a tanúsítványt a kulcstároló
Az Azure Service Fabric-fürt virtuálisgép-méretezési csoport van telepítve.  Töltse fel a tanúsítványt a kulcstároló.  Amikor a fürt telepíti, a tanúsítvány telepítése a virtuálisgép-méretezési csoport a fürtön futó.

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

## <a name="download-and-update-a-sample-template"></a>Töltse le és mintasablon frissítése
Ebben a cikkben az a [biztonságos 5-csomópontot tartalmazó fürtben példa](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) sablon és a sablon paramétereit. Töltse le a *azuredeploy.json* és *azuredeploy.parameters.json* fájlokat a számítógépre.

### <a name="update-parameters-file"></a>Paraméterek fájl frissítése
Első lépésként nyissa meg a *azuredeploy.parameters.json* fájlt egy szövegszerkesztőben, és adja hozzá a következő paraméter értéke:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Következő lépésként állítsa a *certificateCommonName*, *sourceVaultValue*, és *certificateUrlValue* paraméterértékeket kíván az előző parancsfájl által visszaküldött:
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
Ezután nyissa meg a *azuredeploy.json* fájlt egy szövegszerkesztőben, és három frissíti a támogatja a tanúsítvány egyszerű neve.

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

2. Állítsa a *sfrpApiVersion* "2018-02-01" változót:
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. Az a **Microsoft.Compute/virtualMachineScaleSets** erőforrás, a köznapi név tanúsítvány beállításai helyett az ujjlenyomat használandó virtuálisgép-bővítmény frissítése.  A **virtualMachineProfile**->**extenstionProfile**->**bővítmények**->**tulajdonságai** -> **beállítások**->**tanúsítvány**, adja hozzá `"commonNames": ["[parameters('certificateCommonName')]"],` , és távolítsa el `"thumbprint": "[parameters('certificateThumbprint')]",`.
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

4.  Az a **Microsoft.ServiceFabric/clusters** erőforrás, a frissítés az API-verziója "2018-02-01".  Is hozzáadhat egy **certificateCommonNames** beállításnál egy **commonNames** tulajdonság, és távolítsa el a **tanúsítvány** beállítás (a az ujjlenyomat tulajdonság), mint a következő Példa:
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
* További tudnivalók [fürt biztonsági](service-fabric-cluster-security.md).
* Megtudhatja, hogyan [helyettesítő fürt tanúsítvány](service-fabric-cluster-rollover-cert-cn.md)
* [Fürt tanúsítványok frissítése és kezelése](service-fabric-cluster-security-update-certs-azure.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png