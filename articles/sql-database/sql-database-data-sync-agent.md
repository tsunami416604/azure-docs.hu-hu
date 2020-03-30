---
title: Adatszinkronizáló ügynök az SQL-adatszinkronizáláshoz
description: Megtudhatja, hogy miként telepítheti és futtathatja az Azure SQL Data Sync adatszinkronizálási ügynökét az adatok helyszíni SQL Server-adatbázisokkal való szinkronizálásához
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 6d0a728401ac9f0156cc8fa913ce486bb577c6dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825177"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>Adatszinkronizáló ügynök az Azure SQL Data Sync-hez

Szinkronizálja az adatokat a helyszíni SQL Server-adatbázisokkal az Azure SQL Data Sync adatszinkronizálási ügynökének telepítésével és konfigurálásával. Az SQL Data Sync szolgáltatásról további információt az [Adatok szinkronizálása több felhőbeli és helyszíni adatbázisban](sql-database-sync-data.md)az SQL Data Sync segítségével című témakörben talál.

> [!IMPORTANT]
> Az Azure SQL Data Sync jelenleg **nem** támogatja az Azure SQL Database felügyelt példánya.

## <a name="download-and-install"></a>Letöltés és telepítés

Az adatszinkronizálási ügynök letöltéséhez nyissa meg az [SQL Azure Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693)című.

### <a name="install-silently"></a>Telepítése csendben

Ha a parancssorból csendben szeretné telepíteni az adatszinkronizálási ügynököt, írjon be egy, az alábbi példához hasonló parancsot. Ellenőrizze a letöltött .msi fájl fájlnevét, és adja meg saját értékeit a **TARGETDIR** és **SERVICEACCOUNT** argumentumoknak.

- Ha nem ad meg értéket a **TARGETDIR** `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`értékhez, az alapértelmezett érték a .

- Ha a `LocalSystem` **SERVICEACCOUNT**értékét adja meg, használja az SQL Server hitelesítést, amikor konfigurálja az ügynököt a helyszíni SQL Server kiszolgálóhoz való csatlakozásra.

