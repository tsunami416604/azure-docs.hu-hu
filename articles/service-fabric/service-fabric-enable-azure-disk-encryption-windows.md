---
title: Lemeztitkosítás engedélyezve, az Azure Service Fabric Windows-fürtök |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Service Fabric-fürtcsomópont lemeztitkosítás engedélyezése az Azure Key Vault az Azure Resource Manager használatával.
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
ms.openlocfilehash: c31fc43729bcb58c755959db0c8bc5185b8197f4
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66471409"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>A Windows Azure Service Fabric-fürtcsomópont lemeztitkosítás engedélyezve 
> [!div class="op_single_selector"]
> * [Disk Encryption Windows-szolgáltatás](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Lemeztitkosítás linuxhoz](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Ebben az oktatóanyagban megismerheti, hogyan engedélyezéséhez a lemeztitkosítást a Service Fabric-fürtcsomópont a Windows lesz. Kövesse az alábbi lépéseket az egyes csomóponttípusok és virtuálisgép-méretezési csoportok kell. A csomópontok titkosításához, a virtual machine scale sets fogjuk használni az Azure Disk Encryption képesség.

Az útmutató témakörei a következők:

* Érdemes figyelembe vennie, amikor beállítja a lehetővé teszi a lemeztitkosítást, a Service Fabric-fürt virtuálisgép-méretezési Windows alapfogalmakat.
* Mielőtt engedélyezné a lemeztitkosítást a Service Fabric követendő lépéseket a Windows fürtcsomópont.
* Service Fabric-fürtcsomópont a Windows lemez titkosításának követendő lépéseket.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

**Önkiszolgáló regisztráció** 

A virtuálisgép-méretezési csoportot a lemez titkosítási előzetes regisztrációs van szükség. Ehhez a következő lépések szükségesek: 

1. Először futtassa a következő parancsot:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Várjon körülbelül 10 percet, amíg az állapot *regisztrált*. Az állapot a következő parancs futtatásával ellenőrizheti: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. Hozzon létre egy kulcstartót ugyanazt az előfizetést és a régióban, mint a méretezési csoportban, majd válassza ki a **EnabledForDiskEncryption** házirendet a key vault eléréséhez a PowerShell-parancsmag használatával. A Key Vault felhasználói felület az Azure Portalon a következő parancs használatával is beállíthatja a szabályzatot:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Telepítse a legújabb verzióját a [Azure CLI-vel](/cli/azure/install-azure-cli), amely rendelkezik az új titkosítási parancsait.
3. Telepítse a legújabb verzióját a [Azure SDK-t az Azure PowerShell](https://github.com/Azure/azure-powershell/releases) kiadás. A virtuális gép méretezési csoport engedélyezése az Azure Disk Encryption parancsmagok a következők ([beállítása](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) titkosítási, lekérése ([első](/powershell/module/az.compute/get-azvmssvmdiskencryption)) titkosítási állapotát, és távolítsa el ([letiltása](/powershell/module/az.compute/disable-azvmssdiskencryption)) a méretezési csoport titkosításának példány beállítása.

| Parancs | Version |  Source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 vagy újabb | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 vagy újabb | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 vagy újabb | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 vagy újabb | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 vagy újabb | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 vagy újabb | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Lemeztitkosítás támogatott helyzetek
* Virtuálisgép-méretezési csoportokhoz tartozó titkosítási csak a létrehozott felügyelt lemezekkel rendelkező méretezési csoportok esetében támogatott. Natív (vagy nem felügyelt) lemezek méretezési csoportok esetében nem támogatott.
* Titkosítás támogatott az operációs rendszer, és beállítja az adatkötetek a virtuálisgép-méretezési csoport a Windows. Tiltsa le titkosítás is támogatott az operációs rendszer és a virtuálisgép-méretezési csoport adatmennyisége beállítja a Windows.
* Virtuális gép rendszerkép alaphelyzetbe állítását és a frissítési műveletek virtuálisgép-méretezési csoportok nem támogatottak az aktuális előzetes verzióban érhető el.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Hozzon létre egy új fürtöt, és a lemeztitkosítás engedélyezve

A következő parancsok használatával hozzon létre egy fürtöt, és a lemeztitkosítás engedélyezve az Azure Resource Manager-sablon és a egy önaláírt tanúsítvány használatával.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 
Jelentkezzen be a következő parancsokat:
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Használja az egyéni sablont, amely már rendelkezik 

Hozzon létre egy egyéni sablont, hogy illeszkedjen az igényeihez van szüksége, ha kifejezetten ajánljuk, hogy az egyik elérhető sablont megkezdése a [Azure Service Fabric-fürt létrehozása sablonminták](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) lapot. A [fürtsablonhoz testreszabása] [ customize-your-cluster-template] területén az alábbi útmutatóban talál.

Ha már rendelkezik egy egyéni sablont, és ellenőrizze, hogy minden három tanúsítvánnyal kapcsolatos paraméterekkel a sablon és a alkalmazásparaméter-fájlt a következő nevesített és értéke null a következő:

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


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Service Fabric-fürt, a Windows-alkalmazás üzembe helyezése
Egy alkalmazást a fürtön üzembe helyezéséhez kövesse a lépéseket és útmutatójában [PowerShell-lel telepítés és eltávolítás alkalmazások](service-fabric-deploy-remove-applications.md).


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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Ha a lemeztitkosítás engedélyezve van egy Windows virtuális gép méretezési ellenőrzése
Egy teljes virtuálisgép-méretezési csoportot, vagy minden olyan példány állapotának lekérése a méretezési csoportban a következő parancsok futtatásával.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Emellett jelentkezzen be a virtuálisgép-méretezési csoportot, és ellenőrizze, hogy a meghajtók vannak titkosítva.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Tiltsa le a Service Fabric-fürt virtuális gép méretezési lemeztitkosítás 
Tiltsa le a lemeztitkosítás állítsa a következő parancsok futtatásával virtuálisgép-méretezési csoportot. Vegye figyelembe, hogy a teljes virtuálisgép-méretezési csoportot és a egy egyéni példány nem érvényes lemeztitkosítás letiltása.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>További lépések
Ezen a ponton kell egy biztonságos fürt, és tudja, hogyan engedélyezheti és tilthatja le a Service Fabric-fürtcsomópontok és a virtual machine scale sets lemeztitkosítás. A Linux Service Fabric-fürtcsomópont hasonló útmutatásért lásd: [Disk Encryption for Linux](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
