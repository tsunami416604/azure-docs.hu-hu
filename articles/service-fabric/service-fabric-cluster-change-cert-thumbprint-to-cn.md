---
title: Fürt frissítése a tanúsítvány köznapi nevének használatára
description: Megtudhatja, hogyan válthat egy Service Fabric-fürtöt a tanúsítvány ujjlenyomatai megfelelnek a tanúsítvány köznapi nevének használatával.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 1926b0501766eb0a5fe086ceada0c9bf45e3dcf6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272627"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Fürt módosítása tanúsítvány-ujjlenyomatról köznapi névre
Két tanúsítvány nem rendelkezhet ugyanazzal az ujjlenyomattal, ami lehetővé teszi a fürt tanúsítványainak átváltását vagy felügyeletét. Több tanúsítvány, azonban ugyanaz a köznapi név vagy a tárgy lehet.  Ha egy telepített fürtöt a tanúsítvány ujjlenyomatai megfelelnek használ a tanúsítványok köznapi nevének használatára, a Tanúsítványkezelő sokkal egyszerűbbé válik. Ez a cikk azt ismerteti, hogyan lehet frissíteni egy futó Service Fabric fürtöt, hogy a Tanúsítvány ujjlenyomata helyett a tanúsítvány köznapi nevét használja.

>[!NOTE]
> Ha két ujjlenyomata van deklarálva a sablonban, két üzemelő példányt kell végrehajtania.  Az első üzembe helyezés a cikk lépéseinek követése előtt történik.  Az első telepítés beállítja a sablon **ujjlenyomat** tulajdonságát a használt tanúsítványra, és eltávolítja a **thumbprintSecondary** tulajdonságot.  A második központi telepítéshez kövesse a cikk lépéseit.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Tanúsítvány beszerzése
Először kérje le a tanúsítványt egy [hitelesítésszolgáltatótól (CA)](https://wikipedia.org/wiki/Certificate_authority).  A tanúsítvány köznapi nevének a saját egyéni tartományának kell lennie, és egy tartományregisztráló alapján kell megvásárolnia. Például: "azureservicefabricbestpractices.com"; azok, akik nem Microsoft-alkalmazottak, nem tudnak kiépíteni tanúsítványokat az MS-tartományokhoz, így nem használhatja az LB vagy a Traffic Manager DNS-neveit a tanúsítvány általános neveként, és létre kell hoznia egy [Azure DNS zónát](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) , ha az egyéni tartomány feloldható az Azure-ban. Ha azt szeretné, hogy a portál tükrözze a fürt egyéni tartományának aliasát, azt is be kell jelentenie, hogy a fürt "z".

Tesztelési célból a HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítványt egy ingyenes vagy nyitott hitelesítésszolgáltatótól szerezheti be.

> [!NOTE]
> Az önaláírt tanúsítványok, beleértve azokat is, amelyek akkor jönnek létre, amikor egy Service Fabric fürtöt telepítenek a Azure Portal, nem támogatottak. 

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Töltse fel a tanúsítványt, és telepítse a méretezési csoportba
Az Azure-ban egy Service Fabric-fürt üzembe helyezése egy virtuálisgép-méretezési csoporton történik.  Töltse fel a tanúsítványt egy kulcstartóba, majd telepítse azt a virtuálisgép-méretezési csoportba, amelyen a fürt fut.

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
> A méretezési csoport titka nem támogatja ugyanazt az erőforrás-azonosítót két külön titok esetében, mivel mindegyik titkos kód egy verzióval ellátott, egyedi erőforrás. 

## <a name="download-and-update-the-template-from-the-portal"></a>A sablon letöltése és frissítése a portálról
A tanúsítvány telepítve van a mögöttes méretezési csoporton, de frissítenie kell a Service Fabric-fürtöt a tanúsítvány és a köznapi neve használatára is.  Most töltse le a fürt üzembe helyezéséhez tartozó sablont.  Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon a fürtöt tároló erőforráscsoporthoz.  A **Beállítások**területen válassza a **központi telepítések**lehetőséget.  Válassza ki a legutóbbi telepítést, és kattintson a **sablon megtekintése**elemre.

![Sablonok megtekintése][image1]

Töltse le a sablon és a paraméterek JSON-fájljait a helyi számítógépre.

Először nyissa meg a parameters (paraméterek) fájlt egy szövegszerkesztőben, és adja hozzá a következő paraméter értékét:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Ezután nyissa meg a sablonfájlt egy szövegszerkesztőben, és három frissítést készítsen a tanúsítvány köznapi nevének támogatásához.

1. A **Parameters (paraméterek** ) szakaszban adjon hozzá egy *certificateCommonName* paramétert:
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Vegye fontolóra a *certificateThumbprint*eltávolítását is, ezért előfordulhat, hogy a Resource Manager-sablon nem hivatkozik rá.

2. A **Microsoft. számítási/virtualMachineScaleSets** erőforrásban frissítse a virtuálisgép-bővítményt, hogy az ujjlenyomat helyett az általános nevet használja a tanúsítvány beállításainál.  A **virtualMachineProfile**->**extensionProfile**->-**bővítmények**->**Tulajdonságok**->**beállításai****certificate** `"commonNames": ["[parameters('certificateCommonName')]"],` `"thumbprint": "[parameters('certificateThumbprint')]",`tanúsítvány, Hozzáadás és Eltávolítás elemre.->
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

3.  A **Microsoft. ServiceFabric/Clusters** erőforrásban frissítse az API verzióját "2018-02-01"-re.  Vegyen fel egy **certificateCommonNames** -beállítást egy **commonNames** tulajdonsággal, és távolítsa el a **tanúsítvány** beállítását (az ujjlenyomat tulajdonsággal), ahogy az alábbi példában is látható:
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

További információ: [Service Fabric-fürt üzembe helyezése, amely az ujjlenyomat helyett a tanúsítvány köznapi nevét használja.](https://docs.microsoft.com/azure/service-fabric/service-fabric-create-cluster-using-cert-cn)

## <a name="deploy-the-updated-template"></a>A frissített sablon üzembe helyezése
A módosítások végrehajtása után telepítse újra a frissített sablont.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>További lépések
* További információ a [fürt biztonságáról](service-fabric-cluster-security.md).
* Tudnivalók a [fürt tanúsítványainak átváltásáról](service-fabric-cluster-rollover-cert-cn.md)
* [Fürt tanúsítványainak frissítése és kezelése](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
