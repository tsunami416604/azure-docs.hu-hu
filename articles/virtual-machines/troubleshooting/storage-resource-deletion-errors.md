---
title: A tárolási erőforrások törlésével kapcsolatos hibák elhárítása Linux rendszerű virtuális gépeken az Azure-ban | Microsoft Docs
description: A csatlakoztatott virtuális merevlemezeket tartalmazó tárolási erőforrások törlésekor felmerülő problémák elhárítása.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "71058152"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Tárolási erőforrások törlésével kapcsolatos hibák elhárítása

Bizonyos esetekben előfordulhat, hogy a következő hibák valamelyikét tapasztalja, amikor egy Azure Resource Manager üzemelő példányban próbál törölni egy Azure Storage-fiókot, tárolót vagy blobot:

> **Nem sikerült törölni a (z) StorageAccountName Storage-fiókot. Hiba: a Storage-fiók nem törölhető, mert az összetevői használatban vannak.**
> 
> **Nem sikerült törölni a (z) # tárolók száma: <br> virtuális merevlemezek: a tárolón jelenleg bérlet található, és a kérelemben nincs megadva címbérlet-azonosító.**
> 
> **A # nem sikerült törölni a # Blobok: <br> BlobName. vhd fájlt: jelenleg van bérlet a blobon, és nincs megadva címbérlet-azonosító a kérelemben.**

Az Azure-beli virtuális gépeken használt virtuális merevlemezek az Azure standard vagy Premium Storage-fiókjában blobként tárolt VHD-fájlok. További információ az Azure-lemezekről: [Bevezetés a Managed Disks](../linux/managed-disks-overview.md)szolgáltatásba.

Az Azure megakadályozza a virtuális gépekhez csatolt lemezek törlését a sérülés megelőzése érdekében. Emellett megakadályozza a tárolók és a virtuális gépekhez csatolt oldal Blobok törlését. 

