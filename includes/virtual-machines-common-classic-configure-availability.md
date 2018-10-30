---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 32f533d06b7db0284459951e65f9c04fe0bb0285
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226746"
---
Rendelkezésre állási készlet segít például folyamatosan elérhető leállás, a virtuális gépek karbantartás közben. A redundanciát rendelkezésre állását, alkalmazások vagy szolgáltatások a virtuális gépet futtató két vagy több hasonlóan konfigurált virtuális gépek elhelyezése egy rendelkezésre állási csoportot hoz létre. Ez működésével kapcsolatos részletekért lásd: [virtuális gépek rendelkezésre állásának kezelése][Manage the availability of virtual machines].

Ajánlott eljárás segítségével a rendelkezésre állási csoportok és a terheléselosztási végpontok érdekében, hogy az alkalmazás mindig elérhető és futó hatékonyan. Elosztott terhelésű végpontok kapcsolatos részletekért lásd: [terheléselosztás az Azure infrastruktúra-szolgáltatásoknak][Load balancing for Azure infrastructure services].

Klasszikus virtuális gépeket adhat hozzá egy rendelkezésre állási csoport két lehetőség egyikét használva be:

* [1. lehetőség: Hozzon létre egy virtuális gép és a egy rendelkezésre állási csoport egy időben][Option 1: Create a virtual machine and an availability set at the same time]. Ezután új virtuális gépek felvétele a készlet azon virtuális gépek létrehozásakor.
* [2. lehetőség: Hozzáadása egy meglévő virtuális gépet egy rendelkezésre állási csoporthoz][Option 2: Add an existing virtual machine to an availability set].

> [!NOTE]
> A klasszikus modellben, hogy el szeretné helyezni az azonos rendelkezésre állási csoportban lévő virtuális gépek ugyanazon a felhőszolgáltatáson kell tartoznia.
> 
> 

## <a id="createset"> </a>1. lehetőség: Hozzon létre egy virtuális gép és a egy rendelkezésre állási csoport egy időben
Ehhez használhatja az Azure portal vagy a Azure PowerShell-parancsokat.

Az Azure portal használata:

1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **erőforrás létrehozása** > **számítási**.
3. Válassza ki a használni kívánt Marketplace virtuálisgép-lemezkép. Ha szeretné, hozzon létre egy Linux vagy Windows virtuális gépet.
4. A kiválasztott virtuális gép, győződjön meg arról, hogy az üzembe helyezési modell értéke **klasszikus** majd **létrehozása**
   
    ![Helyettesítő képszöveg](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)
5. Adja meg a virtuális gép neve, felhasználónév és jelszó (a Windows-gépeken) vagy SSH nyilvános kulcs (a Linux rendszerű gépek). 
6. Válassza ki a virtuális gép méretét, és kattintson a **kiválasztása** folytatásához.
7. Válasszon **választható konfiguráció > rendelkezésre állási csoport**, és válassza ki a rendelkezésre állási csoport, a virtuális gép hozzá kívánja.
   
    ![Helyettesítő képszöveg](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png) 
8. Tekintse át a konfigurációs beállításokat. Ha elkészült, kattintson a **létrehozás**.
9. Amíg az Azure létrehozza a virtuális gép, nyomon követheti a folyamat állapotát **virtuális gépek** a központ menüben.

Azure PowerShell-parancsok használatával hozzon létre egy Azure virtuális gépen, és adja hozzá egy új vagy meglévő rendelkezésre állási csoportot, lásd: [hozhat létre, és előre konfigurálása a Windows-alapú virtuális gépek Azure PowerShell használata](../articles/virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a id="addmachine"> </a>2. lehetőség: Hozzáadása egy meglévő virtuális gépet egy rendelkezésre állási csoporthoz
Az Azure Portalon is hozzáadhat meglévő klasszikus virtuális gépek olyan meglévő rendelkezésre állási beállítása, vagy hozzon létre egy újat a számukra. (Vegye figyelembe, hogy az ugyanazon rendelkezésre állási csoportban lévő virtuális gépek ugyanazon a felhőszolgáltatáson kell tartoznia.) A lépések ugyanazok, szinte. Az Azure PowerShell használatával a virtuális gép egy meglévő rendelkezésre állási csoporthoz is hozzáadhat.

1. Ha még nem tette meg, jelentkezzen be a [az Azure portal](https://portal.azure.com).
2. A bal oldali menüben kattintson a **virtuális gépek (klasszikus)**.
   
    ![Helyettesítő képszöveg](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)
3. Virtuális gépek listájából válassza ki a virtuális gép hozzá a kívánt nevét.
4. Válasszon **rendelkezésre állási csoport** a virtuális gépről **beállítások**.
   
    ![Helyettesítő képszöveg](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)
5. Válassza ki a rendelkezésre állási csoport a virtuális gép hozzá kívánja. A virtuális gép ugyanazon a felhőszolgáltatáson, a rendelkezésre állási csoporthoz kell tartoznia.
   
    ![Helyettesítő képszöveg](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)
6. Kattintson a **Save** (Mentés) gombra.

Azure PowerShell-parancsok használatához nyissa meg a rendszergazdai Azure PowerShell-munkamenetet, és futtassa a következő parancsot. A helyőrzők értékét (például &lt;VmCloudServiceName&gt;), cserélje le a mindent, ami az ajánlatokat, többek között a < és > karakter, a megfelelő nevekkel.

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

> [!NOTE]
> A virtuális gépet lehet hozzáadni a rendelkezésre állási csoport befejezéséhez újra kell indítani.
> 
> 

<!-- LINKS -->
[Option 1: Create a virtual machine and an availability set at the same time]: #createset
[Option 2: Add an existing virtual machine to an availability set]: #addmachine

[Load balancing for Azure infrastructure services]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Manage the availability of virtual machines]:../articles/virtual-machines/linux/manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md

