---
title: "Azure virtuális gépek replikálása Azure-régiók közötti, a vész helyreállítási igényeket: Azure az Azure-bA |} Microsoft Docs"
description: "Azure virtuális gépek replikálása Azure-régiók (Azure-Azure) az Azure Site Recovery szolgáltatásban, a vész-helyreállítási igényekre lépéseket foglalja össze."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: raynew
ms.openlocfilehash: 9ca33057f6030fdcc233f6053fdc392d62f8f9f4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>Azure virtuális gépek replikálása az Azure Site Recovery régiók között

>[!NOTE]
>
> Az Azure virtuális gépek (VM) az Azure Site Recovery replikációs jelenleg előzetes verzió.

Ez a cikk ismerteti, hogyan Azure virtuális gépek replikálása Azure-régiók használatával a [Site Recovery](site-recovery-overview.md) szolgáltatás az Azure portálon.

Ez a cikk vagy a alsó megjegyzések és kérdések utáni a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="disaster-recovery-in-azure"></a>Az Azure-ban katasztrófa utáni helyreállítás

Beépített Azure infrastruktúra-szolgáltatásait és funkcióit hozzájárul az Azure virtuális gépeken futó számítási feladatokat egy hatékony és rugalmas rendelkezésre állási stratégiát. Vannak azonban miért meg kell terveznie az Azure-régiók közötti vész-helyreállítási saját kezűleg számos oka:

- Bizonyos alkalmazások és munkafolyamatok, amelyek üzleti folytonossági és vészhelyreállítási (BCDR) helyreállítási stratégia kérése megfelelőségi irányelveinek teljesítéséhez szüksége.
- Azt szeretnénk, ha védelme és helyreállítása Azure virtuális gépeken, az üzleti döntések alapján, és nem csak a beépített Azure funkciókon alapulnak.
- Feladatátvételét és helyreállítását az üzleti és megfelelőségi igényeinek érintő forgalomkiesés nélkül éles megfelelően tesztelni kell.
- Feladatok átadása a helyreállítási régió katasztrófa esetén, és zökkenőmentesen visszaadják feladataikat az eredeti forrás régióban kell.

Az Azure-Azure virtuális gép replikációs Site Recovery segítségével ezeket a feladatokat.


## <a name="why-use-site-recovery"></a>Miért előnyös a Site Recovery használata?      

A Site Recovery Azure virtuális gépek replikálása régiók közötti egyszerű módszert kínál:

- **Automatikus központi telepítési**. Az aktív-aktív replikációs modell eltérően nincs szükség egy összetett és költséges infrastruktúra másodlagos régióban. Ha engedélyezi a replikációt, Site Recovery automatikusan létrehozza a szükséges erőforrások a cél régióban forrás régió beállításai alapján.
- **Szabályozza a régiók**. A Site Recovery segítségével replikálhatja bármely régióban a kontinensen belül bármely régióban. Hasonlítsa ezt össze írásvédett georedundáns tárolás, amelyek szabványos közötti aszinkron módon replikál [régiók párosítva](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) csak. Írásvédett georedundáns tárolás az adatokat a cél régióban olvasási hozzáférést biztosít.
- **Replikációs automatikus**. A Site Recovery automatizált folyamatos replikálásra biztosít. A feladatátvételi és a feladat-visszavétel egyetlen kattintással is elindítható.
- **RTO és a helyreállítási Időkorlát**. A Site Recovery kihasználja az Azure hálózati infrastruktúra, amely a régiók RTO és a helyreállítási Időkorlát nagyon alacsony tartani.
- **Tesztelési**. Az igény szerinti feladatátvételi teszteket, szükséges, hogy az nem befolyásolja a termelési számítási feladatokhoz vagy a folyamatban lévő replikáció vész-helyreállítási gyakorlatokat is futtathatja.
- **A helyreállítási terv**. A helyreállítási tervek segítségével levezényelni a feladatátvétel és a teljes alkalmazás több virtuális gépeken futó feladat-visszavétel. A helyreállítási terv funkció Azure automation-forgatókönyveket gazdag első osztályú integrálva van.


## <a name="deployment-summary"></a>Központi telepítési összefoglaló

Itt található egy Összegzés kell tennie a virtuális gépek Azure-régiók közötti replikáció beállításához:

1. Recovery Services-tároló létrehozása. A tároló-konfigurációs beállításait tartalmazza, és koordinálja a replikációt.
2. Engedélyezze az Azure virtuális gépek replikálását.
3. Győződjön meg arról, hogy minden a várt módon működik feladatátvételi teszt futtatása.

>[!IMPORTANT]
>
> Ellenőrizheti a [támogatási mátrixot az Azure virtuális gép replikációs](./site-recovery-support-matrix-azure-to-azure.md).

>[!IMPORTANT]
>
> A szükséges hálózati kimenő kapcsolat konfigurálása Azure virtuális gépek esetén a Site Recovery replikáció információkért lásd: a [hálózati útmutató](./site-recovery-azure-to-azure-networking-guidance.md).

### <a name="before-you-start"></a>Előkészületek

