---
title: Lemeztitkosítás engedélyezése Windows-fürtökhöz
description: Ez a cikk ismerteti, hogyan engedélyezheti a lemeztitkosítást az Azure Service Fabric fürtcsomópontok az Azure Key Vault használatával az Azure Resource Manager.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: b08cdb63aa6f334c5a6f7c230b1624d232206c3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251819"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Lemeztitkosítás engedélyezése az Azure Service Fabric fürtcsomópontjaihoz a Windows rendszerben 
> [!div class="op_single_selector"]
> * [Lemeztitkosítás windowsos rendszerben](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Lemeztitkosítás Linuxhoz](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Ebben az oktatóanyagban megtudhatja, hogyan engedélyezheti a lemeztitkosítást a Service Fabric fürtcsomópontjain a Windows rendszerben. Ezeket a lépéseket kell végrehajtania az egyes csomóponttípusok és a virtuálisgép-méretezési csoportok esetében. A csomópontok titkosításához az Azure Disk Encryption képesség a virtuális gép méretezési csoportok.

Az útmutató a következő témákkal foglalkozik:

* A Service Fabric fürt virtuálisgép-méretezési készletein a Windows rendszerben a lemeztitkosítás engedélyezésekor figyelembe veendő legfontosabb fogalmak.
* A lemeztitkosítás engedélyezése előtt követendő lépések a Windows Service Fabric fürtcsomópontjain.
* A Windows Service Fabric fürtcsomópontjain a lemeztitkosítás engedélyezéséhez követendő lépések.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

**Önregisztráció** 

A virtuálisgép-méretezési csoport lemeztitkosítási előnézete önregisztrációt igényel. Ehhez a következő lépések szükségesek: 

1. Először futtassa a következő parancsot:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Várjon körülbelül 10 percet, amíg az állapot *olvasása Regisztrált*. Az állapotot a következő parancs futtatásával ellenőrizheti: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. Hozzon létre egy key vault ugyanabban az előfizetésben és régióban, mint a méretezési csoport, majd válassza ki az **EnabledForDiskEncryption** hozzáférési szabályzatot a key vault segítségével a PowerShell-parancsmag. A szabályzatot a Key Vault felhasználói felületének használatával is beállíthatja az Azure Portalon a következő paranccsal:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Telepítse az Azure [CLI](/cli/azure/install-azure-cli)legújabb verzióját, amely az új titkosítási parancsokat tartalmaz.
3. Telepítse az Azure SDK legújabb verzióját az [Azure PowerShell-kiadásból.](https://github.com/Azure/azure-powershell/releases) A következőkben a virtuális gép méretezési készlet E-lemeztitkosítás imtárcsák engedélyezése[(set)](/powershell/module/az.compute/set-azvmssdiskencryptionextension)titkosítás, beolvasása ([beolvasása](/powershell/module/az.compute/get-azvmssvmdiskencryption)) titkosítási állapot, és távolítsa el[(disable)](/powershell/module/az.compute/disable-azvmssdiskencryption)titkosítást a méretezési készlet példány.

| Parancs | Verzió |  Forrás  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 vagy újabb | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 vagy újabb | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 vagy újabb | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 vagy újabb | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 vagy újabb | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 vagy újabb | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Támogatott forgatókönyvek lemeztitkosításhoz
* A virtuálisgép-méretezési csoportok titkosítása csak a felügyelt lemezekkel létrehozott méretezési csoportok esetében támogatott. Natív (vagy nem felügyelt) lemezméretezés-készletek esetén nem támogatott.
* A titkosítás a Windows virtuálisgép-méretezési készleteiben lévő operációs rendszer és adatkötetek esetében támogatott. A titkosítás letiltása az operációs rendszer és az adatkötetek esetében is támogatott a Windows virtuálisgép-méretezési készleteihez.
* A virtuálisgép-méretezési csoportok virtuálisgép-újraképzési és frissítési műveleteinem támogatottak az aktuális előzetes verzióban.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Új fürt létrehozása és lemeztitkosítás engedélyezése

A következő parancsokkal hozzon létre egy fürtöt, és engedélyezze a lemeztitkosítást egy Azure Resource Manager-sablon és egy önaláírt tanúsítvány használatával.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 
Jelentkezzen be a következő parancsokkal:
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>A már meglévő egyéni sablon használata 

Ha az igényeinek megfelelően egyéni sablont kell készítette, javasoljuk, hogy kezdje az [Azure Service Fabric-fürt létrehozási sablonminták](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) lapján elérhető sablonok egyikével. A fürtsablon szakasz [testreszabásához][customize-your-cluster-template] olvassa el az alábbi útmutatót.

Ha már rendelkezik egyéni sablonnal, ellenőrizze, hogy a sablonban és a paraméterfájlban lévő mindhárom tanúsítványsal kapcsolatos paraméter a következőképpen van-e elnevezve, és hogy az értékek a következők:

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Alkalmazás központi telepítése Service Fabric-fürtre windowsos rendszerben
Ha egy alkalmazást a fürtre szeretne telepíteni, kövesse az [alkalmazások központi telepítésével és eltávolításával](service-fabric-deploy-remove-applications.md)kapcsolatos lépéseket és útmutatást a PowerShell használatával.


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Lemeztitkosítás engedélyezése a korábban létrehozott virtuálisgép-méretezési csoportokhoz

Ha engedélyezni szeretné a lemeztitkosítást az előző lépésekben létrehozott virtuálisgép-méretezési készletekhez, futtassa a következő parancsokat:
 
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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Annak ellenőrzése, hogy engedélyezve van-e lemeztitkosítás a Windows rendszerben beállított virtuálisgép-méretezési csoporthoz
A következő parancsok futtatásával egy teljes virtuálisgép-méretezési csoport vagy egy méretezési csoport bármely példányának állapotát kapja meg.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Ezenkívül bejelentkezhet a virtuálisgép-méretezési csoportba, és meggyőződhet arról, hogy a meghajtók titkosítva vannak.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>A szolgáltatásháló-fürtben lévő virtuálisgép-méretezési csoport lemeztitkosításának letiltása 
Tiltsa le a lemeztitkosítást egy virtuálisgép-méretezési csoportban a következő parancsok futtatásával. Vegye figyelembe, hogy a lemeztitkosítás letiltása a teljes virtuálisgép-méretezési csoportra vonatkozik, és nem egy adott példányra.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>További lépések
Ezen a ponton rendelkeznie kell egy biztonságos fürttel, és tudnia kell, hogyan engedélyezheti és tilthatja le a lemeztitkosítást a Service Fabric fürtcsomópontok és a virtuálisgép-méretezési készletek számára. Hasonló útmutatást a Service Fabric fürtcsomópontok Linux, lásd: [Lemeztitkosítás Linux.](service-fabric-enable-azure-disk-encryption-linux.md)

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
