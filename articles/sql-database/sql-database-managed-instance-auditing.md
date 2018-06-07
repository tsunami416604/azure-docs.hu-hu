---
title: Az Azure SQL adatbázis felügyelt példány naplózása |} Microsoft Docs
description: 'Útmutató: Ismerkedés az Azure SQL Database felügyelt példány Auditing T-SQL használatával'
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: giladm
ms.openlocfilehash: 71929be456de4b798da48bb202969deb71e1c371
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648853"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Ismerkedés az Azure SQL Database felügyelt példány Auditing

[Az Azure SQL adatbázis felügyelt példány](sql-database-managed-instance.md) naplózási nyomon követi az adatbázisok események, mind az írás őket naplózási jelentkezzen be az Azure storage-fiók. A naplózás is:
- Segít törvényi megfelelőség fenntartásában, ismerje meg adatbázis-tevékenység, és azok az eltérések és rendellenességek, amelyek üzleti problémát jelenthetnek, vagy a biztonság megsértésére betekintést.
- Lehetővé teszi, és elősegíti a megfelelést a megfelelőségi követelményeket, de azt nem garantálja a megfelelőség. További információ az Azure programokat, hogy támogatási szabványoknak való megfelelés, a következő témakörben: a [Azure biztonsági és adatkezelési központ](https://azure.microsoft.com/support/trust-center/compliance/).


## <a name="set-up-auditing-for-your-server"></a>A server naplózásának beállítása

Az alábbi szakasz ismerteti a felügyelt példányán naplózás konfigurációját.
1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. Az alábbi lépéseket, hozzon létre egy Azure Storage **tároló** naplók tárolására.

   - Nyissa meg az Azure Storage, hol szeretné tárolni a naplókat.

     > [!IMPORTANT]
     > Használja a storage-fiók ugyanabban a régióban, a példány felügyelt kiszolgálóként elkerülése érdekében a kereszt-régió olvasása/írása.

   - Lépjen a tárfiókban lévő **áttekintése** kattintson **Blobok**.

     ![Navigációs ablaktábla][1]

   - Kattintson a felső menüben **+ tároló** egy új tároló létrehozásához.

     ![Navigációs ablaktábla][2]

   - Adja meg a tároló **neve**, állítsa a nyilvános hozzáférés szintet **titkos**, és kattintson a **OK**.

     ![Navigációs ablaktábla][3]

   - A tárolók listában kattintson az újonnan létrehozott tároló majd **a tároló tulajdonságainak**.

     ![Navigációs ablaktábla][4]

   - A tároló URL-Címének másolása a Másolás ikonra kattintva, és mentse az URL-CÍMÉT (például a Jegyzettömbbel) későbbi használatra. A tároló URL formátumúnak kell lennie `https://<StorageName>.blob.core.windows.net/<ContainerName>`

     ![Navigációs ablaktábla][5]

3. Az alábbi lépéseket az Azure Storage készítése **SAS-Token** biztosítanak a tárolási fiók példány naplózás által felügyelt hozzáférési jogosultsága.

   - Nyissa meg az Azure Storage-fiók, amelyben létrehozta a tárolót az előző lépésben.

   - Kattintson a **a közös hozzáférésű jogosultságkód** a tárolási beállítások menüjében.

     ![Navigációs ablaktábla][6]

   - Adja meg a biztonsági Társítások az alábbiak szerint:
     - **Engedélyezett szolgáltatások**: Blob
     - **Kezdő dátum**: időzónával kapcsolatos problémák elkerülése érdekében javasoljuk, hogy tegnapi dátum használata.
     - **Záró dátuma**: válasszon, amely a SAS-jogkivonat lejárati. 

       > [!NOTE]
       > A naplózási hibák elkerülése érdekében lejáratkor-token megújításához.

     - Kattintson az **SAS előállítása** gombra.

       ![Navigációs ablaktábla][7]

   - Miután rákattintott a SAS létre, a SAS-Token alján jelenik meg. Másolja a jogkivonatot a Másolás ikonra kattintva, és mentse azt (például a Jegyzettömbbel) későbbi használatra.

     > [!IMPORTANT]
     > Távolítsa el a kérdőjel ("?") karaktert a jogkivonat az elejétől.

     ![Navigációs ablaktábla][8]

4. A felügyelt példányát az SQL Server Management Studio (SSMS) keresztül csatlakozni.

5. Hajtsa végre a következő T-SQL utasítást a **hozzon létre egy új hitelesítő adatok** a tároló URL-cím és a SAS-Token, amelyet az előző lépésben hozott létre:

    ```SQL
    CREATE CREDENTIAL [<container_url>]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',
    SECRET = '<SAS KEY>'
    GO
    ```

6. Hajtsa végre a következő T-SQL utasítást egy új Server Audit létrehozásához (válassza ki a saját naplózási nevét, a tároló URL-címet, amelyet az előző lépésben hozott létre):

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>]
    TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
    GO
    ```

    Ha nincs megadva, `RETENTION_DAYS` alapértelmezett érték a 0 (korlátlan megőrzés).

    További információ:
    - [Naplózás példány felügyelt, az Azure SQL Database és az SQL Server közötti különbségek](#subheading-3)
    - [KISZOLGÁLÓ NAPLÓZÁSI LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
    - [AZ ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

7. Az SQL Server kiszolgáló Audit Specification vagy adatbázishoz Audit Specification létrehozása:
    - [Kiszolgáló naplózási specification T-SQL útmutató létrehozása](https://docs.microsoft.com/ sql/t-sql/statements/create-server-audit-specification-transact-sql)
    - [Adatbázis naplózási specification T-SQL útmutató létrehozása](https://docs.microsoft.com/ sql/t-sql/statements/create-database-audit-specification-transact-sql)

8. A 6. lépésben létrehozott kiszolgálón naplózás engedélyezése:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="analyze-audit-logs"></a>Naplók elemzése
Többféleképpen segítségével blob naplófájlok megtekintése.

- A rendszer funkcióval `sys.fn_get_audit_file` (T-SQL) vissza a napló adatairól táblázatos formátumban. Ez a funkció használatáról további információkért lásd: a [sys.fn_get_audit_file dokumentáció](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Az ellenőrzési napló fogyasztás módszerek teljes listáját, tekintse meg a [Ismerkedés az SQL-adatbázis naplózásának](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).

> [!IMPORTANT]
> A naplózási bejegyzések az Azure portálról ("Rekordok naplózása" ablaktáblán) megtekintéséhez módja jelenleg nem használható a felügyelt példány.

## <a name="auditing-differences-between-managed-instance-azure-sql-database-and-sql-server"></a>Naplózás példány felügyelt, az Azure SQL Database és SQL Server közötti különbségek

SQL-naplózási példány felügyelt, az Azure SQL Database és SQL Server helyszíni közötti legfőbb különbségek a következők:

- Felügyelt példányában SQL-naplózási működik. a kiszolgáló és tárolók `.xel` naplófájlok az Azure blob storage-fiók.
- Az Azure SQL-adatbázis SQL-naplózás az adatbázis szintjén működik.
- Helyszíni SQL Server / virtuális gépeket, SQL-naplózási működik a kiszolgálón szint, de tárolók események fájlok rendszer és a windows eseménynaplóit.

Az Azure blob storage tárolók XEvent naplózási felügyelt példányban támogatja. Fájl- és windows-naplók **nem támogatott**.

A kulcs közötti különbségek a `CREATE AUDIT` az Azure blob storage a naplózás szintaxis vannak:
- Egy új szintaxis `TO URL` valósul meg, és megadhatja az URL-CÍMÉT az Azure blob Storage tárolót ahol a `.xel` fájlok kerülnek.
- A szintaxis `TO FILE` van **nem támogatott** mert felügyelt példány nem fér hozzá a Windows-fájlmegosztásokon.
- Leállítás lehetőség **nem támogatott**.
- `queue_delay` a 0 van **nem támogatott**.


## <a name="next-steps"></a>További lépések

- Az ellenőrzési napló fogyasztás módszerek teljes listáját, tekintse meg a [Ismerkedés az SQL-adatbázis naplózásának](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).
- További információ az Azure programokat, hogy támogatási szabványoknak való megfelelés, a következő témakörben: a [Azure biztonsági és adatkezelési központ](https://azure.microsoft.com/support/trust-center/compliance/).


<!--Anchors-->
[Set up auditing for your server]: #subheading-1
[Analyze audit logs]: #subheading-2
[Auditing differences between Managed Instance, Azure SQL DB and SQL Server]: #subheading-3

<!--Image references-->
[1]: ./media/sql-managed-instance-auditing/1_blobs_widget.png
[2]: ./media/sql-managed-instance-auditing/2_create_container_button.png
[3]: ./media/sql-managed-instance-auditing/3_create_container_config.png
[4]: ./media/sql-managed-instance-auditing/4_container_properties_button.png
[5]: ./media/sql-managed-instance-auditing/5_container_copy_name.png
[6]: ./media/sql-managed-instance-auditing/6_storage_settings_menu.png
[7]: ./media/sql-managed-instance-auditing/7_sas_configure.png
[8]: ./media/sql-managed-instance-auditing/8_sas_copy.png
