---
title: Csatlakozás Microsoft Azure-alapú virtuális géphez | Azure Piactér
description: Bemutatja, hogyan csatlakozhat az Azure-ban létrehozott új virtuális géphez.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 6f2652a98aa081b62ba80951212ef3a3de513593
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272661"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Csatlakozás azure-alapú virtuális géphez

> [!IMPORTANT]
> 2020. április 13-tól megkezdjük az Azure virtuálisgép-ajánlatok partnerközpontba való áthelyezését. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [Azure Virtuálisgép technikai eszközeinek létrehozása](https://aka.ms/AzureVMTechAsset) az áttelepített ajánlatok kezeléséhez című, az Utasításokat.

Ez a cikk bemutatja, hogyan csatlakozhat az Azure-ban létrehozott virtuális gépekhez, és hogyan jelentkezhet be.  Miután sikeresen csatlakozott, úgy dolgozhat a virtuális géptel, mintha helyileg jelentkezett volna be a gazdakiszolgálóra. 

## <a name="connect-to-a-windows-based-vm"></a>Csatlakozás Windows-alapú virtuális géphez

A távoli asztali ügyfél segítségével csatlakozhat az Azure-ban üzemeltetett Windows-alapú virtuális géphez.  A Windows legtöbb verziója natív módon támogatja a távoli asztali protokollt (RDP).  Más gépek esetében további információt az ügyfelekről a [Távoli asztali ügyfelek ben](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)talál.  

Az alábbi cikk bemutatja, hogyan használható a beépített Windows RDP-támogatás a virtuális géphez való csatlakozáshoz: [Csatlakozás és bejelentkezés Windows rendszerű Azure virtuális gépre.](../../../virtual-machines/windows/connect-logon.md)  

>[!TIP]
> A folyamat során biztonsági figyelmeztetéseket kaphat, például azt, hogy az .rdp fájl ismeretlen közzétevőtől származik, vagy hogy a felhasználói hitelesítő adatok nem ellenőrizhetők.  Ezeket a figyelmeztetéseket nyugodtan figyelmen kívül hagyhatja.


## <a name="connect-to-a-linux-based-vm"></a>Csatlakozás Linux-alapú virtuális géphez

A Linux-alapú virtuális gép csatlakoztatásához egy biztonságos rendszerhéj-protokoll (SSH) ügyfélre van szükség.  Ez a vita fogja használni az ingyenes [PuTTY](https://www.ssh.com/ssh/putty/) SHH terminál.

1. Nyissa meg az [Azure Portalt.](https://ms.portal.azure.com) Keressen és válasszon **virtuális gépek**lehetőséget. 
2. Válassza ki azt a virtuális gép, amelyhez csatlakozni szeretne.  
3. **Indítsa el** a virtuális gép, ha még nem fut.
4. Kattintson a virtuális gép nevére az **Áttekintő** lap megnyitásához.
5. Vegye figyelembe a nyilvános IP-címet és a VIRTUÁLIS GÉP DNS-nevét.  (Ha ezek az értékek nincsenek beállítva, akkor létre kell [hoznia egy hálózati adaptert](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![Virtuális gép – áttekintés beállításai](./media/publishvm_019.png)
 
6. Nyissa meg a PuTTY alkalmazást.  
7. A PuTTY-konfiguráció párbeszédpanelen adja meg a virtuális gép IP-címét vagy DNS-nevét. 

   ![PuTTY terminál beállítások](./media/publishvm_020.png)
 
8. A PuTTY terminál megnyitásához kattintson a **Megnyitás** gombra.  
9. Amikor a rendszer kéri, adja meg a Linux virtuális gép fiók nevét és jelszavát. 

Ha csatlakozási problémái vannak, olvassa el az SSH-ügyfél dokumentációját, például [a 10.](https://www.ssh.com/ssh/putty/putty-manuals)

További információt, például arról, hogyan adhat hozzá asztalt egy kiépített Linux virtuális géphez, [olvassa el a Távoli asztal telepítése és konfigurálása linuxos virtuális géphez való csatlakozáshoz az Azure-ban című témakört.](../../../virtual-machines/linux/use-remote-desktop.md)


## <a name="stop-unused-vms"></a>A nem használt virtuális gépek leállítása
A virtuális gép üzemeltetése esetén a virtuális gép futtatása *kor vagy tétlen*ül.  Így ajánlott leállítani a virtuális gépek, amelyek jelenleg nem használatban vannak.  Például a teszt, a biztonsági mentés vagy a kiközösedett virtuális gépek leállítási jelöltek. Virtuális gép leállításához hajtsa végre az alábbi lépéseket:

1. A **Virtuális gépek** panelen válassza ki a leállítani kívánt virtuális gépet. 
2. A lap tetején található eszköztáron kattintson a **Leállítás** gombra.

   ![Virtuális gép leállítása](./media/publishvm_018.png)

Az Azure gyorsan leállítja a virtuális gép egy folyamat nevű *felszabadítása*, amely nem csak leállítja az operációs rendszert a virtuális gép, hanem felszabadítja a hardver és a hálózati erőforrások korábban kiépített hozzá.

Ha később újra szeretne aktiválni egy leállított virtuális gép, jelölje ki, és kattintson a **Start** gombra.


## <a name="next-steps"></a>További lépések

Miután távolról csatlakozott, készen áll [a virtuális gép konfigurálására.](./cpp-configure-vm.md)
