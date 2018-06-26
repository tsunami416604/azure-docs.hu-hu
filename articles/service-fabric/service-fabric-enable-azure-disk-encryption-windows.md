---
title: Lemeztitkosítás engedélyezni a Service Fabric Windows-fürtök |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan lemeztitkosítás Azure Service Fabric fürt csomópontján engedélyezése az Azure Resource Manager és az Azure Key Vault használatával.
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
ms.date: 05/23/2018
ms.author: v-viban
ms.openlocfilehash: 58f9481fa27f44de30fd80fe52e9b6d06d341b41
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750893"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Service Fabric Windows fürtcsomópontok lemeztitkosítás engedélyezése 
> [!div class="op_single_selector"]
> * [A Windows lemeztitkosítás](service-fabric-enable-azure-disk-encryption-windows.md)
> * [A Linux rendszerhez használt adatok titkosítása](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Az alábbi lépések segítségével lemeztitkosítás Azure Service Fabric Windows fürtcsomópontokon engedélyezni. Meg kell tennie ehhez az egyes csomóponttípusok vagy virtuálisgép-méretezési készlet. A csomópontok titkosításához, a virtuálisgép-méretezési csoportok az Azure Disk Encryption funkció fogja használni.

Az útmutató a következő eljárásokat ismerteti:

* A virtuálisgép-méretezési csoportok Service Fabric Windows-fürtök esetén a lemez titkosítása engedélyező főbb fogalmait.
* Előfeltétel-ellenőrzési lépést kell végrehajtania, mielőtt engedélyezné a virtuális gépen lemeztitkosítás méretezhető Service Fabric Windows fürtök-készletet.
* Engedélyezheti vagy letilthatja a lépést a Service Fabric Windows fürtök virtuálisgép-méretezési csoportok lemeztitkosítás.


## <a name="prerequisites"></a>Előfeltételek
1. Önálló regisztrálása az előfizetés a következő parancs beírásával:

   ```PowerShell
   Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
   ```
   
   Várjon körülbelül 10 percig, amíg az állapot `Registered`. Az állapot a következő parancsok futtatásával ellenőrizheti:

   ```PowerShell
   Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
   ```

2. Hozzon létre egy kulcstartót előfizetéshez és a terület, a méretezési készlet. Állítsa be a hozzáférési házirend `EnabledForDiskEncryption` a a key vault a PowerShell-parancsmag használatával. A házirend beállíthatja úgy, hogy a felhasználói felületen Azure Key Vault az Azure portálon is.

   ```PowerShell
   Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
   ```

3. Telepítse [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest), amely rendelkezik a legújabb titkosítási parancsok.

4. Telepítse a legújabb verzióját a [Azure SDK-t az Azure PowerShell](https://github.com/Azure/azure-powershell/releases). A következő parancsmag használatával engedélyezze ([beállítása](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1)) titkosítási, beolvasása ([beolvasása](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) titkosítási állapotát, és távolítsa el ([letiltása](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) terjedő skálán titkosítási beállítása példány:

| Parancs | Verzió |  Forrás  |
| ------------- |-------------| ------------|
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 vagy újabb verzió | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryptionStatus   | 3.4.0 vagy újabb verzió | AzureRM.Compute |
| Disable-AzureRmVmssDiskEncryption   | 3.4.0 vagy újabb verzió | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryption   | 3.4.0 vagy újabb verzió | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryption   | 3.4.0 vagy újabb verzió | AzureRM.Compute |
| Set-AzureRmVmssDiskEncryptionExtension   | 3.4.0 vagy újabb verzió | AzureRM.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Adatok titkosítása támogatott forgatókönyvek
* Virtuális gép méretezési készlet titkosítás csak felügyelt lemezekkel létrehozott méretezési csoportok esetén támogatott. A natív (vagy nem felügyelt) lemez méretezési készlet nem támogatott.
* Virtuális gép méretezési készlet titkosítási támogatott operációs rendszer- és adatkötetek számára a Windows virtuálisgép-méretezési készlet. Engedélyezi, és tiltsa le a titkosítást.
* Virtuálisgép-méretezési készlet Virtuálisgép-lemezkép alaphelyzetbe és frissítési művelet nem támogatott az aktuális előzetes.


## <a name="create-a-windows-cluster"></a>Windows-fürt létrehozása

A következő parancsok segítségével hozzon létre egy fürtöt, és engedélyezze a lemez titkosítása Azure Resource Manager-sablonok és egy önaláírt tanúsítványt.

### <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

```PowerShell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

### <a name="use-a-custom-template"></a>Alapértelmezett sablon használata 

Ha szüksége ahhoz, hogy egy egyéni sablont az igényeinek, azt javasoljuk, hogy az egyik megkezdése a [Azure Service Fabric-sablon minták](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master).

Ha már van egy egyéni sablont, és győződjön meg arról, hogy minden a tanúsítványokkal kapcsolatos paramétereket a sablonban és a paraméterfájl elnevezése a következő. Győződjön meg arról is, hogy értékek a következők null értékű.

```JSON
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


```PowerShell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```


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

### <a name="deploy-an-application-to-the-windows-service-fabric-cluster"></a>Alkalmazás üzembe helyezése a Windows Service Fabric-fürt
Kövesse a lépéseket és útmutatást is [alkalmazás üzembe helyezése a fürt](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications).


## <a name="enable-disk-encryption-for-a-virtual-machine-scale-set"></a>A virtuálisgép-méretezési csoport lemez-titkosítás engedélyezéséhez
Engedélyezze a Service Fabric Windows-fürt korábban létrehozott virtuálisgép-méretezési csoport a lemez titkosítása.
 
```PowerShell

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


## <a name="validate-that-disk-encryption-is-enabled-for-a-virtual-machine-scale-set"></a>Ellenőrizze, hogy a lemez titkosítás engedélyezve van a virtuálisgép-méretezési csoport
Használja a következő parancsokat egy teljes virtuálisgép-méretezési csoport vagy a tetszőleges példányra Virtuálisgép-méretezési csoportban lévő állapotának beolvasása. Jelentkezzen be a méretezési csoportban lévő virtuális gép is, és győződjön meg arról, hogy titkosítva legyenek-e a meghajtó.

```PowerShell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```CLI

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


## <a name="disable-disk-encryption-for-a-virtual-machine-scale-set"></a>Tiltsa le az adatok titkosítása a virtuálisgép-méretezési csoport 
Ha le kell tiltania a virtuálisgép-méretezési a Service Fabric Windows fürt beállítása az adatok titkosítása, használja a következő parancsokat. Lemeztitkosítás letiltását a teljes virtuálisgép-méretezési csoport és példány által nem vonatkozik. 


```PowerShell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>További lépések
Ezen a ponton rendelkezik biztonságos fürttel, és megtudhatja, hogyan engedélyezheti vagy letilthatja a Service Fabric Windows fürt lemeztitkosítás. A következő megismerése [lemeztitkosítás Linux](service-fabric-enable-azure-disk-encryption-linux.md). 

