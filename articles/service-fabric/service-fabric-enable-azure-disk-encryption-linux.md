---
title: Lemeztitkosítás engedélyezve, az Azure Service Fabric Linux-fürtök |} A Microsoft Docs
description: Ez a cikk ismerteti a Linux Azure Service Fabric-fürtcsomópont lemeztitkosítás engedélyezése az Azure Resource Manager és az Azure Key Vault használatával.
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
ms.openlocfilehash: 47b07188d1757708fb494c6a66e93379657e806a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258759"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>A Linux Azure Service Fabric-fürtcsomópont lemeztitkosítás engedélyezve 
> [!div class="op_single_selector"]
> * [Lemeztitkosítás linuxhoz](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Disk Encryption Windows-szolgáltatás](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Ebben az oktatóanyagban elsajátíthatja lesz a Linux Azure Service Fabric-fürtcsomópontok lemeztitkosítással engedélyezése. Kövesse az alábbi lépéseket az egyes csomóponttípusok és virtuálisgép-méretezési csoportok kell. A csomópontok titkosításához, a virtual machine scale sets fogjuk használni az Azure Disk Encryption képesség.

Az útmutató témakörei a következők:

* Érdemes figyelembe vennie, amikor beállítja a lehetővé teszi a lemeztitkosítást, a Service Fabric-fürt virtuálisgép-méretezési Linux alapfogalmakat.
* Mielőtt engedélyezi a lemeztitkosítást a Service Fabric követendő lépések a Linux fürtcsomópont.
* A Linux Service Fabric-fürtcsomópont lemez titkosításának követendő lépéseket.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

 **Önkiszolgáló regisztráció**

A virtuálisgép-méretezési csoportot a lemez titkosítási előzetes regisztrációs van szükség. Ehhez a következő lépések szükségesek:

1. Futtassa a következő parancsot: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Várjon körülbelül 10 percet, amíg az állapot *regisztrált*. Az állapot a következő parancs futtatásával ellenőrizheti:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault**

1. Hozzon létre egy kulcstartót azonos előfizetésben és régióban a méretezési csoportban. Válassza ki a **EnabledForDiskEncryption** házirendet a key vault eléréséhez a PowerShell-parancsmag használatával. A Key Vault felhasználói felület az Azure Portalon a következő parancs használatával is beállíthatja a szabályzatot:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Telepítse a legújabb verzióját a [Azure CLI-vel](/cli/azure/install-azure-cli), amely rendelkezik az új titkosítási parancsait.

3. Telepítse a legújabb verzióját a [Azure SDK-t az Azure PowerShell](https://github.com/Azure/azure-powershell/releases) kiadás. A virtuális gép méretezési csoport engedélyezése az Azure Disk Encryption parancsmagok a következők ([beállítása](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) titkosítási, lekérése ([első](/powershell/module/az.compute/get-azvmssvmdiskencryption)) titkosítási állapotát, és távolítsa el ([letiltása](/powershell/module/az.compute/disable-azvmssdiskencryption)) a méretezési csoport titkosításának példány beállítása.


| Parancs | Version |  source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 vagy újabb | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 vagy újabb | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 vagy újabb | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 vagy újabb | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 vagy újabb | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 vagy újabb | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Lemeztitkosítás támogatott helyzetek
* Virtuálisgép-méretezési csoportokhoz tartozó titkosítási csak a létrehozott felügyelt lemezekkel rendelkező méretezési csoportok esetében támogatott. Natív (vagy nem felügyelt) lemezek méretezési csoportok esetében nem támogatott.
* Titkosítás és a titkosítás letiltása a virtuálisgép-méretezési csoportok az Linux operációsrendszer- és kötetek esetében támogatottak. 
* Virtuális gép (VM) rendszerképeit alaphelyzetbe állítani, és a frissítési műveletek virtuálisgép-méretezési csoportokhoz tartozó nem támogatottak az aktuális előzetes verzióban érhető el.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Hozzon létre egy új fürtöt, és a lemeztitkosítás engedélyezve

A következő parancsok használatával hozzon létre egy fürtöt, és a egy Azure Resource Manager-sablon és a egy önaláírt tanúsítvány használatával lemeztitkosítás engedélyezve.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba  

Jelentkezzen be a következő parancsokat:

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Használja az egyéni sablont, amely már rendelkezik 

Hozzon létre egy egyéni sablon van szüksége, ha kifejezetten ajánljuk, hogy használhatja a sablonok egyikét a [Azure Service Fabric-fürt létrehozása sablonminták](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) lapot. 

Ha már rendelkezik egy egyéni sablont, és ellenőrizze, hogy minden három tanúsítvánnyal kapcsolatos paraméterekkel a sablon és a alkalmazásparaméter-fájlt a következő nevesített. Emellett ellenőrizze, hogy az értékek a következők null értékű:

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

Csak adatok lemeztitkosítás a Linux virtuális gép méretezési csoportok esetében támogatott, mert hozzá kell adnia egy adatlemezt a Resource Manager-sablon használatával. Az adatok lemez üzembe helyezése sablon a következő frissítés:

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

Íme az egyenértékű CLI-paranccsal. Módosítsa az értékeket a declare utasítást a megfelelő értékekre. A parancssori felület támogatja a többi paramétert, amely támogatja az előző PowerShell-parancsot.

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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Linux-példányra adatlemez csatlakoztatása
Egy virtuálisgép-méretezési csoportot a titkosítást a folytatás előtt győződjön meg arról, az új adatok lemez megfelelően csatlakoztatva van. Jelentkezzen be a Linux-fürt virtuális Gépet, és futtassa a **LSBLK** parancsot. A kimenet meg kell jelennie a hozzáadott adatlemez a **csatlakoztatási pont** oszlop.


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>A Linux Service Fabric-fürtön történő üzembe helyezése
Egy alkalmazást a fürtön üzembe helyezéséhez kövesse a lépéseket és útmutatójában [a rövid útmutató: Linux-tárolók üzembe helyezése a Service Fabric](service-fabric-quickstart-containers-linux.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>A korábban létrehozott virtuálisgép-méretezési csoportok a lemeztitkosítás engedélyezve
Ahhoz, hogy a virtuálisgép-méretezési csoport lemeztitkosítás állítja be az előző lépések során a következő parancsok futtatásával keresztül létrehozott:
 
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

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Ha a lemeztitkosítás engedélyezve van a Linux virtuális gép méretezési csoportokhoz tartozó ellenőrzése
Egy méretezési csoportban lévő egy teljes virtuálisgép-méretezési csoportot, vagy minden olyan példány állapotának lekéréséhez futtassa a következő parancsokat.
Emellett, jelentkezzen be a Linux-fürt virtuális Gépet, és futtassa a **LSBLK** parancsot. A kimenet meg kell jelennie a hozzáadott adatlemez a **csatlakoztatási pont** oszlopot, és a **típus** oszlop olvassa el *Crypt*.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Tiltsa le a Service Fabric-fürt virtuális gép méretezési lemeztitkosítás
Tiltsa le a lemeztitkosítás állítsa a következő parancsok futtatásával virtuálisgép-méretezési csoportot. Vegye figyelembe, hogy a teljes virtuálisgép-méretezési csoportot és a egy egyéni példány nem érvényes lemeztitkosítás letiltása.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>További lépések
Ezen a ponton kell egy biztonságos fürt, és tudja, hogyan engedélyezheti és tilthatja le a Service Fabric-fürtcsomópontok és a virtual machine scale sets lemeztitkosítás. A Linux Service Fabric-fürtcsomópont hasonló útmutatásért lásd: [Disk Encryption Windows](service-fabric-enable-azure-disk-encryption-windows.md). 
