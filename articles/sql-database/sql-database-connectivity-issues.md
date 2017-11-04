---
title: "Hárítsa el a SQL kapcsolódási hiba, átmeneti hiba |} Microsoft Docs"
description: "Megtudhatja, hogyan hibáinak elhárítása, diagnosztizálása és megelőzése egy SQL-kapcsolati hiba vagy az Azure SQL Database átmeneti hiba. "
keywords: "SQL-kapcsolatot, kapcsolati karakterláncot, problémák, átmeneti hiba, csatlakozási hiba"
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: efb35451-3fed-4264-bf86-72b350f67d50
ms.service: sql-database
ms.custom: develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: daleche
ms.openlocfilehash: dda284b45e2e8a35a7228d77afef0ad058c8ea42
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>SQL-csatlakozási hibák és átmeneti hibák elhárítása, diagnosztizálása és elkerülése az SQL Database szolgáltatásban
A cikkből megtudhatja, hogyan megakadályozása, hibáinak elhárítása, diagnosztizálása és csatlakozási hibáinak és, hogy az ügyfélalkalmazás tapasztal, amikor hatással van az Azure SQL Database átmeneti hibák elhárítása érdekében. Megtudhatja, hogyan konfigurálhatja az újrapróbálkozási logika, kapcsolódási karakterláncának felépítésére és további kapcsolati beállításokat.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Átmeneti hibák átmeneti
Egy átmeneti hiba - is, átmeneti hiba - rendelkezik egy alapul szolgáló ok, amely hamarosan magától megoldódik. Az alkalmi átmeneti hibák okát akkor, ha az Azure rendszer gyorsan átvált hardver-erőforrások jobb terheléselosztásához különböző munkaterhelések. A legtöbb gyakran teljes 60 másodpercnél kevesebb újrakonfigurálását eseményekből. Az újrakonfigurálás időtartam során előfordulhat, hogy az Azure SQL Database-kapcsolódási problémák. Csatlakozás az Azure SQL Database alkalmazások kell kialakítani, hogy ezek átmeneti hibák várható, ezek helyett felszínre hozza őket a felhasználók számára, mint az alkalmazáshibák kódjukat az újrapróbálkozási logika implementálásával kezelése.

