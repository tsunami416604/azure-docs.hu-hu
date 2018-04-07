---
title: Telepítse át az erőforrás-kezelő a PowerShell-lel |} Microsoft Docs
description: Ez a cikk végigvezeti a platform által támogatott áttelepítési IaaS-erőforrások, például a virtuális gépek (VM), a virtuális hálózatokon (Vnetek) és a storage-fiókok a klasszikus Azure Resource Managerrel (ARM) Azure PowerShell-parancsok segítségével
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
ms.openlocfilehash: c184fa2aa3e8b09c798f3c1f007cad57891c413e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Telepítse át IaaS-erőforrásokra a klasszikus Azure Resource Manager Azure PowerShell használatával
Ezeket a lépéseket mutatja be Azure PowerShell-parancsok használatával telepítse át az infrastruktúra erőforrásként egy szolgáltatási (IaaS) a klasszikus telepítési modellből az Azure Resource Manager telepítési modellhez.

Ha azt szeretné, is áttelepítheti erőforrások használatával a [Azure parancssori felület (Azure CLI)](../linux/migration-classic-resource-manager-cli.md).

* A támogatott áttelepítési forgatókönyvek háttér, lásd: [Platform által támogatott áttelepítési IaaS erőforrások a klasszikus Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Részletes útmutatást és egy áttelepítési forgatókönyv: [műszaki részletes bemutatója a platform által támogatott áttelepítési a klasszikus Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md)

<br>
Ez a sorrendet, amelyben lépéseket kell végrehajtani az áttelepítési folyamat során azonosításához folyamatábra

![Képernyőkép a migrálási lépésekről](media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-plan-for-migration"></a>1. lépés: Az áttelepítés tervezése
Az alábbiakban néhány gyakorlati tanácsok, azt javasoljuk, áttelepítése IaaS-erőforrásokra a hagyományos erőforrás-kezelő értékeli:

* Olvassa végig a [támogatott és nem támogatott a szolgáltatásnak és konfigurálásnak](migration-classic-resource-manager-overview.md). Ha még nem támogatott konfigurációk vagy szolgáltatások használó virtuális gépek, azt javasoljuk, hogy a konfiguráció/szolgáltatás támogatási bejelentések várja. Azt is megteheti Ha azt az igényeinek megfelelő, távolítsa el a szolgáltatást, vagy helyezze át kívül, hogy a konfigurálás engedélyezze az áttelepítést.
* Ha olyan parancsfájlok, amelyek központi telepítése az infrastruktúra és az alkalmazások ma rendelkezik automatikus, hozzon létre egy hasonló vizsgálat beállítása az áttelepítés ezen parancsfájlok használatával. Másik lehetőségként állíthat be minta környezetekben az Azure portál használatával.

> [!IMPORTANT]
> Alkalmazásátjárót jelenleg nem támogatottak az áttelepítéshez a klasszikus az erőforrás-kezelő. A klasszikus virtuális hálózatot az Alkalmazásátjáró át, a hálózati áthelyezése egy előkészítési művelet futtatása előtt távolítsa el az átjáró. Az áttelepítés befejezése után csatlakoztassa újra az átjárót az Azure Resource Manager.
>
>Kapcsolódás egy másik előfizetésben található ExpressRoute-Kapcsolatcsoportok ExpressRoute-átjárók nem telepíthetők át automatikusan. Ebben az esetben távolítsa el az ExpressRoute-átjárót, telepítse át a virtuális hálózaton, és hozza létre újra az átjárót. Ellenőrizze a [áttelepítése ExpressRoute áramkörök, és a Resource Manager üzembe helyezési modellel klasszikus virtuális hálózatok társított](../../expressroute/expressroute-migration-classic-resource-manager.md) további információt.
>
>

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>2. lépés: Az Azure PowerShell legújabb verziójának telepítése
Azure PowerShell telepítése két fő lehetőség: [PowerShell-galériában](https://www.powershellgallery.com/profiles/azure-sdk/) vagy [Webplatform-telepítővel (WebPI)](http://aka.ms/webpi-azps). WebPI havi frissítések kap. PowerShell-galériában folyamatosan frissítések kap. Ez a cikk az Azure PowerShell verzió 2.1.0 alapul.

A telepítési utasításokért lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>3. lépés: Ellenőrizze, hogy a rendszergazda az előfizetéshez tartozó Azure-portálon
Az áttelepítés végrehajtásához meg kell adni az előfizetés társadminisztrátoraként a [Azure-portálon](https://portal.azure.com).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben válassza ki a **előfizetés**. Ha nem látja, válassza ki a **minden szolgáltatás**.
3. Keresse meg a megfelelő előfizetés bejegyzést, majd tekintse meg a **saját SZEREPKÖR** mező. Egy közös rendszergazda értékének kell _fiókadminisztrátor_.

Ha nem tud társadminisztrátorának hozzáadni, majd kérje meg, egy szolgáltatás-rendszergazda vagy a közös rendszergazdát az előfizetéshez tartozó saját kezűleg hozzá.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>4. lépés: Állítsa be az előfizetéshez, és regisztráljon áttelepítése
Először egy PowerShell-parancssorba. Az áttelepítéshez, be kell állítania a környezetet az mindkét klasszikus és Resource Manager.

Jelentkezzen be a fiókjának a Resource Manager modellt.

```powershell
    Login-AzureRmAccount
```

Az elérhető előfizetések beolvasása a következő paranccsal:

```powershell
    Get-AzureRMSubscription | Sort Name | Select Name
```

Állítsa be az Azure-előfizetéshez az aktuális munkamenet. Ebben a példában a alapértelmezett előfizetés nevének beállítása **saját Azure-előfizetés**. Cserélje le a példában előfizetés nevét a saját.

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Regisztrációs egy egyszeri lépést, de egyszer a migrálás megkezdése előtt kell tennie. Ha nem regisztrálja, a következő hibaüzenet jelenik meg:
>
> *BadRequest: Az előfizetés nincs regisztrálva az áttelepítéshez.*
>
>

Az áttelepítés erőforrás-szolgáltató regisztrálása a következő paranccsal:

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Kis türelmet, a regisztráció befejezéséhez öt perc. A jóváhagyási állapotát a következő paranccsal ellenőrizheti:

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Győződjön meg arról, hogy van-e RegistrationState `Registered` folytatás előtt.

Most jelentkezzen be a Klasszikus modell esetében a fiókjába.

```powershell
    Add-AzureAccount
```

Az elérhető előfizetések beolvasása a következő paranccsal:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Állítsa be az Azure-előfizetéshez az aktuális munkamenet. Ez a példa állítja be az alapértelmezett előfizetés **saját Azure-előfizetés**. Cserélje le a példában előfizetés nevét a saját.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>5. lépés: Ellenőrizze, hogy elegendő Azure Resource Manager virtuális gép Vcpu Azure-régióban a jelenlegi üzemelő példány vagy virtuális hálózaton
A következő PowerShell-parancs segítségével ellenőrizze, rendelkezik az Azure Resource Manager Vcpu aktuális száma. VCPU kvóták kapcsolatos további információkért lásd: [korlátozásai és az Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

Ebben a példában elérhetőségét ellenőrzi a **USA nyugati régiója** régióban. Cserélje le a példában régió neve a saját.

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>6. lépés: Futtassa a parancsokat az IaaS-erőforrások áttelepítése
> [!NOTE]
> Itt leírt műveletek idempotent. Ha a probléma nem támogatott szolgáltatása vagy konfigurációs hiba, azt javasoljuk, hogy a prepare újra, vagy megszakításra műveletet. A platform majd próbálja megismételni a műveletet.
>
>

## <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>6.1. lépés: 1. lehetőség – (nem része virtuális hálózatnak) felhőszolgáltatás a virtuális gépek áttelepítése
A felhőszolgáltatások listájának lekérdezése a következő paranccsal, és ezután válassza ki a felhőalapú szolgáltatás, amely az áttelepíteni kívánt. Ha a felhőszolgáltatás a virtuális gépek egy virtuális hálózatot, vagy ha webes vagy feldolgozói szerepköröket, a parancs hibaüzenetet ad vissza.

```powershell
    Get-AzureService | ft Servicename
```

A központi telepítés nevét, a felhőszolgáltatás beolvasása. Ebben a példában a szolgáltatás neve megkülönbözteti **saját szolgáltatás**. A példa szolgáltatásnév cserélje le a saját szolgáltatás neve.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Készítse elő a virtuális gépek áttelepítése a felhőalapú szolgáltatás. Rendelkezik két lehetőség közül választhat.

* **1. lehetőség. A virtuális gépek áttelepítése platform által létrehozott virtuális hálózathoz**

    Először ellenőrzi, hogy áttelepítheti a felhőalapú szolgáltatás, a következő parancsokkal:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Az előző parancs megjeleníti a figyelmeztetések és hibák, amelyek blokkolják az áttelepítés. Ha az ellenőrzés nem jelez hibát, majd áthelyezheti a a **Prepare** . lépés:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **2. lehetőség. A Resource Manager üzembe helyezési modellel meglévő virtuális hálózat áttelepítése**

    Ebben a példában az erőforráscsoport neve állítja **myResourceGroup**, a virtuális hálózatok nevét, **myVirtualNetwork** és a alhálózati név **mySubNet**. Cserélje le a példában szereplő erőforrásnevek a saját.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Először ellenőrzi, hogy áttelepítheti a virtuális hálózat, a következő parancsot:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Az előző parancs megjeleníti a figyelmeztetések és hibák, amelyek blokkolják az áttelepítés. Ha az ellenőrzés nem jelez hibát, majd folytathatja a következő előkészítési lépés:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Után az előkészítési művelet sikeres, az előző beállítások valamelyikével, a lekérdezés a virtuális gépek áttelepítésének állapotát. Győződjön meg arról, hogy a `Prepared` állapotát.

Ebben a példában a virtuális gép nevének beállítása **myVM**. A példa neve cserélje le a saját virtuális gép nevét.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Ellenőrizze a konfigurációt, az előkészített erőforrások PowerShell vagy az Azure portál segítségével. Ha nem az áttelepítéshez, és térjen vissza a régi állapot szeretne, használja a következő parancsot:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Az előkészített konfiguráció megfelelőnek tűnik, ha előre, és véglegesíti az erőforrásokat a következő paranccsal:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

## <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>6.1. lépés: 2. lehetőség – a virtuális hálózatban lévő virtuális gépek áttelepítése

A virtuális hálózatban lévő virtuális gépek áttelepítéséhez telepítse át a virtuális hálózat. A virtuális gépek automatikusan áttelepíti a virtuális hálózathoz. Válassza ki az áttelepíteni kívánt virtuális hálózat.
> [!NOTE]
> [Egyetlen klasszikus virtuális gép áttelepítése](migrate-single-classic-to-resource-manager.md) hozzon létre egy új erőforrás-kezelő virtuális gépet felügyelt merevlemezzel a virtuális gép virtuális merevlemez (az operációs rendszer és data) fájlokat használja.
<br>

> [!NOTE]
> Lehet, hogy a virtuális hálózat neve eltér az új portálon is látható. Az új Azure-portál megjeleníti a nevet a következőként `[vnet-name]` a tényleges virtuális hálózati név típusú, de `Group [resource-group-name] [vnet-name]`. Mielőtt áttelepítené, keresés a tényleges virtuális hálózat neve, a parancs segítségével `Get-AzureVnetSite | Select -Property Name` vagy tekintse meg a régi Azure-portálon. 

Ebben a példában a virtuális hálózat nevének beállítása **myVnet**. Cserélje le a példa a virtuális hálózat nevére a saját.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Ha a virtuális hálózat nem támogatott konfigurációjú webes vagy feldolgozói szerepköröket, vagy a virtuális gépeket tartalmaz, egy érvényesítési hibaüzenet kap.
>
>

Először ellenőrzi, hogy a virtuális hálózati telepíthet át a következő paranccsal:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Az előző parancs megjeleníti a figyelmeztetések és hibák, amelyek blokkolják az áttelepítés. Ha az ellenőrzés nem jelez hibát, majd folytathatja a következő előkészítési lépés:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Ellenőrizze az előkészített virtuális gépek konfigurációját az Azure PowerShell vagy az Azure portál segítségével. Ha nem az áttelepítéshez, és térjen vissza a régi állapot szeretne, használja a következő parancsot:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Az előkészített konfiguráció megfelelőnek tűnik, ha előre, és véglegesíti az erőforrásokat a következő paranccsal:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

## <a name="step-62-migrate-a-storage-account"></a>A tárfiók lépés 6.2 áttelepítése
Miután befejezte a virtuális gépek áttelepítéséhez, azt javasoljuk, telepíti át, hogy a storage-fiókok.

A tárfiók az áttelepítés előtt hajtson végre megelőző előfeltétel-ellenőrzések:

* **Klasszikus, amelynek lemezek a storage-fiókban tárolt virtuális gépek áttelepítése**

    A tárfiók a klasszikus virtuális gép lemezeivel RoleName és DiskName tulajdonságainak parancs megelőző adja vissza. RoleName esetén a virtuális gép, amely a lemez csatolva van. Ha az előző parancs visszaadja lemezek majd győződjön meg arról, hogy, amelyhez ezeket a lemezek vannak csatolva hozzá virtuális gépek áttelepítése a tárfiók áttelepítése előtt.
    ```powershell
     $storageAccountName = 'yourStorageAccountName'
      Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
      DiskName | Format-List -Property RoleName, DiskName

    ```
* **A storage-fiókban tárolt választani klasszikus virtuális gépek lemezei törlése**

    Keresse meg a tárolóban nem csatolt klasszikus virtuális gépek lemezei fiók használatával a következő parancsot:

    ```powershell
        $storageAccountName = 'yourStorageAccountName'
        Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

    ```
    Ha a fenti parancs beolvasása lemezek törölje ezeket a következő parancs használatával lemezeket:

    ```powershell
       Remove-AzureDisk -DiskName 'yourDiskName'
    ```
* **A storage-fiókban tárolt Virtuálisgép-rendszerképek törlése**

    A Virtuálisgép-lemezképek parancs megelőző adja vissza a storage-fiókban tárolt operációsrendszer-lemezzel.
     ```powershell
        Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                } | Select-Object -Property ImageName, ImageLabel
     ```
     A storage-fiókban tárolt adatok lemezzel parancs megelőző adja vissza a Virtuálisgép-lemezképeket.
     ```powershell

        Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                         -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                        } | Select-Object -Property ImageName, ImageLabel
     ```
    Törölje az előző parancs használata parancsok fent által visszaadott összes virtuális gép lemezképet:
    ```powershell
    Remove-AzureVMImage -ImageName 'yourImageName'
    ```

Áttelepítés minden tárfiók érvényesítése a következő parancs használatával. Ebben a példában a tárfiók neve van **myStorageAccount**. A példa neve cserélje le a saját storage-fiók nevét.

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
```

Következő lépés az, hogy a tárfiók Felkészülés az áttelepítésre

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
```

Ellenőrizze az előkészített tárfiók konfigurációját az Azure PowerShell vagy az Azure portál segítségével. Ha nem az áttelepítéshez, és térjen vissza a régi állapot szeretne, használja a következő parancsot:

```powershell
    Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
```

Az előkészített konfiguráció megfelelőnek tűnik, ha előre, és véglegesíti az erőforrásokat a következő paranccsal:

```powershell
    Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
```

## <a name="next-steps"></a>További lépések
* [IaaS-erőforrásokra a klasszikus Azure Resource Manager platform által támogatott áttelepítésének áttekintése](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Műszaki részletes bemutatója a platform által támogatott áttelepítési a klasszikus Azure Resource Managerbe](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS-erőforrások áttelepítése a klasszikus Azure Resource Manager parancssori felület használatával](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS-erőforrásokra a klasszikus Azure Resource Manager áttelepítésének védelmével kapcsolatos közösségi eszközök](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakrabban feltett kérdésekre áttelepítése IaaS-erőforrásokra a klasszikus Azure Resource Manager áttekintése](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
