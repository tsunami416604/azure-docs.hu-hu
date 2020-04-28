---
title: Hibás Azure-beli virtuális gép hibáinak megoldása az Azure-beli beágyazott virtualizálás használatával | Microsoft Docs
description: Azure-beli virtuális gép hibáinak elhárítása beágyazott virtualizálás használatával az Azure-ban
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76119618"
---
# <a name="troubleshoot-a-faulty-azure-vm-by-using-nested-virtualization-in-azure"></a>Hibás Azure-beli virtuális gép hibáinak megoldása az Azure-beli beágyazott virtualizálás használatával

Ez a cikk bemutatja, hogyan hozhat létre beágyazott virtualizációs környezetet a Microsoft Azureban, így hibaelhárítási célból csatlakoztathatja a hibás virtuális gép lemezét a Hyper-V-gazdagépen (mentő virtuális gépen).

## <a name="prerequisites"></a>Előfeltételek

A hibás virtuális gép csatlakoztatásához a mentési virtuális gépnek ugyanazt a típusú Storage-fiókot (standard vagy prémium) kell használnia, mint a hibás virtuális gép.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>1. lépés: mentési virtuális gép létrehozása és Hyper-V szerepkör telepítése

1.  Hozzon létre egy új mentési virtuális gépet:

    -  Operációs rendszer: Windows Server 2016 Datacenter

    -  Size (méret): bármely v3 sorozat legalább két, beágyazott virtualizációt támogató maggal. További információ: [az új Dv3 és a EV3 VM-méretek bemutatása](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Ugyanaz a hely, a Storage-fiók és az erőforráscsoport, mint a hibás virtuális gép.

    -  Válassza ki ugyanazt a tárolási típust, mint a hibás virtuális gép (standard vagy prémium).

2.  A mentési virtuális gép létrehozása után a távoli asztal a mentési virtuális géphez.

3.  A Kiszolgálókezelőben válassza a **kezelés** > **szerepkörök és szolgáltatások hozzáadása**lehetőséget.

4.  A **telepítés típusa** szakaszban válassza a **szerepköralapú vagy a szolgáltatáson alapuló telepítés**lehetőséget.

5.  A **célkiszolgáló kijelölése** szakaszban győződjön meg arról, hogy a mentési virtuális gép ki van választva.

6.  Válassza ki a **Hyper-V szerepkör** > **szolgáltatások hozzáadása**elemet.

7.  A **funkciók** szakaszban válassza a **tovább** lehetőséget.

8.  Ha elérhető egy virtuális kapcsoló, válassza ki. Ellenkező esetben válassza a **tovább**lehetőséget.

9.  Az **áttelepítés** szakaszban válassza a **tovább** lehetőséget.

10. Az **alapértelmezett áruházak** szakaszban válassza a **tovább**lehetőséget.

11. Jelölje be a jelölőnégyzetet a kiszolgáló automatikus újraindításához, ha szükséges.

12. Válassza az **Install** (Telepítés) lehetőséget.

13. A Hyper-V szerepkör telepítésének engedélyezése a kiszolgálónak. Ez eltarthat néhány percig, és a kiszolgáló automatikusan újraindul.

## <a name="step-2-create-the-faulty-vm-on-the-rescue-vms-hyper-v-server"></a>2. lépés: a hibás virtuális gép létrehozása a mentési virtuális gép Hyper-V-kiszolgálóján

1.  [Hozzon létre egy pillanatkép-lemezt](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk) a problémát okozó virtuális gép operációsrendszer-lemezén, majd csatolja a pillanatkép-lemezt a recuse virtuális géphez.

2.  Távoli asztal a mentő virtuális géphez.

3.  Nyissa meg a Lemezkezelés (diskmgmt. msc) parancsot. Győződjön meg arról, hogy a hibás virtuális gép lemeze **Offline**értékre van állítva.

4.  Nyissa meg a Hyper-V kezelőjét: a **Kiszolgálókezelőben**válassza ki a **Hyper-v szerepkört**. Kattintson a jobb gombbal a kiszolgálóra, majd válassza ki a **Hyper-V kezelőjét**.

5.  A Hyper-V kezelőjében kattintson a jobb gombbal a mentő virtuális gépre, majd válassza az **új** > **virtuális gép** > **tovább**lehetőséget.

6.  Adja meg a virtuális gép nevét, majd kattintson a **tovább**gombra.

7.  Válassza az **1. generáció**lehetőséget.

8.  Állítsa be az indítási memóriát 1024 MB-nál nagyobb értékre.

9. Ha alkalmazható, válassza ki a létrehozott Hyper-V hálózati kapcsolót. Másik lépés a következő lapra.

10. Válassza **a virtuális merevlemez csatlakoztatása később**lehetőséget.

    ![a virtuális merevlemez csatolása később lehetőséggel kapcsolatos rendszerkép](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. A virtuális gép létrehozásakor válassza a **Befejezés** lehetőséget.

12. Kattintson a jobb gombbal a létrehozott virtuális gépre, majd válassza a **Beállítások**lehetőséget.

13. Válassza az **ide-vezérlő 0**lehetőséget, válassza a **merevlemez**lehetőséget, majd kattintson a **Hozzáadás**gombra.

    ![az új merevlemez-meghajtó hozzáadására szolgáló rendszerkép](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. A **fizikai merevlemez**területen válassza ki az Azure-beli virtuális géphez csatlakoztatott hibás virtuális gép lemezét. Ha nem látja a felsorolt lemezeket, ellenőrizze, hogy a lemez offline értékre van-e állítva a Lemezkezelés eszköz használatával.

    ![a lemez csatlakoztatására szolgáló rendszerkép](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. Válassza az **Apply** (Alkalmaz) lehetőséget, majd kattintson az **OK** gombra.

16. Kattintson duplán a virtuális gépre, majd indítsa el.

17. Most már dolgozhat a virtuális gépen a helyszíni virtuális gép használatával. Követheti a szükséges hibaelhárítási lépéseket.

## <a name="step-3-replace-the-os-disk-used-by-the-faulty-vm"></a>3. lépés: a hibás virtuális gép által használt operációsrendszer-lemez cseréje

1.  Miután a virtuális gép ismét online állapotba került, állítsa le a virtuális gépet a Hyper-V kezelőjében.

2.  Válassza [le és válassza le a javított operációsrendszer-lemezt](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk
).
3.  [Cserélje le a virtuális gép által a javított operációsrendszer-lemezzel használt operációsrendszer-lemezt](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
).

## <a name="next-steps"></a>További lépések

Ha problémába ütközik a virtuális géphez való csatlakozással kapcsolatban, tekintse meg [az RDP-kapcsolatok hibaelhárítása Azure-beli virtuális géppel](troubleshoot-rdp-connection.md)című témakört. A virtuális gépen futó alkalmazások elérésével kapcsolatos problémákért lásd: az [alkalmazások kapcsolódási problémáinak elhárítása Windows rendszerű virtuális gépen](troubleshoot-app-connection.md).
