---
title: Azure-beli virtuális gép hibáinak elhárítása beágyazott virtualizálás használatával az Azure-ban | Microsoft Docs
description: Azure-beli virtuális gép hibáinak elhárítása beágyazott virtualizálás használatával az Azure-ban
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 18d7e9b0ab44dfe18df0dcd7cd36fb708649a4bc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089684"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Azure-beli virtuális gép hibáinak elhárítása beágyazott virtualizálás használatával az Azure-ban

Ez a cikk bemutatja, hogyan hozhat létre beágyazott virtualizációs környezetet a Microsoft Azureban, így hibaelhárítási célból csatlakoztathatja a problémát okozó virtuális gép lemezét a Hyper-V-gazdagépen (mentő virtuális gépen).

## <a name="prerequisites"></a>Előfeltételek

A probléma virtuális gép csatlakoztatásához a mentési virtuális gépnek meg kell felelnie a következő előfeltételeknek:

-   A mentési virtuális gépnek a probléma virtuális géppel azonos helyen kell lennie.

-   A mentési virtuális gépnek ugyanabban az erőforráscsoporthoz kell tartoznia, mint a probléma virtuális gépnek.

-   A mentési virtuális gépnek ugyanolyan típusú Storage-fiókot (standard vagy prémium) kell használnia, mint a probléma virtuális gépe.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>1\. lépés: Mentési virtuális gép létrehozása és Hyper-V szerepkör telepítése

1.  Hozzon létre egy új mentési virtuális gépet:

    -  Operációs rendszer: Windows Server 2016 Datacenter

    -  Méret: Bármely v3-sorozat legalább két, beágyazott virtualizációt támogató maggal. További információ: [az új Dv3 és a EV3 VM-méretek bemutatása](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Ugyanaz a hely, a Storage-fiók és az erőforráscsoport, mint a probléma virtuális gép.

    -  Válassza ki ugyanazt a tárolási típust, mint a probléma virtuális gép (standard vagy prémium).

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

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>2\. lépés: A probléma virtuális gép létrehozása a mentési virtuális gép Hyper-V-kiszolgálóján

1.  Jegyezze fel a lemez nevét a probléma virtuális gépen, majd törölje a problémát okozó virtuális gépet. Győződjön meg arról, hogy megtartja az összes csatlakoztatott lemezt. 

2.  Csatlakoztassa a problémát okozó virtuális gép operációsrendszer-lemezét a mentési virtuális gép adatlemezéhez.

    1.  A probléma a virtuális gép törlése után lépjen a mentési virtuális gépre.

    2.  Válassza a **lemezek**lehetőséget, majd **adja hozzá az**adatlemezt.

    3.  Válassza ki a probléma virtuális gép lemezét, majd kattintson a **Mentés**gombra.

3.  A lemez sikeres csatolása után a távoli asztal a mentési virtuális géphez.

4.  Nyissa meg a Lemezkezelés (diskmgmt. msc) parancsot. Győződjön meg arról, hogy a probléma virtuális gépe lemeze **Offline**értékre van állítva.

5.  A Hyper-V kezelőjének megnyitása: A **Kiszolgálókezelőben**válassza ki a **Hyper-V szerepkört**. Kattintson a jobb gombbal a kiszolgálóra, majd válassza ki a **Hyper-V kezelőjét**.

6.  A Hyper-V kezelőjében kattintson a jobb gombbal a mentő virtuális gépre, majd válassza az **új** > **virtuális gép** > **tovább**lehetőséget.

7.  Adja meg a virtuális gép nevét, majd kattintson a **tovább**gombra.

8.  Válassza az **1. generáció**lehetőséget.

9.  Állítsa be az indítási memóriát 1024 MB-nál nagyobb értékre.

10. Ha alkalmazható, válassza ki a létrehozott Hyper-V hálózati kapcsolót. Másik lépés a következő lapra.

11. Válassza **a virtuális merevlemez csatlakoztatása később**lehetőséget.

    ![a virtuális merevlemez csatolása később lehetőséggel kapcsolatos rendszerkép](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. A virtuális gép létrehozásakor válassza a **Befejezés** lehetőséget.

13. Kattintson a jobb gombbal a létrehozott virtuális gépre, majd válassza a **Beállítások**lehetőséget.

14. Válassza az **ide-vezérlő 0**lehetőséget, válassza a **merevlemez**lehetőséget, majd kattintson a **Hozzáadás**gombra.

    ![az új merevlemez-meghajtó hozzáadására szolgáló rendszerkép](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. A **fizikai merevlemez**területen válassza ki az Azure-beli virtuális géphez csatlakoztatott probléma virtuális gépe lemezét. Ha nem látja a felsorolt lemezeket, ellenőrizze, hogy a lemez offline értékre van-e állítva a Lemezkezelés eszköz használatával.

    ![a lemez csatlakoztatására szolgáló rendszerkép](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. Válassza az **Apply** (Alkalmaz) lehetőséget, majd kattintson az **OK** gombra.

18. Kattintson duplán a virtuális gépre, majd indítsa el.

19. Most már dolgozhat a virtuális gépen a helyszíni virtuális gép használatával. Követheti a szükséges hibaelhárítási lépéseket.

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>3\. lépés: Azure-beli virtuális gép újbóli létrehozása az Azure-ban

1.  Miután a virtuális gép ismét online állapotba került, állítsa le a virtuális gépet a Hyper-V kezelőjében.

2.  Nyissa meg a [Azure Portal](https://portal.azure.com) , és válassza ki a helyreállító virtuális gép > lemezeket, és másolja a lemez nevét. A következő lépésben a nevet fogja használni. Válassza le a rögzített lemezt a mentési virtuális gépről.

3.  Nyissa meg az **összes erőforrást**, keresse meg a lemez nevét, majd válassza ki a lemezt.

     ![a lemez keresésének képe](media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. Kattintson a **virtuális gép létrehozása**elemre.

     ![a virtuális gépet a lemezről létrehozó lemezkép](media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

Azure PowerShell használatával is létrehozhatja a virtuális gépet a lemezről. További információ: [az új virtuális gép létrehozása meglévő lemezről a PowerShell használatával](../windows/create-vm-specialized.md#create-the-new-vm). 

## <a name="next-steps"></a>További lépések

Ha problémába ütközik a virtuális géphez való csatlakozással kapcsolatban, tekintse meg [az RDP-kapcsolatok hibaelhárítása Azure-beli virtuális géppel](troubleshoot-rdp-connection.md)című témakört. A virtuális gépen futó alkalmazások elérésével kapcsolatos problémákért lásd: az [alkalmazások kapcsolódási problémáinak elhárítása Windows rendszerű virtuális gépen](troubleshoot-app-connection.md).
