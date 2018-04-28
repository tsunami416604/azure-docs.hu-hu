---
title: 'Áttekintés: Azure virtuális gépek védelme Recovery Services-tárolóval | Microsoft Docs'
description: Megvédheti Azure virtuális gépeit egy Recovery Services-tárolóval. Adatai védelmére a Resource Manager-alapú és a klasszikus üzemi modellen alapuló virtuális gépek, a Premium Storage-alapú virtuális gépek, a titkosított virtuális gépek és a Managed Disksen található virtuális gépek biztonsági másolatait használhatja. Létrehozhat és regisztrálhat egy Recovery Services-tárolót. Regisztrálhat virtuális gépeket, létrehozhat egy házirendet, és megvédheti virtuális gépeit az Azure-ban.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keyword: backups; vm backup
ms.assetid: 45e773d6-c91f-4501-8876-ae57db517cd1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: markgal;jimpark
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e6a29e184a47e3b4304f9c4683e76feab3e75dd4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="back-up-azure-virtual-machines-to-recovery-services-vault"></a>Biztonsági másolatot az Azure virtuális gépek Recovery Services-tároló

Ez a cikk azt ismerteti, hogy a virtuális gép védelmének beállítása a virtuális gépek műveletek menüből, vagy a Recovery Services tároló. A Recovery Services-tárolók megvédik:

* Az Azure Resource Manager által telepített virtuális gépeket
* A klasszikus virtuális gépeket
* A Standard szintű tárolós virtuális gépeket
* A Prémium szintű tárolós virtuális gépeket
* A Managed Disksen futó virtuális gépek
* Az Azure Disk Encryption használatával titkosított virtuális gépek
* Windows rendszerű virtuális gépek alkalmazáskonzisztens biztonsági mentése VSS és Linux rendszerű virtuális gépekkel egyéni, pillanatkép előtti és utáni szkriptekkel

