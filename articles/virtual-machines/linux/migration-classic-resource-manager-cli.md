---
title: Virtuális gépek áttelepítése a Resource Manager Azure parancssori felületével |} A Microsoft Docs
description: Ez a cikk végigvezeti az erőforrások a platform által támogatott áttelepítés klasszikusból Azure Resource Manager Azure CLI-vel
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d6f5a877-05b6-4127-a545-3f5bede4e479
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 0011ee646215c01e84aec71c7b992afca1ca3c2a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997165"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Áttelepítése IaaS-erőforrások klasszikusból Azure Resource Manager-Azure CLI-vel
Ezek a lépések bemutatják, hogyan áttelepítése infrastruktúra-szolgáltatás (IaaS) erőforrások a klasszikus üzemi modellben az Azure Resource Manager-alapú üzemi modellbe, az Azure parancssori felület (CLI) parancsok használatával. A cikkben foglaltak végrehajtásához a [Azure klasszikus parancssori felület](../../cli-install-nodejs.md). Mivel az Azure CLI csak akkor érvényes, az Azure Resource Manager-erőforrásokat, akkor az áttelepítés nem használható.

> [!NOTE]
> Az itt ismertetett összes művelet idempotens. Ha rendelkezik olyan probléma lép fel egy nem támogatott funkció vagy konfigurációs hibának, azt javasoljuk, hogy az előkészítés, újra megpróbálja megszakítható, vagy a véglegesítési műveletet. A platform ezután megpróbálja megismételni a műveletet.
> 
> 

<br>
Azonosíthatja a sorrendet, amelyben lépéseket kell végrehajtani egy áttelepítési folyamat során folyamatábra

