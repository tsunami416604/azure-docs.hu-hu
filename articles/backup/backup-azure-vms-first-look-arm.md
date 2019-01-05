---
title: Azure virtuális gépek biztonsági mentése az Azure Backup szolgáltatással
description: 'Útmutató: Azure virtuális gépek biztonsági mentése az Azure Backup szolgáltatással'
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: raynew
ms.openlocfilehash: c1bd92b9c4611465b680f195e4881a447f4bb701
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54044399"
---
# <a name="back-up-azure-vms-with-the-azure-backup-service"></a>Azure virtuális gépek biztonsági mentése az Azure Backup szolgáltatással

Ez a cikk bemutatja, hogyan lehet beállítani a védelmet a virtuális gép virtuális gépek műveletek menüből, vagy a Recovery Services-tároló. A Recovery Services-tárolók megvédik:

* Az Azure Resource Manager által telepített virtuális gépeket
* A klasszikus virtuális gépeket
* A Standard szintű tárolós virtuális gépeket
* A Prémium szintű tárolós virtuális gépeket
* A Managed Disksen futó virtuális gépek
* Az Azure Disk Encryption használatával titkosított virtuális gépek
* Windows rendszerű virtuális gépek alkalmazáskonzisztens biztonsági mentése VSS és Linux rendszerű virtuális gépekkel egyéni, pillanatkép előtti és utáni szkriptekkel

