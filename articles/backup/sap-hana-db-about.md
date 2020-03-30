---
title: Sap HANA-adatbázis biztonsági mentése az Azure virtuális gépeken
description: Ebben a cikkben megtudhatja, hogy miként biztonsági másolatot készíteni az Azure virtuális gépeken futó SAP HANA-adatbázisokról.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 52c235c95cea73a0c51c62fcb55f7f711d2eff21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476457"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Sap HANA-adatbázis biztonsági mentése az Azure virtuális gépeken

AZ SAP HANA-adatbázisok kritikus fontosságú számítási feladatok, amelyek alacsony helyreállítási pont célkitűzést (RPO) és gyors helyreállítási idő célkitűzést (RTO) igényelnek. Most már biztonsági másolatot készíthet [az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokról](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) az [Azure Backup használatával.](https://docs.microsoft.com/azure/backup/backup-overview)

Az Azure Backup az SAP által [hitelesített Backint,](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) amely natív biztonsági mentési támogatást nyújt az SAP HANA natív API-jainak kihasználásával. Ez az ajánlat az Azure Backup-ból igazodik az Azure Backup **zéró infrastruktúra** biztonsági mentési mantrájához, így nincs szükség a biztonsági mentési infrastruktúra üzembe helyezésére és kezelésére. Mostantól zökkenőmentesen biztonsági másolatot készíthet és visszaállíthatja az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokat ([az M sorozatú virtuális gépek](../virtual-machines/m-series.md) is támogatottak most!), és kihasználhatja az Azure Backup által biztosított vállalati felügyeleti képességeket.

## <a name="added-value"></a>Hozzáadott érték

Az Azure Backup használatával az SAP HANA-adatbázisok biztonsági mentése és visszaállítása a következő előnyökkel jár:

* **15 perces helyreállítási pont célkitűzés (RPO):** A kritikus adatok akár 15 perces helyreállítása már lehetséges.
* **Egykattintásos, időponthoz kötött visszaállítás:** Az éles adatok visszaállítása alternatív HANA-kiszolgálókra egyszerű. A biztonsági mentések és katalógusok lehelése a visszaállítások végrehajtásához mind az Azure által a színfalak mögött.
* **Hosszú távú megőrzés**: A szigorú megfelelési és ellenőrzési igények érdekében. Őrizze meg a biztonsági mentések évekig, a megőrzési időtartam alapján, amelyen túl a helyreállítási pontok at automatikusan a beépített életciklus-kezelési képesség lesz kimetszéssel.
* **Biztonsági mentés kezelése az Azure-ból:** Az Azure Backup felügyeleti és figyelési képességeivel javíthatja a felügyeleti élményt. Az Azure CLI is támogatott.

A biztonsági mentési és visszaállítási forgatókönyvek, amelyeket ma támogatunk, tekintse meg az [SAP HANA forgatókönyv támogatási mátrix.](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)

## <a name="backup-architecture"></a>Biztonsági mentési architektúra

![Biztonsági másolat architektúrájának diagramja](./media/sap-hana-db-about/backup-architecture.png)

* A biztonsági mentési folyamat egy [helyreállítási szolgáltatások tárolójának létrehozásával](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault) kezdődik az Azure-ban. Ez a tároló az idő múlásával létrehozott biztonsági mentések és helyreállítási pontok tárolására szolgál.
* Az Azure-beli virtuális gép SAP HANA-kiszolgáló regisztrálva van a tárolóban, és a biztonsági mentésre váró adatbázisok [at fedeznek fel.](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases) Ahhoz, hogy az Azure Backup szolgáltatás adatbázisok felderítéséhez, egy [előzetes regisztrációs parancsfájlt](https://aka.ms/scriptforpermsonhana) kell futtatni a HANA-kiszolgálón, mint a root felhasználó.
* Ez a szkript létrehozza **az AZUREWLBACKUPHANAUSER** DB felhasználót és egy megfelelő kulcsot ugyanazzal a névvel a **hdbuserstore -ban.** Tekintse meg a [Mi az előzetes regisztrációs parancsfájl nem](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) szakasz, hogy jobban megismerjék, mit csinál a parancsfájl.
* Az Azure backup szolgáltatás most telepíti az **Azure Backup Plugin hana** a regisztrált SAP HANA-kiszolgálón.
* Az **azurewlbackuphanauser** db felhasználó által létrehozott előregisztrációs parancsfájl által használt **Azure Backup Plugin hana** az összes biztonsági mentési és visszaállítási műveletek végrehajtásához. Ha a parancsfájl futtatása nélkül próbálja meg konfigurálni az SAP HANA DB-k biztonsági mentését, a következő hibaüzenet jelenhet meg: **UserErrorHanaScriptNotRun**.
* A [felderített adatbázisok biztonsági mentésének konfigurálásához](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup) válassza ki a szükséges biztonsági mentési házirendet, és engedélyezze a biztonsági mentéseket.

* A biztonsági mentés konfigurálása után az Azure Backup szolgáltatás a következő Backint-paramétereket állítja be az ADATBÁZIS szintjén a védett SAP HANA-kiszolgálón:
  * [catalog_backup_using_backint:true]
  * [enable_accumulated_catalog_backup:hamis]
  * [parallel_data_backup_backint_channels:1]
  * [log_backup_timeout_s:900)]
  * [backint_response_timeout:7200]

>[!NOTE]
>Győződjön meg arról, hogy ezek a paraméterek *nincsenek* jelen host szinten. Az állomásszintű paraméterek felülírják ezeket a paramétereket, és nem várt viselkedést okozhatnak.
>

* Az **Azure Backup Plugin hana** fenntartja az összes biztonsági mentési ütemezések és a szabályzat részleteit. Elindítja az ütemezett biztonsági mentéseket, és a Backint API-kon keresztül kommunikál a **HANA biztonsági mentési** motorjával.
* A **HANA biztonsági mentési motor** visszaad egy Backint-adatfolyamot a biztonsági másolatban részt veendő adatokkal.
* Az összes ütemezett biztonsági mentést és igény szerinti biztonsági mentést (az Azure Portalról indítva), amelyek teljesek vagy különbözetiek, az **Azure Backup Plugin for HANA**kezdeményezi. A naplóbiztonsági mentések azonban a **HANA biztonsági másolat** motorja által kezelhetők és aktiválódnak.
* Az SAP HANA Azure Backup szolgáltatása, amely backInt-tanúsítvánnyal rendelkező megoldás, nem függ az alapul szolgáló lemez- vagy virtuálisgép-típusoktól. A biztonsági mentést a HANA által létrehozott adatfolyamok hajtják végre.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Az Azure VM biztonsági mentésének használata az Azure SAP HANA biztonsági mentén

Az SAP HANA biztonsági mentés használata az Azure-ban, amely adatbázisszintű biztonsági mentést és helyreállítást biztosít, az Azure virtuális gép biztonsági mentési megoldással biztonsági másolatot készíthet az operációs rendszer ről és a nem adatbázis-lemezekről.

A [Backint-tanúsítvánnyal rendelkező Azure SAP HANA biztonsági mentési megoldás](#backup-architecture) használható adatbázis biztonsági mentéséhez és helyreállításához.

[Az Azure vm biztonsági mentése](backup-azure-vms-introduction.md) az operációs rendszer és más nem adatbázis-lemezek biztonsági mentéséhez használható. A virtuális gép biztonsági mentése naponta egyszer készül, és biztonsági másolatot készít az összes lemezről (kivéve az **Írásgyorsító (WA) lemezeket** és az **UltraDisks lemezeket).** Mivel az adatbázis ról az Azure SAP HANA biztonsági mentési megoldással készül biztonsági másolat, csak az operációs rendszer és a nem adatbázis-lemezek fájlkonzisztens biztonsági mentést készíthet a kizárási lemez képesség használatával, amely jelenleg előzetes verzióban érhető el.

>[!NOTE]
> Az Azure vm biztonsági mentésével az előzetes közzétételelőtti parancsfájlok használata lehetővé teszi az adatbázis adatköteteinek alkalmazáskonzisztens biztonsági mentését. Ha azonban a naplóterület a WA-lemezeken található, előfordulhat, hogy ezeknek a lemezeknek a pillanatképe nem garantálja a naplózási terület konzisztenciáját. Hana rendelkezik egy explicit módon napló biztonsági mentések pontosan emiatt generálása. Engedélyezze ugyanazt az SAP HANA-ban, és az Azure SAP HANA biztonsági mentésével biztonsági másolatot készíthet.

Az SAP HANA-t futtató virtuális gép visszaállításához hajtsa végre az alábbi lépéseket:

* [Állítsa vissza az új virtuális gép az Azure virtuális gép biztonsági mentését](backup-azure-arm-restore-vms.md) a legújabb helyreállítási pontról. Vagy hozzon létre egy új üres virtuális gép, és csatolja a lemezeket a legújabb helyreállítási pont.
* Mivel a WA lemezekről nem készül biztonsági másolatot készíteni, azok nem lesznek visszaállítva. Üres WA lemezek és naplóterület létrehozása.
* Miután az összes többi konfiguráció (például ip- és rendszernév) be van állítva, a virtuális gép úgy van beállítva, hogy megkapja a DB-adatokat az Azure biztonsági mentéséből.
* Most állítsa vissza a db a virtuális gép az [Azure SAP HANA DB biztonsági mentést](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) a kívánt pont-in-time.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [állíthatja vissza az Azure virtuális gépen futó SAP HANA-adatbázist](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Megtudhatja, hogyan kezelheti az Azure Backup használatával biztonsági mentést készítő [SAP HANA-adatbázisokat](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
