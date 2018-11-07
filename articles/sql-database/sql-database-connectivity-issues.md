---
title: Egy SQL-csatlakozási hiba, átmeneti hiba javítása |} A Microsoft Docs
description: Ismerje meg, hogyan elhárítása, diagnosztizálása és elkerülése az egy SQL-csatlakozási hiba vagy egy átmeneti hiba az Azure SQL Database-ben.
keywords: SQL-kapcsolatot, kapcsolati karakterlánc, kapcsolódási problémák, átmeneti hiba, kapcsolódási hiba
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
ms.author: ninarn
ms.reviewer: carlrab
manager: craigg
ms.date: 08/01/2018
ms.openlocfilehash: ee5542c72991a2aa8de94f5dc2e819eb5d311a27
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246803"
---
# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>SQL-csatlakozási hibák és átmeneti hibák elhárítása, diagnosztizálása és elkerülése az SQL Database szolgáltatásban
Ez a cikk ismerteti, hogyan megelőzése, elhárítása, diagnosztizálása és csökkentheti a csatlakozási hibák és átmeneti hibák, amely az ügyfélalkalmazásban fordul elő, amikor interaktálnak az Azure SQL Database. Ismerje meg, hogyan konfigurálhatja az újrapróbálkozási logika, a kapcsolati karakterlánc összeállítása és további kapcsolati beállításokat.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Átmeneti hibák (átmeneti hibák)
Átmeneti hiba, más néven egy átmeneti hibák rendelkezik az alapul szolgáló ok, amely hamarosan megszünteti magát. Egy átmeneti hibákra alkalmanként okának esetén, az Azure rendszer gyorsan kerülnek a figyelem középpontjába hardver-erőforrások jobb terheléselosztás különböző számítási feladatokat. Ezek az események újrakonfigurálása a legtöbb 60 másodpercnél rövidebb be. Az újrakonfigurálás időtartományon lehetséges, hogy az SQL Database-kapcsolódási problémák. Az alkalmazásokat, amelyek a csatlakozás az SQL Database ilyen átmeneti hibákra várható kell építeni. Kezelni őket, újrapróbálkozási a logikát alkalmazzák a kódra összpontosítsanak, felszínre hozza őket a felhasználók számára, mint az alkalmazáshibák helyett.

