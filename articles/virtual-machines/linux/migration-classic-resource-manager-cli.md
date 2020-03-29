---
title: Virtuális gépek áttelepítése az Erőforrás-kezelőbe az Azure CLI használatával
description: Ez a cikk bemutatja az erőforrások platformáltal támogatott áttelepítését a klasszikusról az Azure Resource Managerbe az Azure CLI használatával
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: c41292a05e5c857cd0b1c120784a400f2f5410ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945358"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>IaaS-erőforrások migrálása a klasszikusból Resource Manager-alapú környezetbe az Azure CLI használatával

> [!IMPORTANT]
> Ma az IaaS virtuális gépek mintegy 90%-a használja az [Azure Resource Managert.](https://azure.microsoft.com/features/resource-manager/) 2020. február 28-tól a klasszikus virtuális gépek elavultak, és 2023. március 1-jén teljesen nyugdíjba kerülnek. [Tudjon meg többet]( https://aka.ms/classicvmretirement) erről az eprecációról [és arról, hogy ez milyen hatással van Önre.](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)

Ezek a lépések bemutatják, hogyan használhatja az Azure parancssori felület (CLI) parancsait az infrastruktúra szolgáltatásként (IaaS) való áttelepítéséhez a klasszikus üzembe helyezési modellről az Azure Resource Manager telepítési modellre. A cikk megköveteli az [Azure klasszikus CLI.](../../cli-install-nodejs.md) Mivel az Azure CLI csak az Azure Resource Manager-erőforrások, nem használható az áttelepítéshez.

> [!NOTE]
> Az itt leírt műveletek mindegyike idempotens. Ha nem támogatott szolgáltatásvagy konfigurációs hiba miatt nem probléma merült fel, javasoljuk, hogy próbálkozzon újra az előkészítési, megszakítási vagy véglegesítési művelettel. A platform ezután újra próbálkozik a művelettel.
> 
> 

<br>
Itt van egy folyamatábra, amely azonosítja azt a sorrendet, amelyben az áttelepítési folyamat során lépéseket kell végrehajtani

![Képernyőkép a migrálási lépésekről](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>1. lépés: Felkészülés az áttelepítésre
Íme néhány ajánlott eljárás, amelyet az IaaS-erőforrások klasszikusról Erőforrás-kezelőre való áttelepítésének kiértékelésére ajánlott:

* Olvassa el a [nem támogatott konfigurációk és szolgáltatások listáját.](../windows/migration-classic-resource-manager-overview.md) Ha nem támogatott konfigurációkat vagy szolgáltatásokat használó virtuális gépekkel rendelkezik, javasoljuk, hogy várja meg a szolgáltatás/konfiguráció támogatás ának bejelentését. Azt is megteheti, hogy eltávolítja ezt a funkciót, vagy kiköltözik a konfigurációból az áttelepítés engedélyezéséhez, ha az igényeinek megfelel.
* Ha az infrastruktúra és az alkalmazások üzembe helyezése automatikus parancsfájlok ma, próbálja meg létrehozni egy hasonló teszt beállítás segítségével ezeket a parancsfájlokat az áttelepítéshez. Másik lehetőségként beállíthat mintakörnyezeteket az Azure Portal használatával.

> [!IMPORTANT]
> Az alkalmazásátjárók jelenleg nem támogatottak a klasszikusról az Erőforrás-kezelőre való áttéréshez. Klasszikus virtuális hálózat alkalmazásátjáróval történő áttelepítéséhez távolítsa el az átjárót, mielőtt előkészíti a műveletet a hálózat áthelyezéséhez. Miután befejezte az áttelepítést, csatlakoztassa újra az átjárót az Azure Resource Managerben. 
>
>Egy másik előfizetésexpressRoute-áramköreihez csatlakozó ExpressRoute-átjárók nem telepíthetők át automatikusan. Ilyen esetekben távolítsa el az ExpressRoute-átjárót, telepítse át a virtuális hálózatot, és hozza létre újra az átjárót. További információért olvassa el [az ExpressRoute-áramkörök és a kapcsolódó virtuális hálózatok áttelepítése a klasszikusról az Erőforrás-kezelő telepítési modellre](../../expressroute/expressroute-migration-classic-resource-manager.md) című témakört.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>2. lépés: Állítsa be az előfizetést, és regisztrálja a szolgáltatót
Áttelepítési forgatókönyvek esetén be kell állítania a környezetet a klasszikus és az erőforrás-kezelőhöz is. [Telepítse az Azure CLI-t,](../../cli-install-nodejs.md) és [válassza ki az előfizetést.](/cli/azure/authenticate-azure-cli)

Jelentkezzen be a fiókjába.

    azure login

Válassza ki az Azure-előfizetést a következő paranccsal.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> A regisztráció egyszeri lépés, de az áttelepítés megkísérlése előtt egyszer kell elvégezni. Regisztráció nélkül a következő hibaüzenet jelenik meg 
> 
> *BadRequest : Az előfizetés nincs regisztrálva az áttelepítéshez.* 
> 
> 

Regisztráljon az áttelepítési erőforrás-szolgáltatónál a következő paranccsal. Ne feledje, hogy bizonyos esetekben ez a parancs időát. A regisztráció azonban sikeres lesz.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Várjon öt percet, amíg a regisztráció befejeződik. A jóváhagyás állapotát a következő paranccsal ellenőrizheti. Győződjön meg arról, hogy RegistrationState van, `Registered` mielőtt folytatná.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Most váltson `asm` a CLI-t a módba.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>3. lépés: Győződjön meg arról, hogy elegendő Azure Resource Manager virtuálisgép-vCPU-val rendelkezik az aktuális központi telepítés vagy a virtuális hálózat Azure-régiójában
Ehhez a lépéshez át kell `arm` váltania módra. Ehhez a következő paranccsal.

```
azure config mode arm
```

A következő CLI-paranccsal ellenőrizheti az Azure Resource Manager ben használt vCPU-k aktuális számát. A vCPU-kvótákról a [Korlátok és az Azure Resource Manager](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)című témakörben olvashat bővebben.

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Miután végzett a lépés ellenőrzésével, visszaválthat `asm` módba.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>4. lépés: 1. lehetőség – Virtuális gépek áttelepítése felhőalapú szolgáltatásban
A felhőszolgáltatások listájának beszerezése a következő paranccsal, majd válassza ki az áttelepíteni kívánt felhőszolgáltatást. Vegye figyelembe, hogy ha a virtuális gépek a felhőszolgáltatásban egy virtuális hálózatban, vagy ha rendelkeznek webes/feldolgozói szerepkörökkel, hibaüzenetet kap.

    azure service list

Futtassa a következő parancsot a felhőszolgáltatás központi telepítési nevének lekérnia a részletes kimenetről. A legtöbb esetben a központi telepítési név megegyezik a felhőszolgáltatás nevével.

    azure service show <serviceName> -vv

Először ellenőrizze, hogy áttelepítheti-e a felhőszolgáltatást a következő parancsokkal:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Készítse elő a virtuális gépeket a felhőszolgáltatásban az áttelepítéshez. Két lehetőség közül választhat.

Ha a virtuális gépeket egy platform által létrehozott virtuális hálózatra szeretné áttelepíteni, használja a következő parancsot.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Ha az Erőforrás-kezelő telepítési modelljében egy meglévő virtuális hálózatra szeretne áttérni, használja a következő parancsot.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Miután az előkészítési művelet sikeres, átnézheti a részletes kimenetet a virtuális gépek áttelepítési állapotának leigazolásához, és biztosíthatja, hogy állapotban `Prepared` vannak.

    azure vm show <vmName> -vv

Ellenőrizze az előkészített erőforrások konfigurációját a CLI vagy az Azure Portal használatával. Ha nem áll készen az áttelepítésre, és vissza szeretne lépni a régi állapotba, használja a következő parancsot.

    azure service deployment abort-migration <serviceName> <deploymentName>

Ha az előkészített konfiguráció jól néz ki, a következő paranccsal előreléphet és véglegesítheti az erőforrásokat.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>4. lépés: 2. lehetőség – Virtuális gépek áttelepítése virtuális hálózatban
Válassza ki az áttelepíteni kívánt virtuális hálózatot. Vegye figyelembe, hogy ha a virtuális hálózat webes/feldolgozói szerepköröket vagy nem támogatott konfigurációval rendelkező virtuális gépeket tartalmaz, érvényesítési hibaüzenet et kap.

Az alábbi parancs segítségével az előfizetés összes virtuális hálózatát beszerezheti.

    azure network vnet list

A kimenet a következőhöz hasonlóan fog kinézni:

![Képernyőkép a teljes virtuális hálózat nevének kiemelésével ellátott parancssorról.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

A fenti példában a **virtualNetworkName** a **"Group classicubuntu16 classicubuntu16"** teljes neve.

Először ellenőrizze, hogy a virtuális hálózat áttelepíthető-e a következő paranccsal:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Készítse elő a választott virtuális hálózatot az áttelepítésre a következő paranccsal.

    azure network vnet prepare-migration <virtualNetworkName>

Ellenőrizze az előkészített virtuális gépek konfigurációját a CLI vagy az Azure Portal használatával. Ha nem áll készen az áttelepítésre, és vissza szeretne lépni a régi állapotba, használja a következő parancsot.

    azure network vnet abort-migration <virtualNetworkName>

Ha az előkészített konfiguráció jól néz ki, a következő paranccsal előreléphet és véglegesítheti az erőforrásokat.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>5. lépés: Tárfiók áttelepítése
Miután befejezte a virtuális gépek áttelepítését, javasoljuk, hogy telepítse át a tárfiókot.

A tárfiók előkészítése az áttelepítésre a következő paranccsal

    azure storage account prepare-migration <storageAccountName>

Ellenőrizze az előkészített tárfiók konfigurációját a CLI vagy az Azure Portal használatával. Ha nem áll készen az áttelepítésre, és vissza szeretne lépni a régi állapotba, használja a következő parancsot.

    azure storage account abort-migration <storageAccountName>

Ha az előkészített konfiguráció jól néz ki, a következő paranccsal előreléphet és véglegesítheti az erőforrásokat.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>További lépések

* [Az IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Managerre](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Az IaaS-erőforrások klasszikusról az Azure Resource Managerbe való áttelepítése a PowerShell használatával](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Közösségi eszközök az IaaS-erőforrások klasszikusról Azure Resource Managerre való áttelepítéséhez](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tekintse át az IaaS-erőforrások klasszikusról Azure Resource Managerbe való áttelepítésével kapcsolatos leggyakoribb kérdéseket](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
