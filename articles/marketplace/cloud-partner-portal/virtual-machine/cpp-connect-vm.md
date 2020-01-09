---
title: Kapcsolódás a Microsoft Azure-alapú virtuális géphez | Azure piactér
description: Ez a cikk azt ismerteti, hogyan csatlakozhat az Azure-on létrehozott új virtuális géphez.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 3256115821abf5e81d04268ffd2eb310d213ab06
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432007"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Kapcsolódás Azure-alapú virtuális géphez

Ez a cikk azt ismerteti, hogyan csatlakozhat az Azure-ban létrehozott virtuális gépekhez (VM-EK), és bejelentkezhet azokba.  A sikeres csatlakozást követően ugyanúgy dolgozhat a virtuális géppel, mintha helyileg bejelentkezett volna a gazdagép kiszolgálójára. 

## <a name="connect-to-a-windows-based-vm"></a>Kapcsolódás Windows-alapú virtuális géphez

A távoli asztal ügyfelével csatlakozhat az Azure-ban üzemeltetett Windows-alapú virtuális géphez.  A Windows legtöbb verziója natív módon támogatja a Remote Desktop Protocol (RDP) szolgáltatást.  Más gépek esetén további információkat találhat [Távoli asztal ügyfelek](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)ügyfeleiről.  

A következő cikk részletesen ismerteti, hogyan használható a beépített Windows RDP-támogatás a virtuális géphez való kapcsolódáshoz: [Kapcsolódás és bejelentkezés egy Windows rendszerű Azure-beli virtuális gépre](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> A folyamat során biztonsági figyelmeztetéseket kaphat, például arról, hogy az. rdp fájl ismeretlen közzétevőből származik, vagy a felhasználói hitelesítő adatok nem ellenőrizhetők.  Ezeket a figyelmeztetéseket nyugodtan figyelmen kívül hagyhatja.


## <a name="connect-to-a-linux-based-vm"></a>Kapcsolódás Linux-alapú virtuális géphez

A Linux-alapú virtuális gép összekapcsolásához Secure Shell Protocol-(SSH-) ügyfélre van szükség.  Ez a vitafórum az ingyenes [Putty](https://www.ssh.com/ssh/putty/) SHH-terminált fogja használni.

1. Nyissa meg az [Azure Portalt](https://ms.portal.azure.com). Keresse meg és válassza ki a **virtuális gépeket**. 
2. Válassza ki azt a virtuális gépet, amelyhez csatlakozni szeretne.  
3. Ha még nem fut, **indítsa el** a virtuális gépet.
4. Kattintson a virtuális gép nevére, és nyissa meg az **Áttekintés** oldalát.
5. Jegyezze fel a virtuális gép nyilvános IP-címét és DNS-nevét.  (Ha ezek az értékek nincsenek beállítva, akkor létre kell [hoznia egy hálózati adaptert](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface) .

   ![VIRTUÁLIS gépek áttekintése – beállítások](./media/publishvm_019.png)
 
6. Nyissa meg a PuTTY alkalmazást.  
7. A PuTTY konfigurációja párbeszédpanelen adja meg a virtuális gép IP-címét vagy DNS-nevét. 

   ![Putty-terminál beállításai](./media/publishvm_020.png)
 
8. A PuTTY-terminál megnyitásához kattintson a **Megnyitás** gombra.  
9. Amikor a rendszer kéri, adja meg a linuxos virtuális gép fiókjának nevét és jelszavát. 

Ha kapcsolódási problémák léptek fel, tekintse meg az SSH-ügyfél dokumentációját, például a [10. fejezet: gyakori hibaüzenetek](https://www.ssh.com/ssh/putty/putty-manuals).

További információt, többek között az asztal egy kiépített linuxos virtuális géphez való hozzáadásával kapcsolatban lásd: [Távoli asztal telepítése és konfigurálása egy linuxos virtuális géphez való kapcsolódáshoz az Azure-ban](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Használaton kívüli virtuális gépek leállítása
Azure-számlák virtuális gépek üzemeltetéséhez, ha egy virtuális gép fut *vagy üresjáratban*van.  Ezért ajánlott leállítani a jelenleg nem használt virtuális gépeket.  Például a teszt, a biztonsági mentés vagy a kivont virtuális gépek leállítására jelöltek. A virtuális gép leállításához hajtsa végre a következő lépéseket:

1. A **Virtual Machines (virtuális gépek** ) panelen válassza ki a leállítani kívánt virtuális gépet. 
2. A lap tetején található eszköztáron kattintson a **Leállítás** gombra.

   ![Virtuális gép leállítása](./media/publishvm_018.png)

Az Azure gyorsan leállítja a virtuális gépet egy *felszabadítás*nevű folyamatban, amely nem csak a virtuális gép operációs rendszerét állítja le, hanem felszabadítja a korábban kiosztott hardver-és hálózati erőforrásokat is.

Ha később szeretné újraaktiválni egy leállított virtuális gépet, jelölje ki, majd kattintson a **Start** gombra.


## <a name="next-steps"></a>Következő lépések

A távoli csatlakozást követően készen áll [a virtuális gép konfigurálására](./cpp-configure-vm.md).
