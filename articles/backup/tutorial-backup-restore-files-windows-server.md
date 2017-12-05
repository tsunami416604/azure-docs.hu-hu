---
title: "Fájlok helyreállítása az Azure-ból egy Windows Server |} Microsoft Docs"
description: "Ez az oktatóanyag az Azure-ból egy Windows Server helyreállításával elemek részletezi."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
keywords: "Windows server biztonsági mentése; fájlok a windows Server-kiszolgáló visszaállítása biztonsági mentés és katasztrófa utáni helyreállítás"
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2017
ms.author: saurabhsensharma;markgal;
ms.custom: mvc
ms.openlocfilehash: b5f77ec04ef6d267583a6dc6a4476f118a4d0f74
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Fájlok helyreállítása az Azure-ból egy Windows Server

Azure biztonsági mentés lehetővé teszi, hogy a helyreállítás az egyes elemek a Windows Server biztonsági másolatból. Fájlok helyreállítása akkor hasznos, ha véletlenül törölt fájlok gyorsan kell visszaállítani. Ez az oktatóanyag bemutatja, hogyan használhatja a Microsoft Azure Recovery Services Agent (MARS) ügynök elemek helyreállítása biztonsági másolatból már elvégezte az Azure-ban. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Elindítani a helyreállítást az egyes elemek 
> * Válasszon ki egy helyreállítási pontot 
> * A helyreállítási pontból elemek visszaállítása

Ez az oktatóanyag azt feltételezi, hogy már elvégezte a lépéseket [biztonsági mentése a Windows Server Azure](backup-configure-vault.md) és van legalább egy biztonsági mentése a Windows Server-fájlok az Azure-ban.

## <a name="initiate-recovery-of-individual-items"></a>Elindítani a helyreállítást az egyes elemek

A Microsoft Azure Recovery Services (MARS) ügynök telepítve van a Microsoft Azure Backup nevű hasznos felhasználói felület varázsló. A Microsoft Azure biztonsági mentés varázsló használható a Microsoft Azure Recovery Services (MARS) ügynök az Azure-ban tárolt helyreállítási pontokról való letöltésének engedélyezése a biztonsági mentési adatokat. A Microsoft Azure biztonsági mentés varázsló segítségével azonosíthatja a fájlok vagy mappák Windows Server visszaállítja. 

1. Nyissa meg a **a Microsoft Azure Backup szolgáltatás** beépülő modult. A megkereséséhez keressen rá a gépen a **Microsoft Azure Backup** kifejezésre.

    ![Biztonsági mentés függőben](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. A varázslóban kattintson **adatok helyreállítása** a a **műveletek panel** az ügynök konzol elindítani a **adatok helyreállítása** varázsló.

    ![Biztonsági mentés függőben](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. Az a **bevezetés** lapon jelölje be **ehhez a kiszolgálóhoz (kiszolgálónév)** kattintson **tovább**.

4. A a **válassza ki a helyreállítási mód** lapon, válassza ki **egyes fájlok és mappák** majd **tovább** a helyreállítási pont kiválasztása megkezdéséhez.
 
5. Az a **mennyiség kiválasztása és a dátum** lapon, válassza ki a kötetet a fájlok vagy mappák visszaállítása, és kattintson a kívánt tartalmazó **csatlakoztatási**. Válassza ki a dátumot, és válassza ki a megfelelő a legördülő menüből, amely megfelel a helyreállítási pont. A dátumok **félkövér** legalább egy helyreállítási pont rendelkezésre állását jelzi az adott napon.

    ![Biztonsági mentés függőben](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)
 
    Amikor rákattint **csatlakoztatási**, Azure Backup szolgáltatás elérhetővé teszi a helyreállítási pontok egy lemezt. Keresse meg és a fájlok helyreállítása a lemezről.

## <a name="restore-items-from-a-recovery-point"></a>A helyreállítási pontból elemek visszaállítása

1. Ha a helyreállítási kötet csatlakoztatva van, kattintson a **Tallózás** nyissa meg a Windows Intézőt, és keresse meg a fájlok és a helyreállítani kívánt mappákat. 

    ![Biztonsági mentés függőben](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    Nyissa meg a fájlok közvetlenül a helyreállítási kötet, és ellenőrizze a fájlokat.

2. A Windows Intézőben másolja a fájlokat és/vagy mappákat szeretné visszaállítani, majd illessze be a kívánt helyre a kiszolgálón.

    ![Biztonsági mentés függőben](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. Ha elkészült a fájlok és/vagy mappák visszaállítása a a **tallózással keresse meg és a helyreállítási fájlokat** oldalán a **adatok helyreállítása** varázsló, kattintson a **leválasztási**. 

    ![Biztonsági mentés függőben](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4.  Kattintson a **Igen** annak ellenőrzéséhez, hogy szeretné-e a kötet leválasztása.

    A pillanatkép nem csatlakoztatott, amint **feladat befejeződött** jelenik meg a **feladatok** ablaktáblán az ügynök konzolon.

## <a name="next-steps"></a>Következő lépések

Ezzel befejezte az oktatóanyagok a biztonsági mentése és visszaállítása a Windows Server-adatok az Azure-bA. Azure biztonsági mentéssel kapcsolatos további tudnivalókért tekintse meg a PowerShell minta titkosított virtuális gépek biztonsági mentését.

> [!div class="nextstepaction"]
> [Készítsen biztonsági másolatot titkosított virtuális gép](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