Ha az ügyfélprogram ADO.NET használ, a program az átmeneti hiba van kevésbé a throw egy **SqlException**. A **szám** tulajdonságát is összehasonlítja a témakör tetejénél átmeneti hibák listája: [SQL Database-ügyfélalkalmazások SQL hibakódok](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Kapcsolat és a parancs
Próbálja meg újra az SQL-kapcsolatot fog, vagy létre újra, attól függően, hogy a következő:

* **Egy átmeneti hiba akkor fordul elő, a kapcsolódási kísérlet során**: A kapcsolat meg kell ismételni után késleltetése néhány másodpercig.
* **Egy átmeneti hiba jelentkezik egy SQL-lekérdezés parancs közben**: A parancs kell nem azonnal kísérli meg újra. Ehelyett késleltetés után a kell frissen létesíthető kapcsolat. Akkor is megpróbálja újból végrehajtani a parancsot.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

### <a name="retry-logic-for-transient-errors"></a>Újrapróbálkozási logika átmeneti hibák esetén
Ügyfél programok, amelyek találkozik, egy átmeneti hiba is robusztusabb, ha újrapróbálkozási logika tartalmaznak.

Amikor a program a 3. fél köztes keresztül kommunikál az Azure SQL Database, lekérdezése a gyártójával, hogy a köztes újrapróbálkozási logika átmeneti hibák tartalmaz-e.

<a id="principles-for-retry" name="principles-for-retry"></a>

#### <a name="principles-for-retry"></a>Az újrapróbálkozási alapelvei
* A kapcsolat megnyitására tett kísérlet meg kell ismételni, ha a hiba átmeneti.
* Olyan SQL SELECT utasításban, amely egy átmeneti hiba miatt sikertelen nem közvetlenül végrehajtásával lehet újrapróbálkozni.
  
  * Ehelyett friss kapcsolatot létesíteni, és ismételje meg a SELECT.
* Ha egy frissítés az SQL-utasítás egy átmeneti hiba miatt nem sikerül, egy friss kell kapcsolatot előtt a rendszer ismét megkísérli a FRISSÍTÉST.
  
  * Az újrapróbálkozási logika győződjön meg arról, hogy a teljes adatbázis-tranzakció befejeződött vagy, hogy a teljes tranzakció vissza lesz állítva.

#### <a name="other-considerations-for-retry"></a>Újrapróbálkozási egyéb szempontjai
* A kötegfájl, amely automatikusan elindul a munkaidőn, és amely reggel, mielőtt befejezi az újrapróbálkozások között hosszú idő időközökkel nagyon türelmet is megadja.
* A felhasználói felület program kell fiókot a túl hosszú várakozás után feladták emberi hajlamos.
  
  * Azonban a megoldás nem kell, majd ismételje meg minden néhány másodpercben, mert az adott házirendnek is kéréssekkel kérelmeket a rendszer.

#### <a name="interval-increase-between-retries"></a>Az újrapróbálkozások közötti időköz növekedése
Azt javasoljuk, hogy addig elhalasztani, az 5 másodperc az első újrapróbálkozás előtti. Újrapróbálkozás rövidebb, mint a felhőszolgáltatás overwhelming 5 másodperc kockázatok késleltetéssel. A késleltetés kell méretének növelése exponenciálisan növekszik, minden ezt követő újrapróbálkozásra legfeljebb 60 másodperc.

Tárgyalja a *blokkolási időtartam* ADO.NET használó ügyfelek számára érhető el [SQL Server készletezését (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

Érdemes előtt a program automatikusan leáll, állítsa be az újrapróbálkozások maximális számát is.

#### <a name="code-samples-with-retry-logic"></a>Az újrapróbálkozási logika mintakódok
Az újrapróbálkozási logika, a különböző programnyelveken, Kódminták webhelyen érhetők el:

* [Adatkapcsolattárak SQL Database és SQL Server](sql-database-libraries.md)

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

#### <a name="test-your-retry-logic"></a>Az újrapróbálkozási logika tesztelése
Az újrapróbálkozási logika teszteléséhez szimulálása, vagy hibát okoz, mint javítható, a program futása közben.

##### <a name="test-by-disconnecting-from-the-network"></a>Tesztelje a hálózati kapcsolat bontása
Tesztelheti az újrapróbálkozási logika egyik módja az ügyfélszámítógépen leválasztása a hálózatról, a program futása közben. A hiba a következő lesz:

* **SqlException.Number** = 11001
* Üzenet: "ilyen állomás nem található"

Az első újrapróbálkozások részeként, a program is javítsa ki a helyesírási, és csatlakozni.

Ahhoz, hogy ez gyakorlati, akkor eltávolíthatja a számítógép elérése a hálózatról a program indítása előtt. Majd a program felismeri a futási idő paraméter, ami miatt a program:

1. 11001 ideiglenesen hozzáadása a tekinti átmeneti jellegű hibák listája.
2. Az első csatlakozás a megszokott módon történt kísérlet.
3. A hiba lépett fel van, távolítsa el 11001 a listából.
4. Megjelenít egy üzenetet, szólítja fel a felhasználót, a számítógépet csatlakoztatni a hálózathoz.
   * További végrehajtásának felfüggesztése használatával vagy a **Console.ReadLine** metódus vagy egy párbeszédpanelen az OK gombra. A felhasználó megnyomja az Enter billentyűt, miután a számítógép csatlakoztatva a hálózathoz.
5. Próbálja meg újra csatlakozni, sikeres vár.

##### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Tesztelje a adatbázis függvénynév a csatlakozáskor
A program szándékosan is hibásan a felhasználó nevét, az első kapcsolódási kísérlet előtt. A hiba a következő lesz:

* **SqlException.Number** = 18456
* Üzenet: "bejelentkezés sikertelen volt a felhasználói"WRONG_MyUserName"."

Az első újrapróbálkozások részeként, a program is javítsa ki a helyesírási, és csatlakozni.

Ahhoz, hogy ez gyakorlati, a program sikerült ismeri fel a futási idő paraméter, ami miatt a program:

1. 18456 ideiglenesen hozzáadása a tekinti átmeneti jellegű hibák listája.
2. "WRONG_" szándékosan hozzá a felhasználónevet.
3. A hiba lépett fel van, távolítsa el 18456 a listából.
4. Távolítsa el a felhasználónevet "WRONG_".
5. Próbálja meg újra csatlakozni, sikeres vár.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

### <a name="net-sqlconnection-parameters-for-connection-retry"></a>Újrapróbálkozási .NET SqlConnection paramétereinek
Ha az ügyfélprogram az Azure SQL Database használatával kapcsolódik a .NET-keretrendszer osztály **System.Data.SqlClient.SqlConnection**, használjon .NET 4.6.1 vagy újabb (vagy a .NET Core), a kapcsolat újrapróbálkozási funkcióját. A szolgáltatás részletei [Itt](http://go.microsoft.com/fwlink/?linkid=393996).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->


Összeállításakor a [kapcsolati karakterlánc](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) a a **SqlConnection** objektumot, az értékek között az alábbi paramétereket kell koordinálni:

* ConnectRetryCount &nbsp; &nbsp; *(alapértelmezett érték 1. Tartománya 0 és 255 között.)*
* A ConnectRetryInterval &nbsp; &nbsp; *(alapértelmezett értéke 1 másodperc. Tartomány: 1 – 60.)*
* Kapcsolat időtúllépése &nbsp; &nbsp; *(alapértelmezett érték 15 másodpercre. A tartománya 0 és 2147483647 között)*

A kiválasztott értékek kifejezetten, a következő egyenlőség igaz kell tennie:

* Kapcsolat időtúllépése = ConnectRetryCount * ConnectionRetryInterval

Például ha a szám = 3, és időköz = 10 másodperc, a időtúllépés csak 29 másodperc nem elég adna a rendszer elegendő időt a 3. és végső újrapróbálkozási: Kapcsolódás: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Kapcsolat és a parancs
A **ConnectRetryCount** és **ConnectRetryInterval** paraméterek lehetővé teszik a **SqlConnection** objektum szólítja fel vagy bothering nélkül próbálja megismételni a kapcsolódási művelet a program, például a program visszatér a vezérlő. A próbálkozások a következő esetekben fordulhat elő:

* mySqlConnection.Open metódus hívása
* mySqlConnection.Execute metódus hívása

Nincs olyan subtlety. Egy átmeneti hiba akkor fordul elő, ha közben az *lekérdezés* végrehajtott, a **SqlConnection** objektum nem próbálja meg újra a kapcsolódási művelet, és hogy biztosan nem próbálja meg újra a lekérdezést. Azonban **SqlConnection** nagyon gyorsan ellenőrzi a kapcsolatot a végrehajtásra lekérdezés elküldése előtt. Ha a Gyorsellenőrzés észleli a kapcsolat hibája **SqlConnection** újrapróbálkozik a kapcsolódási művelet. Ha sikeres az újra gombra, hogy lekérdezést továbbítja a végrehajtási.

#### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Kombinálható ConnectRetryCount alkalmazás újrapróbálkozási logika?
Tegyük fel, hogy az alkalmazás rendelkezik robusztus egyéni újrapróbálkozási logika. Az lehet, hogy ismételje meg a kapcsolódási művelet 4 alkalommal. Ha ad hozzá **ConnectRetryInterval** és **ConnectRetryCount** = 3 a kapcsolati karakterlánc módosításait növeli az újrapróbálkozások maximális számát, és 4 * 3 = 12 újrapróbálkozások. Előfordulhat, hogy nem kíván ilyen újrapróbálkozások magas száma.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-azure-sql-database"></a>Az Azure SQL Database-kapcsolatok száma
<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Kapcsolat: Kapcsolati karakterlánc
A kapcsolati karakterláncot az Azure SQL Database való kapcsolódáshoz szükséges némileg eltér a karakterlánc a Microsoft SQL Server való kapcsolódáshoz. A kapcsolati karakterlánc másolhatja az adatbázis a [Azure-portálon](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Kapcsolat: IP-cím
Konfigurálnia kell az SQL Database-kiszolgáló kommunikációt fogad az IP-címét a számítógép, amelyen az ügyfélprogram. Ehhez a tűzfal beállításainak a szerkesztése a [Azure-portálon](https://portal.azure.com/).

Ha az IP-cím konfigurálása, a program egy kényelmes hibaüzenet arról, hogy a szükséges IP-cím sikertelen lesz.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

További információkért lásd: [hogyan: SQL Database tűzfalbeállításainak konfigurálása](sql-database-configure-firewall-settings.md)

<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Kapcsolat: portok
Általában csak kell győződjön meg arról, hogy a számítógépen, amelyen meg ügyfélprogram kimenő kommunikációra nyitva-e 1433-as port.

Például ha az ügyfélprogram Windows rendszerű számítógépeken, a a Windows tűzfal a gazdagépen lehetővé teszi 1433-as port megnyitásához:

1. Nyissa meg a Vezérlőpultot
2. &gt;Minden Vezérlőpultelem
3. &gt;A Windows tűzfal
4. &gt;Speciális beállítások
5. &gt;Kimenő szabályok
6. &gt;Műveletek
7. &gt;Új szabály

Ha az ügyfélprogram egy Azure virtuális gép (VM) üzemelteti, olvassa el:<br/>[Az ADO.NET 4.5-ös és az SQL Database 1433 kívüli portokon](sql-database-develop-direct-route-ports-adonet-v12.md).

Háttér-információkat a portok és IP-cím cofiguration, lásd: [Azure SQL Database-tűzfal](sql-database-firewall-configure.md)

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>Kapcsolat: ADO.NET 4.6.1.
Ha a program használja az ADO.NET osztályok hasonló **System.Data.SqlClient.SqlConnection** csatlakozzon az Azure SQL Database, javasoljuk, hogy használja-e a .NET-keretrendszer 4.6.1 verzió vagy újabb.

ADO.NET 4.6.1:

* Az Azure SQL Database esetén nincs továbbfejlesztett megbízhatósági kapcsolat használatával való megnyitásakor a **SqlConnection.Open** metódust. A **nyitott** metódus most magában foglalja a legjobb elérhető újrapróbálkozási mechanizmusok átmeneti hibák válaszul bizonyos hibák, a kapcsolat időkorláton belül.
* Kapcsolat készletezését támogatja. Ez magában foglalja egy hatékony annak ellenőrzése, hogy működik-e a kapcsolat objektum, a program biztosít.

Egy olyan kapcsolatobjektum kapcsolat készletből használatakor javasoljuk, hogy a program ideiglenesen létesített kapcsolat bezárása nem azonnal használata esetén. Kapcsolat újbóli létesítése nincs költséges az új kapcsolat létrehozása módja.

Ha az ADO.NET 4.0-s vagy korábbi, azt javasoljuk, hogy frissítsen a legújabb ADO.NET.

* 2015. November frissítésétől is [töltse le az ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnosztika
<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnosztika: Ellenőrizze, hogy csatlakozni tud-segédprogramok
Ha a program nem sikerült a csatlakozás az Azure SQL Database, egy diagnosztikai lehetőség egy megpróbálnak csatlakozni egy segédprogram programmal. Ideális esetben a segédprogram kapcsolódnának ugyanazt a szalagtárat, amelyek a program használatával.

Minden Windows rendszeren ezeket a segédprogramokat próbálkozhat:

* SQL Server Management Studio (ssms.exe), amely összeköti az ADO.NET használatával.
* Az Sqlcmd.exe, amelyek használatával kapcsolódik [ODBC](http://msdn.microsoft.com/library/jj730308.aspx).

A csatlakozás után tesztelje, hogy egy rövid SQL SELECT lekérdezés működése.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnosztika: Ellenőrizze a megnyitott portok
Tegyük fel, hogy azt feltételezi, hogy a kapcsolódási kísérletek port problémák miatt nem működnek. A számítógépen egy segédprogram, amely a portbeállításokat jelentések futtatásához.

Linux hasznosak lehetnek a következő szolgáltatásokkal:

* `netstat -nap`
* `nmap -sS -O 127.0.0.1`
  * (A példában szereplő értéket kell lennie az IP-cím módosítása)

A Windows a [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) segédprogram akkor lehetnek hasznosak. Íme egy példa végrehajtását, amely egy Azure SQL adatbázis-kiszolgáló port helyzet kérdezhetők le, és amelyek futtatták hordozható számítógépen:

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

### <a name="diagnostics-log-your-errors"></a>Diagnosztika: A hibák naplózására.
Probléma néha legjobb felderítette általános a minta az észlelést keresztül nap vagy hét.

Az ügyfél segít a diagnosztikai naplózás minden hibákat. Előfordulhat, hogy a naplóbejegyzéseket, amelyre az Azure SQL Database bejelentkezik maga belső hiba adatokkal összefüggéseket.

Vállalati könyvtár 6 (EntLib60) naplózás segít a felügyelt .NET osztályok kínálja:

* [5 - lehető legkönnyebben alá tartozó ki a napló: a naplózás alkalmazás-blokk használata](http://msdn.microsoft.com/library/dn440731.aspx)

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnosztika: Ellenőrizze a rendszer eseménynaplóit az hibák
Hiba a lekérdezés naplók és egyéb adatokat az alábbiakban néhány Transact-SQL SELECT utasítás.

| A lekérdezési napló | Leírás |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |A [sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) nézet nyújt információkat események, köztük egyes, amelyek átmeneti hibák vagy csatlakozási hibák okozhatják.<br/><br/>Ideális esetben hozhatók a **start_time** vagy **end_time** értékek információkkal, ha az ügyfélprogram észlelt problémákat.<br/><br/>**Tipp:** csatlakoznia kell a **fő** ez adatbázis. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |A [sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) nézet összesített számát az eseménytípusok, a további diagnosztikához is kínál.<br/><br/>**Tipp:** csatlakoznia kell a **fő** ez adatbázis. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnosztika: Keresse meg a probléma az SQL-adatbázis naplóbeli eseményei
Azure SQL-adatbázis naplója probléma kapcsolatos bejegyzéseket kereshet. Próbálja meg a következő Transact-SQL SELECT utasítás a **fő** adatbázis:

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
Ezután van, hogyan nézhet ki egy visszaadott sort. A null értékek látható nincsenek gyakran más egy sor null.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Vállalati könyvtár 6
Vállalati könyvtár 6 (EntLib60) egy keretében .NET-osztályok, amelyek segítségével megvalósíthatja robusztus ügyfeleket a felhőszolgáltatások, amelyek egyike az Azure SQL Database szolgáltatásban. Első ellátogatva minden területen, ahol EntLib60 segít dedikált témakörök keresheti meg:

* [Vállalati könyvtár 6 – 2013. április](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)

Újrapróbálkozási logika átmeneti hibák kezelése pedig segít a EntLib60 egy területen:

* [4 - perseverance, a titkos kulcsot, annak összes Triumphs: átmeneti hiba kezelési alkalmazás blokk használatával](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)

> [!NOTE]
> EntLib60 forráskódja érhető el a nyilvános [letöltése](http://go.microsoft.com/fwlink/p/?LinkID=290898). A Microsoftnál tervezzük további szolgáltatás- vagy karbantartás frissítéseket EntLib számára.
> 
> 

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Átmeneti hibák, és próbálkozzon ismét EntLib60 osztályok
A következő EntLib60 osztályok különösen hasznosak újrapróbálkozási logika. A fenti a, vagy további, a wsrmp **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

*A következő névtérbeli **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:*

* **A RetryPolicy** osztály
  
  * **ExecuteAction** módszer
* **ExponentialBackoff** osztály
* **SqlDatabaseTransientErrorDetectionStrategy** osztály
* **ReliableSqlConnection** osztály
  
  * **Az ExecuteCommand** módszer

A következő névtérbeli **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

* **AlwaysTransientErrorDetectionStrategy** osztály
* **NeverTransientErrorDetectionStrategy** osztály

Az alábbiakban EntLib60 kapcsolatos információkra mutató hivatkozásokat:

* Szabad [letöltési foglalható le: Fejlesztői útmutató a Microsoft Enterprise könyvtárba, 2. kiadás](http://www.microsoft.com/download/details.aspx?id=41145)
* Gyakorlati tanácsok: [ismételje meg az általános útmutatást](../best-practices-retry-general.md) az újrapróbálkozási logika egy kiváló részletes ismertető rendelkezik.
* A NuGet letöltésének [vállalati Library - alkalmazás blokk átmeneti hiba kezelő 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: A naplózás letiltása
* A naplózási blokk egy rendkívül rugalmas és konfigurálható megoldás, amely lehetővé teszi:
  
  * Hozzon létre, és számos különböző helyek naplóüzenetek tárolja.
  * Kategorizálását, és üzenetek szűréséhez.
  * Információgyűjtés környezetfüggő, amely hasznos a Hibakeresés és nyomkövetés, illetve a naplózási és általános naplózási követelményeknek.
* A naplózás letiltása kivonatolja a naplócél naplózási funkcióit, úgy, hogy az alkalmazás kódjának konzisztens, függetlenül a helyét és a cél naplózási tároló típusát.

További információ:: [5 –, egyszerűen mint alá tartozó ki a napló: az alkalmazás naplózási blokk használatával](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>EntLib60 IsTransient metódus forráskód
A következő a **SqlDatabaseTransientErrorDetectionStrategy** osztály, a C# forráskódja a **IsTransient** metódust. A forráskód tisztázza, hogy mely hibák tekintették átmeneti és az Ismét gombra, 2013. április frissítésétől worthy.

Számos **//comment** sorok el lettek távolítva ezt a példányt, hogy hangsúlyozzák az olvashatóság érdekében.

```
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


## <a name="next-steps"></a>Következő lépések
* Más közös Azure SQL-adatbázis kapcsolati problémák elhárítása, látogasson el [kapcsolódási problémák az Azure SQL Database](sql-database-troubleshoot-common-connection-issues.md).
* [SQL Server-kapcsolatkészlet (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)
* [*Újrapróbálkozás* van egy Apache 2.0 licenccel rendelkező általános célú könyvtár írt újrapróbálkozás **Python**, újrapróbálkozásra ad hozzá elemek feladatának leegyszerűsítése érdekében.](https://pypi.python.org/pypi/retrying)