A Prémium szintű tárolós virtuális gépek védelméről további információt a következő cikkben talál: [Prémium szintű tárolós virtuális gépek biztonsági mentése és visszaállítása](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup). További információ a felügyelt lemezeken található virtuális gépek támogatásáról: [ Virtuális gépek biztonsági mentése és visszaállítása felügyelt lemezeken](backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). A Linux rendszerű virtuális gépek biztonsági mentéseinek szkript előtti és utáni keretrendszerével kapcsolatos további információért lásd: [Linux rendszerű virtuális gépek alkalmazáskonzisztens biztonsági mentése szkript előtti és utáni keretrendszerrel](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Mi is, és nem készíthető biztonsági másolat kapcsolatos további tudnivalókért lásd: [készítse elő a környezetet a biztonsági mentése Azure virtuális gépek](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm).

> [!NOTE]
> Ez az oktatóprogram feltételezi, hogy Ön már rendelkezik egy virtuális géppel az Azure-előfizetésében, és hogy lépéseket tett azért, hogy a biztonsági mentési szolgáltatás hozzáférjen a virtuális géphez.
>
>

A védeni kívánt virtuális gépek számától függően különböző kiindulási pontokból kezdhet. Ha több virtuális gépről szeretne biztonsági mentést készíteni egy művelettel, lépjen a Recovery Services-tárolóhoz, és [indítsa el a biztonsági mentési feladatot a tároló irányítópultjáról](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-recovery-services-vault). Készítsen biztonsági másolatot egyetlen virtuális gépre, ha [kezdeményezni a virtuális gép műveletek menüjében a biztonsági mentési feladat](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-operations-menu).

## <a name="configure-the-backup-job-from-the-vm-operations-menu"></a>A biztonsági mentési feladatot a virtuális gép műveletek menüből konfigurálása

A következő lépésekkel konfigurálhatja a biztonsági mentési feladatot a virtuális gép műveletek menüből. A lépéseket csak virtuális gépek Azure-portálon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A központi menüben kattintson a **Minden szolgáltatás** elemre, majd a Szűrés párbeszédpanelen írja be a következőt: **Virtuális gépek**. A gépelés során a rendszer szűri az erőforrások listáját. Amikor meglátja a Virtuális gépet, kattintson rá.

  ![Képernyőkép, amely bemutatja, hogyan navigálhat a virtuális gépekhez a Minden szolgáltatás területről](./media/backup-azure-vms-first-look-arm/open-vm-from-hub.png)

  A virtuális gépek (VM) az előfizetés listája jelenik meg.

  ![Megjelenik az előfizetésben található virtuális gépek listája.](./media/backup-azure-vms-first-look-arm/list-of-vms.png)

3. Válasszon egy virtuális gépet a listából, amelyről biztonsági mentést kíván készíteni.

  ![Megjelenik az előfizetésben található virtuális gépek listája.](./media/backup-azure-vms-first-look-arm/list-of-vms-selected.png)

  Amikor választja, a virtuális Gépet, a virtuális gépek elmozdul a bal oldali és a virtuális gép felügyeleti menü és a virtuális gép irányítópult listáját nyitja meg.

4. A virtuális gép felügyeleti menüben a a **műveletek** kattintson **biztonsági mentés**. </br>

  ![Biztonsági mentési beállításának VM kezelés menü](./media/backup-azure-vms-first-look-arm/vm-management-menu.png)

  A biztonsági mentés engedélyezése menü megnyitása.

  ![Biztonsági mentési beállításának VM kezelés menü](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup.png)

5. Kattintson a Recovery Services tárolóban található **válasszon meglévő** és legördülő listájából válasszon ki egy tárolót.

  ![Biztonsági mentés engedélyezése varázsló](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

  Ha nincsenek Recovery Services-tárolók, vagy ha új tárolót szeretne használni, kattintson az **Új létrehozása** elemre, és adja meg az új tároló nevét. Az új tároló ugyanabban az erőforráscsoportban és régióban jön létre, ahol a virtuális gép is van. Ha más értékekkel szeretne Recovery Services-tárolót létrehozni, tekintse meg a [Recovery Services-tároló létrehozását](backup-azure-vms-first-look-arm.md#create-a-recovery-services-vault-for-a-vm) bemutató szakaszt.

6. Válassza ki a biztonsági mentési házirend menüben válassza ki a házirendet. A kiválasztott házirend a részleteket a legördülő menü alatt jelenik meg.

  Ha azt szeretné, hozzon létre egy új házirendet vagy a meglévő házirend szerkesztése parancsra **létrehozása (és módosítása) egy új házirendet** a biztonsági mentés Helyicsoportházirend-szerkesztő megnyitásához. A biztonsági mentési házirendek meghatározását segítő utasításokat itt találja: [Biztonsági mentési házirend meghatározása](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Mentse a módosításokat a biztonsági mentési házirend, és térjen vissza a biztonsági mentés engedélyezése menü, kattintson a **OK**.

  ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. A Recovery Services tároló és a biztonsági mentési házirend alkalmazása a virtuális gépre, kattintson a **biztonsági mentés engedélyezése** a szabályzatot telepíteni szeretné. A házirend telepítésével társítja azt a tárolóval és a virtuális gépekkel.

  ![Biztonsági mentés engedélyezése gomb](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Nyomon követheti a konfigurációs folyamatot a portálon megjelenő értesítések segítségével. Az alábbi példában az látható, hogy a Telepítés megkezdődött.

  ![Biztonsági mentési értesítések engedélyezése](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-notification.png)

9. Ha a konfigurációs folyamat befejezése után a virtuális gép felügyeleti menüben kattintson **biztonsági mentés** a biztonsági mentés menü megnyitásához, és a rendelkezésre álló részleteinek megtekintése.

  ![Virtuális gép biztonsági mentési elemei nézet](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

  A kezdeti biztonsági mentés befejezéséig az **Utolsó biztonsági mentés állapota** **Figyelmeztetés (kezdeti biztonsági mentés folyamatban)** állapotú. Amikor a következő ütemezett biztonsági mentési feladat jelentkezik, az **összegzés** kattintson a házirend nevét. A biztonsági mentési házirend menü megnyitása és jeleníti meg az ütemezett biztonsági mentés idején.

10. A virtuális gép védelme érdekében kattintson **biztonsági mentés most**. 

  ![a kezdeti biztonsági mentés futtatásához kattintson a Biztonsági mentés elemre](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

  Megnyílik a Biztonsági mentés menü. 

  ![a Biztonsági mentés panel képe](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

11. A biztonsági mentés most menüben kattintson a naptár ikonra, válassza ki az elmúlt nap során ez a helyreállítási pont őrzi meg, majd kattintson a havinaptár-vezérlő segítségével **OK**.

  ![adja meg az utolsó napot, ameddig Biztonsági mentés helyreállítási pontját meg kívánja őrizni](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Az üzembehelyezési értesítések értesítik, hogy a biztonsági mentési feladat elindult, és hogy a feladat állapotát a Biztonsági mentési feladatok oldalon figyelheti.

## <a name="configure-the-backup-job-from-the-recovery-services-vault"></a>A biztonsági mentési feladat konfigurálása a Recovery Services-tárolóból
A biztonsági mentési feladat konfigurálásához végezze el a következő lépéseket.

1. Hozzon létre egy Recovery Services-tárolót egy virtuális géphez.
2. Az Azure Portal használatával válasszon ki egy Forgatókönyvet, állítson be egy Biztonsági mentési házirendet, és azonosítsa a védelemmel ellátni kívánt elemeket.
3. Futtatja a kezdeti biztonsági mentést.

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Létrehoz egy Recovery Services-tárolót egy virtuális géphez.
A Recovery Services-tároló egy olyan entitás, amely eltárolja az idők során létrehozott biztonsági mentéseket és helyreállítási pontokat. A Recovery Services-tároló tartalmazza a védelem alatt álló virtuális gépeken alkalmazott biztonsági mentési házirendet is.

> [!NOTE]
> A virtuális gépek biztonsági mentése egy helyi folyamat. Nem lehet biztonsági mentést készíteni egy adott régióban található virtuális gépről egy másik régióban található helyreállítási tárba. Így aztán minden olyan Azure-régióban, ahol biztonsági mentésre szoruló virtuális gépek vannak, legalább egy helyreállítási tárnak kell lennie.
>
>

Egy Recovery Services-tároló létrehozásához:

1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com/) Azure-előfizetésével.
2. A központi menüben kattintson a **Minden szolgáltatás** elemre, majd a Szűrés párbeszédpanelen írja be a következőt: **Recovery Services**. A gépelés során a rendszer szűri az erőforrások listáját. Amikor meglátja a Recovery Services-tárolót a listában, kattintson rá.

    ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Ha az előfizetés Recovery Services-tárolókat tartalmaz, megjelennek a tárolók.

    ![Recovery Services-tároló létrehozása – 2. lépés](./media/backup-azure-vms-first-look-arm/list-of-rs-vault.png)
3. A **Recovery Services-tárolók** menüben kattintson a **Hozzáadás** elemre.

    ![Recovery Services-tároló létrehozása – 2. lépés](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    A Recovery Services tároló menü megnyitása, amely felszólítja, hogy adja meg a **neve**, **előfizetés**, **erőforráscsoport**, és **hely**.

    ![Recovery Services-tároló létrehozása – 3. lépés](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. A **Név** mezőben adjon meg egy egyszerű nevet a tároló azonosításához. A névnek egyedinek kell lennie az Azure-előfizetéshez. Írjon be egy 2–50 karakter hosszúságú nevet. Ennek egy betűvel kell kezdődnie, és csak betűket, számokat és kötőjeleket tartalmazhat.

5. Az **Előfizetés** szakaszban, az Azure-előfizetés kiválasztásához használja a legördülő menüt. Ha csak egy előfizetést használ, az az előfizetés jelenik meg, és továbbléphet a következő lépésre. Ha nem biztos benne, hogy melyik előfizetést szeretné használni, használja az alapértelmezett (vagy javasolt) előfizetést. Csak akkor lesz több választási lehetőség, ha a szervezetéhez tartozó fiók több Azure-előfizetéssel van összekötve.

6. Az **Erőforráscsoport** szakaszban:

    * válassza az **Új létrehozása** lehetőséget, ha erőforráscsoportot szeretne létrehozni.
    Vagy
    * válassza a **Meglévő használata** lehetőséget, és kattintson a legördülő menüben az elérhető erőforráscsoportok listájának megtekintéséhez.

  Átfogó információk az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md).

7. Kattintson a **Hely** elemre a tárolóhoz tartozó földrajzi régió kiválasztásához. Ez a választás határozza meg a földrajzi régiót, ahová az adatok biztonsági másolata el lesz küldve.

  > [!IMPORTANT]
  > Ha nem biztos benne, hogy mi a virtuális gép régiója, lépjen ki a tároló létrehozási párbeszédpaneléből, és lépjen a virtuális gépek listájához a portálon. Ha több régióban rendelkezik virtuális gépekkel, minden régióban hozzon létre egy Recovery Services-tárolót. Hozza létre a tárolót az első régióban, majd lépjen a következő régióra. Az adatok biztonsági másolatának tárolásához használt tárfiókok megadása nem szükséges – a Recovery Services-tároló és az Azure Backup szolgáltatás automatikusan kezeli a tárolást.
  >

8. A Recovery Services-tároló menü alján kattintson **létrehozása**.

    A Recovery Services-tároló létrehozása több percet is igénybe vehet. Figyelje az állapotértesítéseket a portál jobb felső területén. Miután a tároló létrejött, megjelenik a Recovery Services-tárolók listájában. Ha több perc után sem látja a tárolót, kattintson a **Frissítés** gombra.

    ![Kattintson a Frissítés gombra](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Ha látja a tárolót a Recovery Services-tárolók listájában, készen áll tárhely-redundancia beállítására.

Most, hogy létrehozta a tárolóját, megismerkedhet a tárreplikáció beállításának módjával.

### <a name="set-storage-replication"></a>Tárreplikáció beállítása
A tárreplikáció lehetősége lehetővé teszi, hogy georedundáns tárolás és helyileg redundáns tárolás között válasszon. Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik. Ha a Recovery Services-tároló az elsődleges biztonsági mentési tároló, hagyja a tárolóreplikációs beállítást georedundáns tároláson. Ha egy olcsóbb, rövidebb élettartamú megoldást szeretne, válassza a helyileg redundáns tárolást. A [georedundáns](../storage/common/storage-redundancy-grs.md) és a [helyileg redundáns](../storage/common/storage-redundancy-lrs.md) tárolási lehetőségekről többet olvashat az [Azure tárreplikáció áttekintése](../storage/common/storage-redundancy.md) című cikkben.

A tárreplikációs beállítás szerkesztése:

1. Az a **Recovery Services-tárolók** menüben válassza ki az új tárolóba.

  ![A Recovery Services-tárolók listájából válassza ki az új tárolót](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

  Ha bejelöli a tárolóban, a beállítások menü (*a tároló neve tartalmaz felső*) és a tároló irányítópult megnyitása.

  ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-update.png)

2. Az új tároló felügyeleti menü segítségével a függőleges diák görgessen le a kezelés szakaszt, és kattintson **biztonsági infrastruktúra** a biztonsági mentés infrastruktúra menü megnyitásához.
 
   ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/set-storage-config-bkup-infra.png)

3. A biztonsági mentés infrastruktúra menüben kattintson a **biztonsági mentési konfigurációhoz** megnyitásához a **biztonsági mentési konfigurációhoz** menü.

    ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/set-storage-open-infra.png)
4. Válassza ki a megfelelő tárolóreplikációs beállítást a tárolóhoz.

    ![a tároló konfigurálásának lehetőségei](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik. Ha az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, folytassa a **georedundáns** beállítás használatát. Ha nem az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, válassza a **Helyileg redundáns** lehetőséget, amellyel csökkentheti az Azure Storage-költségeit. A [georedundáns](../storage/common/storage-redundancy-grs.md) és a [helyileg redundáns](../storage/common/storage-redundancy-lrs.md) tárolási lehetőségekről többet olvashat ebben a [Tárhely-redundancia áttekintésben](../storage/common/storage-redundancy.md).


## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Válassza ki a biztonsági mentés célját, állítsa be a házirendet, és határozza meg a megvédeni kívánt elemeket
Mielőtt regisztrálna egy virtuális gépet a tárolóval, futtassa a felfedezési folyamatot, hogy meggyőződjön arról, hogy az előfizetéshez hozzáadott minden új virtuális gép azonosítva lett. A folyamat lekéri az Azure-ból az előfizetésben található virtuális gépek listáját, olyan kiegészítő információkkal, mint a felhőszolgáltatás neve és a régió. Az Azure portálon a forgatókönyv arra utal, amit bele fog tenni a Recovery Services-tárolóba. A házirend adja meg a helyreállítási pontok gyakoriságának és elhelyezési idejének a menetrendjét. A házirend emellett tartalmazza a helyreállítási pontok megőrzési tartományát.

1. Ha már meg van nyitva egy Recovery Services-tároló, folytassa a 2. lépéssel. Ellenkező esetben kattintson a **Minden szolgáltatás** elemre. Írja be a **Recovery Services** kifejezést, és kattintson a **Recovery Services-tárolók** elemre.

    ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    A Recovery Services-tárolók listája megjelenik.

    ![A Recovery Services-tároló listájának nézete](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

    A Recovery Services-tárolók listájából válasszon ki egy tárolót az irányítópult megnyitásához.

     ![A tárolók menüjének megnyitása](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. A tároló irányítópultjának menüjében kattintson a **Biztonsági mentés** elemre a Biztonsági mentés menü megnyitásához.

    ![Biztonsági mentés menü megnyitása](./media/backup-azure-arm-vms-prepare/backup-button.png)

    A biztonsági mentés és a biztonsági mentési cél menü megnyitása.

    ![Forgatókönyv menü megnyitása](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)
3. A biztonsági mentési cél menüben a a **a számítási feladatok futtató** legördülő menüben válassza ki az Azure. A **Miről szeretne biztonsági másolatot készíteni?** legördülő menüből válassza a Virtuális gép lehetőséget, és kattintson az **OK** gombra.

    Ezek a műveletek regisztrálják a virtuálisgép-bővítményt a tárolóban. A biztonsági mentési cél menü bezárása és a **biztonsági mentési házirend** menü megnyitása.

    ![Forgatókönyv menü megnyitása](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. A biztonsági mentési házirend menüben válassza ki a biztonsági mentési házirendet kíván alkalmazni a tárolóba.

    ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

    Az alapértelmezett házirend részletei megtalálhatók a legördülő menüben. Ha házirendet hozna létre, válassza az **Új létrehozása** elemet a legördülő menüből. A biztonsági mentési házirendek meghatározását segítő utasításokat itt találja: [Biztonsági mentési házirend meghatározása](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Kattintson az **OK** gombra a biztonsági mentési házirend a tárolóhoz való hozzárendeléséhez.

    A biztonsági mentési házirend menü bezárása és a **válassza ki a virtuális gépek** menü megnyitása.
5. Az a **válassza ki a virtuális gépek** menüben válassza ki a virtuális gépek társítani a megadott házirend, és kattintson a **OK**.

    ![Számítási feladat kiválasztása](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    Megtörténik a kiválasztott virtuális gép ellenőrzése. Ha nem látja a várt virtuális gépeket, ellenőrizze, hogy ugyanazon az Azure-helyen találhatók-e, mint a Recovery Services-tároló, és hogy nem állnak-e már védelem alatt. A Recovery Services-tároló helye a tároló irányítópultján jelenik meg.

6. Most, hogy a tároló összes beállítás a biztonsági mentés menü adta meg, kattintson a **biztonsági mentés engedélyezése** a házirend központi telepítése a tároló és a virtuális gépeket. A biztonsági mentési házirend telepítése nem hozza létre a virtuális géphez tartozó első helyreállítási pontot.

    ![Biztonsági mentés engedélyezése](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

A biztonsági mentés sikeres engedélyezése után a biztonsági mentési házirend az ütemezés szerint fog futni. Ennek ellenére lépjen tovább az első biztonsági mentési feladat elindításához.

## <a name="initial-backup"></a>Kezdeti biztonsági mentés
Az, hogy egy biztonsági mentési házirend lett bevezetve a virtuális gépeken, nem jelenti azt, hogy az adatokról biztonsági mentés készült. Alapértelmezés szerint az első ütemezett biztonsági mentés (a biztonsági mentési házirendben meghatározottak szerint) a kezdeti biztonsági mentés. Amíg a kezdeti biztonsági másolatot történik, az utolsó biztonsági mentés állapota a **biztonsági mentési feladatok** menü megjelenik, mint **figyelmeztetés (függőben lévő kezdeti biztonsági másolatot)**.

![Biztonsági mentés függőben](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

Hacsak a kezdeti biztonsági mentés kezdete nem a nagyon közeli jövőben van, érdemes futtatni a **Biztonsági másolat készítése** lehetőséget.

A kezdeti biztonsági mentési feladat futtatása:

1. A tároló irányítópultján kattintson a **Biztonsági mentési elemek** szakaszban található számra, vagy kattintson a **Biztonsági mentési elemek** csempére. <br/>
  ![Beállítások ikon](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  Megnyílik a **Biztonsági másolati elemek** menü.

  ![Elemek biztonsági mentése](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. Az a **biztonsági mentés elemek** menüben válassza ki a elemet.

  ![Beállítások ikon](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  Megnyílik a **Biztonsági mentési elemek** listája. <br/>

  ![Biztonsági mentési feladat elindul](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. A **Biztonsági mentési elemek** listában kattintson a három pontra **...** a helyi menü megnyitásához.

  ![Helyi menü](./media/backup-azure-vms-first-look-arm/context-menu.png)

  Megjelenik a Helyi menü.

  ![Helyi menü](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. A Helyi menüben kattintson a **Biztonsági mentés** elemre.

  ![Helyi menü](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  Megnyílik a Biztonsági mentés menü.

  ![a biztonsági mentés most menü megjelenítése](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. A biztonsági mentés most menüben kattintson a naptár ikonra, válassza ki az elmúlt nap során ez a helyreállítási pont őrzi meg, majd kattintson a havinaptár-vezérlő segítségével **biztonsági mentés**.

  ![adja meg az utolsó napot, ameddig Biztonsági mentés helyreállítási pontját meg kívánja őrizni](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Az üzembehelyezési értesítések értesítik, hogy a biztonsági mentési feladat elindult, és hogy a feladat állapotát a Biztonsági mentési feladatok oldalon figyelheti. A virtuális gép méretétől függően a kezdeti biztonsági mentés létrehozása hosszabb időt vehet igénybe.

6. A kezdeti biztonsági mentés állapotának megtekintéséhez vagy nyomon követéséhez a tároló irányítópultjának **Biztonsági mentési feladatok** csempéjén kattintson a **Folyamatban** elemre.

  ![Biztonsági mentési feladatok csempe](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  A biztonsági mentési feladatok menü megnyitása.

  ![Biztonsági mentési feladatok csempe](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  Az a **biztonsági mentési feladatok** menüben megtekintheti az összes feladat állapota. Ellenőrizze, hogy a virtuális gép biztonsági mentése folyamatban van-e, vagy már befejeződött. Amikor a biztonsági mentési feladat befejeződött, az állapota *Befejezve* lesz.

  > [!NOTE]
  > A biztonsági mentési művelet részeként az Azure Backup szolgáltatás egy parancsot ad minden virtuális gépre a biztonsági mentési bővítménynek, hogy ürítsen ki minden írást, és készítsen egy egységes pillanatképet.
  >
  >


[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Telepítse a virtuális gép ügynökét a virtuális gépre.
Ez az információ szükség esetén mellékelve van. Az Azure virtuálisgép-ügynököt telepíteni kell az Azure virtuális gépre, hogy a Backup bővítmény működjön. Ugyanakkor ha a virtuális gépe az Azure-katalógusból lett létrehozva, a virtuálisgép-ügynök már jelen van a virtuális gépen. A helyszíni adatközpontokból áttelepített virtuális gépek nem rendelkeznek telepített virtuálisgép-ügynökkel. Ebben az esetben a virtuális gép ügynökét telepíteni kell. Ha problémákba ütközik az Azure-beli virtuális gép telepítése közben, ellenőrizze, hogy az Azure virtuálisgép-ügynöke megfelelően telepítve van-e a virtuális gépen (lásd az alábbi táblázatot). Ha egy egyéni virtuális gépet hoz létre, telepítse a virtuális gép ügynökét a virtuális gép kiépítése előtt.

Többet is megtudhat itt a [virtuálisgép-ügynökről](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) és arról, [hogyan kell telepíteni](../virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

A következő táblázat további információkat tartalmaz a Windows és Linux virtuális gépekhez tartozó virtuálisgép-ügynökről.

| **Művelet** | **Windows** | **Linux** |
| --- | --- | --- |
| A virtuálisgép-ügynök telepítése |<li>Töltse le és telepítse az [ügynök MSI-t](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek. <li>[Frissítse a virtuális gép tulajdonságát](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), hogy megjelenjen a telepített ügynök. |<li> Telepítse a legújabb [Linux ügynököt](https://github.com/Azure/WALinuxAgent) a GitHubról. A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek. <li> [Frissítse a virtuális gép tulajdonságát](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), hogy megjelenjen a telepített ügynök. |
| A virtuálisgép-ügynök frissítése |A virtuálisgép-ügynök frissítése a [virtuálisgép-ügynök bináris fájljainak](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) újratelepítéséből áll. <br>Győződjön meg róla, hogy nem fut biztonsági mentési művelet a virtuálisgép-ügynök frissítése közben. |Kövesse a [linuxos virtuálisgép-ügynök frissítését](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ismertető szakasz utasításait. <br>Győződjön meg róla, hogy nem fut biztonsági mentési művelet a virtuálisgép-ügynök frissítése közben. |
| A virtuálisgép-ügynök telepítésének érvényesítése |<li>Lépjen a *C:\WindowsAzure\Packages* mappába az Azure virtuális gépen. <li>Itt találja a WaAppAgent.exe fájlt.<li> Kattintson jobb gombbal a fájlra, válassza a **Tulajdonságok** parancsot, majd nyissa meg a **Részletek** lapot. A Termék verziószáma mezőben 2.6.1198.718 vagy újabb verziónak kell lennie. |– |

### <a name="backup-extension"></a>Backup bővítmény
Miután a virtuálisgép-ügynök telepítve lett a virtuális gépen, az Azure Backup szolgáltatás telepíti a biztonsági mentési bővítményt a virtuálisgép-ügynökhöz. Az Azure Backup szolgáltatás akadásmentesen frissíti és javítja a biztonsági mentési bővítményt a felhasználó további beavatkozása nélkül.

A Backup szolgáltatás akkor is telepíti a biztonsági mentési bővítményt, ha a virtuális gép nem fut. Egy futó virtuális gép adja a legnagyobb esélyt egy alkalmazással konzisztens helyreállítási pont létrehozásának. Ugyanakkor az Azure Backup szolgáltatás folytatja a virtuális gép biztonsági másolatának elkészítését akkor is, ha az ki van kapcsolva és a bővítményt nem lehet telepíteni. Az ilyen típusú biztonsági mentés Offline virtuális gép néven ismeretes, és a helyreállítási pont *összeomlás-konzisztens*.

## <a name="troubleshooting-information"></a>Hibaelhárítási információ
Ha problémákba ütközik a cikkben leírt feladatok elvégzésében, tekintse meg a [Hibaelhárítási útmutatót](backup-azure-vms-troubleshoot.md).

## <a name="pricing"></a>Díjszabás
Az Azure-beli virtuális gépek biztonsági mentésének költsége a védett példányok számától függ. A védett példányok definíciójáért lásd [a fogalmat ismertető szakaszt](backup-introduction-to-azure-backup.md#what-is-a-protected-instance). Ha szeretne egy példát látni a virtuális gépek biztonsági mentési költségeinek számítására, olvassa el [a védett példányok számítását](backup-azure-vms-introduction.md#calculating-the-cost-of-protected-instances) ismertető cikket. [A Backup díjszabásával](https://azure.microsoft.com/pricing/details/backup/) kapcsolatban lásd az Azure Backup díjszabását ismertető oldalt.

## <a name="questions"></a>Kérdései vannak?
Ha kérdései vannak, vagy van olyan szolgáltatás, amelyről hallani szeretne, [küldjön visszajelzést](http://aka.ms/azurebackup_feedback).
