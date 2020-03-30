---
title: SAP HANA adatbázisok biztonsági mentési hibáinak elhárítása
description: Bemutatja, hogyan háríthatja el az okat, amelyek az Azure Backup használatával az SAP HANA-adatbázisok biztonsági mentésekor fordulhatnak elő.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 6520f106011b632da2725f456aeb278c7748ddc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459310"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>SAP HANA-adatbázisok biztonsági mentése az Azure-ban

Ez a cikk hibaelhárítási információkat tartalmaz az SAP HANA-adatbázisok azure-beli virtuális gépeken történő biztonsági mentéséhez. Az SAP HANA biztonsági mentési forgatókönyveiről, amelyeket jelenleg támogatunk, további információt a Forgatókönyv támogatása című témakörben [talál.](sap-hana-backup-support-matrix.md#scenario-support)

## <a name="prerequisites-and-permissions"></a>Előfeltételek és engedélyek

Tekintse meg az [előfeltételeket,](tutorial-backup-sap-hana-db.md#prerequisites) és [mi az előzetes regisztrációs parancsfájl nem](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) szakaszok konfigurálása előtt biztonsági mentések.

## <a name="common-user-errors"></a>Gyakori felhasználói hibák

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Hibaüzenet**      | <span style="font-weight:normal">Az Azure biztonsági mentés nem rendelkezik a biztonsági mentés végrehajtásához szükséges szerepkör-jogosultságokkal</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Lehetséges okok**    | Lehet, hogy felülírták a szerepkört.                          |
| **Javasolt művelet** | A probléma megoldásához futtassa a parancsfájlt a **Discover DB** ablaktáblán, vagy töltse le [itt.](https://aka.ms/scriptforpermsonhana) Másik lehetőségként adja hozzá a "SAP_INTERNAL_HANA_SUPPORT" szerepkört a számítási feladatok biztonsági mentési felhasználójához (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Hibaüzenet      | <span style="font-weight:normal">Nem sikerült csatlakozni a HANA-rendszerhez</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Az SAP HANA-példány lehet, hogy nem.<br/>A HANA-adatbázissal való együttműködéshez szükséges engedélyek nincsenek beállítva az Azure biztonsági mentéséhez. |
| **Javasolt művelet** | Ellenőrizze, hogy az SAP HANA adatbázis a. Ha az adatbázis működik, ellenőrizze, hogy az összes szükséges engedély be van-e állítva. Ha valamelyik engedély hiányzik, futtassa az [előregisztrációs parancsfájlt](https://aka.ms/scriptforpermsonhana) a hiányzó engedélyek hozzáadásához. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Hibaüzenet      | <span style="font-weight:normal">A megadott SAP HANA-példány érvénytelen vagy nem található.</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Egyetlen SAP HANA-példány egyetlen Azure-beli virtuális gépen nem lehet biztonsági másolatot kapni. |
| **Javasolt művelet** | Futtassa az [előzetes regisztrációs parancsfájlt](https://aka.ms/scriptforpermsonhana) az SAP HANA-példányon, amelyről biztonsági másolatot szeretne. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Hibaüzenet      | <span style="font-weight:normal">A megadott SAP HANA-művelet nem támogatott</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Az SAP HANA Azure biztonsági mentése nem támogatja a növekményes biztonsági mentést és az SAP HANA natív ügyfeleken végrehajtott műveleteket (Studio/ Cockpit/ DBA Cockpit) |
| **Javasolt művelet** | További információkért olvassa el [itt](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesnotSupportBackupType

| Hibaüzenet      | <span style="font-weight:normal">Ez az SAP HANA-adatbázis nem támogatja a kért biztonsági mentés típusát</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Az Azure biztonsági mentés nem támogatja a növekményes biztonsági mentést és biztonsági mentést pillanatképek használatával |
| **Javasolt művelet** | További információkért olvassa el [itt](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Hibaüzenet      | <span style="font-weight:normal">A biztonsági másolat naplólánca megszakadt</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Lehet, hogy a naplóbiztonsági mentés imascélja biztonsági másolatról fájlrendszerre lett frissítve, vagy a backint végrehajtható fájlrendszerről lett frissítve. |
| **Javasolt művelet** | A probléma megoldásához teljes biztonsági mentés tindítson el                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UserErrorIncomaptibleSrcTargetSystsemsForRestore

| Hibaüzenet      | <span style="font-weight:normal">A visszaállítás forrás- és célrendszerei nem kompatibilisek</span>    |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | A visszaállításcélrendszere nem kompatibilis a forrással |
| **Javasolt művelet** | A ma támogatott visszaállítási típusokról az SAP [1642148](https://launchpad.support.sap.com/#/notes/1642148) megjegyzése című dokumentumban olvashat. |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Hibaüzenet      | <span style="font-weight:normal">SDC-ről MDC-re frissítés észlelve</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Az SAP HANA-példány sdc-ről MDC-re lett frissítve. A frissítés után a biztonsági mentések sikertelenek lesznek. |
| **Javasolt művelet** | A probléma megoldásához kövesse az [SAP HANA 1.0-2.0 frissítés című szakaszban](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) felsorolt lépéseket. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Hibaüzenet      | <span style="font-weight:normal">Érvénytelen backint konfigurációt észlelt</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | A háttérparaméterek helytelenül vannak megadva az Azure biztonsági mentéséhez |
| **Javasolt művelet** | Ellenőrizze, hogy a következő (backint) paraméterek vannak-e beállítva:<br/>\*[catalog_backup_using_backint:true]<br/>\*[enable_accumulated_catalog_backup:hamis]<br/>\*[parallel_data_backup_backint_channels:1]<br/>\*[log_backup_timeout_s:900)]<br/>\*[backint_response_timeout:7200]<br/>Ha backint-alapú paraméterek vannak jelen a HOST, távolítsa el őket. Ha a paraméterek nem jelennek meg host szinten, de manuálisan módosították az adatbázis szintjén, visszaállíthatja őket a korábban leírt megfelelő értékekre. Vagy futtassa a [stop védelmet, és őrizze meg a biztonsági mentési adatokat](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) az Azure Portalon, majd válassza a Biztonsági mentés **folytatása**lehetőséget. |

## <a name="restore-checks"></a>Ellenőrzések visszaállítása

### <a name="single-container-database-sdc-restore"></a>Egytárolós adatbázis (SDC) visszaállítása

Gondoskodjon a bemenetek, miközben egy tároló adatbázis (SDC) a HANA egy másik SDC-gépre visszaállítása közben. Az adatbázis nevét kisbetűvel és zárójelben "sdc" betűvel kell megadni. A HANA-példány nagybetűkben jelenik meg.

Tegyük fel, hogy egy SDC HANA-példány "H21" biztonsági másolatot készíteni. A biztonsági másolat elemeinek lapja **"h21(sdc)"** néven jeleníti meg a biztonsági másolat elemének nevét. Ha megpróbálja visszaállítani az adatbázist egy másik cél SDC, mondjuk H11, majd a következő bemeneteket kell biztosítani.

![Visszaállított SDC-adatbázis neve](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Vegye figyelembe a következő szempontokat:

- Alapértelmezés szerint a visszaállított adatbázisnév a biztonsági másolat elemnevével lesz feltöltve. Ebben az esetben a h21(sdc).
- Ha h11-ként jelöli ki a célt, az NEM változtatja meg automatikusan a visszaállított adatbázis nevét. **A rendeletet h11(sdc) értékre kell kidolgozni.** Az SDC esetében a visszaállított adatbázisnév lesz a kisbetűkkel és a zárójelben hozzáfűzött "sdc" betűazonosítóval rendelkező célpéldányazonosító.
- Mivel az SDC-nek csak egyetlen adatbázisa lehet, a jelölőnégyzetre is rá kell kattintania, hogy a meglévő adatbázisadatok felülíródjanak a helyreállítási pont adataival.
- A Linux a kis- és nagybetűket is érzékenyen kivan. Ezért legyen óvatos, hogy megőrizze az ügyet.

### <a name="multiple-container-database-mdc-restore"></a>Többtárolós adatbázis (MDC) visszaállítása

A HANA több tárolóadatbázisában a szabványos konfiguráció systemdb + 1 vagy több bérlői DB. Egy teljes SAP HANA-példány visszaállítása azt jelenti, hogy a SYSTEMDB és a bérlői DB-k visszaállítása. Az egyik először visszaállítja a SYSTEMDB-t, majd folytatja a bérlői adatbázist. A Rendszer-adatbázis lényegében azt jelenti, hogy felülírja a kiválasztott cél rendszeradatait. Ez a visszaállítás felülbírálja a célpéldány BackInt-vel kapcsolatos információit is. Így miután a rendszer DB visszaáll a célpéldány, futtassa újra az előzetes regisztrációs parancsfájlt. Csak akkor a későbbi bérlői adatbázis-visszaállítások sikeresek lesznek.

## <a name="upgrading-from-sap-hana-10-to-20"></a>Frissítés SAP HANA 1.0-ról 2.0-ra

Ha az SAP HANA 1.0-s adatbázisokat védi, és 2.0-ra szeretne frissíteni, majd hajtsa végre a következő lépéseket:

- Állítsa le a [védelmet](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) a régi SDC-adatbázis adatainak megőrzésével.
- Végezze el a frissítést. A befejezés után a HANA mostantól MDC egy rendszerdb-vel és egy bérlői db-vel
- Futtassa újra [az előzetes regisztrációs parancsfájlt](https://aka.ms/scriptforpermsonhana) a (sid és mdc) helyes részleteivel.
- Regisztráljon újra bővítményt ugyanahhoz a géphez az Azure Portalon (Biztonsági mentés -> részletek megtekintéséhez -> Válassza ki a megfelelő Azure VM -> újraregisztrálás).
- Kattintson újra felfedezni a db-k ugyanahhoz a virtuális géphez. Ennek a műveletnek a 2.
- Az új adatbázisok biztonsági mentésének konfigurálása.

## <a name="upgrading-without-an-sid-change"></a>Frissítés sid-módosítás nélkül

Az operációs rendszerre vagy az SAP HANA-ra való, sid-módosítást nem okozó frissítések az alábbiak szerint kezelhetők:

- [A védelem leállítása](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) az adatbázis adatainak megőrzésével
- Végezze el a frissítést.
- Futtassa újra az [előzetes regisztrációs parancsfájlt](https://aka.ms/scriptforpermsonhana). Általában láttuk frissítési folyamat eltávolítja a szükséges szerepköröket. Az előzetes regisztrációs parancsfájl futtatása segít az összes szükséges szerepkör ellenőrzésében.
- Az adatbázis [védelmének folytatása](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database)

## <a name="re-registration-failures"></a>Újraregisztrációs hibák

Az újbóli regisztrálási művelet megkezdése előtt ellenőrizze, hogy van-e az alábbi jelenség:

- Minden művelet (például a biztonsági mentés, visszaállítás és a biztonsági mentés konfigurálása) sikertelen a virtuális számítógépen a következő hibakódok egyikével: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Ha a **biztonsági másolat állapota** területen látható a Nem érhető **el,** zárja ki az összes többi okot, amely ugyanazt az állapotot eredményezheti:

  - A virtuális gép biztonsági mentésével kapcsolatos műveletek végrehajtására vonatkozó engedély hiánya
  - A virtuális gép leáll, így a biztonsági mentések nem történhetnek meg
  - Hálózati problémák

Ezek a tünetek a következő okok miatt merülhetnek fel:

- Egy bővítményt töröltek vagy eltávolítottak a portálról.
- A virtuális gép visszaállt az időben a helyszíni lemez-visszaállítás révén.
- A virtuális gép hosszabb időre leállt, így a bővítmény konfigurációja lejárt.
- A virtuális gép törölve lett, és egy másik virtuális gép jött létre ugyanazzal a névvel, és ugyanabban az erőforráscsoportban, mint a törölt virtuális gép.

Az előző forgatókönyvekben azt javasoljuk, hogy indítsa el a virtuális gép újraregisztrálási műveletet.

## <a name="next-steps"></a>További lépések

- Tekintse át az SAP HANA-adatbázisok azure-beli virtuális gépeken való biztonsági mentésével kapcsolatos [gyakori kérdéseket.](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm)
