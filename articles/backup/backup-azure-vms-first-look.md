---
title: "Áttekintés: Azure virtuális gépek védelme Backup-tárolóval | Microsoft Docs"
description: "Megvédheti Azure virtuális gépeit egy Backup-tárolóval. Ez az oktatóanyag azt írja le, hogyan regisztrálhat virtuális gépeket, hozhat létre egy házirendet és védheti meg virtuális gépeit az Azure-ban."
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
ms.assetid: 722820dc-b65f-425c-a9e5-c1946e896a87
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/15/2016
ms.author: markgal; jimpark
translationtype: Human Translation
ms.sourcegitcommit: 9eddde0df61ac7dde9b24f973e7eec786cd48f24
ms.openlocfilehash: b8f62d1fab4b9132f35e2fab632968cf001c832d


---
# <a name="first-look-backing-up-azure-virtual-machines"></a>Áttekintés: Azure virtuális gépek biztonsági mentése
> [!div class="op_single_selector"]
> * [Virtuális gépek védelme Recovery Services-tárolóval](backup-azure-vms-first-look-arm.md)
> * [Azure virtuális gépek védelme Backup-tárolóval](backup-azure-vms-first-look.md)
>
>

Ez az oktatóanyag végigvezeti egy Azure virtuális gép (VM) egy Azure-beli Backup-tárolóba való biztonsági mentésének lépésein. Ez a cikk a virtuális gépek biztonsági mentésének klasszikus modelljét vagy Service Managerrel üzemi modelljét ismerteti. Ha többet szeretne tudni a virtuális gépek egy erőforráscsoporthoz tartozó Recovery Services-tárolóba való biztonsági mentéséről, olvassa el az [Áttekintés: Virtuális gépek védelme Recovery Services-tárolóval](backup-azure-vms-first-look-arm.md) című cikket. Az oktatóanyag sikeres elvégzéséhez a következő előfeltételek szükségesek:

* Létrehozott egy virtuális gépet az Azure-előfizetésben.
* A virtuális gép csatlakozik Azure nyilvános IP-címekhez. További információkért lásd: [Hálózati kapcsolatok](backup-azure-vms-prepare.md#network-connectivity).

A virtuális gépek biztonsági mentésének öt fő lépése van:  

![első lépés](./media/backup-azure-vms-first-look/step-one.png) Backup-tároló létrehozása vagy meglévő Backup-tároló azonosítása. <br/>
![második lépés](./media/backup-azure-vms-first-look/step-two.png) A virtuális gépek felderítése és regisztrálása a klasszikus Azure portállal. <br/>
![harmadik lépés](./media/backup-azure-vms-first-look/step-three.png) A virtuálisgép-ügynök telepítése. <br/>
![negyedik lépés](./media/backup-azure-vms-first-look/step-four.png) A virtuális gépeket védő házirend létrehozása. <br/>
![ötödik lépés](./media/backup-azure-vms-first-look/step-five.png) A biztonsági mentés futtatása.

![A virtuális gépek biztonsági mentési folyamatának áttekintése](./media/backup-azure-vms-first-look/backupazurevm-classic.png)

> [!NOTE]
> Az Azure két üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md). Ez az oktatóanyag olyan virtuális gépekhez készült, amelyeket a klasszikus Azure portálon lehet létrehozni. Az Azure Backup szolgáltatás támogatja a Resource Manager-alapú virtuális gépeket. A virtuális gépek Recovery Services-tárolóba való biztonsági mentésének részleteit az [Áttekintés: Virtuális gépek védelme Recovery Services-tárolóval](backup-azure-vms-first-look-arm.md) című cikkben tekintheti meg.
>
>

## <a name="step-1-create-a-backup-vault-for-a-vm"></a>1. lépés – Backup-tároló létrehozása egy virtuális géphez
A Backup-tároló egy olyan entitás, amely tárolja az idők során létrehozott biztonsági mentéseket és helyreállítási pontokat. A Backup-tároló azokat a biztonsági mentési házirendeket is tartalmazza, amelyek a biztonsági mentés alatt álló virtuális gépekre érvényesek.

