---
title: Telepítse át a virtuális gépek a Resource Manager Azure parancssori felületével |} Microsoft Docs
description: Ez a cikk végigvezeti a platform által támogatott áttelepítési erőforrások a klasszikus Azure Resource Manager Azure parancssori felület használatával
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
ms.openlocfilehash: f986246e74305789eb2978a95fd6a3e51accd25e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30909751"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Telepítse át IaaS-erőforrásokra a klasszikus Azure Resource Manager Azure parancssori felület használatával
Ezeket a lépéseket mutatja be az Azure parancssori felület (CLI) parancsok használatával telepítse át az infrastruktúra erőforrásként egy szolgáltatási (IaaS) a klasszikus telepítési modellből az Azure Resource Manager telepítési modellhez. A cikk igényel a [Azure CLI 1.0](../../cli-install-nodejs.md). Mivel Azure CLI 2.0 csak az Azure Resource Manager erőforrások alkalmazható, akkor az áttelepítés nem használható.

> [!NOTE]
> Itt leírt műveletek idempotent. Ha a probléma nem támogatott szolgáltatása vagy konfigurációs hiba, azt javasoljuk, hogy a prepare újra, vagy megszakításra műveletet. A platform fog majd próbálja megismételni a műveletet.
> 
> 

<br>
Ez a sorrendet, amelyben lépéseket kell végrehajtani az áttelepítési folyamat során azonosításához folyamatábra

