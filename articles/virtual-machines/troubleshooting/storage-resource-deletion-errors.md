---
title: Linux rendszerű virtuális gépek az Azure-ban a storage erőforrás törlésével kapcsolatos hibák elhárítása |} A Microsoft Docs
description: Problémák elhárításáról tárolási erőforrásokat tartalmazó csatlakoztatott virtuális merevlemezek törlésekor.
keywords: ''
services: virtual-machines
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 5ab0a9a92297c46a4090583d41f22f2035bd310c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816186"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Storage erőforrás törlésével kapcsolatos hibák elhárítása

Bizonyos esetekben merülhetnek fel az alábbi hibák, miközben a próbál egy Azure storage-fiók, tároló vagy egy Azure Resource Manager üzembe helyezése a blobok törlése:

>**Nem sikerült törölni a storage-fiók "StorageAccountName". Hiba: A tárfiók nem törölhető, mert az összetevői használatban.**

>**Nem sikerült törölni a # # tároló(k) mimo:<br>virtuális merevlemezek: Jelenleg a címbérlet a tárolót, és a kérésben megadott nincs bérlet azonosítója.**

>**Nem sikerült törölni a # # blobok mimo:<br>BlobName.vhd: Jelenleg a címbérlet a blob és a kérésben megadott nincs bérlet azonosítója.**

Az Azure-beli virtuális gépeken használt virtuális merevlemezek egy standard vagy prémium szintű storage-fiókot az Azure-ban lapblobként tárolt .vhd-fájlokat. Az Azure disks kapcsolatos további információkért lásd: [készül nem felügyelt és felügyelt lemezes tárolás a Microsoft Azure Linux rendszerű virtuális gépek](../linux/about-disks-and-vhds.md). 

Azure megakadályozza, hogy az egy lemezt, amely csatolva van egy virtuális gép meghibásodásának elkerülése érdekében törlését. Megakadályozza a tárolók és a storage-fiókok, amelyek egy lapblob egy virtuális géphez csatolt törlését is. 

