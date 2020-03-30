---
title: Átmeneti hibák javítása
description: Ismerje meg, hogyan háríthatja el, diagnosztizálhatja és előzheti meg az SQL-kapcsolati hibákat vagy átmeneti hibákat az Azure SQL Database-ben.
keywords: sql-kapcsolat,kapcsolati karakterlánc,kapcsolódási problémák,átmeneti hiba,csatlakozási hiba
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
manager: dcscontentpm
ms.author: ninarn
ms.reviewer: carlrab, vanto
ms.date: 01/14/2020
ms.openlocfilehash: d2b56e259f551f7655936c975a7a864a27a1df79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269080"
---
# <a name="troubleshooting-transient-connection-errors-to-sql-database"></a>Az SQL Database-rel kapcsolatos átmeneti csatlakozási hibák elhárítása

Ez a cikk ismerteti, hogyan előzheti meg, háríthatja el, diagnosztizálhatja és csökkentheti a csatlakozási hibákat és az okat, amelyeket az ügyfélalkalmazás az Azure SQL Database-kiszolgálóval való kommunikáció során észlel. Megtudhatja, hogyan konfigurálhatja az újrapróbálkozási logikát, hogyan hozhat létre a kapcsolati karakterláncot, és hogyan módosíthatja az egyéb kapcsolatbeállításokat.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Átmeneti hibák (átmeneti hibák)

Egy átmeneti hiba, más néven átmeneti hiba, van egy mögöttes oka, hogy hamarosan magától megoldódik. Az átmeneti hibák eseti oka az, amikor az Azure-rendszer gyorsan áthelyezi a hardvererőforrásokat a különböző munkaterhelések jobb terheléselosztása érdekében. Az újrakonfigurálási események többsége kevesebb mint 60 másodperc alatt befejeződik. Az újrakonfigurálási időtartam alatt előfordulhat, hogy kapcsolódási problémák vannak az SQL Database-rel. Az SQL Database-hez csatlakozó alkalmazásokat úgy kell kialakítani, hogy ezeket az átmeneti hibákat elvárják. Kezelni őket, valósítsa meg újrapróbálkozási logika a kódot, ahelyett, hogy felületi őket a felhasználók számára, mint alkalmazás hibák.

Ha az ügyfélprogram ADO.NET használ, a program az **SqlException**dobásával értesült az átmeneti hibáról. 

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Kapcsolat és parancs

Próbálkozzon újra az SQL-kapcsolattal, vagy hozza létre újra, a következőktől függően:

- **Átmeneti hiba történik a kapcsolatindítás során**

Néhány másodperces késleltetés után próbálkozzon újra a kapcsolattal.

- **Sql-lekérdezési parancs közben átmeneti hiba történik**

Ne próbálkozzon azonnal újra a paranccsal. Ehelyett, egy kis késés után, frissen létre a kapcsolatot. Ezután próbálkozzon újra a paranccsal.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Újrapróbálkozási logika átmeneti hibák esetén

Az átmeneti hibával időnként előforduló ügyfélprogramok robusztusabbak, ha újrapróbálkozási logikát tartalmaznak. Amikor a program harmadik féltől származó köztes szoftveren keresztül kommunikál az SQL Database-lel, kérdezze meg a szállítót, hogy a köztes szoftver tartalmaz-e átmeneti hibák újrapróbálkozási logikáját.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Az újrapróbálkozás alapelvei

- Ha a hiba átmeneti, próbálja meg újra megnyitni a kapcsolatot.
- Ne próbálkozzon közvetlenül `SELECT` egy SQL-utasítással, amely átmeneti hibával sikertelen volt. Ehelyett hozzon létre egy új kapcsolatot, majd próbálkozzon újra a `SELECT`.
- Ha egy `UPDATE` SQL utasítás átmeneti hibával sikertelen, hozzon létre egy új kapcsolatot, mielőtt újra próbálkozna az UPDATE-el. Az újrapróbálkozási logikának biztosítania kell, hogy a teljes adatbázistranzakció befejeződött, vagy a teljes tranzakció vissza lett állítva.

### <a name="other-considerations-for-retry"></a>Az újrapróbálkozás egyéb szempontjai

- A kötegprogram, amely automatikusan elindul a munkaidő után, és befejezi a reggeli előtt engedheti meg magának, hogy nagyon türelmes, hosszú időintervallumok között újra megpróbálja.
- A felhasználói felület program figyelembe kell vennie az emberi tendencia, hogy adja fel túl hosszú várakozás után. A megoldás nem próbálja meg újra néhány másodpercenként, mert ez a házirend eláraszthatja a rendszert kérésekkel.

