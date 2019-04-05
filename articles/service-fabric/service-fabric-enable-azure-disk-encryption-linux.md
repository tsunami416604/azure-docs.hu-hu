---
title: Lemeztitkosítás engedélyezve, az Azure Service Fabric Linux-fürtök |} A Microsoft Docs
description: Ez a cikk ismerteti a Service Fabric-fürt méretezési beállítása az Azure-ban Azure Resource Manager, az Azure Key Vault használatával a lemeztitkosítás engedélyezve.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2019
ms.author: aljo
ms.openlocfilehash: f580bf02b222f01a3d5aad1254f208791ea22b38
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046979"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>A service fabric Linux-fürtcsomópontokon lemeztitkosítás engedélyezve 
> [!div class="op_single_selector"]
> * [Lemeztitkosítás linuxhoz](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Disk Encryption Windows-szolgáltatás](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

A Service Fabric Linux-fürtcsomópontokon lemeztitkosítás engedélyezve az alábbi lépésekkel. Az egyes típusok/virtuális gép méretezési csoportok a csomópont nem kell. A csomópontok titkosításához, használja azt az Azure Disk Encryption funkció, virtuálisgép-méretezési csoportokon.

Az útmutató a következő eljárásokat ismerteti:

* Főbb fogalmak, amely a lemeztitkosítást a virtuálisgép-méretezési csoport Service Fabric Linux-fürt engedélyezése ki figyelembe kell beállítani.
* Előfeltételek lépéseket kell követni a Service Fabric-fürt Linux virtuális gép méretezési csoporthoz lemeztitkosítással engedélyezése előtt.
* Állítsa be a lemeztitkosítást a virtuálisgép-méretezési csoport Service Fabric Linux-fürt engedélyezése követendő lépéseket.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

* **Regisztrációs** – használatához a virtuális gép méretezési készlet lemez titkosítási előzetes regisztrációs igényel
* Önálló regisztrálhatja előfizetését futtassa az alábbi lépéseket: 
```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* Várjon, amíg az állapot körülbelül 10 percet, "Regisztrálva". Az állapot a következő parancs futtatásával ellenőrizheti: 
```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```
* **Az Azure Key Vault** – hozzon létre egy KeyVault azonos előfizetésben és régióban, a virtuálisgép-méretezési csoport állítsa be, és állítsa be a hozzáférési házirend "EnabledForDiskEncryption" a fiókidentitás a PS-parancsmag segítségével. A szabályzat a KeyVault felhasználói felület segítségével az Azure Portalon is állíthatja be: 
```powershell
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* Telepítse a legújabb [Azure CLI-vel](/cli/azure/install-azure-cli) , amely rendelkezik az új titkosítási parancsait.
* Telepítse a legújabb verzióját, [Azure SDK-t az Azure PowerShell](https://github.com/Azure/azure-powershell/releases) kiadás. Az alábbiakban a virtuális gép méretezési ADE parancsmagjaival engedélyezhetik ([beállítása](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) titkosítási, lekérése ([első](/powershell/module/az.compute/get-azvmssvmdiskencryption)) titkosítási állapotát, és távolítsa el ([letiltása](/powershell/module/az.compute/disable-azvmssdiskencryption)) méretezési titkosításának set-példány. 

| Parancs | Verzió |  Forrás  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 vagy újabb | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 vagy újabb | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 vagy újabb | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 vagy újabb | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 vagy újabb | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 vagy újabb | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Lemeztitkosítás támogatott helyzetek
* Virtual machine scale set titkosítási csak a managed disks szolgáltatással létrehozott, és nem támogatott natív (vagy nem felügyelt) lemezek méretezési csoportok a méretezési csoportok esetében támogatott.
* Virtual machine scale set titkosítási adatmennyiség Linux virtuálisgép-méretezési csoport esetében támogatott. Az operációs rendszer lemeztitkosítás nem támogatott Linux rendszeren az aktuális előzetes verzióban érhető el.
* Virtuálisgép-méretezési VM-rendszerképeit alaphelyzetbe állítani, és a frissítési műveletek nem támogatottak az aktuális előzetes verzióban érhető el.


### <a name="create-new-linux-cluster-and-enable-disk-encryption"></a>Új Linux-fürt létrehozása és a lemeztitkosítás engedélyezve

Az alábbi parancsokkal hozható létre fürt és az Azure Resource Manager-sablon & önaláírt tanúsítvány használatával lemeztitkosítás engedélyezve.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba  

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Használja az egyéni sablont, amely már rendelkezik 

Ha hozzon létre egy egyéni sablont, hogy illeszkedjen az igényeihez van szüksége, azt javasoljuk, hogy az egyik elérhető sablont megkezdése a [az azure service fabric-sablonminták](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) Linux-fürt számára. 

Ha már rendelkezik egy egyéni sablont, majd győződjön meg arról, hogy ellenőrizze, hogy minden a három tanúsítvánnyal kapcsolatos paramétereket a sablon és a paraméterfájl elnevezése a következő és értékek a következők null értékű.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Óta Linux virtuálisgép-méretezési csoport – csak adatokat lemeztitkosítás támogatott, adatlemez az Azure Resource Manager-sablon használatával hozzá kell adnunk a. Az adatszolgáltatás lemez az alábbi, a sablon frissítésére:

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
```
 

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Íme az előbbiek az egyenértékű CLI-paranccsal. Módosítsa az értékeket a declare utasítást a megfelelő értékekre. Parancssori felület támogatja a többi paramétert, amely támogatja a fenti powershell-parancsot.

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

#### <a name="linux-data-disk-mounting"></a>Linux-adatok lemez csatlakoztatása
Linux virtuálisgép-méretezési csoport a titkosítást a folytatás előtt ellenőrizze, hogy a hozzáadott adatlemez megfelelően van csatlakoztatva, vagy nem kell. Jelentkezzen be a Linux-fürt virtuális Gépet, és futtassa LSBLK parancsot. A kimeneti meg kell jelennie a hozzáadott adatlemez csatlakoztatási pont oszlop alapján.


#### <a name="deploy-application-to-linux-service-fabric-cluster"></a>A Linux Service Fabric-fürt üzembe helyezése
Kövesse a lépéseket és útmutatás [alkalmazás üzembe helyezése a fürtön](service-fabric-quickstart-containers-linux.md)


#### <a name="enable-disk-encryption-for-service-fabric-linux-cluster-virtual-machine-scale-set-created-above"></a>A fentiekben létrehozott Service Fabric Linux-fürt virtuális gép méretezési lemeztitkosítás engedélyezve
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

#### <a name="validate-if-disk-encryption-enabled-for-linux-virtual-machine-scale-set"></a>Ha a lemeztitkosítás engedélyezve a Linuxos virtuálisgép-méretezési csoport érvényesítése.
Egy teljes virtuálisgép-méretezési csoportot, vagy a méretezési csoportban lévő bármely példány virtuális gép állapotának lekéréséhez. Tekintse meg az alábbi parancsokat.
Emellett felhasználó jelentkezzen be a Linux-fürt virtuális gép és LSBLK parancs futtatásához. A kimeneti meg kell jelennie a hozzáadott adatlemez a csatlakoztatási pont és típusú oszlop, a titkosítási hozzáadott adatlemez.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Tiltsa le a Service Fabric-fürt virtuálisgép-méretezési lemeztitkosítás 
Tiltsa le lemeztitkosítás vonatkozik, és a példány nem a teljes virtuálisgép-méretezési csoporthoz 

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>További lépések
Ezen a ponton rendelkezik egy biztonságos fürthöz Service Fabric-fürt Linux virtuálisgép-méretezési lemeztitkosítás engedélyezését vagy letiltását annak. Ezután [Windows a lemeztitkosítás](service-fabric-enable-azure-disk-encryption-windows.md) 
