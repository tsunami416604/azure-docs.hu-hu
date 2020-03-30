---
title: Az Azure Windows VM hálózati felületének alaphelyzetbe állítása| Microsoft dokumentumok
description: Az Azure Windows VM hálózati illesztőjének alaphelyzetbe állítása
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
ms.openlocfilehash: a8bd12d98b76d5848753987c4f7bcb76d4e2266d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250074"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Windows rendszerű Azure-beli virtuális gép hálózati adapterének visszaállítása 

Ez a cikk bemutatja, hogyan állíthatja alaphelyzetbe az Azure Windows VM hálózati felületét a problémák megoldásához, ha nem tud csatlakozni a Microsoft Azure Windows virtuális géphez (VM) a következő után:

* Letiltja az alapértelmezett hálózati adaptert. 
* Manuálisan állítja be a hálózati adapter statikus IP-címét. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Hálózati adapter alaphelyzetbe állítása

### <a name="for-vms-deployed-in-resource-group-model"></a>Az erőforráscsoport-modellben üzembe helyezett virtuális gépek esetén

1.  Nyissa meg az [Azure Portalt.](https://ms.portal.azure.com)
2.  Válassza ki az érintett virtuális gépet.
3.  Válassza **a Hálózat lehetőséget,** majd válassza ki a virtuális gép hálózati illesztőfelületét.

    ![Hálózati adapter helye](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Válassza az **IP-konfigurációk lehetőséget.**
5.  Válassza ki az IP-címet. 
6.  Ha a **privát IP-hozzárendelés** nem **statikus,** módosítsa **statikusra.**
7.  Módosítsa az **IP-címet** egy másik, az alhálózatban elérhető IP-címre.
8. A virtuális gép újraindul az új hálózati adapter inicializálásához a rendszerbe.
9.  Próbálja rdp a gépre. Ha sikeres, a privát IP-címet visszamódosíthatja az eredetire, ha szeretné. Különben megtarthatod. 

#### <a name="use-azure-powershell"></a>Azure PowerShell használatával

1. Győződjön meg arról, hogy [a legújabb Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) telepítve van
2. Nyisson meg egy emelt szintű Azure PowerShell-munkamenetet (futtatás rendszergazdaként). Futtassa az alábbi parancsot:

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
    Get-AzVM -ResourceGroupName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Próbálja rdp a gépre.  Ha sikeres, a privát IP-címet visszamódosíthatja az eredetire, ha szeretné. Különben megtarthatod.

### <a name="for-classic-vms"></a>Klasszikus virtuális gépekhez

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

A hálózati adapter alaphelyzetbe állításához hajtsa végre az alábbi lépéseket:

#### <a name="use-azure-portal"></a>Az Azure Portal használata

1.  Nyissa meg az [Azure Portalt.]( https://ms.portal.azure.com)
2.  Válassza ki **a virtuális gépek (klasszikus)** lehetőséget.
3.  Válassza ki az érintett virtuális gépet.
4.  Válassza az **IP-címek lehetőséget.**
5.  Ha a **privát IP-hozzárendelés** nem **statikus,** módosítsa **statikusra.**
6.  Módosítsa az **IP-címet** egy másik, az alhálózatban elérhető IP-címre.
7.  Kattintson a **Mentés** gombra.
8.  A virtuális gép újraindul az új hálózati adapter inicializálásához a rendszerbe.
9.  Próbálja rdp a gépre. Ha sikeres, dönthet úgy, hogy visszaállítja a privát IP-címet az eredetire.  

#### <a name="use-azure-powershell"></a>Azure PowerShell használatával

1. Győződjön meg arról, hogy [a legújabb Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) telepítve van.
2. Nyisson meg egy emelt szintű Azure PowerShell-munkamenetet (futtatás rendszergazdaként). Futtassa az alábbi parancsot:

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
    Get-AzureVM -ResourceGroupName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Próbálja rdp a gépre. Ha sikeres, a privát IP-címet visszamódosíthatja az eredetire, ha szeretné. Különben megtarthatod. 

## <a name="delete-the-unavailable-nics"></a>A nem elérhető hálózati adapterek törlése
Miután a számítógéphez távoli asztalt használhat, a lehetséges probléma elkerülése érdekében törölnie kell a régi hálózati adaptereket:

1.  Indítsa el az Eszközkezelőt.
2.  Válassza **a Rejtett** > **eszközök megjelenítése**lehetőséget.
3.  Válassza a **Hálózati adapterek lehetőséget.** 
4.  Ellenőrizze a "Microsoft Hyper-V hálózati adapter" nevű adaptereket.
5.  Előfordulhat, hogy egy nem elérhető adapter szürkén jelenik meg. Kattintson a jobb gombbal az adapterre, majd válassza az Eltávolítás parancsot.

    ![a hálózati adapter képe](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Csak a "Microsoft Hyper-V hálózati adapter" nevű nem elérhető adaptereket távolítsa el. Ha eltávolítja a többi rejtett adaptert, az további problémákat okozhat.
    >
    >

6.  Most az összes nem elérhető adaptert törölni kell a rendszerből.
