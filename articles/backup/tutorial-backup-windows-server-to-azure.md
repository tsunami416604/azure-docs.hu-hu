---
title: "Biztonsági másolat készítése a Windows Serverről az Azure-ba | Microsoft Docs"
description: "Ez az oktatóanyag részletesen ismerteti a helyszíni Windows Serverek helyreállítási tárba történő biztonsági mentését."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
keywords: "windows server biztonsági másolat; windows server biztonsági mentése; biztonsági mentés és vészhelyreállítás"
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 2/14/2018
ms.author: saurabhsensharma;markgal;
ms.custom: mvc
ms.openlocfilehash: de0398b7f295894a3cac7c67b68cef237c3ac2c2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="back-up-windows-server-to-azure"></a>Windows Server biztonsági mentése az Azure-ba


Az Azure Backuppal megvédheti a Windows Servert a sérülésektől, támadásoktól és katasztrófáktól. Az Azure Backup tartalmaz egy egyszerű eszközt, amelynek a neve Microsoft Azure Recovery Services- (MARS-) ügynök. A MARS-ügynök a Windows Serverre települ, és a Windows Server rendszerállapotán keresztül megvédi a fájlokat, a mappákat és a kiszolgáló konfigurációs információit. Ez az oktatóanyag azt ismerteti, hogyan készíthető biztonsági másolat a Windows Serverről az Azure-ba a MARS-ügynökkel. Ezen oktatóanyag segítségével megtanulhatja a következőket: 


> [!div class="checklist"]
> * A MARS-ügynök letöltése és beállítása
> * A kiszolgáló biztonsági mentési időpontjainak és a másolatok megőrzési ütemezésének konfigurálása
> * Igény szerinti biztonsági mentés


## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

Mielőtt biztonsági másolatot készíthetne a Windows Serverről, létre kell hoznia egy helyet a biztonsági másolatok vagy helyreállítási pontok tárolásához. A [helyreállítási tár](backup-azure-recovery-services-vault-overview.md) egy olyan Azure-beli tároló, amely a Windows Server biztonsági másolatait tárolja. Az alábbi lépésekkel hozhat létre helyreállítási tárt az Azure Portalon. 

