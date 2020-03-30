---
title: Lemeztitkosítás engedélyezése Linux-fürtökhöz
description: Ez a cikk ismerteti, hogyan engedélyezheti a lemeztitkosítást az Azure Service Fabric fürtcsomópontok Linux on Azure Resource Manager és az Azure Key Vault használatával.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: c600d822d20b0e5a0ca613935b1dfa4be838fcec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252813"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Lemeztitkosítás engedélyezése az Azure Service Fabric fürtcsomópontjaihoz Linux alatt 
> [!div class="op_single_selector"]
> * [Lemeztitkosítás Linuxhoz](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Lemeztitkosítás windowsos rendszerben](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Ebben az oktatóanyagban megtudhatja, hogyan engedélyezheti a lemeztitkosítást az Azure Service Fabric fürtcsomópontjain Linux alatt. Ezeket a lépéseket kell végrehajtania az egyes csomóponttípusok és a virtuálisgép-méretezési csoportok esetében. A csomópontok titkosításához az Azure Disk Encryption képesség a virtuális gép méretezési csoportok.

Az útmutató a következő témákkal foglalkozik:

* A legfontosabb fogalmak, amelyeket figyelembe kell venni, amikor engedélyezi a lemeztitkosítást a Service Fabric fürt virtuálisgép-méretezési készletek Linuxon.
* A lemeztitkosítás engedélyezése előtt a Service Fabric fürtcsomópontjainak linuxos engedélyezése előtt követendő lépések.
* A rendszer a Service Fabric fürtcsomópontjain a lemeztitkosítás engedélyezéséhez követendő lépéseket.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

 **Önregisztráció**

A virtuálisgép-méretezési csoport lemeztitkosítási előnézete önregisztrációt igényel. Ehhez a következő lépések szükségesek:

1. Futtassa az alábbi parancsot: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Várjon körülbelül 10 percet, amíg az állapot *olvasása Regisztrált*. Az állapotot a következő parancs futtatásával ellenőrizheti:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault**

1. Hozzon létre egy kulcstartót ugyanabban az előfizetésben és régióban, mint a méretezési csoport. Ezután válassza ki az **EnabledForDiskEncryption** hozzáférési szabályzatot a key vaulton a PowerShell-parancsmag használatával. A szabályzatot a Key Vault felhasználói felületének használatával is beállíthatja az Azure Portalon a következő paranccsal:
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
* Mind a titkosítás, mind a titkosítás letiltása támogatja az operációs rendszer és az adatkötetek a virtuálisgép-méretezési készletek linuxos. 
* Virtuálisgép(VM) újraképzési és frissítési műveletek virtuálisgép-méretezési készletek nem támogatott a jelenlegi előzetes verzió.


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

Ha egyéni sablont kell készítette, javasoljuk, hogy használja az [Azure Service Fabric-fürt létrehozási sablonminták](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) lapján található sablonok egyikét. 

Ha már rendelkezik egyéni sablonnal, ellenőrizze, hogy a sablonban és a paraméterfájlban lévő mindhárom tanúsítványsal kapcsolatos paraméter neve a következő. Győződjön meg arról is, hogy az értékek a következők:

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

Mivel a Linux ban csak az adatlemez-titkosítás támogatott, egy Erőforrás-kezelő sablon használatával adatlemezt kell hozzáadnia. Frissítse az adatlemez-kiépítés sablonját az alábbiak szerint:

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

Itt van az egyenértékű CLI parancs. Módosítsa a deklarált utasítások értékeit a megfelelő értékekre. A CLI támogatja az összes többi paramétert, amelyet az előző PowerShell-parancs támogat.

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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Adatlemez csatlakoztatása Linux-példányra
Mielőtt folytatná a titkosítást egy virtuálisgép-méretezési csoporton, győződjön meg arról, hogy a hozzáadott adatlemez megfelelően van csatlakoztatva. Jelentkezzen be a Linux-fürt virtuális gépébe, és futtassa az **LSBLK** parancsot. A kimenetnek meg kell jelennie a mount **point** oszlopban hozzáadott adatlemeznek.


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Alkalmazás központi telepítése egy Service Fabric-fürtre Linux alatt
Egy alkalmazás fürtre való üzembe helyezéséhez kövesse a rövid útmutató lépéseit és [útmutatásait: Linux-tárolók telepítése a Service Fabric szolgáltatásba.](service-fabric-quickstart-containers-linux.md)


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

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Annak ellenőrzése, hogy engedélyezve van-e lemeztitkosítás egy Linux-alapú virtuálisgép-méretezési csoporthoz
A teljes virtuálisgép-méretezési csoport vagy a méretezési csoport bármely példányának állapotának leéséhez futtassa a következő parancsokat.
Emellett bejelentkezhet a Linux-fürt virtuális gépére, és futtathatja az **LSBLK** parancsot. A kimenetnek meg kell jelennie a hozzáadott adatlemeznek a **Mount Point** oszlopban, és a **Típus** oszlopnak a *Kriptának kell olvasnia.*

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

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
Ezen a ponton rendelkeznie kell egy biztonságos fürttel, és tudnia kell, hogyan engedélyezheti és tilthatja le a lemeztitkosítást a Service Fabric fürtcsomópontok és a virtuálisgép-méretezési készletek számára. Hasonló útmutatást a Service Fabric fürtcsomópontok linuxos, lásd: [Lemeztitkosítás windowshoz](service-fabric-enable-azure-disk-encryption-windows.md). 