A folyamat egy tárfiókot, tárolót vagy blobot törölni, ezek a hibák egyike fogadásakor van: 
1. Egy virtuális Géphez csatolt blobok azonosítása
2. [Törlés rendelkező virtuális gépeket csatolt **operációsrendszer-lemez**](#step-2-delete-vm-to-detach-os-disk)
3. [Válassza le az összes **adatok (eke) t** a többi virtuális gép](#step-3-detach-data-disk-from-the-vm)

Próbálja meg újra törölni a tárfiókot, tárolót vagy blobot, miután végrehajtotta ezeket a lépéseket.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>1. lépés: Azonosítsa a virtuális Géphez csatolt blob

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>1. forgatókönyv: Blobok – törlés csatlakoztatott virtuális gépek azonosítása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben válassza ki a **összes erőforrás**. Nyissa meg a storage-fiókban található **Blob Service** kiválasztása **tárolók**, és keresse meg a törölni kívánt blob.
3. Ha a blob **bérlet állapota** van **bérelt**, majd kattintson a jobb gombbal, és válassza ki **metaadatainak szerkesztése** Blob metaadatainak panel megnyitásához. 

    ![A portál képernyőképe a Storage-blobok fiókját, és kattintson a jobb gombbal > "Szerkesztése metaadatok" a kiemelésével](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. A Blob metaadatainak ablaktáblán ellenőrizze, és jegyezze fel az értéket a **MicrosoftAzureCompute_VMName**. Ez az érték az a név a virtuális gép által a virtuális merevlemez csatolva van. (Lásd: **fontos** Ha ez a mező nem létezik.)
5. A Blob metaadatainak ablaktáblán ellenőrizze, és jegyezze fel az értékét **MicrosoftAzureCompute_DiskType**. Ez az érték azonosítja a csatlakoztatott lemezek esetén a rendszer- és lemez (lásd: **fontos** Ha ez a mező nem létezik). 

     ![A portálon nyissa meg a storage "Blob metaadatainak" ablaktábla képernyőképe](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Ha a blob lemez **OSDisk** kövesse [2. lépés: Törölje a virtuális gép operációsrendszer-lemez leválasztásához](#step-2-delete-vm-to-detach-os-disk). Egyéb esetben, ha a blob lemez típusa **DataDisk** kövesse a [3. lépés: A virtuális gépről adatlemez leválasztása](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Ha **MicrosoftAzureCompute_VMName** és **MicrosoftAzureCompute_DiskType** nem jelennek meg a blob metaadatai, az azt jelzi, hogy a blob egy explicit módon bérelt, és a egy virtuális gép nincs csatlakoztatva. Bérelt blobok bérletének érvénytelenítése az első nélkül nem lehet törölni. Érvényteleníteni a bérletet, kattintson a jobb gombbal a blobot, és válassza ki **Break lease**. Bérelt a blobokat, amelyekre a virtuális gép nem csatlakozik a blob törlésének megakadályozása, de nem akadályozzák meg a tároló vagy a storage-fiók törlése.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>2. forgatókönyv: Egy tároló - törlése az összes BLOB tárolóból, amely a virtuális gépekhez csatolt azonosítása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben válassza ki a **összes erőforrás**. Nyissa meg a storage-fiókban található **Blob Service** kiválasztása **tárolók**, és keresse meg a tároló lehet törölni.
3. Ide kattintva nyissa meg a tárolót, és a benne lévő blobok listája jelenik meg. Azonosítsa a Blob típusa a blobok = **lapblob** és bérleti állapot = **bérelt** ebből a listából. Hajtsa végre az 1. forgatókönyv esetében az ilyen blobok társított virtuális gépet.

    ![Képernyőkép a portal, a Storage-fiók BLOB és a "bérleti állapot" a "Bérelt" kiemelésével](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Hajtsa végre a [2. lépés](#step-2-delete-vm-to-detach-os-disk) és [3. lépés](#step-3-detach-data-disk-from-the-vm) törölni a virtuális gép **OSDisk** és leválasztása **DataDisk**. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>3. forgatókönyv: Törlése a storage-fiók – minden BLOB storage-fiókon belül, amely a virtuális gépekhez csatolt azonosítása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben válassza ki a **összes erőforrás**. Nyissa meg a storage-fiókban található **Blob Service** kiválasztása **Blobok**.
3. A **tárolók** ablaktáblán az összes tároló azonosítása ahol **bérlet állapota** van **bérelt** , és kövesse [2. forgatókönyv](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) minden  **Bérelt** tároló.
4. Hajtsa végre a [2. lépés](#step-2-delete-vm-to-detach-os-disk) és [3. lépés](#step-3-detach-data-disk-from-the-vm) törölni a virtuális gép **OSDisk** és leválasztása **DataDisk**. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>2. lépés: Törölje a virtuális gép operációsrendszer-lemez leválasztása
Ha a VHD-t operációsrendszer-lemezt, a csatlakoztatott virtuális merevlemez törlése előtt törölnie kell a virtuális Gépet. Semmilyen további műveletet, miután végrehajtotta ezeket a lépéseket a ugyanazon virtuális Géphez csatolt adatlemezek szükség lesz:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben válassza ki a **virtuális gépek**.
3. Válassza ki a virtuális gép, amelyhez a virtuális merevlemez csatolva van.
4. Győződjön meg arról, hogy semmi sem aktívan használ a virtuális gépet, és a virtuális gép már nincs szüksége.
5. Felső részén a **virtuális gép részletei** ablaktáblán válassza **törlése**, és kattintson a **Igen** megerősítéséhez.
6. A virtuális Gépet törölni kell, de a VHD-t meg kell őrizni. A virtuális merevlemez azonban már nem kell csatlakoztatni egy virtuális Gépet, vagy a címbérlet rendelkezik rá vonatkozó. A bérlet kiadandó néhány percig is eltarthat. Győződjön meg arról, hogy a bérlet, keresse meg a blob helyére, majd a a **Blob tulajdonságai** panelen a **bérleti státusz** kell **elérhető**.

## <a name="step-3-detach-data-disk-from-the-vm"></a>3. lépés: A virtuális gépről adatlemez leválasztása
Ha a virtuális merevlemez adatlemez, válassza le a virtuális Merevlemezt a virtuális gépről eltávolítani a bérletet:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben válassza ki a **virtuális gépek**.
3. Válassza ki a virtuális gép, amelyhez a virtuális merevlemez csatolva van.
4. Válassza ki **lemezek** a a **virtuális gép részletei** ablaktáblán.
5. Válassza ki a törlendő az adatlemezt a virtuális merevlemez csatolva van. Megadhatja, hogy melyik blob URL-címét a virtuális merevlemez ellenőrzésével a lemezen van csatolva.
6. A blob hely a lemezen, ellenőrizze az elérési utat gombra kattintva ellenőrizheti **VHD URI** mező.
7. Válassza ki **szerkesztése** tetején **lemezek** ablaktáblán.
8. Kattintson a **ikon leválasztása** törölni az adatokat lemezről.

     ![A portálon nyissa meg a storage "Blob metaadatainak" ablaktábla képernyőképe](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Kattintson a **Mentés** gombra. A lemez most már le van választva a virtuális gépről, és a egy már nem bérelt a VHD-t. A bérlet kiadandó néhány percig is eltarthat. Győződjön meg arról, hogy a címbérlete megszűnt, keresse meg a blob helyére, majd a a **Blob tulajdonságai** panelen a **bérleti státusz** értéket kell **feloldva** vagy **Elérhető**.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

