---
title: Adatok szinkronizálása az Azure SQL Data Sync ügynök |} A Microsoft Docs
description: Ismerje meg, hogyan telepítheti és futtathatja a Data Sync-ügynök az Azure SQL Data Sync, a szinkronizálják az adatokat a helyszíni SQL Server-adatbázisok
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: adb8917605a00208b328e7fd15f96d28c7838988
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58485204"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>Az Azure SQL Data Sync adatok szinkronizációs ügynök

A helyszíni SQL Server-adatbázisok telepítésével és a Data Sync-ügynök konfigurálása az Azure SQL Data Sync-adatok szinkronizálása. Az SQL Data Sync szolgáltatással kapcsolatos további információkért lásd: [szinkronizálja az adatokat több felhőalapú és helyszíni adatbázisban az SQL Data Sync szolgáltatással](sql-database-sync-data.md).

> [!IMPORTANT]
> Az Azure SQL Data Sync does **nem** támogatja az Azure SQL Database felügyelt példánya jelenleg.

## <a name="download-and-install"></a>Letöltés és telepítés

A Data Sync-ügynök letöltéséhez nyissa meg [SQL Azure Data Sync-ügynök](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="install-silently"></a>Csendes telepítése

A Data Sync-ügynök csendes telepítése a parancssorból, írja be az alábbi példához hasonló parancsot. Ellenőrizze a fájlnevet a letöltött .msi-fájl, és adja meg a saját értékeit a **TARGETDIR** és **SERVICEACCOUNT** argumentumokat.

- Ha nem ad meg értéket **TARGETDIR**, az alapértelmezett érték `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`.

- Ha megadja `LocalSystem` értékeként **SERVICEACCOUNT**, SQL Server-hitelesítés használata a csatlakozhat a helyszíni SQL Server-ügynök konfigurálásakor.

- Ha egy tartományi felhasználói fiók vagy helyi felhasználói fiókot ad meg értéket **SERVICEACCOUNT**, is meg kell adnia a jelszót a **ServicePassword paraméter csak** argumentum. Például: `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>Adatok szinkronizálása a helyszíni SQL Server

A Data Sync-ügynök így tudja szinkronizálni az adatokat az egy vagy több helyszíni SQL Server-adatbázisok konfigurálásáról lásd: [adjon hozzá egy helyszíni SQL Server-adatbázis](sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="agent-faq"></a> Data Sync-ügynök – gyakori kérdések

### <a name="why-do-i-need-a-client-agent"></a>Miért kell egy ügyfélügynök

Az SQL Data Sync szolgáltatás SQL Server-adatbázisok az ügyfélügynök-n keresztül kommunikál. Ez a biztonsági funkció megakadályozza, hogy a tűzfal mögött található adatbázisokkal való közvetlen kommunikáció. Ha az SQL Data Sync szolgáltatás kommunikál az ügynök használatával hajtja végre titkosított kapcsolatokat és a egy egyedi token vagy *ügynökkulcs*. Az SQL Server-adatbázisok az ügynök, a kapcsolati karakterláncot, és az ügynök kulccsal hitelesítheti. Ez a kialakítás biztosít magas szintű az adatok biztonságát.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>A helyi ügynök hány példánya felhasználói felületén futtathatók

A felhasználói felület csak egy példányban futtatható.

### <a name="how-can-i-change-my-service-account"></a>Hogyan módosíthatom a szolgáltatás fiók

Miután telepít egy ügyfélügynök, szolgáltatásfiók módosítása csak úgy, távolítsa el a bővítményt, és a egy új ügyfél-ügynök telepítése az új szolgáltatásfiók a.

### <a name="how-do-i-change-my-agent-key"></a>Hogyan változtatható a saját ügynökkulcs

Egy ügynök kulcs csak egyszer használhatók fel az ügynökök által. Ha eltávolítja, majd telepítse újra az új ügynök, és nem használható több ügynök is lehet, nem használható újra. Hozzon létre egy új kulcsot egy meglévő ügynöknek kell, ha arról, hogy az ügyfél-ügynökkel és az SQL Data Sync szolgáltatással rögzített ugyanazzal a kulccsal kell lennie.

### <a name="how-do-i-retire-a-client-agent"></a>Hogyan vonja ki egy ügyfélügynök

Azonnal érvénytelenné kivonása vagy az ügynök, újragenerálja a kulcsot a portálon, de nem küldenek, az ügynök felhasználói felületén. A kulcs újragenerálása érvényteleníti a korábbi kulcs függetlenül, ha a megfelelő ügynök online vagy offline állapotú.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Hogyan helyezhetek át egy ügyfélügynök egy másik számítógépre

Ha azt szeretné, a szolgáltatás jelenleg egy másik számítógépről futnak, a helyi ügynök, tegye a következőket:

1. Telepítse az ügynököt a kívánt számítógépen.
2. Jelentkezzen be az SQL Data Sync portálra, és a egy ügynököt az új ügynök újragenerálása.
3. Az új ügynök felhasználói felület használatával küldje el az új ügynök kulcsot.
4. Várjon, amíg az ügyfélügynök letölti a helyszíni adatbázisok korábban regisztrált listáját.
5. Adja meg az adatbázis hitelesítő adatait az összes adatbázis megjelenítő szerint nem érhető el. Ezeket az adatbázisokat az új számítógép, amelyen az ügynök telepítve van a elérhetőnek kell lennie.

## <a name="agent-tshoot"></a> Hibaelhárítás a Sync-ügynök

- [Az ügyfél-ügynök telepítése, eltávolítása vagy javítása sikertelen](#agent-install)

- [Az ügyfélügynök megszakítom az Eltávolítás után nem működik.](#agent-uninstall)

- [Az adatbázis nem szerepel a listán az ügynök listában](#agent-list)

- [Ügyfélügynök nem indul el (Hiba 1069)](#agent-start)

- [Az ügynök kulcs nem küldhető be](#agent-key)

- [Az ügyfélügynök nem törölhető a portálról, ha annak társított helyszíni adatbázis nem érhető el](#agent-delete)

- [Szinkronizációs ügynök helyi alkalmazás nem tud kapcsolódni a helyi szinkronizálási szolgáltatás](#agent-connect)

### <a name="agent-install"></a> Az ügyfél-ügynök telepítése, eltávolítása vagy javítása sikertelen

- **OK**. Számos forgatókönyv ezt a hibát okozhatja. Ez a hiba az adott hiba okának megállapításához tekintse meg a naplókat.

- **Feloldási**. Keresse meg a konkrét okát, hozzon létre, és tekintse meg a Windows-telepítési naplókat. Bekapcsolhatja a naplózást, a parancssorba. Például, ha a letöltött telepítési fájlt `SQLDataSyncAgent-2.0-x86-ENU.msi`, hozzon létre, és vizsgálja meg a naplófájlokat a következő parancssorok használatával:

  - Telepítések: `msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - Az eltávolítása: `msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    Windows Installer által végzett összes telepítés naplózását is bekapcsolhatja. A Microsoft Tudásbázis megfelelő cikkében [Windows Installer-naplózás engedélyezése](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) kapcsolja be a naplózást, a Windows Installer egykattintásos megoldást kínál. A naplók helye is tartalmazza.

### <a name="agent-uninstall"></a> Az ügyfélügynök megszakítom az Eltávolítás után nem működik.

Az ügyfélügynök nem működik, akár az eltávolítási megszüntetése után.

- **OK**. Ennek oka az, hogy az SQL Data Sync ügyfélügynök nem tárolja a hitelesítő adatokat.

- **Feloldási**. E két megoldásokkal próbálkozhat:

    -   Írja be újra a hitelesítő adatokat, az ügyfél ügynökének a services.msc használatával.
    -   Távolítsa el az ügyfél ügynökprogramjának, és a egy új telepítését. Töltse le és telepítse a legújabb ügyfél ügynököt [letöltőközpontból](https://go.microsoft.com/fwlink/?linkid=221479).

### <a name="agent-list"></a> Az adatbázis nem szerepel a listán az ügynök listában

Meglévő SQL Server-adatbázis hozzáadása a szinkronizálási csoport kísérli meg, amikor nem ügynökök listája jelenik meg az adatbázis.

Ezekben az esetekben előfordulhat, hogy a probléma:

- **OK**. Az ügyfél-ügynök és a szinkronizálási csoport különböző adatközpontokban találhatók.

- **Feloldási**. Az ügyfél-ügynök és a szinkronizálási csoport ugyanabban az adatközpontban kell lennie. Beállítására, két lehetősége van:

    -   Hozzon létre egy új ügynököt az adatközpont, ahol a szinkronizálási csoport is található. Az ügynök ezután regisztrálja az adatbázisban.
    -   A jelenlegi szinkronizálási csoport törlése. Majd hozza létre a szinkronizálási csoport az adatközpont, ahol az ügynök nem található.

- **OK**. Az ügyfélügynök-adatbázisok listája nem aktuális.

- **Feloldási**. Állítsa le és indítsa újra az ügyfélügynök-szolgáltatás.

    A helyi ügynök letölti az ügynökkulcs első benyújtása csak a társított adatbázisok listája. Nem sikerül letölteni a későbbi ügynök kulcs beküldés a társított adatbázisok listája. Adatbázisok, amelyek regisztrálva vannak az ügynök áthelyezés ideje alatt nem jelenik meg az eredeti példány.

### <a name="agent-start"></a> Ügyfélügynök nem indul el (Hiba 1069)

Tudomására jut, hogy az ügynök nem fut SQL Servert futtató számítógépre. Amikor megpróbálja manuálisan elindítani az ügynököt, megjelenik egy párbeszédpanel, amely a következő üzenetet jeleníti meg "1069. hiba: A szolgáltatás nem indult el bejelentkezési hiba miatt."

![Szinkronizálási hiba 1069 párbeszédpanel](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **OK**. Egy valószínűleg ez a hiba oka, hogy a jelszót a helyi kiszolgálón módosult-e, mivel az ügynök és az ügynök jelszót hozott létre.

- **Feloldási**. Az ügynök jelszó frissítése a kiszolgáló jelenlegi jelszavát:

  1. Keresse meg az SQL Data Sync ügyfélügynök-szolgáltatás.  
    a. Válassza ki **Start**.  
    b. A Keresés mezőbe írja be a **services.msc**.  
    c. A keresési eredmények között, válassza ki a **szolgáltatások**.  
    d. Az a **szolgáltatások** ablakban görgessen a bejegyzés **SQL Data Sync-ügynök**.  
  1. Kattintson a jobb gombbal **SQL Data Sync-ügynök**, majd válassza ki **leállítása**.
  1. Kattintson a jobb gombbal **SQL Data Sync-ügynök**, majd válassza ki **tulajdonságok**.
  1. A **SQL Data Sync ügynök tulajdonságainak**, jelölje be a **bejelentkezés** fülre.
  1. Az a **jelszó** mezőbe írja be a jelszót.
  1. Az a **jelszó megerősítése** mezőbe írja be újból a jelszót.
  1. Válassza az **Apply** (Alkalmaz) lehetőséget, majd kattintson az **OK** gombra.
  1. Az a **szolgáltatások** ablakban kattintson a jobb gombbal a **SQL Data Sync-ügynök** szolgáltatásra, és kattintson a **Start**.
  1. Zárja be a **szolgáltatások** ablak.

### <a name="agent-key"></a> Az ügynök kulcs nem küldhető be

Miután hoz létre, vagy hozza létre a kulcsot egy ügynöknek, küldje el a kulcs az SqlAzureDataSyncAgent alkalmazáson keresztül próbál. A küldés nem lehetett végrehajtani.

![Hiba-párbeszédpanel szinkronizálása – nem küldhetnek ügynökkulcs](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Előfeltételek**. Mielőtt folytatná, ellenőrizze a következő előfeltételek vonatkoznak:

  - Az SQL Data Sync Windows-szolgáltatás fut.

  - A szolgáltatás az SQL Data Sync Windows-szolgáltatásfiókot hálózati hozzáféréssel rendelkezik.

  - A kimenő 1433-as port meg nyitva, a helyi tűzfalszabály.

  - A helyi IP-cím hozzáadódik a kiszolgáló vagy adatbázis tűzfalszabályt a szinkronizálási metaadat-adatbázisként.

- **OK**. Az ügynökkulcs egyedileg azonosítja az egyes helyi ügynök. A kulcs két feltételeknek kell megfelelniük:

  -   Az SQL Data Sync-kiszolgáló és a helyi számítógépen az ügynök ügyfélkulcsot azonosnak kell lennie.
  -   Az ügyfél ügynökkulcs csak egyszer használható.

- **Feloldási**. Az ügynök nem működik, ha, mert az egyik vagy mindkét ezek a feltételek nem teljesülnek. Az ügynök újra működéséhez lekérése:

  1. Hozzon létre egy új kulcsot.
  1. Az ügynök az új kulcs érvényesek.

  A alkalmazni az ügynököt az új kulcs:

  1. A Fájlkezelőben nyissa meg az ügynöktelepítési könyvtár. Az alapértelmezett telepítési könyvtárra a C:\\Program Files (x86)\\Microsoft SQL Data Sync szolgáltatással.
  1. Kattintson duplán a bin alkönyvtárat.
  1. Open the SqlAzureDataSyncAgent application.
  1. Válassza ki **Ügynökkulcs elküldése**.
  1. A rendelkezésre álló illessze be a vágólapra másolt kulcsot.
  1. Kattintson az **OK** gombra.
  1. Zárja be a program.

### <a name="agent-delete"></a> Az ügyfélügynök nem törölhető a portálról, ha annak társított helyszíni adatbázis nem érhető el

Ha a helyi végpont (azaz egy adatbázis), amely regisztrálva van egy SQL Data Sync ügyfélügynök elérhetetlenné válik, az ügyfél ügynöke nem lehet törölni.

- **OK**. A helyi ügynök nem törölhető, mert az nem érhető el adatbázis továbbra is regisztrálva van az ügynök. Ha törli az ügynököt próbál, a törlési folyamat megpróbálja elérni az adatbázist, amelyet nem sikerül.

- **Feloldási**. Használja a "kényszerítéséhez delete" törli nem érhető el az adatbázist.

> [!NOTE]
> Ha a szinkronizálási metaadat-táblát egy "kényszerítéséhez delete" után továbbra is használható `deprovisioningutil.exe` karbantartása őket.

### <a name="agent-connect"></a> Szinkronizációs ügynök helyi alkalmazás nem tud kapcsolódni a helyi szinkronizálási szolgáltatás

- **Feloldási**. Próbálja meg a következőket:

  1. Lépjen ki az alkalmazást.  
  1. Az összetevő-szolgáltatások Panel megnyitásához.  
    a. A tálcán a Keresés mezőbe írja be **services.msc**.  
    b. A keresési eredmények között kattintson duplán a **szolgáltatások**.  
  1. Állítsa le a **SQL Data Sync** szolgáltatás.
  1. Indítsa újra a **SQL Data Sync** szolgáltatás.  
  1. újra meg kell nyitniuk az alkalmazást;

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>A parancssorból futtassa az adatok szinkronizációs ügynök

A következő Data Sync-ügynök parancsok futtathatja a parancssorból:

### <a name="ping-the-service"></a>Pingelje a szolgáltatás

#### <a name="usage"></a>Használat

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>Példa

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>Regisztrált adatbázisok megjelenítése

#### <a name="usage"></a>Használat

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>Példa

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>Az ügynökkulcs beküldése

#### <a name="usage"></a>Használat

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>Példa

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>Egy adatbázis regisztrálása

#### <a name="usage"></a>Használat

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Példák

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>Egy adatbázis regisztrációjának törlése

Ez a parancs használatakor egy adatbázis regisztrációjának törlése, deprovisions az adatbázis teljes egészében. Ha az adatbázis többi szinkronizálási csoport tagja, ez a művelet megszakítja a szinkronizálási csoportok.

#### <a name="usage"></a>Használat

```cmd
SqlDataSyncAgentCommand.exe -action unregisterdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]
```

#### <a name="example"></a>Példa

```cmd
SqlDataSyncAgentCommand.exe -action "unregisterdatabase" -serverName localhost -databaseName testdb
```

### <a name="update-credentials"></a>Hitelesítő adatok frissítése

#### <a name="usage"></a>Használat

```cmd
SqlDataSyncAgentCommand.exe -action updatecredential -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Példák

