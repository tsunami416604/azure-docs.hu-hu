---
title: Azure-beli virtuális gépek nagy léptékű biztonsági mentése
description: Egyszerre több virtuális gép biztonsági mentése az Azure-ba
services: backup
keywords: virtuális gép biztonsági mentése; vm biztonsági mentése; Azure-beli virtuális gép biztonsági mentése; biztonsági mentés és vészhelyreállítás
author: markgalioto
ms.author: markgal
ms.date: 2/14/2018
ms.topic: tutorial
ms.service: backup
ms.custom: mvc
ms.openlocfilehash: dfe561e7a7231c8e7f9465819a01cd4b0a35f47c
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434540"
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Több virtuális gép biztonsági mentése az Azure Portalon

Amikor adatok biztonsági mentését végzi az Azure-ban, egy Recovery Services-tároló nevű Azure-erőforrásban tárolja a másolatokat. A helyreállításitár-erőforrás a legtöbb Azure-szolgáltatás Beállítások menüjéből elérhető. Az Azure-szolgáltatások Beállítások menüjébe integrált helyreállítási tár előnye, hogy könnyen lehet biztonsági másolatot készíteni az adatokról. A cég vagy intézmény adatbázisainak vagy virtuális gépeinek egyenkénti kezelése azonban fárasztó feladat. Mi történik, ha egy részleg vagy egy helyszín összes virtuális gépének adatairól szeretne biztonsági mentést készíteni? Könnyedén elvégezheti egyszerre több virtuális gép biztonsági mentését, ha létrehoz egy, az érintett virtuális gépekre vonatkozó biztonsági mentési szabályzatot. Ez az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Recovery Services-tároló létrehozása
> * Biztonsági mentési szabályzat meghatározása
> * A biztonsági mentési szabályzat alkalmazása több virtuális gép védelme érdekében
> * A védett virtuális gépek biztonsági mentési feladatának manuális aktiválása

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A helyreállítási tár tartalmazza a biztonsági mentési adatokat és a védett virtuális gépekre vonatkozó biztonsági mentési szabályzatot. A virtuális gépek biztonsági mentése egy helyi folyamat. Nem lehet biztonsági mentést készíteni egy adott helyen található virtuális gépről egy másik helyen található helyreállítási tárba. Ezért minden olyan Azure-helyen, ahol biztonsági mentésre szoruló virtuális gépek vannak, legalább egy helyreállítási tárnak kell lennie.

1. A bal oldali menüben válassza a **Minden szolgáltatás** elemet, és a szolgáltatások listájába írja be a *Recovery Services* kifejezést. A gépelés során a rendszer szűri az erőforrások listáját. Amikor meglátja a Recovery Services-tárolók elemet a listában, kattintson rá a Recovery Services-tárolók menü megnyitásához.

    ![A Recovery Services-tárolók menü megnyitása](./media/tutorial-backup-vm-at-scale/full-browser-open-rs-vault.png) <br/>

2. A **Recovery Services-tárolók** menüben kattintson a **Hozzáadás** gombra a helyreállítási tárok menüjének megnyitásához.

    ![A tárolók menüjének megnyitása](./media/tutorial-backup-vm-at-scale/provide-vault-detail-2.png)

3. A helyreállítási tár menüjében:

    - Írja be a *myRecoveryServicesVault* értéket a **Név** mezőbe.
    - Megjelenik az aktuális előfizetési azonosító az **Előfizetés** mezőben. Ha több előfizetéssel is rendelkezik, egy másik előfizetést is választhat az új tárolóhoz.
    - Az **Erőforráscsoport** mezőben válassza a **Meglévő használata** és a *myResourceGroup* elemet. Ha a *myResourceGroup* nem létezik, válassza ki az **Új létrehozása** elemet, és írja be a *myResourceGroup* kifejezést.
    - A **Hely** legördülő menüből válassza a *Nyugat-Európa* elemet.
    - Kattintson a **Létrehozás** gombra a helyreállítási tár létrehozásához.

A helyreállítási tárnak ugyanazon a helyen kell lennie, mint a virtuális gépeknek, amelyeknek védelmet biztosít. Ha több régióban rendelkezik virtuális gépekkel, minden régióban hozzon létre egy Recovery Services-tárolót. Ebben az oktatóanyagban *Nyugat-Európában* hozzuk létre a helyreállítási tárat, mert itt jött létre a *myVM* (a rövid útmutatóban létrehozott virtuális gép).

A Recovery Services-tároló létrehozása több percet is igénybe vehet. Figyelje az állapotértesítéseket a portál jobb felső területén. Miután a tároló létrejött, megjelenik a Recovery Services-tárolók listájában.

