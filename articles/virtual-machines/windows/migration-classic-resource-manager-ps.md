---
title: Migrálás a Resource Managerbe a PowerShell használatával | Microsoft Docs
description: Ez a cikk a IaaS-erőforrások, például a virtuális gépek (VM-EK), a virtuális hálózatok (virtuális hálózatok), valamint a Klasszikusból a Azure Resource Managerba (ARM) való, Azure PowerShell parancsok használatával történő áttelepítését ismerteti.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: bf964f23b6c38444fb15b61161cb7ed5a2b15e00
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102659"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>IaaS-erőforrások migrálása klasszikusról Azure Resource Managerra Azure PowerShell használatával
Ezek a lépések bemutatják, hogyan használhatók Azure PowerShell parancsok a klasszikus üzemi modellből származó infrastruktúra szolgáltatásként (IaaS) a Azure Resource Manager üzembe helyezési modellbe való áttelepítéséhez.

Ha szeretné, az [Azure parancssori felületének (Azure CLI)](../linux/migration-classic-resource-manager-cli.md)használatával is áttelepítheti az erőforrásokat.

* A támogatott áttelepítési forgatókönyvek hátteréről lásd: a [IaaS-erőforrások platform által támogatott áttelepítése klasszikusról Azure Resource Managerra](migration-classic-resource-manager-overview.md).
* Részletes útmutatást és áttelepítési útmutatót a [klasszikus és Azure Resource Manager platform által támogatott](migration-classic-resource-manager-deep-dive.md)áttelepítéssel foglalkozó témakörben talál.
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md)

<br>
Az alábbi folyamatábra azt határozza meg, hogy milyen sorrendben kell végrehajtani a lépéseket egy áttelepítési folyamat során.

![Képernyőkép a migrálási lépésekről](media/migration-classic-resource-manager/migration-flow.png)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="step-1-plan-for-migration"></a>1\. lépés: Az áttelepítés megtervezése
Íme néhány ajánlott eljárás, amelyet ajánlunk a IaaS-erőforrások klasszikusról Resource Managerbe való áttelepítésének kiértékelése során:

* Olvassa el a [támogatott és nem támogatott funkciókat és konfigurációkat](migration-classic-resource-manager-overview.md). Ha olyan virtuális gépekkel rendelkezik, amelyek nem támogatott konfigurációkat vagy szolgáltatásokat használnak, javasoljuk, hogy várjon a konfiguráció/szolgáltatás támogatásának bejelentésére. Ha megfelel az igényeinek, távolítsa el ezt a szolgáltatást, vagy lépjen ki a konfigurációból az áttelepítés engedélyezéséhez.
* Ha olyan automatizált parancsfájlokkal rendelkezik, amelyek jelenleg telepítik az infrastruktúrát és az alkalmazásokat, próbáljon meg hasonló tesztet létrehozni a parancsfájlok áttelepítéshez való használatával. Azt is megteheti, hogy a Azure Portal használatával is beállíthatja a mintavételezési környezeteket.

