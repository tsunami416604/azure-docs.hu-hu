---
title: Azure Linux virtuális gépekhez csatlakoztatott virtuális merevlemezek hibaelhárítása |} Microsoft Docs
description: Például a Linux virtuális gépek vagy törölni, a tárolási fiók tartalmazó váratlan újraindítások kapcsolatos problémák elhárítása a virtuális merevlemezek csatolni.
keywords: ssh kapcsolat visszautasította, ssh hiba és az azure-ssh, SSH-kapcsolat sikertelen
services: virtual-machines-linux
author: roygara
manager: jeconnoc
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/28/2018
ms.author: rogarana
ms.openlocfilehash: 3e1bac226a75ace3efdbc9886d152da055075920
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshoot-attached-vhds-on-azure-linux-virtual-machines"></a>Azure Linux virtuális gépekhez csatlakoztatott virtuális merevlemezek hibaelhárítása

Azure virtuális gépekhez csatlakoztatott virtuális merevlemezek (VHD) az operációsrendszer-lemezképet, és minden adatlemezek támaszkodnak. Virtuális merevlemezek, egy vagy több Azure Storage-fiókok a lapblobokat tárolódnak. Ez a cikk ismerteti a VHD-k az kapcsolatos gyakori hibák elhárítása. 

  * [Váratlan újraindítások csatlakoztatott virtuális merevlemezek a virtuális gépek]
  * [A Resource Manager deployment tárolási tárfióktörlési hibák]

## <a name="you-are-experiencing-unexpected-reboots"></a>Váratlan újraindítások csatlakoztatott virtuális merevlemezek a virtuális gépek

Ha egy Azure virtuális gép (VM) ugyanabban a tárfiókban lévő csatlakoztatott virtuális merevlemezek nagy mennyiségű, haladhatja meg a méretezhetőségi célok, az egyes tárfiók a virtuális gép leállását okozza. Ellenőrizze a tárfiók percenkénti metrikákat (**TotalRequests**/**TotalIngress**/**TotalEgress**) a teljesítményt, amelyek mérete meghaladja a méretezhetőségi célok tárfiókok esetén. Ha segítségre van szüksége a meghatározhatja, hogy a sávszélesség-szabályozás történt a tárfiók "[metrikák megjelenítése egy PercentThrottlingError növekedése]" című szakaszában talál.

Általában minden egyes bemeneti vagy kimeneti művelet a virtuális gép virtuális merevlemez az eszköz **első oldal** vagy **Put lap** az alapul szolgáló oldalakra vonatkozó blob műveleteket. Ezért a környezetnek a becsült IOPS segítségével is rendelkezik egyetlen tárfiókokban alapján az adott viselkedést az alkalmazás hány virtuális merevlemezek finomítsa. A Microsoft azt javasolja, 40 vagy kevesebb lemezek egyetlen tárfiókokban. Lásd: [Azure Storage méretezhetőségi és teljesítménycéloknak](../../storage/common/storage-scalability-targets.md) vonatkozó további információért méretezhetőségi célok storage-fiókok, különösen a teljes kérelmek aránya és a tárfióknak a típusa sávszélességet használ.

Ha a tárfiók túllépte a vonatkozó méretezhetőségi célok, helyezze a virtuális merevlemezek több tárfiókot a tevékenység minden egyes számlára csökkentése érdekében.

## <a name="storage-delete-errors-in-rm"></a>A Resource Manager deployment tárolási tárfióktörlési hibák

Ez a szakasz nyújt hibaelhárítási útmutatót, ha a hibák a következők egyikét végrehajthatók, miközben a kívánt Azure-tárfiókot, tároló, vagy Azure Resource Manager-telepítés blob törlése.

>**Nem sikerült törölni a tárfiókot "StorageAccountName". Hiba: A tárfiók nem törölhető a mert az összetevői használatban vannak.**

>**Nem sikerült törölni a # kívül # tárolója:<br>VHD-k: jelenleg a címbérlet a tárolóra, és a kérelemben nincs bérleti azonosító lett megadva.**

>**Nem sikerült törölni a # kívül # blobok:<br>BlobName.vhd: jelenleg a címbérlet blobot, és a kérelemben nincs bérleti azonosító lett megadva.**