![Képernyőkép a migrálási lépésekről](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>1. lépés: Az áttelepítés előkészítése
Az alábbiakban néhány ajánlott eljárást, amely azt javasoljuk, áttelepítése IaaS-erőforrásokat a klasszikusból a Resource Managernek kipróbálása:

* Olvassa el a [nem támogatott konfigurációk vagy szolgáltatások listája](../windows/migration-classic-resource-manager-overview.md). Nem támogatott konfigurációkat és funkciókat használó virtuális gépekkel rendelkezik, azt javasoljuk, hogy várja meg a szolgáltatás vagy a konfigurációs támogatás később jelentjük be. Azt is megteheti távolítsa el ezt a funkciót, vagy helyezze át ki, hogy konfigurációja engedélyezze az áttelepítést, ha azt az igényeinek megfelelő.
* Ha automatizálta parancsprogramokat, amelyek az infrastruktúra és alkalmazások telepítése még ma, próbálja létrehozni egy hasonló teszt beállítást az áttelepítés ezen parancsfájlok használatával. Azt is megteheti hogy mintakörnyezetek úgy állíthatja az Azure portal használatával.

> [!IMPORTANT]
> Az Application Gateway átjárók jelenleg nem támogatottak az áttelepítés klasszikusról Resource Manager. Az Application gateway egy klasszikus virtuális hálózat migrálása, távolítsa el az átjáró áthelyezése a hálózat-előkészítési művelet futtatása előtt. Miután befejezte az áttelepítést, csatlakoztassa újra az átjárót, az Azure Resource Manager. 
>
>ExpressRoute-átjáró csatlakoztatása ExpressRoute-Kapcsolatcsoportok egy másik előfizetésben nem telepíthetők át automatikusan. Ezekben az esetekben távolítsa el az ExpressRoute-átjárót, a virtuális hálózat migrálása, és hozza létre újra az átjárót. Lásd: [áttelepítése ExpressRoute-Kapcsolatcsoportok és kapcsolódó virtuális hálózatok a klasszikusból a Resource Manager-alapú üzemi modellbe](../../expressroute/expressroute-migration-classic-resource-manager.md) további információt.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>2. lépés: Az előfizetés beállításához, és regisztrálja a szolgáltatót
Áttelepítési forgatókönyvek esetén szükséges, állítsa be a környezetet, mind a klasszikus és Resource Manager. [Azure CLI telepítése](../../cli-install-nodejs.md) és [válassza ki az előfizetését](/cli/azure/authenticate-azure-cli).

Jelentkezzen be a fiókjába.

    azure login

Válassza ki az Azure-előfizetés a következő parancs segítségével.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> A regisztráció csak az idő lépést, de kell tenni, egyszer a migrálás megkezdése előtt. Regisztrálás nélkül látni fogja a következő hibaüzenet 
> 
> *BadRequest: Előfizetés nincs regisztrálva migrálásra.* 
> 
> 

Regisztrálja az áttelepítés erőforrás-szolgáltató a következő parancs segítségével. Vegye figyelembe, hogy bizonyos esetekben ez a parancs túllépi az időkorlátot. A regisztráció azonban sikeres lesz.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Kérjük, várjon öt perc alatt, a regisztráció befejezéséhez. A jóváhagyás állapotát a következő paranccsal ellenőrizheti. Győződjön meg arról, hogy van-e RegistrationState `Registered` folytatás előtt.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Most váltson a parancssori felület a `asm` mód.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>3. lépés: Ellenőrizze, hogy elegendő az Azure Resource Manager virtuális gépének vcpu-k a jelenlegi üzemelő példány vagy virtuális hálózat Azure-régióban
Váltson át kell ehhez a lépéshez `arm` mód. Ehhez a következő paranccsal.

```
azure config mode arm
```

A következő CLI-parancs segítségével ellenőrizze az Azure Resource Manager rendelkezik vcpu-k aktuális száma. VCPU-kvóták kapcsolatos további információkért lásd: [korlátok és az Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Miután elkészült ellenőrzése ebben a lépésben, válthat vissza a `asm` mód.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>4. lépés: 1. lehetőség – felhőszolgáltatás virtuális gépek Migrálása
A cloud services listájának lekérése a következő paranccsal, és ezután válassza ki a felhőalapú szolgáltatás, amely a migrálni kívánt. Vegye figyelembe, hogy a beállítást, ha a cloud service-ben a virtuális gépek egy virtuális hálózaton, vagy ha webes vagy feldolgozói szerepkörök rendelkeznek, hibaüzenetet kap.

    azure service list

A következő parancsot a felhőszolgáltatás üzemelőpéldány-név lekérése a részletes kimenet. A legtöbb esetben az üzemelő példány neve megegyezik a felhőszolgáltatás neve.

    azure service show <serviceName> -vv

Először is ellenőrizheti, hogy telepíthet át a felhőalapú szolgáltatás, a következő parancsokkal:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

A virtuális gépek áttelepítése a felhőbe történő előkészítéséhez. Rendelkezik két lehetőség közül választhat.

Ha szeretné a virtuális gépek áttelepítése a platform által létrehozott virtuális hálózathoz, a következő paranccsal.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Ha szeretne áttelepíteni egy meglévő virtuális hálózatot a Resource Manager-alapú üzemi modellben, használja a következő parancsot.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Miután az előkészítési művelet sikeres, áttekintheti a részletes információk a virtuális gépek a migrálás állapotát, és győződjön meg arról, hogy vannak-e a a `Prepared` állapota.

    azure vm show <vmName> -vv

Ellenőrizze a konfigurációt, az előkészített erőforrások parancssori felület vagy az Azure portal használatával. Ha nem áll készen a migrálásra, és térjen vissza a régi állapot szeretne, használja a következő parancsot.

    azure service deployment abort-migration <serviceName> <deploymentName>

Az előkészített konfiguráció megfelelőnek tűnik, ha előre, és véglegesítse az erőforrásokat a következő parancs segítségével.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>4. lépés: 2. lehetőség – a virtuális hálózatban a virtuális gépek Migrálása
Válassza ki az áttelepíteni kívánt virtuális hálózat. Vegye figyelembe, hogy ha a virtuális hálózat nem támogatott konfigurációval webes/feldolgozói szerepkörök vagy a virtuális gépeket tartalmaz, kap egy érvényesítési hibaüzenet.

A virtuális hálózatok a következő paranccsal lépjen be az előfizetés.

    azure network vnet list

A kimenet következőhöz hasonlóan fog kinézni:

![Képernyőkép – a teljes virtuális hálózat neve, kiemelve a parancssorban.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

A fenti példában a **virtualNetworkName** teljes neve **"Csoport classicubuntu16 classicubuntu16"**.

Először is ellenőrizheti, hogy áttelepítheti a virtuális hálózat, a következő paranccsal:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

A kiválasztott virtuális hálózat előkészítése az áttelepítéshez a következő parancs segítségével.

    azure network vnet prepare-migration <virtualNetworkName>

Ellenőrizze az előkészített virtuális gépek konfigurációját a parancssori felület vagy az Azure portal használatával. Ha nem áll készen a migrálásra, és térjen vissza a régi állapot szeretne, használja a következő parancsot.

    azure network vnet abort-migration <virtualNetworkName>

Az előkészített konfiguráció megfelelőnek tűnik, ha előre, és véglegesítse az erőforrásokat a következő parancs segítségével.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>5. lépés: A tárfiók Migrálása
Miután elkészült a virtuális gépek migrálása, javasoljuk, hogy telepít át, a storage-fiókot.

A következő parancs segítségével a tárfiók migrálásának előkészítése

    azure storage account prepare-migration <storageAccountName>

Ellenőrizze a konfigurációt, az előkészített tárfiók parancssori felület vagy az Azure portal használatával. Ha nem áll készen a migrálásra, és térjen vissza a régi állapot szeretne, használja a következő parancsot.

    azure storage account abort-migration <storageAccountName>

Az előkészített konfiguráció megfelelőnek tűnik, ha előre, és véglegesítse az erőforrásokat a következő parancs segítségével.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>További lépések

* [A platform által támogatott áttelepítés IaaS-erőforrások klasszikusból Azure Resource Manager áttekintése](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [IaaS-erőforrások áttelepítése klasszikusból Azure Resource Manager PowerShell-lel](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager áttelepítését segítő közösségi eszközök](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [A legtöbb – gyakori kérdések áttelepítése IaaS-erőforrások klasszikusból Azure Resource Manager áttekintése](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