![Képernyőkép a migrálási lépésekről](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>1. lépés: Felkészülés az áttelepítésre
Az alábbiakban néhány gyakorlati tanácsok, azt javasoljuk, áttelepítése IaaS-erőforrásokra a hagyományos erőforrás-kezelő értékeli:

* Olvassa végig a [listája nem támogatott konfigurációk vagy szolgáltatások](../windows/migration-classic-resource-manager-overview.md). Ha még nem támogatott konfigurációk vagy szolgáltatások használó virtuális gépek, azt javasoljuk, hogy a szolgáltatás vagy a konfigurációs támogatási bejelentések várja. Alternatív megoldásként távolítsa el a szolgáltatást, vagy helyezze át kívül, hogy a konfigurálás engedélyezze az áttelepítést, ha az igényeinek megfelelő.
* Ha olyan parancsfájlok, amelyek központi telepítése az infrastruktúra és az alkalmazások ma rendelkezik automatikus, hozzon létre egy hasonló vizsgálat beállítása az áttelepítés ezen parancsfájlok használatával. Másik lehetőségként állíthat be minta környezetekben az Azure portál használatával.

> [!IMPORTANT]
> Alkalmazásátjárót jelenleg nem támogatottak az áttelepítéshez a klasszikus az erőforrás-kezelő. A klasszikus virtuális hálózatot az Alkalmazásátjáró át, a hálózati áthelyezése egy előkészítési művelet futtatása előtt távolítsa el az átjáró. Az áttelepítés befejezése után csatlakoztassa újra az átjárót az Azure Resource Manager. 
>
>Kapcsolódás egy másik előfizetésben található ExpressRoute-Kapcsolatcsoportok ExpressRoute-átjárók nem telepíthetők át automatikusan. Ebben az esetben távolítsa el az ExpressRoute-átjárót, telepítse át a virtuális hálózaton, és hozza létre újra az átjárót. Ellenőrizze a [áttelepítése ExpressRoute áramkörök, és a Resource Manager üzembe helyezési modellel klasszikus virtuális hálózatok társított](../../expressroute/expressroute-migration-classic-resource-manager.md) további információt.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>2. lépés: Állítsa be az előfizetéshez, és regisztrálja a szolgáltatót
Áttelepítési forgatókönyvek esetén, be kell állítania a környezetet az mindkét klasszikus és Resource Manager. [Az Azure parancssori felület telepítése](../../cli-install-nodejs.md) és [jelölje ki az előfizetését](/cli/azure/authenticate-azure-cli).

Jelentkezzen be fiókjába.

    azure login

Válassza ki az Azure-előfizetés a következő parancs használatával.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> A regisztráció egy az idő a lépést, de az azért van szükség, egyszer a migrálás megkezdése előtt. Ha nem regisztrálja látni fogja, a következő hibaüzenet 
> 
> *BadRequest: Az előfizetés nincs regisztrálva az áttelepítéshez.* 
> 
> 

Az áttelepítés erőforrás-szolgáltató regisztrálása a következő parancs használatával. Vegye figyelembe, hogy néhány esetben ez a parancs végrehajtásának időkorlátja. Azonban a regisztráció sikeres lesz.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Kis türelmet, a regisztráció befejezéséhez öt perc. A jóváhagyási állapotát a következő paranccsal ellenőrizheti. Győződjön meg arról, hogy van-e RegistrationState `Registered` folytatás előtt.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Most a parancssori felület a kapcsoló a `asm` mód.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>3. lépés: Ellenőrizze, hogy elegendő Azure Resource Manager virtuális gép Vcpu Azure-régióban a jelenlegi üzemelő példány vagy virtuális hálózaton
Ebben a lépésben kell váltani `arm` mód. Ehhez a következő paranccsal.

```
azure config mode arm
```

A következő parancssori parancsot segítségével ellenőrizze, rendelkezik az Azure Resource Manager Vcpu aktuális száma. VCPU kvóták kapcsolatos további információkért lásd: [korlátozásai és az Azure erőforrás-kezelő](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Miután befejezte ellenőrzése ezt a lépést, is visszavált `asm` mód.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>4. lépés: 1. lehetőség – a felhőszolgáltatásban található virtuális gépek áttelepítése
A felhőszolgáltatások listájának lekérdezése a következő paranccsal, és ezután válassza ki a felhőalapú szolgáltatás, amely az áttelepíteni kívánt. Vegye figyelembe, hogy a beállítást, ha a felhőszolgáltatás a virtuális gépek virtuális hálózatban, vagy ha webes vagy feldolgozói szerepköröket, hibaüzenetet kap-e.

    azure service list

A következő parancsot a központi telepítés nevét, a felhőszolgáltatás lekérése a részletes kimenet. A legtöbb esetben a telepítés neve megegyezik a felhőszolgáltatás neve.

    azure service show <serviceName> -vv

Először ellenőrzi, hogy áttelepítheti a felhőalapú szolgáltatás, a következő parancsokkal:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Készítse elő a virtuális gépek áttelepítése a felhőalapú szolgáltatás. Rendelkezik két lehetőség közül választhat.

Ha szeretne áttelepíteni a virtuális gépek platform által létrehozott virtuális hálózathoz, a következő paranccsal.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Ha szeretné áttelepíteni meglévő virtuális hálózat a Resource Manager üzembe helyezési modellel, az alábbi paranccsal.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Az előkészítési művelet befejezését követően a virtuális gépek áttelepítésének állapotát, és győződjön meg arról, hogy vannak-e a részletes kimenet keresztül megtekintheti a `Prepared` állapotát.

    azure vm show <vmName> -vv

Ellenőrizze a konfigurációt, az előkészített erőforrások CLI vagy az Azure-portál használatával. Ha nem az áttelepítéshez, és térjen vissza a régi állapot szeretne, használja a következő parancsot.

    azure service deployment abort-migration <serviceName> <deploymentName>

Az előkészített konfiguráció megfelelőnek tűnik, ha előre, és véglegesíti az erőforrásokat a következő parancs használatával.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>4. lépés: 2. lehetőség – a virtuális hálózatban lévő virtuális gépek áttelepítése
Válassza ki az áttelepíteni kívánt virtuális hálózat. Vegye figyelembe, hogy ha a virtuális hálózat nem támogatott konfigurációjú webes vagy feldolgozói szerepkörök vagy a virtuális gépeket tartalmaz, üzenetet fog kapni egy érvényesítési hiba.

Az alábbi parancs segítségével könnyebben nyerhet a virtuális hálózatok az előfizetést.

    azure network vnet list

A kimenet ehhez hasonló fog kinézni:

![Képernyőkép a kiemelt teljes virtuális hálózat nevét a parancssorban.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

A fenti példában a **virtualNetworkName** teljes neve **"Csoport classicubuntu16 classicubuntu16"**.

Először ellenőrzi, hogy áttelepítheti a virtuális hálózat, a következő parancsot:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Az Ön által választott virtuális hálózatot Felkészülés az áttelepítésre az alábbi paranccsal.

    azure network vnet prepare-migration <virtualNetworkName>

Ellenőrizze az előkészített virtuális gépek konfigurációját a CLI vagy az Azure-portál használatával. Ha nem az áttelepítéshez, és térjen vissza a régi állapot szeretne, használja a következő parancsot.

    azure network vnet abort-migration <virtualNetworkName>

Az előkészített konfiguráció megfelelőnek tűnik, ha előre, és véglegesíti az erőforrásokat a következő parancs használatával.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>5. lépés: A storage-fiókok áttelepítése
Miután befejezte a virtuális gépek áttelepítéséhez, azt javasoljuk, telepíti át a tárfiókot.

A tárfiók előkészítése az áttelepítésre az alábbi parancs használatával

    azure storage account prepare-migration <storageAccountName>

Ellenőrizze a konfigurációt, az előkészített tárfiók CLI vagy az Azure-portál használatával. Ha nem az áttelepítéshez, és térjen vissza a régi állapot szeretne, használja a következő parancsot.

    azure storage account abort-migration <storageAccountName>

Az előkészített konfiguráció megfelelőnek tűnik, ha előre, és véglegesíti az erőforrásokat a következő parancs használatával.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>További lépések

* [IaaS-erőforrásokra a klasszikus Azure Resource Manager platform által támogatott áttelepítésének áttekintése](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Műszaki részletes bemutatója a platform által támogatott áttelepítési a klasszikus Azure Resource Managerbe](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [IaaS-erőforrások áttelepítése a klasszikus Azure Resource Manager PowerShell használatával](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS-erőforrásokra a klasszikus Azure Resource Manager áttelepítésének védelmével kapcsolatos közösségi eszközök](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [A leggyakrabban feltett kérdésekre áttelepítése IaaS-erőforrásokra a klasszikus Azure Resource Manager áttekintése](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
