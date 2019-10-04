---
title: Az Azure Windows rendszerű virtuális gép hálózati adapterének alaphelyzetbe állítása | Microsoft Docs
description: Bemutatja, hogyan lehet alaphelyzetbe állítani az Azure Windows rendszerű virtuális gépek hálózati adapterét
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: afb8335d3206a76b8f9bc47733e9816126e80af0
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058466"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Az Azure Windows rendszerű virtuális gép hálózati adapterének alaphelyzetbe állítása 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Ez a cikk bemutatja, hogyan állíthatja alaphelyzetbe az Azure Windows rendszerű virtuális gép hálózati adapterét a problémák megoldásához, ha nem tud csatlakozni Microsoft Azure Windows rendszerű virtuális géphez (VM), miután:

* Letiltja az alapértelmezett hálózati adaptert (NIC). 
* Manuálisan állítson be statikus IP-címet a hálózati adapterhez. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Hálózati adapter alaphelyzetbe állítása

### <a name="for-vms-deployed-in-resource-group-model"></a>Erőforráscsoport-modellben üzembe helyezett virtuális gépek esetén

1.  Nyissa meg az [Azure Portal](https://ms.portal.azure.com).
2.  Válassza ki az érintett virtuális gépet.
3.  Válassza a **hálózatkezelés** lehetőséget, majd válassza ki a virtuális gép hálózati adapterét.

    ![Hálózati adapter helye](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Válassza az **IP-konfigurációk**lehetőséget.
5.  Válassza ki az IP-címet. 
6.  Ha a **magánhálózati IP-hozzárendelés** nem **statikus**, módosítsa **statikusra**.
7.  Módosítsa az **IP-címet** egy másik IP-címhez, amely elérhető az alhálózatban.
8. A virtuális gép újraindul, hogy inicializálja az új hálózati adaptert a rendszerhez.
9.  Próbálja meg az RDP-t a gépre. Ha a művelet sikeres, a magánhálózati IP-címet visszaállíthatja az eredetire, ha szeretné. Ellenkező esetben megtarthatja. 

#### <a name="use-azure-powershell"></a>Azure PowerShell használatával

1. Ellenőrizze, hogy telepítve van-e [a legújabb Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
2. Nyisson meg egy emelt szintű Azure PowerShell-munkamenetet (Futtatás rendszergazdaként). Futtassa az alábbi parancsot:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzVM -ServiceName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Próbálja meg az RDP-t a gépre.  Ha a művelet sikeres, a magánhálózati IP-címet visszaállíthatja az eredetire, ha szeretné. Ellenkező esetben megtarthatja.

### <a name="for-classic-vms"></a>Klasszikus virtuális gépek esetén

A hálózati adapter alaphelyzetbe állításához kövesse az alábbi lépéseket:

#### <a name="use-azure-portal"></a>Az Azure Portal használata

1.  Nyissa meg az [Azure Portal]( https://ms.portal.azure.com).
2.  Válassza a **Virtual Machines (klasszikus)** lehetőséget.
3.  Válassza ki az érintett virtuális gépet.
4.  Válassza az **IP-címek**lehetőséget.
5.  Ha a **magánhálózati IP-hozzárendelés** nem **statikus**, módosítsa **statikusra**.
6.  Módosítsa az **IP-címet** egy másik IP-címhez, amely elérhető az alhálózatban.
7.  Kattintson a **Mentés** gombra.
8.  A virtuális gép újraindul, hogy inicializálja az új hálózati adaptert a rendszerhez.
9.  Próbálja meg az RDP-t a gépre. Ha a művelet sikeres, dönthet úgy, hogy visszaállít egy magánhálózati IP-címet az eredetire.  

#### <a name="use-azure-powershell"></a>Azure PowerShell használatával

1. Ellenőrizze, hogy telepítve van-e [a legújabb Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) .
2. Nyisson meg egy emelt szintű Azure PowerShell-munkamenetet (Futtatás rendszergazdaként). Futtassa az alábbi parancsot:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ServiceName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Próbálja meg az RDP-t a gépre. Ha a művelet sikeres, a magánhálózati IP-címet visszaállíthatja az eredetire, ha szeretné. Ellenkező esetben megtarthatja. 

## <a name="delete-the-unavailable-nics"></a>A nem elérhető hálózati adapterek törlése
Miután a Távoli asztalt elvégezte a gépen, törölnie kell a régi hálózati adaptereket, hogy elkerülje a lehetséges problémát:

1.  Nyissa meg Eszközkezelő.
2.  Válassza a **nézet** > **rejtett eszközök megjelenítése**lehetőséget.
3.  Válassza a **hálózati adapterek**lehetőséget. 
4.  Keresse meg a "Microsoft Hyper-V hálózati adapter" nevű adaptereket.
5.  A nem elérhető adapterek szürke színnel jelennek meg. Kattintson a jobb gombbal az adapterre, majd válassza az Eltávolítás lehetőséget.

    ![a hálózati adapter képe](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Csak a "Microsoft Hyper-V hálózati adapter" nevű nem elérhető adaptereket távolítsa el. Ha eltávolít egy másik rejtett adaptert, további problémák merülhetnek fel.
    >
    >

6.  Most az összes nem elérhető adaptert törölni kell a rendszeren.