A Prémium szintű tárolós virtuális gépek védelméről további információt a következő cikkben talál: [Prémium szintű tárolós virtuális gépek biztonsági mentése és visszaállítása](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup). További információ a felügyelt lemezeken található virtuális gépek támogatásáról: [ Virtuális gépek biztonsági mentése és visszaállítása felügyelt lemezeken](backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). A Linux rendszerű virtuális gépek biztonsági mentéseinek szkript előtti és utáni keretrendszerével kapcsolatos további információért lásd: [Linux rendszerű virtuális gépek alkalmazáskonzisztens biztonsági mentése szkript előtti és utáni keretrendszerrel](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

További információk a Mit, és nem készíthető biztonsági másolat megkereséséhez lásd: [Azure virtuális gépek biztonsági mentése a környezet előkészítése](backup-azure-arm-vms-prepare.md#before-you-start).

> [!NOTE]
> A biztonsági mentési szolgáltatás létrehoz egy külön erőforráscsoportot, mint az erőforráscsoport, a virtuális gép visszaállításipont-gyűjtemény tárolásához. Ügyfelek, a Backup szolgáltatás használni létrehozott erőforráscsoportot zárolása nem végigvitelével.
A Backup szolgáltatás által létrehozott erőforráscsoport elnevezési formátuma: AzureBackupRG_`<Geo>`_`<number>`
<br>Például: AzureBackupRG_northeurope_1
>
>

A védeni kívánt virtuális gépek számától függően különböző kiindulási pontokból kezdhet. Ha több virtuális gépről szeretne biztonsági mentést készíteni egy művelettel, lépjen a Recovery Services-tárolóhoz, és [indítsa el a biztonsági mentési feladatot a tároló irányítópultjáról](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-recovery-services-vault). Ha azt szeretné, hogy egyetlen virtuális gép biztonsági mentése [kezdeményezni a virtuális gép műveletek menüből a biztonsági mentési feladat](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-operations-menu).

## <a name="configure-the-backup-job-from-the-vm-operations-menu"></a>A virtuális gép műveletek menüből a biztonsági mentési feladat konfigurálása

A következő lépések segítségével konfigurálhatja a biztonsági mentési feladat, a virtuális gép műveletek menüből. A lépések csak az Azure Portalon a virtuális gépek vonatkoznak.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A központi menüben kattintson a **Minden szolgáltatás** elemre, majd a Szűrés párbeszédpanelen írja be a következőt: **Virtuális gépek**. A gépelés során a rendszer szűri az erőforrások listáját. Amikor meglátja a Virtuális gépet, kattintson rá.

  ![Képernyőkép, amely bemutatja, hogyan navigálhat a virtuális gépekhez a Minden szolgáltatás területről](./media/backup-azure-vms-first-look-arm/open-vm-from-hub.png)

  A virtuális gépek (VM) az előfizetés listája jelenik meg.

  ![Megjelenik az előfizetésben található virtuális gépek listája.](./media/backup-azure-vms-first-look-arm/list-of-vms.png)

3. Válasszon egy virtuális gépet a listából, amelyről biztonsági mentést kíván készíteni.

  ![Megjelenik az előfizetésben található virtuális gépek listája.](./media/backup-azure-vms-first-look-arm/list-of-vms-selected.png)

  Amikor kiválasztja a virtuális gép, virtuális gépek műszakok a bal oldali és a virtuális gép lekérdezéskezelés menü, és a virtuális gép irányítópultja listája nyissa meg.

4. A felügyeleti virtuális gép menüjében a a **Operations** területén kattintson **Backup**. </br>

  ![Backup beállítás a virtuális gép lekérdezéskezelés menü](./media/backup-azure-vms-first-look-arm/vm-management-menu.png)

  Megnyílik a biztonsági mentés engedélyezése menü.

  ![Backup beállítás a virtuális gép lekérdezéskezelés menü](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup.png)

5. A Recovery Services vault területen kattintson a **válasszon meglévő** , majd válassza a legördülő listából válassza ki egy tárolót.

  ![Biztonsági mentés engedélyezése varázsló](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

  Ha nincsenek Recovery Services-tárolók, vagy ha új tárolót szeretne használni, kattintson az **Új létrehozása** elemre, és adja meg az új tároló nevét. Az új tároló ugyanabban az erőforráscsoportban és régióban jön létre, ahol a virtuális gép is van. Ha más értékekkel szeretne Recovery Services-tárolót létrehozni, tekintse meg a [Recovery Services-tároló létrehozását](backup-azure-vms-first-look-arm.md#create-a-recovery-services-vault-for-a-vm) bemutató szakaszt.

6. Válassza ki a biztonsági mentési szabályzat menüben válassza ki egy szabályzatot. A kiválasztott házirend részleteit alatt a legördülő menü jelenik meg.

  Ha azt szeretné, hozzon létre egy új szabályzatot, vagy a meglévő szabályzatot, kattintson **létrehozása (vagy szerkesztése) egy új szabályzat** a biztonsági mentés Helyicsoportházirend-szerkesztő megnyitásához. A biztonsági mentési házirendek meghatározását segítő utasításokat itt találja: [Biztonsági mentési házirend meghatározása](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). A módosítások mentéséhez a biztonsági mentési szabályzathoz, és térjen vissza a engedélyezése biztonsági mentés menü, kattintson a **OK**.

  ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. A virtuális gép a Recovery Services tároló és a biztonsági mentési szabályzat alkalmazásához kattintson **biztonsági mentés engedélyezése** a szabályzatot telepíteni szeretné. A házirend telepítésével társítja azt a tárolóval és a virtuális gépekkel.

  ![Biztonsági mentés engedélyezése gomb](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Nyomon követheti a konfigurációs folyamatot a portálon megjelenő értesítések segítségével. Az alábbi példában az látható, hogy a Telepítés megkezdődött.

  ![Biztonsági mentési értesítések engedélyezése](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-notification.png)

9. A felügyeleti virtuális gép menüjében a konfigurációs folyamat befejezése után kattintson a **Backup** a biztonsági mentés menü megnyitásához, és a rendelkezésre álló részleteinek megtekintéséhez.

  ![Virtuális gép biztonsági mentési elemei nézet](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

  A kezdeti biztonsági mentés befejezéséig az **Utolsó biztonsági mentés állapota** **Figyelmeztetés (kezdeti biztonsági mentés folyamatban)** állapotú. Megtekintheti, ha a következő ütemezett biztonsági mentési feladat történik, a **összefoglalás** kattintson a házirend nevét. A biztonsági mentési szabályzat menüje megnyílik, és megjeleníti a ütemezett biztonsági mentés időpontját.

10. A virtuális gép védelmét, kattintson a **biztonsági mentés**.

  ![a kezdeti biztonsági mentés futtatásához kattintson a Biztonsági mentés elemre](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

  Megnyílik a Biztonsági mentés menü.

  ![a Biztonsági mentés panel képe](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

11. A biztonsági mentés menüben kattintson a naptár ikonra, használja a naptárvezérlőt annak kiválasztására a helyreállítási pont maradnak, és kattintson a **OK**.

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

    A helyreállítási tár menü megnyílik, adja meg, és felszólítja egy **neve**, **előfizetés**, **erőforráscsoport**, és **hely**.

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

8. A helyreállítási tárok menüjének alján kattintson **létrehozás**.

    A Recovery Services-tároló létrehozása több percet is igénybe vehet. Figyelje az állapotértesítéseket a portál jobb felső területén. Miután a tároló létrejött, megjelenik a Recovery Services-tárolók listájában. Ha több perc után sem látja a tárolót, kattintson a **Frissítés** gombra.

    ![Kattintson a Frissítés gombra](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Ha látja a tárolót a Recovery Services-tárolók listájában, készen áll tárhely-redundancia beállítására.

Most, hogy létrehozta a tárolóját, megismerkedhet a tárreplikáció beállításának módjával.

### <a name="set-storage-replication"></a>Tárreplikáció beállítása
A tárreplikáció lehetősége lehetővé teszi, hogy georedundáns tárolás és helyileg redundáns tárolás között válasszon. Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik. Ha a Recovery Services-tároló az elsődleges biztonsági mentési tároló, hagyja a tárolóreplikációs beállítást georedundáns tároláson. Ha egy olcsóbb, rövidebb élettartamú megoldást szeretne, válassza a helyileg redundáns tárolást. A [georedundáns](../storage/common/storage-redundancy-grs.md) és a [helyileg redundáns](../storage/common/storage-redundancy-lrs.md) tárolási lehetőségekről többet olvashat az [Azure tárreplikáció áttekintése](../storage/common/storage-redundancy.md) című cikkben.

A tárreplikációs beállítás szerkesztése:

1. Az a **Recovery Services-tárolók** menüben válassza ki az új tároló.

  ![A Recovery Services-tárolók listájából válassza ki az új tárolót](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

  Amikor kiválasztja a tárolót, a beállítások menüben (*a tároló neve amelynek tetején*), és nyissa meg a tároló irányítópultján.

  ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-update.png)

2. Az új tároló kezelés menü, használja a függőleges csúszkát görgessen le a kezelés szakaszhoz, és kattintson a **biztonsági mentési infrastruktúra** a biztonsági mentési infrastruktúra menü megnyitásához.

   ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/set-storage-config-bkup-infra.png)

3. A biztonsági mentési infrastruktúra menüben kattintson a **biztonsági mentés konfigurációja** megnyitásához a **biztonsági mentés konfigurációja** menü.

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

    Nyissa meg a biztonsági mentés és a biztonsági mentés célja menüben.

    ![Forgatókönyv menü megnyitása](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)
3. A biztonsági mentés célja menüben, a **a számítási feladat futtató** legördülő menüben válassza az Azure. A **Miről szeretne biztonsági másolatot készíteni?** legördülő menüből válassza a Virtuális gép lehetőséget, és kattintson az **OK** gombra.

    Ezek a műveletek regisztrálják a virtuálisgép-bővítményt a tárolóban. A biztonsági mentés célja alatt bezárul, és a **biztonsági mentési szabályzat** menü megnyitása.

    ![Forgatókönyv menü megnyitása](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. A biztonsági mentési szabályzat menüben válassza ki a tároló esetén alkalmazni szeretné a biztonsági mentési szabályzat.

    ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

    Az alapértelmezett házirend részletei megtalálhatók a legördülő menüben. Ha házirendet hozna létre, válassza az **Új létrehozása** elemet a legördülő menüből. A biztonsági mentési házirendek meghatározását segítő utasításokat itt találja: [Biztonsági mentési házirend meghatározása](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Kattintson az **OK** gombra a biztonsági mentési házirend a tárolóhoz való hozzárendeléséhez.

    A biztonsági mentési szabályzat menüje bezárul, és a **válassza ki a virtuális gépek** menü megnyitása.
5. Az a **válassza ki a virtuális gépek** menüben válassza ki a virtuális gépeket a megadott házirendhez hozzárendelni, és kattintson az **OK**.

    ![Számítási feladat kiválasztása](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    Megtörténik a kiválasztott virtuális gép ellenőrzése. Ha nem látja a várt virtuális gépeket, ellenőrizze, hogy ugyanazon az Azure-helyen találhatók-e, mint a Recovery Services-tároló, és hogy nem állnak-e már védelem alatt. A Recovery Services-tároló helye a tároló irányítópultján jelenik meg.

6. Most, hogy minden beállítást megadott a tárolóhoz, a biztonsági mentés menü, kattintson a **biztonsági mentés engedélyezése** a házirend a tároló és a virtuális gépek telepítése. A biztonsági mentési házirend telepítése nem hozza létre a virtuális géphez tartozó első helyreállítási pontot.

    ![Biztonsági mentés engedélyezése](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

A biztonsági mentés sikeres engedélyezése után a biztonsági mentési házirend az ütemezés szerint fog futni. Ennek ellenére lépjen tovább az első biztonsági mentési feladat elindításához.

## <a name="initial-backup"></a>Kezdeti biztonsági mentés
Az, hogy egy biztonsági mentési házirend lett bevezetve a virtuális gépeken, nem jelenti azt, hogy az adatokról biztonsági mentés készült. Alapértelmezés szerint az első ütemezett biztonsági mentés (a biztonsági mentési házirendben meghatározottak szerint) a kezdeti biztonsági mentés. Mindaddig, amíg a kezdeti biztonsági mentés akkor fordul elő, a legutóbbi biztonsági mentés állapota a a **biztonsági mentési feladatok** menü állapota **figyelmeztetés (kezdeti a biztonsági mentés folyamatban)**.

![Biztonsági mentés függőben](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

Hacsak a kezdeti biztonsági mentés kezdete nem a nagyon közeli jövőben van, érdemes futtatni a **Biztonsági másolat készítése** lehetőséget.

A kezdeti biztonsági mentési feladat futtatása:

1. A tároló irányítópultján kattintson a **Biztonsági mentési elemek** szakaszban található számra, vagy kattintson a **Biztonsági mentési elemek** csempére. <br/>
  ![Beállítások ikon](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  Megnyílik a **Biztonsági másolati elemek** menü.

  ![Elemek biztonsági mentése](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. Az a **biztonsági másolati elemek** menüben válassza ki az elemet.

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

  ![a biztonsági mentés menü megjelenítése](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. A biztonsági mentés menüben kattintson a naptár ikonra, használja a naptárvezérlőt annak kiválasztására a helyreállítási pont maradnak, és kattintson a **Backup**.

  ![adja meg az utolsó napot, ameddig Biztonsági mentés helyreállítási pontját meg kívánja őrizni](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Az üzembehelyezési értesítések értesítik, hogy a biztonsági mentési feladat elindult, és hogy a feladat állapotát a Biztonsági mentési feladatok oldalon figyelheti. A virtuális gép méretétől függően a kezdeti biztonsági mentés létrehozása hosszabb időt vehet igénybe.

  > [!NOTE]
  > - Az Azure backuppal biztonsági mentés alatt álló adatok titkosítása keresztül az összes [Storage Service Encryption (SSE)](../storage/common/storage-service-encryption.md).
  >
  >

6. A kezdeti biztonsági mentés állapotának megtekintéséhez vagy nyomon követéséhez a tároló irányítópultjának **Biztonsági mentési feladatok** csempéjén kattintson a **Folyamatban** elemre.

  ![Biztonsági mentési feladatok csempe](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  Megnyílik a biztonsági mentési feladatok menü.

  ![Biztonsági mentési feladatok csempe](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  Az a **biztonsági mentési feladatok** menüben láthatja az összes feladat állapotát. Ellenőrizze, hogy a virtuális gép biztonsági mentése folyamatban van-e, vagy már befejeződött. Amikor a biztonsági mentési feladat befejeződött, az állapota *Befejezve* lesz.

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
| A virtuálisgép-ügynök telepítése |<li>Töltse le és telepítse az [ügynök MSI-t](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek. <li>[Frissítse a virtuális gép tulajdonságát](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), hogy megjelenjen a telepített ügynök. |<li> Telepítse a legújabb [Linux ügynököt](https://github.com/Azure/WALinuxAgent) a GitHubról. A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek. <li> [Frissítse a virtuális gép tulajdonságát](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), hogy megjelenjen a telepített ügynök. |
| A virtuálisgép-ügynök frissítése |A virtuálisgép-ügynök frissítése a [virtuálisgép-ügynök bináris fájljainak](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) újratelepítéséből áll. <br>Győződjön meg róla, hogy nem fut biztonsági mentési művelet a virtuálisgép-ügynök frissítése közben. |Kövesse a [linuxos virtuálisgép-ügynök frissítését](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ismertető szakasz utasításait. <br>Győződjön meg róla, hogy nem fut biztonsági mentési művelet a virtuálisgép-ügynök frissítése közben. |
| A virtuálisgép-ügynök telepítésének érvényesítése |<li>Lépjen a *C:\WindowsAzure\Packages* mappába az Azure virtuális gépen. <li>Itt találja a WaAppAgent.exe fájlt.<li> Kattintson jobb gombbal a fájlra, válassza a **Tulajdonságok** parancsot, majd nyissa meg a **Részletek** lapot. A Termék verziószáma mezőben 2.6.1198.718 vagy újabb verziónak kell lennie. |– |

### <a name="backup-extension"></a>Backup bővítmény
Miután a virtuálisgép-ügynök telepítve lett a virtuális gépen, az Azure Backup szolgáltatás telepíti a biztonsági mentési bővítményt a virtuálisgép-ügynökhöz. Az Azure Backup szolgáltatás akadásmentesen frissíti és javítja a biztonsági mentési bővítményt a felhasználó további beavatkozása nélkül.

A Backup szolgáltatás akkor is telepíti a biztonsági mentési bővítményt, ha a virtuális gép nem fut. Egy futó virtuális gép adja a legnagyobb esélyt egy alkalmazással konzisztens helyreállítási pont létrehozásának. Ugyanakkor az Azure Backup szolgáltatás folytatja a virtuális gép biztonsági másolatának elkészítését akkor is, ha az ki van kapcsolva és a bővítményt nem lehet telepíteni. Az ilyen típusú biztonsági mentés Offline virtuális gép néven ismeretes, és a helyreállítási pont *összeomlás-konzisztens*.

## <a name="troubleshooting-information"></a>Hibaelhárítási információ
Ha problémákba ütközik a cikkben leírt feladatok elvégzésében, tekintse meg a [Hibaelhárítási útmutatót](backup-azure-vms-troubleshoot.md).

## <a name="pricing"></a>Díjszabás
Az Azure-beli virtuális gépek biztonsági mentésének költsége a védett példányok számától függ. A védett példányok definíciójáért lásd [a fogalmat ismertető szakaszt](backup-introduction-to-azure-backup.md#what-is-a-protected-instance). [A Backup díjszabásával](https://azure.microsoft.com/pricing/details/backup/) kapcsolatban lásd az Azure Backup díjszabását ismertető oldalt.

## <a name="next-steps"></a>További lépések

[Kezelése](backup-azure-manage-vms.md) a biztonsági másolatokat.
