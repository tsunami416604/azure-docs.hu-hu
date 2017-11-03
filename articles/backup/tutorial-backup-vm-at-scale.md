---
title: "Az Azure virtuális gépek léptékű |} Microsoft Docs"
description: "Egyszerre több virtuális gépek biztonsági mentésére az Azure-bA"
services: backup
keywords: "virtuális gép biztonsági mentése; virtuális gép biztonsági mentése; Készítsen biztonsági másolatot a virtuális gép; biztonsági mentési vm; Azure virtuális gép; biztonsági mentés biztonsági mentés és katasztrófa utáni helyreállítás"
author: markgalioto
ms.author: markgal
ms.date: 09/16/2017
ms.topic: article
ms.service: backup
ms.openlocfilehash: f144db921a8b2d01dbfe883a48574c4fabdae6f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Azure portál használatával készítsen biztonsági másolatot a több virtuális gép

Amikor az adatok biztonsági másolatát az Azure-ban, az Azure Recovery Services-tároló neve erőforrás tárolja ezeket az adatokat. A Recovery Services tároló-erőforrás áll rendelkezésre az Azure szolgáltatások, a beállítások menüből. A Recovery Services-tároló, a beállítások menüben, az Azure-szolgáltatások integrálva előnyös egyszerűen nagyon adatok biztonsági mentéséhez. Azonban külön-külön használata minden egyes adatbázis vagy a virtuális gép az üzleti fárasztó. Mi történik, ha szeretné az összes virtuális gép egy részleg vagy egyetlen helyen az adatok biztonsági mentéséhez? Egyszerű biztonsági mentési házirend létrehozása és a házirend alkalmazása a kívánt virtuális gépek biztonsági mentése több virtuális gép. Ez az oktatóanyag azt ismerteti, hogyan:

> [!div class="checklist"]
> * Recovery Services-tároló létrehozása
> * A biztonsági mentési házirend meghatározása
> * Több virtuális gép védelmét a biztonsági mentési házirend alkalmazása
> * Az igény a védett virtuális gépek biztonsági mentési feladatot indít

## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services-tároló tartalmazza, a biztonsági mentési adatokat, és a biztonsági mentési házirend alkalmazása a védett virtuális gépekre. A virtuális gépek biztonsági mentése egy helyi folyamat. Nem készíthet biztonsági másolatot a virtuális gépek egyik helyről a Recovery Services-tároló egy másik helyen. Igen minden Azure-beli hely, amely rendelkezik a virtuális gépek biztonsági mentését, legalább egy Recovery Services-tároló léteznie kell az adott helyre.

1. A bal oldali menüben válassza **további szolgáltatások** írja be a szolgáltatások listájában *Recovery Services*. A gépelés során a rendszer szűri az erőforrások listáját. Amikor megjelenik a listában Recovery Services-tárolók, válassza ki azt a Recovery Services-tárolók menü megnyitásához.

    ![Recovery Services-tároló menü megnyitása](./media/tutorial-backup-vm-at-scale/full-browser-open-rs-vault.png) <br/>

2. Az a **Recovery Services-tárolók** menüben kattintson a **Hozzáadás** a Recovery Services-tároló menü megnyitásához.

    ![Nyissa meg a tároló menü](./media/tutorial-backup-vm-at-scale/provide-vault-detail-2.png)

3. A helyreállítási szolgáltatások tároló menü 

    - Típus *myRecoveryServicesVault* a **neve**,
    - Az azonosító szerepel a jelenlegi előfizetés **előfizetés**. Ha további előfizetéssel rendelkezik, megadhatja, hogy az új tároló másik előfizetést.
    - A **erőforráscsoport** válasszon **meglévő** válassza *myResourceGroup*. Ha *myResourceGroup* nem létezik, válassza ki **hozzon létre új** és típus *myResourceGroup*.
    - Az a **hely** legördülő menüben válasszon *Nyugat-Európa*.
    - Kattintson a **létrehozása** a Recovery Services-tároló létrehozásához.

A Recovery Services-tárolónak ugyanazon a helyen, mint a védett virtuális gépek kell lennie. Ha több régióba virtuális gépek, hozzon létre a Recovery Services-tároló minden régióban. Ebben az oktatóanyagban létrehoz a Recovery Services-tároló *Nyugat-Európában* mert, amely akkor, ha *myVM* (a virtuális gép létre a gyors üzembe helyezés) hozták létre.

A Recovery Services-tároló létrehozása több percet is igénybe vehet. Figyelje az állapotértesítéseket a portál jobb felső területén. Miután a tároló létrejött, megjelenik a Recovery Services-tárolók listájában.

