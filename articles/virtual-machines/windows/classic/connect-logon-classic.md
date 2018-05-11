---
title: Jelentkezzen be a klasszikus Azure virtuális gépek |} Microsoft Docs
description: Az Azure portál használatával jelentkezzen be a klasszikus üzembe helyezési modellel létrehozott Windows virtuális gépek.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 87ecc65d2d4802ae826f3260b66b26e0bbe414e6
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Bejelentkezés windowsos virtuális gépre az Azure Portal használatával
Az Azure portálon, használja a **Connect** gombra kattintva indítsa el a távoli asztali munkamenet, és jelentkezzen be a Windows virtuális gépek.

Végrehajtja a Linux virtuális gépek csatlakozni? Lásd: [Linuxot futtató virtuális gép bejelentkezés](../../linux/mac-create-ssh-keys.md).

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Jelentkezzen be a virtuális gépek erőforrás-kezelő modellel kapcsolatos további információkért lásd: [Itt](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez
1. Jelentkezzen be az Azure portálra.
2. Kattintson a virtuális gépen, amelyet el szeretne érni. A szerepel-e a **összes erőforrás** ablaktáblán.

    ![Virtuális-machine-helyek](./media/connect-logon/azureportaldashboard.png)

1. Kattintson a **Connect** gombra a virtuális gép tulajdonságok lapján. 
2. Az a **kapcsolódás a virtuális gép** lapon hagyja válassza ki a megfelelő beállításokat, majd kattintson **letöltése RDP-fájl**.
2. Nyissa meg a letöltött RDP-fájlt, és kattintson a **Connect** megjelenésekor. 
2. Egy figyelmeztetés fog megjelenni arról, hogy az `.rdp`-fájl közzétevője ismeretlen. Ez nem jelent problémát. A Távoli asztal ablakában kattintson a **Csatlakozás** gombra a folytatáshoz.
   
    ![Képernyőkép az ismeretlen közzétevőre vonatkozó figyelmeztetésről.](./media/connect-logon/rdp-warn.png)
3. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a virtuális gép egy fiókjának a hitelesítő adatait, majd kattintson az **OK** gombra.
   
     **Helyi fiók** – ez általában a helyi fiók felhasználójának neve és jelszava, amelyet a virtuális gép létrehozásakor adott meg. Ebben az esetben a tartomány a virtuális gép neve, és a következő módon van megadva: *vmname*&#92;*username*.  
   
    **Tartományhoz csatlakoztatott virtuális gép** – ha a virtuális gép egy tartományhoz tartozik, a felhasználó nevét a következő formátumban adja meg: *Domain*&amp;#92;*Username*. A fióknak szerepelnie kell a Rendszergazdák csoportban, vagy távoli hozzáférési jogosultságokkal kell rendelkeznie a virtuális géphez.
   
    **Tartományvezérlő** – ha a virtuális gép egy tartományvezérlő, írja be egy, a tartományhoz tartozó tartományi rendszergazdai fiók felhasználónevét és jelszavát.
4. Kattintson az **Igen** gombra a virtuális gép identitásának ellenőrzéséhez és a bejelentkezés befejezéséhez.
   
   ![Képernyőkép a virtuális gép identitásának ellenőrzésére vonatkozó üzenetről.](./media/connect-logon/cert-warning.png)

## <a name="next-steps"></a>További lépések
* Ha a **Connect** gomb inaktív vagy egyéb problémák a távoli asztali kapcsolattal, vagy próbálja meg visszaállítani a konfiguráció. Kattintson a **távelérés alaphelyzetbe állítása** a virtuális gép irányítópulton.

    ![Reset-remote-access](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* A jelszó kapcsolatos problémák próbálkozzon a visszaállításával. Kattintson a **jelszó-átállítási** mentén a bal oldali széle virtuális gép irányítópult, a **támogatási + hibaelhárítás**.

    ![Jelszó alaphelyzetbe állítása](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

Ha ezek a tippek nem működnek, vagy nem kapcsolódnak, mire van szüksége, tekintse meg [távoli asztali kapcsolatainak hibaelhárítása a Windows-alapú Azure virtuális gép](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ez a cikk útmutatást nyújt a gyakori problémák diagnosztizálásához és elhárításához.
