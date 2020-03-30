---
title: Az Azure-beli virtuális gépek replikációjának konfigurálása az Azure Site Recovery szolgáltatásban
description: Ismerje meg, hogyan konfigurálhatja a replikációt egy másik régióba az Azure virtuális gépek, a Site Recovery használatával.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2018
ms.openlocfilehash: 1c6b7cfbf193f02598052b6922efec17fb16ec83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973695"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Azure-beli virtuális gépek replikálása egy másik Azure-régióba


Ez a cikk ismerteti, hogyan engedélyezheti az Azure virtuális gépek replikációját az egyik Azure-régióból a másikba.

## <a name="before-you-start"></a>Előkészületek

Ez a cikk feltételezi, hogy az [Azure-azure-beli vész-helyreállítási oktatóanyagban](azure-to-azure-tutorial-enable-replication.md)leírtak szerint előkészítette a Site Recovery üzembe helyezését.

Előfeltételek kell a helyén, és létre kellett volna hoznia egy Recovery Services-tároló.


## <a name="enable-replication"></a>A replikáció engedélyezése

Engedélyezze a replikációt. Ez az eljárás feltételezi, hogy az elsődleges Azure-régió Kelet-Ázsia, a másodlagos régió pedig Délkelet-Ázsia.

1. A tárolóban kattintson a **+Replikálás gombra.**
2. Vegye figyelembe a következő mezőket:
   - **Forrás**: A virtuális gépek származási helye, amely ebben az esetben az **Azure.**
   - **Forrás helye:** Az Azure-régió, ahonnan védeni szeretné a virtuális gépeket. Ehhez az illusztrációhoz a forrás helye "Kelet-Ázsia"
   - **Üzembe helyezési modell:** A forrásgépek Azure üzembe helyezési modellje.
   - **Forrás-előfizetés:** Az az előfizetés, amelyhez a forrás virtuális gépek tartoznak. Ez bármelyik előfizetés lehet azon Azure Active Directory-bérlőn belül, ahol a már meglévő Recovery Services-tárolója van.
   - **Erőforráscsoport:** Az az erőforráscsoport, amelyhez a forrásvirtuális gépek tartoznak. A kijelölt erőforráscsoport ban lévő összes virtuális gép a következő lépésben védelemre van felsorolva.

     ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. A **Virtuális gépek > Válassza ki**a virtuális gépeket, kattintson és jelölje ki a replikálni kívánt virtuális gépeket. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Ezt követően kattintson az **OK** gombra.
    ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. A **Beállítások párbeszédpanelen**tetszés szerint megadhatja a célhely beállításait:

   - **Célhely:** Az a hely, ahol a forrásvirtuális gép adatai replikálódnak. A kiválasztott gépek helyétől függően a Site Recovery megadja a megfelelő célrégiók listáját. Azt javasoljuk, hogy tartsa a célhelyet ugyanaz, mint a Recovery Services tároló helyét.
   - **Célelőfizetés**: A vészhelyreállításhoz használt célelőfizetés. Alapértelmezés szerint a célelőfizetés megegyezik a forrás-előfizetéssel.
   - **Célerőforrás-csoport:** Az az erőforráscsoport, amelyhez az összes replikált virtuális gép tartozik.
       - Alapértelmezés szerint a Site Recovery új erőforráscsoportot hoz létre a célrégióban egy "asr" utótaggal a nevében.
       - Ha a Site Recovery által létrehozott erőforráscsoport már létezik, a program újra felhasználja.
       - Az erőforráscsoport beállításai testreszabhatók.
       - A célerőforrás-csoport helye bármely Azure-régió lehet, kivéve azt a régiót, amelyben a forrás virtuális gépek vannak tárolva.
   - **Virtuális hálózat célzása**: Alapértelmezés szerint a Site Recovery új virtuális hálózatot hoz létre a célrégióban, amelynek neve "asr" utótag. Ez le van képezve a forráshálózatra, és bármilyen jövőbeli védelemhez használható. [További információ](site-recovery-network-mapping-azure-to-azure.md) a hálózati hozzárendelésről.
   - **Céltárfiókok (a forrásvirtuális gép nem használ felügyelt lemezeket)**: Alapértelmezés szerint a Site Recovery új céltárfiókot hoz létre, amely a forrás virtuálisgép-tároló konfigurációját utánozza. Abban az esetben, ha a tárfiók már létezik, újra felhasználja.
   - **Replika által kezelt lemezek (a forrás virtuális gép felügyelt lemezeket használ)**: A Site Recovery új replika által felügyelt lemezeket hoz létre a célrégióban, hogy tükrözze a forrás virtuális gép felügyelt lemezeit, amelyek ugyanolyan tárolótípusúak (Standard vagy prémium szintűek), mint a forrás virtuális gép felügyelt lemezei.
   - **Gyorsítótár-tároló fiókok:** Site Recovery szüksége van extra tárfiók nevű gyorsítótár-tároló a forrásrégióban. A forrásvirtuális gépeken végrehajtott összes módosítást nyomon követi a rendszer, és elküldi a gyorsítótár-tárfiókba, mielőtt replikálni a dedikálást a célhelyre. Ez a tárfiók legyen standard.
   - **Cél rendelkezésre állási csoportok:** Alapértelmezés szerint a Site Recovery létrehoz egy új rendelkezésre állási készlet a célrégióban az "asr" utótag a névben, a virtuális gépek, amelyek részét képezik a forrásrégióban rendelkezésre állási csoport. Ha a Site Recovery által létrehozott rendelkezésre állási készlet már létezik, a program újra felhasználja.
   - **Célelérhetőségi zónák:** Alapértelmezés szerint a Site Recovery ugyanazt a zónaszámot rendeli hozzá, mint a célrégió forrásrégiója, ha a célrégió támogatja a rendelkezésre állási zónákat.

     Ha a célrégió nem támogatja a rendelkezésre állási zónákat, a célvirtuális gépek alapértelmezés szerint egyetlen példányként vannak konfigurálva. Ha szükséges, konfigurálhatja az ilyen virtuális gépek, hogy része a rendelkezésre állási csoportok a célrégióban a "Testreszabás" gombra kattintva.

     >[!NOTE]
     >A replikáció engedélyezése után nem módosíthatja a rendelkezésre állás típusát – egyetlen példányt, rendelkezésre állási készletet vagy rendelkezésre állási zónát. A rendelkezésre állástípus módosításához le kell tiltania és engedélyeznie kell a replikációt.
     >

   - **Replikációs házirend**: Meghatározza a helyreállítási pontok megőrzésének előzményeit és az alkalmazás konzisztens pillanatkép-gyakoriságát. Alapértelmezés szerint az Azure Site Recovery új replikációs házirendet hoz létre, amelynek alapértelmezett beállításai "24 óra" a helyreállítási pontok megőrzésére és "4 óra" az alkalmazás konzisztens pillanatfelvétel gyakoriságára.

     ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>A hozzáadott lemezek replikációjának engedélyezése

