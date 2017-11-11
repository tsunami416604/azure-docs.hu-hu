---
title: "Készítsen biztonsági másolatot a Windows Server az Azure-bA |} Microsoft Docs"
description: "Az oktatóanyag adatokat a helyszíni Windows Server biztonsági mentéséről a Recovery Services-tároló."
services: back up
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
keywords: "Windows server biztonsági mentése; Készítsen biztonsági másolatot a windows server; biztonsági mentés és katasztrófa utáni helyreállítás"
ms.assetid: 
ms.service: back up
ms.workload: storage-back up-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: saurabhsensharma;markgal;
ms.custom: 
ms.openlocfilehash: 67a5c1494f2944e91fed2b077cf04e8906788c1e
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="back-up-windows-server-to-azure"></a>A Windows Server biztonság mentése az Azure-be


Azure Backup segítségével a Windows Server megvédeni a sérüléseket, a támadások és a katasztrófák. Azure biztonsági mentés a Microsoft Azure Recovery Services (MARS) ügynök néven egyszerűsített eszközt biztosít. A MARS agent telepítve van a Windows Server fájlok és mappák és kiszolgáló konfigurációs adatainak keresztül Windows Server rendszerállapot védelme érdekében. Ez az oktatóanyag azt ismerteti, hogyan használhatja a MARS Agent biztonsági mentése a Windows Server az Azure-bA. Ezen oktatóanyag segítségével megtanulhatja a következőket: 


> [!div class="checklist"]
> * Töltse le, és állítsa be a MARS Agent
> * Készítsen biztonsági másolatot alkalommal konfigurálása és a kiszolgáló biztonsági mentések adatmegőrzési ütemterv
> * Hajtsa végre az ad hoc biztonsági mentése


## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

Mielőtt készíthet biztonsági mentést a Windows Server, a biztonsági másolatok hely létrehozása, vagy az visszaállítási pontok, a rendszer ne tárolja. A [Recovery Services-tároló](backup-azure-recovery-services-vault-overview.md) egy olyan tároló, amely a Windows Server biztonsági mentés tárolja az Azure-ban. Recovery Services-tároló létrehozása az Azure-portálon az alábbi lépésekkel. 