Ha az ügyfélprogram használja az ADO.NET, a program van a kevésbé az átmeneti hiba miatt, a throw **kivétel**. Hasonlítsa össze a **szám** tulajdonság és az átmeneti hibák, a cikk tetején található listájának [az SQL Database-ügyfélalkalmazások SQL-hibakódok](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Parancs és a kapcsolat
Ismételje meg az SQL-kapcsolatot, vagy újra létrehozni függően a következő:

* **Átmeneti hiba történik a kapcsolódási kísérlet során**: néhány másodperces várakozás után ismételje meg a kapcsolat.
* **Átmeneti hiba következik-e egy SQL-lekérdezés parancs**: nem azonnal próbálja meg újra a parancsot. Ehelyett némi késéssel frissen kapcsolat létesítésére. Próbálkozzon újra a parancsot.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Újrapróbálkozási logika átmeneti hibák esetén
Átmeneti hiba időnként előforduló ügyfélprogramok robusztusabb újrapróbálkozási logikát tartalmazó.

Amikor a program külső közbenső keresztül kommunikál az SQL Database, beszereznie a termékösszekötőt, hogy az a közbenső szoftver tartalmaz újrapróbálkozási logika átmeneti hibák esetén.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Elveket, próbálkozzon újra
* Ha a hiba átmeneti, ismételje meg a kapcsolat megnyitásához.
* Nem közvetlenül próbálja meg újra a SELECT utasítást, amely egy átmeneti hiba miatt sikertelen volt.
  * Ehelyett egy új kapcsolatot, és ismételje meg a SELECT.
* Ha egy frissítés az SQL-utasítás egy átmeneti hiba miatt nem sikerül, friss kapcsolatot létesíteni, mielőtt ismét megpróbálkozik a FRISSÍTÉST.
  * Az újrapróbálkozási logika biztosítania kell, hogy befejeződött-e a teljes adatbázis vagy a tranzakció vagy a teljes tranzakció vissza lesz állítva.

### <a name="other-considerations-for-retry"></a>Újrapróbálkozási egyéb szempontjai
* Batch programok, amelyek automatikusan munkaidő után elindul, és befejezése előtt reggel esetében megengedhető, a hosszú az újrapróbálkozási kísérletek közötti intervallumok nagyon betegek lehet.
* A felhasználói felület program túl hosszú várakozás után feladja a az emberi megfelelő kell figyelembe venni.
  * A megoldás nem kell újra minden néhány másodpercben, mert az adott házirendnek is kéréssekkel túlterhelheti a rendszer a kérelmek.

### <a name="interval-increase-between-retries"></a>Újrapróbálkozások közötti időköz növelése
Azt javasoljuk, hogy várjon 5 másodpercre az első újrapróbálkozás előtt. Újrapróbálkozás rövidebb, mint a felhőszolgáltatás túlterhel 5 másodperc kockázatok késleltetéssel. A próbálkozások, a késleltetés növekedés kell exponenciálisan növekszik, akár 60 másodperc.

A blokkolási időtartam ADO.NET használó ügyfelek számára, lásd: [kapcsolatkészlet (ADO.NET) az SQL Server](https://msdn.microsoft.com/library/8xx3tyca.aspx).

Érdemes azt is beállítja az újrapróbálkozások maximális számát, mielőtt önálló leállítja a programot.

### <a name="code-samples-with-retry-logic"></a>Kódminták az újrapróbálkozási logika
Hitelesítésikód-példák az újrapróbálkozási logika esetén érhető el:

- [Logikára kapcsolódni az SQL az ADO.NET-tel][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Az SQL-PHP logikára csatlakoztatása][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Tesztelje az újrapróbálkozási logika
Tesztelje az újrapróbálkozási logikája, szimulálása, vagy a program futása közben is javítható hiba miatt.

#### <a name="test-by-disconnecting-from-the-network"></a>Tesztelje a hálózatról való leválasztás
Tesztelheti az újrapróbálkozási logika egyik módja válassza le az ügyfélszámítógépen a hálózatról, a program futása közben. A hiba a következő:

* **SqlException.Number** = 11001
* Üzenet: "nincs ilyen gazdagép ismert"

Az első újrapróbálkozási kísérlet részeként a program kijavíthatja a helyesírási, és csatlakozni.

Ahhoz, hogy ez a teszt gyakorlati, le kell választani a hálózatról a számítógép, a program indítása előtt. Ezután a program felismeri, ami miatt a program futásidejű paraméter:

* 11001 ideiglenesen adja hozzá a tekinti az átmeneti hibák listáját.
* Próbálja meg az első kapcsolat a szokásos módon.
* Hiba történt, miután 11001 eltávolítása a listából.
* Megjelenít egy üzenetet, amely arról tájékoztatja a felhasználót, hogy a számítógép csatlakoztassa a hálózathoz.
   * További végrehajtási használatával szüneteltetheti a **Console.ReadLine** metódus vagy egy párbeszédpanelen az OK gombra. A felhasználó lenyomja az Enter billentyűt, miután a számítógép csatlakoztatva van a hálózathoz.
* Próbálja meg újra szeretne csatlakozni, a program sikeres vár.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>A database függvénynév a csatlakozáskor tesztelése
A program szándékosan is hibásan a felhasználó nevét, az első kapcsolódási kísérlet előtt. A hiba a következő:

* **SqlException.Number** = 18456
* Üzenet: "sikertelen bejelentkezés"WRONG_MyUserName"felhasználó számára."

Az első újrapróbálkozási kísérlet részeként a program kijavíthatja a helyesírási, és csatlakozni.

Ahhoz, hogy ez a teszt gyakorlati, a program felismeri, ami miatt a program futásidejű paraméter:

* Átmenetileg adjon 18456 tekinti az átmeneti hibák listáját.
* "WRONG_" szándékosan hozzá a felhasználó nevét.
* Hiba történt, miután 18456 eltávolítása a listából.
* Távolítsa el a felhasználónevet "WRONG_".
* Próbálja meg újra szeretne csatlakozni, a program sikeres vár.


<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>Újrapróbálkozási .NET SqlConnection paraméterei
Ha az ügyfélprogram SQL Database használatával csatlakozik a .NET-keretrendszer osztály **System.Data.SqlClient.SqlConnection**, használja a .NET 4.6.1 vagy újabb (vagy a .NET Core), hogy a kapcsolat újrapróbálkozási funkciót is használhatja. A szolgáltatás további információkért lásd: [a weblap](https://go.microsoft.com/fwlink/?linkid=393996).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Összeállításakor az [kapcsolati karakterlánc](https://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) számára a **SqlConnection** objektumazonosító, koordinálja az értékeket a következő paraméterek közül:

* **ConnectRetryCount**:&nbsp;&nbsp;alapértelmezett érték 1. Értéktartomány: 0 és 255 között.
* **ConnectRetryInterval**:&nbsp;&nbsp;az alapértelmezett érték 1 másodperc. Értéktartomány: 1 és 60.
* **Kapcsolat időtúllépése**:&nbsp;&nbsp;az alapértelmezett érték 15 másodperc. Értéktartomány: 0 és 2147483647 között.

Pontosabban a kiválasztott értékek kell, a következő egyenlőség igaz:

Kapcsolat időtúllépése = ConnectRetryCount * ConnectionRetryInterval

Például, ha a szám értéke 3, és az intervallum értéke 10 másodperc, időkorlát van meghatározva csak a 29 másodperc nem ad a rendszer a harmadik és egyben utolsó újrapróbálkozási való csatlakozáshoz elegendő időt: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Parancs és a kapcsolat
A **ConnectRetryCount** és **ConnectRetryInterval** paraméterek lehetővé teszik a **SqlConnection** objektum ismételje meg a connect művelet közben vagy bothering nélkül a program, például a program visszatér a vezérlő. A próbálkozások a következő esetekben fordulhat elő:

* mySqlConnection.Open metódus hívása
* mySqlConnection.Execute metódus hívása

Nincs olyan subtlety. Átmeneti hiba akkor fordul elő, ha közben az *lekérdezés* folyamatban van, a **SqlConnection** objektum nem próbálja meg újra a connect műveletet. Ez természetesen nem ismételje meg a lekérdezést. Azonban **SqlConnection** nagyon gyorsan ellenőrzi a kapcsolatot, mielőtt elküldené a lekérdezés végrehajtásához. Ha a Gyorsellenőrzés észleli a kapcsolat hibája **SqlConnection** újrapróbálkozik a kapcsolódási művelet. Ha az újrapróbálkozás sikeres, a lekérdezés végrehajtásához küld.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Kombinálható ConnectRetryCount alkalmazás újrapróbálkozási logikát?
Tegyük fel, hogy az alkalmazás rendelkezik robusztus egyéni újrapróbálkozási logikát. Ez előfordulhat, hogy újra a connect művelettel négyszer. Ha hozzáad **ConnectRetryInterval** és **ConnectRetryCount** = 3, a kapcsolati karakterlánc az újrapróbálkozások számának 4 * 3 = 12-re növeli az újrapróbálkozások. Előfordulhat, hogy nem kívánja ilyen újrapróbálkozások nagy számú.


<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>SQL-adatbázis kapcsolatai
<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Kapcsolat: Kapcsolati karakterlánc
A kapcsolati karakterláncot, amely az SQL-adatbázishoz való csatlakozáshoz szükséges némileg eltér az SQL-kiszolgálóhoz való csatlakozáshoz használt karakterlánc. Az adatbázis a is másolja a kapcsolati karakterláncot a [az Azure portal](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Kapcsolat: IP-cím
Konfigurálnia kell az IP-cím annak a számítógépnek, amelyen az ügyfélprogram-kommunikációt fogad az SQL Database-kiszolgálóhoz. Állítsa be ezt a konfigurációt, szerkessze a tűzfal beállításai keresztül a [az Azure portal](https://portal.azure.com/).

Ha az IP-cím konfigurálása, a program egy praktikus hibaüzenetet küld, hogy a szükséges IP-cím a sikertelen lesz.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

További információkért lásd: [az SQL Database tűzfalbeállításainak konfigurálása](sql-database-configure-firewall-settings.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Kapcsolat: port
Általában kell győződjön meg arról, hogy csak a 1433-as portot a számítógépen, amelyen az ügyfélprogram kimenő kommunikációra nyitva-e.

Például ha az ügyfélprogram egy Windows-számítógépen lévő üzemeltetett, segítségével Windows tűzfal a gazdagépen nyissa meg az 1433-as porton.

1. Nyissa meg a Vezérlőpultot.

2. Válassza ki **összes vezérlőpultelemek** > **Windows tűzfal** > **speciális beállítások** > **kimenő szabályok**   >  **Műveletek** > **új szabály**.

Ha az ügyfélprogram-beli virtuális gépen (VM) üzemel, olvassa el [az ADO.NET 4.5 és az SQL Database 1433-Ason túli](sql-database-develop-direct-route-ports-adonet-v12.md).

Háttér-portok és IP-címek konfigurációjával kapcsolatos információkat, tekintse meg a [Azure SQL Database-tűzfal](sql-database-firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Kapcsolat: ADO.NET 4.6.2-es vagy újabb
Ha a program használja az ADO.NET-osztályok hasonló **System.Data.SqlClient.SqlConnection** szeretne csatlakozni az SQL Database, azt javasoljuk, hogy a .NET-keretrendszer 4.6.2-es verzióját használja, vagy később.

Az ADO.NET-tel 4.6.2-es indítása:

- A megnyitott való kapcsolódási kísérlet megpróbálja újból végrehajtani a közvetlenül az Azure SQL-adatbázisok, ezáltal javul a felhőkompatibilis alkalmazások teljesítményét.

Az ADO.NET 4.6.1 kezdőérték:

* Az SQL Database, a megbízhatóság növelése kapcsolat használatával nyissa meg a **SqlConnection.Open** metódust. A **nyílt** metódus mostantól tartalmazza a legjobb újrapróbálkozási mechanizmus az átmeneti hibák válaszul bizonyos hibák a kapcsolat időkorláton belül.
* Kapcsolatkészletezést támogatott, amely egy hatékony annak ellenőrzése, hogy működik-e a kapcsolat objektumot biztosít a programot tartalmazza.

Egy kapcsolat objektumot a kapcsolatkészlet használata esetén javasoljuk, hogy a programot ideiglenesen lezárja a kapcsolatot, ha nem azonnal használatban van. Nem költséges, nyissa meg újra a kapcsolatot, de egy új kapcsolatot létrehozni.

Ha korábban, azt javasoljuk, hogy frissítsen a legújabb ADO.NET vagy ADO.NET 4.0-s verzióját használja. 2018 augusztus is [töltse le az ADO.NET 4.6.2-es](https://blogs.msdn.microsoft.com/dotnet/2018/04/30/announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnosztika
<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnosztika: Segédprogramok csatlakozhatnak-e tesztelése
Ha a program sikertelen csatlakozás az SQL Database, egy diagnosztikai lehetőség egy próbál csatlakozni egy segédprogram programmal. Ideális esetben a segédprogram használatával, amelyek a program egyazon szalagtárat kapcsolódik.

Bármely Windows-számítógépen próbálja ki ezeket a segédprogramokat:

* Az SQL Server Management Studio (ssms.exe), amely csatlakoztatja az ADO.NET használatával
* Sqlcmd.exe, amelyek használatával kapcsolódik [ODBC](https://msdn.microsoft.com/library/jj730308.aspx)

A program csatlakoztatása után tesztelje, hogy egy rövid SQL SELECT lekérdezést működik.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnosztika: Ellenőrizze a portok megnyitása
Ha azt gyanítja, hogy kapcsolódási kísérletek port problémák miatt sikertelenek lesznek, futtathatja egy segédprogram a számítógépen, amely a portbeállításokat kapcsolatos jelentések.

A Linux az alábbi segédprogramokat hasznosak lehetnek:

* `netstat -nap`
* `nmap -sS -O 127.0.0.1`
  * A példában szereplő értéket, az IP-címhez kell módosítani.

A Windows a [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) segédprogram hasznos lehet. Íme egy példa végrehajtását, amely egy SQL Database-kiszolgáló port helyzet lekérdezett és a egy hordozható számítógépen futó:

```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnosztika: A hibák naplózása
Egy időszakos probléma néha a legjobb meghatározása egy általános mintája kimutatása napok vagy hetek alatt.

Az ügyfél-naplózás az összes hibát tapasztal egy diagnosztikai végrehajtásában. Előfordulhat, hogy tudnia kell korrelálni a naplóbejegyzéseket, amely az SQL Database maga belső hiba adatokkal.

Enterprise Library 6 (EntLib60) felügyelt .NET-osztályok, amelyek segítik a naplózást kínál. További információkért lásd: [5 – megírásához alá tartozó, egy log off: használja a naplózási Alkalmazásblokkal](https://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnosztika: Rendszer naplóit is hibák vizsgálata
Az alábbiakban néhány Transact-SQL SELECT utasítást, amely lekérdezési hibanaplókat és egyéb információkat.

| Lekérdezési napló | Leírás |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |A [sys.event_log](https://msdn.microsoft.com/library/dn270018.aspx) nézetet biztosít egyéni eseményekkel kapcsolatos információkat, köztük olyanokat is, amelyek átmeneti hibák vagy csatlakozási hibákat okozhat.<br/><br/>Ideális esetben összehasonlíthatja a **start_time** vagy **end_time** értékek információkat, ha az ügyfélprogram észlelt a problémákat.<br/><br/>Csatlakoznia kell a *fő* adatbázis-lekérdezés futtatásához. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |A [sys.database_connection_stats](https://msdn.microsoft.com/library/dn269986.aspx) nézetet kínál a további diagnosztikához eseménytípusok összesített számát.<br/><br/>Csatlakoznia kell a *fő* adatbázis-lekérdezés futtatásához. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnosztika: Keresse meg a probléma az SQL Database naplózási események
Az SQL Database naplózási események probléma kapcsolatos bejegyzéseket is kereshet. Próbálja ki a következő Transact-SQL SELECT utasítást a *fő* adatbázis:

```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>A sys.fn_xe_telemetry_blob_target_read_file néhány visszaadott sorok
Az alábbi példa bemutatja, hogy a visszaadott sor néz. A NULL értékű látható értékei gyakran nem null értékű, a többi sort.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6
Enterprise Library 6 (EntLib60) egy olyan keretrendszer, a .NET-osztályok, amely segítséget nyújt a cloud services, a robusztus ügyfelei megvalósítása amelyek egyike az SQL Database szolgáltatás. Keresse meg a témakörök dedikált minden területéhez, amelyben EntLib60 végrehajtásában, lásd: [Enterprise Library 6 – 2013. április](https://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx).

Újrapróbálkozási logika átmeneti hibák kezelése a EntLib60 végrehajtásában, amelyben egy területe. További információkért lásd: [4 – Perseverance, az összes triumphs titkos kulcs: használja az átmeneti-kezelési Alkalmazásblokk](https://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> A EntLib60 forráskódja nyilvános letölthető a [letöltőközpontból](https://go.microsoft.com/fwlink/p/?LinkID=290898). A Microsoft tervezzük további szolgáltatás- vagy karbantartási frissítéseket a EntLib rendelkezik.
>
>

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Az átmeneti hibák, és ismételje meg a EntLib60 osztályok
A következő EntLib60 osztályok különösen hasznosak újrapróbálkozási logikát. Ezeket az osztályokat a vagy a névtérben található **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**.

A névtér **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

* **RetryPolicy** osztályban

  * **ExecuteAction** metódus
* **ExponentialBackoff** osztályban
* **SqlDatabaseTransientErrorDetectionStrategy** osztályban
* **ReliableSqlConnection** osztályban

  * **ExecuteCommand** metódus

A névtér **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

* **AlwaysTransientErrorDetectionStrategy** osztályban
* **NeverTransientErrorDetectionStrategy** osztályban

Az alábbiakban néhány EntLib60 kapcsolatos információkra mutató hivatkozásokat:

* Ingyenes könyv letöltése: [fejlesztői útmutató a Microsoft Enterprise Library, 2. kiadás](https://www.microsoft.com/download/details.aspx?id=41145).
* Ajánlott eljárások: [Újrapróbálkozásokra vonatkozó általános útmutató](../best-practices-retry-general.md) tartalmaz egy újrapróbálkozási logika kiváló tárgyalja.
* NuGet-Letöltés: [Enterprise Library - átmeneti hibák kezelése alkalmazás letiltása 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: A naplózás letiltása
* A naplózás letiltása egy rendkívül rugalmas és konfigurálható megoldás, amely segítségével:

  * Hozzon létre, és naplóüzenetek számos különböző helyeken tárolja.
  * Kategorizálásához és szűrik az üzeneteket.
  * Környezeti információk, amely hasznos a hibakereséshez és a nyomkövetés, valamint a naplózás és az általános naplózási követelmények összegyűjtése.
* A naplózás letiltása a napló célhelyen lévő a naplózási funkció kivonatolja, úgy, hogy az alkalmazás kódja egységes, attól függetlenül, helye és a cél naplózási tároló típusa.

További információkért lásd: [5 – megírásához alá tartozó, egy log off: használja a naplózási Alkalmazásblokkal](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>EntLib60 IsTransient metódus forráskód
A következő a **SqlDatabaseTransientErrorDetectionStrategy** osztályhoz, a C# forráskódját a **IsTransient** metódust. A forráskód tisztázza, hogy mely hibák lettek figyelembe véve, átmeneti és újrapróbálkozási 2013. április kezdődően összefügg.

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## <a name="next-steps"></a>További lépések
* Más SQL-adatbázis-kapcsolatok gyakori hibáinak elhárításáról további információkért lásd: [az Azure SQL Database-kapcsolatok problémáinak hibaelhárítása](sql-database-troubleshoot-common-connection-issues.md).
* [Adatkapcsolattárak az SQL Database és SQL Server](sql-database-libraries.md)
* [Az SQL Server kapcsolatkészlet (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
* [*Újrapróbálkozás* egy Apache 2.0 licenccel rendelkező általános célú könyvtár, pythonban írt, újrapróbálkozás van](https://pypi.python.org/pypi/retrying) újrapróbálkozási viselkedés hozzáadásának bármik feladatának leegyszerűsítése érdekében.


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
