---
title: Tárolási erőforrások törlésével kapcsolatos hibák elhárítása linuxos virtuális gépeken az Azure-ban | Microsoft dokumentumok
description: A csatlakoztatott virtuális gépeket tartalmazó tárolóerőforrások törlésekor felmerülő problémák elhárítása.
keywords: ''
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 50ab4b0f1e676ffcba0ce69ab6aa957e4c77ab88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058152"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Tárolási erőforrás törlési hibáinak elhárítása

Bizonyos esetekben előfordulhat, hogy az alábbi hibák valamelyike fordul elő, miközben egy Azure Resource Manager-telepítésben próbál törölni egy Azure-tárfiókot, -tárolót vagy -blobot:

> **Nem sikerült törölni a "StorageAccountName" tárfiókot. Hiba: A tárfiók nem törölhető, mert a műtermékek használatban vannak.**
> 
> **Nem sikerült törölni # a #<br>container(s): vhds: Jelenleg van egy címbérlet a tárolón, és nincs címbérlet-azonosító megadva a kérelemben.**
> 
> **Nem sikerült törölni # ki #<br>blobok: BlobName.vhd: Jelenleg van egy bérlet a blob, és nincs címbérlet-azonosító megadva a kérelemben.**

Az Azure virtuális gépekben használt virtuális merevlemezek .vhd fájlok, amelyek et az Azure-ban egy szabványos vagy prémium szintű tárfiókban lapblobként tárolnak. Az Azure-lemezekről további információt a Felügyelt lemezek – bevezetés című témakörben [talál.](../linux/managed-disks-overview.md)

Az Azure megakadályozza a virtuális géphez csatlakoztatott lemez törlését a sérülés megelőzése érdekében. Azt is megakadályozza, hogy a tárolók és a tárfiókok, amelyek egy virtuális géphez csatlakoztatott lapblobot tartalmazó tárolók és tárfiókok törlését. 