> [!IMPORTANT]
> Az Application Gateway-átjárók jelenleg nem támogatottak a Klasszikusból a Resource Managerbe való áttelepítéshez. Egy klasszikus virtuális hálózat Application Gateway-átjáróval való áttelepítéséhez távolítsa el az átjárót, mielőtt egy előkészítési művelet futtatása után áthelyezi a hálózatot. Az áttelepítés befejezése után csatlakoztassuk újra az átjárót Azure Resource Manager.
>
>A ExpressRoute-áramkörökhöz egy másik előfizetésben csatlakozó ExpressRoute-átjárók nem telepíthetők át automatikusan. Ilyen esetekben távolítsa el a ExpressRoute-átjárót, telepítse át a virtuális hálózatot, és hozza létre újra az átjárót. További információkért lásd: [ExpressRoute-áramkörök és társított virtuális hálózatok áttelepítése a Klasszikusból a Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md) -alapú üzemi modellbe.

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>2\. lépés: A Azure PowerShell legújabb verziójának telepítése
A Azure PowerShell telepítésének két fő lehetősége van: [PowerShell-Galéria](https://www.powershellgallery.com/profiles/azure-sdk/) vagy [webplatform-telepítő (WebPI)](https://aka.ms/webpi-azps). A WebPI havi frissítéseket fogad. A PowerShell-galéria folyamatosan fogadja a frissítéseket. Ez a cikk a Azure PowerShell 2.1.0 verzióján alapul.

A telepítési utasításokért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>3\. lépés: Győződjön meg arról, hogy Ön az előfizetés rendszergazdája Azure Portal
Az áttelepítés végrehajtásához hozzá kell adnia az előfizetés társ-rendszergazdaként való hozzáadását a [Azure Portal](https://portal.azure.com).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központi menüben válassza az **előfizetés**lehetőséget. Ha nem látja, válassza a **minden szolgáltatás**lehetőséget.
3. Keresse meg a megfelelő előfizetési bejegyzést, majd tekintse meg a **saját szerepkör** mezőt. A társ-rendszergazda esetében az értéknek _Account admin_értékűnek kell lennie.

Ha nem tud hozzáadni egy társ-rendszergazdát, vegye fel a kapcsolatot a szolgáltatás rendszergazdájával vagy az előfizetés közös rendszergazdájával.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>4\. lépés: Előfizetés beállítása és regisztráció áttelepítésre
Először indítson el egy PowerShell-parancssort. Az áttelepítéshez be kell állítania a környezetet a klasszikus és a Resource Managerhez is.

Jelentkezzen be a fiókjába a Resource Manager-modellhez.

```powershell
    Connect-AzAccount
```

Szerezze be az elérhető előfizetéseket a következő paranccsal:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Állítsa be az Azure-előfizetését az aktuális munkamenethez. Ez a példa az alapértelmezett előfizetés nevét állítja be az **Azure**-előfizetésre. Cserélje le a példában szereplő előfizetés nevét a saját nevére.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> A regisztráció egyszeri lépés, de csak egyszer kell megtennie az áttelepítés megkísérlése előtt. A regisztráció nélkül a következő hibaüzenet jelenik meg:
>
> *BadRequest Az előfizetés nincs regisztrálva áttelepítésre.*

Regisztrálja az áttelepítési erőforrás-szolgáltatót a következő parancs használatával:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Várjon öt percet, amíg a regisztráció befejeződik. A jóváhagyás állapotát a következő parancs használatával tekintheti meg:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

A `Registered` folytatás előtt győződjön meg arról, hogy a RegistrationState.

Most jelentkezzen be a fiókjába a klasszikus modellhez.

```powershell
    Add-AzureAccount
```

Szerezze be az elérhető előfizetéseket a következő paranccsal:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Állítsa be az Azure-előfizetését az aktuális munkamenethez. Ez a példa az alapértelmezett előfizetést állítja be az **Azure**-előfizetésre. Cserélje le a példában szereplő előfizetés nevét a saját nevére.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>5\. lépés: Győződjön meg arról, hogy van-e elegendő Azure Resource Manager virtuálisgép-vCPU az aktuális üzembe helyezési vagy VNET Azure-régiójában
A következő PowerShell-paranccsal ellenőrizhető, hogy az aktuálisan hány vCPU Azure Resource Manager. További információ a vCPU-kvótákkal kapcsolatban: [korlátok és a Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-azure-resource-manager).

Ez a példa az **USA nyugati** régiójában lévő rendelkezésre állást ellenőrzi. Cserélje le a példában szereplő régió nevét a saját nevére.

```powershell
Get-AzVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>6\. lépés: Parancsok futtatása a IaaS-erőforrások áttelepíteni
* [Virtuális gépek migrálása felhőalapú szolgáltatásokban (nem a virtuális hálózaton)](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Virtuális gépek migrálása virtuális hálózatban](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Storage-fiók migrálása](#step-62-migrate-a-storage-account)

> [!NOTE]
> Az itt leírt összes művelet idempotens. Ha a nem támogatott funkció vagy a konfigurációs hiba nem megfelelő, javasoljuk, hogy próbálkozzon újra az előkészítés, a megszakítás vagy a végrehajtás művelettel. A platform ezután újra próbálkozik a művelettel.


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>6,1. lépés: 1. lehetőség – a virtuális gépek áttelepítése egy felhőalapú szolgáltatásba (nem virtuális hálózatban)
A következő parancs használatával szerezze be a Cloud Services listáját, majd válassza ki az áttelepíteni kívánt felhőalapú szolgáltatást. Ha a felhőalapú szolgáltatásban lévő virtuális gépek virtuális hálózaton vannak, vagy webes vagy feldolgozói szerepkörökkel rendelkeznek, a parancs hibaüzenetet ad vissza.

```powershell
    Get-AzureService | ft Servicename
```

Szerezze be a felhőalapú szolgáltatás központi telepítési nevét. Ebben a példában a szolgáltatás neve a **saját szolgáltatás**. Cserélje le a példában szereplő szolgáltatásnevet a saját szolgáltatás nevére.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Készítse elő a virtuális gépeket a Cloud Service-ben áttelepítésre. Két lehetőség közül választhat.

* **1. lehetőség. Virtuális gépek migrálása platformmal létrehozott virtuális hálózatba**

    Először is ellenőrizze, hogy a következő parancsok segítségével áttelepítheti-e a Cloud Service-t:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    A következő parancs megjeleníti az áttelepítést tiltó figyelmeztetéseket és hibákat. Ha az érvényesítés sikeres, akkor átléphet az **előkészítési** lépésre:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **2. lehetőség. Migrálás meglévő virtuális hálózatra a Resource Manager-alapú üzemi modellben**

    Ez a példa az erőforráscsoport nevét állítja be **myResourceGroup**, a virtuális hálózat nevét a **myVirtualNetwork** és az alhálózat nevét a **mySubNet**értékre. Cserélje le a példában szereplő neveket a saját erőforrásainak nevére.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Először is ellenőrizze, hogy a következő paranccsal telepítheti-e át a virtuális hálózatot:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    A következő parancs megjeleníti az áttelepítést tiltó figyelmeztetéseket és hibákat. Ha az érvényesítés sikeres, akkor folytathatja a következő előkészítési lépést:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Miután az előkészítési művelet sikeresen megtörtént az előző beállítások bármelyikével, lekérdezheti a virtuális gépek áttelepítési állapotát. Győződjön meg arról, hogy az `Prepared` állapotban vannak.

Ebben a példában a virtuális gép nevét **myVM**értékre állítja. Cserélje le a példában szereplő nevet a saját virtuális gépe nevére.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

A PowerShell vagy a Azure Portal használatával vizsgálja meg az előkészített erőforrások konfigurációját. Ha nem áll készen az áttelepítésre, és vissza szeretne térni a régi állapotra, használja a következő parancsot:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Ha az előkészített konfiguráció jól néz ki, a következő parancs használatával továbbíthatja és véglegesítheti az erőforrásokat:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>6,1. lépés: 2. lehetőség – virtuális gépek migrálása virtuális hálózatban

A virtuális gépek virtuális hálózatban való áttelepíthetők a virtuális hálózat áttelepíthetők. A virtuális gépeket a rendszer automatikusan áttelepíti a virtuális hálózattal. Válassza ki az áttelepíteni kívánt virtuális hálózatot.
> [!NOTE]
> Egy [klasszikus virtuális gép](migrate-single-classic-to-resource-manager.md) áttelepítéséhez hozzon létre egy új Resource Manager-alapú virtuális gépet a Managed Disks használatával a virtuális gép VHD-fájljának (operációsrendszer-és adatfájljai) segítségével.
<br>

> [!NOTE]
> Lehet, hogy a virtuális hálózat neve eltér az új portálon láthatótól. Az új Azure Portal a nevet jeleníti `[vnet-name]` meg, de a tényleges virtuális hálózat neve típusú `Group [resource-group-name] [vnet-name]`. Migrálás előtt a tényleges virtuális hálózat nevét a paranccsal `Get-AzureVnetSite | Select -Property Name` vagy a régi Azure Portalon tekintheti meg. 

Ez a példa a virtuális hálózat nevét állítja be a **myVnet**. Cserélje le a példában szereplő virtuális hálózat nevét a saját nevére.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Ha a virtuális hálózat webes vagy feldolgozói szerepköröket, illetve nem támogatott konfigurációval rendelkező virtuális gépeket tartalmaz, érvényesítési hibaüzenetet kap.

Először is ellenőrizze, hogy a következő paranccsal telepítheti-e át a virtuális hálózatot:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

A következő parancs megjeleníti az áttelepítést tiltó figyelmeztetéseket és hibákat. Ha az érvényesítés sikeres, akkor folytathatja a következő előkészítési lépést:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Az előkészített virtuális gépek konfigurációját a Azure PowerShell vagy a Azure Portal használatával vizsgálhatja meg. Ha nem áll készen az áttelepítésre, és vissza szeretne térni a régi állapotra, használja a következő parancsot:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Ha az előkészített konfiguráció jól néz ki, a következő parancs használatával továbbíthatja és véglegesítheti az erőforrásokat:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-62-migrate-a-storage-account"></a>6,2. lépés a Storage-fiók migrálása
Ha befejezte a virtuális gépek áttelepítését, javasoljuk, hogy a Storage-fiókok áttelepítése előtt végezze el az alábbi előfeltételek ellenőrzését.

> [!NOTE]
> Ha a Storage-fiók nem rendelkezik társított lemezekkel vagy virtuálisgép-adatmennyiséggel, akkor közvetlenül a **Storage-fiók ellenőrzése és az áttelepítés** megkezdése szakaszban lehet kihagyni.

* **Előfeltételek ellenőrzése ha a virtuális gépeket áttelepítette, vagy a Storage-fiók lemezes erőforrásokkal rendelkezik**
    * **Telepítse át azokat a klasszikus virtuális gépeket, amelyek lemezei a Storage-fiókban tárolódnak**

        A következő parancs a Storage-fiókban lévő összes klasszikus virtuálisgép-lemez RoleName és DiskName tulajdonságait adja vissza. A RoleName annak a virtuális gépnek a neve, amelyhez a lemez csatolva van. Ha a parancs lemezeket ad vissza, akkor győződjön meg arról, hogy a Storage-fiók áttelepítése előtt a rendszer áttelepíti azokat a virtuális gépeket, amelyekre ezek a lemezek csatlakoztatva vannak.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * **A Storage-fiókban tárolt, nem csatlakoztatott klasszikus virtuálisgép-lemezek törlése**

        A nem csatolt klasszikus virtuálisgép-lemezeket a Storage-fiókban a következő parancs használatával keresheti meg:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Ha a fenti parancs visszaadja a lemezeket, akkor a következő paranccsal törölheti ezeket a lemezeket:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * **A Storage-fiókban tárolt virtuálisgép-lemezképek törlése**

        A következő parancs a Storage-fiókban tárolt operációsrendszer-lemezzel rendelkező virtuálisgép-lemezképeket adja vissza.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         A következő parancs az összes virtuálisgép-rendszerképet visszaadja a Storage-fiókban tárolt adatlemezekkel.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Törölje az összes fenti parancs által visszaadott virtuálisgép-lemezképet a következő parancs használatával:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* **A Storage-fiók ellenőrzése és az áttelepítés megkezdése**

    A következő parancs használatával ellenőrizze az egyes Storage-fiókok áttelepítését. Ebben a példában a Storage-fiók neve **myStorageAccount**. Cserélje le a példában szereplő nevet a saját Storage-fiókja nevére.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    A következő lépés a Storage-fiók előkészítése az áttelepítéshez

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Az előkészített Storage-fiók konfigurációját a Azure PowerShell vagy a Azure Portal használatával vizsgálhatja meg. Ha nem áll készen az áttelepítésre, és vissza szeretne térni a régi állapotra, használja a következő parancsot:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Ha az előkészített konfiguráció jól néz ki, a következő parancs használatával továbbíthatja és véglegesítheti az erőforrásokat:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>További lépések
* [A IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A CLI használatával IaaS-erőforrásokat telepíthet át a klasszikusról Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Közösségi eszközök a IaaS-erőforrások Klasszikusból Azure Resource Managerba való áttelepítésének támogatásához](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Tekintse át a IaaS-erőforrások klasszikusról Azure Resource Managerra való áttelepítésével kapcsolatos leggyakoribb kérdéseket](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
