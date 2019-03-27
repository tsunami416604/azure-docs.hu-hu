---
title: Lemeztitkosítás engedélyezve, az Azure Service Fabric Windows-fürtök |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Service Fabric-fürtcsomópontok lemeztitkosítás engedélyezve az Azure Resource Managerrel, az Azure Key Vault.
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
ms.openlocfilehash: a620563be9ffe18ae0f7fa4a78df83ea5b35a5d2
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488226"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Disk encryption Windows uzly clusteru service fabric-szolgáltatás engedélyezése 
> [!div class="op_single_selector"]
> * [Disk Encryption Windows-szolgáltatás](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Lemeztitkosítás linuxhoz](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

A Windows Uzly Clusteru Service Fabric lemeztitkosítás engedélyezve az alábbi lépésekkel. Az egyes típusok/virtuális gép méretezési csoportok a csomópont nem kell. A csomópontok titkosításához, használja azt az Azure Disk Encryption funkció, virtuálisgép-méretezési csoportokon.

Az útmutató a következő eljárásokat ismerteti:

* Főbb fogalmak, amely a lemeztitkosítást a virtuálisgép-méretezési csoport Service Fabric Windows-fürt engedélyezése ki figyelembe kell beállítani.
* Előfeltételek lépéseket kell követni a Service Fabric-fürt Windows virtuális gép méretezési csoporthoz lemeztitkosítással engedélyezése előtt.
* Állítsa be a lemeztitkosítást a virtuálisgép-méretezési csoport Service Fabric Windows-fürt engedélyezése követendő lépéseket.


## <a name="prerequisites"></a>Előfeltételek
* **Regisztrációs** – használatához a virtuális gép méretezési készlet lemez titkosítási előzetes regisztrációs igényel
* Önálló regisztrálhatja előfizetését futtassa az alábbi lépéseket: 
```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* Várjon, amíg az állapot körülbelül 10 percet, "Regisztrálva". Az állapot a következő parancs futtatásával ellenőrizheti: 
```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```
* **Az Azure Key Vault** – hozzon létre egy KeyVault azonos előfizetésben és régióban, a méretezési csoport és a hozzáférési házirend "EnabledForDiskEncryption" beállítása a KeyVault a PS-parancsmag segítségével. A szabályzat a KeyVault felhasználói felület segítségével az Azure Portalon is állíthatja be: 
```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* Telepítse a legújabb [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) , amely rendelkezik az új titkosítási parancsait.
* Telepítse a legújabb verzióját, [Azure SDK-t az Azure PowerShell](https://github.com/Azure/azure-powershell/releases) kiadás. A virtuális gép méretezési ADE parancsmagjaival engedélyezhetik a következők ([beállítása](/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1)) titkosítási, lekérése ([első](/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) titkosítási állapotát, és távolítsa el ([letiltása](/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) titkosítás a méretezési csoportban a példány.

| Parancs | Verzió |  Forrás  |
| ------------- |-------------| ------------|
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 vagy újabb | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryptionStatus   | 3.4.0 vagy újabb | AzureRM.Compute |
| Disable-AzureRmVmssDiskEncryption   | 3.4.0 vagy újabb | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryption   | 3.4.0 vagy újabb | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryption   | 3.4.0 vagy újabb | AzureRM.Compute |
| Set-AzureRmVmssDiskEncryptionExtension   | 3.4.0 vagy újabb | AzureRM.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Lemeztitkosítás támogatott helyzetek
* Virtual machine scale set titkosítási csak a managed disks szolgáltatással létrehozott, és nem támogatott natív (vagy nem felügyelt) lemezek méretezési csoportok a méretezési csoportok esetében támogatott.
* Virtual machine scale set titkosítási Windows virtuálisgép-méretezési csoport operációsrendszer- és kötetek esetében támogatott. Tiltsa le titkosítás Windows méretezési csoport operációsrendszer- és kötetek esetében támogatott.
* Virtuálisgép-méretezési VM-rendszerképeit alaphelyzetbe állítani, és a frissítési műveletek nem támogatottak az aktuális előzetes verzióban érhető el.


### <a name="create-new-cluster-and-enable-disk-encryption"></a>Hozzon létre új fürtöt, és a lemeztitkosítás engedélyezve

Az alábbi parancsokkal hozható létre fürt és az Azure Resource Manager-sablon & önaláírt tanúsítvány használatával lemeztitkosítás engedélyezve.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

```powershell
Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Használja az egyéni sablont, amely már rendelkezik 

Ha hozzon létre egy egyéni sablont, hogy illeszkedjen az igényeihez van szüksége, azt javasoljuk, hogy az egyik elérhető sablont megkezdése a [az azure service fabric-sablonminták](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Kövesse az útmutatást és magyarázatok az [fürtsablonhoz testreszabása] [ customize-your-cluster-template] szakaszt.

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


```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```


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

#### <a name="deploy-application-to-windows-service-fabric-cluster"></a>A Windows Service Fabric-fürt üzembe helyezése
Kövesse a lépéseket és útmutatás [alkalmazás üzembe helyezése a fürtön](service-fabric-deploy-remove-applications.md)


#### <a name="enable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set-created-above"></a>A fentiekben létrehozott Service Fabric-fürt virtuális gép méretezési lemeztitkosítás engedélyezve
 
```powershell

$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```


#### <a name="validate-if-disk-encryption-enabled-for-windows-virtual-machine-scale-set"></a>Ha engedélyezve van a Windows virtuálisgép-méretezési csoport lemeztitkosítás ellenőrzése.
Egy teljes virtuálisgép-méretezési csoportot, vagy minden olyan példány állapotának lekéréséhez a méretezési csoportban. Tekintse meg az alábbi parancsokat.
Emellett felhasználó jelentkezzen be a virtuális gép méretezési és ellenőrizze, hogy a meghajtó titkosított

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Tiltsa le a Service Fabric-fürt virtuálisgép-méretezési lemeztitkosítás 
Tiltsa le lemeztitkosítás vonatkozik, és a példány nem a teljes virtuálisgép-méretezési csoporthoz 

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>További lépések
Ezen a ponton rendelkezik egy biztonságos fürthöz Service Fabric-fürt virtuálisgép-méretezési csoport esetében a lemeztitkosítás engedélyezését vagy letiltását annak. Ezután [lemeztitkosítás linuxhoz](service-fabric-enable-azure-disk-encryption-linux.md) 

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template