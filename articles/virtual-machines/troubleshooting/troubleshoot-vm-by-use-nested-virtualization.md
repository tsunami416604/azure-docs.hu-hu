---
title: A beágyazott virtualizálás az Azure-ban az Azure virtuális gép hiba elhárítása |} A Microsoft Docs
description: Hogyan háríthatók el a probléma az Azure virtuális gép használatával a beágyazott virtualizálás az Azure-ban
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/06/2018
ms.author: genli
ms.openlocfilehash: 36c42830738f160b24f65e86576ba93aac1ea737
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414155"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>A beágyazott virtualizálás az Azure-ban az Azure virtuális gép hiba elhárítása

Ez a cikk bemutatja, hogyan hozhat létre egy beágyazott virtualizálási környezet a Microsoft Azure-ban, így akkor is csatlakoztathatja a lemezt a virtuális gép hibát a Hyper-V gazdagép (VM-mentési) hibaelhárítás céljából.

## <a name="prerequisites"></a>Előfeltételek

Csatlakoztatja a hibás virtuális Gépnek, a mentési virtuális Gépet a következő előfeltételeknek kell megfelelnie:

-   A mentési virtuális gép ugyanazon a helyen, mint a hibás virtuális Gépnek kell lennie.

-   A mentési VM ugyanazt az erőforráscsoportot, a hibás virtuális Gépnek kell lennie.

-   A mentési VM ugyanolyan típusú (Standard vagy prémium szintű) Tárfiók kell használnia, mint a virtuális gép hibás.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>1. lépés: Mentő virtuális gép létrehozása és Hyper-V szerepkör telepítése

