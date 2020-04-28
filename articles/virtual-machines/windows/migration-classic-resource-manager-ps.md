---
title: Áttelepítés az Erőforrás-kezelőbe a PowerShell használatával
description: Ez a cikk bemutatja az IaaS-erőforrások, például a virtuális gépek , a virtuális hálózatok és a tárfiókok platformáltal támogatott migrálását a klasszikusról az Azure Resource Managerbe az Azure PowerShell-parancsok használatával
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 314d7a4725709f00ba5cdbf54595857502bc5805
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865947"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>IaaS-erőforrások áttelepítése klasszikusról az Azure Resource Managerbe a PowerShell használatával

> [!IMPORTANT]
> Ma az IaaS virtuális gépek mintegy 90%-a használja az [Azure Resource Managert.](https://azure.microsoft.com/features/resource-manager/) 2020. február 28-tól a klasszikus virtuális gépek elavultak, és 2023. március 1-jén teljesen nyugdíjba kerülnek. [Tudjon meg többet]( https://aka.ms/classicvmretirement) erről az eprecációról [és arról, hogy ez milyen hatással van Önre.](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)

Ezek a lépések bemutatják, hogyan használhatja az Azure PowerShell-parancsokat az infrastruktúra szolgáltatásként (IaaS) szolgáltatásként történő áttelepítéséhez a klasszikus üzembe helyezési modellről az Azure Resource Manager üzembe helyezési modellre.

Ha szeretné, az [Azure CLI](../linux/migration-classic-resource-manager-cli.md)használatával is áttelepítheti az erőforrásokat.

* A támogatott áttelepítési forgatókönyvek hátterét az [IaaS-erőforrások klasszikusról Azure Resource Manager betekintése című témakörben olvassa](migration-classic-resource-manager-overview.md)el.
* Részletes útmutatást és áttelepítési forgatókönyvet a platform által támogatott klasszikusról az [Azure Resource Managerre való áttérés technikai részletes áttelepítése](migration-classic-resource-manager-deep-dive.md)című témakörben talál.
* [Tekintse át a leggyakoribb áttelepítési hibákat](migration-classic-resource-manager-errors.md).

<br>
Az alábbi folyamatábra azonosítja, hogy milyen sorrendben kell végrehajtani a lépéseket az áttelepítési folyamat során.

![Képernyőkép a migrálási lépésekről](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>1. lépés: Migrációs terv
Íme néhány ajánlott eljárás, amelyet az IaaS-erőforrások klasszikusról erőforrás-kezelőre való áttelepítése korának kiértékelésére ajánlott:

* Olvassa el a [támogatott és nem támogatott szolgáltatásokat és konfigurációkat.](migration-classic-resource-manager-overview.md) Ha nem támogatott konfigurációkat vagy szolgáltatásokat használó virtuális gépekkel rendelkezik, várja meg, amíg bejelentia konfigurációt vagy szolgáltatástámogatást. Másik lehetőségként, ha megfelel az igényeinek, távolítsa el a funkciót, vagy helyezze n ki a konfigurációból az áttelepítés engedélyezéséhez.
* Ha az infrastruktúra és az alkalmazások üzembe helyezése automatikus parancsfájlok ma, próbálja meg létrehozni egy hasonló teszt beállítás segítségével ezeket a parancsfájlokat az áttelepítéshez. Másik lehetőségként beállíthat mintakörnyezeteket az Azure Portal használatával.

> [!IMPORTANT]
> Az alkalmazásátjárók jelenleg nem támogatottak a klasszikusról az Erőforrás-kezelőre való áttéréshez. Ha egy alkalmazásátjáróval szeretne virtuális hálózatot áttelepíteni, távolítsa el az átjárót, mielőtt előkészíti a hálózat áthelyezését. Miután befejezte az áttelepítést, csatlakoztassa újra az átjárót az Azure Resource Managerben.
>
> Az ExpressRoute-átjárók, amelyek egy másik előfizetésben csatlakoznak az ExpressRoute-áramkörökhöz, nem telepíthetők át automatikusan. Ilyen esetekben távolítsa el az ExpressRoute-átjárót, telepítse át a virtuális hálózatot, és hozza létre újra az átjárót. További információ: [ExpressRoute-áramkörök és a kapcsolódó virtuális hálózatok áttelepítése a klasszikusról az Erőforrás-kezelő telepítési modelljére.](../../expressroute/expressroute-migration-classic-resource-manager.md)

## <a name="step-2-install-the-latest-version-of-powershell"></a>2. lépés: A PowerShell legújabb verziójának telepítése
Az Azure PowerShell telepítésének két fő lehetősége van: [a PowerShell-galéria](https://www.powershellgallery.com/profiles/azure-sdk/) vagy [a WebPLATFORM Installer (WebPI).](https://aka.ms/webpi-azps) A WebPI havi frissítéseket kap. A PowerShell-galéria folyamatosan frissítéseket kap. Ez a cikk az Azure PowerShell 2.1.0-s verzióján alapul.

A telepítési útmutatót az [Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)című témakörben találja.

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>3. lépés: Győződjön meg arról, hogy ön az előfizetés rendszergazdája
Az áttelepítés végrehajtásához hozzá kell adni a társadminisztrátorként az előfizetés az [Azure Portalon.](https://portal.azure.com)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A **Központ menüben** válassza az **Előfizetés**lehetőséget. Ha nem látja, válassza a **Minden szolgáltatás lehetőséget.**
3. Keresse meg a megfelelő előfizetési bejegyzést, majd tekintse meg a **SAJÁT SZEREPKÖR** mezőt. A társadminisztrátorok esetében az értéknek _fiókadminisztrátornak_kell lennie.

Ha nem tud társadminisztrátort felvenni, lépjen kapcsolatba egy szolgáltatásrendszergazdával vagy az előfizetés társrendszergazdájával, hogy felvegye magát.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>4. lépés: Állítsa be az előfizetést, és regisztráljon az áttelepítésre
Először indítsa el a PowerShell-parancssora. Az áttelepítéshez állítsa be a környezetet a klasszikus és az Erőforrás-kezelő számára is.

Jelentkezzen be a fiókjába az Erőforrás-kezelő modellhez.

```powershell
    Connect-AzAccount
```

A rendelkezésre álló előfizetések beszerzése a következő paranccsal:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Állítsa be az Azure-előfizetést az aktuális munkamenethez. Ez a példa az alapértelmezett előfizetési nevet **a My Azure Subscription értékre állítja.** Cserélje le a példa előfizetés nevét a saját.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> A regisztráció egyszeri lépés, de az áttelepítés megkísérlése előtt egyszer kell megtennie. Regisztráció nélkül a következő hibaüzenet jelenik meg:
>
> *BadRequest : Az előfizetés nincs regisztrálva az áttelepítéshez.*

Regisztráljon az áttelepítési erőforrás-szolgáltatónál a következő paranccsal:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Várjon öt percet, amíg a regisztráció befejeződik. Ellenőrizze a jóváhagyás állapotát a következő paranccsal:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Győződjön meg arról, hogy RegistrationState van, `Registered` mielőtt folytatná.

Mielőtt átváltana a klasszikus üzembe helyezési modellre, győződjön meg arról, hogy elegendő Azure Resource Manager virtuálisgép-vCPU-val rendelkezik az aktuális üzembe helyezés vagy virtuális hálózat Azure-régiójában. A következő PowerShell-parancs segítségével ellenőrizheti az Azure Resource Manager ben használt vCPU-k aktuális számát. A vCPU-kvótákról a [Korlátok és az Azure Resource Manager](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)című témakörben olvashat bővebben.

Ez a példa ellenőrzi a rendelkezésre álló **az USA nyugati régiójában.** Cserélje le a példa terület nevét a saját.

```powershell
    Get-AzVMUsage -Location "West US"
```

Most jelentkezzen be a fiókjába a klasszikus üzembe helyezési modellhez.

```powershell
    Add-AzureAccount
```

A rendelkezésre álló előfizetések beszerzése a következő paranccsal:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Állítsa be az Azure-előfizetést az aktuális munkamenethez. Ez a példa a My Azure Subscription alapértelmezett előfizetését **állítja be.** Cserélje le a példa előfizetés nevét a saját.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>5. lépés: Parancsok futtatása az IaaS-erőforrások áttelepítéséhez
* [Virtuális gépek áttelepítése felhőszolgáltatásban (nem virtuális hálózatban)](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Virtuális gépek áttelepítése virtuális hálózatban](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Tárfiók áttelepítése](#step-52-migrate-a-storage-account)

> [!NOTE]
> Az itt leírt műveletek mindegyike idempotens. Ha nem támogatott szolgáltatásvagy konfigurációs hiba miatt nem probléma merült fel, javasoljuk, hogy próbálkozzon újra az előkészítési, megszakítási vagy véglegesítési művelettel. A platform ezután újra próbálkozik a művelettel.


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>5.1. lépés: 1. lehetőség – Virtuális gépek áttelepítése felhőszolgáltatásban (nem virtuális hálózatban)
A felhőszolgáltatások listájának beszerezése a következő paranccsal. Ezután válassza ki az áttelepíteni kívánt felhőszolgáltatást. Ha a virtuális gépek a felhőszolgáltatásban egy virtuális hálózatban, vagy ha rendelkeznek webes vagy feldolgozói szerepkörök, a parancs hibaüzenetet ad vissza.

```powershell
    Get-AzureService | ft Servicename
```

A felhőszolgáltatás központi telepítési nevének beszereznie. Ebben a példában a szolgáltatás neve **Saját szolgáltatás**. Cserélje le a példaszolgáltatás nevét a saját szolgáltatásnevére.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Készítse elő a virtuális gépeket a felhőszolgáltatásban az áttelepítéshez. Két lehetőség közül választhat.

* **1. lehetőség: Telepítse át a virtuális gépeket egy platform által létrehozott virtuális hálózatra.**

    Először ellenőrizze, hogy áttelepítheti-e a felhőszolgáltatást a következő parancsokkal:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    A következő parancs az áttelepítést tiltó figyelmeztetéseket és hibákat jeleníti meg. Ha az ellenőrzés sikeres, továbbléphet a Felkészülés lépésre.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **2. lehetőség: Áttelepítés egy meglévő virtuális hálózatra az Erőforrás-kezelő telepítési modelljében.**

    Ez a példa az erőforráscsoport nevét **a myResourceGroup-ra,** a virtuális hálózat nevét **a myVirtualNetwork-re,** az alhálózat nevét pedig a **mySubNet-re állítja**be. Cserélje le a példában szereplő neveket a saját erőforrások nevére.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Először ellenőrizze, hogy a virtuális hálózat áttelepíthető-e a következő paranccsal:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    A következő parancs az áttelepítést tiltó figyelmeztetéseket és hibákat jeleníti meg. Ha az ellenőrzés sikeres, folytathatja a következő Előkészítés i akövetkező lépést:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Miután a Előkészítés művelet sikeres lett az előző beállítások bármelyikével, lekérdezi a virtuális gépek áttelepítési állapotát. Gondoskodj róla, hogy `Prepared` az államban legyenek.

Ez a példa a virtuális gép nevét **a myVM-re állítja**be. Cserélje le a példa nevét a saját virtuálisgép-nevére.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Ellenőrizze az előkészített erőforrások konfigurációját a PowerShell vagy az Azure Portal használatával. Ha még nem áll készen az áttelepítésre, és vissza szeretne lépni a régi állapotba, használja a következő parancsot:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Ha az előkészített konfiguráció jól néz ki, a következő paranccsal előreléphet és véglegesítheti az erőforrásokat:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>5.1 lépés: 2. lehetőség – Virtuális gépek áttelepítése virtuális hálózatban

Virtuális gépek virtuális hálózatban való áttelepítéséhez telepítse át a virtuális hálózatot. A virtuális gépek automatikusan áttelepülnek a virtuális hálózattal. Válassza ki az áttelepíteni kívánt virtuális hálózatot.
> [!NOTE]
> A klasszikus központi telepítési modell használatával létrehozott [egyetlen virtuális gép áttelepítése](migrate-single-classic-to-resource-manager.md) egy új Erőforrás-kezelő virtuális gép felügyelt lemezekkel a virtuális gép virtuális gép virtuális merevlemez-fájljainak (operációs rendszer és adatok) használatával.
<br>

> [!NOTE]
> A virtuális hálózat neve eltérhet az új portálon láthatótól. Az új Azure portalon `[vnet-name]`a név a , de `Group [resource-group-name] [vnet-name]`a tényleges virtuális hálózat neve típus. Az áttelepítés megkezdése előtt keresse meg a tényleges virtuális `Get-AzureVnetSite | Select -Property Name` hálózat nevét a parancs használatával, vagy tekintse meg a régi Azure Portalon. 

Ez a példa a virtuális hálózat nevét **a myVnet**-re állítja. Cserélje le a példa virtuális hálózat nevét a saját.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Ha a virtuális hálózat webes vagy feldolgozói szerepköröket, vagy nem támogatott konfigurációval rendelkező virtuális gépeket tartalmaz, érvényesítési hibaüzenet jelenik meg.

Először ellenőrizze, hogy a virtuális hálózat áttelepíthető-e a következő paranccsal:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

A következő parancs az áttelepítést tiltó figyelmeztetéseket és hibákat jeleníti meg. Ha az ellenőrzés sikeres, folytathatja a következő Előkészítés i akövetkező lépést:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Ellenőrizze az előkészített virtuális gépek konfigurációját az Azure PowerShell vagy az Azure Portal használatával. Ha még nem áll készen az áttelepítésre, és vissza szeretne lépni a régi állapotba, használja a következő parancsot:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Ha az előkészített konfiguráció jól néz ki, a következő paranccsal előreléphet és véglegesítheti az erőforrásokat:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>5.2 lépés: Tárfiók áttelepítése
Miután befejezte a virtuális gépek áttelepítését, hajtsa végre a következő előfeltétel-ellenőrzéseket a tárfiókok áttelepítése előtt.

> [!NOTE]
> Ha a tárfiók nem rendelkezik társított lemezekkel vagy virtuális gép adatokkal, közvetlenül ugorhat a "Tárfiókok ellenőrzése és az áttelepítés indítása" szakaszra.

* Előfeltétel-ellenőrzések, ha telepítette a virtuális gépek vagy a tárfiók lemezerőforrásokkal rendelkezik:
    * Telepítse át azokat a virtuális gépeket, amelyek lemezeit a tárfiók tárolja.

        A következő parancs a tárfiók összes virtuálisgép-lemezének Szerepkör- és DiskName tulajdonságát adja vissza. A RoleName annak a virtuális gépnek a neve, amelyhez a lemez csatlakozik. Ha ez a parancs lemezeket ad vissza, győződjön meg arról, hogy a tárfiók áttelepítése előtt áttelepíti azokat a virtuális gépeket, amelyekhez ezek a lemezek kapcsolódnak.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Törölje a tárfiókban tárolt nem csatlakoztatott virtuálisgép-lemezeket.

        A nem csatlakoztatott virtuálisgép-lemezek megkeresése a tárfiókban a következő paranccsal:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Ha az előző parancs lemezeket ad vissza, törölje ezeket a lemezeket a következő paranccsal:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * A tárfiókban tárolt virtuálisgép-lemezképek törlése.

        A következő parancs visszaadja az összes virtuális gép lemezképek operációs rendszer a tárfiókban tárolt rendszerű lemezekkel.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         A következő parancs visszaadja az összes virtuális gép lemezképek adatlemezekkel a tárfiókban tárolt.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Törölje az előző parancsok által visszaadott összes virtuális gépképet ezzel a paranccsal:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* Ellenőrizze a tárfiókokat, és indítsa el az áttelepítést.

    Ellenőrizze az egyes tárfiók áttelepítési a következő parancs használatával. Ebben a példában a tárfiók neve **myStorageAccount**. Cserélje le a példa nevét a saját tárfiók nevére.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    A következő lépés a tárfiók előkészítése az áttelepítéshez.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Ellenőrizze az előkészített tárfiók konfigurációját az Azure PowerShell vagy az Azure Portal használatával. Ha még nem áll készen az áttelepítésre, és vissza szeretne lépni a régi állapotba, használja a következő parancsot:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Ha az előkészített konfiguráció jól néz ki, a következő paranccsal előreléphet és véglegesítheti az erőforrásokat:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>További lépések
* [Az IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Managerre](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A CLI használatával áttelepítheti az IaaS-erőforrásokat klasszikusról Azure Resource Managerre](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Közösségi eszközök az IaaS-erőforrások klasszikusról Azure Resource Managerre való áttelepítéséhez](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Tekintse át az IaaS-erőforrások klasszikusról Azure Resource Managerbe való áttelepítésével kapcsolatos leggyakoribb kérdéseket](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
