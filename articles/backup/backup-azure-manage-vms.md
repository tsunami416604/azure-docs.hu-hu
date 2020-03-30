---
title: Azure virtuális gépek biztonsági másolatainak kezelése és figyelése
description: Ismerje meg, hogyan kezelheti és figyelheti az Azure virtuális gépek biztonsági mentését az Azure Backup szolgáltatás használatával.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: dd4e9dc199048b3faf3da0cadfdf60bdcb26c5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248150"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>Azure Virtuálisgép-biztonsági mentések kezelése az Azure Backup szolgáltatással

Ez a cikk ismerteti, hogyan kezelheti az Azure biztonsági mentési szolgáltatással biztonsági mentést készítő Azure virtuális [gépeket (VM-eket).](backup-overview.md) A cikk is összefoglalja a biztonsági mentési információkat a tároló irányítópultján található.

Az Azure Portalon a Recovery Services-tároló irányítópultja hozzáférést biztosít a tároló adataihoz, többek között a következőkhöz:

* A legújabb biztonsági mentés, amely egyben a legújabb visszaállítási pont.
* A biztonsági mentési szabályzat.
* Az összes biztonsági mentési pillanatkép teljes mérete.
* A biztonsági mentések engedélyezett virtuális gépek száma.

A biztonsági mentések kezelése az irányítópult használatával és az egyes virtuális gépek lefúrásával. A gépbiztonsági mentések megkezdéséhez nyissa meg a tárolót az irányítópulton.