A tárfiók, tároló vagy blob törlésének folyamata a következő hibák fogadásakor: 
1. Virtuális géphez csatolt blobok azonosítása
2. [Csatlakoztatott **operációsrendszer-lemezzel** rendelkező virtuális gépek törlése](#step-2-delete-vm-to-detach-os-disk)
3. [Az összes **adatlemez (ek) leválasztása** a fennmaradó virtuális gép(ek)ről](#step-3-detach-data-disk-from-the-vm)

Próbálja meg újra a tárfiók, tároló vagy blob törlése után ezeket a lépéseket.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>1. lépés: A virtuális géphez csatolt blob azonosítása

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>1. forgatókönyv: Blob törlése – a csatlakoztatott virtuális gép azonosítása
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A Központ menüben válassza az **Összes erőforrás lehetőséget.** Nyissa meg a tárfiókot, a **Blob Service** csoportban válassza **a tárolók**, és keresse meg a blob törölni.
3. Ha a blob **bérleti állapota** **bérelt,** majd kattintson a jobb gombbal, és válassza **a Metaadatok szerkesztése** a Blob metaadat-ablaktábla megnyitásához. 

    ![Képernyőkép a portálról, amelyen a Tárfiók blobjai és a jobb gombbal > "Metaadatok szerkesztése" kiemelve](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. A Blob metaadat-ablaktáblájában ellenőrizze és rögzítse a **MicrosoftAzureCompute_VMName**értékét. Ez az érték annak a virtuális gépnek a neve, amelyhez a virtuális merevlemez kapcsolódik. (Lásd **fontos,** ha ez a mező nem létezik)
5. A Blob metaadat-ablaktáblájában ellenőrizze és rögzítse a **MicrosoftAzureCompute_DiskType**értékét. Ez az érték azonosítja, ha a csatolt lemez operációs rendszer vagy adatlemez (lásd **a fontos,** ha ez a mező nem létezik). 

     ![Képernyőkép a portálról, amelyen a "Blob Metaadatok" ablaktábla van megnyitva](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Ha a blob lemez típusa **OSDisk** kövesse [a 2.](#step-2-delete-vm-to-detach-os-disk) Ellenkező esetben, ha a blob lemez típusa **DataDisk** kövesse a [3.](#step-3-detach-data-disk-from-the-vm) 

> [!IMPORTANT]
> Ha **MicrosoftAzureCompute_VMName** és **MicrosoftAzureCompute_DiskType** nem jelennek meg a blob metaadatok, azt jelzi, hogy a blob explicit bérletben, és nem kapcsolódik a virtuális géphez. A bérelt blobok nem törölhetők a bérlet megszakadása nélkül. A bérlet megszakításához kattintson a jobb gombbal a blobra, és válassza a **Bérlet megszakítása parancsot.** A virtuális géphez nem csatolt bérelt blobok megakadályozzák a blob törlését, de nem akadályozzák meg a tároló vagy a tárfiók törlését.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>2. forgatókönyv: Tároló törlése – azonosítsa a virtuális gépekhez csatlakoztatott tárolón belüli összes blobot
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A Központ menüben válassza az **Összes erőforrás lehetőséget.** Nyissa meg a tárfiókot, a **Blob Service** csoportban válassza **a Tárolók**lehetőséget, és keresse meg a törölni kívánt tárolót.
3. Kattintson ide a tároló megnyitásához, és megjelenik a benne lévő blobok listája. Azonosítsa az összes blobot blobtípussal = **lapblobtal** és bérleti állapottal = ebből a listából **bérelt.** Kövesse az 1.

    ![Képernyőkép a portálról, a Storage-fiók blobjai és a "Lease State" a "Bérelt" kiemelve](./media/troubleshoot-vhds/utd-disks-sm.png)

4. [A 2.](#step-2-delete-vm-to-detach-os-disk) [Step 3](#step-3-detach-data-disk-from-the-vm) **OSDisk** **DataDisk** 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>3. forgatókönyv: Tárfiók törlése – a virtuális gépekhez csatlakoztatott összes blob(ok) azonosítása a tárfiókon belül
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A Központ menüben válassza az **Összes erőforrás lehetőséget.** Nyissa meg a tárfiókot, a **Blob Service** csoportban válassza a **Blobs**lehetőséget.
3. A **Tárolók** ablaktáblában azonosítsa az összes olyan **Leased** **tárolót,** ahol a **bérleti állapot** bérelt, és kövesse a [2.](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms)
4. [A 2.](#step-2-delete-vm-to-detach-os-disk) [Step 3](#step-3-detach-data-disk-from-the-vm) **OSDisk** **DataDisk** 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>2. lépés: Virtuális gép törlése az operációs rendszer lemeze i.
Ha a virtuális merevlemez egy operációs rendszer lemeze, törölnie kell a virtuális gép, mielőtt a csatlakoztatott virtuális merevlemez törölhető. A lépések elvégzése után az ugyanazon virtuális géphez csatlakoztatott adatlemezekhez nem lesz szükség további műveletekre:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A Központ menüben válassza a **Virtuális gépek**lehetőséget.
3. Válassza ki azt a virtuális gép, amelyhez a virtuális merevlemez csatlakozik.
4. Győződjön meg arról, hogy semmi sem használja aktívan a virtuális gépet, és hogy már nincs szüksége a virtuális gépre.
5. A **Virtuális gép részletei** ablaktábla tetején válassza a **Törlés**lehetőséget, majd a megerősítéshez kattintson az **Igen** gombra.
6. A virtuális gép törölni kell, de a virtuális merevlemez megtartható. Azonban a virtuális merevlemez már nem kell csatolni egy virtuális gép, vagy egy bérlet rajta. Eltarthat néhány percig, amíg a bérleti szerződés lejár. A bérlet felszabadításának ellenőrzéséhez keresse meg a blob helyét, és a **Blob-tulajdonságok** ablaktáblában a **címbérlet állapotának** **elérhetőnek**kell lennie.

## <a name="step-3-detach-data-disk-from-the-vm"></a>3. lépés: Adatlemez leválasztása a virtuális gépről
Ha a virtuális merevlemez adatlemez, válassza le a virtuális merevlemezt a virtuális gépről a címbérlet eltávolításához:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A Központ menüben válassza a **Virtuális gépek**lehetőséget.
3. Válassza ki azt a virtuális gép, amelyhez a virtuális merevlemez csatlakozik.
4. Válassza **a Lemezek lehetőséget** a **Virtuálisgép részletei** ablaktáblán.
5. Jelölje ki azt az adatlemezt, amelyhez a virtuális merevlemez csatolva van. A virtuális merevlemez URL-címének ellenőrzésével meghatározhatja, hogy melyik blob van csatlakoztatva a lemezhez.
6. A blob helyének ellenőrzéséhez kattintson a lemezre, hogy ellenőrizze az elérési utat a **VHD** URI-mezőben.
7. Válassza a **Szerkesztés** lehetőséget a **Lemezek** ablaktábla tetején.
8. Kattintson a törölni a törölni a törölni a törölni a törölni a törölni a törölni a lemez **leválasztási ikonjára.**

     ![Képernyőkép a portálról, amelyen a "Blob Metaadatok" ablaktábla van megnyitva](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Kattintson a **Mentés** gombra. A lemez most levan választva a virtuális gépről, és a virtuális merevlemez már nem bérelt. Eltarthat néhány percig, amíg a bérleti szerződés lejár. A címbérlet felszabadításának ellenőrzéséhez keresse meg a blob helyét, és a **Blob-tulajdonságok** ablaktáblában a **Címbérlet állapota** értéknek **fel oldva** vagy **elérhetőnek**kell lennie.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

