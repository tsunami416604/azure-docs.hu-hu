---
title: Felügyelt példány naplózása
description: Ismerje meg, hogyan kezdheti el az Azure SQL Database felügyelt példánynaplózását a T-SQL használatával
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
ms.date: 03/27/2020
ms.openlocfilehash: 405ac27fad3c24d3064f11476f452ad00abb9b02
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387767"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Ismerkedés a felügyelt Azure SQL Database-példányok naplózásával

[A felügyelt példány](sql-database-managed-instance.md) naplózása nyomon követi az adatbázis-eseményeket, és az Azure storage-fiókjában naplóba írja őket. A naplózás további előnyei:

- Segít a jogszabályi megfelelőség fenntartásában és az adatbázison végzett tevékenység megértésében, valamint az esetleg üzleti veszélyeket vagy biztonsági problémákat jelző rendellenességek feltárásában.
- Lehetővé teszi és megkönnyíti a megfelelőségi szabványok betartását, bár nem mindig garantálja a megfelelőséget. A szabványoknak való megfelelést támogató Azure-programokról az [Azure Adatvédelmi központban](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) talál, ahol az SQL Database megfelelőségi tanúsítványainak legfrissebb listáját találja.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>A kiszolgáló Azure-tárolóba való naplózásának beállítása