* Egyes rendelkeznie kell Azure felhasználói fiókja [engedélyek](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) egy Azure virtuális gép replikációjának engedélyezéséhez.
* Az Azure-előfizetéshez engedélyezni kell a célként megadott helyen, a vész-helyreállítási régió használni kívánt virtuális gépek létrehozásához. A szükséges kvóta engedélyezéséhez forduljon a támogatási szolgálathoz.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> Azt javasoljuk, hogy a helyen, ahol azt szeretné, hogy a virtuális gépek replikálásához hozzon létre a Recovery Services-tároló. Például, ha a cél elérési útja a központi USA, hozzon létre a tárolót, a **USA középső RÉGIÓJA**.

## <a name="enable-replication"></a>A replikáció engedélyezése

A **Recovery Services-tárolók**, kattintson a tároló nevére. A tárolóban, kattintson a **+ replikálás** gombra.

### <a name="step-1-configure-the-source"></a>1. lépés Konfigurálja a forrás
1. A **forrás**, jelölje be **Azure - előzetes**.
2. A **adatforrásról**, válasszon ki forrást az Azure-régió, ahol a virtuális gépek futnak.
3. A virtuális gépek telepítési modell kiválasztása: **erőforrás-kezelő** vagy **klasszikus**.
4. Válassza ki a **forrás-erőforráscsoporton** erőforrás-kezelő virtuális gépek vagy **felhőalapú szolgáltatás** klasszikus virtuális gépekhez.

    ![Konfigurálja a forrás](./media/site-recovery-azure-to-azure/source.png)

### <a name="step-2-select-virtual-machines"></a>2. lépés Válassza ki a virtuális gépek

* Válassza ki a virtuális gépek replikálása, és kattintson a kívánt **OK**.

    ![Válassza ki a virtuális gépek](./media/site-recovery-azure-to-azure/vms.png)

### <a name="step-3-configure-settings"></a>3. lépés Beállítások konfigurálása

1. Felülírják az alapértelmezett cél beállításokat, és adja meg a beállításokat az Ön által választott, kattintson a **Testreszabás**. További információkért lásd: [célerőforrások testreszabása](site-recovery-replicate-azure-to-azure.md##customize-target-resources).

    ![Beállítások konfigurálása](./media/site-recovery-azure-to-azure/settings.png)


2. Alapértelmezés szerint a Site Recovery veszi alkalmazáskonzisztens pillanatképek 4 óránként, és megtartja a helyreállítási pont 24 órán át replikációs házirendet hoz létre. Különböző beállításokkal házirend létrehozásához kattintson a **Testreszabás** melletti **replikációs házirend**.

    ![Házirend testreszabása](./media/site-recovery-azure-to-azure/customize-policy.png)

3. A tároló erőforrásait kiépítés elindításához kattintson **célerőforrások létrehozása**. Kiépítés egy percet vesz igénybe. Ne zárja be a panelt kiépítése során, vagy kezdje újra a folyamatot kell.

4. A kijelölt virtuális gép replikálását indításához kattintson **engedélyezze a replikálást**.

5. Előrehaladásának nyomon követheti a **engedélyezni a védelmet** feladat **beállítások** > **feladatok** > **Site Recovery-feladatok**.

6. A **beállítások** > **replikált elemek**, megtekintheti az állapotát, a virtuális gépek és a kezdeti replikáció folyamatban van. Kattintson a virtuális gépek a részletekbe menően tárhatják fel annak beállításait.

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Miután beállította mindent, győződjön meg arról, hogy minden a várt módon működik a feladatátvételi teszt futtatása:

1. Az egyetlen számítógépen, feladatátvételt **beállítások** > **replikált elemek**, kattintson a virtuális gép **+ feladatátvételi teszt** ikonra.

2. A helyreállítási tervben feladatátvételt a **beállítások** > **helyreállítási tervek**, kattintson a jobb gombbal a terv **feladatátvételi teszt**. Helyreállítási terv létrehozásához [kövesse ezeket az utasításokat](site-recovery-create-recovery-plans.md). 

3. A **feladatátvételi teszt**, jelölje ki a cél Azure-beli virtuális hálózat, amely az Azure virtuális gépek a feladatátvételt követően csatlakoznak.

4. A feladatátvételi elindításához kattintson **OK**. Nyomon követni, kattintson a virtuális gép tulajdonságainak megnyitásához. Vagy kattintson a **feladatátvételi teszt** feladat a tároló neve > **beállítások** > **feladatok** > **Site Recovery-feladatok**.

5. A feladatátvétel befejezése után a replika Azure gép megjelenik-e az Azure portálon > **virtuális gépek**. Győződjön meg arról, hogy a virtuális gép a megfelelő, csatlakozik-e a megfelelő hálózati méretét, és fut-e.

6. A virtuális gép feladatátvételi tesztje során létrehozott törléséhez kattintson **karbantartása a feladatátvételi teszt** a replikált cikk vagy a helyreállítási terv. A **megjegyzések**, és a feladatátvételi teszttel kapcsolatos megfigyelések feljegyzéséhez mentéséhez. 

[További](site-recovery-test-failover-to-azure.md) vonatkozó feladatátvételi tesztet.


## <a name="next-steps"></a>Következő lépések

Miután a telepítés tesztelésére:

- [További információk](site-recovery-failover.md) a feladatátvételek különféle típusaival és a futtatásukkal kapcsolatban.
- További információ [helyreállítási tervek segítségével](site-recovery-create-recovery-plans.md) RTO csökkentése érdekében.