1. A bal oldali menüben válassza a **Minden szolgáltatás** elemet, és a szolgáltatások listájába írja be a **Recovery Services** kifejezést. Kattintson a **Helyreállítási tárak** elemre.

   ![Helyreállítási tár megnyitása](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2.  A **Recovery Services-tárolók** menüben kattintson a **Hozzáadás** elemre.

   ![a tár információinak megadása](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3.  A **Helyreállítási tár** menüben:

    - Írja be a *myRecoveryServicesVault* értéket a **Név** mezőbe.
    - Megjelenik az aktuális előfizetési azonosító az **Előfizetés** mezőben.
    - Az **Erőforráscsoport** mezőben válassza a **Meglévő használata** és a *myResourceGroup* elemet. Ha a *myResourceGroup* nem létezik, válassza ki az **Új létrehozása** elemet, és írja be a *myResourceGroup* kifejezést. 
    - A **Hely** legördülő menüből válassza a *Nyugat-Európa* elemet.
    - Kattintson a **Létrehozás** gombra a helyreállítási tár létrehozásához.
 
Miután a tároló létrejött, megjelenik a Recovery Services-tárolók listájában.

## <a name="download-recovery-services-agent"></a>A Recovery Services-ügynök letöltése

A Microsoft Azure Recovery Services- (MARS-) ügynök egy társítást hoz létre a Windows Server és a helyreállítási tár között. Az alábbi eljárás azt ismerteti, hogyan töltheti le az ügynököt a kiszolgálóra.

1.  A helyreállítási tárak listájából válassza a **myRecoveryServicesVault** tárolót az irányítópult megnyitásához.

   ![a tár információinak megadása](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2.  A tároló irányítópultjának menüjében kattintson a **Biztonsági mentés** elemre.

3.  A **Biztonsági mentés célja** menüben:

    - a **Hol futnak az alkalmazások és szolgáltatások?** pontnál válassza a **Helyszíni** lehetőséget, 
    - a **Miről szeretne biztonsági másolatot készíteni?** pontnál válassza a **Fájlok és mappák** és a **Rendszerállapot** elemet 

    ![a tár információinak megadása](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)
    
4.  Kattintson az **Infrastruktúra előkészítése** elemre az **Infrastruktúra előkészítése** menü megnyitásához.
5.  Az **Infrastruktúra előkészítése** menüben kattintson **A Windows Server- vagy a Windows-ügyfél ügynökének letöltése** elemre a *MARSAgentInstaller.exe* letöltéséhez. 

    ![infrastruktúra előkészítése](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    A telepítő megnyit egy külön böngészőablakot, és letölti a **MARSAgentInstaller.exe** fájlt.
 
6.  A letöltött fájl futtatása előtt kattintson az Infrastruktúra előkészítése panel **Letöltés** gombjára, és mentse a **Tár hitelesítő adatai** fájlt. Ez a fájl szükséges a MARS-ügynök helyreállítási tárhoz való csatlakozásához.

    ![infrastruktúra előkészítése](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)
 
## <a name="install-and-register-the-agent"></a>Az ügynök telepítése és regisztrálása

1. Keresse meg a letöltött **MARSagentinstaller.exe** fájlt, és kattintson rá duplán.
2. Ekkor megjelenik a **Microsoft Azure Recovery Services-ügynök telepítővarázslója**. Amikor a varázsló kéri, adja meg az alábbi adatokat, majd kattintson a **Regisztráció** elemre.
    - A telepítés és a gyorsítótár mappájának helye.
    - A proxykiszolgáló információi, ha proxykiszolgálóval csatlakozik az internethez.
    - Az Ön felhasználóneve és jelszava, ha hitelesített proxyt használ.

    ![infrastruktúra előkészítése](./media/tutorial-backup-windows-server-to-azure/mars-installer.png) 

3. A varázsló befejezésekor kattintson a **Tovább a regisztrációra** elemre, és adja meg a **tároló hitelesítő adatait** tartalmazó fájlt, amelyet az előző eljárás során töltött le.
 
4. Amikor a rendszer kéri, adja meg a Windows Server biztonsági másolatainak titkosításához szükséges jelszót. A Microsoft nem tudja helyreállítani az elveszített jelszót, ezért mentse biztonságos helyre.

5. Kattintson a **Befejezés** gombra. 

## <a name="configure-backup-and-retention"></a>Biztonsági mentés és megőrzés konfigurálása

A Microsoft Azure Recovery Services-ügynökkel ütemezheti a Windows Server Azure-ba történő biztonsági mentéseit. Hajtsa végre a következő lépéseket azon a kiszolgálón, amelyre letöltötte az ügynököt.

1. Nyissa meg a Microsoft Azure Recovery Services-ügynököt. A megkereséséhez keressen rá a gépen a **Microsoft Azure Backup** kifejezésre.

2.  A Recovery Services-ügynökkonzolban kattintson a **Műveletek ablaktábla** **Biztonsági mentés ütemezése** elemére.

    ![infrastruktúra előkészítése](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. A **Tovább** gombra kattintva lépjen tovább az **Elemek kiválasztása biztonsági mentéshez** lapra.

4. Kattintson az **Elemek hozzáadása** gombra, és a megnyíló párbeszédpanelen válassza a **Rendszerállapot** lehetőséget, valamint a fájlokat vagy mappákat, amelyekről biztonsági másolatot kíván készíteni. Ezután kattintson az **OK** gombra.

5. Kattintson a **Tovább** gombra.

6. A **Biztonsági mentési ütemezés megadása (Rendszerállapot)** lapon adja meg a nap vagy a hét azon időpontját, amikor aktiválni kívánja a Rendszerállapot biztonsági mentését, majd kattintson a **Tovább** gombra 

7.  Az **Adatmegőrzési szabályzat kijelölése (Rendszerállapot)** lapon válassza ki a rendszerállapot biztonsági mentésére vonatkozó megőrzési szabályzatot, majd kattintson a **Tovább** gombra
8. Hasonló módon válassza ki a kijelölt fájlok és mappák biztonsági mentési ütemezését és megőrzési szabályzatát. 
8.  A **Kezdeti biztonsági mentési típus kiválasztása** lapon hagyja bejelölve az **Automatikusan a hálózaton keresztül** beállítást, majd kattintson a **Tovább** gombra.
9.  A **Jóváhagyás** lapon ellenőrizze az információkat, majd kattintson a **Befejezés** gombra.
10. Miután a varázsló befejezte a biztonsági mentési ütemezés létrehozását, kattintson a **Bezárás** gombra.

## <a name="perform-an-ad-hoc-back-up"></a>Igény szerinti biztonsági mentés

Létrehozta a biztonsági mentési feladatok futtatásának ütemezését. A kiszolgálóról azonban még nem készített biztonsági másolatot. A vészhelyreállítás egyik ajánlott eljárása egy igény szerinti biztonsági mentés végrehajtása, amellyel biztosíthatja a kiszolgáló adatainak rugalmasságát.

1.  A Microsoft Azure Recovery Services-ügynökkonzolon kattintson az **Azonnali biztonsági mentés** parancsra.

    ![infrastruktúra előkészítése](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2.  Az **Azonnali biztonsági mentés** varázslóban válassza a **Fájlok és mappák** vagy a **Rendszerállapot** lehetőségek közül azt, amelyikről biztonsági másolatot kíván készíteni, majd kattintson a **Tovább** gombra. 
3. A **Jóváhagyás** lapon tekintse át azokat a beállításokat, amelyeket az **Azonnali biztonsági mentés** varázsló a kiszolgáló biztonsági mentéséhez fog használni. Ezután kattintson a **Biztonsági mentés** gombra.
4.  A varázsló bezárásához kattintson a **Bezárás** gombra. Ha bezárja a varázslót a biztonsági mentési folyamat befejezése előtt, a varázsló továbbra is fut a háttérben.
4.  A kezdeti biztonsági mentés befejezése után a **Feladat befejezve** állapot jelenik meg a MARS-ügynökkonzol **Feladatok** ablaktáblájában.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket végezte el az Azure Portalon: 
 
> [!div class="checklist"] 
> * Recovery Services-tároló létrehozása 
> * A Microsoft Azure Recovery Services-ügynök letöltése 
> * Az ügynök telepítése 
> * A Windows Server biztonsági mentésének konfigurálása 
> * Igény szerinti biztonsági mentés elvégzése 

Folytassa a következő oktatóanyaggal az Azure-beli fájlok Windows Serverre történő helyreállításához

> [!div class="nextstepaction"] 
> [Azure-beli fájlok visszaállítása Windows Serverre](./tutorial-backup-restore-files-windows-server.md) 

