---
title: Kezelése és Azure virtuális gép biztonsági mentéseinek figyelése az Azure Backup szolgáltatás használatával
description: Ismerje meg, hogyan kezelheti és Azure virtuális gép biztonsági mentéseinek figyelése az Azure Backup szolgáltatás használatával.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: sogup
ms.openlocfilehash: 0fa221721471772b066990ec2d33f0cedb960239
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453541"
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

4. Az a **biztonsági másolati elemek** csempéről válassza **Azure Virtual Machines**.

    ![Nyissa meg a biztonsági másolati elemek csempét](./media/backup-azure-manage-vms/contoso-vault-1606.png)

5. Az a **biztonsági másolati elemek** panelen láthatja, hogy az egyes elemekhez az utolsó biztonsági mentési feladat. Ebben a példában a tároló egy virtuális gépet véd: demovm-markgal.  

    ![A biztonsági mentési elemek panel megjelenítése](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)


6. A tároló elem irányítópultról is hozzon létre vagy módosítsa a biztonsági mentési szabályzatok, visszaállítási pont megtekintése, futtatásakor egy igény szerinti biztonsági másolat, állítsa le vagy virtuális gépek védelmének folytatása, törölni a helyreállítási pontok, és futtathat visszaállítást.

    ![A biztonsági másolati elemek irányítópult és a beállítások panel](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policies"></a>Biztonsági mentési irányelvek kezelése

Biztonsági mentési házirend kezeléséhez:

1. Az a [tároló elem irányítópulton](#view-vms-in-the-dashboard)válassza **minden beállítás**.

    ![Az összes beállítás](./media/backup-azure-manage-vms/all-settings-button.png)
2. A **beállítások**válassza **biztonsági mentési szabályzat**.
3. Az a **biztonsági mentési házirend kiválasztása** menüben:

   * Házirendek váltson. Válasszon ki egy másik szabályzatot, majd válassza ki a **mentése**. Ekkor a rendszer automatikusan alkalmazza az új házirendet a tárolón.
   * Hozzon létre egy házirendet, válassza ki a **hozzon létre új**. További információkért lásd: [biztonsági mentési szabályzat konfigurálása](backup-azure-arm-vms-prepare.md#configure-a-backup-policy).

     ![A biztonsági mentési házirend kiválasztása](./media/backup-azure-manage-vms/backup-policy-create-new.png)


## <a name="run-an-on-demand-backup"></a>Egy igény szerinti biztonsági mentés futtatása
A virtuális gépek egy igény szerinti biztonsági mentést a védelmének beállítása után futtathatja. Tartsa szem előtt ezeket az adatokat: 
- Ha a kezdeti biztonsági mentés függőben, igény szerinti biztonsági mentést a Recovery Services-tárolóban hoz létre a virtuális gép teljes másolatának.
- Ha a kezdeti biztonsági mentés befejeződött, egy igény szerinti biztonsági mentést fogja csak elküldi a módosításokat a korábbi pillanatképből a Recovery Services-tároló. Vagyis a későbbi biztonsági mentések mindig növekményes.
- Egy igény szerinti biztonsági mentés a megőrzési tartomány megadott megőrzési értékkel, hogy mikor indít el a biztonsági mentés.

Egy igény szerinti biztonsági mentés elindítása:

1. Az a [tároló elem irányítópulton](#view-vms-in-the-dashboard)alatt **védett elem**válassza **biztonságimásolat-elem**.

    ![A biztonsági mentés most lehetőség](./media/backup-azure-manage-vms/backup-now-button.png)

2. A **biztonságimásolat-felügyeleti típussal**válassza **Azure virtuális gép**. A **biztonsági mentési elemek (Azure virtuális gép)** panel jelenik meg.
3. Válassza ki a virtuális Gépet, majd **a biztonsági mentés** egy igény szerinti biztonsági mentés létrehozásához. A **biztonsági mentés** panel jelenik meg.
4. Az a **megőrzése biztonsági mentési amíg** mezőben adja meg kell őrizni a biztonsági mentés dátumát.

    ![A biztonsági mentés naptár](./media/backup-azure-manage-vms/backup-now-check.png)

5. Válassza ki **OK** a biztonsági mentési feladat futtatása.

Nyomon követheti a feladat előrehaladását a tároló irányítópultján válassza a **biztonsági mentési feladatok** csempére.

## <a name="stop-protecting-a-vm"></a>Virtuális gép védelmének megszüntetése

Leállítja a virtuális gép védelmét két módja van:

- Állítsa le az összes jövőbeli biztonsági mentési feladatot, és törli az összes helyreállítási pontot. Ebben az esetben nem lehet visszaállítani a virtuális Gépet.
- Állítsa le az összes jövőbeli biztonsági mentési feladatot, és megőrizni a helyreállítási pontokat. Bár a következőket kell tennie, hogy a helyreállítási pontok a tárolóban kell fizetnie, fogja tudni visszaállítani a virtuális gép, szükség esetén. További információkért lásd: [Azure Backup árairól](https://azure.microsoft.com/pricing/details/backup/).

>[!NOTE]
>Ha töröl egy adatforrást biztonsági mentés leállítása nélkül, akkor az új biztonsági mentések sikertelenek lesznek. Régi helyreállítási pontok érvényessége lejár a házirendnek megfelelően, de egy, a legutóbbi helyreállítási pont mindig kell tartani, amíg a biztonsági mentés leállítása, és törölheti az adatokat.
>

Virtuális gép védelmének megszüntetése:

1. Az a [tároló elem irányítópultján](#view-vms-in-the-dashboard)válassza **biztonsági mentés leállítása**.
2. Döntse el, hogy megtartja vagy a biztonsági mentési adatok törlése, és ellenőrizze a választást, igény szerint. Ha azt szeretné, adjon hozzá megjegyzést. Ha nem tudja biztosan az elem nevét, a kurzort a felkiáltójel nevének megjelenítéséhez.

    ![Védelem leállítása](./media/backup-azure-manage-vms/retain-or-delete-option.png)

     Egy értesítés jelzi, hogy a biztonsági mentési feladatok leállított.

## <a name="resume-protection-of-a-vm"></a>A virtuális gépek védelmének folytatása

Ha a virtuális gép leállításakor biztonsági mentési adatokat tárolja, később folytathatja a védelmet. Ha törli a biztonsági mentési adatokat, a védelem nem folytatható.

Virtuális gép védelmének visszaállításához:

1. Az a [tároló elem irányítópultján](#view-vms-in-the-dashboard)válassza **biztonsági mentés folytatása**.

2. Kövesse a [biztonsági mentési házirendek kezelése](#manage-backup-policies) a szabályzat hozzárendelése a virtuális gép számára. Válassza ki a virtuális gép kezdeti alkalmazásvédelmi szabályzatot nem kell.
3. A biztonsági mentési szabályzat alkalmazása a virtuális géphez, után a következő üzenet jelenik meg:

    ![Sikeresen megtörtént a védett virtuális gépek jelző üzenet](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>biztonsági mentési adatok törlése

Virtuális gép biztonsági mentési adatok során törölheti a **biztonsági mentés leállítása** feladat vagy a biztonsági mentési feladat befejeződése után. Biztonsági mentési adatok törlése előtt tartsa szem előtt ezeket az adatokat:

- Várjon a napok vagy hetek a helyreállítási pontok törlése előtt érdemes lehet.
- A folyamat ellentétben a helyreállítási pontok visszaállítása, biztonsági mentési adatok törlésekor nem választható, adott helyreállítási pontok törlése. A biztonsági mentési adatok törlése, ha törli az összes társított helyreállítási pontokkal.

Állítsa le, vagy tiltsa le a virtuális gép biztonsági mentési feladata után törölheti a biztonsági mentési adatokat:


1. Az a [tároló elem irányítópulton](#view-vms-in-the-dashboard), jelölje be **Delete backup**.

    ![Törlés biztonsági másolat kiválasztása](./media/backup-azure-manage-vms/delete-backup-buttom.png)

1. Írja be a biztonsági mentési elem győződjön meg arról, hogy szeretné-e a helyreállítási pontok törlése.

    ![Győződjön meg arról, hogy szeretné-e a helyreállítási pontok törlése](./media/backup-azure-manage-vms/item-verification-box.png)

1. A biztonsági mentési adatok, az elem törléséhez válassza ki **törlése**. Egy értesítési üzenet jelzi, hogy a biztonsági mentési adatok törölve lett.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [biztonsági mentése Azure virtuális gépek a virtuális gép beállításai közül](backup-azure-vms-first-look-arm.md).
- Ismerje meg, hogyan [virtuális gépek visszaállítása](backup-azure-arm-restore-vms.md).
- Ismerje meg, hogyan [Azure virtuális gép biztonsági mentéseinek figyelése](backup-azure-monitor-vms.md).
