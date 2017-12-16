---
title: "Klasszikus telepített Azure virtuális gépek biztonsági mentése a mentési tárolóban |} Microsoft Docs"
description: "Ismerje meg, és regisztrálja, készítsen biztonsági másolatot a virtuális gépek ezekkel az eljárásokkal az Azure virtuális gépek biztonsági mentéséhez."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "virtuális gép biztonsági mentése; Készítsen biztonsági másolatot a virtuális gép; biztonsági mentés és katasztrófa utáni helyreállítás; virtuális gép biztonsági mentése"
ms.assetid: c0ab5469-65fd-4af5-ae9b-f5d183f82ce8
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cwatson
ms.openlocfilehash: f2165edf67c1d512342084d6fe80ed6c264ee53a
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="back-up-azure-virtual-machines-classic-portal"></a>Készítsen biztonsági másolatot az Azure virtuális gépek (klasszikus portál)
> [!div class="op_single_selector"]
> * [Recovery Services-tároló virtuális gépek mentésére](backup-azure-arm-vms.md)
> * [Biztonsági másolatot a virtuális gépek mentési tárolóba](backup-azure-vms.md)
>
>

Ez a cikk a eljárásokat biztosít a biztonsági másolatot a klasszikus üzembe az Azure virtuális gép (VM) a biztonsági másolatok tárolóját. Nincsenek néhány feladatot kell gondoskodunk előtt készíthet biztonsági másolatot egy Azure virtuális gépen. Ha még nem tette meg, végezze el a [Előfeltételek](backup-azure-vms-prepare.md) készítse fel a környezetet a virtuális gépek biztonsági mentéséről.

További információkért tekintse meg a cikkek [az Azure virtuális gép biztonsági mentési infrastruktúrájának megtervezésével](backup-azure-vms-introduction.md) és [Azure virtuális gépek](https://azure.microsoft.com/documentation/services/virtual-machines/).

> [!NOTE]
> Az Azure két üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). A biztonsági másolatok tárolóját csak klasszikus telepített virtuális gépek védelmére. A biztonsági másolatok tárolóját erőforrás-kezelő telepített virtuális gépek nem védhetők. Lásd: [készítsen biztonsági mentést a Recovery Services-tároló virtuális gépek](backup-azure-arm-vms.md) talál részletes információt használata a Recovery Services-tárolók.
>
>

Az Azure virtuális gépek biztonsági mentését három fő lépésből áll:

![Készítsen biztonsági másolatot az Azure infrastruktúra-szolgáltatási virtuális gép három lépésben](./media/backup-azure-vms/3-steps-for-backup.png)

> [!NOTE]
> A virtuális gépek biztonsági mentése egy helyi folyamat. Nem készíthet biztonsági másolatot egyetlen virtuális gépeket egy másik régióban egy biztonsági mentési tárolóba. Ezért létre kell hozni egy mentési tárolót minden Azure-régió, ahol a virtuális gép készül biztonsági másolat.
>
> [!IMPORTANT]
> 2017 márciusától már nem hozhat létre Backup-tárolókat a klasszikus portálon.
> A biztonsági mentési tárakról mostantól lehetőség van helyreállítási tárakra váltani. A részletekről bővebben az [Váltás biztonsági mentési tárolóról Recovery Services-tárolóra](backup-azure-upgrade-backup-to-recovery-services.md) című cikkben olvashat. A Microsoft azt javasolja, hogy a biztonsági mentési tárolóról váltson Recovery Services-tárolóra.<br/> Utáni novemberi 30, 2017 csak akkor tud biztonsági mentési tárolók létrehozása a PowerShell használatával. **2017. November 30 által**:
>- Minden fennmaradó Backup-tároló automatikusan Recovery Services-tárolóra frissül.
>- A klasszikus portálon nem lehet majd hozzáférni a biztonsági másolati adatokhoz. Helyette az Azure Portal segítségével férhet hozzá a Recovery Services-tárolókban található biztonsági mentési adatokhoz.
>

## <a name="step-1---discover-azure-virtual-machines"></a>1. lépés – az Azure virtuális gépek észlelése
Győződjön meg arról, mielőtt regisztrálná azonosított bármely új virtuális gépek (VM) az előfizetéshez történő hozzáadása, futtassa a felderítési folyamat. A folyamat lekéri az Azure-ból az előfizetésben található virtuális gépek listáját, olyan kiegészítő információkkal, mint a felhőszolgáltatás neve és a régió.