Ha lemezeket ad hozzá egy olyan Azure-beli virtuális géphez, amelyen engedélyezve van a replikáció, a következő történik:
-   A virtuális gép replikációs állapota figyelmeztetést jelenít meg, és egy megjegyzés tájékoztatja, hogy egy vagy több lemez védelemre rendelkezésre áll.
-   Ha engedélyezi a hozzáadott lemezek védelmét, a figyelmeztetés a lemez kezdeti replikációja után eltűnik.
-   Ha úgy dönt, hogy nem engedélyezi a lemez replikációját, a figyelmeztetés elvetése is kiválasztható.


    ![Új lemez hozzáadva](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Ha engedélyezni szeretné egy hozzáadott lemez replikációját, tegye a következőket:

1.  A tárolóban > **replikált elemek,** kattintson a virtuális gép, amelyhez hozzáadta a lemezt.
2.  Kattintson a **Lemezek gombra,** majd jelölje ki azt az adatlemezt, amelynek replikációját engedélyezni szeretné (ezek a lemezek **Nem védett állapotúak).**
3.  A **Lemez részletei csoportban**kattintson a **Replikáció engedélyezése gombra.**

    ![Replikáció engedélyezése a hozzáadott lemezhez](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

A replikációs feladat lehetővé tétele és a kezdeti replikáció befejezése után a lemezprobléma replikációs állapotra vonatkozó figyelmeztetése törlődik.



## <a name="customize-target-resources"></a>Célerőforrások testreszabása

Módosíthatja a Site Recovery által használt alapértelmezett célbeállításokat.

1. Kattintson **a Testreszabás:** a "Cél előfizetés" elem mellett az alapértelmezett cél-előfizetés módosításához. Válassza ki az előfizetést az ugyanabban az Azure Active Directory (AAD) bérlőben elérhető összes előfizetés listájából.

2. Az alapértelmezett beállítások módosításához kattintson a **Testreszabás:** gombra:
    - A **Cél erőforráscsoportban**válassza ki az erőforráscsoportot az előfizetés célhelyén lévő összes erőforráscsoport listájából.
    - A **Cél virtuális hálózat**, válassza ki a hálózatot a célhelyen lévő összes virtuális hálózat listájából.
    - Az **elérhetőségi készletben**hozzáadhatja a rendelkezésre állási beállításokat a virtuális géphez, ha azok a forrásrégióban lévő rendelkezésre állási csoport részét képezik.
    - A **Céltár-fiókok ban**válassza ki a használni kívánt fiókot.

        ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Kattintson **a Testreszabás:** gombra a replikációs beállítások módosításához.
4. A **Többvirtuális gép konzisztenciájában**válassza ki a replikálni kívánt virtuális gépeket.
    - A feladatátvételkor a replikációs csoportba tartozó összes gép megosztott összeomlás-konzisztens és alkalmazáskonzisztens helyreállítási pontokkal rendelkezik majd.
    - A több virtuális gép konzisztenciájának engedélyezése hatással lehet a számítási feladatok teljesítményére (mivel cpu-igényes). Csak akkor engedélyezve kell lennie, ha a gépek ugyanazt a számítási feladatot futtatják, és több gép közötti konzisztenciára van szükség.
    - Ha például egy alkalmazás 2 SQL Server virtuális géppel és két webkiszolgálóval rendelkezik, akkor csak az SQL Server virtuális gépeket kell hozzáadnia egy replikációs csoporthoz.
    - Választhat, hogy legfeljebb 16 virtuális gép van egy replikációs csoportban.
    - Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással.
    - Győződjön meg arról, hogy nincs olyan tűzfalberendezés, amely blokkolja a virtuális gépek közötti belső kommunikációt a 20004-es porton keresztül.
    - Ha azt szeretné, hogy a Linux virtuális gépek egy replikációs csoport részei legyenek, győződjön meg arról, hogy a 20004-es portkimenő forgalma manuálisan van megnyitva az adott Linux-verzióra vonatkozó útmutatásnak megfelelően.
![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)

5. Kattintson **a Célerőforrás** > **létrehozása replikáció engedélyezése gombra.**
6. Miután a virtuális gépek engedélyezve vannak a replikációhoz, ellenőrizheti a virtuális gép állapotának állapotát a **Replikált elemek alatt**

>[!NOTE]
>A kezdeti replikáció során az állapot frissítése némi időt vehet igénybe, folyamat nélkül. Kattintson a **Frissítés** gombra, hogy a legújabb állapot.
>

## <a name="next-steps"></a>További lépések

[További információ](site-recovery-test-failover-to-azure.md) a tesztfeladat-átvétel futtatásáról.