A következő szakasz ismerteti a felügyelt példány naplózásának konfigurációját.

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
2. Hozzon létre egy Azure **Storage-tárolót,** ahol a naplónaplók tárolása.

   1. Keresse meg az Azure Storage-t, ahol a naplónaplókat szeretné tárolni.

      > [!IMPORTANT]
      > Használjon egy tárfiókot ugyanabban a régióban, mint a felügyelt példány régióközi olvasási/írási elkerülése érdekében.

   1. A tárfiókban nyissa **meg az Áttekintés című témakört,** és kattintson a **Blobok**elemre.

      ![Az Azure Blob widget](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. Új tároló létrehozásához kattintson a **+ Tároló** gombra a felső menüben.

      ![Blob-tároló létrehozása ikon](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Adja meg a tároló **nevét**, állítsa a nyilvános hozzáférési szintet **privátra,** majd kattintson **az OK**gombra.

      ![Blob-tároló konfigurációjának létrehozása](./media/sql-managed-instance-auditing/3_create_container_config.png)
  > [!IMPORTANT]
  > Azoknak az ügyfeleknek, akik kiszolgáló- vagy adatbázisszintű naplózási eseményeikhez nem módosítható naplót kívánnak konfigurálni, követniük kell az [Azure Storage utasításait](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) (győződjön meg arról, hogy a nem módosítható blobtároló konfigurálásakor **a további hozzáfűzések engedélyezése** lehetőséget választotta)
  
3. A naplónaplók tárolójának létrehozása után kétféleképpen konfigurálhatja a naplók célként: a [T-SQL](#blobtsql) vagy [az SQL Server Management Studio (SSMS) felhasználói felületének használatával:](#blobssms)

   - <a id="blobtsql"></a>Blog-tárterület konfigurálása a naplók t-SQL használatával:

     1. A tárolók listájában kattintson az újonnan létrehozott tárolóra, majd a **Tároló tulajdonságai parancsra.**

        ![Blob tároló tulajdonságai gomb](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Másolja a tároló URL-címét a másolásikonra kattintva, és mentse az URL-címet (például a Jegyzettömbben) későbbi használatra. A tároló URL-formátumát`https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![Blob-tároló url-címe](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Hozzon létre egy Azure Storage **SAS-jogkivonatot,** amely nek biztosítania kell a felügyelt példány naplózási hozzáférési jogait a tárfiókhoz:

        - Keresse meg azt az Azure Storage-fiókot, ahol az előző lépésben létrehozta a tárolót.

        - Kattintson a **Tárolóbeállítások** menü Megosztott hozzáférés aláírása parancsára.

          ![Megosztott hozzáférésű aláírás ikonja a tárhelybeállítások menüben](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Állítsa be a SAS-t az alábbiak szerint:

          - **Engedélyezett szolgáltatások**: Blob

          - **Kezdő dátum**: Az időzónával kapcsolatos problémák elkerülése érdekében ajánlott a tegnapi dátumot használni

          - **Záró dátum**: válassza ki azt a dátumot, amikor ez a SAS-token lejár

            > [!NOTE]
            > A rendszerkivonat megújítása a sikertelenség elkerülése érdekében.

          - Kattintson az **SAS előállítása** gombra.
            
            ![SAS-konfiguráció](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - A SAS létrehozása gombra kattintva a SAS-token alul jelenik meg. Másolja a tokent a másolás ikonra kattintva, és mentse el (például a Jegyzettömbben) későbbi használatra.

          ![SAS-token másolása](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Távolítsa el a kérdőjel ("?") karaktert a token elejéről.

     1. Csatlakozzon a felügyelt példányhoz az SQL Server Management Studio (SSMS) vagy bármely más támogatott eszköz segítségével.

     1. A következő T-SQL utasítás végrehajtásával **hozzon létre egy új hitelesítő adatokat** az előző lépésekben létrehozott tároló URL-címés SAS-token használatával:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Hajtsa végre a következő T-SQL utasítást egy új kiszolgálónaplózás létrehozásához (válassza ki a saját naplózási nevét, használja az előző lépésekben létrehozott tároló URL-címét). Ha nincs megadva, `RETENTION_DAYS` az alapértelmezett érték 0 (korlátlan megőrzés):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. Folytatás [a kiszolgálónaplózási specifikáció vagy az adatbázis-naplózási specifikáció létrehozásával](#createspec)

   - <a id="blobssms"></a>Blob-tároló konfigurálása a naplók naplózásához az SQL Server Management Studio (SSMS) 18 (előzetes verzió) használatával:

     1. Csatlakozzon a felügyelt példányhoz az SQL Server Management Studio (SSMS) felhasználói felületével.

     1. Bontsa ki az Objektumkezelő gyökérjegyzetét.

     1. Bontsa ki a **biztonsági** **csomópontot,** kattintson a jobb gombbal a Naplózás csomópontra, és kattintson az "Új naplózás" gombra:

        ![A biztonság és a naplózási csomópont bővítése](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Győződjön meg arról, hogy az "URL" van kiválasztva a **Naplózás cél,** és kattintson a **Tallózás:**

        ![Az Azure Storage böngészése](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. (Nem kötelező) Jelentkezzen be Azure-fiókjába:

        ![Bejelentkezés az Azure-ba](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. Válasszon ki egy előfizetést, a tárfiókot és a Blob-tárolót a legördülő menüből, vagy hozzon létre saját tárolót a **Create**gombra kattintva. Miután befejezte az **OK**gombot:

        ![Válassza ki az Azure-előfizetést, a tárfiókot és a blobtárolót](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Kattintson az **OK gombra** a "Naplózás létrehozása" párbeszédpanelen.

4. <a id="createspec"></a>Miután konfigurálta a Blob-tárolót a naplók célként, hozzon létre és engedélyezze a kiszolgálónaplózási specifikációt vagy az adatbázis-naplózási specifikációt, ahogy az SQL Server esetében tenné:

   - [Kiszolgálónaplózási specifikáció létrehozása T-SQL útmutató](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Adatbázis-ellenőrzési specifikáció létrehozása T-SQL útmutató](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. A 3.

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

További információ:

- [Az egyes adatbázisok, rugalmas készletek és felügyelt példányok közötti különbségek naplózása az Azure SQL Database-ben és az SQL Server-adatbázisokban](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [KISZOLGÁLÓNAPLÓZÁS LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER KISZOLGÁLÓ NAPLÓZÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>A kiszolgáló naplózásának beállítása az Event Hub vagy az Azure Monitor naplóiba

A felügyelt példány naplói az Even Hubs vagy az Azure Monitor-naplókba küldhetők. Ez a szakasz a következők konfigurálását ismerteti:

1. Navigáljon az [Azure Portalon](https://portal.azure.com/) a felügyelt példányhoz.

2. Kattintson a **Diagnosztikai beállítások gombra.**

3. Kattintson **a Diagnosztika bekapcsolása gombra.** Ha a diagnosztika már engedélyezve van, a *+Add diagnosztikai beállítás* jelenik meg helyette.

4. Válassza ki az **SQLSecurityAuditEvents** elemet a naplók listájában.

5. Válassza ki a cél a naplózási események – Event Hub, Az Azure Monitor naplók, vagy mindkettő. Minden célhoz konfigurálja a szükséges paramétereket (pl. Log Analytics munkaterület).

6. Kattintson a **Mentés** gombra.

    ![Diagnosztikai beállítások konfigurálása](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Csatlakozzon a felügyelt példányhoz az **SQL Server Management Studio (SSMS)** vagy bármely más támogatott ügyfél használatával.

8. A kiszolgáló naplózásának létrehozásához hajtsa végre a következő T-SQL utasítást:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Az SQL Server kiszolgálóhoz megfelelően hozzon létre és engedélyezze a kiszolgálónaplózási vagy adatbázis-naplózási specifikációt:

   - [Kiszolgálónaplózási specifikáció létrehozása T-SQL útmutató](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Adatbázis-ellenőrzési specifikáció létrehozása T-SQL útmutató](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. A 8.
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Naplónaplók felhasználása

### <a name="consume-logs-stored-in-azure-storage"></a>Az Azure Storage-ban tárolt naplók felhasználása

Számos módszer segítségével megtekintheti a blob naplózási naplók.

- A rendszerfüggvény `sys.fn_get_audit_file` (T-SQL) segítségével a naplóadatokat táblázatos formátumban adja vissza. A funkció használatáról további információt a [sys.fn_get_audit_file dokumentációjában](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)talál.

- A naplónaplókat egy olyan eszköz, például az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)segítségével fedezheti fel. Az Azure storage-ban a naplózási naplók blobfájlok gyűjteményeként kerülnek mentésre egy tárolóban, amely a naplónaplók tárolására lett definiálva. A tárolómappa hierarchiájáról, az elnevezési konvenciókról és a naplóformátumról a [Blob naplófájl formátumának hivatkozási száma című témakörben talál további részleteket.](https://go.microsoft.com/fwlink/?linkid=829599)

- A naplófelhasználási módszerek teljes listáját az [SQL-adatbázis naplózásának első lépései](sql-database-auditing.md)című dokumentum tartalmazza.

### <a name="consume-logs-stored-in-event-hub"></a>Az Event Hubban tárolt naplók felhasználása

A naplózási naplók adatainak felhasználásához az Event Hub, be kell állítania egy adatfolyamot az események felhasználásához, és írja azokat a cél. További információ: Azure Event Hubs documentation.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Az Azure Monitor-naplókban tárolt naplók felhasználása és elemzése

Ha a naplónaplók az Azure Monitor naplóiba vannak írva, azok a Log Analytics-munkaterületen érhetők el, ahol speciális kereséseket futtathat a naplózási adatokon. Kiindulási pontként keresse meg a Log Analytics munkaterületet, és az *Általános* szakaszban `search "SQLSecurityAuditEvents"` kattintson a *Naplók* elemre, és adjon meg egy egyszerű lekérdezést, például: a naplónaplók megtekintéséhez.  

Az Azure Monitor naplói valós idejű operatív elemzéseket nyújtanak az integrált keresési és egyéni irányítópultok használatával, amelyek segítségével több millió rekordot elemezhet az összes számítási feladatában és kiszolgálóján. Az Azure Monitor naplóinak keresési nyelvéről és parancsairól az [Azure Monitor naplóinak keresési hivatkozásában talál](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)további hasznos információt.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Az Azure SQL Database és az SQL Server adatbázisai közötti különbségek naplózása

Az Azure SQL Database és az SQL Server adatbázisainak naplózása közötti legfontosabb különbségek a következők:

- A felügyelt példány üzembe helyezését lehetőség az Azure SQL Database, naplózás működik a kiszolgáló szintjén, és tárolja a naplófájlokat az Azure Blob storage.With the managed instance deployment option in Azure SQL Database, auditing works at the server level and stores `.xel` log files in Azure Blob storage.
- A helyszíni SQL Serverben / virtuális gépeken a naplózás kiszolgálói szinten működik, de fájlokon/windows-eseménynaplókban tárolja az eseményeket.

XEvent naplózás a felügyelt példány támogatja az Azure Blob storage-célokat. A fájl- és windows-naplók **nem támogatottak.**

Az Azure Blob `CREATE AUDIT` storage naplózásának főbb különbségei a következők:

- Egy új `TO URL` szintaxis érhető el, és lehetővé teszi, `.xel` hogy adja meg az Azure blob Storage-tároló URL-címét, ahol a fájlok kerülnek.
- Új szintaxis `TO EXTERNAL MONITOR` érhető el az Even Hub és az Azure Monitor naplóinak engedélyezéséhez.
- A szintaxis nem `TO FILE` **támogatott,** mert az SQL Database nem tud hozzáférni a Windows fájlmegosztásokhoz.
- A leállítási beállítás **nem támogatott.**
- `queue_delay`a 0 **nem támogatott**.

## <a name="next-steps"></a>További lépések

- A naplófelhasználási módszerek teljes listáját az [SQL-adatbázis naplózásának első lépései](sql-database-auditing.md)című dokumentum tartalmazza.
- A szabványoknak való megfelelést támogató Azure-programokról az [Azure Adatvédelmi központban](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) talál, ahol az SQL Database megfelelőségi tanúsítványainak legfrissebb listáját találja.

<!--Image references-->









