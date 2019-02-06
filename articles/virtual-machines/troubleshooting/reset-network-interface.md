---
title: Azure Windows virtuális gép alaphelyzetbe állítása a hálózati adapter |} A Microsoft Docs
description: Bemutatja, hogyan Azure Windows virtuális gép hálózati adapter alaphelyzetbe állítása
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: 7b8516566709c024043c413098c948965e255e13
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746659"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Azure Windows virtuális gép alaphelyzetbe állítása a hálózati adapter 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Ez a cikk bemutatja az Azure Windows virtuális gép problémák megoldásához, ha nem tud kapcsolódni a Microsoft Azure Windows virtuális gép (VM) után a hálózati adapter alaphelyzetbe állítása:

* Letiltja az alapértelmezett hálózati adaptert (NIC). 
* Manuálisan beállított egy statikus IP-címet a hálózati adaptert. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Hálózati adapter alaphelyzetbe állítása

### <a name="for-vms-deployed-in-resource-group-model"></a>A csoport erőforrásmodell üzembe helyezett virtuális gépek

1.  Nyissa meg az [Azure Portal](https://ms.portal.azure.com).
2.  Válassza ki az érintett virtuális gépet.
3.  Válassza ki **hálózatkezelés** , és válassza ki a hálózati adapter a virtuális gép.

    ![Hálózati adapter helye](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Válassza ki **IP-konfigurációk**.
5.  Válassza ki a IP-cím. 
6.  Ha a **magánhálózati IP-hozzárendelés** nem **statikus**, módosítsa a következőre **statikus**.
7.  Módosítsa a **IP-cím** egy másik alhálózaton elérhető IP-címre.
8. Inicializálja az új hálózati Adaptert a rendszer a virtuális gép újraindul.
9.  Próbálja ki az RDP-hez a gépre. Sikeres művelet esetén módosíthatja a magánhálózati IP-cím vissza az eredeti, ha szeretné. Ellenkező esetben megtarthatja ezt. 

#### <a name="use-azure-powershell"></a>Azure PowerShell használatával

1. Győződjön meg arról, hogy rendelkezik-e [az Azure PowerShell legújabb verzióját](https://docs.microsoft.com/powershell/azure/overview) telepítve
2. Nyisson meg egy emelt szintű Azure PowerShell-munkamenetet (Futtatás rendszergazdaként). Futtassa az alábbi parancsot:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureRMAccount
    Select-AzureRMSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzureRMVM -ServiceName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzureRMVM
    ```
3. Próbálja ki az RDP-hez a gépre.  Sikeres művelet esetén módosíthatja a magánhálózati IP-cím vissza az eredeti, ha szeretné. Ellenkező esetben megtarthatja ezt.

### <a name="for-classic-vms"></a>A klasszikus virtuális gépek

Hálózati adapter alaphelyzetbe állítása, kövesse az alábbi lépéseket:

#### <a name="use-azure-portal"></a>Az Azure Portal használata

1.  Nyissa meg az [Azure Portal]( https://ms.portal.azure.com).
2.  Válassza ki **virtuális gépek (klasszikus)**.
3.  Válassza ki az érintett virtuális gépet.
4.  Válassza ki **IP-címek**.
5.  Ha a **magánhálózati IP-hozzárendelés** nem **statikus**, módosítsa a következőre **statikus**.
6.  Módosítsa a **IP-cím** egy másik alhálózaton elérhető IP-címre.
7.  Kattintson a **Mentés** gombra.
8.  Inicializálja az új hálózati Adaptert a rendszer a virtuális gép újraindul.
9.  Próbálja ki az RDP-hez a gépre. Ha ez sikeres, ha szeretné, térhet vissza az eredeti a magánhálózati IP-cím.  

#### <a name="use-azure-powershell"></a>Azure PowerShell használatával

1. Győződjön meg arról, hogy rendelkezik-e [az Azure PowerShell legújabb verzióját](https://docs.microsoft.com/powershell/azure/overview) telepítve.
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
3. Próbálja ki az RDP-hez a gépre. Sikeres művelet esetén módosíthatja a magánhálózati IP-cím vissza az eredeti, ha szeretné. Ellenkező esetben megtarthatja ezt. 

## <a name="delete-the-unavailable-nics"></a>Nem érhető el a hálózati adapter törlése
Után is a távoli asztal a géphez, törölnie kell a régi hálózati adaptereket a lehetséges problémák elkerülése érdekében:

1.  Nyissa meg az Eszközkezelőt.
2.  Válassza ki **nézet** > **rejtett eszközök megjelenítése**.
3.  Válassza ki **hálózati adapterek**. 
4.  Ellenőrizze, hogy a "Microsoft Hyper-V hálózati Adapter" nevű adaptereket.
5.  A nem elérhető adapterek szürke színnel jelennek meg. Kattintson a jobb gombbal az adaptert, és kattintson az eltávolítás.

    ![a hálózati adapter képe](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Csak távolítsa el a nem érhető el "Microsoft Hyper-V hálózati Adapter" nevű adaptereket. Ha bármely más rejtett adapter távolítja el, további problémákat okozhat.
    >
    >

6.  Most már nem érhető el az összes adapter ne legyen bejelölve, a rendszer.
