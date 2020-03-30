---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5104c3b4446a8d3747ce7cc1648ef05dd117eb3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013772"
---
A Storage Explorer 1.10.0 lehetővé teszi a felhasználók számára a felügyelt lemezek feltöltését, letöltését és másolását, valamint pillanatképek létrehozását. A további képességek miatt a Storage Explorer segítségével áttelepítheti az adatokat a helyszíni Azure-ból az Azure-ba, és áttelepítheti az adatokat az Azure-régiók között.

## <a name="prerequisites"></a>Előfeltételek

A cikk kitöltéséhez a következőkre lesz szüksége:
- Azure-előfizetés
- Egy vagy több Azure által kezelt lemez
- Az Azure [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) legújabb verziója

## <a name="connect-to-an-azure-subscription"></a>Csatlakozás Azure-előfizetéshez

Ha a Storage Explorer nem csatlakozik az Azure-hoz, nem fogja tudni használni az erőforrások kezelésére. Ez a szakasz az Azure-fiókhoz való csatlakoztatása, így az erőforrások kezelése a Storage Explorer használatával.

1. Indítsa el az Azure Storage Exploreralkalmazást, és kattintson a bal oldali **beépülő modul** ikonjára.

    ![Kattintson a beépülő modul ikonjára](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. Válassza **az Azure-fiók hozzáadása**lehetőséget, majd kattintson a **Tovább**gombra.

    ![Azure-fiók hozzáadása](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. Az **Azure Bejelentkezés** párbeszédpanelen adja meg az Azure-hitelesítő adatait.

    ![Az Azure bejelentkezési párbeszédpanelje](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Válassza ki az előfizetést a listából, majd kattintson az **Alkalmaz** gombra.

    ![Válassza ki előfizetését.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Felügyelt lemez feltöltése helyszíni virtuális merevlemezről

1. A bal oldali ablaktáblán bontsa ki a **Lemezek csomópontot,** és válassza ki azt az erőforráscsoportot, amelyre fel szeretné tölteni a lemezt.

    ![Erőforráscsoport kiválasztása 1.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Válassza a **Feltöltés** lehetőséget.

    ![Feltöltés kiválasztása](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. A **VHD feltöltése** adja meg a forrás Virtuális merevlemez, a lemez nevét, az operációs rendszer típusát, a kívánt régiót feltölteni a lemezt, valamint a fiók típusát. Egyes régiókban a rendelkezésre állási zónák támogatottak, ezeknél a régióknál kiválaszthatja a kívánt zónát.
1. A lemez feltöltésének megkezdéséhez válassza a **Létrehozás** lehetőséget.

    ![A vhd feltöltése párbeszédpanel](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. A feltöltés állapota most antól megjelenik a **Tevékenységek ben.**

    ![Feltöltés állapota](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Ha a feltöltés befejeződött, és a lemez nem látható a jobb oldali ablaktáblában, válassza a **Frissítés**lehetőséget.

## <a name="download-a-managed-disk"></a>Felügyelt lemez letöltése

A következő lépések bemutatják, hogyan tölthet le felügyelt lemezt egy helyszíni virtuális merevlemezre. A lemez állapotának **le nem csatoltnak** kell lennie ahhoz, hogy letölthesse, nem tölthető le **csatolt** lemez.

1. Ha még nincs kibontva a bal oldali ablaktáblán, bontsa ki a **Lemezek csomópontot,** és válassza ki azt az erőforráscsoportot, amelyről le szeretné tölteni a lemezt.

    ![Erőforráscsoport kiválasztása 1.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. A jobb oldali ablaktáblán jelölje ki a letölteni kívánt lemezt.
1. Válassza **a Letöltés** lehetőséget, majd válassza ki, hogy hová szeretné menteni a lemezt.

    ![Felügyelt lemez letöltése](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. Válassza a **Mentés** lehetőséget, és a lemez letöltése megkezdődik. A letöltés állapota megjelenik a **Tevékenységek területen.**

    ![Letöltési állapot](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Felügyelt lemez másolása

A Storage Explorer segítségével a csoporton lévő lemezeket régiókon belül vagy régiók között másolhatja. Lemez másolása:

1. A bal oldali **Lemezek** legördülő menüben jelölje ki a másolni kívánt lemezt tartalmazó erőforráscsoportot.

    ![Erőforráscsoport kiválasztása 1.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. A jobb oldali ablaktáblában jelölje ki a másolni kívánt lemezt, és válassza a **Másolás lehetőséget.**

    ![Felügyelt lemez másolása](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. A bal oldali ablaktáblán jelölje ki azt az erőforráscsoportot, amelybe be szeretné illeszteni a lemezt.

    ![2. erőforráscsoport kiválasztása](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. Válassza a **Beillesztés** lehetőséget a jobb oldali ablaktáblán.

    ![Felügyelt lemez beillesztése](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. A **Lemez beillesztése** párbeszédpanelen töltse ki az értékeket. A támogatott régiókban is megadhat egy rendelkezésre állási zónát.

    ![Lemez beillesztése párbeszédpanel](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. Válassza a **Beillesztés** lehetőséget, és a lemez elkezdi a másolást, az állapot megjelenik a **Tevékenységek alkalmazásban.**

    ![Beillesztési állapot másolása](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Pillanatkép létrehozása

1. A bal oldali **Lemezek** legördülő menüben jelölje ki a pillanatképet tartalmazó erőforráscsoportot.

    ![Erőforráscsoport kiválasztása 1.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. A jobb oldalon jelölje ki a pillanatképet létrehozni kívánt lemezt, és válassza a **Pillanatkép létrehozása lehetőséget.**

    ![Pillanatkép létrehozása](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. A **Pillanatkép létrehozása**csoportban adja meg a pillanatkép nevét, valamint azt az erőforráscsoportot, amelyben létre szeretné hozni. Ezután válassza **a Létrehozás lehetőséget.**

    ![Pillanatkép létrehozása párbeszédpanel](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. A pillanatkép létrehozása után **kiválaszthatja a Megnyitás a portálon** a **tevékenységekben** lehetőséget a pillanatkép megtekintéséhez az Azure Portalon.

    ![Pillanatkép megnyitása a portálon](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>További lépések
