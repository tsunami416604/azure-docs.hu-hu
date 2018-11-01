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
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 23cf02e8cc33b3a66a04ae0472b1e5a6baa59cc2
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418993"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Azure Windows virtuális gép alaphelyzetbe állítása a hálózati adapter 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Miután letiltja az alapértelmezett hálózati adaptert (NIC) vagy manuálisan beállítja a statikus IP-címet a hálózati nem tud kapcsolódni a Microsoft Azure Windows virtuális gép (VM) Ez a cikk bemutatja, hogyan alaphelyzetbe állítani a hálózati adapter Azure Windows virtuális gép, amely feloldja a távoli kapcsolati probléma.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]
## <a name="reset-network-interface"></a>Hálózati adapter alaphelyzetbe állítása

### <a name="for-classic-vms"></a>A klasszikus virtuális gépek

Hálózati adapter alaphelyzetbe állítása, kövesse az alábbi lépéseket:

1.  Nyissa meg az [Azure Portal]( https://ms.portal.azure.com).
2.  Válassza ki **virtuális gépek (klasszikus)**.
3.  Válassza ki az érintett virtuális gépet.
4.  Válassza ki **IP-címek**.
5.  Ha a **magánhálózati IP-hozzárendelés** nem **statikus**, módosítsa a következőre **statikus**.
6.  Módosítsa a **IP-cím** egy másik alhálózaton elérhető IP-címre.
7.  Válassza a Mentés gombra.
8.  Inicializálja az új hálózati Adaptert a rendszer a virtuális gép újraindul.
9.  Próbálja ki az RDP-hez a gépre. Sikeres művelet esetén módosíthatja a magánhálózati IP-cím vissza az eredeti, ha szeretné. Ellenkező esetben megtarthatja ezt. 

### <a name="for-vms-deployed-in-resource-group-model"></a>A csoport erőforrásmodell üzembe helyezett virtuális gépek

1.  Nyissa meg az [Azure Portal]( https://ms.portal.azure.com).
2.  Válassza ki az érintett virtuális gépet.
3.  Válassza ki **hálózati adapterek**.
4.  Válassza ki a géphez társított hálózati adapter
5.  Válassza ki **IP-konfigurációk**.
6.  Válassza ki a IP-cím. 
7.  Ha a **magánhálózati IP-hozzárendelés** nem **statikus**, módosítsa a következőre **statikus**.
8.  Módosítsa a **IP-cím** egy másik alhálózaton elérhető IP-címre.
9. Inicializálja az új hálózati Adaptert a rendszer a virtuális gép újraindul.
10. Próbálja ki az RDP-hez a gépre. Sikeres művelet esetén módosíthatja a magánhálózati IP-cím vissza az eredeti, ha szeretné. Ellenkező esetben megtarthatja ezt. 

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

6.  Most már az összes nem elérhető adaptert eltávolíthatja a rendszerből.
