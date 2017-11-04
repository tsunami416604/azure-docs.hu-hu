---
title: "Az Azure storage-fiókok, a tárolók és a VHD törlésekor hibák elhárítása |} Microsoft Docs"
description: "Hibaelhárítás az Azure storage-fiókok, a tárolók és a VHD törlésekor"
services: storage
documentationcenter: 
author: passaree
manager: cshepard
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 7a3c9422887592c29c2eae8bd75cf960865808fd
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-storage-delete-errors-in-resource-manager-deployment"></a>Tároló törlése Resource Manager telepítés hibáinak elhárítása
Ez a cikk nyújt hibaelhárítási útmutatót, ha a hibák a következők egyikét fordulhat elő, ha az Azure storage-fiók, a tároló vagy a blob az Azure Resource Manager deployment törlésére tett kísérlet során.

>**Nem sikerült törölni a tárfiókot "StorageAccountName". Hiba: A tárfiók nem törölhető a mert az összetevői használatban vannak.**

>**Nem sikerült törölni a # kívül # tárolója:<br>VHD-k: jelenleg a címbérlet a tárolóra, és a kérelemben nincs bérleti azonosító lett megadva.**

>**Nem sikerült törölni a # kívül # blobok:<br>BlobName.vhd: jelenleg a címbérlet blobot, és a kérelemben nincs bérleti azonosító lett megadva.**

Az Azure virtuális gépeken használt virtuális merevlemezeket tárolja a lapblobokat, a standard vagy prémium szintű storage-fiók az Azure-ban a .vhd fájl.  További információ az Azure-lemezeket található [Itt](../../virtual-machines/windows/about-disks-and-vhds.md). Azure megakadályozza, hogy a sérülés megelőzése érdekében egy virtuális géphez csatlakoztatott lemez törlése. Megakadályozza a tárolók és van egy virtuális géphez csatolt oldalakra vonatkozó blob storage-fiókok törlését is. 