### <a name="interval-increase-between-retries"></a>Időköz növelése az újrapróbálkozások között

Javasoljuk, hogy várjon 5 másodpercet az első újrapróbálkozás előtt. Az 5 másodpercnél rövidebb késleltetés után az újrapróbálkozás a felhőszolgáltatás elárasztásának kockázatával jár. Minden ezt követő újrapróbálkozás esetén a késleltetés exponenciálisan, legfeljebb 60 másodpercig növekszik.

A ADO.NET használó ügyfelek blokkolási időszakáról az [SQL Server kapcsolatkészletezés (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx)című témakörben tud megbeszélni.

Előfordulhat, hogy a program önbeszüntetése előtt be szeretné állítani az újrapróbálkozások maximális számát.

### <a name="code-samples-with-retry-logic"></a>Kódminták újrapróbálkozási logikával

Az újrapróbálkozási logikával rendelkező kódpéldák a következő helyen érhetők el:

- [Rugalmasan csatlakozhat az SQL-hez ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Csatlakozzon rugalmasan az SQL-hez a PHP-vel][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Az újrapróbálkozási logika tesztelése

Az újrapróbálkozási logika teszteléséhez szimulálnia kell vagy olyan hibát kell okoznia, amely a program futása közben javítható.

#### <a name="test-by-disconnecting-from-the-network"></a>Tesztelés a hálózatról való leválasztással

Az újrapróbálkozási logika tesztelésének egyik módja, ha a program futása közben leválasztja az ügyfélszámítógépet a hálózatról. A hiba:

- **SqlException.Number** = 11001
- Üzenet: "Ilyen állomás nem ismert"

Az első újrapróbálkozási kísérlet részeként újra csatlakoztathatja az ügyfélszámítógépet a hálózathoz, majd megpróbálhat csatlakozni.

Ahhoz, hogy ez a teszt praktikus legyen, a program indítása előtt húzza ki a számítógépet a hálózatból. Ezután a program felismeri a futásidejű paramétert, amely a következő tetszést okozza:

- Ideiglenesen adja hozzá az 11001-et a hibás hibák listájához, amelyet átmenetinek kell tekinteni.
- Próbálja meg az első kapcsolatot a szokásos módon.
- A hiba után távolítsa el az 11001-et a listából.
- Üzenet megjelenítése, amely arra utasítja a felhasználót, hogy csatlakoztassa a számítógépet a hálózathoz.
- A további végrehajtás szüneteltetése a **Console.ReadLine** metódus vagy egy OK gombbal rendelkező párbeszédablak használatával. A felhasználó lenyomja az Enter billentyűt, miután a számítógép csatlakoztatva van a hálózathoz.
- Próbáljon meg újra csatlakozni, és várta a sikert.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Tesztelés az adatbázis nevének elírásával csatlakozáskor

A program szándékosan elgépelheti a felhasználónevet az első csatlakozási kísérlet előtt. A hiba:

- **SqlException.Number** = 18456
- Üzenet: "A bejelentkezés nem sikerült a "WRONG_MyUserName" felhasználó számára."

Az első újrapróbálkozási kísérlet részeként a program kijavíthatja az elírást, majd megpróbálhat csatlakozni.

Ahhoz, hogy ez a teszt praktikus legyen, a program felismer egy futásidejű paramétert, amely a következő tetszést okozza:

- Ideiglenesen adja hozzá 18456-ot a hibák listájához, amelyeket átmenetinek kell tekinteni.
- Szándékosan adja hozzá a "WRONG_" értéket a felhasználónévhez.
- A hiba észlelése után távolítsa el az 18456-ot a listából.
- Távolítsa el a "WRONG_" nevet a felhasználónévből.
- Próbáljon meg újra csatlakozni, és várta a sikert.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>.NET SqlConnection paraméterek a kapcsolat újrapróbálkozásához

Ha az ügyfélprogram a **System.Data.SqlClient.SqlConnection**. A funkcióval kapcsolatos további információkért tekintse meg [ezt a weboldalt.](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection)

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Az **SqlConnection** objektum [kapcsolati karakterláncának](https://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) létrehozásakor koordinálja az értékeket a következő paraméterek között:

- **ConnectRetryCount**&nbsp;&nbsp;: Az alapértelmezett érték 1. A távolság 0 és 255 között van.
- **ConnectRetryInterval**&nbsp;&nbsp;: Az alapértelmezett érték 10 másodperc. A távolság 1-től 60-ig terjed.
- **Kapcsolat időtúllépés:**&nbsp;&nbsp;Az alapértelmezett érték 15 másodperc. A tartomány 0 és 2147483647 között van.

Pontosabban a kiválasztott értékeknek a következő egyenlőséget kell igazná tenniük: Kapcsolati időtúllépés = ConnectRetryCount * ConnectionRetryInterval

Ha például a számláló 3, az intervallum pedig 10 másodperc, a mindössze 29 másodperces időtúlfelvétel nem ad elég időt a rendszernek a harmadik és egyben utolsó újrapróbálkozásra: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Kapcsolat és parancs

A **ConnectRetryCount** és **a ConnectRetryInterval** paraméterek lehetővé teszik, hogy az **SqlConnection** objektum újramegpróbálja a csatlakozási műveletet anélkül, hogy a programot megbeszélné vagy zavarná, például visszaadja a vezérlőt a programnak. Az újrapróbálkozások a következő esetekben fordulhatnak elő:

- mySqlConnection.Open metódushívás
- mySqlConnection.Execute metódushívás

Van egy finomság. Ha a *lekérdezés* végrehajtása közben átmeneti hiba történik, az **SqlConnection-objektum** nem próbálja meg újra a csatlakozási műveletet. Ez biztosan nem próbálja meg újra a lekérdezést. Az **SqlConnection** azonban nagyon gyorsan ellenőrzi a kapcsolatot, mielőtt a lekérdezést végrehajtásra küldenék. Ha a gyorsellenőrzés csatlakozási problémát észlel, az **SqlConnection** újrapróbálkozik a csatlakozási művelettel. Ha az újrapróbálkozás sikeres, a lekérdezés végrehajtásra kerül.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>A ConnectRetryCount-ot kombinálni kell az alkalmazás-újrapróbálkozási logikával

Tegyük fel, hogy az alkalmazás robusztus egyéni újrapróbálkozási logikával rendelkezik. Előfordulhat, hogy négyszer próbálja meg újra a csatlakozási műveletet. Ha **connectretryInterval** és **ConnectRetryCount** =3 a kapcsolati karakterlánc, növeli az újrapróbálkozások száma 4 * 3 = 12 újrapróbálkozások. Lehet, hogy nem kíván ilyen nagy számú újrapróbálkozást.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>Kapcsolatok az SQL-adatbázissal

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Kapcsolat: Kapcsolati karakterlánc

Az SQL Database-hez való csatlakozáshoz szükséges kapcsolati karakterlánc némileg eltér az SQL Server kiszolgálóhoz való csatlakozáshoz használt karakterlánctól. Az adatbázis kapcsolati karakterláncát az [Azure Portalról](https://portal.azure.com/)másolhatja.

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Kapcsolat: IP-cím

Az SQL Database kiszolgálót úgy kell konfigurálnia, hogy fogadja az ügyfélprogramot tároló számítógép IP-címéről érkező kommunikációt. A konfiguráció beállításához az [Azure Portalon](https://portal.azure.com/)keresztül szerkesztse a tűzfalbeállításokat.

Ha elfelejti beállítani az IP-címet, a program sikertelen lesz egy praktikus hibaüzenettel, amely a szükséges IP-címet adja meg.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

További információt a [Tűzfalbeállítások konfigurálása az SQL Database rendszerben című témakörben talál.](sql-database-configure-firewall-settings.md)
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Kapcsolat: Portok

Általában biztosítania kell, hogy csak az 1433-as port legyen megnyitva az ügyfélprogramot tároló számítógépen a kimenő kommunikációhoz.

Ha például az ügyfélprogram egy Windows rendszerű számítógépen van, az állomáson lévő Windows tűzfal segítségével megnyithatja az 1433-as portot.

1. Nyissa meg a Vezérlőpultot.
2. Jelölje be **az összes Vezérlőpult-elem** > **A Windows tűzfal** > **speciális beállításai** > **kimenő szabályok** > **műveletek** > **Új szabály**.

Ha az ügyfélprogram egy Azure virtuális gépen (VM) található, olvassa el [a Portok 1433 utáni állapotát a ADO.NET 4.5-ös és az SQL Database számára.](sql-database-develop-direct-route-ports-adonet-v12.md)

A portok és IP-címek konfigurálásáról az [Azure SQL Database tűzfal című](sql-database-firewall-configure.md)témakörben talál háttér-információkat.

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Csatlakozás: ADO.NET 4.6.2 vagy újabb

Ha a program ADO.NET osztályt használ, például **a System.Data.SqlClient.SqlConnection adatbázist,** javasoljuk, hogy a .NET Framework 4.6.2-es vagy újabb verzióját használja.

#### <a name="starting-with-adonet-462"></a>Kezdve ADO.NET 4.6.2

- A kapcsolat megnyitása megkísérli, hogy azonnal újra az Azure SQL-adatbázisok, ezáltal a felhőalapú alkalmazások teljesítményének javítása.

#### <a name="starting-with-adonet-461"></a>Kezdve ADO.NET 4.6.1

- Az SQL Database esetében a megbízhatóság javul, ha az **SqlConnection.Open** metódussal nyit meg egy kapcsolatot. Az **Open** metódus mostantól a csatlakozási időtúllépési időszakon belül bizonyos hibák átmeneti hibáira adott válaszként a legjobb újrapróbálkozási mechanizmusokat tartalmazza.
- A kapcsolatkészletezés támogatott, amely magában foglalja annak hatékony ellenőrzését, hogy a program nak adott kapcsolatobjektum működik-e.

Ha kapcsolati objektumot használ egy kapcsolatkészletből, azt javasoljuk, hogy a program ideiglenesen zárja le a kapcsolatot, amikor az nincs azonnal használatban. Nem drága újra megnyitni a kapcsolatot, de új kapcsolatot kell létrehozni.

Ha ADO.NET 4.0-s vagy korábbi használata, javasoljuk, hogy frissítsen a legújabb ADO.NET. 2018 augusztusától a [4.6.2 ADO.NET töltheti le.](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/)

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnosztika

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnosztika: Ellenőrizze, hogy a segédprogramok csatlakozhatnak-e

Ha a program nem tud csatlakozni az SQL Database-hez, az egyik diagnosztikai lehetőség egy segédprogrammal való kapcsolat megkísérlése. Ideális esetben a segédprogram a program által használt könyvtár használatával csatlakozik.

Bármely Windows számítógépen kipróbálhatja az alábbi segédprogramokat:

- SQL Server Management Studio (ssms.exe), amely ADO.NET
- `sqlcmd.exe`, amely az [ODBC](https://msdn.microsoft.com/library/jj730308.aspx) használatával csatlakozik

A program csatlakoztatása után ellenőrizze, hogy működik-e egy rövid SQL SELECT lekérdezés.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnosztika: Ellenőrizze a nyitott portokat

Ha azt gyanítja, hogy a csatlakozási kísérletek portproblémák miatt sikertelenek, futtathat egy segédprogramot a számítógépen, amely a portkonfigurációkról számol be.

Linux alatt a következő segédprogramok lehetnek hasznosak:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: Módosítsa a példa értéket, hogy az IP-cím.

Windows rendszerben a [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) segédprogram hasznos lehet. Íme egy példa a végrehajtásra, amely lekérdezte a porthelyzetet egy SQL Database-kiszolgálón, és amelyet egy hordozható számítógépen futtattak:

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnosztika: A hibák naplózása

Egy időszakos probléma néha a legjobban diagnosztizált kimutatásával általános minta napokon vagy heteken keresztül.

Az ügyfél segíthet a diagnózis naplózásával az összes hibát találkozik. Előfordulhat, hogy a naplóbejegyzéseket össze tudja kapcsolni az SQL Database által belsőleg naplózó hibaadatokkal.

Az Enterprise Library 6 (EntLib60) .NET által kezelt osztályokat kínál a naplózáshoz. További információ: [5 - Olyan egyszerű, mint leesik egy naplóról: Használja a naplózási alkalmazásblokkot.](https://msdn.microsoft.com/library/dn440731.aspx)

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnosztika: Vizsgálja meg a rendszernaplókat a hibák ra

Íme néhány Transact-SQL SELECT utasítás, amely hibanaplókat és egyéb információkat kérdez.

| Napló lekérdezése | Leírás |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |A [sys.event_log](https://msdn.microsoft.com/library/dn270018.aspx) nézet információkat nyújt az egyes eseményekről, amelyek olyan eseményeket tartalmaznak, amelyek átmeneti hibákat vagy kapcsolódási hibákat okozhatnak.<br/><br/>Ideális esetben az **start_time** vagy **end_time** értékeket összevetheti azzal az információval, hogy az ügyfélprogram mikor tapasztalt problémákat.<br/><br/>A lekérdezés futtatásához csatlakoznia kell a *fő* adatbázishoz. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |A [sys.database_connection_stats](https://msdn.microsoft.com/library/dn269986.aspx) nézet ben a további diagnosztikához az eseménytípusok összesített száma áll.<br/><br/>A lekérdezés futtatásához csatlakoznia kell a *fő* adatbázishoz. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnosztika: Problémaesemények keresése az SQL-adatbázis naplójában

A problémaeseményekre vonatkozó bejegyzéseket az SQL-adatbázis naplójában keresheti meg. Próbálkozzon a következő Transact-SQL *master* SELECT utasítással a főadatbázisban:

```sql
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

#### <a name="a-few-returned-rows-from-sysfn_xe_telemetry_blob_target_read_file"></a>Néhány visszaadott sor a sys.fn_xe_telemetry_blob_target_read_file

A következő példa bemutatja, hogyan nézhet ki egy visszaadott sor. A megjelenített null értékek gyakran nem null értékűek más sorokban.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Vállalati könyvtár 6

Az Enterprise Library 6 (EntLib60) a .NET osztályok keretrendszere, amely segít a felhőszolgáltatások robusztus ügyfeleinek megvalósításában, amelyek közül az egyik az SQL Database szolgáltatás. Az EntLib60 által támogatott területekkel kapcsolatos témakörök megkereséséhez lásd: [Vállalati könyvtár 2013.](https://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)

Az átmeneti hibák kezelésére szolgáló újrapróbálkozási logika az entLib60 egyik olyan területe, amelyen az EntLib60 segíthet. További információ: [4 - Kitartás, minden diadal titka: Használja a Tranziens hibakezelési alkalmazásblokkot.](https://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)

> [!NOTE]
> Az EntLib60 forráskódja nyilvánosan letölthető a [letöltőközpontból.](https://go.microsoft.com/fwlink/p/?LinkID=290898) A Microsoft nem tervezi további funkció- vagy karbantartási frissítéseket készíteni az EntLib-hez.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>EntLib60 osztályok átmeneti hibák hoz és újrapróbálkozás

A következő EntLib60 osztályok különösen hasznosak az újrapróbálkozási logika esetén. Mindezek az osztályok a **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**névtérben vagy alatt találhatók.

A **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling névtérben:**

- **ÚjrapróbálkozásI házirend** osztály
  - **ExecuteAction** metódus
- **ExponentialBackoff** osztály
- **SqlDatabaseTransientErrorDetectionStrategy** osztály
- **ReliableSqlConnection** osztály
  - **ExecuteCommand** metódus

A **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport névtérben:**

- **AlwaysTransientErrorDetectionStrategy** osztály
- **NeverTransientErrorDetectionStrategy** osztály

Íme néhány link az EntLib60-ról:

- Ingyenes könyvletöltés: [Developer's Guide to Microsoft Enterprise Library, 2nd edition](https://www.microsoft.com/download/details.aspx?id=41145).
- Ajánlott eljárások: [Az általános útmutató újrapróbálkozása](../best-practices-retry-general.md) kiváló részletes en-próbálkozási logikával rendelkezik.
- NuGet letöltés: [Enterprise Library - Átmeneti hibakezelési alkalmazásblokk 6.0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: A naplózási blokk

- A naplózási blokk egy rendkívül rugalmas és konfigurálható megoldás, amelya következőkre használható:
  - A naplóüzeneteket sokféle helyen hozhat létre és tárolhatja.
  - Üzenetek kategorizálása és szűrése.
  - A hibakereséshez és nyomkövetéshez, valamint a naplózáshoz és az általános naplózási követelményekhez hasznos környezetfüggő adatok gyűjtése.
- A naplózási blokk absztrakt a naplózási funkciót a napló cél, hogy az alkalmazás kód konzisztens, függetlenül a helyét és típusát a cél naplózási tároló.

További információ: [5 - Olyan egyszerű, mint leesik egy naplóról: Használja a naplózási alkalmazásblokkot.](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>EntLib60 IsTransient metódus forráskódja

Ezután az **SqlDatabaseTransientErrorDetectionStrategy** osztályból az **IsTransient** metódus C# forráskódja. A forráskód egyértelművé teszi, hogy 2013 áprilisától mely hibákat tekintették átmenetinek és érdemesnek az újrapróbálkozásra.

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

- [Kapcsolattárak SQL Database és SQL Server rendszerhez](sql-database-libraries.md)
- [SQL Server-kapcsolatkészletezés (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [ *Az újrapróbálkozás* egy Apache 2.0 licencelt általános célú újrapróbálkozási könyvtár, amelyet Pythonban írtak,](https://pypi.python.org/pypi/retrying) hogy egyszerűsítse az újrapróbálkozási viselkedés hozzáadásának feladatát a bármihez.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