1. Jelentkezzen be a [klasszikus Azure portálra](http://manage.windowsazure.com/).
2. Kattintson az Azure Portal bal alsó sarkában található **Új** gombra.

    ![Kattintson az Új menüre](./media/backup-azure-vms-first-look/new-button.png)
3. A Gyorslétrehozás varázslóban kattintson az **Adatszolgáltatások** > **Recovery Services** > **Backup-tároló** > **Gyorslétrehozás** elemre.

    ![Backup-tároló létrehozása](./media/backup-azure-vms-first-look/new-vault-wizard-one-subscription.png)

    A varázsló a **Név** és a **Régió** megadását kéri. Ha több előfizetést felügyel, megjelenik az előfizetés kiválasztására szolgáló párbeszédpanel.
4. A **Név** mezőben adjon meg egy egyszerű nevet a tároló azonosításához. A névnek egyedinek kell lennie az Azure-előfizetéshez.
5. A **Régió** területen válassza ki a tároló földrajzi régióját. A tárolónak ugyanabban a régióban **kell** lennie, mint az általa védett virtuális gépeknek.

    Ha nem tudja, hogy melyik régióban található a virtuális gép, zárja be ezt a varázslót, és kattintson az Azure-szolgáltatások listájában a **Virtual Machines** elemre. A Hely oszlopban látható a régió neve. Ha több régióban rendelkezik virtuális gépekkel, minden régióban hozzon létre egy Backup-tárolót.
6. Ha nincs **Előfizetés** párbeszédpanel a varázslóban, ugorjon a következő lépésre. Ha több előfizetést használ, válassza ki az új Backup-tárolóval társítani kívánt előfizetést.

    ![Bejelentési értesítés létrehozása a tárolóhoz](./media/backup-azure-vms-first-look/backup-vaultcreate.png)
7. Kattintson a **Tároló létrehozása** gombra. A Backup-tároló létrehozása eltarthat egy ideig. Figyelje az állapottal kapcsolatos értesítéseket a portál alján.

    ![Bejelentési értesítés létrehozása a tárolóhoz](./media/backup-azure-vms-first-look/create-vault-demo.png)

    Egy üzenet megerősíti, hogy a tároló sikeresen létrejött. Megtalálható a **Recovery Services** lapon, és **Aktív** állapotú.

    ![Bejelentési értesítés létrehozása a tárolóhoz](./media/backup-azure-vms-first-look/create-vault-demo-success.png)
8. A **Recovery Services** lapon a tárolók listájából válassza ki a létrehozott tárolót a **Gyors üzembe helyezés** lap elindításához.

    ![A Backup-tárolók listája](./media/backup-azure-vms-first-look/active-vault-demo.png)
9. A **Gyors üzembe helyezés** lapon kattintson a **Konfigurálás** gombra a tárreplikációs beállítás megnyitásához.
    ![A Backup-tárolók listája](./media/backup-azure-vms-first-look/configure-storage.png)
10. A **tárreplikációs** beállítások között válassza ki a kívánt replikációs beállítást a tárolóhoz.

    ![A Backup-tárolók listája](./media/backup-azure-vms-first-look/backup-vault-storage-options-border.png)

    Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik. Ha ez az elsődleges biztonsági mentési tároló, válassza a georedundáns tárolást. Ha egy olcsóbb, rövidebb élettartamú megoldást szeretne, válassza a helyileg redundáns tárolást. A georedundáns és a helyileg redundáns tárolási lehetőségekről többet olvashat az [Azure Storage replication overview](../storage/storage-redundancy.md) (Azure tárreplikáció áttekintése) című cikkben.

Miután kiválasztotta a tárolási beállítást a tároló számára, készen áll, hogy hozzárendelje a virtuális gépet a tárolóhoz. A hozzárendelés megkezdéséhez fel kell deríteni és regisztrálni kell az Azure virtuális gépeket.

## <a name="step-2-discover-and-register-azure-virtual-machines"></a>2. lépés – Az Azure virtuális gépek felderítése és regisztrálása
Mielőtt regisztrálná a virtuális gépet egy tárolóval, futtassa a felderítési folyamatot az új virtuális gépek azonosítása érdekében. Ez visszaadja az előfizetésben található virtuális gépek listáját, olyan kiegészítő információkkal, mint a felhőszolgáltatás neve és a régió.

1. Jelentkezzen be a [klasszikus Azure portálra](http://manage.windowsazure.com/).
2. A klasszikus Azure portálon kattintson a **Recovery Services** elemre a Recovery Services-tárolók listájának megnyitásához.
    ![Számítási feladat kiválasztása](./media/backup-azure-vms-first-look/recovery-services-icon.png)
3. A tárolók listájában válassza ki a virtuális gép biztonsági mentéséhez használni kívánt tárolót.

    Amikor kiválasztja a tárolót, megnyílik a **Gyors üzembe helyezés** oldal.
4. A tároló menüjében kattintson a **Regisztrált elemek** lehetőségre.

    ![Számítási feladat kiválasztása](./media/backup-azure-vms-first-look/configure-registered-items.png)
5. A **Típus** menüből válassza az **Azure virtuális gép** elemet.

    ![Számítási feladat kiválasztása](./media/backup-azure-vms/discovery-select-workload.png)
6. Kattintson a lap alján található **FELDERÍTÉS** gombra.
    ![Felderítés gomb](./media/backup-azure-vms/discover-button-only.png)

    A felderítési folyamat eltarthat pár percig, amíg a virtuális gépeket táblázatba rendezi a rendszer. A képernyő alján lévő értesítés jelzi, hogy a folyamat fut.

    ![Virtuális gépek felderítése](./media/backup-azure-vms/discovering-vms.png)

    Az értesítés módosul, amikor a folyamat befejeződött.

    ![A felderítés kész](./media/backup-azure-vms-first-look/discovery-complete.png)
7. Kattintson a lap alján lévő **REGISZTRÁLÁS** gombra.
    ![Regisztrálás gomb](./media/backup-azure-vms-first-look/register-icon.png)
8. Az **Elemek regisztrálása** helyi menüben válassza ki azokat a virtuális gépeket, amelyeket regisztrálni szeretne.

   > [!TIP]
   > Egyszerre több virtuális gép is regisztrálható.
   >
   >

    Létrejön egy feladat minden egyes kiválasztott virtuális géphez.
9. Kattintson a **Feladat megtekintése** gombra az értesítésben a **Feladatok** lap megnyitásához.

    ![Regisztrációs feladat](./media/backup-azure-vms/register-create-job.png)

    A virtuális gép a regisztrált elemek listájában is megjelenik a regisztrációs művelet állapotával együtt.

    ![1. regisztrációs állapot](./media/backup-azure-vms/register-status01.png)

    A művelet befejezése után az állapot módosul, hogy a *regisztrált* állapotot jelezze.

    ![2. regisztrációs állapot](./media/backup-azure-vms/register-status02.png)

## <a name="step-3-install-the-vm-agent-on-the-virtual-machine"></a>3. lépés – A virtuálisgép-ügynök telepítése a virtuális gépre
Az Azure virtuálisgép-ügynököt telepíteni kell az Azure virtuális gépre, hogy a Backup bővítmény működjön. Ha a virtuális gépe az Azure-katalógusból lett létrehozva, a virtuálisgép-ügynök már megtalálható a virtuális gépen. Ekkor [a virtuális gépek védelmével](backup-azure-vms-first-look.md#step-4---create-the-backup-policy) folytathatja.

Ha a virtuális gépe helyszíni adatközpontból lett áttelepítve, a virtuális gépre valószínűleg nincs telepítve a virtuálisgép-ügynök. Telepítenie kell a virtuálisgép-ügynököt a virtuális gépen a virtuális gép védelme előtt. A virtuálisgép-ügynök telepítésének részletes lépéseiért lásd [a virtuális gépek biztonsági mentéséről szóló cikk virtuálisgép-ügynökökre vonatkozó szakaszát](backup-azure-vms-prepare.md#vm-agent).

## <a name="step-4-create-the-backup-policy"></a>4. lépés – A biztonsági mentési házirend létrehozása
A kezdeti biztonsági mentési feladat elindítása előtt készítse el a biztonsági mentési pillanatképek rögzítésének ütemezését. A biztonsági mentési házirend azt az ütemtervet jelenti, amely alapján biztonsági mentési pillanatképek készülnek, valamint ezen pillanatképek megőrzésének hosszát. A megőrzési információk a nagyapa-apa-fiú típusú biztonságimentés-rotációs sémán alapulnak.

1. Keresse meg a klasszikus Azure portál **Recovery Services** területén a Backup-tárolót, és kattintson a **Regisztrált elemek** lehetőségre.
2. Válassza az **Azure virtuális gép** lehetőséget a legördülő menüből.

    ![Számítási feladat kiválasztása a portálon](./media/backup-azure-vms/select-workload.png)
3. Kattintson a lap alján lévő **VÉDELEM** gombra.
    ![Kattintson a Védelem gombra](./media/backup-azure-vms-first-look/protect-icon.png)

    Megjelenik az **Elemek védelme varázsló**, és *csak* a regisztrált és nem védett virtuális gépeket listázza.

    ![Méretezett védelem konfigurálása](./media/backup-azure-vms/protect-at-scale.png)
4. Válassza ki a védeni kívánt virtuális gépeket.

    Ha két vagy több virtuális gépnek ugyanaz a neve, a felhőszolgáltatással különböztesse meg a virtuális gépeket.
5. A **Védelem konfigurálása** menüben válasszon ki egy meglévő házirendet, vagy hozzon létre új házirendet a meghatározott virtuális gépek védelméhez.

    Az új Backup-tárolókhoz alapértelmezett házirend van társítva. Ez a házirend napi pillanatképet készít minden este, és 30 napig megőrzi a napi pillanatképeket. Minden biztonsági mentési házirendhez több virtuális gép társítható. Azonban egy virtuális gép egyszerre csak egy házirendhez társítható.

    ![Védelem új házirenddel](./media/backup-azure-vms/policy-schedule.png)

   > [!NOTE]
   > A biztonsági mentési házirendekben található egy megőrzési séma az ütemezett biztonsági mentésekhez. Ha meglévő biztonsági mentési házirendet választ, nem tudja módosítani a következő lépésben a megőrzési lehetőségeket.
   >
   >
6. A **Megőrzési tartomány** területen határozza meg az adott biztonsági mentési pontok napi, heti, havi és éves hatókörét.

    ![A virtuális gép helyreállítási ponttal van mentve](./media/backup-azure-vms/long-term-retention.png)

    A megőrzési házirend határozza meg a biztonsági másolatok tárolásának hosszát. Különböző megőrzési házirendeket határozhat meg a biztonsági másolat készítésének ideje alapján.
7. Kattintson a **Feladatok** gombra a **Védelemkonfigurálási** feladatok listájának megtekintéséhez.

    ![Védelemkonfigurálási feladat](./media/backup-azure-vms/protect-configureprotection.png)

    Most, hogy létrehozta a házirendet, folytassa a következő lépéssel és futtassa a kezdeti biztonsági mentést.

## <a name="step-5-initial-backup"></a>5. lépés – Kezdeti biztonsági mentés
Miután egy virtuális gépet házirenddel védett, megtekintheti a kapcsolatot a **Védett elemek** lapon. Amíg a kezdeti biztonsági mentés be nem fejeződik, a **Védelem állapota** **Védett – (kezdeti biztonsági mentés függőben)** értékű. Alapértelmezés szerint az első ütemezett biztonsági mentés a *kezdeti biztonsági mentés*.

![Biztonsági mentés függőben](./media/backup-azure-vms-first-look/protection-pending-border.png)

A kezdeti biztonsági mentés indítása most:

1. A **Védett elemek** lap alján kattintson a **Biztonsági mentés** gombra.
    ![Biztonsági mentés ikon](./media/backup-azure-vms-first-look/backup-now-icon.png)

    Az Azure Backup szolgáltatás biztonsági mentési feladatot hoz létre a kezdeti biztonsági mentési művelethez.
2. Kattintson a **Feladatok** fülre a feladatok listájának megtekintéséhez.

    ![Biztonsági mentés folyamatban](./media/backup-azure-vms-first-look/protect-inprogress.png)

    Amikor elkészült a kezdeti biztonsági mentés, a **Védett elemek** lapon a virtuális gép állapota *Védett* értékű.

    ![A virtuális gép helyreállítási ponttal van mentve](./media/backup-azure-vms/protect-backedupvm.png)

   > [!NOTE]
   > A virtuális gépek biztonsági mentése egy helyi folyamat. Nem lehet biztonsági mentést készíteni egy adott régióban található virtuális gépről egy másik régióban található Backup-tárolóra. Így minden olyan Azure-régióban, ahol biztonsági mentésre szoruló virtuális gépek vannak, legalább egy Backup-tárolót létre kell hozni.
   >
   >

## <a name="next-steps"></a>Következő lépések
Most, hogy sikeresen készített biztonsági mentést egy virtuális gépről, számos további lépés végezhető. A leglogikusabb lépés az adatok virtuális gépre végzett visszaállításának megismerése. De olyan felügyeleti tevékenységek is elérhetők, amelyekkel megtudhatja, hogyan védheti meg az adatait, és hogyan csökkentheti minimálisra a költségeket.

* [A virtuális gépek kezelése és figyelése](backup-azure-manage-vms.md)
* [Virtuális gépek visszaállítása](backup-azure-restore-vms.md)
* [Hibaelhárítási útmutató](backup-azure-vms-troubleshoot.md)

## <a name="questions"></a>Kérdései vannak?
Ha kérdései vannak, vagy van olyan szolgáltatás, amelyről hallani szeretne, [küldjön visszajelzést](http://aka.ms/azurebackup_feedback).



<!--HONumber=Nov16_HO2-->


