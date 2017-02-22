---
title: "Áttekintés: Azure virtuális gépek védelme Recovery Services-tárolóval | Microsoft Docs"
description: "Megvédheti Azure virtuális gépeit egy Recovery Services-tárolóval. Adatai védelmére a Resource Manager-alapú és a klasszikus üzemi modellen alapuló virtuális gépek, a Premium Storage-alapú virtuális gépek, a titkosított virtuális gépek és a Managed Disksen található virtuális gépek biztonsági másolatait használhatja. Létrehozhat és regisztrálhat egy Recovery Services-tárolót. Regisztrálhat virtuális gépeket, létrehozhat egy házirendet, és megvédheti virtuális gépeit az Azure-ban."
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
keyword: backups; vm backup
ms.assetid: 45e773d6-c91f-4501-8876-ae57db517cd1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/06/2017
ms.author: markgal; jimpark
translationtype: Human Translation
ms.sourcegitcommit: b73d5bb1f4f9e7adc65bf38a3c98db846a341cb3
ms.openlocfilehash: 2d8bedcf51cf55e13cc22d5a2d258218f0a786bb


---
# <a name="first-look-protect-azure-vms-with-a-recovery-services-vault"></a>Áttekintés: Azure virtuális gépek védelme Recovery Services-tárolóval
> [!div class="op_single_selector"]
> * [Virtuális gépek védelme Recovery Services-tárolóval](backup-azure-vms-first-look-arm.md)
> * [Virtuális gépek védelme Backup-tárolóval](backup-azure-vms-first-look.md)
>
>

Ez az oktatóprogram végigkíséri egy Recovery Services-tároló létrehozásának és egy Azure virtuális gép (VM) biztonsági mentésének lépésein. A Recovery Services-tárolók megvédik:

* Az Azure Resource Manager által telepített virtuális gépeket
* A klasszikus virtuális gépeket
* A Standard szintű tárolós virtuális gépeket
* A Prémium szintű tárolós virtuális gépeket 
* A Managed Disksen futó virtuális gépek
* Az Azure Disk Encryption használatával titkosított virtuális gépeket, amelyek rendelkeznek BEK-kel és KEK-kel

A Prémium szintű tárolós virtuális gépek védelméről további információ: [Prémium szintű tárolós virtuális gépek biztonsági mentése és visszaállítása](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup). További információ a felügyelt lemezeken található virtuális gépek támogatásáról: [ Virtuális gépek biztonsági mentése és visszaállítása felügyelt lemezeken](backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup)

> [!NOTE]
> Ez az oktatóprogram feltételezi, hogy Ön már rendelkezik egy virtuális géppel az Azure-előfizetésében, és hogy lépéseket tett azért, hogy a biztonsági mentési szolgáltatás hozzáférjen a virtuális géphez.
>
>

[!INCLUDE [learn-about-Azure-Backup-deployment-models](../../includes/backup-deployment-models.md)]

A védelem alá vonni kívánt virtuális gépek számától függően különféle kezdőpontokról indulhat – ha több virtuális gépről szeretne egyetlen műveletben biztonsági mentést készíteni, lépjen a Recovery Services tárolóba, és indítsa el a biztonsági mentést a tár irányítópultjáról. Ha egyetlen virtuális géppel rendelkezik, amelyről biztonsági mentést készítene, azt közvetlenül a virtuális gép felügyeleti paneljéről is megteheti.

## <a name="configure-backup-from-vm-management-blade"></a>Biztonsági mentés konfigurálása a Virtuális gép kezelése panelről
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. A központi menüben kattintson a **További szolgáltatások** elemre, majd az erőforrások listájába írja be a következőt: **Virtuális gépek**.  Megjelenik a virtuális gépek listája. A virtuális gépek listájából válasszon ki egyet, amelyről biztonsági mentést szeretne készíteni. Ezzel megnyílik a virtuális gépek kezelőpanelje.
 ![Virtuális gép kezelése panel](./media/backup-azure-vms-first-look-arm/vm-management-blade.png)