1. Jelentkezzen be a [klasszikus portál](http://manage.windowsazure.com/)
2. Azure-szolgáltatáshoz, kattintson a **Recovery Services** tárolók biztonsági mentés és helyreállítás listájának megnyitásához.
    ![Nyissa meg a tároló listája](./media/backup-azure-vms/choose-vault-list.png)
3. A mentési tárolókban, jelölje ki a tároló biztonsági mentéséhez egy virtuális Gépet.

    Ha ez egy új tárolót a portál megnyitja a **gyors üzembe helyezés** lap.

    ![Regisztrált elemek menü megnyitása](./media/backup-azure-vms/vault-quick-start.png)

    Ha a tároló már be lett állítva, a portál megnyitja a legutóbb használt menübe.
4. A tároló menüben (a lap tetején), kattintson az **regisztrált elemek**.

    ![Regisztrált elemek menü megnyitása](./media/backup-azure-vms/vault-menu.png)
5. A **Típus** menüből válassza az **Azure virtuális gép** elemet.

    ![Számítási feladat kiválasztása](./media/backup-azure-vms/discovery-select-workload.png)
6. Kattintson a lap alján található **FELDERÍTÉS** gombra.
    ![Felderítés gomb](./media/backup-azure-vms/discover-button-only.png)

    A felderítési folyamat eltarthat pár percig, amíg a virtuális gépeket táblázatba rendezi a rendszer. A képernyő alján lévő értesítés jelzi, hogy a folyamat fut.

    ![Virtuális gépek felderítése](./media/backup-azure-vms/discovering-vms.png)

    Az értesítés módosul, amikor a folyamat befejeződött. Ha a felderítési folyamat nem találta meg a virtuális gépek, először biztosítson a virtuális gépeket. A virtuális gépek esetén ellenőrizze a virtuális gépek és a biztonsági mentési tárolónak ugyanabban a régióban. Ha a virtuális gépek létezik, és ugyanabban a régióban van, győződjön meg arról, a virtuális gépek nem már regisztrálva van a mentési tárolóban. Ha egy virtuális Géphez van rendelve egy mentési tárolót, hozzá kell rendelni a többi biztonsági mentési tárolók nem érhető el.

    ![A felderítés kész](./media/backup-azure-vms/discovery-complete.png)

    A felfedezett az új elemek után folytassa a 2, és regisztrálja a virtuális gépek.

## <a name="step-2---register-azure-virtual-machines"></a>2. lépés – regisztrálása az Azure virtuális gépek
Rögzítheti az Azure Backup szolgáltatás társítsa Azure virtuális géphez. Ez általában az egyszeri tevékenység.

1. Lépjen a mentési tároló alatt **Recovery Services** az Azure-portálon, és kattintson a **regisztrált elemek**.
2. Válassza az **Azure virtuális gép** lehetőséget a legördülő menüből.

    ![Számítási feladat kiválasztása](./media/backup-azure-vms/discovery-select-workload.png)
3. Kattintson a lap alján lévő **REGISZTRÁLÁS** gombra.
    ![Regisztrálás gomb](./media/backup-azure-vms/register-button-only.png)
4. Az **Elemek regisztrálása** helyi menüben válassza ki azokat a virtuális gépeket, amelyeket regisztrálni szeretne. Ha két vagy több virtuális gépek ugyanazzal a névvel, a felhőalapú szolgáltatás segítségével megkülönböztetését.

   > [!TIP]
   > Egyszerre több virtuális gép is regisztrálható.
   >
   >

    Létrejön egy feladat minden egyes kiválasztott virtuális géphez.
5. Kattintson a **Feladat megtekintése** gombra az értesítésben a **Feladatok** lap megnyitásához.

    ![Regisztrációs feladat](./media/backup-azure-vms/register-create-job.png)

    A virtuális gép a regisztrált elemek listájában is megjelenik a regisztrációs művelet állapotával együtt.

    ![1. regisztrációs állapot](./media/backup-azure-vms/register-status01.png)

    A művelet befejezése után az állapot módosul, hogy a *regisztrált* állapotot jelezze.

    ![2. regisztrációs állapot](./media/backup-azure-vms/register-status02.png)

## <a name="step-3---protect-azure-virtual-machines"></a>3. lépés - az Azure virtuális gépek védelme
Most a virtuális gép egy biztonsági mentési és adatmegőrzési házirend állíthat be. Több virtuális gépek védhetők egyetlen művelet védelme.

A tároló beépített alapértelmezett házirend létrehozása után a 2015. május kapható Azure mentési tárolókban. Ez az alapértelmezett házirend tartalmaz egy alapértelmezett megőrzési 30 nap és a biztonsági mentési ütemezés szerint naponta egyszer.

1. Lépjen a mentési tároló alatt **Recovery Services** az Azure-portálon, és kattintson a **regisztrált elemek**.
2. Válassza az **Azure virtuális gép** lehetőséget a legördülő menüből.

    ![Számítási feladat kiválasztása a portálon](./media/backup-azure-vms/select-workload.png)
3. Kattintson a lap alján lévő **VÉDELEM** gombra.

    A **elemek védelme varázsló** jelenik meg. A varázsló csak a regisztrált és nem védett virtuális gépek sorolja fel. Válassza ki a védeni kívánt virtuális gépeket.

    Ha két vagy több virtuális gépek ugyanazzal a névvel, a felhőalapú szolgáltatás segítségével különböztetheti meg egymástól a virtuális gépek között.

   > [!TIP]
   > Egyszerre több virtuális gép védhet.
   >
   >

    ![Méretezett védelem konfigurálása](./media/backup-azure-vms/protect-at-scale.png)

4. Válasszon egy **biztonsági mentés ütemezése** biztonsági másolatot készíteni a kijelölt virtuális gépek számára. Válasszon egy meglévő házirendcsoport címet, vagy új megadása.

    Minden biztonsági mentési házirendhez több virtuális gép társítható. Azonban a virtuális gép csak társítható egy házirend álljon időben.

    ![Védelem új házirenddel](./media/backup-azure-vms/policy-schedule.png)

   > [!NOTE]
   > A biztonsági mentési házirendekben található egy megőrzési séma az ütemezett biztonsági mentésekhez. Ha egy meglévő biztonsági mentési házirend, nem módosítható a megőrzési lehetőségek a következő lépésben.
   >
   >

5. Válasszon egy **megőrzési időtartam** rendelje hozzá a biztonsági mentés.

    ![Rugalmas megőrzést védelme](./media/backup-azure-vms/policy-retention.png)

    A megőrzési házirend határozza meg a biztonsági másolatok tárolásának hosszát. Különböző megőrzési házirendeket határozhat meg a biztonsági másolat készítésének ideje alapján. Például egy biztonsági mentési pont végrehajtott naponta (ami működési helyreállítási pontjaként szolgál) lehet, hogy megőrzi a 90 napig. Összehasonlításképpen egy biztonsági mentési pont (naplózási célokra) minden negyedév végén kell sok hónap vagy év megőrzi.

    ![A virtuális gép helyreállítási ponttal van mentve](./media/backup-azure-vms/long-term-retention.png)

    A példa képen:

   * **Napi adatmegőrzési**: naponta készített biztonsági másolatok 30 napig tárolja.
   * **Heti adatmegőrzési**: 104 hétig minden héten vasárnap készített biztonsági másolatok megmaradnak.
   * **Havi adatmegőrzési**: 120 hónapig minden hónap utolsó vasárnap biztonsági másolatokat megmaradnak.
   * **Éves adatmegőrzési**: minden január első vasárnap biztonsági másolatokat 99 évig megmaradnak.

     Egy feladat jön létre, konfigurálja a védelmi házirendet, és rendelje hozzá a virtuális gépek minden egyes virtuális gép újraszinkronizálását választotta, az adott házirendnek.
6. A listájának megtekintéséhez **Védelemkonfigurálási** feladatok, a tárolók menüben kattintson a **feladatok** válassza ki **Védelemkonfigurálási** a a **művelet**szűrő.

    ![Védelemkonfigurálási feladat](./media/backup-azure-vms/protect-configureprotection.png)

## <a name="initial-backup"></a>Kezdeti biztonsági mentés
Után a virtuális gép védett szabályzatnak, akkor megjelenik a a **védett elemek** lapon a állapotú *védett - (függőben lévő kezdeti biztonsági másolatot)*. Alapértelmezés szerint az első ütemezett biztonsági mentés a *kezdeti biztonsági mentés*.

A kezdeti biztonsági mentés elindítása után azonnal védelmének konfigurálása:

1. Alján a **védett elemek** kattintson **biztonsági mentés most**.

    Az Azure Backup szolgáltatás biztonsági mentési feladatot hoz létre a kezdeti biztonsági mentési művelethez.
2. Kattintson a **Feladatok** fülre a feladatok listájának megtekintéséhez.

    ![Biztonsági mentés folyamatban](./media/backup-azure-vms/protect-inprogress.png)

> [!NOTE]
> A biztonsági mentési művelet során az Azure Backup szolgáltatás minden egyes virtuális gépen ürítse ki az összes írási feladat, és egységes pillanatképet készít a biztonsági mentési bővítmény parancsot ad ki.
>
>

Ha a kezdeti biztonsági mentés befejezése után a virtuális gép állapotát a **védett elemek** lap *védett*.

![A virtuális gép helyreállítási ponttal van mentve](./media/backup-azure-vms/protect-backedupvm.png)

## <a name="viewing-backup-status-and-details"></a>Biztonsági mentés állapotának és a részletek megtekintése
Védett, miután a virtuális gépek számát is növekszik a **irányítópult** összefoglaló lap. A **irányítópult** lapon is látható az elmúlt 24 órában, melyeket a feladatok száma *sikeres*, rendelkezik *sikertelen*, és a *folyamatban* . A a **feladatok** oldalon a **állapot**, **művelet**, vagy **a** és **való** menü használatával szűrje a feladatokat.

![Biztonsági mentés a irányítópult-oldalon állapota](./media/backup-azure-vms/dashboard-protectedvms.png)

Az irányítópult értékeket 24 óránként egyszer frissülnek.

## <a name="troubleshooting-errors"></a>Kapcsolatos hibák elhárítása
Ha biztonsági során problémákat tapasztal a virtuális gép, tekintse meg a [VM hibaelhárítási cikke](backup-azure-vms-troubleshoot.md) segítségét.

## <a name="next-steps"></a>Következő lépések
* [A virtuális gépek kezelése és figyelése](backup-azure-manage-vms.md)
* [Virtuális gépek visszaállítása](backup-azure-restore-vms.md)