1.  Hozzon létre egy új mentési virtuális Gépet:

    -  Operációs rendszer: Windows Server 2016 Datacenter

    -  Méret: Bármely v3-as sorozat legalább két mag, hogy támogatja a beágyazott virtualizálás. További információkért lásd: [bemutatjuk az új Dv3 és Ev3 Virtuálisgép-méretek](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Azonos helyen, Tárfiók, és erőforráscsoporthoz tartozik, mint a hibás virtuális Gépnek.

    -  Válasszon azonos tárolási módot, a hibás virtuális Gépnek (Standard vagy prémium).

2.  A készenléti Virtuálisgép követő jön létre, a mentési virtuális géphez a távoli asztal.

3.  A Kiszolgálókezelőben válasza **kezelés** > **szerepkörök és szolgáltatások hozzáadása**.

4.  Az a **telepítési típus** szakaszban jelölje be **szerepköralapú vagy szolgáltatásalapú telepítés**.

5.  Az a **célkiszolgáló kijelölése** területen győződjön meg arról, hogy a mentési virtuális gép el van-e kiválasztva.

6.  Válassza ki a **Hyper-V szerepkör** > **szolgáltatások hozzáadása**.

7.  Válassza ki **tovább** a a **funkciók** szakaszban.

8.  Ha egy virtuális kapcsoló nem érhető el, kattintson rá. Ellenkező esetben válasszon **tovább**.

9.  Az a **áttelepítési** szakaszban jelölje be **tovább**

10. Az a **alapértelmezett tárak** szakaszban jelölje be **tovább**.

11. Jelölje be a jelölőnégyzetet, hogy szükség esetén automatikusan újraindítja a kiszolgálót.

12. Válassza az **Install** (Telepítés) lehetőséget.

13. Lehetővé teszi a Hyper-V szerepkör telepítése a kiszolgálón. Ez eltarthat néhány percig, és a kiszolgáló automatikusan újraindul.

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>2. lépés: A hibás virtuális Gépnek a mentési virtuális gép Hyper-V kiszolgálón hozzon létre.

1.  Rögzítse a hibás virtuális Gépnek a lemez nevét, és ezután törölje a hibás virtuális Gépnek. Győződjön meg arról, hogy őrizze meg az összes csatlakoztatott lemezek. 

2.  A problémát a virtuális gép operációsrendszer-lemezének csatolása a virtuális gép mentési adatlemezként.

    1.  Miután a virtuális gép törlését a probléma látogasson el a készenléti Virtuálisgép.

    2.  Válassza ki **lemezek**, majd **Hozzáadás adatlemez**.

    3.  A probléma a virtuális gép lemezét, majd válassza ki és **mentése**.

3.  Miután a lemez sikeresen a mentési virtuális géphez csatolt, távoli asztali.

4.  Nyissa meg a Lemezkezelés (diskmgmt.msc). Győződjön meg arról, hogy a probléma a lemez virtuális gép van beállítva, **Offline**.

5.  Nyissa meg a Hyper-V kezelőjével: A **Kiszolgálókezelő**, jelölje be a **Hyper-V szerepkör**. Kattintson a jobb gombbal a kiszolgáló, és válassza a **Hyper-V Manager**.

6.  A Hyper-V kezelőjében kattintson a jobb gombbal a mentési virtuális Gépet, és válassza **új** > **virtuális gép** > **tovább**.

7.  Adja meg a virtuális gép nevét, és válassza **tovább**.

8.  Válassza ki **1. generációs**.

9.  Az indítási memóriát 1024 MB vagy annál beállítani.

10. Ha van ilyen válassza ki a Hyper-V hálózati kapcsoló, amely lett létrehozva. Ellenkező esetben helyezze át a következő lapra.

11. Válassza ki **egy virtuális merevlemez csatlakoztatása később**.

    ![a csatolás egy újabb virtuális merevlemez lehetőséget bemutató kép](/media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. Válassza ki **Befejezés** a virtuális gép létrehozásakor.

13. Kattintson a jobb gombbal a virtuális gép által létrehozott, és válassza ki **beállítások**.

14. Válassza ki **IDE-vezérlő 0**válassza **merevlemez**, és kattintson a **Hozzáadás**.

    ![új rögzített meghajtót ad hozzá a bemutató kép](/media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. A **fizikai merevlemez**, jelölje ki a lemezt a virtuális Gépet, amely az Azure virtuális géphez csatolt probléma. Ha nem látja az összes felsorolt lemezt, ellenőrizze, ha a lemez értéke offline Lemezkezelés használatával.

    ![a csatlakoztatja a lemezt bemutató kép](/media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. Válassza az **Apply** (Alkalmaz) lehetőséget, majd kattintson az **OK** gombra.

18. Kattintson duplán a virtuális gépen, és indítsa el azt.

19. Most már használhatja a virtuális gépen a helyszíni virtuális gépként. Sikerült hajtsa végre a szükséges hibaelhárítási lépéseket.

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>3. lépés: Hozzon létre újra az Azure virtuális Gépen az Azure-ban

1.  Online állapotba a virtuális gép kap, miután állítsa le a virtuális gép a Hyper-V kezelőjében.

2.  Nyissa meg a [az Azure portal](https://portal.azure.com) , és válassza ki a mentési virtuális gép > lemezek, másolja a lemez neve. A név a következő lépésben fogja használni. Válassza le a rögzített méretű lemezt a mentési virtuális gépről.

3.  Lépjen a **összes erőforrás**, keresse meg a lemez neve, és válassza ki a lemezt.

     ![bemutató kép megkeresi a lemezen](/media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. Kattintson a **virtuális gép létrehozása**.

     ![bemutató kép virtuális gépet hoz létre a lemezről](./media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

Azure PowerShell használatával a virtuális gép létrehozása a lemezről. További információkért lásd: [az új virtuális gép létrehozása egy meglévő lemezről a PowerShell használatával](../windows/create-vm-specialized.md#create-the-new-vm). 

## <a name="next-steps"></a>További lépések

Ha a virtuális Géphez való csatlakozással kapcsolatos problémákat tapasztal, tekintse meg [hibaelhárítása RDP-kapcsolatai egy Azure virtuális gép](troubleshoot-rdp-connection.md). A virtuális Gépen futó alkalmazások elérésével kapcsolatos problémák, lásd: [a Windows virtuális gép alkalmazások csatlakozási hibáinak elhárítása](troubleshoot-app-connection.md).