3. A Virtuális gép kezelése panelen kattintson a „Backup” lehetőségre a bal oldalon a Beállítások területen.
![Backup beállítás a Virtuális gép kezelése panelen](./media/backup-azure-vms-first-look-arm/backup-option-vm-management-blade.png)

4. Ezzel megnyílik a Biztonsági mentés engedélyezése panel. Ez a panel két bemenetet vár: a Recovery Services-tárolót (ez egy Azure Backup-erőforrás, amely a virtuális gép biztonsági másolatát tárolja); valamint a biztonsági mentési házirendet, amely a mentések ütemezését és a másolatok megőrzésének időtartamát határozza meg. A panel alapértelmezett beállításokat tartalmaz. A beállításokat a biztonsági mentési igényeknek megfelelően testre szabhatja.

  ![Biztonsági mentés engedélyezése varázsló](./media/backup-azure-vms-first-look-arm/vm-blade-enable-backup.png)

5. Recovery Services-tárolóként kiválaszthat egy meglévő tárolót, vagy létrehozhat egy újat. Ha új tárolót hoz létre, az ugyanabban az erőforráscsoportban, ugyanazokkal a virtuálisgép- és helybeállításokkal jön létre, mint a virtuális gép. Ha más értékekkel szeretne Recovery Services-tárolót létrehozni, végezze el a [Recovery Services tároló létrehozását](backup-azure-vms-first-look-arm.md#create-a-recovery-services-vault-for-a-vm), mielőtt a&3;. lépésben a Backup beállításra kattintana, és ott kiválasztaná azt a panelen.

6. A Biztonsági mentési házirend panelen válassza ki a tároló esetén alkalmazni kívánt biztonsági mentési házirendet, és kattintson az **OK** gombra.
    ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy-new.png)

    Az alapértelmezett házirend részletei megtalálhatók a részletek listában. Ha házirendet hozna létre, válassza az **Új létrehozása** elemet a legördülő menüből. A legördülő menü arra is lehetőséget biztosít, hogy a pillanatkép elkészítésének időpontját is beállítsa. A biztonsági mentési házirendek meghatározását segítő utasításokat itt találja: [Biztonsági mentési házirend meghatározása](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Miután az **OK** gombra kattint, a biztonsági mentési házirend hozzá lesz rendelve a virtuális géphez.

7. Kattintson a „Biztonsági mentés engedélyezése” gombra a biztonsági mentés konfigurálásához a virtuális gépen. Ezzel megkezdődik az üzembe helyezés.
![Biztonsági mentés engedélyezése gomb](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-button.png)

8. A konfiguráció folyamata az értesítések alapján követhető.
![Biztonsági mentési értesítések engedélyezése](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-notification.png)

9. Miután befejeződött a Biztonsági mentés konfigurálásának üzembe helyezése, ha a Virtuális gép kezelése panelen a „Backup” beállításra kattint, a rendszer a biztonsági mentés alá vont virtuális gépnek megfelelő Biztonsági mentési elemek panelre lépteti.
![Virtuális gép biztonsági mentési elemei nézet](./media/backup-azure-vms-first-look-arm/backup-item-view.png)

## <a name="configure-backup-from-recovery-services-vault-view"></a>Biztonsági mentés konfigurálása a Recovery Services-tároló nézetből
Lényegében a következő lépéseket fogja végrehajtani.  

1. Létrehoz egy Recovery Services-tárolót egy virtuális géphez.
2. Az Azure portál használatával választ egy Forgatókönyvet, beállít egy Házirendet és azonosítja a megvédendő elemeket.
3. Futtatja a kezdeti biztonsági mentést.

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Létrehoz egy Recovery Services-tárolót egy virtuális géphez.
A Recovery Services-tároló egy olyan entitás, amely eltárolja az idők során létrehozott biztonsági mentéseket és helyreállítási pontokat. A Recovery Services-tároló tartalmazza a védelem alatt álló virtuális gépeken alkalmazott biztonsági mentési házirendet is.

> [!NOTE]
> A virtuális gépek biztonsági mentése egy helyi folyamat. Nem lehet biztonsági mentést készíteni egy adott helyen található virtuális gépről egy másik helyen található Recovery Services-tárolóra. Így aztán minden olyan Azure-helyen, ahol biztonsági mentésre szoruló virtuális gépek vannak, legalább egy Recovery Services-tárolónak kell lennie.
>
>

Egy Recovery Services-tároló létrehozásához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A központi menüben kattintson a **Tallózás** elemre, majd az erőforrások listájába írja be a következőt: **Recovery Services**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Kattintson a **Recovery Services-tároló** elemre.

    ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

    A Recovery Services-tárolók listája megjelenik.
3. A **Recovery Services-tárolók** menüben kattintson a **Hozzáadás** elemre.

    ![Recovery Services-tároló létrehozása – 2. lépés](./media/backup-azure-vms-first-look-arm/rs-vault-menu.png)

    Megnyílik a Recovery Services-tároló panelje, a rendszer pedig egy **Név**, **Előfizetés**, **Erőforráscsoport** és **Hely** megadását kéri.

    ![Recovery Services-tároló létrehozása – 5. lépés](./media/backup-azure-vms-first-look-arm/rs-vault-attributes.png)
4. A **Név** mezőben adjon meg egy egyszerű nevet a tároló azonosításához. A névnek egyedinek kell lennie az Azure-előfizetéshez. Írjon be egy 2–50 karakter hosszúságú nevet. Ennek egy betűvel kell kezdődnie, és csak betűket, számokat és kötőjeleket tartalmazhat.
5. Kattintson az **Előfizetés** elemre az elérhető előfizetések listájának megtekintéséhez. Ha nem biztos benne, hogy melyik előfizetést szeretné használni, használja az alapértelmezett (vagy javasolt) előfizetést. Csak akkor lesz több választási lehetőség, ha a szervezetéhez tartozó fiók több Azure-előfizetéssel van összekötve.
6. Kattintson az **Erőforráscsoport** elemre az elérhető erőforráscsoportok listájának megtekintéséhez, vagy kattintson az **Új** elemre egy új erőforráscsoport létrehozásához. Átfogó információk az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md).
7. Kattintson a **Hely** elemre a tárolóhoz tartozó földrajzi régió kiválasztásához. A tárolónak ugyanabban a régióban **kell** lennie, mint a megvédeni kívánt virtuális gépeknek.

   > [!IMPORTANT]
   > Ha nem biztos a virtuális gép helyében, lépjen ki a tároló-létrehozási párbeszédpanelből, és lépjen a virtuális gépek listájához a portálon. Ha több régióban rendelkezik virtuális gépekkel, minden régióban hozzon létre egy Recovery Services-tárolót. Hozza létre a tárolót az első helyen, majd lépjen a következő helyre. Az adatok biztonsági másolatának tárolásához nincs szükség tárfiókok megadására – a Recovery Services-tároló és az Azure Backup szolgáltatás ezt automatikusan kezeli.
   >
   >
