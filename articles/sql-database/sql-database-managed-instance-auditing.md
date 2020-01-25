---
title: Felügyelt példány naplózása
description: Megtudhatja, hogyan kezdheti meg Azure SQL Database felügyelt példányok naplózását a T-SQL használatával
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/08/2019
ms.openlocfilehash: 9b96969027431f289e366b150fbfc6a62ee6a908
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719908"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Ismerkedés a Azure SQL Database felügyelt példányok naplózásával

A [felügyelt példányok](sql-database-managed-instance.md) naplózása nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókban lévő naplóba írja azokat. Naplózás is:

- Segít megőrizni a jogszabályi megfelelőséget, értelmezni az adatbázis tevékenységeit, és betekintést nyerhet olyan eltérésekkel és rendellenességekkel, amelyek üzleti vagy feltételezett biztonsági szabálysértéseket jelezhetnek.
- Engedélyezi és megkönnyíti a megfelelőségi szabványok betartását, bár nem garantálja a megfelelőséget. A szabványok megfelelőségét támogató Azure-programokkal kapcsolatos további információkért tekintse meg a [Azure biztonsági és adatkezelési központ](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , ahol megtalálhatja a SQL Database megfelelőségi tanúsítványok legújabb listáját.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Naplózás beállítása a kiszolgálóhoz az Azure Storage-hoz

A következő szakasz ismerteti a felügyelt példány naplózásának konfigurációját.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. Hozzon létre egy Azure Storage- **tárolót** , amelyben a naplók tárolása történik.

   1. Navigáljon az Azure Storage-ba, ahol a naplókat tárolni szeretné.

      > [!IMPORTANT]
      > A régiók közötti olvasási/írási műveletek elkerülése érdekében használjon a felügyelt példányokkal megegyező régióban található Storage-fiókot.

   1. A Storage-fiókban válassza az **Áttekintés** lehetőséget, majd kattintson a **Blobok**elemre.

      ![Azure Blob widget](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. A felső menüben kattintson a **+ tároló** elemre egy új tároló létrehozásához.

      ![BLOB-tároló létrehozása ikon](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Adja meg a tároló **nevét**, állítsa a nyilvános hozzáférési szintet **magán**értékre, majd kattintson **az OK**gombra.

      ![BLOB-tároló konfigurációjának létrehozása](./media/sql-managed-instance-auditing/3_create_container_config.png)

1. Miután létrehozta a tárolót a naplókhoz, kétféleképpen konfigurálható a naplók céljaként: a [T-SQL használatával](#blobtsql) vagy [a SQL Server Management Studio (SSMS) felhasználói felület használatával](#blobssms):

   - <a id="blobtsql"></a>A blog Storage konfigurálása a naplókhoz a T-SQL használatával:

     1. A tárolók listában kattintson az újonnan létrehozott tárolóra, majd a **tároló tulajdonságai**elemre.

        ![BLOB-tároló tulajdonságai gomb](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Másolja a tároló URL-címét a másolás ikonra kattintva, és mentse az URL-címet (például a Jegyzettömbben) későbbi használatra. A tároló URL-formátumának `https://<StorageName>.blob.core.windows.net/<ContainerName>` kell lennie

        ![BLOB-tároló másolási URL-címe](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Azure Storage sas- **token** létrehozása a felügyelt példányok naplózásához a Storage-fiókhoz való hozzáférési jogosultságok biztosításához:

        - Navigáljon ahhoz az Azure Storage-fiókhoz, ahol az előző lépésben létrehozta a tárolót.

        - Kattintson a **megosztott hozzáférés aláírása** lehetőségre a tárolási beállítások menüben.

          ![Közös hozzáférésű aláírás ikon a tárolási beállítások menüben](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Konfigurálja az SAS-t a következőképpen:

          - **Engedélyezett szolgáltatások**: blob

          - **Kezdési dátum**: az időzónával kapcsolatos problémák elkerülése érdekében ajánlott a tegnapi dátum használata

          - **Befejezési dátum**: válassza ki a dátumot, AMELYEN az SAS-jogkivonat lejár

            > [!NOTE]
            > A naplózási hibák elkerülése érdekében újítsa meg a tokent a lejárat után.

          - Kattintson az **SAS előállítása** gombra.
            
            ![SAS-konfiguráció](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - Miután rákattintott az SAS előállítására, megjelenik az SAS-jogkivonat az alján. Másolja a tokent a másolás ikonra kattintva, és mentse (például a Jegyzettömbben) későbbi használatra.

          ![SAS-token másolása](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Eltávolítja a kérdőjel ("?") karaktert a jogkivonat elejétől.

     1. Kapcsolódjon a felügyelt példányhoz SQL Server Management Studio (SSMS) vagy bármely más támogatott eszköz használatával.

     1. A következő T-SQL-utasítás végrehajtásával **hozzon létre egy új hitelesítő adatot** az előző lépésekben létrehozott Container URL-lel és Sas-token használatával:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. A következő T-SQL-utasítás végrehajtásával hozzon létre egy új kiszolgáló-naplózást (válassza ki a saját napló nevét, használja az előző lépésekben létrehozott tároló URL-címét). Ha nincs megadva, `RETENTION_DAYS` alapértelmezett értéke 0 (korlátlan megőrzés):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. Folytatás [egy kiszolgáló naplózási specifikációjának vagy adatbázis-naplózási specifikációjának létrehozásával](#createspec)

   - <a id="blobssms"></a>A blob Storage konfigurálása a naplókhoz a SQL Server Management Studio (SSMS) 18 (előzetes verzió) használatával:

     1. Kapcsolódjon a felügyelt példányhoz SQL Server Management Studio (SSMS) felhasználói felület használatával.

     1. Bontsa ki a Object Explorer legfelső szintű megjegyzését.

     1. Bontsa ki a **Biztonság** csomópontot, kattintson a jobb gombbal a **naplózás** csomópontra, majd kattintson az "új naplózás" elemre:

        ![Biztonsági és naplózási csomópont kibontása](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Győződjön meg arról, hogy az "URL" elem be van jelölve a **naplózási célhelyen** , és kattintson a **Tallózás**gombra:

        ![Az Azure Storage tallózása](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. Választható Jelentkezzen be az Azure-fiókjába:

        ![Bejelentkezés az Azure-ba](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. Válassza ki az előfizetést, a Storage-fiókot és a BLOB-tárolót a legördülő listából, vagy hozzon létre egy saját tárolót a **Létrehozás**gombra kattintva. Ha elkészült, kattintson **az OK**gombra:

        ![Válassza az Azure-előfizetés, a Storage-fiók és a blob-tároló elemet.](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Kattintson az **OK** gombra a "napló létrehozása" párbeszédablakban.

1. <a id="createspec"></a>Miután konfigurálta a BLOB-tárolót a naplók céljának megfelelően, hozzon létre egy kiszolgáló-naplózási specifikációt vagy egy adatbázis-naplózási specifikációt SQL Server:

   - [Kiszolgáló naplózási specifikációjának létrehozása T-SQL-útmutató](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Adatbázis-naplózási specifikáció létrehozása T-SQL-útmutató](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

1. Engedélyezze a 6. lépésben létrehozott kiszolgáló-naplózást:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

További információ:

- [Az önálló adatbázisok, rugalmas készletek és felügyelt példányok közötti különbségek naplózása Azure SQL Database és adatbázisaiban SQL Server](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [KISZOLGÁLÓ NAPLÓZÁSÁNAK LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>A kiszolgáló naplózásának beállítása az Event hub vagy a Azure Monitor naplók számára

A felügyelt példányokból származó naplókat még hubokba vagy Azure Monitor naplókba is lehet elküldeni. Ez a szakasz a konfigurálásának módját ismerteti:

1. Navigáljon az [Azure Portalon](https://portal.azure.com/) a felügyelt példányra.

2. Kattintson a **diagnosztikai beállítások**elemre.

3. Kattintson a **diagnosztika bekapcsolása**elemre. Ha a diagnosztika már engedélyezve van, a *+ diagnosztika hozzáadása beállítás* jelenik meg helyette.

4. A naplók listájában válassza a **SQLSecurityAuditEvents** lehetőséget.

5. Válassza ki a naplózási események (Event hub, Azure Monitor naplók vagy mindkettő) célhelyét. Konfigurálja az egyes célkitűzésekhez a szükséges paramétereket (például Log Analytics munkaterület).

6. Kattintson a **Mentés** gombra.

    ![Diagnosztikai beállítások konfigurálása](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Kapcsolódjon a felügyelt példányhoz **SQL Server Management Studio (SSMS)** vagy bármely más támogatott ügyfél használatával.

8. A következő T-SQL-utasítás végrehajtásával hozzon létre egy kiszolgáló-naplózást:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Hozzon létre egy kiszolgáló-naplózási specifikációt vagy egy adatbázis-naplózási specifikációt SQL Server:

   - [Kiszolgáló naplózási specifikációjának létrehozása T-SQL-útmutató](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Adatbázis-naplózási specifikáció létrehozása T-SQL-útmutató](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Engedélyezze a 8. lépésben létrehozott kiszolgáló-naplózást:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>] WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Naplók felhasználása

### <a name="consume-logs-stored-in-azure-storage"></a>Az Azure Storage-ban tárolt naplók felhasználása

Több módszer is használható a blob-naplózási naplók megtekintésére.

- A rendszerfunkció `sys.fn_get_audit_file` (T-SQL) segítségével táblázatos formátumban visszaküldheti a naplózási adatokat. A függvény használatáról további információt a [sys. fn_get_audit_file dokumentációjában](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)talál.

- A naplókat a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)eszközzel is megismerheti. Az Azure Storage-ban a naplózási naplók a naplófájlok tárolására megadott tárolóban lévő blob-fájlok gyűjteményében lesznek mentve. A tárolási mappa hierarchiájának, az elnevezési konvencióknak és a napló formátumának további részleteiért tekintse meg a [blob naplózási napló formátumának referenciáját](https://go.microsoft.com/fwlink/?linkid=829599).

- A naplózási naplók használatának teljes listájáért tekintse meg az [SQL Database naplózásának első lépéseivel](sql-database-auditing.md)foglalkozó témakört.

### <a name="consume-logs-stored-in-event-hub"></a>Az Event hub-ban tárolt naplók felhasználása

Az Event hub naplózási adatainak felhasználása érdekében be kell állítania egy streamet az események felhasználásához, és egy célhoz kell írnia azokat. További információ: Azure Event Hubs dokumentáció.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Azure Monitor-naplókban tárolt naplók felhasználása és elemzése

Ha a naplókat Azure Monitor naplókba írja a rendszer, azok a Log Analytics munkaterületen érhetők el, ahol a naplózási adatként speciális kereséseket futtathat. Kiindulási pontként lépjen a Log Analytics munkaterületre, és az *általános* szakaszban kattintson a *naplók* elemre, és adjon meg egy egyszerű lekérdezést, például: `search "SQLSecurityAuditEvents"` a naplók megtekintéséhez.  

A Azure Monitor naplók valós idejű üzemeltetési elemzéseket biztosítanak az integrált keresés és az egyéni irányítópultok használatával, amelyekkel a munkaterhelések és a kiszolgálók több millió rekordját is könnyedén elemezheti. További hasznos információk Azure Monitor naplók keresési nyelvéről és parancsairól: [Azure monitor naplók keresési referenciája](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>A Azure SQL Database és adatbázisaiban található adatbázisok közötti különbségek naplózása SQL Server

A Azure SQL Database és a SQL Server adatbázisaiban található adatbázisok naplózása közötti fő különbségek a következők:

- A Azure SQL Database felügyelt példányok központi telepítésének beállításával a naplózás a kiszolgáló szintjén működik, és `.xel` naplófájlokat tárol az Azure Blob Storage-ban.
- SQL Server helyszíni/virtuális gépek esetében a naplózás a kiszolgáló szintjén működik, de az eseményeket a fájlrendszer/Windows eseménynaplókban tárolja.

A felügyelt példány XEvent-naplózása támogatja az Azure Blob Storage-célokat. A fájl-és Windows-naplók **nem támogatottak**.

Az Azure Blob Storage-ba való naplózás `CREATE AUDIT` szintaxisának főbb eltérései a következők:

- A rendszer új szintaxist `TO URL` biztosít, és lehetővé teszi az Azure Blob Storage-tároló URL-címének megadását, ahol a `.xel` fájlok vannak elhelyezve.
- A rendszer új szintaxist `TO EXTERNAL MONITOR` biztosít ahhoz, hogy még a hub és a Azure Monitor naplózza a naplózási célokat.
- A `TO FILE` szintaxis **nem támogatott** , mert SQL Database nem fér hozzá a Windows-fájlmegosztás számára.
- A Leállítás beállítás **nem támogatott**.
- a 0 `queue_delay` **nem támogatott**.

## <a name="next-steps"></a>Következő lépések

- A naplózási naplók használatának teljes listájáért tekintse meg az [SQL Database naplózásának első lépéseivel](sql-database-auditing.md)foglalkozó témakört.
- A szabványok megfelelőségét támogató Azure-programokkal kapcsolatos további információkért tekintse meg a [Azure biztonsági és adatkezelési központ](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , ahol megtalálhatja a SQL Database megfelelőségi tanúsítványok legújabb listáját.

<!--Image references-->









