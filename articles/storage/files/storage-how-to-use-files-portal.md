---
title: Azure-fájlmegosztások felügyelete az Azure Portallal
description: Itt megismerheti, hogyan felügyelheti az Azure Files szolgáltatást az Azure Portal használatával.
services: storage
author: wmgries
ms.service: storage
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: b4cace3922983ed93987069ac7cf59f2cb69403b
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578548"
---
# <a name="managing-azure-file-shares-with-the-azure-portal"></a>Azure-fájlmegosztások felügyelete az Azure Portallal 
Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure-fájlmegosztások Windows, Linux és macOS rendszeren csatlakoztathatók. Ez az útmutató az Azure-fájlmegosztások [Azure Portallal](https://portal.azure.com/) való használatának alapvető lépéseit mutatja be. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Erőforráscsoport és tárfiók létrehozása
> * Azure-fájlmegosztás létrehozása 
> * Könyvtár létrehozása
> * Fájl feltöltése 
> * Fájl letöltése
> * Megosztási pillanatkép létrehozása és használata

Ha nem rendelkezik Azure-előfizetéssel, első lépésként létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-storage-account"></a>Tárfiók létrehozása
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása
Fájlmegosztás létrehozása:

1. Válassza ki a tárfiókot az irányítópulton.
2. A tárfiók oldalának **Szolgáltatások** szakaszában válassza a **Fájlok** elemet.
    ![A tárfiók szolgáltatások szakaszának képernyőképe; válassza a Fájlok szolgáltatást](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. A **Fájlszolgáltatás** tetején lévő menüben kattintson a **+ Fájlmegosztás** elemre. Lenyílik az **Új fájlmegosztás** oldal.
4. A **Név** mezőbe írja be a *myshare* karakterláncot.
5. Az Azure-fájlmegosztás létrehozásához kattintson az **OK** gombra.

## <a name="work-with-the-contents-of-the-azure-file-share"></a>Az Azure-fájlmegosztás tartalmának használata
Most, hogy létrehozott egy Azure-fájlmegosztást, csatlakoztathatja azt az SMB szolgáltatással [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) vagy [macOS](storage-how-to-use-files-mac.md) rendszeren. Másik megoldásként az Azure Portalon is használhatja az Azure-fájlmegosztást. Az Azure Portalon keresztül intézett összes kérelem a fájl REST API-jával történik, amely lehetővé teszi a fájlok és könyvtárak létrehozását, módosítását és törlését SMB-hozzáférés nélküli ügyfeleken.

### <a name="create-a-directory"></a>Könyvtár létrehozása
Ha egy új *myDirectory* nevű könyvtárat szeretne létrehozni az Azure-fájlmegosztás gyökérmappájában:

1. A **Fájlszolgáltatás** oldalon válassza ki a **myshare** fájlmegosztást. Megnyílik a fájlmegosztás oldala.
2. Az oldal tetején lévő menüben kattintson a **+ Könyvtár hozzáadása** elemre. Lenyílik az **Új könyvtár** oldal.
3. Írja be a *myDirectory* nevet, majd kattintson az **OK**.gombra.

### <a name="upload-a-file"></a>Fájl feltöltése 
A fájlok feltöltésének bemutatása érdekében először létre kell hoznia vagy ki kell választania a feltölteni kívánt fájlt. Ezt bármilyen módon megteheti. Miután kiválasztotta a feltölteni kívánt fájlt:

1. Kattintson a **myDirectory** könyvtárra. Megnyílik a **myDirectory** panel.
2. A felül található menüben kattintson a **Feltöltés** elemre. Megnyílik a **Fájlok feltöltése** panel.  
    ![A fájlok feltöltése panel képernyőképe](media/storage-how-to-use-files-portal/upload-file-1.png)

3. Kattintson a mappa ikonra a helyi fájlok megkeresésére szolgáló ablak megnyitásához. 
4. Válasszon egy fájlt, majd kattintson a **Megnyitás** gombra. 
5. A **Fájlok feltöltése** oldalon ellenőrizze a fájl nevét, majd kattintson a **Feltöltés** elemre.
6. Amikor végzett, a fájlnak meg kell jelennie a **myDirectory** oldalon lévő listában.

### <a name="download-a-file"></a>Fájl letöltése
A fájlra a jobb gombbal kattintva letöltheti a feltöltött fájl másolatát. Miután a letöltés gombra kattintott, a pontos élmény az operációs rendszertől és a használt böngészőtől függ.

## <a name="create-and-modify-share-snapshots"></a>Megosztási pillanatképek létrehozása és módosítása
Az Azure-fájlmegosztással végezhető egyik további hasznos feladat a megosztási pillanatképek létrehozása. A pillanatképek megőrzik az Azure-fájlmegosztások adott időpontban látható állapotát. A megosztási pillanatképek hasonlóak az esetleg már ismert operációsrendszer-technológiákhoz, például a következőkhöz:
- [Kötet árnyékmásolata szolgáltatás (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) Windows fájlrendszerekhez (például NTFS és ReFS)
- [Logikaikötet-kezelő (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pillanatképek Linux rendszerekhez
- [Apple fájlrendszer (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) pillanatképek macOS rendszerhez. 

Megosztási pillanatkép létrehozása:

1. A fájlmegosztás oldalának megnyitásához nyissa meg a tárfiókot az irányítópultról > **Fájlok** > **myshare**. 
2. Kattintson a fájlmegosztás oldalának tetején lévő menü **Pillanatkép** gombjára, majd válassza a **Pillanatkép létrehozása** elemet.  
    ![A pillanatkép létrehozása gomb megkeresését bemutató képernyőkép](media/storage-how-to-use-files-portal/create-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Megosztási pillanatképek listázása és böngészése
A pillanatkép létrehozása után ismét a **Pillanatkép** gombra kattinthat, majd a **Pillanatképek megtekintése** elemet választva megjelenítheti a megosztás pillanatképeinek listáját. Az eredményül kapott ablaktáblán megjelennek a megosztás pillanatképei. Kattintson egy megosztási pillanatképre annak megkereséséhez.

### <a name="restore-from-a-share-snapshot"></a>Visszaállítás megosztási pillanatképből
A fájlok megosztási pillanatképből való visszaállításának bemutatása érdekében először törölnünk kell egy fájlt az éles Azure-fájlmegosztásból. Lépjen a *myDirectory* mappába, kattintson a jobb gombbal a feltöltött fájlra, majd kattintson a **Törlés** gombra. Ezután a fájl megosztási pillanatképből való visszaállításához:

1. Kattintson a **Pillanatképek** elemre a felső menüben, majd válassza a **Pillanatképek megtekintése** elemet. 
2. Kattintson a korábban létrehozott pillanatképre, ez új oldalon nyitja meg a tartalmát. 
3. Kattintson a pillanatképen a **myDirectory** elemre, ekkor láthatja a törölt fájlt. 
4. Kattintson a jobb gombbal a törölt fájlra, és válassza a **Visszaállítás** lehetőséget.
5. Megjelenik egy előugró ablak, amelyben választhat, hogy másolatként állítja-e vissza a fájlt, vagy felülírja az eredeti fájlt. Mivel töröltük az eredeti fájlt, **az eredeti fájl felülírásának** kiválasztásával a törlés előtti állapotba állíthatjuk vissza azt. A fájl Azure-fájlmegosztásba való visszaállításához kattintson az **OK** gombra.  
    ![A fájl visszaállítása párbeszédpanel képernyőképe](media/storage-how-to-use-files-portal/restore-snapshot-1.png)

6. A fájl visszaállítása után zárja be a pillanatkép oldalát, és térjen vissza a **myshare** > **myDirectory** könyvtárba, ekkor a fájlnak az eredeti helyén kell lennie.

### <a name="delete-a-share-snapshot"></a>Megosztási pillanatkép törlése
Megosztási pillanatkép törléséhez [keresse meg a megosztási pillanatképek listáját](#list-and-browse-a-share-snapshot). Jelölje be a megosztási pillanatkép neve melletti jelölőnégyzetet, és válassza a **Törlés** gombot.

![Megosztási pillanatkép törlésének képernyőképe](media/storage-how-to-use-files-portal/delete-snapshot-1.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>További lépések
- [Fájlmegosztások felügyelete az Azure PowerShell használatával](storage-how-to-use-files-powershell.md)
- [Fájlmegosztások felügyelete az Azure CLI használatával](storage-how-to-use-files-cli.md)
- [Fájlmegosztások felügyelete az Azure Storage Explorer használatával](storage-how-to-use-files-storage-explorer.md)
- [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)