A Storage-fiók, tároló vagy blob törlésének folyamata a következő hibák valamelyikének fogadásakor: 
1. Virtuális géphez csatolt Blobok azonosítása
2. [Csatlakoztatott **operációsrendszer-lemezzel** rendelkező virtuális gépek törlése](#step-2-delete-vm-to-detach-os-disk)
3. [Az összes **adatlemez (ek)** leválasztása a fennmaradó virtuális gépekről](#step-3-detach-data-disk-from-the-vm)

A lépések végrehajtása után próbálja meg újra törölni a Storage-fiókot, a tárolót vagy a blobot.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>1. lépés: a virtuális géphez csatolt blob azonosítása

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>1. forgatókönyv: blob törlése – csatlakoztatott virtuális gép azonosítása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központi menüben válassza a **Minden erőforrás** elemet. Nyissa meg a Storage-fiókot a **blob Service** elemnél válassza a **tárolók**lehetőséget, és keresse meg a törölni kívánt blobot.
3. Ha a blob **címbérleti állapota** **bérletbe**kerül, kattintson a jobb gombbal, majd válassza a **metaadatok szerkesztése** lehetőséget a blob metaadatainak panel megnyitásához. 

    ![Képernyőkép a portálról, a Storage-fiók blobok és a jobb gombbal a "metaadatok szerkesztése" elemre > kattintva kiemelve](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. A blob metaadatainak ablaktáblájában tekintse meg és jegyezze fel **MicrosoftAzureCompute_VMName**értékét. Ez az érték annak a virtuális gépnek a neve, amelyhez a VHD csatolva van. (Lásd a **fontos** , ha a mező nem létezik)
5. A blob metaadatainak ablaktáblájában tekintse meg és jegyezze fel **MicrosoftAzureCompute_DiskType**értékét. Ez az érték határozza meg, hogy a csatlakoztatott lemez operációs rendszer vagy adatlemez-e (lásd: **fontos** , ha ez a mező nem létezik). 

     ![Képernyőkép a portálról, amelyen a "blob metadata" panel nyílik meg.](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Ha a blob-lemez típusa **OSDisk** , kövesse a [2. lépés: a virtuális gép törlése az operációsrendszer-lemez leválasztásához](#step-2-delete-vm-to-detach-os-disk). Ellenkező esetben, ha a blob lemez típusa **adatlemez** , kövesse a [3. lépés: adatlemez leválasztása a virtuális](#step-3-detach-data-disk-from-the-vm)gépről című témakör lépéseit. 

> [!IMPORTANT]
> Ha **MicrosoftAzureCompute_VMName** és **MicrosoftAzureCompute_DiskType** nem jelenik meg a blob metaadataiban, az azt jelzi, hogy a blob explicit módon van bérletben, és nincs csatlakoztatva a virtuális géphez. A bérelt Blobok nem törölhetők a bérlet első megszakítása nélkül. A bérlet megszakításához kattintson a jobb gombbal a blobra, majd válassza a **bérlet megszüntetése**lehetőséget. A virtuális géphez nem csatolt bérelt Blobok megakadályozzák a blob törlését, de nem akadályozzák meg a tároló vagy a Storage-fiók törlését.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>2. forgatókönyv: tároló törlése – a virtuális gépekhez csatlakoztatott tárolóban található összes blob azonosítása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központi menüben válassza a **Minden erőforrás** elemet. Nyissa meg a Storage-fiókot a **blob Service** - **tárolók**területen, és keresse meg a törölni kívánt tárolót.
3. Kattintson ide a tároló megnyitásához és a benne található Blobok listájának megjelenítéséhez. Azonosítsa az összes blobot a blob Type = **Page blob** **és a bérlet állapota = a** listából. Kövesse az 1. forgatókönyvet az egyes blobokhoz társított virtuális gép azonosításához.

    ![Képernyőkép a portálról, a Storage-fiók Blobokkal és a "bérlet állapota" értékkel kiemelve](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Kövesse a [2. lépést](#step-2-delete-vm-to-detach-os-disk) és a 3. [lépést](#step-3-detach-data-disk-from-the-vm) a virtuális gép (ek) **OSDisk** és leválasztási **adatlemez**való törléséhez. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>3. forgatókönyv: Storage-fiók törlése – a virtuális gépekhez csatolt, a Storage-fiókban található összes blob (ok) azonosítása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központi menüben válassza a **Minden erőforrás** elemet. Nyissa meg a Storage-fiókot a **blob Service** alatt, **majd válassza a Blobok**elemet.
3. A **tárolók** ablaktáblán azonosítsa az összes olyan tárolót, amelyben a **címbérleti állapotot** **bérbe** adja, és kövesse a [2. forgatókönyvet](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) az egyes **bérelt** tárolók esetében
4. Kövesse a [2. lépést](#step-2-delete-vm-to-detach-os-disk) és a 3. [lépést](#step-3-detach-data-disk-from-the-vm) a virtuális gép (ek) **OSDisk** és leválasztási **adatlemez**való törléséhez. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>2. lépés: a virtuális gép törlése az operációsrendszer-lemez leválasztásához
Ha a VHD egy operációsrendszer-lemez, törölnie kell a virtuális gépet, mielőtt törölni lehetne a csatolt VHD-t. A következő lépések végrehajtása után nincs szükség további műveletre az ugyanahhoz a virtuális géphez csatolt adatlemezek esetén:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központi menüben válassza a **Virtual Machines**lehetőséget.
3. Válassza ki azt a virtuális gépet, amelyhez a VHD csatolva van.
4. Ügyeljen arra, hogy a virtuális gép ne legyen aktívan használatban, és hogy már nincs szüksége a virtuális gépre.
5. A **virtuális gép részletei** ablaktábla tetején válassza a **Törlés**lehetőséget, majd kattintson az **Igen** gombra a megerősítéshez.
6. A virtuális gépet törölni kell, de a VHD-t meg lehet őrizni. A VHD-t azonban már nem lehet virtuális géphez csatlakoztatni, vagy bérlettel kell rendelkeznie. A bérlet felszabadítása néhány percet is igénybe vehet. A bérlet felszabadításának ellenőrzéséhez keresse meg a blob helyét, és a **blob tulajdonságai** panelen **elérhetővé**kell tennie a **címbérlet állapotát** .

## <a name="step-3-detach-data-disk-from-the-vm"></a>3. lépés: adatlemez leválasztása a virtuális gépről
Ha a VHD adatlemez, válassza le a virtuális MEREVLEMEZt a virtuális gépről a bérlet eltávolításához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központi menüben válassza a **Virtual Machines**lehetőséget.
3. Válassza ki azt a virtuális gépet, amelyhez a VHD csatolva van.
4. Válassza a **lemezek** elemet a **virtuális gép részletei** ablaktáblán.
5. Válassza ki a törölni kívánt adatlemezt, amelyhez a VHD csatolva van. A virtuális merevlemez URL-címének ellenőrzésével meghatározhatja, hogy melyik blob van csatlakoztatva a lemezhez.
6. A blob helyének ellenőrzéséhez kattintson a lemezre, és ellenőrizze az elérési utat a **VHD URI** mezőben.
7. Válassza a **Szerkesztés** lehetőséget a **lemezek** panel tetején.
8. Kattintson a törölni kívánt adatlemez **leválasztási ikonja** elemre.

     ![Képernyőkép a portálról, amelyen a "blob metadata" panel nyílik meg.](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Kattintson a **Mentés** gombra. A lemez most már le van választva a virtuális gépről, és a virtuális merevlemez már nincs bérletben. A bérlet felszabadítása néhány percet is igénybe vehet. Annak ellenőrzéséhez, hogy a bérlet megjelent-e, keresse meg a blob helyét, és a **blob tulajdonságai** ablaktáblán a **címbérlet állapota** értékének **zárolása** vagy **elérhetőnek**kell lennie.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

