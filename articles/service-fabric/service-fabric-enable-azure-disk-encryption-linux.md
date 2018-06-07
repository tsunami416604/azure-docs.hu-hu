---
title: A service fabric Linux-fürtök lemeztitkosítás engedélyezése |} Microsoft Docs
description: Ez a cikk ismerteti a Service Fabric-fürt bővített beállítása az Azure-ban Azure Resource Manager, az Azure Key Vault használatával lemeztitkosítás engedélyezése.
services: service-fabric
documentationcenter: .net
author: v-viban
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/24/2018
ms.author: v-viban
ms.openlocfilehash: 46f7f88768ab7ae9d84f392f340750865fef3b96
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655248"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>Service fabric Linux fürtcsomópontok lemeztitkosítás engedélyezése 
> [!div class="op_single_selector"]
> * [A Linux rendszerhez használt adatok titkosítása](service-fabric-enable-azure-disk-encryption-linux.md)
> * [A Windows lemeztitkosítás](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

A Service Fabric Linux fürtcsomópontokon lemez-titkosítás engedélyezéséhez az alábbi lépésekkel. Meg kell tennie ezek minden csomópont típusú virtuális gépek méretezési készlet. A csomópontok titkosítására, azt fogja használni, az Azure Disk Encryption képességet, a virtuálisgép-méretezési készlet.

Az útmutató a következő eljárásokat ismerteti:

* Alapfogalmak, amelyekre szüksége van ahhoz, hogy a Service Fabric-fürt Linux virtuálisgép-méretezési lemeztitkosítás ki tudják, hogy állítsa be.
* Előfeltételek lépéseket kell követni ahhoz, hogy a lemez titkosítása a Service Fabric-fürt Linux virtuálisgép-méretezési készlet.
* Állítsa be a Service Fabric-fürt Linux virtuálisgép-méretezési lemez titkosításának követendő lépéseket.


## <a name="prerequisites"></a>Előfeltételek

1. **Regisztrációs** – kíván használni, virtuális gép méretezési készlet lemez titkosítási preview regisztrációs van szükség.
2. Az előfizetés a következő lépések futtatásával önálló regisztrálhatja: 
```Powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
3. Várjon, amíg a körülbelül 10 percig amíg az állapot szerint "Regisztrált". Az állapot a következő parancs futtatásával ellenőrizheti: 
```Powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```
4. **Az Azure Key Vault** -hozzon létre egy KeyVault az azonos előfizetésbe és azonos térségbe a virtuálisgép-méretezési állítsa be, és a hozzáférési házirend "EnabledForDiskEncryption" be a KeyVault a PS-parancsmag használatával. A házirend a KeyVault felhasználói felületen az Azure portálon is állíthatja be: 
```Powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
5. Telepítse a legújabb [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) , amely az új titkosítási parancsokat tartalmaz.
6. Telepítse a legújabb verzióját [Azure SDK-t az Azure PowerShell](https://github.com/Azure/azure-powershell/releases) kiadási. Az alábbiakban a VMSS ADE-parancsmagjaival engedélyezhetik ([beállítása](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1)) titkosítási, beolvasása ([beolvasása](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) titkosítási állapotát, és távolítsa el ([letiltása](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) skálán titkosítási állítson be egy példányt. 

| Parancs | Verzió |  Forrás  |
| ------------- |-------------| ------------|
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 vagy újabb | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryptionStatus   | 3.4.0 vagy újabb | AzureRM.Compute |
| Disable-AzureRmVmssDiskEncryption   | 3.4.0 vagy újabb | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryption   | 3.4.0 vagy újabb | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryption   | 3.4.0 vagy újabb | AzureRM.Compute |
| Set-AzureRmVmssDiskEncryptionExtension   | 3.4.0 vagy újabb | AzureRM.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Adatok titkosítása támogatott forgatókönyvek
* Virtuális gép méretezési készlet titkosítási csak felügyelt lemezek létre, és nem támogatott a natív (vagy nem felügyelt) lemez méretezési csoportok méretezési csoportok esetén támogatott.
* Virtuális gép méretezési készlet titkosítási Linux virtuálisgép-méretezési csoport az adatmennyiség esetén támogatott. Az aktuális előzetes Linux operációsrendszer-lemez titkosítása nem támogatott.
* Virtuálisgép-méretezési csoport virtuális gép új lemezképet készíteni, és a frissítési műveletek nem támogatottak az aktuális előzetes verzió.


### <a name="create-new-linux-cluster-and-enable-disk-encryption"></a>Új Linux-fürt létrehozása és engedélyezése a lemez titkosítása

Használja a következő parancsokat a fürt létrehozása és engedélyezése Azure Resource Manager sablonnal lemeztitkosítás & önaláírt tanúsítványt.

### <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba  

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Az egyéni sablon használata, amely már rendelkezik 

Ha egy egyéni sablont az igényeinek szerzői van szüksége, javasoljuk, hogy az egyik elérhető sablont megkezdése a [azure service fabric sablon minták](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) Linux-fürt számára. 

Ha már rendelkezik egy egyéni sablont, majd győződjön meg arról, hogy ellenőrizze, hogy minden a tanúsítványokkal kapcsolatos paramétereket a sablonban és a paraméterfájl elnevezése a következő és értékek a következők null értékű.

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

Óta Linux virtuálisgép-méretezési csoport – csak lemez adattitkosítás támogatott ezért ellenőriznünk kell a Azure Resource Manager sablonnal adatlemez hozzáadása. A sablon a következő adatszolgáltatás lemez frissítésére:

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
 

```Powershell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Ez az egyenértékű parancssori felület parancsot ugyanerre. Módosítsa az értékeket a declare utasítást a megfelelő értékeket. Parancssori felülete támogatja az összes többi paraméter, amely támogatja a fenti powershell-parancsot.

```CLI

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

#### <a name="linux-data-disk-mounting"></a>Linux adatok lemez csatlakoztatása
Linux virtuálisgép-méretezési csoport titkosítás a folytatás előtt győződjön meg arról, hogy a felvett adatok lemez megfelelően van csatlakoztatva, vagy nem kell. Linux-fürt virtuális gép és futtatási LSBLK parancs bejelentkezni. A kimeneti jelenítsen meg, hogy a felvett adatok lemez csatlakoztatási pont oszlop alapján.


#### <a name="deploy-application-to-linux-service-fabric-cluster"></a>Alkalmazás Linux Service Fabric-fürt telepítése
Kövesse a lépéseket és útmutatást is [telepítheti az alkalmazást a fürtön](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-quickstart-containers-linux)


#### <a name="enable-disk-encryption-for-service-fabric-linux-cluster-virtual-machine-scale-set-created-above"></a>A Service Fabric-fürt Linux virtuálisgép-méretezési csoport a fenti létrehozott lemeztitkosítás engedélyezése
 
```Powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```CLI

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

#### <a name="validate-if-disk-encryption-enabled-for-linux-virtual-machine-scale-set"></a>Annak ellenőrzéséhez, hogy a lemez titkosítása engedélyezve a Linux virtuálisgép-méretezési beállítása.
Egy teljes virtuálisgép-méretezési csoport vagy a tetszőleges példányra Virtuálisgép-méretezési csoportban lévő állapotának beolvasása. Tekintse meg az alábbi parancsok.
Felhasználói emellett Linux-fürt Virtuálisgép jelentkezni, és a LSBLK parancsot. A kimeneti kell megjelenítése, hogy a felvett adatok lemez a csatlakoztatási pont oszlop és a Type column, a titkosítási hozzáadott adatlemez.

```Powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```CLI

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```



#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Tiltsa le a Service Fabric-fürt virtuálisgép-méretezési csoport lemeztitkosítás 
Tiltsa le a lemeztitkosítás teljes virtuálisgép-méretezési csoport és példány által nem vonatkozik. 

```Powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>További lépések
Ezen a ponton hogy a biztonságos fürt engedélyezését vagy letiltását az adatok titkosítása a Service Fabric-fürt Linux virtuálisgép-méretezési módját. Ezt követően [Windows lemeztitkosítás](service-fabric-enable-azure-disk-encryption-windows.md) 