Az Azure virtuális gépeken használt virtuális merevlemezeknek a .vhd fájlok lapblobokat egy standard vagy prémium szintű storage-fiókban az Azure-ban tárolja.  További információ az Azure-lemezeket található [Itt](../../virtual-machines/windows/about-disks-and-vhds.md). Azure megakadályozza, hogy a sérülés megelőzése érdekében egy virtuális géphez csatlakoztatott lemez törlése. Megakadályozza a tárolók és van egy virtuális géphez csatolt oldalakra vonatkozó blob storage-fiókok törlését is. 

A folyamat egy tárfiókot, tárolót vagy blobot törölni, ezek a hibák egyik fogadásakor történik: 
1. [A virtuális Géphez csatlakozik, blobok azonosítása](#step-1-identify-blobs-attached-to-a-vm)
2. [Törlés rendelkező virtuális gépek csatolt **operációsrendszer-lemez**](#step-2-delete-vm-to-detach-os-disk)
3. [Válassza le az összes **adatok lemez(ek)** a többi virtuális gép van](#step-3-detach-data-disk-from-the-vm)

A lépések elvégzése után ismételje meg a tárfiók törlése, a tárolót vagy blobot.

### <a name="step-1-identify-blob-attached-to-a-vm"></a>1. lépés: Egy virtuális Géphez csatlakozik, blob azonosítása

#### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>1. forgatókönyv: Egy blob – törlése csatolt VM azonosítása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben válassza ki a **összes erőforrás**. Nyissa meg a tárolási fiók alatt **Blob szolgáltatás** kiválasztása **tárolók**, és keresse meg a blob törlése.
3. Ha a blob **bérleti állapot** van **Leased**, majd kattintson a jobb gombbal, és válassza ki **szerkesztése metaadatok** Blob metaadatai ablaktábla megnyitása. 

    ![Képernyőkép a portálon, a a tárolóval blobok fiókot, és kattintson a jobb gombbal > "Szerkesztése metaadatok" a kijelölt](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. A Blob metaadatai ablaktáblán ellenőrizze, és jegyezze fel értéke **MicrosoftAzureCompute_VMName**. Ez az érték esetén a virtuális gép, amely a virtuális merevlemez csatolva van. (Lásd: **fontos** Ha ez a mező nem létezik.)
5. A Blob metaadatai ablaktáblán ellenőrizze, és jegyezze fel értékének **MicrosoftAzureCompute_DiskType**. Ez az érték azt jelzi, hogy a csatlakoztatott lemez az operációs rendszer vagy az adatok lemez (lásd: **fontos** Ha ez a mező nem létezik). 

     ![Képernyőkép a portálon, a tárolási "Blob metaadatai" ablak megnyitása](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Ha a blob lemez **OSDisk** kövesse [2. lépés: virtuális gép törlése operációsrendszer-lemez leválasztása](#step-2-delete-vm-to-detach-os-disk). Ellenkező esetben ha a blob lemez **DataDisk** kövesse a [3. lépés: adatok lemezt leválasztani a virtuális gép](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Ha **MicrosoftAzureCompute_VMName** és **MicrosoftAzureCompute_DiskType** nem jelennek meg a blob metaadatai, azt jelzi, hogy a blob explicit módon bérelt, és nem egy virtuális Géphez van csatolva. Bérelt blobok a címbérlet első megszüntetése nélkül nem lehet törölni. Törés az bérleti, kattintson a jobb gombbal a blob, és válassza ki **Break bérleti**. Bérelt blobok, amelyek nem kapcsolódnak egy virtuális Gépet a blob törlésének megakadályozása, de nem akadályozzák meg a tároló vagy a storage-fiók törlése.

#### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>2. forgatókönyv: A tároló - törlésével minden blob(s) tárolóban, a virtuális gépekhez csatolt azonosítása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben válassza ki a **összes erőforrás**. Nyissa meg a tárolási fiók alatt **Blob szolgáltatás** válassza **tárolók**, és keresse meg a tároló törölni.
3. Ide kattintva nyissa meg a tároló és a benne lévő blobokat listája jelenik meg. Azonosítsa az összes BLOB Blokkblob típusú = **oldalakra vonatkozó blob** és bérleti állapot = **Leased** lekerül erről a listáról. Hajtsa végre a [1. forgatókönyv](#step-1-identify-blobs-attached-to-a-vm) a virtuális Gépet, a blobok társított azonosításához.

    ![Képernyőkép a portálon, a tárolási fiók blobok és a "bérleti állapot" a "Leased" kiemelve](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Hajtsa végre a [2. lépés](#step-2-delete-vm-to-detach-os-disk) és [3. lépés](#step-3-detach-data-disk-from-the-vm) törölni a virtuális gép van **OSDisk** és leválasztási **DataDisk**. 

#### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>3. forgatókönyv: A tároló törlése fiók – összes blob(s) tárfiókon belül, a virtuális gépekhez csatolt azonosítása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben válassza ki a **összes erőforrás**. Nyissa meg a tárolási fiók alatt **Blob szolgáltatás** válasszon **tárolók**.

    ![Képernyőkép a portálon, a fiók a tároló és a "bérleti állapot" a "Leased" kiemelve](./media/troubleshoot-vhds/utd-containers-sm.png)

3. A **tárolók** ablaktáblán azonosítsa az összes tároló ahol **bérleti állapot** van **Leased** kövesse [2. forgatókönyv](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) minden  **Bérelt** tároló.
4. Hajtsa végre a [2. lépés](#step-2-delete-vm-to-detach-os-disk) és [3. lépés](#step-3-detach-data-disk-from-the-vm) törölni a virtuális gép van **OSDisk** és leválasztási **DataDisk**. 

### <a name="step-2-delete-vm-to-detach-os-disk"></a>2. lépés: Az operációsrendszer-lemez leválasztása a virtuális gép törlése
Ha a virtuális Merevlemezt egy operációsrendszer-lemez, a csatolt virtuális merevlemez törlése előtt törölnie kell a virtuális gép. Nincs további teendője adatlemezek az azonos virtuális Géphez csatlakozik, miután végrehajtotta ezeket a lépéseket kell:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben válassza ki a **virtuális gépek**.
3. Válassza ki a virtuális Gépet, amely a virtuális merevlemez csatolva van.
4. Győződjön meg arról, hogy semmi sem aktívan használ a virtuális gép és a virtuális gép már nem szükséges.
5. Felső részén a **virtuális gép összes adatát** ablaktáblán válassza előbb **törlése**, és kattintson a **Igen** megerősítéséhez.
6. A virtuális Gépet törölni kell, de a VHD-t meg kell őrizni. Azonban a virtuális merevlemez már nem csatolható a virtuális géphez, vagy a címbérlet rendelkezzen. A címbérlet mikorra várható néhány percig is eltarthat. Győződjön meg arról, hogy megjelent-e a címbérlet, keresse meg a blob helyére, majd a a **Blob-tulajdonságok** ablaktáblán, a **bérleti állapot** kell **elérhető**.

### <a name="step-3-detach-data-disk-from-the-vm"></a>3. lépés: Adatok lemezt leválasztani a virtuális gép
Ha a virtuális merevlemez adatlemezt, válassza le a virtuális Merevlemezt a virtuális gép eltávolítása a címbérlet:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben válassza ki a **virtuális gépek**.
3. Válassza ki a virtuális Gépet, amely a virtuális merevlemez csatolva van.
4. Válassza ki **lemezek** a a **virtuális gép összes adatát** ablaktáblán.
5. Válassza ki a törlendő adatok lemezt a virtuális merevlemez csatolva van. Azt is meghatározhatja, melyik blob csatlakozik a lemez a virtuális merevlemez URL-CÍMÉT ellenőrzésével.
6. A blob hely a lemezen, hogy ellenőrizze az elérési utat kattintva ellenőrizheti **virtuális merevlemez URI** mező.
7. Válassza ki **szerkesztése** a a **lemezek** ablaktáblán.
8. Kattintson a **ikon leválasztani** törlendő adatok lemezéhez.

     ![Képernyőkép a portálon, a tárolási "Blob metaadatai" ablak megnyitása](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Kattintson a **Mentés** gombra. A lemez most már le van választva a virtuális gépről, és a virtuális merevlemez már nem egy bérelt. A címbérlet mikorra várható néhány percig is eltarthat. Győződjön meg arról, hogy a címbérlet kiadása, keresse meg a blob helyére, majd a a **Blob-tulajdonságok** ablaktáblán, a **bérleti állapot** értéket kell **feloldva** vagy **Elérhető**.



[Váratlan újraindítások csatlakoztatott virtuális merevlemezek a virtuális gépek]: #you-are-experiencing-unexpected-reboots
[A Resource Manager deployment tárolási tárfióktörlési hibák]: #storage-delete-errors-in-rm