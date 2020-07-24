---
title: A U-SQL programozható útmutatója Azure Data Lake
description: Ismerkedjen meg Azure Data Lake Analytics szolgáltatásokkal, amelyek lehetővé teszik a felhőalapú big data-platform létrehozását.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 80edafb9cffa43f7163c1b75c9faaaefbb97c616
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87127417"
---
# <a name="u-sql-programmability-guide"></a>A U-SQL programozható útmutatója

Az U-SQL egy olyan lekérdezési nyelv, amelyet a számítási feladatok big dataához terveztek. Az U-SQL egyik egyedi funkciója az SQL-szerű deklaratív nyelv kombinációja a C# által biztosított bővíthetőséggel és programozhatóság használatával. Ebben az útmutatóban a C# által engedélyezett U-SQL nyelv bővíthetőségére és programozhatóra koncentrálunk.

## <a name="requirements"></a>Követelmények

Töltse le és telepítse [a Visual studióhoz készült Azure Data Lake-eszközöket](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>U-SQL – első lépések  

Tekintse meg a következő U-SQL-parancsfájlt:

```usql
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

Ez a parancsfájl két sorhalmazt definiál: `@a` és `@results` . A sorhalmaz a `@results` következőből van definiálva: `@a` .

## <a name="c-types-and-expressions-in-u-sql-script"></a>C#-típusok és-kifejezések az U-SQL-parancsfájlban

A U-SQL kifejezés egy C# kifejezés, amely a U-SQL logikai műveletekkel, például a és a szolgáltatással együtt használható `AND` `OR` `NOT` . A U-SQL kifejezések a SELECT, a EXTRACT, a WHERE, a GROUP BY és a DECLARE kifejezéssel használhatók. Például a következő parancsfájl egy sztringet DateTime értékként elemez.

```usql
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

A következő kódrészlet egy karakterláncot DateTime értékként elemez a DECLARE utasításban.

```usql
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>C#-kifejezések használata adattípus-konverzióhoz

Az alábbi példa azt mutatja be, hogyan végezheti el a DateTime típusú adatkonverziót C#-kifejezések használatával. Ebben az adott esetben a string datetime-adatok konvertálása standard datetime értékre történik, éjfél 00:00:00 idő jelöléssel.

```usql
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>A mai dátumhoz C#-kifejezések használata

A mai dátum lekéréséhez a következő C# kifejezést használhatja:`DateTime.Now.ToString("M/d/yyyy")`

Íme egy példa arra, hogyan használhatja ezt a kifejezést egy parancsfájlban:

```usql
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>.NET-szerelvények használata

Az U-SQL bővíthetőségi modellje nagy mértékben támaszkodik arra, hogy egyéni kódot adjon hozzá a .NET-szerelvényekhez. 

### <a name="register-a-net-assembly"></a>.NET-szerelvény regisztrálása

A `CREATE ASSEMBLY` .net-szerelvényt a következő utasítással helyezheti el egy U-SQL Databaseba. Ezt követően az U-SQL-parancsfájlok ezeket a szerelvényeket használhatják az `REFERENCE ASSEMBLY` utasítás használatával. 

A következő kód bemutatja, hogyan regisztrálhat egy szerelvényt:

```usql
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

A következő kód bemutatja, hogyan hivatkozhat egy szerelvényre:

```usql
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

További részletekért tekintse meg a [szerelvény regisztrációs utasításait](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) , amelyek részletesebben ismertetik ezt a témakört.


### <a name="use-assembly-versioning"></a>Szerelvény verziószámozásának használata
Jelenleg az U-SQL a .NET-keretrendszer 4,5-es verzióját használja. Ügyeljen arra, hogy a saját szerelvényei kompatibilisek legyenek a futtatókörnyezet adott verziójával.

Ahogy korábban említettük, az U-SQL 64 bites (x64) formátumban futtatja a kódot. Ügyeljen arra, hogy a kód fordítása x64-re fusson. Ellenkező esetben helytelen formátumú hibaüzenetet kap.

Minden feltöltött összeállítási DLL-fájl és erőforrásfájl, például egy másik futtatókörnyezet, egy natív szerelvény vagy egy konfigurációs fájl legfeljebb 400 MB lehet. A központilag telepített erőforrások teljes mérete (akár üzembe helyezési erőforrás, akár szerelvények és a további fájlok esetében) nem haladhatja meg a 3 GB-ot.

Végezetül vegye figyelembe, hogy minden U-SQL-adatbázis csak egy adott szerelvény egyetlen verzióját tudja tartalmazni. Ha például a NewtonSoft Json.NET Library 7-es verziójára és 8-as verziójára van szüksége, akkor két különböző adatbázisban kell regisztrálnia. Emellett az egyes parancsfájlok csak egy adott szerelvény-DLL egy verziójára vonatkozhatnak. Ebben a tekintetben az U-SQL a C# szerelvények felügyeletét és verziószámozását követi.

## <a name="use-user-defined-functions-udf"></a>Felhasználó által definiált függvények használata: UDF
A felhasználó által definiált U-SQL-függvények vagy UDF-EK olyan programozási rutinok, amelyek elfogadják a paramétereket, végrehajtanak egy műveletet (például egy összetett számítást), és értékként adják vissza a művelet eredményét. Az UDF visszatérési értéke csak egyetlen skaláris lehet. A u-SQL UDF a U-SQL alapparancsfájlban hívható meg, mint bármely más C# skaláris függvény.

Javasoljuk, hogy a felhasználó által definiált U-SQL-függvényeket **nyilvánosként** és **statikusan**inicializálja.

```usql
public static string MyFunction(string param1)
{
    return "my result";
}
```

Először tekintsük át az UDF létrehozásának egyszerű példáját.

Ebben a használati helyzetben meg kell határozni a pénzügyi időszakot, beleértve az adott felhasználóhoz tartozó első bejelentkezés pénzügyi negyedévét és pénzügyi hónapját. Az év első pénzügyi hónapja a forgatókönyvben június.

A pénzügyi időszak kiszámításához a következő C#-függvényt mutatjuk be:

```usql
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

Egyszerűen kiszámítja a pénzügyi hónapot és a negyedévet, és egy sztring értéket ad vissza. Júniusban az első pénzügyi negyedév első hónapjára a "Q1: P1" kifejezést használjuk. Júliusban a "Q1: P2" kifejezést használjuk, és így tovább.

Ez egy normál C#-függvény, amelyet a U-SQL-projektben fogunk használni.

Itt látható, hogyan néz ki a kód mögötti szakasz ebben a forgatókönyvben:

```usql
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

Most hívjuk ezt a függvényt az alap U-SQL-szkriptből. Ehhez meg kell adnia a függvény teljes nevét, beleértve a névteret is, amely ebben az esetben a NameSpace. class. Function (paraméter).
```usql
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

A következő a tényleges U-SQL-alapú parancsfájl:
```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

A szkript végrehajtásának kimeneti fájlja a következő:

```output
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Ez a példa a beágyazott UDF egyszerű használatát mutatja be U-SQL-ben.

### <a name="keep-state-between-udf-invocations"></a>Állapot megőrzése az UDF-hívások között
Az U-SQL C# programozható objektumok kifinomultabbak, és a globális változók mögötti interaktivitást is igénybe vehetik. Nézzük meg a következő üzleti használati esetet.

A nagyméretű szervezeteknél a felhasználók válthatnak a belső alkalmazások különböző fajtái között. Ilyenek lehetnek például a Microsoft Dynamics CRM, a Power BI stb. Előfordulhat, hogy az ügyfelek telemetria-elemzést kívánnak alkalmazni arról, hogy a felhasználók hogyan válthatnak a különböző alkalmazások között, milyen trendek vannak, és így tovább. Az üzleti cél az alkalmazások használatának optimalizálása. Emellett érdemes lehet különböző alkalmazásokat vagy konkrét bejelentkezési rutinokat kombinálni.

Ennek a célnak a megvalósításához meg kell határozni a munkamenet-azonosítókat és a késési időt az utolsó munkamenet során.

Meg kell találni egy korábbi bejelentkezést, majd hozzá kell rendelnie ezt a bejelentkezést az ugyanahhoz az alkalmazáshoz létrehozott összes munkamenethez. Az első kihívás az, hogy az U-SQL alap parancsfájl nem teszi lehetővé a számítások alkalmazását a már számított oszlopokon a LAG függvénnyel. A második kihívás az, hogy meg kell őrizni az adott munkamenetet az adott időszakon belüli összes munkamenethez.

A probléma megoldásához egy globális változót használunk a kód mögötti szakaszban: `static public string globalSession;` .

Ezt a globális változót a szkript végrehajtása során a teljes sorhalmazra alkalmazza a rendszer.

Itt látható a U-SQL program kódjának mögötti szakasza:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

Ez a példa a függvényen belül használt globális változót mutatja be `static public string globalSession;` `getStampUserSession` , és minden alkalommal újrainicializálja a munkamenet-paramétert.

A U-SQL alap parancsfájl a következő:

```usql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

A függvényt `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` a rendszer a második memória sorhalmazának kiszámítása során hívja meg. Átadja az `UserSessionTimestamp` oszlopot, és a változásig visszaadja az értéket `UserSessionTimestamp` .

A kimeneti fájl a következő:

```output
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

Ez a példa egy bonyolultabb használati esetet mutat be, amelyben egy globális változót használunk egy kód mögötti szakaszban, amely a teljes memória sorhalmazra van alkalmazva.

## <a name="use-user-defined-types-udt"></a>Felhasználó által definiált típusok használata: UDT
A felhasználó által definiált típusok vagy UDT a U-SQL egy másik programozható szolgáltatása. Az U-SQL-UDT egy normál C# felhasználó által definiált típushoz hasonlóan működik. A C# egy határozottan gépelt nyelv, amely lehetővé teszi a beépített és egyéni felhasználók által definiált típusok használatát.

Az U-SQL nem tud implicit módon szerializálni vagy deszerializálni tetszőleges UDT, amikor a UDT a csúcspontok között átadja a sorhalmazban. Ez azt jelenti, hogy a felhasználónak explicit formázó eszközt kell megadnia a IFormatter felület használatával. Ez biztosítja a U-SQL-t a UDT szerializálási és deszerializáló módszereivel.

> [!NOTE]
> A U-SQL beépített kivonói és-előállítók jelenleg nem tudják szerializálni vagy deszerializálni a UDT-adatait, még a IFormatter is. Tehát amikor UDT ír egy fájlba a kimeneti utasítással, vagy beolvassa azt egy kivonóval, akkor karakterlánc-vagy byte-tömbként kell átadnia. Ezután meghívja a szerializálási és a deszerializálási kódot (azaz a UDT ToString () metódusát) explicit módon. A felhasználó által definiált kiállítók és a lekérések a UDT olvashatók és írhatók.

Ha a UDT-t a kivonón vagy a kimeneten (az előző SELECT-ből) próbáljuk használni, az itt látható módon:

```usql
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

A következő hibaüzenetet kapja:

```output
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

A UDT-ben való együttműködéshez a ToString () metódussal kell szerializálni a karakterláncot, vagy létre kell hoznia egy egyéni leválasztó eszközt.

A UDT jelenleg nem használható a GROUP BY utasításban. Ha a UDT a GROUP BY utasításban használja, a következő hiba történik:

```output
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

A UDT definiálásához a következőket kell tennie:

* Adja hozzá a következő névtereket:

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Adja hozzá `Microsoft.Analytics.Interfaces` , amely a UDT-interfészekhez szükséges. Emellett szükség lehet `System.IO` az IFormatter felület definiálására is.

* Definiáljon egy használt típust a SqlUserDefinedType attribútummal.

A **SqlUserDefinedType** az U-SQL-ben felhasználó által definiált típusként (UDT) jelöli meg a szerelvény típus definícióját. Az attribútum tulajdonságai a UDT fizikai jellemzőit tükrözik. Ez az osztály nem örökölhető.

A SqlUserDefinedType a UDT-definíció kötelező attribútuma.

Az osztály konstruktora:  

* SqlUserDefinedTypeAttribute (típus formázó)

* Type formázó: kötelező paraméter egy UDT formázó definiálásához – konkrétan a csatoló típusát `IFormatter` itt kell átadni.

```csharp
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* A tipikus UDT az IFormatter felület definícióját is igényli, ahogy az az alábbi példában is látható:

```csharp
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

Az `IFormatter` illesztőfelület szerializálja és deszerializálja az Object gráfot a gyökérszintű típussal \<typeparamref name="T"> .

\<typeparam name="T">Az objektum gráfjának legfelső szintű típusa szerializáláshoz és deszerializáláshoz.

* **Deszerializálás**: megszünteti a megadott adatfolyamban lévő adatmennyiséget, és visszaképezi az objektumok gráfját.

* **Szerializálás**: egy objektum vagy objektum gráfjának szerializálására szolgál a megadott adatfolyammal.

`MyType`példány: a típus példánya.  
`IColumnWriter`író/ `IColumnReader` olvasó: az alapul szolgáló oszlop adatfolyama.  
`ISerializationContext`Context: Enum, amely a szerializálás során megadja a forrás vagy a cél környezetét az adatfolyamhoz.

* **Köztes**: azt határozza meg, hogy a forrás vagy a cél környezet nem egy megőrzött tároló.

* **Megőrzés**: Megadja, hogy a forrás vagy a cél környezet megőrzött tároló-e.

Normál C#-típusként a U-SQL-UDT definíciója olyan operátorok felülbírálását is magában foglalhatja, mint a +/= =/! =. Statikus metódusokat is tartalmazhat. Ha például ezt a UDT paraméterként egy U-SQL MIN összesítő függvényhez fogjuk használni, meg kell határozni < operátor felülbírálását.

Az útmutató korábbi részében egy példát mutatunk be a pénzügyi időszak azonosítására a formátumban megadott dátum alapján `Qn:Pn (Q1:P10)` . Az alábbi példa bemutatja, hogyan határozhat meg egyéni típust a pénzügyi időszakok értékeihez.

Az alábbi példa egy, az egyéni UDT és a IFormatter felülettel ellátott kódrészletet tartalmaz:

```csharp
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

A megadott típus két számot tartalmaz: a negyedévet és a hónapot. Az operátorok `==/!=/>/<` és a statikus metódusok `ToString()` itt vannak meghatározva.

Ahogy azt korábban említettük, a UDT használhatók a SELECT kifejezésekben, de nem használhatók a kiállítók/kivonók egyéni szerializálás nélkül. Ezt a karakterláncot `ToString()` vagy egy egyéni, illetve KIvonót használó sztringként kell szerializálni.

Most beszéljünk a UDT használatáról. A kód mögötti szakaszban a GetFiscalPeriod függvényt a következőre módosították:

```csharp
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

Amint láthatja, a FiscalPeriod típusának értékét adja vissza.

Íme egy példa arra, hogyan használhatja tovább a U-SQL alap parancsfájlban. Ez a példa az U-SQL-szkriptből származó UDT hívás különböző formáit mutatja be.

```usql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
           start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

Íme egy példa a teljes kód mögötti szakaszra:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Felhasználó által definiált összesítések használata: UDAGG
A felhasználó által definiált összesítések bármely olyan összesítéssel kapcsolatos függvény, amely nem a U-SQL-mel van kiszállítva. A példa az egyéni matematikai számítások, a karakterlánc-összefűzések, a karakterláncokkal végzett manipulációk és így tovább.

A felhasználó által definiált aggregált alaposztály definíciója a következő:

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

A **SqlUserDefinedAggregate** azt jelzi, hogy a típust felhasználó által definiált összesítésként kell regisztrálni. Ez az osztály nem örökölhető.

A SqlUserDefinedType attribútum nem **kötelező** a UDAGG-definícióhoz.


Az alaposztály lehetővé teszi három absztrakt paraméter átadását: két bemeneti paraméterként, az egyiket pedig eredményként. Az adattípusok változó, és az osztályok öröklése során kell meghatározni.

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* Az **init** a számítás során egyszer meghívja az egyes csoportokat. Inicializálási rutint biztosít az egyes összesítési csoportokhoz.  
* A **felhalmozódás** minden értéknél egyszer lesz végrehajtva. Ez biztosítja az összesítési algoritmus fő funkcióit. A segítségével az osztályok öröklése során definiált különböző adattípusokkal összesítheti az értékeket. Elfogadhatja a változó adattípusok két paraméterét is.
* A **megszakítás** a feldolgozás végén egy összesítési csoporton keresztül történik, hogy kiadja az eredményeket az egyes csoportokhoz.

A helyes bemeneti és kimeneti adattípusok bejelentéséhez használja az osztály definícióját az alábbiak szerint:

```csharp
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: az első felhalmozó paraméter
* T2: a halmozódás második paramétere
* TResult: a megszakítás visszatérési típusa

Például:

```csharp
public class GuidAggregate : IAggregate<string, int, int>
```

vagy

```csharp
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>A UDAGG használata a U-SQL-ben
A UDAGG használatához először kódot kell megadnia, vagy hivatkoznia kell rá a létező programozható DLL-ből a korábban tárgyalt módon.

Ezután használja a következő szintaxist:

```csharp
AGG<UDAGG_functionname>(param1,param2)
```

Íme egy példa a UDAGG:

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

És alap U-SQL-parancsfájl:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Ebben a használati helyzetben az adott felhasználók osztály GUID azonosítóit fűzi össze.

## <a name="use-user-defined-objects-udo"></a>Felhasználó által definiált objektumok használata: UDO
Az U-SQL lehetővé teszi, hogy egyéni programozható objektumokat határozzon meg, amelyeket felhasználó által definiált objektumoknak vagy UDO-nek nevezünk.

A következő lista az U-SQL-ben UDO-t tartalmazza:

* Felhasználó által definiált kivonók
    * Sor kibontása sorba
    * Egyéni strukturált fájlokból származó adatok kinyerésének megvalósítására használatos

* Felhasználó által definiált előszerkesztők
    * Kimeneti sor soronként
    * Egyéni adattípusok vagy egyéni fájlformátumok kimenetére használatos

* Felhasználó által definiált processzorok
    * Egy sor készítése és egy sor létrehozása
    * Az oszlopok számának csökkentésére vagy új oszlopok előállítására szolgál, amelyek egy meglévő oszlopból származtatott értékekkel rendelkeznek.

* Felhasználó által definiált beadási típusok
    * Egy sor elkészítése és 0 – n sor létrehozása
    * KÜLSŐ/KERESZTtel használatos

* Felhasználó által definiált kombinálók
    * A sorhalmazok egyesítése – felhasználó által definiált illesztések

* Felhasználó által definiált szűkítők
    * N sor készítése és egy sor létrehozása
    * A sorok számának csökkentésére használatos

UDO általában a U-SQL-parancsfájlban explicit módon van meghívva a következő U-SQL-utasítások részeként:

* KINYERÉS
* KIMENETI
* FOLYAMAT
* EGYESÍTÉSE
* CSÖKKENTHETI

> [!NOTE]  
> A UDO-k 0,5 GB memóriát használnak fel.  Ez a memória-korlátozás nem vonatkozik a helyi végrehajtásokra.

## <a name="use-user-defined-extractors"></a>Felhasználó által definiált kivonók használata
A U-SQL lehetővé teszi külső adatok importálását egy EXTRACT utasítás használatával. A kinyerési utasítások beépített UDO-kiállítók használatát használhatják:  

* *Extrahálók. Text ()*: a különböző kódolások tagolt szövegfájlokból való kivonást biztosít.

* *Extractors.Csv ()*: a különböző kódolású, vesszővel tagolt (CSV) fájlok kinyerését teszi lehetővé.

* *Extrahálók. TSV ()*: a különböző kódolású, tabulátorokkal tagolt (TSV) fájlokból kinyert kibontást biztosít.

Hasznos lehet egyéni kivonót létrehozni. Ez az adatok importálása során hasznos lehet, ha a következő feladatokat szeretné elvégezni:

* A bemeneti adatok módosítása oszlopok felosztásával és az egyes értékek módosításával. A processzor funkciója jobb az oszlopok kombinálásával.
* Strukturálatlan adatelemzések, például weblapok és e-mailek, vagy félig strukturálatlan, például XML/JSON típusú adat elemzése.
* Az adatelemzés nem támogatott kódolású.

Felhasználó által definiált Kivonó vagy UDE definiálásához létre kell hozni egy `IExtractor` felületet. A kivonó összes bemeneti paraméterét, például az oszlop/sorok elhatárolóit és a kódolást az osztály konstruktorában kell meghatározni. Az `IExtractor` illesztőfelületnek tartalmaznia kell a felülbírálás definícióját is a `IEnumerable<IRow>` következőképpen:

```csharp
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

A **SqlUserDefinedExtractor** attribútum azt jelzi, hogy a típust felhasználó által definiált kivonóként kell regisztrálni. Ez az osztály nem örökölhető.

A SqlUserDefinedExtractor a UDE-definíció opcionális attribútuma. A UDE objektum AtomicFileProcessing tulajdonságának definiálására szolgál.

* bool AtomicFileProcessing   

* **true** = azt jelzi, hogy a kivonóhoz atomi bemeneti fájlok szükségesek (JSON, XML,...)
* **false** = azt jelzi, hogy ez a kivonó képes kezelni a felosztott/elosztott fájlokat (CSV, SEQ,...)

A fő UDE programozható objektumok **bemenetek** és **kimenetek**. A bemeneti objektum a bemeneti adatok enumerálására szolgál a következőként: `IUnstructuredReader` . A kimeneti objektum a kivonó tevékenység eredményeként a kimeneti adatokat adja meg.

A bemeneti adatok a és a használatával érhetők el `System.IO.Stream` `System.IO.StreamReader` .

A bemeneti oszlopok enumerálásakor a bemeneti adatfolyamot először egy sor elválasztójának használatával daraboljuk.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Ezután bontsa ki a bemeneti sort oszlop részeibe.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

A kimeneti adatokat a metódus használatával állíthatja be `output.Set` .

Fontos tisztában lenni azzal, hogy az egyéni kivonó csak a kimenetben definiált oszlopokat és értékeket jeleníti meg. Állítsa be a metódus hívását.

```csharp
output.Set<string>(count, part);
```

A tényleges kivonó kimenet a meghívásával aktiválódik `yield return output.AsReadOnly();` .

A következő a kivonó példa:

```csharp
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

Ebben a használati helyzetben a Extractor újragenerálta a GUID azonosítóját a "GUID" oszlophoz, és a "felhasználó" oszlop értékeit nagybetűvé alakítja. Az egyéni kivonók bonyolultabb eredményeket hozhatnak létre a bemeneti adatok elemzésével és a kezelésével.

A következő olyan alap U-SQL-szkript, amely egyéni kivonót használ:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-outputters"></a>Felhasználó által definiált leszerkesztők használata
A felhasználó által definiált egy másik U-SQL UDO, amely lehetővé teszi a beépített U-SQL-funkciók kiterjesztését. A kivonóhoz hasonlóan több beépített kimenet is található.

* Kimenetek *. Text ()*: a különböző kódolású szövegfájlba írja az adatot.
* *Outputters.Csv ()*: a különböző kódolású, vesszővel tagolt (CSV) fájlokba írja az adatok értékét.
* Kimenetek *. TSV ()*: a különböző kódolású, tabulátorokkal tagolt (TSV) fájlokba írja az adatok.

Az egyéni leválasztó lehetővé teszi, hogy egyéni formátumban írja az adatbevitelt. Ez a következő feladatokhoz lehet hasznos:

* Adat írása részben strukturált vagy strukturálatlan fájlokra.
* Az adatírás nem támogatott kódolást eredményez.
* Kimeneti adatokat módosíthat vagy egyéni attribútumokat adhat hozzá.

A felhasználó által definiált megadáshoz létre kell hozni a `IOutputter` felületet.

Az `IOutputter` alaposztály implementációja a következő:

```csharp
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

A kijelölő összes bemeneti paraméterét, például az oszlop/sorok határolóit, a kódolást és így tovább, az osztály konstruktorában kell meghatározni. Az `IOutputter` illesztőfelületnek tartalmaznia kell a felülbírálás definícióját is `void Output` . Az attribútum `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` beállítható a atomi fájlok feldolgozásához. További információ: az alábbi részletek.

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output`minden bemeneti sorhoz meghívva. Visszaadja a `IUnstructuredWriter output` sorhalmazt.
* A konstruktor osztály paraméterek átadására szolgál a felhasználó által definiált leszerkesztőhöz.
* `Close`a nem kötelezően felülbírálja a költséges állapotot, vagy meghatározhatja az utolsó sor megírását.

A **SqlUserDefinedOutputter** attribútum azt jelzi, hogy a típust felhasználó által definiált előtagjaként kell regisztrálni. Ez az osztály nem örökölhető.

A SqlUserDefinedOutputter egy nem kötelező attribútum a felhasználó által definiált leválasztási definícióhoz. A AtomicFileProcessing tulajdonság definiálására szolgál.

* bool AtomicFileProcessing   

* **igaz** = azt jelzi, hogy ez a kijelölő atomi kimeneti fájlokat igényel (JSON, XML,...)
* **false** = azt jelzi, hogy ez a leválasztó képes kezelni a felosztott/elosztott fájlokat (CSV, SEQ,...)

A fő programozható objektumok a **sor** és a **kimenet**. A **sor** objektum a kimeneti adatokat illesztőfelületként való enumerálásra használja `IRow` . A **kimenet** a célfájl kimeneti értékének megadására szolgál.

A kimeneti adatokat a felületén keresztül érheti el a rendszer `IRow` . A kimeneti adatokat egyszerre egy sor adja át.

Az egyes értékeket a rendszer a IRow felület Get metódusának meghívásával sorolja fel:

```csharp
row.Get<string>("column_name")
```

Az egyes oszlopnevek az alábbiak meghívásával határozhatók meg `row.Schema` :

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Ez a megközelítés lehetővé teszi, hogy rugalmasan hozzon létre egy metaadat-sémát.

A kimeneti adatokat fájlba kell írni a használatával `System.IO.StreamWriter` . A stream paraméter a (z `output.BaseStream` ) részeként van beállítva `IUnstructuredWriter output` .

Fontos megjegyezni, hogy az adatpuffert az egyes sorok iterációja után ki kell üríteni a fájlba. Emellett az `StreamWriter` objektumot az engedélyezett rendelkezésre álló attribútummal (alapértelmezett) és a **using** kulcsszóval kell használni:

```csharp
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Ellenkező esetben a flush () metódus explicit módon meghívja az egyes iterációkat. Ezt a következő példában mutatjuk be.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Fejlécek és láblécek beállítása a felhasználó által definiált kiállítók számára
A fejlécek beállításához használjon egy iterációs végrehajtási folyamatot.

```csharp
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

Az első `if (isHeaderRow)` blokkban lévő kódot csak egyszer hajtja végre a rendszer.

A lábléchez használja az objektum () példányának hivatkozását `System.IO.Stream` `output.BaseStream` . Írja be a láblécet az interfész Bezárás () metódusában `IOutputter` .  (További információ a következő példában található.)

A következő példa egy felhasználó által definiált leszerkesztőt mutat be:

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

És U-SQL alap parancsfájl:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Ez egy HTML-fájl, amely egy táblázatos adattal rendelkező HTML-fájlt hoz létre.

### <a name="call-outputter-from-u-sql-base-script"></a>A U-SQL alap parancsfájlból származó
Ha az alapszintű U-SQL-parancsfájlból szeretne egy egyéni leírókat meghívni, létre kell hozni a kibővített objektum új példányát.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Ha el szeretné kerülni az objektum példányának létrehozását az alapparancsfájlban, létrehozhat egy függvény burkolót, ahogy az a korábbi példában is látható:

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

Ebben az esetben az eredeti hívás a következőhöz hasonlóan néz ki:

```usql
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Felhasználó által definiált processzorok használata
A felhasználó által definiált processzor vagy UDP a U-SQL UDO egyik típusa, amely lehetővé teszi a bejövő sorok feldolgozását a programozható funkciók alkalmazásával. Az UDP lehetővé teszi az oszlopok egyesítését, az értékek módosítását, és szükség esetén új oszlopok hozzáadását. Alapvetően segít a sorhalmaz feldolgozása a szükséges adatelemek létrehozásához.

Az UDP definiálásához létre kell hozni egy `IProcessor` illesztőfelületet az `SqlUserDefinedProcessor` attribútummal, amely nem kötelező az UDP számára.

Az illesztőfelületnek tartalmaznia kell a `IRow` kapcsolati sorhalmaz felülbírálásának definícióját, ahogy az az alábbi példában is látható:

```csharp
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

A **SqlUserDefinedProcessor** azt jelzi, hogy a típust felhasználó által definiált processzorként kell regisztrálni. Ez az osztály nem örökölhető.

Az SqlUserDefinedProcessor attribútum nem **kötelező** az UDP-definícióhoz.

A fő programozható objektumok **bemenetek** és **kimenetek**. A bemeneti objektum a bemeneti oszlopok és kimenetek enumerálására és a kimeneti adatok beállítására szolgál a processzor tevékenységének eredményeképpen.

A bemeneti oszlopok enumerálásához a `input.Get` metódust használjuk.

```csharp
string column_name = input.Get<string>("column_name");
```

A metódus paramétere a `input.Get` `PRODUCE` `PROCESS` U-SQL alapparancsfájl utasításának részeként átadott oszlop. Itt a megfelelő adattípust kell használnia.

A kimenethez használja a `output.Set` metódust.

Fontos megjegyezni, hogy az egyéni gyártó csak a metódus hívásával definiált oszlopokat és értékeket jeleníti meg `output.Set` .

```csharp
output.Set<string>("mycolumn", mycolumn);
```

A processzor tényleges kimenete a hívásával aktiválódik `return output.AsReadOnly();` .

A következő példa egy processzort mutat be:

```csharp
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

Ebben a használati helyzetben a processzor egy "full_description" nevű új oszlopot hoz létre a meglévő oszlopok kombinálásával – ebben az esetben a "felhasználó" a nagybetűs és a "des". Emellett újra létrehozza a GUID azonosítót, és visszaadja az eredeti és az új GUID-értéket.

Ahogy az előző példában is látható, meghívhatja a C# metódusokat a `output.Set` metódus hívása során.

Az alábbi példa olyan alap U-SQL-parancsfájlt mutat be, amely egyéni processzort használ:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-appliers"></a>Felhasználó által definiált beszolgáltatók használata
Egy U-SQL felhasználó által definiált szolgáltató lehetővé teszi egy egyéni C#-függvény meghívását minden olyan sorhoz, amelyet a lekérdezés külső tábla kifejezése adott vissza. A rendszer kiértékeli a megfelelő bemenetet a bal oldali bemenet minden egyes sorára, és a létrehozott sorok a végső kimenethez kombinálhatók. Az APPLY operátor által létrehozott oszlopok listája a bal oldali és a jobb oldali bemenet oszlopainak kombinációja.

A felhasználó által definiált bejelentkezőt a USQL SELECT kifejezés részeként kell meghívni.

A felhasználó által definiált betekintő a következőhöz hasonlóan néz ki:

```usql
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Ha további információt szeretne arról, hogyan használhatók a megjelölések a SELECT kifejezésben, tekintse meg a [U-SQL kiválasztási lehetőséget a Cross Apply és a Outer alkalmazásban](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply).

A felhasználó által megadott, a következő alaposztály definíciója a következő:

```csharp
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Felhasználó által definiált hozzárendelő megadásakor létre kell hoznia a `IApplier` felületet a [ `SqlUserDefinedApplier` ] attribútummal, amely nem kötelező a felhasználó által definiált hozzárendelt-definícióhoz.

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* A rendszer a külső tábla minden egyes sorára alkalmazza a kérelmet. A `IUpdatableRow` kimeneti sorhalmazt adja vissza.
* A konstruktor osztály paraméterek átadására szolgál a felhasználó által megadott előfizetéshez.

A **SqlUserDefinedApplier** azt jelzi, hogy a típust felhasználó által definiált fogadóként kell regisztrálni. Ez az osztály nem örökölhető.

A felhasználó által definiált **SqlUserDefinedApplier** megadása nem **kötelező** .


A fő programozható objektumok a következők:

```csharp
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

A bemeneti sorhalmazok bemenetként lesznek átadva `IRow` . A kimeneti sorok `IUpdatableRow` kimeneti illesztőfelületként jönnek létre.

Az egyes oszlopnevek meghatározása a séma metódusának meghívásával lehetséges `IRow` .

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

A bejövő adatok tényleges értékének lekéréséhez `IRow` használja a Get () metódust az `IRow` illesztőfelületen.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Vagy a séma oszlop nevét használjuk:

```csharp
row.Get<int>(row.Schema[0].Name)
```

A kimeneti értékeket a `IUpdatableRow` kimenettel kell beállítani:

```csharp
output.Set<int>("mycolumn", mycolumn)
```

Fontos tisztában lennie azzal, hogy az egyéni használóknak csak a metódus hívásával megadott kimeneti oszlopokat és értékeket kell megadniuk `output.Set` .

A tényleges kimenet meghívásával aktiválódik `yield return output.AsReadOnly();` .

A felhasználó által megadott, a kivitelezőnek átadandó paraméterek a konstruktornak adhatók át. A berendelő olyan változó oszlopok számát adja vissza, amelyeket a rendszer az alap U-SQL-parancsfájlban definiált, a beléptetési hívás során meg kell határozni.

```csharp
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

A felhasználó által definiált példa a következő:

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

A következő a felhasználó által definiált előtagja számára az alap U-SQL-szkript:

```usql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Ebben a használati esetben a felhasználó által definiált szolgáltató vesszővel tagolt érték-elemzőként működik az autó-flotta tulajdonságainál. A bemeneti fájl sorai a következőhöz hasonlóan néznek ki:

```text
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Ez egy tipikus, tabulátorral tagolt TSV-fájl, amely egy olyan tulajdonságlapot tartalmaz, amely az autó tulajdonságait, például a make és a Model tulajdonságot tartalmazza. Ezeket a tulajdonságokat a táblázat oszlopaiba kell elemezni. A megadott szolgáltató azt is lehetővé teszi, hogy dinamikus számú tulajdonságot hozzon az eredmény-sorhalmazban az átadott paraméter alapján. Létrehozhatja az összes tulajdonságot, illetve a tulajdonságok egy adott halmazát is.

```text
...USQL_Programmability.ParserApplier ("all")
...USQL_Programmability.ParserApplier ("make")
...USQL_Programmability.ParserApplier ("make&model")
```

A felhasználó által definiált berendelő a (z) rendszerű alkalmazás új példányának hívható:

```usql
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Vagy egy burkoló gyári módszer meghívásával:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Felhasználó által definiált kombinálók használata
A felhasználó által definiált Combiner vagy UDC lehetővé teszi, hogy egyéni logika alapján egyesítse a bal és a jobb oldali sorhalmaz sorait. A felhasználó által definiált kombinált szolgáltatás kombináló kifejezéssel van használatban.

A rendszer összevonást hív meg a COMBINing kifejezéssel, amely biztosítja a szükséges információkat a bemeneti sorhalmazokkal, a csoportosítási oszlopokkal, a várt eredmény sémával és további információkkal.

A következő szintaxissal hívhat egy Combiner-et egy Base U-SQL-parancsfájlban:

```usql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

További információ: [Combine Expression (U-SQL)](/u-sql/statements-and-expressions/combine-expression).

Felhasználó által definiált kombinálás definiálásához létre kell hozni a `ICombiner` felületet a [ `SqlUserDefinedCombiner` ] attribútummal, amely nem kötelező a felhasználó által definiált Combiner-definíció esetében.

`ICombiner`Alaposztály definíciója:

```csharp
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

Egy felület egyéni implementációjában `ICombiner` szerepelnie kell egy `IEnumerable<IRow>` kombinált felülbírálás definíciójának.

```csharp
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

A **SqlUserDefinedCombiner** attribútum azt jelzi, hogy a típust felhasználó által definiált kombináló kell regisztrálni. Ez az osztály nem örökölhető.

A **SqlUserDefinedCombiner** a Combiner Mode tulajdonság definiálására szolgál. A felhasználó által definiált Combiner-definíció opcionális attribútuma.

CombinerMode mód

A CombinerMode Enum a következő értékeket veheti fel:

* A teljes (0) minden kimeneti sor a bal és a jobb oldali bemeneti soroktól függ.

* A bal oldali (1) minden kimeneti sor a bal oldali bemeneti sorból (és a jobb oldali, azonos kulccsal rendelkező sorokból) függ.

* A jobb oldali (2) minden kimeneti sor a jobb oldaliból egy bemeneti sorból, a bal oldalitól pedig az azonos Key értékkel rendelkező soroktól függ.

* A belső (3) minden kimeneti sor a bal és a jobb oldali, azonos értékű bemeneti sorból függ.

Példa: [ `SqlUserDefinedCombiner(Mode=CombinerMode.Left)` ]


A fő programozható objektumok a következők:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

A bemeneti sorhalmazok a **bal** és a **jobb oldali** illesztőfelület-típus szerint lesznek átadva `IRowset` . Mindkét sorhalmazt fel kell sorolni a feldolgozáshoz. Az egyes felületeket csak egyszer lehet enumerálni, ezért szükség esetén fel kell sorolni és gyorsítótárazni kell.

Gyorsítótárazási célokra létrehozhatunk egy \<T\> adattípust a LINQ-lekérdezés végrehajtásának eredményeképpen, különösen<`IRow`>. A névtelen adattípus az enumerálás során is használható.

A IEnumerable felülettel kapcsolatos további információkért lásd: a [LINQ-lekérdezések bemutatása (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) . További információ a LINQ-lekérdezésekről és a [IEnumerable \<T\> felületről](/dotnet/api/system.collections.generic.ienumerable-1) \<T\> .

A bejövő adatok tényleges értékének lekéréséhez `IRowset` használja a Get () metódust az `IRow` illesztőfelületen.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Az egyes oszlopnevek meghatározása a séma metódusának meghívásával lehetséges `IRow` .

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Vagy a séma oszlopának neve alapján:

```csharp
c# row.Get<int>(row.Schema[0].Name)
```

A LINQ-sel való általános számbavétel a következőhöz hasonlóan néz ki:

```csharp
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Mindkét sorhalmaz enumerálása után az összes sort át fogjuk venni. A bal oldali sorhalmaz minden egyes sorában megtalálja az összes olyan sort, amely megfelel a kombinációnk feltételének.

A kimeneti értékeket a kimenet értékkel kell beállítani `IUpdatableRow` .

```csharp
output.Set<int>("mycolumn", mycolumn)
```

A tényleges kimenet a következő hívásával aktiválódik: `yield return output.AsReadOnly();` .

A következő egy kombináló példa:

```csharp
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

Ebben a használati helyzetben a kiskereskedelmi elemzési jelentést készítünk. A cél az, hogy megkeresse az összes olyan terméket, amely több mint $20 000, és hogy a webhelyhez képest gyorsabb, mint a normál kiskereskedő egy adott időszakon belül.

Itt látható az alap U-SQL-szkript. Összehasonlíthatja a logikát egy normál illesztés és egy kombináló között:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

A felhasználó által definiált kombináció hívható a (z) objektum új példánya:

```csharp
USING new MyNameSpace.MyCombiner();
```


Vagy egy burkoló gyári módszer meghívásával:

```csharp
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Felhasználó által definiált szűkítők használata

Az U-SQL lehetővé teszi egyéni sorhalmaz-szűkítők írását a C# nyelvben a felhasználó által definiált kezelői keretrendszer használatával és egy IReducer felület megvalósításával.

A felhasználó által definiált szűkítő vagy UDR felhasználható a szükségtelen sorok eltávolítására az kinyeréskor (importáláskor). Emellett a sorok és oszlopok kezelésére és értékelésére is használható. A programozható logika alapján azt is meghatározhatja, hogy mely sorokat kell kinyerni.

UDR osztály definiálásához egy `IReducer` opcionális attribútummal rendelkező felületet kell létrehozni `SqlUserDefinedReducer` .

Ez az osztály-illesztőfelületnek tartalmaznia kell egy definíciót a `IEnumerable` kapcsolati sorhalmaz felülbírálásához.

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

A **SqlUserDefinedReducer** attribútum azt jelzi, hogy a típust felhasználó által definiált szűkítőként kell regisztrálni. Ez az osztály nem örökölhető.
A **SqlUserDefinedReducer** egy felhasználó által definiált csökkentő definíció opcionális attribútuma. A IsRecursive tulajdonság definiálására szolgál.

* bool IsRecursive    
* **true** = azt jelzi, hogy ez a szűkítő asszociatív és kommutatív

A fő programozható objektumok **bemenetek** és **kimenetek**. A bemeneti objektum a bemeneti sorok enumerálására szolgál. A kimenet a tevékenységek csökkentésének eredményeképpen kimeneti sorok beállítására szolgál.

A bemeneti sorok enumerálásakor a `Row.Get` metódust használjuk.

```csharp
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

A metódus paramétere a `Row.Get` `PRODUCE` `REDUCE` U-SQL alap parancsfájl utasításának részeként átadott oszlop. Itt is a megfelelő adattípust kell használnia.

A kimenethez használja a `output.Set` metódust.

Fontos megérteni, hogy az egyéni csökkentő csak a metódus hívásával megadott értékeket adja meg `output.Set` .

```csharp
output.Set<string>("mycolumn", guid);
```

A tényleges csökkentő kimenet a hívásával aktiválódik `yield return output.AsReadOnly();` .

A következő egy rövidített példa:

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

Ebben a használati helyzetben a csökkentő kihagyja a sorokat üres felhasználónévvel. A sorhalmaz minden egyes sorában beolvassa az egyes szükséges oszlopokat, majd kiértékeli a Felhasználónév hosszát. A tényleges sort csak akkor adja eredményül, ha a Felhasználónév értéke 0-nál nagyobb.

A következő olyan alap U-SQL-szkript, amely egyéni szűkítőt használ:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```
