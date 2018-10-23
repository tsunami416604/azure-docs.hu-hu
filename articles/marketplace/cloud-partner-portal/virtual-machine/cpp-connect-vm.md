---
title: A Microsoft Azure-alapú virtuális gép csatlakoztatása |} A Microsoft Docs
description: Azt ismerteti, hogyan csatlakozhat az új virtuális gép létrehozása az Azure-ban.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: fd68846b9144c3efcc71dec369d64119427758a3
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639761"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Csatlakozás az Azure-alapú virtuális gépen

Ez a cikk bemutatja, hogyan csatlakozhat, és jelentkezzen be az Azure-ban létrehozott virtuális gépek (VM).  Miután sikeresen csatlakozott, használhatja a virtuális géppel, mintha helyileg a gazdagép-kiszolgálón történő bejelentkezés. 

## <a name="connect-to-a-windows-based-vm"></a>Windows-alapú virtuális Géphez való csatlakozás

A távoli asztali ügyfél használatával fog csatlakozni a Windows-alapú virtuális gép Azure-ban üzemeltetett.  A Windows legtöbb verziója natív módon tartalmaz a távoli asztal protokoll (RDP) támogatása.  További információ az ügyfelek annak más gépek [távoli asztali ügyfelek](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

A következő cikk részletesen bemutatja a beépített Windows RDP-támogatás használata a virtuális Géphez való csatlakozáshoz: [csatlakoztatása és bejelentkezés Windows rendszert futtató Azure virtuális gép](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> Kaphat biztonsági figyelmeztetés a folyamat során például, hogy van-e az .rdp fájl közzétevője ismeretlen vagy nem ellenőrizhető a felhasználói hitelesítő adatait.  Már biztonságosan figyelmen kívül hagyja ezeket a figyelmeztetéseket.


## <a name="connect-to-a-linux-based-vm"></a>Csatlakozás egy Linux-alapú virtuális géphez

A Linux-alapú virtuális gép csatlakozik, a secure shell (SSH) protokoll ügyfél van szüksége.  A hozzászólás fogja használni az ingyenes [PuTTY](https://www.ssh.com/ssh/putty/) SHH terminált.

1. Az a **virtuális gépek** paneljén a [az Azure portal](https://ms.portal.azure.com), válassza ki a virtuális Gépet, amelyhez csatlakozni kíván.  
2. **Indítsa el** már nem fut a virtuális gép.
3. Kattintson a virtuális Gépre, és nyissa meg a neve annak **áttekintése** lap.
4. Vegye figyelembe a nyilvános IP-cím és a virtuális gép DNS-nevét.  (Ha ezek az értékek nincsenek megadva, akkor meg kell [egy hálózati adapter létrehozása](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![Áttekintés a virtuális gépek beállításai](./media/publishvm_019.png)
 
5. Nyissa meg a PuTTY alkalmazást.  
6. A PuTTY konfigurációs párbeszédpanelen adja meg az IP-cím vagy a virtuális gép DNS-nevét. 

   ![PuTTY terminál beállításai](./media/publishvm_020.png)
 
7. Kattintson a **nyissa meg a** , nyissa meg a PuTTY terminált.  
8. Amikor a rendszer kéri, adja meg a fiók nevét és jelszavát a Linux rendszerű virtuális gép. 

   Ha kapcsolódási problémákat tapasztal, tekintse meg az SSH-ügyfél dokumentációját például [fejezet 10: Gyakori hibaüzenetek](https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter10.html#errors).

További információk, beleértve a kiépített Linux rendszerű virtuális gép hozzáadása egy asztali: [telepítése és konfigurálása az Azure-beli Linuxos virtuális gép kapcsolódni a távoli asztal](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>A fel nem használt virtuális gépek leállítása
Virtuális gép üzemeltetésére, ha a virtuális gép fut az Azure számlák *vagy tétlen*.  Ezért ajánlott leállítani a virtuális gépeket, amelyek jelenleg nem használt.  Például tesztelni, biztonsági mentést, vagy kivont virtuális gépek leállítása a deduplikációra. Állítsa le a virtuális Géphez, hajtsa végre az alábbi lépéseket:

1. Az a **virtuális gépek** panelen válassza ki a virtuális Gépet leállítja. 
2. Az eszköztáron a lap tetején kattintson a a **leállítása** gombra.

   ![Virtuális gép leállítása](./media/publishvm_018.png)

Azure gyors leállítja a virtuális Géphez a nevű folyamat *felszabadítási*, amely nem csupán a virtuális gép operációs rendszerének leállítja, de a mindennapos infrastruktúrakezelési azt a korábban kiosztott hardver- és hálózati erőforrásokat.

Ha később a leállított virtuális gép újraaktiválni szeretné, válassza ki azt, majd kattintson a **Start** gombra.


## <a name="next-steps"></a>További lépések

Miután Ön távolról kapcsolódik, készen áll-e, [konfigurálja a virtuális gép](./cpp-configure-vm.md).
