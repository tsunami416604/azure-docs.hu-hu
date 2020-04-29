---
title: Virtuális gépek migrálása a Resource Managerbe az Azure CLI használatával
description: Ez a cikk végigvezeti az erőforrások platformon támogatott áttelepítésének klasszikusról Azure Resource Managerre az Azure CLI használatával
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: c41292a05e5c857cd0b1c120784a400f2f5410ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78945358"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>IaaS-erőforrások migrálása a klasszikusból Resource Manager-alapú környezetbe az Azure CLI használatával

> [!IMPORTANT]
> Napjainkban a IaaS virtuális gépek 90%-a [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)használ. 2020. február 28-án a klasszikus virtuális gépek elavultak, és 2023. március 1-jén teljesen megszűnnek. [További]( https://aka.ms/classicvmretirement) információ erről az elavult szolgáltatásról, valamint arról, [hogy Ön hogyan befolyásolja Önt](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Ezek a lépések bemutatják, hogyan használhatja az Azure parancssori felület (CLI) parancsait az infrastruktúra szolgáltatásként (IaaS) való áttelepítésére a klasszikus üzemi modellből a Azure Resource Manager telepítési modellbe. A cikkhez a [klasszikus Azure parancssori](../../cli-install-nodejs.md)felület szükséges. Mivel az Azure CLI csak Azure Resource Manager erőforrásokra alkalmazható, nem használható ehhez az áttelepítéshez.

> [!NOTE]
> Az itt leírt összes művelet idempotens. Ha a nem támogatott funkció vagy a konfigurációs hiba nem megfelelő, javasoljuk, hogy próbálkozzon újra az előkészítés, a megszakítás vagy a végrehajtás művelettel. A platform ezután újra próbálkozik a művelettel.
> 
> 

<br>
Az alábbi folyamatábra azt határozza meg, hogy milyen sorrendben kell végrehajtani a lépéseket egy áttelepítési folyamat során.

![Képernyőkép a migrálási lépésekről](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>1. lépés: Felkészülés az áttelepítésre
Íme néhány ajánlott eljárás, amelyet ajánlunk a IaaS-erőforrások klasszikusról Resource Managerbe való áttelepítésének kiértékelése során:

* Olvassa el a nem [támogatott konfigurációk vagy szolgáltatások listáját](../windows/migration-classic-resource-manager-overview.md). Ha olyan virtuális gépekkel rendelkezik, amelyek nem támogatott konfigurációkat vagy szolgáltatásokat használnak, javasoljuk, hogy várjon, amíg bejelenti a szolgáltatás/konfiguráció támogatását. Azt is megteheti, hogy eltávolítja ezt a szolgáltatást, vagy kilép az adott konfigurációból, hogy engedélyezze az áttelepítést, ha az megfelel az igényeinek.
* Ha olyan automatizált parancsfájlokkal rendelkezik, amelyek jelenleg telepítik az infrastruktúrát és az alkalmazásokat, próbáljon meg hasonló tesztet létrehozni a parancsfájlok áttelepítéshez való használatával. Azt is megteheti, hogy a Azure Portal használatával is beállíthatja a mintavételezési környezeteket.

> [!IMPORTANT]
> Az Application Gateway-átjárók jelenleg nem támogatottak a Klasszikusból a Resource Managerbe való áttelepítéshez. Egy klasszikus virtuális hálózat Application Gateway-átjáróval való áttelepítéséhez távolítsa el az átjárót, mielőtt egy előkészítési művelet futtatása után áthelyezi a hálózatot. Az áttelepítés befejezése után csatlakoztassuk újra az átjárót Azure Resource Manager. 
>
>A ExpressRoute-áramkörökhöz egy másik előfizetésben csatlakozó ExpressRoute-átjárók nem telepíthetők át automatikusan. Ilyen esetekben távolítsa el a ExpressRoute-átjárót, telepítse át a virtuális hálózatot, és hozza létre újra az átjárót. További információkért lásd: [ExpressRoute-áramkörök és társított virtuális hálózatok áttelepítése a Klasszikusból a Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md) -alapú üzemi modellbe.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>2. lépés: az előfizetés beállítása és a szolgáltató regisztrálása
Áttelepítési forgatókönyvek esetén a környezetet a klasszikus és a Resource Managerhez is be kell állítania. [Telepítse az Azure CLI](../../cli-install-nodejs.md) -t, és [válassza ki az előfizetését](/cli/azure/authenticate-azure-cli).

Jelentkezzen be a fiókjába.

    azure login

Az alábbi parancs használatával válassza ki az Azure-előfizetést.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> A regisztráció egy egyszeri lépés, de az áttelepítés megkísérlése előtt egyszer kell elvégezni. Regisztráció nélkül az alábbi hibaüzenet jelenik meg 
> 
> *BadRequest: az előfizetés nincs regisztrálva áttelepítésre.* 
> 
> 

Regisztrálja az áttelepítési erőforrás-szolgáltatót az alábbi parancs használatával. Vegye figyelembe, hogy bizonyos esetekben a parancs időtúllépést eredményez. A regisztráció azonban sikeres lesz.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Várjon öt percet, amíg a regisztráció befejeződik. A jóváhagyás állapotát a következő parancs használatával tekintheti meg. A `Registered` folytatás előtt győződjön meg arról, hogy a RegistrationState.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Most váltson a CLI- `asm` re a módba.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>3. lépés: Győződjön meg arról, hogy rendelkezik-e elegendő Azure Resource Manager virtuálisgép-vCPU a jelenlegi üzembe helyezési vagy VNET Azure-régiójában
Ehhez a lépéshez át kell váltania `arm` módba. Ezt a következő paranccsal teheti meg.

```
azure config mode arm
```

A következő CLI-paranccsal ellenőrizhető, hogy az aktuálisan hány vCPU Azure Resource Manager. További információ a vCPU-kvótákkal kapcsolatban: [korlátok és a Azure Resource Manager](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Ha elkészült a lépés ellenőrzésével, visszaválthat `asm` módba.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>4. lépés: 1. lehetőség – virtuális gépek migrálása egy felhőalapú szolgáltatásban
A következő parancs használatával szerezze be a Cloud Services listáját, majd válassza ki az áttelepíteni kívánt felhőalapú szolgáltatást. Vegye figyelembe, hogy ha a felhőalapú szolgáltatásban lévő virtuális gépek virtuális hálózaton vannak, vagy webes/feldolgozói szerepkörökkel rendelkeznek, hibaüzenet jelenik meg.

    azure service list

Futtassa a következő parancsot a felhőalapú szolgáltatás központi telepítési nevének a részletes kimenetből való lekéréséhez. A legtöbb esetben a központi telepítés neve megegyezik a felhőalapú szolgáltatás nevével.

    azure service show <serviceName> -vv

Először is ellenőrizze, hogy a következő parancsok segítségével áttelepítheti-e a Cloud Service-t:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Készítse elő a virtuális gépeket a Cloud Service-ben áttelepítésre. Két lehetőség közül választhat.

Ha a virtuális gépeket egy platform által létrehozott virtuális hálózatra szeretné áttelepíteni, használja a következő parancsot.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Ha a Resource Manager-alapú üzemi modellben meglévő virtuális hálózatra kíván áttelepítést végezni, használja a következő parancsot.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Az előkészítési művelet sikeres végrehajtása után megtekintheti a részletes kimenetet, hogy beolvassa a virtuális gépek áttelepítési állapotát, és győződjön meg `Prepared` arról, hogy azok állapotban vannak.

    azure vm show <vmName> -vv

A CLI vagy a Azure Portal használatával vizsgálja meg az előkészített erőforrások konfigurációját. Ha nem áll készen az áttelepítésre, és vissza szeretné állítani a régi állapotot, használja a következő parancsot.

    azure service deployment abort-migration <serviceName> <deploymentName>

Ha az előkészített konfiguráció jól néz ki, az alábbi parancs használatával továbbíthatja és véglegesítheti az erőforrásokat.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>4. lépés: 2. lehetőség – virtuális gépek migrálása virtuális hálózatban
Válassza ki az áttelepíteni kívánt virtuális hálózatot. Vegye figyelembe, hogy ha a virtuális hálózat olyan webes/feldolgozói szerepköröket vagy virtuális gépeket tartalmaz, amelyek nem támogatott konfigurációval rendelkeznek, egy érvényesítési hibaüzenet jelenik meg.

Az alábbi parancs használatával szerezze be az előfizetésben található összes virtuális hálózatot.

    azure network vnet list

A kimenet a következőhöz hasonlóan fog kinézni:

![Képernyőkép a parancssorról a Kiemelt teljes virtuális hálózat nevével.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

A fenti példában a **virtualNetworkName** a **"csoport classicubuntu16 classicubuntu16"** teljes neve.

Először is ellenőrizze, hogy a következő paranccsal telepítheti-e át a virtuális hálózatot:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Készítse elő az áttelepítéshez választott virtuális hálózatot a következő parancs használatával.

    azure network vnet prepare-migration <virtualNetworkName>

A CLI vagy a Azure Portal használatával vizsgálja meg az előkészített virtuális gépek konfigurációját. Ha nem áll készen az áttelepítésre, és vissza szeretné állítani a régi állapotot, használja a következő parancsot.

    azure network vnet abort-migration <virtualNetworkName>

Ha az előkészített konfiguráció jól néz ki, az alábbi parancs használatával továbbíthatja és véglegesítheti az erőforrásokat.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>5. lépés: Storage-fiók átmigrálása
Ha elkészült a virtuális gépek áttelepítésével, javasoljuk, hogy telepítse át a Storage-fiókot.

Készítse elő a Storage-fiókot az áttelepítéshez a következő parancs használatával

    azure storage account prepare-migration <storageAccountName>

A CLI vagy a Azure Portal használatával vizsgálja meg az előkészített Storage-fiók konfigurációját. Ha nem áll készen az áttelepítésre, és vissza szeretné állítani a régi állapotot, használja a következő parancsot.

    azure storage account abort-migration <storageAccountName>

Ha az előkészített konfiguráció jól néz ki, az alábbi parancs használatával továbbíthatja és véglegesítheti az erőforrásokat.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>További lépések

* [A IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [A IaaS-erőforrások migrálása a Klasszikusból a Azure Resource Managerba a PowerShell használatával](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Közösségi eszközök a IaaS-erőforrások Klasszikusból Azure Resource Managerba való áttelepítésének támogatásához](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tekintse át a IaaS-erőforrások klasszikusról Azure Resource Managerra való áttelepítésével kapcsolatos leggyakoribb kérdéseket](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