![Teljes irányítópult-nézet csúszkával](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Virtuális gépek megtekintése az irányítópulton

Virtuális gépek megtekintése a tároló irányítópultján:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. A Központ menüben válassza a **Tallózás gombot.** Az erőforrások listájába írja be a következőt: **Recovery Services**. Gépelés közben a rendszer a bevitel alapján szűri a listát. Válassza a **Helyreállítási szolgáltatások tárolóit**.

    ![Recovery Services-tároló létrehozása](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. A használat megkönnyítése érdekében kattintson a jobb gombbal a tárteremre, és válassza **a Rögzítés az irányítópultra parancsot.**
4. Nyissa meg a páncélterem irányítópultját.

    ![A tároló irányítópultjának és a Beállítások ablaktábla megnyitása](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. A **Biztonsági másolat elemei** csempén válassza az **Azure virtuális gépek**lehetőséget.

    ![A Biztonsági másolat elemei csempe megnyitása](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. A **Biztonsági másolat elemei** ablaktáblán megtekintheti a védett virtuális gépek listáját. Ebben a példában a tároló egy virtuális gépet véd: a demobackup.  

    ![A Biztonsági másolat elemei ablaktábla megtekintése](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. A tárolóelem irányítópultjáról módosítsa a biztonsági mentési házirendeket, futtasson igény szerinti biztonsági mentést, állítsa le vagy folytassa a virtuális gépek védelmét, törölje a biztonsági mentési adatokat, tekintse meg a visszaállítási pontokat, és futtasson egy visszaállítást.

    ![A Biztonsági másolat elemei irányítópult és a Beállítások ablaktábla](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Virtuális gép biztonsági mentési szabályzatának kezelése

Biztonsági mentési házirend kezelése:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/) Nyissa meg a páncélterem irányítópultját.
2. A **Biztonsági másolat elemei** csempén válassza az **Azure virtuális gépek**lehetőséget.

    ![A Biztonsági másolat elemei csempe megnyitása](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. A **Biztonsági másolat elemei** ablaktáblán megtekintheti a védett virtuális gépek listáját és az utolsó biztonsági mentés állapotát a legutóbbi visszaállítási idővel.

    ![A Biztonsági másolat elemei ablaktábla megtekintése](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. A tárolóelem irányítópultján kiválaszthat egy biztonsági mentési szabályzatot.

   * Házirendek közötti váltáshoz válasszon másik házirendet, majd válassza a **Mentés gombot.** Ekkor a rendszer automatikusan alkalmazza az új házirendet a tárolón.

     ![Biztonsági mentési szabályzat kiválasztása](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentés futtatása

A virtuális gép igény szerinti biztonsági mentését futtathatja, miután beállította annak védelmét. Tartsa szem előtt ezeket az adatokat:

* Ha a kezdeti biztonsági mentés függőben van, az igény szerinti biztonsági mentés létrehozza a virtuális gép teljes másolatát a Helyreállítási szolgáltatások tárolójában.
* Ha a kezdeti biztonsági mentés befejeződött, az igény szerinti biztonsági mentés csak az előző pillanatkép módosításait küldi el a Recovery Services-tárolóba. Ez azt, hogy a későbbi biztonsági mentések mindig növekményesek.
* Az igény szerinti biztonsági mentés megőrzési tartománya a biztonsági mentés aktiválásakor megadott megőrzési érték.

Igény szerinti biztonsági mentés aktiválása:

1. A [tárolóelem irányítópultján](#view-vms-on-the-dashboard)a **Védett elem**csoportban válassza a **Biztonsági másolat elem lehetőséget.**

    ![A Biztonsági mentés most lehetőség](./media/backup-azure-manage-vms/backup-now-button.png)

2. A **Biztonságimásolat-kezelés típusa területen**válassza az Azure virtuális **gép**lehetőséget. Megjelenik **a biztonsági másolat ielem (Az Azure virtuális gép)** ablaktábla.
3. Válasszon ki egy virtuális gép, és válassza **a Biztonsági mentés most** egy igény szerinti biztonsági mentés létrehozásához. Megjelenik **a Biztonsági mentés most** ablaktábla.
4. A **Biztonsági másolat megőrzője vagonmezőben** adja meg a biztonsági másolat megőrzésének dátumát.

    ![A Biztonsági mentés most naptár](./media/backup-azure-manage-vms/backup-now-check.png)

5. A biztonsági mentési feladat futtatásához válassza az **OK gombot.**

A feladat előrehaladásának nyomon követéséhez a tároló irányítópultján válassza a **Biztonsági mentési feladatok** csempét.

## <a name="stop-protecting-a-vm"></a>Virtuális gép védelmének leállítása

A virtuális gépek védelmének leállítását kétféleképpen lehet leállítani:

* **A védelem leállítása és a biztonsági mentési adatok megőrzése**. Ez a beállítás leállítja az összes jövőbeli biztonsági mentési feladatok a virtuális gép védelme; azonban az Azure Backup szolgáltatás megtartja a helyreállítási pontokat, amelyekről biztonsági mentésre került.  Fizetnie kell a helyreállítási pontok a tárolóban való megtartásáért (a részletekért lásd az [Azure Backup díjszabását).](https://azure.microsoft.com/pricing/details/backup/) Szükség esetén visszaállíthatja a virtuális gép. Ha úgy dönt, hogy folytatja a virtuális gép védelmét, akkor *használhatja a Biztonsági mentés folytatása* lehetőséget.
* **A védelem leállítása és a biztonsági mentési adatok törlése**. Ez a beállítás leállítja az összes jövőbeli biztonsági mentési feladatok a virtuális gép védelme, és törölje az összes helyreállítási pontokat. Nem fogja tudni visszaállítani a virtuális gép, és nem használja *folytatása biztonsági mentési* lehetőséget.

>[!NOTE]
>Ha a biztonsági mentések leállítása nélkül töröl egy adatforrást, az új biztonsági mentések sikertelenek lesznek. A régi helyreállítási pontok a házirendnek megfelelően lejárnak, de egy utolsó helyreállítási pont mindig megmarad, amíg le nem állítja a biztonsági mentéseket, és nem törli az adatokat.
>

### <a name="stop-protection-and-retain-backup-data"></a>A védelem leállítása és a biztonsági mentési adatok megőrzése

A virtuális gép védelmének leállítása és az adatok megőrzése:

1. A [tárolóelem irányítópultján](#view-vms-on-the-dashboard)válassza a **Biztonsági mentés leállítása**lehetőséget.
2. Válassza **a Biztonsági másolat adatainak megőrzése**lehetőséget, és szükség szerint erősítse meg a beállítást. Megjegyzést fűzzön hozzá, ha szeretné. Ha nem biztos az elem nevében, vigye az egérmutatót a felkiáltójel fölé a név megtekintéséhez.

    ![Biztonsági másolat adatainak megőrzése](./media/backup-azure-manage-vms/retain-backup-data.png)

Egy értesítés jelzi, hogy a biztonsági mentési feladatok leálltak.

### <a name="stop-protection-and-delete-backup-data"></a>A védelem leállítása és a biztonsági mentési adatok törlése

A virtuális gép védelmének leállítása és az adatok törlése:

1. A [tárolóelem irányítópultján](#view-vms-on-the-dashboard)válassza a **Biztonsági mentés leállítása**lehetőséget.
2. Válassza **a Biztonsági másolat adatainak törlése**lehetőséget, és szükség szerint erősítse meg a kijelölést. Írja be a biztonsági másolat elemének nevét, és szükség esetén fűzzön megjegyzést.

    ![Biztonsági mentési adatok törlése](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>Virtuális gép védelmének folytatása

Ha a [Védelem leállítása és a biztonsági mentési adatok megtartása](#stop-protection-and-retain-backup-data) lehetőséget választotta a virtuális gép védelem leállítása során, akkor **használhatja a Biztonsági mentés folytatása**lehetőséget. Ez a beállítás nem érhető el, ha a [Védelem leállítása és a biztonsági mentési adatok törlése](#stop-protection-and-delete-backup-data) vagy a Biztonsági másolat adatainak törlése lehetőséget [választja.](#delete-backup-data)

A virtuális gép védelmének folytatása:

1. A [tárolóelem irányítópultján](#view-vms-on-the-dashboard)válassza a **Biztonsági mentés folytatása**lehetőséget.

2. Kövesse a [biztonsági mentési szabályzatok kezelése](#manage-backup-policy-for-a-vm) a virtuális gép házirendjének hozzárendeléséhez kövesse a lépéseket. Nem kell választania a virtuális gép kezdeti védelmi szabályzatát.
3. Miután alkalmazta a biztonsági mentési szabályzatot a virtuális gépre, a következő üzenet jelenik meg:

    ![Sikeresen védett virtuális gépre utaló üzenet](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Biztonsági mentési adatok törlése

A virtuális gépek biztonsági másolatának adatait kétféleképpen törölheti:

* A tárolóelem irányítópultján válassza a Biztonsági mentés leállítása lehetőséget, és kövesse a Védelem leállítása és a biztonsági mentési adatok törlése beállítás [utasításait.](#stop-protection-and-delete-backup-data)

  ![Válassza a Biztonsági mentés leállítása lehetőséget](./media/backup-azure-manage-vms/stop-backup-buttom.png)

* A tárolóelem irányítópultján válassza a Biztonsági másolat adatainak törlése lehetőséget. Ez a beállítás akkor engedélyezett, ha a virtuális gép védelmének leállítása és megőrzése beállítás [átállítása](#stop-protection-and-retain-backup-data) kor döntött a biztonsági mentési adatok beállításának leállítása érdekében

  ![Válassza a Biztonsági másolat törlése lehetőséget](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  * A [tárolóelem irányítópultján](#view-vms-on-the-dashboard)válassza a **Biztonsági másolat adatainak törlése**lehetőséget.
  * Írja be a biztonsági másolat elemének nevét, és erősítse meg, hogy törölni szeretné a helyreállítási pontokat.

    ![Biztonsági mentési adatok törlése](./media/backup-azure-manage-vms/delete-backup-data1.png)

  * Az elem biztonsági mentési adatainak törléséhez válassza a **Törlés**lehetőséget. Egy értesítési üzenet jelzi, hogy a biztonsági mentési adatok at törölték.

Az adatok védelme érdekében az Azure Backup tartalmazza a helyreállítható törlési funkciót. A helyreállítható törléssel a virtuális gép biztonsági mentése (az összes helyreállítási pont) törlése után is a biztonsági mentési adatok további 14 napig megmaradnak. További információt [a helyreállítható törlési dokumentációban](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud)talál.

  > [!NOTE]
  > A biztonsági mentési adatok törlésekor törli az összes társított helyreállítási pontot. Nem választhat ki törölni kívánt helyreállítási pontokat.

### <a name="backup-item-where-primary-data-source-no-longer-exists"></a>Biztonsági másolat olyan elemről, amelynek elsődleges adatforrása már nem létezik

* Ha az Azure biztonsági mentéséhez konfigurált Azure virtuális gépek et törlik vagy áthelyezik a védelem leállítása nélkül, akkor mind az ütemezett biztonsági mentési feladatok, mind az igény szerinti (ad-hoc) biztonsági mentési feladatok sikertelenek lesznek a UserErrorVmNotFoundV2 hibával. A biztonsági mentés előzetes ellenőrzése csak a sikertelen igény szerinti biztonsági mentési feladatok esetén jelenik meg kritikusként (a sikertelen ütemezett feladatok nem jelennek meg).
* Ezek a biztonsági mentési elemek aktívak maradnak a felhasználó által beállított biztonsági mentési és adatmegőrzési házirendhez tapadó rendszerben. Az Azure-beli virtuális gépek biztonsági másolatot követő adatait az adatmegőrzési szabályzatnak megfelelően őrizzük meg. A lejárt helyreállítási pontok (kivéve az utolsó helyreállítási pont) törlődnek a biztonsági mentési házirendben beállított megőrzési tartománynak megfelelően.
* A felhasználóknak ajánlott törölni a biztonsági mentési elemeket, ahol az elsődleges adatforrás már nem létezik, hogy elkerüljék a további költségeket, ha a törlési erőforrások biztonsági mentési elemére/adataira már nincs szükség, mivel az utolsó helyreállítási pont örökre megmarad, és a felhasználónak a vonatkozó biztonsági mentési díjszabásnak megfelelően kell fizetnie.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [biztonsági másolatot az Azure-beli virtuális gépekről a virtuális gép beállításaiból.](backup-azure-vms-first-look-arm.md)
* További információ a [virtuális gépek visszaállításáról.](backup-azure-arm-restore-vms.md)
* Ismerje meg, hogyan figyelheti az [Azure virtuális gépek biztonsági mentéseit.](backup-azure-monitor-vms.md)