## <a name="solution"></a>Megoldás
A folyamat egy tárfiókot, tárolót vagy blobot törölni, a fenti hibák egyik fogadásakor történik: 
1. [A virtuális Géphez csatlakozik, blobok azonosítása](#step-1-identify-blobs-attached-to-a-vm)
2. [Törlés rendelkező virtuális gépek csatolt **operációsrendszer-lemez**](#step-2-delete-vm-to-detach-os-disk)
3. [Válassza le az összes **adatok lemez(ek)** a többi virtuális gép van](#step-3-detach-data-disk-from-the-vm)

A fenti lépések elvégzése után próbálkozzon újra a tárfiók törlése, a tárolót vagy blobot.

### <a name="step-1-identify-blob-attached-to-a-vm"></a>1. lépés: Egy virtuális Géphez csatlakozik, blob azonosítása

#### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>1. forgatókönyv: Egy blob – törlése csatolt VM azonosítása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben válassza ki a **összes erőforrás**. Nyissa meg a tárolási fiók alatt **Blob szolgáltatás** válasszon **tárolók** , és keresse meg a törlendő blob.
3. Ha a blob **bérleti állapot** van **Leased**, kattintson a jobb gombbal, és válassza ki **szerkesztése metaadatok** Blob metaadatai ablaktábla megnyitása. 

    ![Képernyőkép a portálon, a a tárolóval blobok fiókot, és kattintson a jobb gombbal > hilighted "Metaadatok szerkesztése"](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-edit-metadata-sm.png)

4. A Blob metaadatai ablaktáblán ellenőrizze, és jegyezze fel értéke **MicrosoftAzureCompute_VMName**. Ez az érték esetén a virtuális gép, amely a virtuális merevlemez csatolva van. (Lásd: **fontos** Ha ez a mező nem létezik.)
5. A Blob metaadatai ablaktáblán ellenőrizze, és jegyezze fel értékének **MicrosoftAzureCompute_DiskType**. Ez azt jelzi, hogy a csatlakoztatott lemez az operációs rendszer vagy az adatok lemez (lásd: **fontos** Ha ez a mező nem létezik). 

     ![Képernyőkép a portálon, a tárolási "Blob metaadatai" ablak megnyitása](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-blob-metadata-sm.png)

6. Ha a blob lemez **OSDisk** kövesse [2. lépés: virtuális gép törlése operációsrendszer-lemez leválasztása](#step-2-delete-vm-to-detach-os-disk). Ellenkező esetben ha a blob lemez **DataDisk** kövesse a [3. lépés: adatok lemezt leválasztani a virtuális gép](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Ha **MicrosoftAzureCompute_VMName** és **MicrosoftAzureCompute_DiskType** nem jelennek meg a blob metaadatai, azt jelzi, hogy a blob explicit módon bérelt, és nem egy virtuális Géphez van csatolva. Bérelt blobok a címbérlet első megszüntetése nélkül nem lehet törölni. Törés az bérleti, kattintson jobb gombbal a blob, és válassza ki **Break bérleti**. Bérelt blobok nem kapcsolódnak egy virtuális Gépet a blob törlésének megakadályozása, de nem akadályozzák meg a tároló vagy a storage-fiók törlése.

#### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>2. forgatókönyv: A tároló - törlésével minden blob(s) tárolóban, a virtuális gépekhez csatolt azonosítása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben válassza ki a **összes erőforrás**. Nyissa meg a tárolási fiók alatt **Blob szolgáltatás** válasszon **tárolók** keresse meg a tároló törölni.
3. Ide kattintva nyissa meg a tároló és a benne lévő blobokat listája jelenik meg. Azonosítsa az összes BLOB Blokkblob típusú = **oldalakra vonatkozó blob** és bérleti állapot = **Leased** lekerül erről a listáról. Hajtsa végre a [1. forgatókönyv](#step-1-identify-blobs-attached-to-a-vm) a virtuális Gépet, a blobok társított azonosításához.

    ![Képernyőkép a portálon, a tárolási fiók blobok és a "bérleti állapot" a "Leased" kiemelve](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-disks-sm.png)

4. Hajtsa végre a [2. lépés](#step-2-delete-vm-to-detach-os-disk) és [3. lépés](#step-3-detach-data-disk-from-the-vm) törölni a virtuális gép van **OSDisk** és leválasztási **DataDisk**. 

#### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>3. forgatókönyv: A tároló törlése fiók – összes blob(s) tárfiókon belül, a virtuális gépekhez csatolt azonosítása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben válassza ki a **összes erőforrás**. Nyissa meg a tárolási fiók alatt **Blob szolgáltatás** válasszon **tárolók**.

    ![Képernyőkép a portálon, a fiók a tároló és a "bérleti állapot" a "Leased" kiemelve](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-containers-sm.png)

3. A **tárolók** panelen azonosítsa az összes tároló ahol **bérleti állapot** van **Leased** kövesse [2. forgatókönyv](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) minden  **Bérelt** tároló.
4. Hajtsa végre a [2. lépés](#step-2-delete-vm-to-detach-os-disk) és [3. lépés](#step-3-detach-data-disk-from-the-vm) törölni a virtuális gép van **OSDisk** és leválasztási **DataDisk**. 

### <a name="step-2-delete-vm-to-detach-os-disk"></a>2. lépés: Az operációsrendszer-lemez leválasztása a virtuális gép törlése
Ha a virtuális Merevlemezt egy operációsrendszer-lemez, a csatolt virtuális merevlemez törlése előtt törölnie kell a virtuális gép. Nincs további teendője adatlemezek az azonos virtuális Géphez csatlakozik, miután végrehajtotta ezeket a lépéseket kell:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben válassza ki a **virtuális gépek**.
3. Válassza ki a virtuális Gépet, amely a virtuális merevlemez csatolva van.
4. Győződjön meg arról, hogy semmi sem aktívan használ a virtuális gép és a virtuális gép már nem szükséges.
5. Felső részén a **virtuális gép összes adatát** panelen válassza **törlése**, és kattintson a **Igen** megerősítéséhez.
6. A virtuális Gépet törölni kell, de a VHD-t meg kell őrizni. Azonban a virtuális merevlemez már nem csatolható a virtuális géphez, vagy a címbérlet rendelkezzen. A címbérlet mikorra várható néhány percig is eltarthat. Győződjön meg arról, hogy megjelent-e a címbérlet, keresse meg a blob helyére, majd a a **Blob-tulajdonságok** ablaktáblán, a **bérleti állapot** kell **elérhető**.

### <a name="step-3-detach-data-disk-from-the-vm"></a>3. lépés: Adatok lemezt leválasztani a virtuális gép
Ha a virtuális merevlemez adatlemezt, válassza le a virtuális Merevlemezt a virtuális gép eltávolítása a címbérlet:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben válassza ki a **virtuális gépek**.
3. Válassza ki a virtuális Gépet, amely a virtuális merevlemez csatolva van.
4. Válassza ki **lemezek** a a **virtuális gép összes adatát** panelen.
5. Válassza ki a törlendő adatok lemezt a virtuális merevlemez csatolva van. Azt is meghatározhatja, melyik blob csatlakozik a lemez a virtuális merevlemez URL-CÍMÉT ellenőrzésével.
6. A blob hely a lemezen, hogy ellenőrizze az elérési utat kattintva ellenőrizheti **virtuális merevlemez URI** mező.
7. Válassza ki **szerkesztése** a a **lemezek** panelen.
8. Kattintson a **ikon leválasztani** törlendő adatok lemezéhez.

     ![Képernyőkép a portálon, a tárolási "Blob metaadatai" ablak megnyitása](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-vm-disks-edit.png)

9. Kattintson a **Mentés** gombra. A lemez most a virtuális gép választható le, és a virtuális merevlemez már nem kell rendelkeznie a címbérlet rajta. A címbérlet mikorra várható néhány percig is eltarthat. Győződjön meg arról, hogy a címbérlet kiadása, keresse meg a blob helyére, majd a a **Blob-tulajdonságok** ablaktáblán, a **bérleti állapot** értéket kell **feloldva** vagy **Elérhető**.

## <a name="next-steps"></a>Következő lépések
Ismételje meg a tárolási objektum, amely korábban nem sikerült törölni.