8. Kattintson a **Létrehozás** gombra. A Recovery Services-tároló létrehozása eltarthat egy ideig. Figyelje az állapotértesítéseket a portál jobb felső területén. Miután a tároló létrejött, megjelenik a Recovery Services-tárolók listájában.

    ![A Backup-tárolók listája](./media/backup-azure-vms-first-look-arm/rs-list-of-vaults.png)

Most, hogy létrehozta a tárolóját, megismerkedhet a tárreplikáció beállításának módjával.

### <a name="set-storage-replication"></a>Tárreplikáció beállítása
A tárreplikáció lehetősége lehetővé teszi, hogy georedundáns tárolás és helyileg redundáns tárolás között válasszon. Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik. Ha ez az elsődleges biztonsági mentési tároló, hagyja a beállítást georedundáns tároláson. Ha egy olcsóbb, rövidebb élettartamú megoldást szeretne, válassza a helyileg redundáns tárolást. A [georedundáns](../storage/storage-redundancy.md#geo-redundant-storage) és a [helyileg redundáns](../storage/storage-redundancy.md#locally-redundant-storage) tárolási lehetőségekről többet olvashat az [Azure tárreplikáció áttekintése](../storage/storage-redundancy.md) című cikkben.

A tárreplikációs beállítás szerkesztése:

1. Válassza ki a tárolót, amelyhez megnyitja a tároló irányítópultját és a Beállítások panelt. Ha a **Beállítások** panel nem nyílik meg, kattintson az **Összes beállítás** elemre a tároló irányítópultján.
2. A **Beállítások** panelen kattintson a **Biztonsági mentési infrastruktúra** > **Biztonsági mentés konfigurációja** elemre a **Biztonsági mentés konfigurációja** panel megnyitásához. A **Biztonsági mentés konfigurációja** panelen válassza ki a tárreplikációs beállítást a tároló számára.

    ![A Backup-tárolók listája](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Miután kiválasztotta a tárolási beállítást a tároló számára, készen áll, hogy hozzárendelje a virtuális gépet a tárolóhoz. A hozzárendelés megkezdéséhez fel kell fedezni és regisztrálni kell az Azure virtuális gépeket.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Válassza ki a biztonsági mentés célját, állítsa be a házirendet, és határozza meg a megvédeni kívánt elemeket
Mielőtt regisztrálna egy virtuális gépet a tárolóval, futtassa a felfedezési folyamatot, hogy meggyőződjön arról, hogy az előfizetéshez hozzáadott minden új virtuális gép azonosítva lett. A folyamat lekéri az Azure-ból az előfizetésben található virtuális gépek listáját, olyan kiegészítő információkkal, mint a felhőszolgáltatás neve és a régió. Az Azure portálon a forgatókönyv arra utal, amit bele fog tenni a Recovery Services-tárolóba. A házirend adja meg a helyreállítási pontok gyakoriságának és elhelyezési idejének a menetrendjét. A házirend emellett tartalmazza a helyreállítási pontok megőrzési tartományát.

1. Ha már meg van nyitva egy Recovery Services-tároló, folytassa a 2. lépéssel. Ha nincs megnyitva egy Recovery Services-tároló, de az Azure Portal igen, a központi menüben kattintson a **Tallózás** elemre.

   * Az erőforrások listájába írja be a következőt: **Recovery Services**.
   * Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Amikor meglátja a **Recovery Services-tárolót**, kattintson rá.

     ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

     A Recovery Services-tárolók listája megjelenik.
   * A Recovery Services-tárolók listájából válasszon ki egy tárolót.

     Megnyílik a kiválasztott tároló irányítópultja.

     ![Tároló panelének megnyitása](./media/backup-azure-vms-first-look-arm/vault-settings.png)
2. A tároló irányítópultos menüjében kattintson a **Biztonsági mentés** elemre a Biztonsági mentés panel megnyitásához.

    ![Biztonsági mentés panel megnyitása](./media/backup-azure-vms-first-look-arm/backup-button.png)

    Amikor a panel megnyílik, a Backup szolgáltatás új virtuális gépeket keres az előfizetésben.

    ![Virtuális gépek felderítése](./media/backup-azure-vms-first-look-arm/discovering-new-vms.png)
3. A Biztonsági mentés panelen kattintson a **Biztonsági mentés célja** elemre a Biztonsági mentés célja panel megnyitásához.

    ![Forgatókönyv panel megnyitása](./media/backup-azure-vms-first-look-arm/select-backup-goal-one.png)
4. A Biztonsági mentés célja panelen állítsa a **Számítási feladat futásának helye** beállítást az Azure-ra, és a **Miről készüljön biztonsági mentés** beállítást virtuális gépre, majd kattintson az **OK** gombra.

    A Biztonsági mentés célja panel bezárul, a Biztonsági mentési házirend panel pedig megnyílik.

    ![Forgatókönyv panel megnyitása](./media/backup-azure-vms-first-look-arm/select-backup-goal-two.png)
5. A Biztonsági mentési házirend panelen válassza ki a tároló esetén alkalmazni kívánt biztonsági mentési házirendet, és kattintson az **OK** gombra.

    ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy-new.png)

    Az alapértelmezett házirend részletei megtalálhatók a részletek listában. Ha házirendet hozna létre, válassza az **Új létrehozása** elemet a legördülő menüből. A legördülő menü arra is lehetőséget biztosít, hogy a pillanatkép elkészítésének idejét 19 órára váltsa. A biztonsági mentési házirendek meghatározását segítő utasításokat itt találja: [Biztonsági mentési házirend meghatározása](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Miután az **OK** gombra kattint, a biztonsági mentési házirend hozzá lesz rendelve a tárolóhoz.

    Ezután válassza ki azon virtuális gépeket, amelyeket a tárolóhoz szeretne rendelni.
6. Válassza ki a megadott házirendhez hozzárendelni kívánt virtuális gépeket, majd kattintson a **Kiválasztás** gombra.

    ![Számítási feladat kiválasztása](./media/backup-azure-vms-first-look-arm/select-vms-to-backup-new.png)

    Ha nem látja a kívánt virtuális gépet, ellenőrizze, hogy ugyanazon az Azure-helyen található-e, mint a Recovery Services-tároló.
7. Most, hogy minden beállítást megadott a tárolóhoz, a Backup panelen kattintson a **Biztonsági mentés engedélyezése** elemre az oldal alján. Ez üzembe helyezi a tároló és a virtuális gépek házirendjét.

    ![Biztonsági mentés engedélyezése](./media/backup-azure-vms-first-look-arm/enable-backup-settings-new.png)

## <a name="initial-backup"></a>Kezdeti biztonsági mentés
Az, hogy egy biztonsági mentési házirend lett bevezetve a virtuális gépeken, nem jelenti azt, hogy az adatokról biztonsági mentés készült. Alapértelmezés szerint az első ütemezett biztonsági mentés (a biztonsági mentési házirendben meghatározottak szerint) a kezdeti biztonsági mentés. A kezdeti biztonsági mentés végrehajtásáig a **Biztonsági mentési feladatok** panelen az Utolsó biztonsági mentés állapota **Figyelmeztetés (kezdeti biztonsági mentés folyamatban)** állapotú.

![Biztonsági mentés függőben](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

Hacsak a kezdeti biztonsági mentés kezdete nem a nagyon közeli jövőben van, érdemes futtatni a **Biztonsági másolat készítése** lehetőséget.

A **Biztonsági másolat készítése** futtatásához:

1. A tároló irányítópultjának **Biztonsági mentés** csempéjén kattintson az **Azure virtuális gépek** elemre <br/>
    ![Beállítások ikon](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    Megnyílik a **Biztonsági mentési elemek** panel.
2. A **Biztonsági mentési elemek** panelen kattintson jobb gombbal a tárolóra, amelyről biztonsági másolatot szeretne készíteni, és kattintson a **Biztonsági másolat készítése** elemre.

    ![Beállítások ikon](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    A biztonsági mentési feladat elindul. <br/>

    ![Biztonsági mentési feladat elindul](./media/backup-azure-vms-first-look-arm/backup-triggered.png)
3. A kezdeti biztonsági mentés befejezettségének megtekintéséhez a tároló irányítópultjának **Biztonsági mentési feladatok** csempéjén kattintson az **Azure virtuális gépek** elemre.

    ![Biztonsági mentési feladatok csempe](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    Megnyílik a Biztonsági mentési feladatok panel.
4. A Biztonsági mentési feladatok panelen megtekintheti az összes feladat állapotát.

    ![Biztonsági mentési feladatok csempe](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

   > [!NOTE]
   > A biztonsági mentési művelet részeként az Azure Backup szolgáltatás egy parancsot ad minden virtuális gépre a biztonsági mentési bővítménynek, hogy ürítsen ki minden írást, és készítsen egy egységes pillanatképet.
   >
   >

    Amikor a biztonsági mentési feladat befejeződött, az állapota *Befejezve* lesz.

[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Telepítse a virtuális gép ügynökét a virtuális gépre.
Ez az információ szükség esetén mellékelve van. Az Azure virtuálisgép-ügynököt telepíteni kell az Azure virtuális gépre, hogy a Backup bővítmény működjön. Ugyanakkor ha a virtuális gépe az Azure-katalógusból lett létrehozva, a virtuálisgép-ügynök már jelen van a virtuális gépen. A helyszíni adatközpontokból áttelepített virtuális gépek nem rendelkeznek telepített virtuálisgép-ügynökkel. Ebben az esetben a virtuális gép ügynökét telepíteni kell. Ha problémákba ütközik az Azure virtuális gép telepítése közben, ellenőrizze, hogy az Azure virtuálisgép-ügynök megfelelően telepítve van-e a virtuális gépen (lásd az alábbi táblázatot). Ha egy egyéni virtuális gépet hoz létre, [a **Virtuálisgép-ügynök telepítése** jelölőnégyzet legyen bejelölve](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) a virtuális gép kiépítése előtt.

Többet is megtudhat itt a [virtuálisgép-ügynökről](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) és arról, [hogyan kell telepíteni](../virtual-machines/virtual-machines-windows-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

A következő táblázat további információkat tartalmaz a Windows és Linux virtuális gépekhez tartozó virtuálisgép-ügynökről.

| **Művelet** | **Windows** | **Linux** |
| --- | --- | --- |
| A virtuálisgép-ügynök telepítése |<li>Töltse le és telepítse az [ügynök MSI-t](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek. <li>[Frissítse a virtuális gép tulajdonságát](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), hogy megjelenjen a telepített ügynök. |<li> Telepítse a legújabb [Linux ügynököt](https://github.com/Azure/WALinuxAgent) a GitHubról. A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek. <li> [Frissítse a virtuális gép tulajdonságát](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), hogy megjelenjen a telepített ügynök. |
| A virtuálisgép-ügynök frissítése |A virtuálisgép-ügynök frissítése a [virtuálisgép-ügynök bináris fájljainak](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) újratelepítéséből áll. <br>Győződjön meg róla, hogy nem fut biztonsági mentési művelet a virtuálisgép-ügynök frissítése közben. |Kövesse a [Linux virtuálisgép-ügynök frissítése](../virtual-machines/virtual-machines-linux-update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) szakasz utasításait. <br>Győződjön meg róla, hogy nem fut biztonsági mentési művelet a virtuálisgép-ügynök frissítése közben. |
| A virtuálisgép-ügynök telepítésének érvényesítése |<li>Lépjen a *C:\WindowsAzure\Packages* mappába az Azure virtuális gépen. <li>Itt találja a WaAppAgent.exe fájlt.<li> Kattintson jobb gombbal a fájlra, válassza a **Tulajdonságok** parancsot, majd nyissa meg a **Részletek** lapot. A Termék verziószáma mezőben 2.6.1198.718 vagy újabb verziónak kell lennie. |N/A |

### <a name="backup-extension"></a>Backup bővítmény
Miután a virtuálisgép-ügynök telepítve lett a virtuális gépen, az Azure Backup szolgáltatás telepíti a biztonsági mentési bővítményt a virtuálisgép-ügynökhöz. Az Azure Backup szolgáltatás akadásmentesen frissíti és javítja a biztonsági mentési bővítményt a felhasználó további beavatkozása nélkül.

A biztonsági mentési bővítményt a Backup szolgáltatás a virtuális gép futásától függetlenül telepíti. Egy futó virtuális gép adja a legnagyobb esélyt egy alkalmazással konzisztens helyreállítási pont létrehozásának. Ugyanakkor az Azure Backup szolgáltatás folytatja a virtuális gép biztonsági másolatának elkészítését akkor is, ha az ki van kapcsolva és a bővítményt nem lehet telepíteni. Ennek neve offline virtuális gép. Ebben az esetben a helyreállítási pont az *összeomláshoz igazodik* lesz.

## <a name="troubleshooting-information"></a>Hibaelhárítási információ
Ha problémákba ütközik a cikkben leírt feladatok elvégzésében, tekintse meg a [Hibaelhárítási útmutatót](backup-azure-vms-troubleshoot.md).

## <a name="pricing"></a>Díjszabás
Az Azure VM-ek biztonsági mentése a védett példányok modellje alapján kerül kiszámlázására. További információ a [Backup díjszabásról](https://azure.microsoft.com/pricing/details/backup/)

## <a name="questions"></a>Kérdései vannak?
Ha kérdései vannak, vagy van olyan szolgáltatás, amelyről hallani szeretne, [küldjön visszajelzést](http://aka.ms/azurebackup_feedback).



<!--HONumber=Feb17_HO2-->