```cmd
SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication windows -encryption true
```

## <a name="next-steps"></a>További lépések

Az SQL Data Sync szolgáltatással kapcsolatos további információkért tekintse meg a következő cikkeket:

-   Áttekintés – [szinkronizálja az adatokat több felhőalapú és helyszíni adatbázis között az Azure SQL Data Sync szolgáltatással](sql-database-sync-data.md)
-   Data Sync beállítása
    - A portálban – [oktatóanyag: A helyszíni adatokat az Azure SQL Database és SQL Server között, az SQL Data Sync beállítása](sql-database-get-started-sql-data-sync.md)
    - A PowerShell-lel
        -  [A PowerShell használata több Azure SQL Database-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [A PowerShell használata egy Azure-beli SQL Database-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Ajánlott eljárások – [ajánlott eljárások az Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   A figyelő - [SQL Data Sync monitorozása az Azure Monitor-naplók](sql-database-sync-monitor-oms.md)
-   Hibaelhárítás – [az Azure SQL Data Sync szolgáltatással kapcsolatos problémák elhárítása](sql-database-troubleshoot-data-sync.md)
-   Szinkronizálási sémájának frissítéséhez
    -   A Transact-SQL - [az Azure SQL Data Sync sémamódosítások-replikáció automatizálása](sql-database-update-sync-schema.md)
    -   PowerShell-lel – [használja a Powershellt, a meglévő szinkronizálási csoport szinkronizálási sémájának frissítéséhez](scripts/sql-database-sync-update-schema.md)
