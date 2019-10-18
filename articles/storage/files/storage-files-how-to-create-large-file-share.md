---
title: Nagyméretű fájlmegosztás engedélyezése és létrehozása – Azure Files
description: Ebből a cikkből megtudhatja, hogyan engedélyezheti és hozhatja létre a nagyméretű fájlmegosztást.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 00db5905c008644bc21704179363849756fa7dcc
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518420"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>Nagyméretű fájlmegosztás engedélyezése és létrehozása

A standard fájlmegosztás eredetileg legfeljebb 5 TiB-ra méretezhető, így a nagyméretű fájlmegosztás esetében akár 100 TiB-ra is méretezhető. Alapértelmezés szerint a prémium szintű fájlmegosztás akár 100 TiB-ra is méretezhető. 

A standard fájlmegosztás használatával akár 100 TiB-ra is szükség lehet a Storage-fiók nagyméretű fájlmegosztás használatára való méretezéséhez. Engedélyezheti egy meglévő fiókot, vagy létrehozhat egy új fiókot nagyméretű fájlmegosztás használatához.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="restrictions"></a>Korlátozások

A nagyméretű fájlmegosztást engedélyező fiókok támogatják a LRS vagy a ZRS. Egyelőre a nagyméretű fájlmegosztást engedélyező fiókok nem támogatják a GZRS, a GRS és az RA-GRS. A nagyméretű fájlmegosztás egy fiókban való engedélyezése visszafordíthatatlan folyamat, miután engedélyezte a fiókját, nem alakítható át GZRS, GRS vagy RA-GRS értékre.

## <a name="create-a-new-storage-account"></a>Új tárfiók létrehozása

Jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. Az Azure Portalon válassza a **Minden szolgáltatás** elemet. Az erőforrások listájába írja be a **Storage-fiókok** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Tárfiókok** lehetőséget.
1. A megjelenő **Storage-fiókok** ablakban válassza a **Hozzáadás** lehetőséget.
1. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a tárfiókot.
1. Az **Erőforráscsoport** mező alatt válassza az **Új létrehozása** elemet. Az alábbi képen látható módon nevezze el az új erőforráscsoportot.

    ![Erőforráscsoport portálon való létrehozását bemutató képernyőkép](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Ezután adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A név 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
1. Válasszon egy helyet a Storage-fiókhoz, győződjön meg arról, hogy az [egyike a LFS által támogatott régióknak](storage-files-planning.md#regional-availability).
1. Állítsa a replikálást **helyileg redundáns tárolási** vagy **zóna-redundáns tárterületre**.
1. Ne módosítsa a következő mezők alapértelmezett értékeit:

   |Mező  |Value (Díj)  |
   |---------|---------|
   |Üzemi modell     |Erőforrás-kezelő         |
   |Teljesítmény     |Standard         |
   |Fióktípus     |StorageV2 (általános célú v2)         |
   |Elérési szint     |Gyakori         |

1. Válassza a **speciális** lehetőséget, majd a **nagyméretű fájlmegosztás**esetén válassza az **engedélyezve** lehetőséget.
1. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.

    ![Large-file-shares-Advanced-Enable. png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Kattintson a **Létrehozás** gombra.

## <a name="enable-on-existing-account"></a>Engedélyezés meglévő fiókban

A nagyméretű fájlmegosztást is engedélyezheti a meglévő fiókokon. Ha ezt teszi, a fiók többé nem lesz képes a GZRS, GRS vagy RA-GRS átalakításra. Ez a lehetőség nem vonható vissza ezen a fiókon.

1. Nyissa meg a [Azure Portal](https://portal.azure.com) , és navigáljon ahhoz a Storage-fiókhoz, amelyen engedélyezni szeretné a nagyméretű fájlmegosztást.
1. Nyissa meg a Storage-fiókot, és válassza a **konfiguráció**lehetőséget.
1. Válassza az **engedélyezve** lehetőséget a **nagyméretű fájlmegosztás**elemnél, majd kattintson a Mentés gombra.
1. Válassza az **Áttekintés** lehetőséget, és válassza a **frissítés**lehetőséget.

![Enable-Large-file-shares-on-existing. png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Ezzel engedélyezte a nagyméretű fájlmegosztás használatát a Storage-fiókban.

Ha a következő hibaüzenetet kapja: "a nagyméretű fájlmegosztás még nem érhető el a fiókhoz." Várjon egy kis időt, mivel a régió valószínűleg a kilépések befejezésének közepén van, vagy ha sürgősen szüksége van rá, forduljon a támogatási szolgálathoz.

## <a name="create-a-large-file-share"></a>Nagyméretű fájlmegosztás létrehozása

A nagyméretű fájlmegosztás létrehozása majdnem azonos a szabványos fájlmegosztás létrehozásával. A fő különbség az, hogy a kvótát akár 100 TiB-re is beállíthatja.

1. A Storage-fiókban válassza a **fájlmegosztás**lehetőséget.
1. Válassza a **+ fájlmegosztás**lehetőséget.
1. Adjon meg egy nevet a fájlmegosztás számára, és (opcionálisan) a kvóta kívánt méretét, akár 100 TiB-t, majd válassza a **Létrehozás**lehetőséget. 

![Large-file-shares-Create-share. png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

## <a name="expand-existing-file-shares"></a>Meglévő fájlmegosztás kibontása

Miután engedélyezte a nagyméretű fájlmegosztást a Storage-fiókjában, kiterjesztheti a meglévő megosztásokat a magasabb kvótára.

1. A Storage-fiókban válassza a **fájlmegosztás**lehetőséget.
1. Kattintson a jobb gombbal a fájlmegosztás elemre, és válassza a **kvóta**lehetőséget.
1. Adja meg a kívánt új méretet, majd kattintson **az OK gombra**.

![Update-Large-file-share-quota. png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

## <a name="next-steps"></a>Következő lépések

* [Fájlmegosztás csatlakoztatása – Windows](storage-how-to-use-files-windows.md)
* [Fájlmegosztás csatlakoztatása – Linux](../storage-how-to-use-files-linux.md)
* [Fájlmegosztás csatlakoztatása – macOS](storage-how-to-use-files-mac.md)