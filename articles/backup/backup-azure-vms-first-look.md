---
title: "Áttekintés: Azure virtuális gépek biztonsági mentése Backup-tárolóval | Microsoft Docs"
description: "A klasszikus portállal biztonsági másolatot készíthet az Azure virtuális gépekről egy Backup-tárolóba. Ez az oktatóanyag leírja a munkafolyamat összes fázisát, beleértve a Backup-tároló létrehozását, a virtuális gépek regisztrálását, a biztonsági mentési szabályzat létrehozását és a kezdeti biztonsági mentési feladat futtatását."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 722820dc-b65f-425c-a9e5-c1946e896a87
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/14/2017
ms.author: markgal;
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 61328e32763faea90074fc6d499e660c4109ab6d
ms.contentlocale: hu-hu
ms.lasthandoff: 06/16/2017


---
# <a name="first-look-backing-up-azure-virtual-machines"></a>Áttekintés: Azure virtuális gépek biztonsági mentése
> [!div class="op_single_selector"]
> * [Virtuális gépek védelme Recovery Services-tárolóval](backup-azure-vms-first-look-arm.md)
> * [Azure virtuális gépek védelme Backup-tárolóval](backup-azure-vms-first-look.md)
>
>

Ez az oktatóanyag végigvezeti egy Azure virtuális gép (VM) egy Azure-beli Backup-tárolóba való biztonsági mentésének lépésein. Ez a cikk a virtuális gépek biztonsági mentésének klasszikus modelljét vagy Service Managerrel üzemi modelljét ismerteti. A következő lépések csak a klasszikus portálon létrehozott Backup-tárolókra érvényesek. A Microsoft azt javasolja, hogy az új üzemelő példányokhoz a Resource Manager modellt használja.

Ha többet szeretne tudni a virtuális gépek egy erőforráscsoporthoz tartozó Recovery Services-tárolóba való biztonsági mentéséről, olvassa el az [Áttekintés: Virtuális gépek védelme Recovery Services-tárolóval](backup-azure-vms-first-look-arm.md) című cikket.

A következő oktatóanyag sikeres elvégzéséhez a következő előfeltételek szükségesek:

* Létrehozott egy virtuális gépet az Azure-előfizetésben.
* A virtuális gép csatlakozik Azure nyilvános IP-címekhez. További információkért lásd: [Hálózati kapcsolatok](backup-azure-vms-prepare.md#network-connectivity).


> [!NOTE]
> Az Azure két üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez az oktatóanyag olyan virtuális gépekhez készült, amelyeket a klasszikus portálon hoztak létre.
>
>

## <a name="create-a-backup-vault"></a>Backup-tároló létrehozása
A Backup-tároló egy olyan entitás, amely tárolja az idők során létrehozott biztonsági mentéseket és helyreállítási pontokat. A Backup-tároló azokat a biztonsági mentési házirendeket is tartalmazza, amelyek a biztonsági mentés alatt álló virtuális gépekre érvényesek.

> [!IMPORTANT]
> 2017 márciusától már nem hozhat létre Backup-tárolókat a klasszikus portálon.
> A biztonsági mentési tárakról mostantól lehetőség van helyreállítási tárakra váltani. A részletekről bővebben az [Upgrade a Backup vault to a Recovery Services vault](backup-azure-upgrade-backup-to-recovery-services.md) (Váltás biztonsági mentési tárról helyreállítási tárra) című cikkben olvashat. A Microsoft arra biztat, hogy a biztonsági mentési tárról váltson helyreállítási tárra.<br/> **2017. november 1-től kezdődően**:
>- A rendszer automatikusan elvégzi valamennyi megmaradó biztonsági mentési tár helyreállítási tárra való frissítését.
>- A klasszikus portálon nem lehet majd hozzáférni a biztonsági másolati adatokhoz. Helyette az Azure Portal segítségével férhet hozzá a helyreállítási tárakban található biztonsági másolati adatokhoz.
>

## <a name="discover-and-register-azure-virtual-machines"></a>Az Azure virtuális gépek felderítése és regisztrálása
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

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Telepítse a virtuális gép ügynökét a virtuális gépre.
Az Azure virtuálisgép-ügynököt telepíteni kell az Azure virtuális gépre, hogy a Backup bővítmény működjön. Ha a virtuális gépe az Azure-katalógusból lett létrehozva, a virtuálisgép-ügynök már megtalálható a virtuális gépen, így továbbléphet [a virtuális gépek védelmére](backup-azure-vms-first-look.md#create-the-backup-policy).

Ha a virtuális gépe helyszíni adatközpontból lett áttelepítve, a virtuális gépre valószínűleg nincs telepítve a virtuálisgép-ügynök. Telepítenie kell a virtuálisgép-ügynököt a virtuális gépen a virtuális gép védelme előtt. A virtuálisgép-ügynök telepítésének részletes lépéseiért lásd [a virtuális gépek biztonsági mentéséről szóló cikk virtuálisgép-ügynökökre vonatkozó szakaszát](backup-azure-vms-prepare.md#vm-agent).

## <a name="create-the-backup-policy"></a>A biztonsági mentési házirend létrehozása
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

## <a name="initial-backup"></a>Kezdeti biztonsági mentés
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

