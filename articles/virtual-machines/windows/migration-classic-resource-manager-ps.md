---
title: Áttelepítése a Resource Manager a PowerShell-lel |} A Microsoft Docs
description: Ez a cikk végigvezeti az IaaS-erőforrások, például a virtuális gépeken (VM), a virtuális hálózatok (Vnetek) és a storage-fiókok a platform által támogatott áttelepítés klasszikusból Azure Resource Manager (ARM) Azure PowerShell-parancsok használatával
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: e1144611c68e8a3c450f8017388cfa84629f9921
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256493"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Át IaaS-erőforrások klasszikusból Azure Resource Manager Azure PowerShell-lel
Ezek a lépések bemutatják, hogyan áttelepítése infrastruktúra-szolgáltatás (IaaS) erőforrások a klasszikus üzemi modellben az Azure Resource Manager-alapú üzemi modellbe, az Azure PowerShell-parancsok használatával.

Ha azt szeretné, így migrálhatja erőforrások használatával a [Azure parancssori felület (Azure CLI)](../linux/migration-classic-resource-manager-cli.md).

* A támogatott áttelepítési forgatókönyvek a háttér-információkért lásd: [Platform által támogatott áttelepítés IaaS-erőforrások klasszikusból Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Részletes útmutató és a egy áttelepítési forgatókönyv [részletes technikai platform által támogatott áttelepítés a klasszikusból az Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md)

<br>
Azonosíthatja a sorrendet, amelyben lépéseket kell végrehajtani egy áttelepítési folyamat során folyamatábra

![Képernyőkép a migrálási lépésekről](media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-plan-for-migration"></a>1. lépés: Az áttelepítés megtervezése
Az alábbiakban néhány ajánlott eljárást, amely azt javasoljuk, áttelepítése IaaS-erőforrásokat a klasszikusból a Resource Managernek kipróbálása:

* Olvassa el a [támogatott és nem támogatott szolgáltatásokat és konfigurációkat](migration-classic-resource-manager-overview.md). Ha nem támogatott konfigurációkat és funkciókat használó virtuális gépeket, javasoljuk, hogy később jelentjük be a konfigurációs szolgáltatások/támogatásáról várja. Azt is megteheti Ha azt az igényeinek megfelelő, távolítsa el ezt a funkciót, vagy helyezze át ki, hogy a configuration engedélyezze az áttelepítést.
* Ha automatizálta parancsprogramokat, amelyek az infrastruktúra és alkalmazások telepítése még ma, próbálja létrehozni egy hasonló teszt beállítást az áttelepítés ezen parancsfájlok használatával. Azt is megteheti hogy mintakörnyezetek úgy állíthatja az Azure portal használatával.

> [!IMPORTANT]
> Az Application Gateway átjárók jelenleg nem támogatottak az áttelepítés klasszikusról Resource Manager. Az Application gateway egy klasszikus virtuális hálózat migrálása, távolítsa el az átjáró áthelyezése a hálózat-előkészítési művelet futtatása előtt. Miután befejezte az áttelepítést, csatlakoztassa újra az átjárót, az Azure Resource Manager.
>
>ExpressRoute-átjáró csatlakoztatása ExpressRoute-Kapcsolatcsoportok egy másik előfizetésben nem telepíthetők át automatikusan. Ezekben az esetekben távolítsa el az ExpressRoute-átjárót, a virtuális hálózat migrálása, és hozza létre újra az átjárót. Lásd: [áttelepítése ExpressRoute-Kapcsolatcsoportok és kapcsolódó virtuális hálózatok a klasszikusból a Resource Manager-alapú üzemi modellbe](../../expressroute/expressroute-migration-classic-resource-manager.md) további információt.

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>2. lépés: Telepítse az Azure PowerShell legújabb verzióját
Azure PowerShell telepítése két fő módja van: [PowerShell-galériából](https://www.powershellgallery.com/profiles/azure-sdk/) vagy [Webplatform-telepítő (WebPI)](https://aka.ms/webpi-azps). WebPI kapja a havi frissítéseit. PowerShell-galériából rendszeresen frissítéseket kapja. Ez a cikk az Azure PowerShell-lel 2.1.0-ás alapul.

A telepítési utasításokért lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>3. lépés: Győződjön meg arról, hogy-e az előfizetés-rendszergazda az Azure Portalon
Az áttelepítés végrehajtásához meg kell adni az előfizetés társadminisztrátorai a [az Azure portal](https://portal.azure.com).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben válassza ki a **előfizetés**. Ha nem látja, válassza ki a **minden szolgáltatás**.
3. Keresse meg a megfelelő előfizetést bejegyzést, majd tekintse meg a **saját SZEREPKÖR** mező. A társ-rendszergazda, az érték lehet _fiókadminisztrátor_.

Ha nem a társ-rendszergazdaként adhat hozzá, majd lépjen kapcsolatba, szolgáltatás-rendszergazdaként vagy megtalál hozzá az előfizetés társadminisztrátori.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>4. lépés: Az előfizetés beállításához, és iratkozzon fel az áttelepítéshez
Először indítsa el egy PowerShell-parancssort. Az áttelepítéshez, állítsa be a környezetet, mind a klasszikus üzemi modell esetén kell és a Resource Manager.

Jelentkezzen be a fiókját a Resource Manager-modellben.

```powershell
    Connect-AzureRmAccount
```

A következő paranccsal kérje le a rendelkezésre álló előfizetések:

```powershell
    Get-AzureRMSubscription | Sort Name | Select Name
```

Állítsa be az Azure-előfizetés az aktuális munkamenet. Ebben a példában a alapértelmezett előfizetés nevének beállítása **saját Azure-előfizetés**. Cserélje le a példában előfizetés nevét a saját.

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Regisztráció egy egyszeri lépést, de egyszer a migrálás megkezdése előtt kell tennie. Regisztrálja, a következő hibaüzenet jelenik meg:
>
> *BadRequest: Előfizetés nincs regisztrálva migrálásra.*

Regisztrálás az áttelepítés erőforrás-szolgáltató a következő paranccsal:

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Kérjük, várjon öt perc alatt, a regisztráció befejezéséhez. A jóváhagyás állapotát a következő paranccsal ellenőrizheti:

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Győződjön meg arról, hogy van-e RegistrationState `Registered` folytatás előtt.

Most jelentkezzen be a Klasszikus modell esetében a fiók.

```powershell
    Add-AzureAccount
```

A következő paranccsal kérje le a rendelkezésre álló előfizetések:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Állítsa be az Azure-előfizetés az aktuális munkamenet. Ebben a példában az alapértelmezett előfizetést állítja **saját Azure-előfizetés**. Cserélje le a példában előfizetés nevét a saját.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>5. lépés: Ellenőrizze, hogy elegendő az Azure Resource Manager virtuális gépének vcpu-k a jelenlegi üzemelő példány vagy virtuális hálózat Azure-régióban
A következő PowerShell-parancs segítségével ellenőrizze az Azure Resource Manager rendelkezik vcpu-k aktuális száma. VCPU-kvóták kapcsolatos további információkért lásd: [korlátok és az Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

Ebben a példában a rendelkezésre állását ellenőrzi a **USA nyugati RÉGIÓJA** régióban. Cserélje le a régió neve a példában a saját.

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>6. lépés: Futtassa a parancsokat az IaaS-erőforrások áttelepítése
* [Virtuális gépek áttelepítése egy felhőalapú szolgáltatásban (nem a virtuális hálózat esetén)](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Virtuális gépek áttelepítése a virtuális hálózaton](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Tárfiók migrálása](#step-62-migrate-a-storage-account)

> [!NOTE]
> Az itt ismertetett összes művelet idempotens. Ha rendelkezik olyan probléma lép fel egy nem támogatott funkció vagy konfigurációs hibának, azt javasoljuk, hogy az előkészítés, újra megpróbálja megszakítható, vagy a véglegesítési műveletet. A platform majd újrapróbálkozik a művelettel.


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>6.1. lépés: 1. lehetőség – (nem a virtuális hálózatban) egy felhőszolgáltatás virtuális gépek Migrálása
A cloud services listájának lekérése a következő paranccsal, és ezután válassza ki a felhőalapú szolgáltatás, amely a migrálni kívánt. Ha a virtuális gépek, a cloud service-ben a virtuális hálózatban, vagy ha webes vagy feldolgozói szerepkörök rendelkeznek, a parancs hibaüzenetet ad vissza.

```powershell
    Get-AzureService | ft Servicename
```

Kérje le a központi telepítés nevét, a felhőszolgáltatás számára. Ebben a példában a szolgáltatásnév az **saját szolgáltatás**. A példa a szolgáltatás nevére cserélje le a saját szolgáltatás neve.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

A virtuális gépek áttelepítése a felhőbe történő előkészítéséhez. Rendelkezik két lehetőség közül választhat.

* **1. lehetőség. A virtuális gépek áttelepítése a platform által létrehozott virtuális hálózathoz**

    Először is ellenőrizheti, hogy telepíthet át a felhőalapú szolgáltatás, a következő parancsokkal:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    A következő parancs megjeleníti a figyelmeztetések és hibák, amelyek a migrálás letiltása. Ha az ellenőrzés nem jelez hibát, majd áthelyezheti a a **előkészítése** . lépés:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **2. lehetőség. Át egy meglévő virtuális hálózatot a Resource Manager-alapú üzemi modellben**

    Ebben a példában az erőforráscsoport nevét állítja **myResourceGroup**, a virtuális hálózat nevét **myVirtualNetwork** és az alhálózat nevére **mySubNet**. Cserélje le a példában a nevek saját erőforrások nevei.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Először is ellenőrizheti, hogy áttelepítheti a virtuális hálózat, a következő paranccsal:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    A következő parancs megjeleníti a figyelmeztetések és hibák, amelyek a migrálás letiltása. Ha az ellenőrzés nem jelez hibát, majd, és folytassa a következő előkészítési lépés:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Az előkészítési művelet sikeres, a fenti lehetőségek bármelyikével, miután a lekérdezés a virtuális gépek a migrálás állapotát. Győződjön meg arról, hogy azok a `Prepared` állapota.

Ebben a példában a virtuális gép nevének beállítása **myVM**. A példa nevére cserélje le a saját virtuális gép neve.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Ellenőrizze az előkészített erőforrások konfigurációját a PowerShell vagy az Azure portal használatával. Ha nem áll készen a migrálásra, és térjen vissza a régi állapot szeretne, használja a következő parancsot:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Az előkészített konfiguráció megfelelőnek tűnik, ha előre, és véglegesíti az erőforrásokat a következő paranccsal:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>6.1. lépés: 2. lehetőség – a virtuális hálózatban a virtuális gépek Migrálása

Egy virtuális hálózatban lévő virtuális gépek áttelepítését, telepítse át a virtuális hálózat. A virtuális gépek automatikus áttelepítése a virtuális hálózattal. Válassza ki az áttelepíteni kívánt virtuális hálózat.
> [!NOTE]
> [Egyetlen klasszikus virtuális gép áttelepítése](migrate-single-classic-to-resource-manager.md) hozzon létre egy új erőforrás-kezelő virtuális gép a Managed Disks a virtuális gép VHD-t (operációs rendszer és data) fájlok használatával.
<br>

> [!NOTE]
> Lehet, hogy a virtuális hálózat neve eltér az új portálon jelenik meg. Az új Azure Portal megjeleníti a nevet a következőként `[vnet-name]` , de a tényleges virtuális hálózati név típusú `Group [resource-group-name] [vnet-name]`. Migrálás, keresés a tényleges virtuális hálózat nevét, a parancs előtt `Get-AzureVnetSite | Select -Property Name` illetve megtekinteni azt a régi Azure-portálon. 

Ebben a példában a virtuális hálózat nevének beállítása **myVnet**. Cserélje le a példában virtuális hálózat nevét a saját.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Ha a virtuális hálózat nem támogatott konfigurációval webes vagy feldolgozói szerepkörök, vagy a virtuális gépeket tartalmaz, egy érvényesítési hibaüzenet kap.

Először is ellenőrizheti, hogy a következő parancs segítségével áttelepítheti a virtuális hálózat:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

A következő parancs megjeleníti a figyelmeztetések és hibák, amelyek a migrálás letiltása. Ha az ellenőrzés nem jelez hibát, majd, és folytassa a következő előkészítési lépés:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Ellenőrizze a konfigurációt, az előkészített virtuális gépek Azure PowerShell vagy az Azure portal használatával. Ha nem áll készen a migrálásra, és térjen vissza a régi állapot szeretne, használja a következő parancsot:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Az előkészített konfiguráció megfelelőnek tűnik, ha előre, és véglegesíti az erőforrásokat a következő paranccsal:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-62-migrate-a-storage-account"></a>Lépés 6.2 áttelepítése egy tárfiókhoz
Miután elkészült a virtuális gépek migrálása, javasoljuk, hogy a tárfiókok áttelepítése előtt hajtsa végre a következő előfeltételek ellenőrzését.

> [!NOTE]
> Ha a tárfiók nincs társított lemezek vagy a virtuális gép adatait, akkor ugorjon a **Storage-fiók érvényesítéséhez, és indítsa el áttelepítési** szakaszban.

* **Az Előfeltételek ellenőrzése, ha minden olyan virtuális gépeket telepített át, vagy a tárfiók a lemez erőforrással rendelkezik**
    * **Amelynek lemezek vannak tárolva a tárfiókot klasszikus virtuális gépek migrálása**

        A következő parancsot a tárfiók a klasszikus virtuális gép lemezeivel RoleName és DiskName tulajdonságait adja vissza. RoleName az a név a virtuális gép, amelyhez a lemez csatlakoztatva van. Ha ez a parancs visszaadja lemezek majd győződjön meg arról, hogy a tárfiók migrálása előtt át a virtuális gépeket, amelyhez ezeket a lemezekkel rendelkezik.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * **A storage-fiókban tárolt nem csatolt klasszikus virtuális gép lemezeinek törlése**

        Keresse meg, nem csatolt klasszikus virtuális gép lemezek, a Storage-fiók használatával a következő parancsot:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Ha a fenti parancs beolvasása lemezek törölje ezeket a lemezeket, az alábbi parancs használatával:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * **A storage-fiókban tárolt Virtuálisgép-rendszerképek törlése**

        A következő parancsot a Virtuálisgép-rendszerképek a tárfiókban tárolt operációsrendszer-lemez ad vissza.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         A következő parancsot a Virtuálisgép-rendszerképek a tárfiókban tárolt adatlemezekkel rendelkező adja vissza.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Ezzel a paranccsal parancsok fent által visszaadott összes Virtuálisgép-rendszerkép törlése:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* **Ellenőrizze a Storage-fiók, és indítsa el áttelepítési**

    Minden tárfióknak az áttelepítéshez érvényesítése a következő parancs segítségével. Ebben a példában a tárfiók neve van **myStorageAccount**. A példa nevére cserélje le a saját tárfiókja nevére.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    Következő lépés az, hogy a tárfiók migrálásának előkészítése

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Ellenőrizze a konfigurációt, az előkészített storage-fiókot az Azure PowerShell vagy az Azure portal használatával. Ha nem áll készen a migrálásra, és térjen vissza a régi állapot szeretne, használja a következő parancsot:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Az előkészített konfiguráció megfelelőnek tűnik, ha előre, és véglegesíti az erőforrásokat a következő paranccsal:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>További lépések
* [A platform által támogatott áttelepítés IaaS-erőforrások klasszikusból Azure Resource Manager áttekintése](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS-erőforrások áttelepítése klasszikusból Azure Resource Manager parancssori felület használatával](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager áttelepítését segítő közösségi eszközök](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A legtöbb – gyakori kérdések áttelepítése IaaS-erőforrások klasszikusból Azure Resource Manager áttekintése](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
