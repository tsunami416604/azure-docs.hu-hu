---
title: "Rögzítsen egy rendszerképet az Azure Windows virtuális gép |} Microsoft Docs"
description: "Hagyományos módon üzembe helyezett Azure -beli windowsos virtuális gép lemezképének létrehozása."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: a5986eac-4cf3-40bd-9b79-7c811806b880
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 6f7d8e8b3d1ed7c8a06f7e11fea492eec8b60409
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2018
---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Hagyományos módon üzembe helyezett Azure -beli windowsos virtuális gép lemezképének létrehozása.
> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Erőforrás-kezelő modell információkért lásd: [egy általánosított virtuális Gépet az Azure-ban a felügyelt lemezképének](../capture-image-resource.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Ez a cikk bemutatja, hogy használhassa az képként más virtuális gépek létrehozásához a Windows rendszert futtató Azure virtuális gép rögzítése. Ez a rendszerkép tartalmazza az operációsrendszer-lemez és a virtuális géphez csatolt adatok lemezzel. Hálózati konfigurációk, ezért szüksége hálózati konfigurációk beállításához, a más virtuális gépek, amelyek a lemezkép létrehozásakor nem tartoznak bele.

Azure tárolja a **Virtuálisgép-rendszerképek (klasszikus)**, egy **számítási** szolgáltatás, amely szerepel a listában az összes Azure-szolgáltatások megtekintésekor. Ez az összes feltöltött lemezképet tároló ugyanazon a helyen. Lemezképek kapcsolatos részletekért lásd: [kapcsolatos képek a virtuális gépek](about-images.md?toc=%2fazure%2fvirtual-machines%2fWindows%2fclassic%2ftoc.json).

## <a name="before-you-begin"></a>Előkészületek
Ezek a lépések feltételezik, hogy korábban már létrehozott Azure virtuális gép és konfigurálva az operációs rendszer, beleértve a bármely adatlemezt csatolni. Ha még nem meg, tekintse meg a következő cikkekben talál létrehozására, illetve a virtuális gép előkészítése:

* [Virtuális gép létrehozása lemezkép](createportal.md)
* [Hogyan lehet adatlemezt csatolni egy virtuális géphez](attach-disk.md)
* Győződjön meg arról, hogy a kiszolgálói szerepkörök támogatottak a Sysprep használatával. További információkért lásd: [Sysprep támogatási kiszolgálói szerepköre tekintetében](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!WARNING]
> Ez a folyamat törli az eredeti virtuális gép a rögzítése után.
>
>

Egy lemezkép az Azure virtuális gép rögzítése, előtt ajánlott a cél virtuális gép készül. Az Azure virtuális gépek Azure Backup segítségével készíthető. További részletek: [Azure-beli virtuális gépek biztonsági mentése](../../../backup/backup-azure-arm-vms.md). Hivatalos partnereinktől egyéb megoldások is elérhetők. A jelenleg elérhető szolgáltatásokat keresse az Azure Marketplace-en.

## <a name="capture-the-virtual-machine"></a>A virtuális gép rögzítése
1. Az a [Azure-portálon](http://portal.azure.com), **Connect** a virtuális géphez. Útmutatásért lásd: [hogyan lehet bejelentkezni a Windows Server rendszerű virtuális gép][How to sign in to a virtual machine running Windows Server].
2. Nyisson meg egy parancssori ablakot rendszergazdaként.
3. Lépjen be `%windir%\system32\sysprep`, majd futtassa a sysprep.exe.
4. A **rendszer-előkészítő eszköz** párbeszédpanel jelenik meg. Tegye a következőket:

   * A **Rendszerkarbantartási művelet**, jelölje be **adja meg a rendszer Out-of-Box élmény (OOBE)** , és győződjön meg arról, hogy **Generalize** be van jelölve. A Sysprep használatával kapcsolatos további információkért lásd: [hogyan használja a Sysprep: Bevezetés][How to Use Sysprep: An Introduction].
   * A **leállítási beállítások**, jelölje be **leállítási**.
   * Kattintson az **OK** gombra.

   ![Futtassa a Sysprep eszközt](./media/capture-image/SysprepGeneral.png)
5. A Sysprep leállítja a virtuális gépet, mely az Azure portálon, hogy a virtuális gép állapotának módosítása **leállítva**.
6. Az Azure portálon kattintson **virtuális gépek (klasszikus)** , és válassza ki a felvenni kívánt virtuális gépet. A **Virtuálisgép-rendszerképek (klasszikus)** csoport megtalálható-e **számítási** megtekintésekor **további szolgáltatások**.

7. A parancssávon kattintson **rögzítése**.

   ![Virtuális gép rögzítése](./media/capture-image/CaptureVM.png)

   A **rögzíteni a virtuális gépet** párbeszédpanel jelenik meg.

8. A **lemezképnév**, írja be az új lemezkép nevét. A **lemezképcímke**, írja be az új lemezkép címkéjét.

9. Kattintson a **a virtuális gépen már futtattam a Sysprep**. Ezt a jelölőnégyzetet a műveletek a Sysprep 3-5 lépésben hivatkozik. A képfájl _kell_ általánosítást a Sysprep futtatása előtt a Windows Server-lemezkép hozzáadása az egyéni lemezképek készlete alapján.

10. Miután befejeződött a rögzítést, az új lemezkép elérhetővé válik a **piactér**, a a **számítási**, **Virtuálisgép-rendszerképek (klasszikus)** tároló.

    ![Lemezkép-rögzítési sikeres](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>További lépések
A lemezkép készen áll a virtuális gépek létrehozásához használható. Ennek beállításához hozzon létre egy virtuális gép kiválasztásával a **további szolgáltatások** menüpont a szolgáltatások menü alján **Virtuálisgép-rendszerképek (klasszikus)** a a **számítási** csoport. Útmutatásért lásd: [virtuális gép létrehozása lemezkép](createportal.md).

[How to sign in to a virtual machine running Windows Server]:connect-logon.md
[How to Use Sysprep: An Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