Recovery Services-tároló létrehozásakor alapértelmezés szerint a tárolóban van georedundáns tárolást. Adatok rugalmasságot biztosítani, replikálja az adatokat georedundáns tárolás többször két Azure-régiók közötti.

## <a name="set-backup-policy-to-protect-vms"></a>A virtuális gépek védelme biztonsági mentési házirendjének beállítása

Miután létrehozta a Recovery Services-tároló, a következő lépésre konfigurálhatja az adatok a tárolóban, de a biztonsági mentési házirend beállítása. A biztonsági mentési házirend milyen gyakran és mikor készít-e a helyreállítási pontok ütemezését. A házirend emellett tartalmazza a helyreállítási pontok megőrzési tartományát. Ez az oktatóanyag Tételezzük fel, hogy az üzleti egy sport összetett, a szállodák, stadium, és éttermekben és engedmények, és az adatok a virtuális gépek védelmét. Az alábbi lépéseket a pénzügyi adatairól biztonsági mentési házirendet hozhat létre.

1. Válassza ki a listáról a Recovery Services-tárolók **myRecoveryServicesVault** az irányítópult megnyitásához.

   ![A forgatókönyv menü megnyitása](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

2. Kattintson a tároló irányítópult menü **biztonsági mentés** a biztonsági mentés menü megnyitásához.

3. A biztonsági mentési cél menüben a a **a számítási feladatok futtató** legördülő menüben válasszon *Azure*. Az a **miről szeretne biztonsági másolatot készíteni** legördülő listából válassza a *virtuális gép*, és kattintson a **biztonsági mentési**.

    Ezek a műveletek a Recovery Services-tároló készítse elő a virtuális géppel való interakció. Helyreállítási szolgáltatások tárolók naponta visszaállítási pont létrehozása, amely 30 napig őrzi meg a visszaállítási pontok alapértelmezett házirenddel rendelkezhetnek.

    ![A forgatókönyv menü megnyitása](./media/tutorial-backup-vm-at-scale/backup-goal.png)

4. Egy új házirendet a biztonsági mentési házirend menü létrehozása a **válassza ki a biztonsági mentési házirend** legördülő menüben válassza *hozzon létre új*.

    ![Számítási feladat kiválasztása](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

5. Az a **biztonsági mentési házirend** menüben a **házirendnév** típus *pénzügyi*. Adja meg a biztonsági mentési házirendet a következő módosításokat: 
    - A **biztonsági mentési gyakorisága** állítsa be az időzóna *középidő*. Mivel a sport komplex Texas, a tulajdonos szeretne beállítani a helyi ütemezését. Hagyja a biztonsági mentés gyakoriságát napi 3 órakor: 30 beállítása.
    - A **napi biztonsági mentési pontok megőrzése**, állítsa a 90 naponta.
    - A **heti biztonsági mentési pontok megőrzése**, használja a *hétfő* visszaállítási pont, és azokat megőrizheti 52 hétig.
    - A **havi biztonsági mentési pontok megőrzése**, használja a hónap első vasárnaptól visszaállítási pontot, és azokat megőrizheti 36 hónapig.
    - Törölje a **éves biztonsági mentési pontok megőrzése** lehetőséget. Pénzügyi vezetője nem szeretné 36 hónapnál hosszabb adatok megőrzéséhez.
    - A biztonsági mentési szabályzat létrehozásához kattintson az **OK** gombra.

    ![Számítási feladat kiválasztása](./media/tutorial-backup-vm-at-scale/set-new-policy.png) 

    Miután létrehozta a biztonsági mentési házirend, a virtuális gépek társítja a házirendet.

6. Az a **válassza ki a virtuális gépek** párbeszédpanelen válasszon *myVM* kattintson **OK** a biztonsági mentési házirend központi telepítése a virtuális gépek. 

    Az összes virtuális gép ugyanazon a helyen, és nem már társítva a biztonsági mentési házirend jelennek meg. *myVMH1* és *myVMR1* társítva van kiválasztva a *pénzügyi* házirend.

    ![Számítási feladat kiválasztása](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png) 

    A telepítés befejezését követően kap értesítést, hogy a telepítés sikeresen befejeződött.

## <a name="initial-backup"></a>Kezdeti biztonsági mentés

A Recovery Services-tárolók biztonsági mentésének engedélyezését, de nem lett létrehozva egy kezdeti biztonsági másolatot. Katasztrófa utáni helyreállítás ajánlott eljárás az első biztonsági mentés is, hogy az adatok védelmét. 

Egy igény szerinti biztonsági mentési feladat futtatásához:

1. A tároló irányítópultján kattintson **3** alatt **biztonsági mentés elemek**, a biztonsági mentés elemek menü megnyitásához.

    ![Beállítások ikon](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    A **biztonsági mentés elemek** menü megnyitása.

2. Az a **biztonsági mentés elemek** menüben kattintson a **Azure virtuális gép** a tárolóhoz rendelt virtuális gépek listájának megnyitásához.

    ![Beállítások ikon](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Megnyílik a **Biztonsági mentési elemek** listája.

    ![Biztonsági mentési feladat elindul](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

3. A **Biztonsági mentési elemek** listában kattintson a három pontra **...** a helyi menü megnyitásához.

4. A helyi menüben válassza ki a **biztonsági mentés most**.

    ![Helyi menü](./media/tutorial-backup-vm-at-scale/context-menu.png)

    A biztonsági mentés most menü megnyitása.

5. A biztonsági mentés most menüben adja meg a helyreállítási pont megőrzése, és kattintson az elmúlt nap **biztonsági mentés**.

    ![adja meg az utolsó napot, ameddig Biztonsági mentés helyreállítási pontját meg kívánja őrizni](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    Az üzembehelyezési értesítések értesítik, hogy a biztonsági mentési feladat elindult, és hogy a feladat állapotát a Biztonsági mentési feladatok oldalon figyelheti. A virtuális gép méretétől függően a kezdeti biztonsági másolatot készít eltarthat egy ideig.

    A kezdeti biztonsági mentési feladat befejeződése után megtekintheti annak állapotát a biztonsági mentési feladat menüben. Az igény szerinti biztonsági mentési feladat létrehozása a kezdeti visszaállítási pontot *myVM*. Ha más virtuális gépek biztonsági mentésére, ismételje meg ezeket a lépéseket minden virtuális gép. 

    ![Biztonsági mentési feladatok csempe](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)
  
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, a folytatáshoz a következő útmutatókból együttműködni, üríti az oktatóanyagban szereplő létrejött erőforrásokat. Ha nem tervezi folytatja, ez az oktatóanyag az Azure-portálon létrehozott összes erőforrás törléséhez használja a következő lépéseket.

1. A a **myRecoveryServicesVault** irányítópultján kattintson **3** alatt **biztonsági mentés elemek**, a biztonsági mentés elemek menü megnyitásához.

    ![Beállítások ikon](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)


2. Az a **biztonsági mentés elemek** menüben kattintson a **Azure virtuális gép** a tárolóhoz rendelt virtuális gépek listájának megnyitásához.

    ![Beállítások ikon](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Megnyílik a **Biztonsági mentési elemek** listája.

3. Az a **biztonsági mentés elemek** menüben kattintson a három pont a helyi menü megnyitásához.

    ![Beállítások ikon](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

4. A helyi menüben válassza ki a **Stop biztonsági mentés** biztonsági mentés leállítása menü megnyitásához. 

    ![Beállítások ikon](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

5. Az a **állítsa le a biztonsági mentés** menüben kattintson a felső legördülő menüre, és válassza **biztonságimásolat-adatok törlése**.

6. Az a **írja be a biztonsági másolati elem nevét** párbeszédpanel, írja be *myVM*.
 
7. Ha a biztonsági mentési elem ellenőrzése (egy pipa jelenik meg), **Stop biztonsági mentés** gomb engedélyezve van. Kattintson a **állítsa le a biztonsági mentés** állítsa le a házirendet, és a visszaállítási pontok törléséhez. 

    ![Kattintson a Leállítás biztonsági másolatot tároló törlése](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png).

8. Az a **myRecoveryServicesVault** menüben kattintson a **törlése**.

    ![Kattintson a Leállítás biztonsági másolatot tároló törlése](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    A tároló törlése után visszatérhet a Recovery Services-tárolók listája.


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban használt Azure portálon:

> [!div class="checklist"]
> * Recovery Services-tároló létrehozása
> * Állítsa be a tároló virtuális gépek védelme
> * Hozzon létre egy egyéni biztonsági mentési és adatmegőrzési házirend
> * Több virtuális gép védelmét a házirend kijelölése
> * Indítás, igény szerinti biztonsági feliratkozott a virtuális gépek

Folytassa a következő oktatóanyag a lemezről egy Azure virtuális gép visszaállítására. 

> [!div class="nextstepaction"]
> [Állítsa vissza a virtuális gépek parancssori felület használatával](./tutorial-restore-disk.md)
