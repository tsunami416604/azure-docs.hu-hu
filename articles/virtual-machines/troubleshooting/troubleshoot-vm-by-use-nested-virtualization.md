---
title: Hibás Azure-virtuális gép hibaelhárítása beágyazott virtualizáció használatával az Azure-ban | Microsoft dokumentumok
description: Probléma elhárítása az Azure Virtuális gép beágyazott virtualizáció használatával az Azure-ban
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/19/2019
ms.author: genli
ms.openlocfilehash: e1acfc3216ccfaeac035f1ff31e82c7b67c17daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76119618"
---
# <a name="troubleshoot-a-faulty-azure-vm-by-using-nested-virtualization-in-azure"></a>Hibás Azure-virtuális gép hibaelhárítása beágyazott virtualizáció használatával az Azure-ban

Ez a cikk bemutatja, hogyan hozhat létre egy beágyazott virtualizálási környezetet a Microsoft Azure-ban, így a hibás virtuális gép lemezét a Hyper-V gazdagépen (Rescue VM) hibaelhárítási célokra csatlakoztathatja.

## <a name="prerequisites"></a>Előfeltételek

A hibás virtuális gép csatlakoztatásához a mentési virtuális gépnek ugyanolyan típusú tárfiókot (standard vagy prémium) kell használnia, mint a hibás virtuális gépet.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>1. lépés: Hozzon létre egy mentési virtuális gép és telepítse a Hyper-V szerepkör

1.  Hozzon létre egy új mentési virtuális gép:

    -  Operációs rendszer: Windows Server 2016 Datacenter

    -  Méret: Minden V3-as sorozat, amely legalább két maggal támogatja a beágyazott virtualizációt. További információ: [Az új Dv3 és Ev3 virtuális gép méretei.](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)

    -  Ugyanaz a hely, a tárfiók és az erőforráscsoport, mint a hibás virtuális gép.

    -  Válassza ki ugyanazt a tárolási típust, mint a hibás virtuális gép (Standard vagy Premium).

2.  A mentési virtuális gép létrehozása után a távoli asztalon a mentési virtuális gép.

3.  A Kiszolgálókezelőben **Manage** > válassza a**Szerepkörök és szolgáltatások hozzáadása lehetőséget.**

4.  A **Telepítés típusa** csoportban válassza **a Szerepköralapú vagy szolgáltatásalapú telepítés lehetőséget.**

5.  A **Célkiszolgáló kiválasztása** csoportban győződjön meg arról, hogy a mentési virtuális gép ki van jelölve.

6.  Válassza a **Hyper-V szerepkör** > **szolgáltatások hozzáadása lehetőséget.**

7.  Válassza a **Tovább gombot** a **Funkciók** szakaszon.

8.  Ha rendelkezésre áll egy virtuális kapcsoló, jelölje ki azt. Ellenkező esetben válassza a **Tovább lehetőséget.**

9.  Az **Áttelepítés** csoportban válassza a **Tovább gombot**

10. Az **Alapértelmezett raktárak** csoportban válassza a **Tovább gombot.**

11. Jelölje be a jelölőnégyzetet, ha szükség esetén automatikusan újra szeretné indítani a kiszolgálót.

12. Válassza az **Install** (Telepítés) lehetőséget.

13. Engedélyezze a kiszolgálónak a Hyper-V szerepkör telepítését. Ez néhány percet vesz igénybe, és a kiszolgáló automatikusan újraindul.

## <a name="step-2-create-the-faulty-vm-on-the-rescue-vms-hyper-v-server"></a>2. lépés: A hibás virtuális gép létrehozása a mentési virtuális gép Hyper-V kiszolgálóján

1.  [Hozzon létre egy pillanatkép-lemezt](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk) a problémát okozó virtuális gép operációs rendszerlemezéhez, majd csatolja a pillanatképlemezt a recuse virtuális géphez.

2.  Távoli asztal a mentési virtuális géphez.

3.  Nyissa meg a Lemezkezelés (diskmgmt.msc) programot. Győződjön meg arról, hogy a hibás virtuális gép lemeze **Offline állapotban**van.

4.  Nyissa meg a Hyper-V kezelőt: A **Kiszolgálókezelőben**válassza a **Hyper-V szerepkört**. Kattintson a jobb gombbal a kiszolgálóra, majd válassza a **Hyper-V Manager parancsot.**

5.  A Hyper-V Manager ben kattintson a jobb gombbal a mentési virtuális gépre, majd válassza az **Új** > **virtuális gép** > **ezután parancsot.**

6.  Írja be a virtuális gép nevét, majd válassza a **Tovább**gombot.

7.  Válassza **az 1.**

8.  Állítsa az indítási memóriát legalább 1024 MB-ra.

9. Ha alkalmazható, válassza ki a létrehozott Hyper-V hálózati kapcsolót. Else ugrás a következő oldalra.

10. Válassza **a Virtuális merevlemez csatolása később**lehetőséget.

    ![A virtuális merevlemez csatlakoztatása később beállításról](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. Válassza **a Befejezés lehetőséget** a virtuális gép létrehozásakor.

12. Kattintson a jobb gombbal a létrehozott virtuális gépre, és válassza a **Beállítások parancsot.**

13. Válassza **az IDE-vezérlő 0**lehetőséget, válassza a **Merevlemez**lehetőséget, majd kattintson a **Hozzáadás**gombra.

    ![a kép körülbelül új merevlemezt ad hozzá](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. A **fizikai merevlemezen**válassza ki az Azure virtuális géphez csatlakoztatott hibás virtuális gép lemezét. Ha a listában nem láthatók lemezek, ellenőrizze, hogy a lemez kapcsolat nélküli módra van-e állítva a Lemezkezelés segédprogramsegítségével.

    ![a lemez csatlakoztatásáról szóló kép](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. Válassza az **Apply** (Alkalmaz) lehetőséget, majd kattintson az **OK** gombra.

16. Kattintson duplán a virtuális gépre, majd indítsa el.

17. Most már dolgozhat a virtuális gép, mint a helyszíni virtuális gép. Bármilyen hibaelhárítási lépést elkövethet, amire szüksége van.

## <a name="step-3-replace-the-os-disk-used-by-the-faulty-vm"></a>3. lépés: Cserélje ki a hibás virtuális gép által használt operációsrendszer-lemezt

1.  Miután újra online állapotba hozza a virtuális gép, állítsa le a virtuális gép a Hyper-V kezelőben.

2.  [Válassza le és válassza le a javított operációsrendszer-lemezt](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk
).
3.  [Cserélje le a virtuális gép által használt operációsrendszer-lemezt a javított operációsrendszer-lemezre](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
).

## <a name="next-steps"></a>További lépések

Ha problémái vannak a virtuális géphez való csatlakozással, olvassa [el az RDP-kapcsolatok hibaelhárítása azure-beli virtuális géphez című témakört.](troubleshoot-rdp-connection.md) A virtuális gépen futó alkalmazások elérésével kapcsolatos problémákról a [Windows virtuális gépeken az alkalmazáscsatlakozási problémák elhárítása című](troubleshoot-app-connection.md)témakörben nyújt fel problémát.