1. A bal oldali menüben válassza **minden szolgáltatás** írja be a szolgáltatások listájában **Recovery Services**. Kattintson a **Recovery Services-tárolók**.

   ![Nyissa meg a Recovery Services-tároló](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2.  A **Recovery Services-tárolók** menüben kattintson a **Hozzáadás** elemre.

   ![Adja meg az adatokat tároló](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3.  Az a **Recovery Services-tároló** menü

    - Típus *myRecoveryServicesVault* a **neve**.
    - Az azonosító szerepel a jelenlegi előfizetés **előfizetés**.
    - A **erőforráscsoport**, jelölje be **meglévő** válassza *myResourceGroup*. Ha *myResourceGroup* nem létezik, válassza ki **hozzon létre új** és típus *myResourceGroup*. 
    - Az a **hely** legördülő menüben válasszon *Nyugat-Európa*.
    - Kattintson a **létrehozása** a Recovery Services-tároló létrehozásához.
 
Miután a tároló létrejött, megjelenik a Recovery Services-tárolók listájában.

## <a name="download-recovery-services-agent"></a>A Recovery Services agent letöltése

A Microsoft Azure Recovery Services (MARS) ügynök Windows Server és a Recovery Services-tároló közötti társítást hoz létre. Az alábbi eljárás ismerteti, hogyan töltse le az ügynököt a kiszolgálón.

1.  Válassza ki a listáról a Recovery Services-tárolók **myRecoveryServicesVault** az irányítópult megnyitásához.

   ![Adja meg az adatokat tároló](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2.  Kattintson a tároló irányítópult menü **biztonsági mentés**.

3.  Az a **biztonsági mentési cél** menüben:

    - a **a számítási feladatok futtató?**, jelölje be**helyszíni**, 
    - a **miről szeretne biztonsági másolatot készíteni?**, jelölje be **fájlok és mappák** és **rendszerállapot** 

    ![Adja meg az adatokat tároló](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)
    
4.  Kattintson a **infrastruktúra előkészítése** megnyitásához a **infrastruktúra előkészítése** menü.
5.  Az a **infrastruktúra előkészítése** menüben kattintson a **letöltése ügynök Windows vagy a Windows ügyfél** letölteni a *MARSAgentInstaller.exe*. 

    ![infrastruktúra előkészítése](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    A telepítő egy külön böngészőablakban megnyitja és a letöltéseket **MARSAgentInstaller.exe**.
 
6.  Előtt futtassa a letöltött fájlt, kattintson a **letöltése** töltse le és mentse a Prepare infrastruktúra panelen gombjára a **tárolói hitelesítő adatokat** fájlt. Ez a fájl szükség a MARS Agent összekapcsolása a Recovery Services-tároló.

    ![infrastruktúra előkészítése](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)
 
## <a name="install-and-register-the-agent"></a>Az ügynök telepítése és regisztrálása

1. Keresse meg és kattintson duplán a letöltött **MARSagentinstaller.exe**.
2. A **Microsoft Azure Recovery Services Ügynöktelepítő varázsló** jelenik meg. A varázsló en keresztül halad, adja meg a következő adatokat, amikor a rendszer kéri, kattintson **regisztrálása**.
    - A telepítés és a gyorsítótár mappa helyét.
    - Proxy server adatai, ha proxykiszolgálót segítségével csatlakozni az internethez.
    - A felhasználói nevet és jelszót adatokat egy hitelesített proxy használatakor.

    ![infrastruktúra előkészítése](./media/tutorial-backup-windows-server-to-azure/mars-installer.png) 

3. A varázsló befejezésekor kattintson **továbblépni a regisztrációra** , és adja meg a **tárolói hitelesítő adatokat** letöltött fájlt, az előző eljárásban.
 
4. Amikor a rendszer kéri, adja meg a Windows Server biztonsági mentések titkosításához titkosítás jelszavát. A Microsoft nem lehet helyreállítani a jelszót, adatvesztés mentse egy biztonságos helyre a jelszót.

5. Kattintson a **Befejezés** gombra. 

## <a name="configure-backup-and-retention"></a>Biztonsági mentési és adatmegőrzési konfigurálása

A Microsoft Azure Recovery Services Agent ügynök használatával ütemezni, amikor biztonsági mentést az Azure-ba, a Windows Server. A következő lépések végrehajtása a kiszolgálón, amelybe letöltötte az ügynököt.

1. Nyissa meg a Microsoft Azure Recovery Services-ügynököt. A megkereséséhez keressen rá a gépen a **Microsoft Azure Backup** kifejezésre.

2.  Kattintson a Recovery Services agent konzolon **biztonsági mentés ütemezése** alatt a **műveletek panel**.

    ![infrastruktúra előkészítése](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. Kattintson a **következő** lehetőségre, és navigáljon a **kiválasztja az elemeket a biztonsági mentést** lap.

4. Kattintson a **elemek hozzáadása** és a megnyíló párbeszédpanelen válassza ki **rendszerállapot** és fájlok vagy mappák, amelyet szeretne biztonsági másolatot készíteni. Ezután kattintson az **OK** gombra.

5. Kattintson a **Tovább** gombra.

6. Az a **biztonsági mentési ütemezés megadása (rendszerállapot)** adja meg azokat a nap vagy hét, amikor biztonsági mentést kell rendszerállapot elindul, és kattintson **tovább** 

7.  Az a **válassza ki az adatmegőrzési (rendszerállapot)** lapon válassza ki az adatmegőrzési a rendszerállapot biztonsági másolatot, majd kattintson **tovább**
8. Hasonlóképpen válassza ki a kijelölt fájlok és mappák biztonsági mentési ütemezés és a megőrzési házirend. 
8.  Az a **válassza a kezdeti biztonsági mentés típusa** lapon, hagyja a beállítást **automatikusan a hálózaton keresztül** kiválasztva, és kattintson **következő**.
9.  Az a **megerősítő** lapon tekintse át az adatokat, és kattintson a **Befejezés**.
10. Miután a varázsló befejezte a biztonsági mentési ütemezés létrehozását, kattintson a **Bezárás** gombra.

## <a name="perform-an-ad-hoc-back-up"></a>Hajtsa végre az ad hoc biztonsági mentése

Az ütemezés hozott létre, ha biztonsági mentési feladatok futtatásához. Azonban nem készített biztonsági másolatot a kiszolgálóról. Vész helyreállítási ajánlott annak biztosítására, a kiszolgáló adatrugalmasság egy igény szerinti biztonsági mentés futtatására.

1.  Kattintson a Microsoft Azure Recovery Services agent konzolon **biztonsági másolat készítése most**.

    ![infrastruktúra előkészítése](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2.  Az a **biztonsági másolat készítése most** varázsló, jelölje be egy **fájlok és mappák** vagy **rendszerállapot** készítsen biztonsági másolatot, és kattintson a kívánt **tovább** 
3. Az a **megerősítő** lapján tekintse át a beállításokat, amelyek a **biztonsági másolat készítése most** varázsló használatával készítsen biztonsági másolatot a kiszolgálóról. Ezután kattintson a **Biztonsági mentés** gombra.
4.  A varázsló bezárásához kattintson a **Bezárás** gombra. Ha a biztonságimásolat-folyamatának befejeződése előtt bezárja a varázslót, a varázsló továbbra is fut a háttérben.
4.  A kezdeti biztonsági mentés befejezése után **feladata befejezve** állapota megjelenik a **feladatok** a MARS agent konzol ablaktáblájában.


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban használt Azure portálon: 
 
> [!div class="checklist"] 
> * Recovery Services-tároló létrehozása 
> * A Microsoft Azure Recovery Services agent letöltése 
> * Az ügynök telepítése 
> * A Windows Server biztonsági mentés konfigurálása 
> * Egy igény szerinti biztonsági mentést. 

Továbbra is a következő oktatóanyag helyreállítani a fájlt az Azure-ból Windows Server

> [!div class="nextstepaction"] 
> [Windows Server az Azure-ból fájlok visszaállítása](./tutorial-backup-restore-files-windows-server.md) 

