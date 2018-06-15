---
title: Azure-beli fájlok helyreállítása Windows Serverre
description: Ez az oktatóanyag az Azure-beli fájlok Windows Serverre történő helyreállításának részletes bemutatását tartalmazza.
services: backup
author: saurabhsensharma
manager: shivamg
keywords: windows server biztonsági mentése; fájlok visszaállítása windows serverre; biztonsági mentés és vészhelyreállítás
ms.service: backup
ms.topic: tutorial
ms.date: 2/14/2018
ms.author: saurse
ms.custom: mvc
ms.openlocfilehash: e05c80e52605e051bdd6815608ca8c12e1393727
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607021"
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Azure-beli fájlok helyreállítása Windows Serverre

Az Azure Backup lehetővé teszi kiválasztott elemek helyreállítását a Windows Server biztonsági másolataiból. Az egyes fájlok visszaállítása akkor hasznos megoldás, ha gyorsan szeretné visszaállítani a véletlenül törölt fájlokat. Ez az oktatóanyag bemutatja, hogyan használhatja a Microsoft Azure Recovery Services- (MARS-) ügynököt egyes elemek helyreállítására az Azure-ban készített biztonsági másolatokból. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Csak egyes elemek helyreállításának indítása 
> * Helyreállítási pont kiválasztása 
> * Elemek visszaállítása a helyreállítási pontból

Ez az oktatóanyag azt feltételezi, hogy már elvégezte a [Windows Serverről az Azure-ba történő biztonsági mentés](backup-configure-vault.md) lépéseit, és van legalább egy biztonsági másolata a Windows Server-fájlokról az Azure-ban.

## <a name="initiate-recovery-of-individual-items"></a>Csak egyes elemek helyreállításának indítása

A Microsoft Azure Recovery Services- (MARS-) ügynökkel együtt egy Microsoft Azure Backup nevű hasznos felhasználói felületi varázsló is települ. A Microsoft Azure Backup varázsló együttműködik a Microsoft Azure Recovery Services- (MARS-) ügynökkel az Azure-ban tárolt helyreállítási pontok biztonsági másolataiban lévő adatok lekérésén. A Microsoft Azure Backup varázslóval azonosíthatja azokat a fájlokat vagy mappákat, amelyeket vissza kíván állítani a Windows Serverre. 

1. Nyissa meg a **Microsoft Azure Backup** beépülő modult. A megkereséséhez keressen rá a gépen a **Microsoft Azure Backup** kifejezésre.

    ![Biztonsági mentés függőben](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. A varázslóban kattintson az **Adatok helyreállítása** elemre az ügynökkonzol **Műveletek ablaktábláján**, amellyel elindíthatja az **Adatok helyreállítása** varázslót.

    ![Biztonsági mentés függőben](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. A **Kezdeti lépések** oldalon válassza az **Ez a kiszolgáló (kiszolgáló neve)** lehetőséget, és kattintson a **Tovább** gombra.

4. A **Helyreállítási mód kiválasztása** lapon válassza az **Egyes fájlok és mappák** lehetőséget, majd kattintson a **Tovább** gombra a helyreállítási pont kiválasztásának megkezdéséhez.
 
5. A **Kötet és dátum kiválasztása** oldalon válassza ki a visszaállítani kívánt fájlokat vagy mappákat tartalmazó kötetet, és kattintson a **Csatlakoztatás** elemre. Válassza ki a dátumot, és válassza ki a helyreállítási pontnak megfelelő időpontot a legördülő menüből. A **félkövérrel** szedett dátumok azt jelzik, hogy legalább egy helyreállítási pont áll rendelkezésre az adott napon.

    ![Biztonsági mentés függőben](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)
 
    Ha a **Csatlakoztatás** gombra kattint, az Azure Backup elérhetővé teszi a helyreállítási pontot lemezként. Keresse meg a fájlokat a lemezen, és végezze el a helyreállítást.

## <a name="restore-items-from-a-recovery-point"></a>Elemek visszaállítása a helyreállítási pontból

1. Ha a helyreállítási kötet csatlakoztatva van, kattintson a **Tallózás** gombra a Windows Explorer megnyitásához, és keresse meg a helyreállítani kívánt fájlokat és mappákat. 

    ![Biztonsági mentés függőben](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    A fájlokat közvetlenül a helyreállítási kötetből is megnyithatja és ellenőrizheti.

2. A Windows Explorerben másolja a fájlokat és/vagy mappákat, amelyeket vissza szeretne állítani, majd illessze be őket a kívánt helyre a kiszolgálón.

    ![Biztonsági mentés függőben](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. Ha befejezte a fájlok és/vagy mappák visszaállítását, az **Adatok helyreállítása** varázsló **Fájlok tallózása és visszaállítása** oldalán kattintson a **Leválasztás** elemre. 

    ![Biztonsági mentés függőben](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4.  Kattintson az **Igen** gombra, annak megerősítéséhez, hogy le kívánja választani a kötetet.

    A pillanatkép leválasztása után az ügynökkonzol **Feladatok** ablaktábláján a **Feladat befejezve** üzenet jelenik meg.

## <a name="next-steps"></a>További lépések

Ezzel befejeződik az oktatóanyag-sorozat, amely a Windows Server adatainak az Azure-ba történő biztonsági mentését és visszaállítását ismerteti. Az Azure Backuppal kapcsolatos további információért tekintse meg a titkosított virtuális gépek biztonsági mentéséhez készült PowerShell-mintát.

> [!div class="nextstepaction"]
> [Titkosított virtuális gép biztonsági mentése](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