- Ha a **SERVICEACCOUNT**értékeként tartományi felhasználói fiókot vagy helyi felhasználói fiókot ad meg, akkor a jelszót a **SERVICEPASSWORD** argumentummal is meg kell adnia. Például: `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>Adatok szinkronizálása a helyszíni SQL Server kiszolgálóval

Az adatszinkronizálási ügynök konfigurálása egy vagy több helyszíni SQL Server-adatbázissal való szinkronizáláshoz olvassa el [a Helyszíni SQL Server-adatbázis hozzáadása című témakört.](sql-database-get-started-sql-data-sync.md#add-on-prem)

## <a name="data-sync-agent-faq"></a><a name="agent-faq"></a>Adatszinkronizálási ügynök – gyakori kérdések

### <a name="why-do-i-need-a-client-agent"></a>Miért van szükségem ügyfélügynökre?

Az SQL Data Sync szolgáltatás az ügyfélügynökön keresztül kommunikál az SQL Server adatbázisaival. Ez a biztonsági szolgáltatás megakadályozza a tűzfal mögötti adatbázisokkal való közvetlen kommunikációt. Amikor az SQL Data Sync szolgáltatás kommunikál az ügynökkel, ezt titkosított kapcsolatok és egy egyedi jogkivonat vagy *ügynökkulcs*használatával teszi. Az SQL Server-adatbázisok a kapcsolati karakterlánc és az ügynökkulcs használatával hitelesítik az ügynököt. Ez a kialakítás magas szintű biztonságot nyújt az adatok számára.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>A helyi ügynök felhasználói felületének hány példánya futtatható

A felhasználói felületnek csak egy példánya futtatható.

### <a name="how-can-i-change-my-service-account"></a>Hogyan változtathatom meg a szolgáltatásfiókomat?

Az ügyfélügynök telepítése után a szolgáltatásfiók módosításának egyetlen módja az eltávolítás és az új szolgáltatásfiókkal rendelkező új ügyfélügynök telepítése.

### <a name="how-do-i-change-my-agent-key"></a>Hogyan változtathatom meg az ügynökkulcsomat?

Az ügynökkulcsot csak egyszer használhatja egy ügynök. Nem használható fel újra, ha eltávolít, majd újratelepít egy új ügynököt, és több ügynök sem használhatja. Ha új kulcsot kell létrehoznia egy meglévő ügynökhöz, biztosnak kell lennie abban, hogy ugyanazt a kulcsot rögzíti az ügyfélügynök és az SQL Data Sync szolgáltatás.

### <a name="how-do-i-retire-a-client-agent"></a>Hogyan vonhatok ki egy ügyfélügynököt?

Az ügynök azonnali érvénytelenítéséhez vagy kivonásához hozza létre újra a kulcsot a portálon, de ne küldje el az ügynök felhasználói felületén. A kulcs újragenerálása érvényteleníti az előző kulcsot, függetlenül attól, hogy a megfelelő ügynök online vagy offline állapotban van-e.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Hogyan helyezhetek át ügyfélügynököt egy másik számítógépre?

Ha a helyi ügynököt a jelenlegitől eltérő számítógépről szeretné futtatni, tegye a következőket:

1. Telepítse az ügynököt a kívánt számítógépre.
2. Jelentkezzen be az SQL Data Sync portálra, és hozza létre újra az új ügynök ügynökkulcsát.
3. Az új ügynök felhasználói felületének használatával küldje el az új ügynökkulcsot.
4. Várjon, amíg az ügyfélügynök letölti a korábban regisztrált helyszíni adatbázisok listáját.
5. Adja meg az adatbázis hitelesítő adatait az összes olyan adatbázishoz, amely elérhetetlenként jelenik meg. Ezeknek az adatbázisoknak elérhetőnek kell lenniük abból az új számítógépből, amelyre az ügynök telepítve van.

## <a name="troubleshoot-data-sync-agent-issues"></a><a name="agent-tshoot"></a>Adatszinkronizálási ügynökkel kapcsolatos problémák elhárítása

- [Az ügyfélügynök telepítése, eltávolítása vagy javítása sikertelen](#agent-install)

- [Az ügyfélügynök nem működik az eltávolítás megszakítása után](#agent-uninstall)

- [Az adatbázisom nem szerepel az ügyintézők listájában](#agent-list)

- [Az ügyfélügynök nem indul el (1069-es hiba)](#agent-start)

- [Nem tudom beküldeni az ügynökkulcsot.](#agent-key)

- [Az ügyfélügynök nem törölhető a portálról, ha a társított helyszíni adatbázis nem érhető el](#agent-delete)

- [A helyi szinkronizálási ügynök alkalmazás nem tud csatlakozni a helyi szinkronizálási szolgáltatáshoz](#agent-connect)

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a><a name="agent-install"></a>Az ügyfélügynök telepítése, eltávolítása vagy javítása sikertelen

- **Oka**. Számos forgatókönyv okozhatja ezt a hibát. A hiba konkrét okának meghatározásához tekintse meg a naplókat.

- **felbontás .** A hiba konkrét okának megkereséséhez hozza létre és tekintse meg a Windows Installer naplóit. A naplózást a parancssorban kapcsolhatja be. Ha például a letöltött `SQLDataSyncAgent-2.0-x86-ENU.msi`telepítési fájl, naplófájlokat hozhat létre és vizsgálhat meg a következő parancssorokkal:

  - Telepítések esetén:`msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - Eltávolításesetén:`msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    A Windows Installer által végrehajtott összes telepítésnél is bekapcsolhatja a naplózást. A Microsoft Tudásbázis [cikke: A Windows Installer naplózásának engedélyezése](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) egykattintásos megoldást kínál a Windows Installer naplózásának bekapcsolására. Azt is előírja a helyét a naplókat.

### <a name="the-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a><a name="agent-uninstall"></a>Az ügyfélügynök nem működik az eltávolítás megszakítása után

Az ügyfélügynök nem működik, még az eltávolítás megszakítása után sem.

- **Oka**. Ennek az az oka, hogy az SQL Data Sync ügyfélügynök nem tárolja a hitelesítő adatokat.

- **felbontás .** A következő két megoldás közül választhat:

    -   A services.msc segítségével adja meg újra az ügyfélügynök hitelesítő adatait.
    -   Távolítsa el ezt az ügyfélügynököt, majd telepítsen egy újat. Töltse le és telepítse a legújabb ügyfélügynököt a [letöltőközpontból.](https://www.microsoft.com/download/details.aspx?id=27693)

### <a name="my-database-isnt-listed-in-the-agent-list"></a><a name="agent-list"></a>Az adatbázisom nem szerepel az ügyintézők listájában

Amikor egy meglévő SQL Server-adatbázist próbál hozzáadni egy szinkronizálási csoporthoz, az adatbázis nem jelenik meg az ügynökök listájában.

Ezek a forgatókönyvek okozhatják a problémát:

- **Oka**. Az ügyfélügynök és a szinkronizálási csoport különböző adatközpontokban található.

- **felbontás .** Az ügyfélügynöknek és a szinkronizálási csoportnak ugyanabban az adatközpontban kell lennie. Ennek beállításához két lehetősége van:

    -   Hozzon létre egy új ügynököt abban az adatközpontban, ahol a szinkronizálási csoport található. Ezután regisztrálja az adatbázist az ügynöknél.
    -   Az aktuális szinkronizálási csoport törlése. Ezután hozza létre újra a szinkronizálási csoportot abban az adatközpontban, ahol az ügynök található.

- **Oka**. Az ügyfélügynök adatbázislistája nem aktuális.

- **felbontás .** Állítsa le, majd indítsa újra az ügyfélügynök-szolgáltatást.

    A helyi ügynök csak az ügynökkulcs első elküldésével tölti le a társított adatbázisok listáját. Nem tölti le a kapcsolódó adatbázisok listáját a további ügynökkulcs-beküldéseken. Az ügynökáthelyezés során regisztrált adatbázisok nem jelennek meg az eredeti ügynökpéldányban.

### <a name="client-agent-doesnt-start-error-1069"></a><a name="agent-start"></a>Az ügyfélügynök nem indul el (1069-es hiba)

A rendszer felderíti, hogy az ügynök nem sql server t futtató számítógépen fut. Amikor megpróbálja manuálisan elindítani az ügynököt, megjelenik egy párbeszédpanel, amely a következő üzenetet jeleníti meg: "Hiba 1069: A szolgáltatás bejelentkezési hiba miatt nem indult el."

![Az Adatszinkronizálási hiba 1069 párbeszédpanel](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Oka**. A hiba valószínűleg az okozza, hogy a helyi kiszolgálón lévő jelszó megváltozott az ügynök és az ügynök jelszavának létrehozása óta.

- **felbontás .** Frissítse az ügynök jelszavát a kiszolgáló aktuális jelszavára:

  1. Keresse meg az SQL Data Sync ügyfélügynök-szolgáltatást.  
    a. Válassza az **Indítás** elemet.  
    b. A keresőmezőbe írja be a **services.msc**.  
    c. A keresési eredmények között válassza a **Szolgáltatások**lehetőséget.  
    d. A **Szolgáltatások** ablakban görgessen az **SQL Data Sync Agent**bejegyzéséhez.  
  1. Kattintson a jobb gombbal **az SQL Data Sync Agent**elemre, majd válassza a **Leállítás parancsot.**
  1. Kattintson a jobb gombbal **az SQL Data Sync Agent elemre,** majd válassza a Tulajdonságok **parancsot.**
  1. Az **SQL Data Sync Agent tulajdonságai párbeszédpanelen**jelölje be a **Bejelentkezés** lapot.
  1. A **Jelszó** mezőbe írja be a jelszavát.
  1. A **Jelszó megerősítése** párbeszédpanelen adja meg újra a jelszót.
  1. Válassza az **Apply** (Alkalmaz) lehetőséget, majd kattintson az **OK** gombra.
  1. A **Szolgáltatások** ablakban kattintson a jobb gombbal az **SQL Data Sync Agent** szolgáltatásra, majd kattintson a Start **parancsra.**
  1. Zárja be a **Szolgáltatások ablakot.**

### <a name="i-cant-submit-the-agent-key"></a><a name="agent-key"></a>Nem tudom beküldeni az ügynökkulcsot.

Miután létrehozott vagy újra létrehozott egy kulcsot egy ügynökhöz, megpróbálja elküldeni a kulcsot az SqlAzureDataSyncAgent alkalmazáson keresztül. A beküldés nem fejeződik be.

![Szinkronizálási hiba párbeszédpanel – Nem lehet elküldeni az ügynökkulcsot](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Előfeltételek**. A folytatás előtt ellenőrizze az alábbi előfeltételeket:

  - Az SQL Data Sync Windows szolgáltatás fut.

  - Az SQL Data Sync Windows-szolgáltatás szolgáltatásfiókja hálózati hozzáféréssel rendelkezik.

  - A kimenő 1433-as port meg van nyitva a helyi tűzfalszabályban.

  - A helyi ip hozzáadódik a kiszolgáló vagy adatbázis tűzfalszabálya a szinkronizálásmetaadat-adatbázishoz.

- **Oka**. Az ügynökkulcs egyedileg azonosítja az egyes helyi ügynököket. A kulcsnak két feltételnek kell megfelelnie:

  -   Az SQL Data Sync kiszolgáló és a helyi számítógép ügyfélügynök-kulcsának azonosnak kell lennie.
  -   Az ügyfélügynök kulcs csak egyszer használható.

- **felbontás .** Ha az ügynök nem működik, annak az az oka, hogy az egyik vagy mindkét feltétel nem teljesül. Ahhoz, hogy az ügynök újra dolgozni:

  1. Hozzon létre egy új kulcsot.
  1. Alkalmazza az új kulcsot az ügynökre.

  Az új kulcs alkalmazása az ügynökre:

  1. A Fájlkezelőben nyissa meg az ügynök telepítési könyvtárát. Az alapértelmezett telepítési könyvtár\\a C: Program\\Files (x86) Microsoft SQL Data Sync.
  1. Kattintson duplán a raktárhely alkönyvtárára.
  1. Nyissa meg az SqlAzureDataSyncAgent alkalmazást.
  1. Válassza **az Ügynökkulcs küldése lehetőséget.**
  1. A rendelkezésre álló helyre illessze be a kulcsot a vágólapról.
  1. Válassza **az OK gombot.**
  1. Zárja be a programot.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a><a name="agent-delete"></a>Az ügyfélügynök nem törölhető a portálról, ha a társított helyszíni adatbázis nem érhető el

Ha egy SQL Data Sync ügyfélügynökkel regisztrált helyi végpont (azaz adatbázis) elérhetetlenné válik, az ügyfélügynök nem törölhető.

- **Oka**. A helyi ügynök nem törölhető, mert az elérhetetlen adatbázis továbbra is regisztrálva van az ügynökkel. Amikor megpróbálja törölni az ügynököt, a törlési folyamat megpróbálja elérni az adatbázist, amely sikertelen.

- **felbontás .** Az elérhetetlen adatbázis törléséhez használja a "force delete" parancsot.

> [!NOTE]
> Ha a szinkronizálási metaadattáblák a "force delete" után is megmaradnak, törölje `deprovisioningutil.exe` őket.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a><a name="agent-connect"></a>A helyi szinkronizálási ügynök alkalmazás nem tud csatlakozni a helyi szinkronizálási szolgáltatáshoz

- **felbontás .** Próbálja meg a következőket:

  1. Lépjen ki az alkalmazásból.  
  1. Nyissa meg a Komponensszolgáltatások panelt.  
    a. A tálca keresőmezőjébe írja be a **services.msc**.  
    b. A keresési eredmények között kattintson duplán a **Szolgáltatások**elemre.  
  1. Állítsa le az **SQL Data Sync** szolgáltatást.
  1. Indítsa újra az **SQL Data Sync** szolgáltatást.  
  1. újra meg kell nyitniuk az alkalmazást;

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Az adatszinkronizálási ügynök futtatása a parancssorból

A parancssorból a következő Adatszinkronizáló-ügynök parancsokat futtathatja:

### <a name="ping-the-service"></a>A szolgáltatás pingelése

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

### <a name="submit-the-agent-key"></a>Az ügynökkulcs elküldése

#### <a name="usage"></a>Használat

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>Példa

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>Adatbázis regisztrálása

#### <a name="usage"></a>Használat

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Példák

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>Adatbázis regisztrációjának megszüntetése

Ha ezzel a paranccsal törölje az adatbázis regisztrációját, az teljesen megoldahassa az adatbázist. Ha az adatbázis más szinkronizálási csoportokban is részt vesz, ez a művelet megszakítja a többi szinkronizálási csoportot.

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

Az SQL Data Sync ről az alábbi cikkekben talál további információt:

-   Áttekintés – [Adatok szinkronizálása több felhőbeli és helyszíni adatbázisban az Azure SQL Data Sync segítségével](sql-database-sync-data.md)
-   Adatszinkronizálás beállítása
    - A portálon – [Oktatóanyag: Az SQL Data Sync beállítása az Adatok szinkronizálásához az Azure SQL Database és a helyszíni SQL Server között](sql-database-get-started-sql-data-sync.md)
    - A PowerShell-lel
        -  [A PowerShell használata több Azure SQL-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [A PowerShell használata egy Azure SQL-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Gyakorlati tanácsok – [Gyakorlati tanácsok az Azure SQL Data Sync szolgáltatáshoz](sql-database-best-practices-data-sync.md)
-   Figyelő – [SQL-adatszinkronizálás figyelése az Azure Figyelő naplóival](sql-database-sync-monitor-oms.md)
-   Hibaelhárítás – [Az Azure SQL Data Sync szolgáltatással kapcsolatos problémák elhárítása](sql-database-troubleshoot-data-sync.md)
-   A szinkronizálási séma frissítése
    -   Transact-SQL - [A sémamódosítások replikációjának automatizálása az Azure SQL Data Sync-ben](sql-database-update-sync-schema.md)
    -   A PowerShell használatával – [A PowerShell használatával frissítse a szinkronizálási sémát egy meglévő szinkronizálási csoportban](scripts/sql-database-sync-update-schema.md)
