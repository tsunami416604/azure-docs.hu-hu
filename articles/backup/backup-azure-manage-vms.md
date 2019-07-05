---
title: Kezelése és Azure virtuális gép biztonsági mentéseinek figyelése az Azure Backup szolgáltatás használatával
description: Ismerje meg, hogyan kezelheti és Azure virtuális gép biztonsági mentéseinek figyelése az Azure Backup szolgáltatás használatával.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: sogup
ms.openlocfilehash: add2c72535b5be0edcbc00c077dfe20a6deaa3e0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434194"
---
# <a name="manage-azure-vm-backups"></a>Azure-beli virtuális gépek biztonsági mentésének kezelése

Ez a cikk bemutatja, hogyan kezelheti az Azure virtuális gépeken (VM), amely készül biztonsági másolat használatával a [Azure Backup szolgáltatás](backup-overview.md). A cikk emellett összegzi a biztonsági mentési információkat talál a tároló irányítópultjának.


Az Azure Portalon, a Recovery Services-tároló irányítópultján információ tárba való hozzáférést biztosít többek között:

* A legutóbbi biztonsági másolatra, amely egyben a legújabb visszaállítási pont.
* A biztonsági mentési szabályzat.
* Az összes biztonságimásolat-pillanatképek mérete.
* A virtuális gépeket, amelyek engedélyezve vannak a biztonsági mentések száma.

Biztonsági másolatok összevonással lefelé az egyes virtuális gépekhez és az irányítópult használatával kezelheti. Gépek biztonsági mentéseinek a kezdéshez nyissa meg a tároló irányítópultján.