A létrehozott helyreállítási tárak alapértelmezés szerint georedundáns tárolással rendelkeznek. Az adatok rugalmassága érdekében a georedundáns tárolás többször replikálja az adatokat két Azure-régió között.

## <a name="set-backup-policy-to-protect-vms"></a>Biztonsági mentési szabályzat beállítása a virtuális gépek védelme érdekében

A helyreállítási tár létrehozása után a következő lépés a tároló konfigurálása a használt adattípushoz, illetve a biztonsági mentési szabályzat beállítása. A biztonsági mentési szabályzat adja meg a helyreállítási pontok gyakoriságának és elhelyezési idejének menetrendjét. A házirend emellett tartalmazza a helyreállítási pontok megőrzési tartományát. Az oktatóanyag céljából feltételezzük, hogy az Ön vállalkozása egy sportkomplexum szállodával, stadionnal, éttermekkel és koncessziókkal, és Önnek kell gondoskodni a virtuális gépeken lévő adatok védelméről. Az alábbi lépésekkel a pénzügyi adatokra vonatkozó biztonsági mentési szabályzatot hozhat létre.

1. A helyreállítási tárak listájából válassza a **myRecoveryServicesVault** tárolót az irányítópult megnyitásához.

   ![Forgatókönyv menü megnyitása](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

2. A tároló irányítópultjának menüjében kattintson a **Biztonsági mentés** elemre a Biztonsági mentés menü megnyitásához.

3. A Biztonsági mentés célja alatt, a **Hol futnak az alkalmazások és szolgáltatások?** legördülő menüből válassza az *Azure* lehetőséget. A **Miről szeretne biztonsági másolatot készíteni?** legördülő menüből válassza a *Virtuális gép* lehetőséget, és kattintson a **Biztonsági mentés** gombra.

    Ezek a műveletek előkészítik a helyreállítási tárat a virtuális géppel folytatott interakcióra. A helyreállítási tárak rendelkeznek egy alapértelmezett szabályzattal, amely naponta hoz létre egy visszaállítási pontot, és aztán 30 napig meg is őrzi.

    ![Forgatókönyv menü megnyitása](./media/tutorial-backup-vm-at-scale/backup-goal.png)

4. Egy ú szabályzat létrehozásához a Biztonsági mentés alatt, a **Biztonsági mentési szabályzat kiválasztása** legördülő menüjében válassza az *Új létrehozása* elemet.

    ![Számítási feladat kiválasztása](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

5. A **Biztonsági mentési szabályzat** menü **Szabályzat neve** mezőjébe írja be a *Pénzügyi* kifejezést. Adja meg a következő értékeket a biztonsági mentési szabályzathoz:
    - A **Biztonsági mentés gyakorisága** mezőben állítsa be az *amerikai középidő* időzónáját. Mivel a sportkomplexum Texasban van, a tulajdonos helyi időt szeretne használni. Hagyja a biztonsági mentés gyakoriságát a napi 3:30 értéken.
    - **A napi biztonsági mentési pontok megőrzése** mezőben állítson be 90 napos időszakot.
    - **A heti biztonsági mentési pontok megőrzése** mezőben használja a *Hétfő* visszaállítási pontot, és adjon meg egy 52 hetes megőrzési periódust.
    - **A h biztonsági mentési pontok megőrzése** mezőben használja a hónap első vasárnapját visszaállítási pontként, és adjon meg egy 36 hónapos megőrzési periódust.
    - Törölje **Az éves biztonsági mentési pontok megőrzése** kijelölését. A pénzügyi vezető nem szeretné 36 hónapnál hosszabb ideig megőrizni az adatokat.
    - A biztonsági mentési szabályzat létrehozásához kattintson az **OK** gombra.

    ![Számítási feladat kiválasztása](./media/tutorial-backup-vm-at-scale/set-new-policy.png)

    A biztonsági mentési szabályzat létrehozása után társítsa a szabályzatot a virtuális gépekkel.

6. A **Virtuális gépek kijelölése** párbeszédpanelen válassza a *myVM* elemet, és kattintson az **OK** gombra, hogy a biztonsági mentési szabályzatot üzembe helyezze a virtuális gépeken.

    Megjelenik minden, egy helyen található virtuális gép, amelyhez még nincs biztonsági mentési szabályzat rendelve. A *myVMH1* és a *myVMR1* van kiválasztva, mint a *Pénzügy* szabályzattal társítandó virtuális gép.

    ![Számítási feladat kiválasztása](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png)

    Ha az üzembe helyezés sikeresen befejeződött, értesítést kap.

## <a name="initial-backup"></a>Kezdeti biztonsági mentés

Engedélyezte a biztonsági mentést a helyreállítási tárakhoz, de még nem hozott létre egy kezdeti biztonsági másolatot. Egy esetleges vészhelyreállítás megkönnyítése érdekében ajánlott aktiválni az első biztonsági mentést, hogy az adatait biztonságban tudhassa.

Biztonsági mentési feladat manuális futtatása:

1. A tároló irányítópultján kattintson a **Biztonsági másolati elemek** területen lévő **3** értékre a Biztonsági másolati elemek menü megnyitásához.

    ![Beállítások ikon](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    Megnyílik a **Biztonsági másolati elemek** menü.

2. A **Biztonsági másolati elemek** menüben kattintson az **Azure-beli virtuális gép** elemre a tárolóhoz rendelt virtuális gépek listájának megnyitásához.

    ![Beállítások ikon](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Megnyílik a **Biztonsági mentési elemek** listája.

    ![Biztonsági mentési feladat elindul](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

3. A **Biztonsági mentési elemek** listában kattintson a három pontra **...** a helyi menü megnyitásához.

4. A helyi menüben válassza a **Biztonsági mentés** elemet.

    ![Helyi menü](./media/tutorial-backup-vm-at-scale/context-menu.png)

    Megnyílik a Biztonsági mentés menü.

5. A Biztonsági mentés menüben adja meg azt a napot, ameddig meg szeretné őrizni a helyreállítási pontot, és kattintson a **Biztonsági mentés** gombra.

    ![adja meg az utolsó napot, ameddig Biztonsági mentés helyreállítási pontját meg kívánja őrizni](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    Az üzembehelyezési értesítések értesítik, hogy a biztonsági mentési feladat elindult, és hogy a feladat állapotát a Biztonsági mentési feladatok oldalon figyelheti. A virtuális gép méretétől függően a kezdeti biztonsági mentés létrehozása hosszabb időt is igénybe vehet.

    A kezdeti biztonsági mentési feladat befejeződése után megtekintheti annak állapotát a Biztonsági mentési feladat menüben. A manuális biztonsági mentési feladat létrehozta a *myVM* kezdeti visszaállítási pontját. Ha más virtuális gépekről is szeretne biztonsági másolatot készíteni, minden érintett géphez ismételje meg a fent ismertetett lépéseket.

    ![Biztonsági mentési feladatok csempe](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy az ezt követő oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben az oktatóanyagban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti az Azure Portalon az oktatóanyaghoz létrehozott összes erőforrást.

1. A **myRecoveryServicesVault** irányítópultján kattintson a **Biztonsági másolati elemek** területen lévő **3** értékre a Biztonsági másolati elemek menü megnyitásához.

    ![Beállítások ikon](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)


2. A **Biztonsági másolati elemek** menüben kattintson az **Azure-beli virtuális gép** elemre a tárolóhoz rendelt virtuális gépek listájának megnyitásához.

    ![Beállítások ikon](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Megnyílik a **Biztonsági mentési elemek** listája.

3. A **Biztonsági másolati elemek** menüben kattintson a három pontra a helyi menü megnyitásához.

    ![Beállítások ikon](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

4. A helyi menüben válassza ki a **Biztonsági mentés leállítása** elemet a Biztonsági mentés leállítása menü megnyitásához.

    ![Beállítások ikon](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

5. A **Biztonsági mentés leállítása** menüben válassza a felső legördülő menüt, majd a **Biztonsági másolatok adatainak törlése** lehetőséget.

6. A **biztonságimásolat-elem nevének megadására** szolgáló mezőbe írja be a *myVM* nevet.

7. A biztonságimásolat-elem ellenőrzése után (egy pipa jelenik meg) aktiválódik a **Biztonsági mentés leállítása** gomb. Kattintson a **Biztonsági mentés leállítása** gombra a szabályzat leállításához és a visszaállítási pontok törléséhez.

    ![Kattintás a Biztonsági mentés leállítása gombra a tároló törléséhez](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png)

8. A **myRecoveryServicesVault** menüben kattintson a **Törlés** parancsra.

    ![Kattintás a Biztonsági mentés leállítása gombra a tároló törléséhez](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    A tároló törlése után újra a helyreállítási tárak listája jelenik meg.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket végezte el az Azure Portalon:

> [!div class="checklist"]
> * Recovery Services-tároló létrehozása
> * A tároló beállítása a virtuális gépek védelméhez
> * Egyéni biztonsági mentési és adatmegőrzési szabályzat létrehozása
> * A szabályzat hozzárendelése több védendő virtuális géphez
> * A virtuális gépek biztonsági mentésének manuális elindítása

Folytassa a következő oktatóanyaggal, ha tudni szeretné, hogyan lehet lemezről visszaállítani Azure-beli virtuális gépeket.

> [!div class="nextstepaction"]
> [Virtuális gépek visszaállítása a parancssori felülettel](./tutorial-restore-disk.md)
