---
title: Fürt frissítése a tanúsítvány köznapi nevének használatához
description: Megtudhatja, hogyan válthat a Service Fabric-fürtök tanúsítvány ujjlenyomatok használatával a tanúsítvány köznapi neve.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 66c49ccb7b7633d0eff392b676bb381118eb64a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610199"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Fürt módosítása tanúsítvány-ujjlenyomatról köznapi névre
Két tanúsítvány nem rendelkezhet ugyanazzal az ujjlenyomattal, ami megnehezíti a fürttanúsítvány-váltást vagy -kezelést. Több tanúsítványnak azonban lehet ugyanaz a közös neve vagy tárgya.  Az üzembe helyezett fürt tanúsítványujjlenyomatok használatáról a tanúsítvány közös neveinek használatára való váltása sokkal egyszerűbbé teszi a tanúsítványkezelést. Ez a cikk ismerteti, hogyan frissítheti a futó Service Fabric-fürt a tanúsítvány köznapi nevét a tanúsítvány ujjlenyomata helyett.

>[!NOTE]
> Ha két ujjlenyomat deklarált a sablonban, két központi telepítést kell végrehajtania.  Az első üzembe helyezés a jelen cikkben ismertetett lépések előtt történik.  Az első központi telepítés beállítja a **sablonban** lévő ujjlenyomat-tulajdonságát a használt tanúsítványra, és eltávolítja a **thumbprintSecondary** tulajdonságot.  A második központi telepítéshez kövesse a cikkben leírt lépéseket.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Tanúsítvány beszereznie
Először szerezzen be egy tanúsítványt egy [hitelesítésszolgáltatótól](https://wikipedia.org/wiki/Certificate_authority).  A tanúsítvány köznapi nevének a fürt állomásnevének kell lennie.  Például "myclustername.southcentralus.cloudapp.azure.com".  

Tesztelési célokra egy hitelesítésszolgáltató által aláírt tanúsítványt kaphat egy ingyenes vagy nyílt hitelesítésszolgáltatótól.

> [!NOTE]
> Önaláírt tanúsítványok, beleértve a létrehozott, amikor egy Service Fabric-fürt az Azure Portalon, nem támogatottak.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Töltse fel a tanúsítványt, és telepítse a méretezési készlet
Az Azure-ban egy Service Fabric-fürt egy virtuálisgép-méretezési csoporton van telepítve.  Töltse fel a tanúsítványt egy key vaultba, majd telepítse azt a virtuálisgép-méretezési csoportra, amelyen a fürt fut.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName `
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

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

>[!NOTE]
> Méretezési csoport titkos kulcsok nem támogatják ugyanazt az erőforrás-azonosítót két külön titkos kulcsokat, mivel minden titkos kulcsot egy verzióval ellátott, egyedi erőforrás. 

## <a name="download-and-update-the-template-from-the-portal"></a>A sablon letöltése és frissítése a portálról
A tanúsítvány telepítve van az alapul szolgáló méretezési csoport, de azt is frissítenie kell a Service Fabric-fürt, hogy használja a tanúsítványt és a közös név.  Most töltse le a sablont a fürt központi telepítéséhez.  Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg a fürtöt üzemeltető erőforráscsoportot.  A **Beállítások csoportban**válassza a **Központi telepítések**lehetőséget.  Válassza ki a legutóbbi központi telepítést, és kattintson a **Sablon megtekintése gombra.**

![Sablonok megtekintése][image1]

Töltse le a sablont és a paramétereket JSON fájlokat a helyi számítógépre.

Először nyissa meg a paraméterfájlt egy szövegszerkesztőben, és adja hozzá a következő paraméterértéket:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Ezután nyissa meg a sablonfájlt egy szövegszerkesztőben, és készítsen három frissítést a tanúsítvány köznapi nevének támogatásához.

1. A **paraméterek** szakaszban adjon hozzá egy *certificateCommonName* paramétert:
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Szintén érdemes eltávolítani a *tanúsítványtThumbprint*, előfordulhat, hogy már nem hivatkozik az Erőforrás-kezelő sablonban.

2. A **Microsoft.Compute/virtualMachineScaleSets** erőforrásban frissítse a virtuálisgép-bővítményt úgy, hogy a tanúsítványbeállításokban a közös nevet használja az ujjlenyomat helyett.  A **virtualMachineProfile**->**settings**-> `"commonNames": ["[parameters('certificateCommonName')]"],` `"thumbprint": "[parameters('certificateThumbprint')]",`->->**extensions(bővítmény)**->**extensionProfile****tulajdonságtanúsítványában**adja hozzá és távolítsa el a .**properties**
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

3.  A **Microsoft.ServiceFabric/clusters** erőforrásban frissítse az API-verziót "2018-02-01"-re.  Adjon hozzá egy **commonNames** tulajdonsággal rendelkező **certificateCommonNames** beállítást is, és távolítsa el a **tanúsítványbeállítást** (az ujjlenyomat tulajdonsággal), ahogy az a következő példában szerepel:
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

További információt a [Service Fabric-fürt telepítése, amely ujjlenyomat helyett tanúsítványköznevet használ.](https://docs.microsoft.com/azure/service-fabric/service-fabric-create-cluster-using-cert-cn)

## <a name="deploy-the-updated-template"></a>A frissített sablon telepítése
Telepítse újra a frissített sablont a módosítások végrehajtása után.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>További lépések
* További információ a [fürtbiztonságról](service-fabric-cluster-security.md).
* A [fürttanúsítványok görgetése](service-fabric-cluster-rollover-cert-cn.md)
* [Fürttanúsítványok frissítése és kezelése](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