![A csúszka teljes irányítópult-nézet](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Virtuális gépek megtekintése az irányítópulton

Virtuális gépek megtekintése a tároló irányítópultjának:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A központ menüben válassza ki a **Tallózás**. Az erőforrások listájába írja be a következőt: **Recovery Services**. Írja be, mert a szűrt lista a bemenetet a következők alapján. Válassza ki **Recovery Services-tárolók**.

    ![Recovery Services-tároló létrehozása](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Egyszerűen használható, kattintson a jobb gombbal a tárolót, és válasszon **rögzítés az irányítópulton**.
4. Nyissa meg a tároló irányítópultján.

    ![Nyissa meg a tároló irányítópultjának és a beállítások panel](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. Az a **biztonsági másolati elemek** csempéről válassza **Azure Virtual Machines**.

    ![Nyissa meg a biztonsági másolati elemek csempét](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. Az a **biztonsági másolati elemek** panelen megtekintheti a védett virtuális gépek listáját. Ebben a példában a tároló egy virtuális gépet véd: demobackup.  

    ![A biztonsági mentési elemek panel megjelenítése](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. A tároló elem irányítópulton biztonsági mentési szabályzatok módosításához, futtassa egy igény szerinti biztonsági másolat, állítsa le vagy virtuális gépek védelmének folytatása, biztonsági mentési adatok törlése, visszaállítási pont megtekintése és futtathat visszaállítást.

    ![A biztonsági másolati elemek irányítópult és a beállítások panel](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Virtuális gép biztonsági mentési házirend kezelése

Biztonsági mentési házirend kezeléséhez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). Nyissa meg a tároló irányítópultján.
2. Az a **biztonsági másolati elemek** csempéről válassza **Azure Virtual Machines**.

    ![Nyissa meg a biztonsági másolati elemek csempét](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. Az a **biztonsági másolati elemek** panelen megtekintheti a védett virtuális gépek és a legújabb visszaállítási pont időpontja a legutóbbi biztonsági mentés állapota listáját.

    ![A biztonsági mentési elemek panel megjelenítése](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. A tároló elem irányítópulton válassza ki a biztonsági mentési szabályzat.

   * Házirendek váltson. Válasszon ki egy másik szabályzatot, majd válassza ki a **mentése**. Ekkor a rendszer automatikusan alkalmazza az új házirendet a tárolón.

     ![A biztonsági mentési házirend kiválasztása](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Egy igény szerinti biztonsági mentés futtatása
A virtuális gépek egy igény szerinti biztonsági mentést a védelmének beállítása után futtathatja. Tartsa szem előtt ezeket az adatokat:

- Ha a kezdeti biztonsági mentés függőben, igény szerinti biztonsági mentést a Recovery Services-tárolóban hoz létre a virtuális gép teljes másolatának.
- Ha a kezdeti biztonsági mentés befejeződött, egy igény szerinti biztonsági mentést fogja csak elküldi a módosításokat a korábbi pillanatképből a Recovery Services-tároló. Vagyis a későbbi biztonsági mentések mindig növekményes.
- Egy igény szerinti biztonsági mentés a megőrzési tartomány megadott megőrzési értékkel, hogy mikor indít el a biztonsági mentés.

Egy igény szerinti biztonsági mentés elindítása:

1. Az a [tároló elem irányítópulton](#view-vms-on-the-dashboard)alatt **védett elem**válassza **biztonságimásolat-elem**.

    ![A biztonsági mentés most lehetőség](./media/backup-azure-manage-vms/backup-now-button.png)

2. A **biztonságimásolat-felügyeleti típussal**válassza **Azure virtuális gép**. A **biztonsági mentési elemek (Azure virtuális gép)** panel jelenik meg.
3. Válassza ki a virtuális Gépet, majd **a biztonsági mentés** egy igény szerinti biztonsági mentés létrehozásához. A **biztonsági mentés** panel jelenik meg.
4. Az a **megőrzése biztonsági mentési amíg** mezőben adja meg kell őrizni a biztonsági mentés dátumát.

    ![A biztonsági mentés naptár](./media/backup-azure-manage-vms/backup-now-check.png)

5. Válassza ki **OK** a biztonsági mentési feladat futtatása.

Nyomon követheti a feladat előrehaladását a tároló irányítópultján válassza a **biztonsági mentési feladatok** csempére.

## <a name="stop-protecting-a-vm"></a>Virtuális gép védelmének megszüntetése

Leállítja a virtuális gép védelmét két módja van:

* **Védelem leállítása és a biztonsági másolatok adatainak megőrzése**. Ez a beállítás leállítja a virtuális gép; védelmét az összes jövőbeli biztonsági mentési feladatok azonban az Azure Backup szolgáltatás megőrzi a helyreállítási pontokat biztonsági másolat készült.  Kell fizetni, hogy a helyreállítási pontok a tároló (lásd: [Azure Backup árairól](https://azure.microsoft.com/pricing/details/backup/) részletekért). Láthatja, hogy a virtuális gép visszaállítása szükség esetén. Ha úgy dönt, hogy a virtuális gép védelmének folytatása, akkor használhatja *biztonsági mentés folytatása* lehetőséget.
* **Védelem leállítása és biztonsági mentési adatok törlése**. Ez a beállítás a virtuális gép védelmét az összes jövőbeli biztonsági mentési feladatot leállítja és törli a helyreállítási pontokat. Nem lehet visszaállítani a virtuális gép, sem használja *biztonsági mentés folytatása* lehetőséget.

>[!NOTE]
>Ha töröl egy adatforrást biztonsági mentés leállítása nélkül, akkor az új biztonsági mentések sikertelenek lesznek. Régi helyreállítási pontok érvényessége lejár a házirendnek megfelelően, de egy, a legutóbbi helyreállítási pont mindig kell tartani, amíg a biztonsági mentés leállítása, és törölheti az adatokat.
>

### <a name="stop-protection-and-retain-backup-data"></a>Védelem leállítása és a biztonsági másolatok adatainak megőrzése

Állítsa le a védelmet, és a virtuális gépek adatainak megőrzése:

1. Az a [tároló elem irányítópultján](#view-vms-on-the-dashboard)válassza **biztonsági mentés leállítása**.
2. Válasszon **biztonsági másolatok adatainak megőrzése**, és igény szerint, erősítse meg a választását. Ha azt szeretné, adjon hozzá megjegyzést. Ha nem tudja biztosan az elem nevét, a kurzort a felkiáltójel nevének megjelenítéséhez.

    ![Biztonsági mentési adatok megőrzése](./media/backup-azure-manage-vms/retain-backup-data.png)

Egy értesítés jelzi, hogy a biztonsági mentési feladatok leállított.

### <a name="stop-protection-and-delete-backup-data"></a>Védelem leállítása és biztonsági mentési adatok törlése

Állítsa le a védelmet, és törli az adatokat a virtuális gépek:

1. Az a [tároló elem irányítópultján](#view-vms-on-the-dashboard)válassza **biztonsági mentés leállítása**.
2. Válasszon **biztonsági másolatok adatainak törlése**, és igény szerint, erősítse meg a választását. Adja meg a biztonsági másolati elem nevét, és adjon hozzá megjegyzést, ha azt szeretné.

    ![Biztonsági mentési adatok törlése](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>A virtuális gépek védelmének folytatása

Ha választotta volna [védelem leállítása és a biztonsági másolatok adatainak megőrzése](#stop-protection-and-retain-backup-data) lehetőséget állítsa le a virtuális gép védelmét, közben, akkor **biztonsági mentés folytatása**. Ez a beállítás nem áll rendelkezésre, ha úgy dönt, [védelem leállítása és biztonsági mentési adatok törlése](#stop-protection-and-delete-backup-data) lehetőséget vagy [biztonsági mentési adatok törlése](#delete-backup-data).

Virtuális gép védelmének visszaállításához:

1. Az a [tároló elem irányítópultján](#view-vms-on-the-dashboard)válassza **biztonsági mentés folytatása**.

2. Kövesse a [biztonsági mentési házirendek kezelése](#manage-backup-policy-for-a-vm) a szabályzat hozzárendelése a virtuális gép számára. Válassza ki a virtuális gép kezdeti alkalmazásvédelmi szabályzatot nem kell.
3. A biztonsági mentési szabályzat alkalmazása a virtuális géphez, után a következő üzenet jelenik meg:

    ![Sikeresen megtörtént a védett virtuális gépek jelző üzenet](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Biztonsági mentési adatok törlése

A virtuális gép biztonsági mentési adatok törlése két módja van:

- A tároló elem irányítópultján válassza ki a biztonsági mentés leállítása, és kövesse az utasításokat a [védelem leállítása és biztonsági mentési adatok törlése](#stop-protection-and-delete-backup-data) lehetőséget.

  ![Válassza ki a biztonsági mentés leállítása](./media/backup-azure-manage-vms/stop-backup-buttom.png)

- A tároló elem irányítópultján válassza ki a biztonsági mentési adatok törlése. Ez a beállítás engedélyezve van, ha úgy döntött, kellett [védelem leállítása és a biztonsági másolatok adatainak megőrzése](#stop-protection-and-retain-backup-data) lehetőség alatt állítsa le a virtuális gép védelmét

  ![Törlés biztonsági másolat kiválasztása](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  - Az a [tároló elem irányítópulton](#view-vms-on-the-dashboard)válassza **biztonsági mentési adatok törlése**.
  - Írja be a biztonsági mentési elem győződjön meg arról, hogy szeretné-e a helyreállítási pontok törlése.

    ![Biztonsági mentési adatok törlése](./media/backup-azure-manage-vms/delete-backup-data1.png)

  - A biztonsági mentési adatok, az elem törléséhez válassza ki **törlése**. Egy értesítési üzenet jelzi, hogy a biztonsági mentési adatok törölve lett.

  > [!NOTE]
  > Ha a biztonsági mentési adatok törlése törli az összes társított helyreállítási pontokkal. Adott helyreállítási pontok törlése nem választható.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [biztonsági mentése Azure virtuális gépek a virtuális gép beállításai közül](backup-azure-vms-first-look-arm.md).
- Ismerje meg, hogyan [virtuális gépek visszaállítása](backup-azure-arm-restore-vms.md).
- Ismerje meg, hogyan [Azure virtuális gép biztonsági mentéseinek figyelése](backup-azure-monitor-vms.md).
