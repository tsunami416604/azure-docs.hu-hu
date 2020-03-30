---
title: U-SQL programozhatósági útmutató az Azure Data Lake-hez
description: Ismerje meg az Azure Data Lake Analytics szolgáltatásainak készletét, amelyek lehetővé teszik egy felhőalapú big data platform létrehozását.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: dc55615d7a5c6ae9a393ed4fd5f49cd92aedc0f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73162583"
---
# <a name="u-sql-programmability-guide"></a>U-SQL programozhatósági útmutató

Az U-SQL egy olyan lekérdezési nyelv, amelyet big data-típusú számítási feladatokhoz terveztek. Az U-SQL egyik egyedi jellemzője az SQL-szerű deklaratív nyelv és a C# által biztosított bővíthetőség és programozhatóság kombinációja. Ebben az útmutatóban a C# által engedélyezett U-SQL nyelv bővíthetőségére és programozhatóságára összpontosítunk.

## <a name="requirements"></a>Követelmények

Töltse le és telepítse [az Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)alkalmazást.

## <a name="get-started-with-u-sql"></a>U-SQL – első lépések  

Tekintse meg a következő U-SQL parancsfájlt:

```
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

Ez a parancsfájl két `@a` sorkészletet határoz meg: és `@results`. A `@results` RowSet a `@a`programból van definiálva.

## <a name="c-types-and-expressions-in-u-sql-script"></a>C# típusok és kifejezések az U-SQL parancsfájlban

Az U-SQL kifejezés egy C# kifejezés, amely az `AND` `OR`U-SQL logikai műveletekkel kombinálva , és `NOT`. Az U-SQL kifejezések a SELECT, EXTRACT, WHERE, HAVING, GROUP BY és DECLARE kifejezésekkel használhatók. A következő parancsfájl például datetime értékként elemez egy karakterláncot.

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

A következő kódrészlet datetime értékként egy DECLARE utasításban datetime értékként elemez.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>C# kifejezések használata adattípus-konverziókhoz

A következő példa bemutatja, hogyan lehet csinálni egy datetime adatkonverzió t c# kifejezések használatával. Ebben a konkrét esetben a karakterlánc-datetime adatok at konvertálja a normál datetime éjfélkor 00:00:00 időjelöléssel.

```
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

### <a name="use-c-expressions-for-todays-date"></a>C# kifejezések használata a mai dátumhoz

A mai dátum lehúzásához a következő C# kifejezést használhatjuk:`DateTime.Now.ToString("M/d/yyyy")`

Íme egy példa arra, hogyan használhatja ezt a kifejezést parancsfájlban:

```
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

Az U-SQL bővíthetőségi modellje nagymértékben függ a .NET szerelvényekegyéni kódjának hozzáadásától. 

### <a name="register-a-net-assembly"></a>.NET kódösszeállítás regisztrálása

Az `CREATE ASSEMBLY` utasítás segítségével .NET kódösszeállítást helyezhet el U-SQL adatbázisban. Ezt követően az U-SQL-parancsfájlok használhatják ezeket az összeállításokat az `REFERENCE ASSEMBLY` utasítás használatával. 

A következő kód bemutatja, hogyan kell regisztrálni egy szerelvényt:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

A következő kód bemutatja, hogyan lehet hivatkozni egy szerelvényre:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Tekintse meg a [szerelvény regisztrációs utasításokat,](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) amely részletesebben ismerteti ezt a témakört.


### <a name="use-assembly-versioning"></a>Összeállításverzió-verzióhasználata
Az U-SQL jelenleg a . Ezért győződjön meg arról, hogy a saját szerelvények kompatibilisek a futásidejű verzióval.

Ahogy korábban említettük, az U-SQL 64 bites (x64) formátumban futtatja a kódot. Ezért győződjön meg arról, hogy a kód le van fordítva az x64-es futtatáshoz. Ellenkező esetben a korábban megjelenített helytelen formátumhibát kapja.

Minden feltöltött összeállítási DLL- és erőforrásfájl, például egy másik futásidő, egy natív összeállítás vagy egy konfigurációs fájl legbőlegelhető. Az üzembe helyezett erőforrások teljes mérete, akár a DEPLOY RESOURCE,akár az összeállításokra és azok további fájljaira mutató hivatkozásokon keresztül, nem haladhatja meg a 3 GB-ot.

Végül vegye figyelembe, hogy minden U-SQL-adatbázis csak egy adott kódösszeállítás egy verzióját tartalmazhatja. Ha például a NewtonSoft Json.NET könyvtár 7-es és 8-as verziójára is szüksége van, két különböző adatbázisban kell regisztrálnia őket. Továbbá minden parancsfájl csak egy adott kódösszeállítás-DLL egy verziójára hivatkozhat. Ebben a tekintetben az U-SQL követi a C# összeállítása kezelése és verziószámozás szemantikája.

## <a name="use-user-defined-functions-udf"></a>Felhasználó által definiált függvények használata: UDF
Az U-SQL felhasználó által definiált függvények (UDF) olyan programozási rutinok, amelyek elfogadják a paramétereket, végrehajtanak egy műveletet (például egy összetett számítást), és a művelet eredményét értékként adják vissza. Az UDF visszatérési értéke csak egyetlen skaláris lehet. U-SQL UDF lehet hívni az U-SQL alapscript, mint bármely más C # skalárfüggvény.

Azt javasoljuk, hogy az U-SQL felhasználó által definiált függvényeket **nyilvános** és **statikusként**inicializálja.

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

Először nézzük meg az UDF létrehozásának egyszerű példáját.

Ebben a használati esetben meg kell határozni a pénzügyi időszak, beleértve a pénzügyi negyedévben és a pénzügyi hónap az első bejelentkezés az adott felhasználó számára. Az év első pénzügyi hónapja a mi forgatókönyvünkben június.

A pénzügyi időszak kiszámításához a következő C# függvényt vezetjük be:

```
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

Egyszerűen kiszámítja a pénzügyi hónapot és negyedévet, és karakterlánc-értéket ad vissza. Júniusban, az első pénzügyi negyedév első hónapjában a "Q1:P1" szót használjuk. Júliusban a "Q1:P2"-t használjuk, és így tovább.

Ez egy rendszeres C# függvény, amelyet u-SQL projektünkben fogunk használni.

Ebben az esetben a következőképpen jelenik meg a háttérkódszakasz:

```
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

Most fogjuk hívni ezt a függvényt az alap U-SQL script. Ehhez meg kell adnunk egy teljesen minősített nevet a függvénynek, beleértve a névteret is, amely ebben az esetben a NameSpace.Class.Function(parameter).

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

A következőkben a tényleges U-SQL alapscript:

```
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

A parancsfájl-végrehajtás kimeneti fájlja a következők:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Ez a példa bemutatja a beépített UDF egyszerű használatát az U-SQL-ben.

### <a name="keep-state-between-udf-invocations"></a>Állapot megtartása az UDF-hívások között
Az U-SQL C# programozhatóságú objektumok kifinomultabbak lehetnek, kihasználva az interaktivitást a kód mögötti globális változókon keresztül. Nézzük meg a következő üzleti használati eset forgatókönyv.

A nagy szervezetekben a felhasználók válthatnak a belső alkalmazások fajtái között. Ezek közé tartozhat a Microsoft Dynamics CRM, a PowerBI és így tovább. Előfordulhat, hogy az ügyfelek telemetriai elemzést szeretnének alkalmazni arról, hogy a felhasználók hogyan váltanak a különböző alkalmazások között, mik a használati trendek és így tovább. A cél az üzleti optimalizálása alkalmazás használat. Azt is érdemes kombinálni a különböző alkalmazások vagy konkrét bejelentkezési rutinok.

E cél elérése érdekében meg kell határoznunk a munkamenet-azonosítókat és az utolsó munkamenet közötti időeltolódást.

Meg kell találnunk egy korábbi bejelentkezést, és ezt a bejelentkezést hozzá kell rendelnünk az ugyanahhoz az alkalmazáshoz létrehozott összes munkamenethez. Az első kihívás az, hogy az U-SQL alapszkript nem teszi lehetővé számunkra, hogy számításokat alkalmazzunk a MÁR kiszámított oszlopokra a LAG funkcióval. A második kihívás az, hogy az adott munkamenetet az összes munkamenetre vonatkozóan ugyanabban az időszakban kell tartanunk.

A probléma megoldásához egy globális változót használunk `static public string globalSession;`egy háttérkód-szakaszon belül: .

Ez a globális változó a parancsfájl végrehajtása során a teljes sorhalmazra vonatkozik.

Itt van a kód mögött szakasza u-SQL program:

```
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

Ez a példa `static public string globalSession;` a függvényen belül használt globális változót mutatja be, és a `getStampUserSession` Munkamenet paraméter minden módosításakor újrainicializálódik.

Az U-SQL alapparancsfájl a következő:

```
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

A `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` függvényt itt hívják a második memóriasorhalmaz számítása során. Átmegy az `UserSessionTimestamp` oszlopon, és `UserSessionTimestamp` az értéket ad vissza, amíg meg nem változik.

A kimeneti fájl a következő:

```
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

Ez a példa egy bonyolultabb használati eset forgatókönyv, amelyben egy globális változó belül egy kód mögötti szakasz, amely a teljes memória sorhalmaz.

## <a name="use-user-defined-types-udt"></a>Felhasználó által definiált típusok használata: UDT
A felhasználó által definiált típusok (UDT) az U-SQL egy másik programozhatósági szolgáltatása. U-SQL UDT működik, mint egy normál C# felhasználó által definiált típus. A C# egy erősen beírt nyelv, amely lehetővé teszi a beépített és egyéni felhasználó által definiált típusok használatát.

Az U-SQL nem tudja implicit módon szerializálni vagy deserialize tetszőleges UDT-k, ha az UDT át kerül a csúcspontok között a sorhalmazokban. Ez azt jelenti, hogy a felhasználónak az IFormatter felület használatával explicit formatter-t kell megadnia. Ez biztosítja az U-SQL az UDT szerializálására és szerializálására szolgáló módszereket.

> [!NOTE]
> Az U-SQL beépített elszívói és outputterek jelenleg nem tudják szerializálni vagy deszeriszkíteni az UDT-adatokat a fájlokba vagy fájlokból még az IFormatter készlettel sem. Tehát, ha udt adatokat ír egy fájlt a OUTPUT utasítást, vagy olvassa el egy kivonó, meg kell adni, mint egy karakterlánc vagy bájt tömb. Ezután explicit módon hívja meg a szerializálási és deszerializálási kódot (azaz az UDT ToString() metódusát). A felhasználó által definiált elszívók és kimenetek viszont képesek UDT-k olvasására és írására.

Ha megpróbáljuk használni UDT extractor vagy OUTPUTTER (ki az előző SELECT), mint itt látható:

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

A következő hibaüzenetet kapjuk:

```
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Az UDT-vel való munkához a outputterben szerializálnunk kell a ToString() metódussal való karakterláncba, vagy létre kell hoznunk egy egyéni outputtert.

Az UDT-k jelenleg nem használhatók a GROUP BY-ben. Ha udt-t használ a GROUP BY-ben, a következő hiba jelenik meg:

```
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

Az UDT meghatározásához a következőket kell tennünk:

* Adja hozzá a következő névtereket:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Add `Microsoft.Analytics.Interfaces`, amely szükséges az UDT-összeköttetések. Ezenkívül `System.IO` szükség lehet az IFormatter felület definiálására is.

* Definiáljon egy használt-definiált típust az SqlUserDefinedType attribútummal.

**Az SqlUserDefinedType** segítségével egy kódösszeállításban egy típusdefiníciót felhasználó által definiált típusként (UDT) jelöl meg az U-SQL-ben. Az attribútum tulajdonságai az UDT fizikai jellemzőit tükrözik. Ez az osztály nem örökölhető.

Az SqlUserDefinedType az UDT-definíció kötelező attribútuma.

Az osztály konstruktora:  

* SqlUserDefinedTypeAttribute (típus formatter)

* Típus darab: UdT formatter definiálásához szükséges paraméter – pontosabban az `IFormatter` összeköttetés típusát itt kell átadni.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* A tipikus UDT-hez az IFormatter interfész is szükséges, amint az a következő példában látható:

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

A `IFormatter` felület szerializálja és deszerializálja az \<objektumdiagramot a typeparamref name="T"> gyökértípusával.

\<typeparam name="T">Az objektumdiagram szerializálásának és desorosizálásának gyökértípusa.

* **Deszerializálás :** De-szerializálja a megadott adatfolyamon lévő adatokat, és újraalkotja az objektumok grafikonját.

* **Szerializálás:** Szerializálja az objektumokat vagy az objektumok grafikonját úgy, hogy a megadott gyökér a megadott adatfolyamhoz.

`MyType`példa: A típus példánya.  
`IColumnWriter`író `IColumnReader` / olvasó: A mögöttes oszlop patak.  
`ISerializationContext`context: Enum, amely olyan jelzőket határoz meg, amely a szerializálás során megadja az adatfolyam forrás- vagy célkörnyezetét.

* **Köztes**: Azt adja meg, hogy a forrás- vagy célkörnyezet nem megőrzött tároló.

* **Adatmegőrzés :** Megadja, hogy a forrás- vagy célkörnyezet egy megőrzött tároló.Persistence : Specifies that the source or destination context is a persisted store.

Rendszeres C# típusként az U-SQL UDT-definíció felülbírálásokat tartalmazhat az operátorok számára, például a +/==/!=. Statikus metódusok is lehetnek. Ha például ezt az UDT-t egy U-SQL MIN összesítő függvény paramétereként fogjuk használni, meg kell határoznunk < operátor felülbírálása.

Az útmutató korábbi részében bemutattunk egy példát a pénzügyi `Qn:Pn (Q1:P10)`időszak azonosítására az adott dátumtól kezdve a formátumban. A következő példa bemutatja, hogyan definiálható egyéni típus a pénzügyi időszak értékeihez.

Az alábbiakban egy példa egy kód mögött szakasz egyéni UDT és IFormatter felület:

```
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

A megadott típus két számot tartalmaz: negyedév et és hónapot. A `==/!=/>/<` kezelők `ToString()` és a statikus metódus itt vannak meghatározva.

Ahogy korábban említettük, az UDT használható SELECT kifejezésekben, de nem használható outputter/extractor egyéni szerializálás nélkül. Vagy szerializálandó, mint `ToString()` egy karakterlánc, vagy használt egyéni OUTPUTTER / extractor.

Most beszéljünk az UDT használatáról. A háttérkódszakaszban a GetFiscalPeriod függvényt a következőkre változtattuk:

```
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

Amint láthatja, a FiscalPeriod típus értékét adja vissza.

Itt egy példát, hogyan kell használni tovább U-SQL alap script. Ez a példa az UdT-meghívás különböző formáit mutatja be az U-SQL parancsfájlból.

```
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

Íme egy példa a teljes háttérkódszakaszra:

```
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
        }



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

## <a name="use-user-defined-aggregates-udagg"></a>Felhasználó által definiált aggregátumok használata: UDAGG
A felhasználó által definiált összesítések olyan összesítéssel kapcsolatos függvények, amelyek et nem szállítanak az U-SQL-rel. A példa lehet egy összesítés egyéni matematikai számítások, karakterlánc-összefűzések, karakterláncokkal végzett műveletek és így tovább.

A felhasználó által definiált összesített alaposztály-definíció a következő:

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

**Az SqlUserDefinedAggregate** azt jelzi, hogy a típust felhasználó által definiált összesítésként kell regisztrálni. Ez az osztály nem örökölhető.

Az SqlUserDefinedType attribútum **nem kötelező** az UDAGG-definícióhoz.


Az alaposztály lehetővé teszi, hogy három absztrakt paramétert adjon át: kettőt bemeneti paraméterként és egyet eredményként. Az adattípusok változóak, és az osztályöröklés során kell definiálni őket.

```
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

* **Az Init** a számítás során minden csoporthoz egyszer hív meg. Minden aggregációs csoporthoz inicializálási rutint biztosít.  
* **Az abnak a** függvény minden egyes értékhez egyszer kerül végrehajtásra. Ez biztosítja az aggregációs algoritmus fő funkcióit. Az értékek összesítésére használható különböző adattípusokkal, amelyek az osztályöröklés során vannak definiálva. A változó adattípusok két paraméterét is elfogadja.
* **A lebontás** a feldolgozás végén a feldolgozás végén a feldolgozás végén egyszer kerül végrehajtásra, hogy az egyes csoportok eredményének kimenete.

A helyes bemeneti és kimeneti adattípusok deklarálásához használja az osztálydefiníciót az alábbiak szerint:

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Az első felhalmozandó paraméter
* T2: A második felhalmozandó paraméter
* TResult: A leáll visszatérési típusa

Példa:

```
public class GuidAggregate : IAggregate<string, int, int>
```

vagy

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>UdAGG használata az U-SQL-ben
Az UDAGG használatához először definiálja azt a háttérkódban, vagy hivatkozzon rá a meglévő programozhatósági DLL-ből, amint azt korábban tárgyaltuk.

Ezután használja a következő szintaxist:

```
AGG<UDAGG_functionname>(param1,param2)
```

Íme egy példa az UDAGG-ra:

```
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

És alap U-SQL script:

```
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

Ebben a használati esetben az adott felhasználók osztály GUID azonosítóit fűzzük össze.

## <a name="use-user-defined-objects-udo"></a>Felhasználó által definiált objektumok használata: UDO
Az U-SQL lehetővé teszi egyéni programozhatósági objektumok definiálását, amelyeket felhasználó által definiált objektumoknak vagy UDO-nak neveznek.

Az alábbi lista az UDO-t tartalmazza az U-SQL-ben:

* Felhasználó által definiált elszívók
    * Kibontás soronként
    * Egyéni strukturált fájlokból történő adatkinyerés megvalósításához használható

* Felhasználó által definiált outputterek
    * Kimeneti soronként
    * Egyéni adattípusok vagy egyéni fájlformátumok kimenetére szolgál

* Felhasználó által definiált processzorok
    * Vegyünk egy sort, és készítsen egy sort
    * Az oszlopok számának csökkentésére vagy új oszlopok létrehozására szolgál, meglévő oszlopkészletből származó értékekkel

* Felhasználó által definiált appliers
    * Vegyünk egy sort, és készítsen 0-n sorok
    * Külső/KERESZT APPLY-kel használható

* Felhasználó által definiált kombájnok
    * Sorokkészleteket egyesít – felhasználó által definiált JOIN-okat

* Felhasználó által definiált szűkítők
    * Vegyünk n sorokat, és készítsen egy sort
    * A sorok számának csökkentésére szolgál

Az UDO-t általában explicit módon nevezik meg az U-SQL parancsfájlban a következő U-SQL utasítások részeként:

* KINYERÉS
* Kimeneti
* Folyamat
* Össze
* Csökkenti

> [!NOTE]  
> UDO's korlátozódik fogyasztani 0.5Gb memória.  Ez a memóriakorlátozás nem vonatkozik a helyi végrehajtásokra.

## <a name="use-user-defined-extractors"></a>Felhasználó által definiált kivonók használata
Az U-SQL lehetővé teszi külső adatok importálását kivonatutasítás használatával. Az EXTRACT utasítás beépített UDO-elszívók at használhat:  

* *Extractors.Text()*: Különböző kódolású tagolt szövegfájlokból biztosít kibontást.

* *Extractors.Csv()*: Különböző kódolású vesszővel tagolt értékfájlokból (CSV) való kinyerést biztosít.

* *Extractors.Tsv()*: Különböző kódolású tabulátor-elválasztott értékfájlokból (TSV) való kinyerést biztosít.

Hasznos lehet, hogy dolgozzon ki egy egyéni elszívó. Ez az adatimportálás során hasznos lehet, ha az alábbi feladatok valamelyikét szeretnénk elvégezni:

* A bemeneti adatok módosítása oszlopok felosztásával és az egyes értékek módosításával. A PROCESSOR funkció jobb az oszlopok kombinálása.
* Elemezz a strukturálatlan adatokat, például weblapokat és e-maileket, vagy félig strukturálatlan adatokat, például XML/JSON.Parse strstrukturálatlan data such as web us web and web and email, or semi-strukturálatlan data such as XML/JSON.
* Adatok elemzése nem támogatott kódolással.

A felhasználó által definiált elszívó vagy UDE definiálásához létre kell hoznunk egy felületet. `IExtractor` Az elszívó összes bemeneti paraméterét, például az oszlop/sor határolókat és a kódolást az osztály konstruktorában kell meghatározni. Az `IExtractor` interfésznek tartalmaznia kell `IEnumerable<IRow>` a felülbírálás definícióját is az alábbiak szerint:

```
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

Az **SqlUserDefinedExtractor** attribútum azt jelzi, hogy a típust felhasználó által definiált vonógépként kell regisztrálni. Ez az osztály nem örökölhető.

Az SqlUserDefinedExtractor az UDE definíció nem kötelező attribútuma. Az UDE objektum AtomicFileProcessing tulajdonságának definiálására szolgál.

* bool AtomicFileProcessing   

* **true** = Azt jelzi, hogy ez a kivonó atomi bemeneti fájlokat igényel (JSON, XML, ...)
* **false** = Azt jelzi, hogy ez a kivonó képes kezelni osztott / elosztott fájlokat (CSV, SEQ, ...)

A fő UDE programozhatósági objektumok a **bemenetés** **a kimenet.** A bemeneti objektum a bemeneti `IUnstructuredReader`adatok számbavételére szolgál. A kimeneti objektum a kimeneti adatok beállítására szolgál a kinyerhető tevékenység eredményeként.

A bemeneti adatok `System.IO.Stream` a `System.IO.StreamReader`és a programon keresztül érhetők el.

A bemeneti oszlopok számbavétele esetén először egy sorhatároló használatával osztjuk fel a bemeneti adatfolyamot.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Ezután tovább osztja a bemeneti sort oszloprészekre.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

A kimeneti adatok beállításához a `output.Set` módszert használjuk.

Fontos megérteni, hogy az egyéni kiszívó csak a kimenettel definiált oszlopokat és értékeket adja ki. Metódushívás beállítása.

```
output.Set<string>(count, part);
```

A tényleges elszívó kimenetét `yield return output.AsReadOnly();`a hívás váltja ki.

A következőkben az elszívó példa:

```
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

Ebben a használati esetben a kivonó újragenerálja a GUID azonosítót a "guid" oszlophoz, és a "felhasználó" oszlop értékeit nagybetűssé alakítja. Az egyéni kivonók bonyolultabb eredményeket hozhatnak a bemeneti adatok elemzésével és manipulálásával.

A következőkben az alap U-SQL parancsfájl, amely egy egyéni elszívó:

```
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

## <a name="use-user-defined-outputters"></a>Felhasználó által definiált outputters használata
A felhasználó által definiált outputter egy másik U-SQL UDO, amely lehetővé teszi a beépített U-SQL funkciók kiterjesztését. Hasonló a páraelszívó, több beépített outputters.

* *Outputters.Text()*: Adatokat ír különböző kódolású tagolt szövegfájlokba.
* *Outputters.Csv()*: Adatokat ír különböző kódolású vesszővel tagolt értékfájlokba.
* *Outputters.Tsv()*: Adatokat ír különböző kódolású tabulátoralapú (TSV) fájlokba.

Az egyéni kimenetelő lehetővé teszi az adatok egyéni definiált formátumban írását. Ez a következő feladatok esetén lehet hasznos:

* Adatok írása félig strukturált vagy strukturálatlan fájlokba.
* Az adatok írása nem támogatott kódolások.
* Kimeneti adatok módosítása vagy egyéni attribútumok hozzáadása.

A felhasználó által definiált kimenetelő meghatározásához `IOutputter` létre kell hoznunk a felületet.

A következőkben `IOutputter` az alaposztály megvalósítása látható:

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

A outputter összes bemeneti paraméterét, például az oszlop/sor határolókat, a kódolást stb. Az `IOutputter` interfésznek tartalmaznia `void Output` kell egy felülbírálási definíciót is. Az attribútum `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` opcionálisan beállítható atomi fájlfeldolgozáshoz. További információt az alábbi részletekben talál.

```
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

* `Output`minden bemeneti sorhoz meg kell hívni. A sorhalmazt `IUnstructuredWriter output` adja vissza.
* A Konstruktor osztály paraméterek et adhat át a felhasználó által definiált kimenetelőnek.
* `Close`Segítségével felülírhatja a drága állapot felszabadítását, illetve annak meghatározását, hogy mikor volt az utolsó sor írásakor.

**Az SqlUserDefinedOutputter** attribútum azt jelzi, hogy a típust felhasználó által definiált outputterként kell regisztrálni. Ez az osztály nem örökölhető.

Az SqlUserDefinedOutputter egy nem kötelező attribútum egy felhasználó által definiált outputter definícióhoz. Az AtomicFileProcessing tulajdonság definiálására szolgál.

* bool AtomicFileProcessing   

* **true** = Azt jelzi, hogy ehhez a kimenethez atomi kimeneti fájlokra van szükség (JSON, XML, ...)
* **false** = Azt jelzi, hogy ez a kimenet képes kezelni osztott / elosztott fájlokat (CSV, SEQ, ...)

A fő programozhatósági objektumok a **sor** és **a kimenet**. A **sorobjektum** a kimeneti adatok `IRow` interfészként való számbavételére szolgál. **A kimenet** a kimeneti adatok célfájlba való beállítására szolgál.

A kimeneti adatok a `IRow` felületen keresztül érhetők el. A kimeneti adatok egyszerre egy sornak adnak át.

Az egyes értékeket az IRow-felület Get metódusának hívásával számba veszi a függvény:

```
row.Get<string>("column_name")
```

Az egyes oszlopnevek a `row.Schema`következő hívással határozhatók meg:

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Ez a megközelítés lehetővé teszi, hogy egy rugalmas outputter bármilyen metaadat-séma.

A kimeneti adatok fájlba `System.IO.StreamWriter`írásra kerül a használatával. Az adatfolyam-paraméter `output.BaseStream` a. `IUnstructuredWriter output`részeként van beállítva.

Vegye figyelembe, hogy fontos, hogy az adatpuffert minden sorismétlés után ürítse ki a fájlba. Ezenkívül az `StreamWriter` objektumot úgy kell használni, hogy az Eldobható attribútum engedélyezve van (alapértelmezett), és a **következő kulcsszót használja:**

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Ellenkező esetben hívja meg a Flush() metódust explicit módon minden egyes iteráció után. Ezt a következő példában mutatjuk be.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Élőfejek és élőlábak beállítása a felhasználó által definiált kimenetelőhez
Fejléc beállításához használjon egyetlen iterációs végrehajtási folyamatot.

```
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

Az első `if (isHeaderRow)` blokkban lévő kód végrehajtása csak egyszer történik meg.

Az élőlábhoz használja az objektum `System.IO.Stream` példányára`output.BaseStream`( ). Írja be az élőlábat a `IOutputter` felület Close() metódusa.  (További információt a következő példában talál.)

A következő egy példa a felhasználó által definiált outputter:

```
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

És U-SQL alap script:

```
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

Ez egy HTML outputter, amely létrehoz egy HTML-fájlt táblázatadatokkal.

### <a name="call-outputter-from-u-sql-base-script"></a>Híváskimenetelő az U-SQL alapparancsfájlból
Az alap U-SQL-parancsfájlból származó egyéni outputter hívásához létre kell hozni a outputter objektum új példányát.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Annak elkerülése érdekében, hogy az objektum egy példányát az alap parancsfájlban hozzuk létre, létrehozhatunk egy függvényburkoló, amint az a korábbi példában látható:

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

Ebben az esetben az eredeti hívás a következőképpen néz ki:

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Felhasználó által definiált processzorok használata
A felhasználó által definiált processzor (UDP) az U-SQL UDO egy típusa, amely lehetővé teszi a bejövő sorok feldolgozását programozhatósági szolgáltatások alkalmazásával. Az UDP lehetővé teszi az oszlopok kombinálását, az értékek módosítását és szükség esetén új oszlopok hozzáadását. Alapvetően segít feldolgozni egy sorhalmazt a szükséges adatelemek előállításához.

UdP definiálásához létre kell `IProcessor` hoznunk `SqlUserDefinedProcessor` egy felületet az attribútummal, amely nem kötelező az UDP számára.

Ennek az interfésznek tartalmaznia kell az `IRow` interfész sorkészlet-felülbírálásának definícióját, amint az a következő példában látható:

```
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**Az SqlUserDefinedProcessor** azt jelzi, hogy a típust felhasználó által definiált processzorként kell regisztrálni. Ez az osztály nem örökölhető.

Az SqlUserDefinedProcessor attribútum **nem kötelező** az UDP-definícióhoz.

A fő programozhatósági objektumok a **bemenetés** **a kimenet .** A bemeneti objektum a bemeneti oszlopok és a kimenet számbavételére, valamint a processzortevékenység eredményeként a kimeneti adatok beállítására szolgál.

A bemeneti oszlopok enumerálása esetén a `input.Get` módszert használjuk.

```
string column_name = input.Get<string>("column_name");
```

A `input.Get` metódus paramétere egy olyan oszlop, amely `PRODUCE` az `PROCESS` U-SQL alapparancsfájl utasításának záradéka részeként kerül átadásra. Itt a megfelelő adattípust kell használnunk.

Kimenetesetén használja `output.Set` a módszert.

Fontos megjegyezni, hogy az egyéni gyártó csak a `output.Set` metódushívással definiált oszlopokat és értékeket adja ki.

```
output.Set<string>("mycolumn", mycolumn);
```

A tényleges processzorkimenetet a `return output.AsReadOnly();`hívás váltja ki.

A következő egy processzor példa:

```
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

Ebben a használati esetben a processzor egy "full_description" nevű új oszlopot hoz létre a meglévő oszlopok kombinálásával - ebben az esetben a "felhasználó" nagybetűvel és a "des".In this case case scenario, the processor is generating a new column called "full_description" by combining the existing columns- in this case, "user" in nagybetűs, and "des". Emellett újragenerálja a GUID azonosítót, és visszaadja az eredeti és az új GUID értékeket.

Amint az előző példában látható, a Módszerhívás során `output.Set` c# metódusok hívható meg.

Az alábbiakban egy példa az alap U-SQL parancsfájl, amely egy egyéni processzor:

```
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

## <a name="use-user-defined-appliers"></a>Felhasználó által definiált appliers használata
Az U-SQL-felhasználó által definiált applier lehetővé teszi, hogy meghívja az egyéni C# függvény minden sor, amely a lekérdezés külső tábla kifejezése által visszaadott. A jobb oldali bemenet kiértékelése a bal oldali bemenet minden egyes sorához, és a létrehozott sorok a végső kimenethez lesznek kombinálva. Az APPLY operátor által létrehozott oszlopok listája a bal és a jobb oldali bemenetoszlopkészletének kombinációja.

A felhasználó által definiált applier az USQL SELECT kifejezés részeként van meghívva.

A felhasználó által definiált főalkalmazási szerepkör tipikus hívása a következőképpen néz ki:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Az appliers SELECT kifejezésben való használatáról az [U-SQL SELECT Kijelölés keresztalkalmazása és külső alkalmazás](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply)lehetőséget című témakörben talál.

A felhasználó által definiált applier alaposztály-definíció a következő:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

A felhasználó által definiált applier definiálásához `IApplier` létre kell`SqlUserDefinedApplier`hoznunk a felületet a [ ] attribútummal, amely nem kötelező a felhasználó által definiált applier definícióhoz.

```
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

* Az Apply a külső táblázat minden sorához meg van hívva. A `IUpdatableRow` kimeneti sorhalmazt adja vissza.
* A Konstruktor osztály paraméterek et adhat át a felhasználó által definiált applier.

**Az SqlUserDefinedApplier** azt jelzi, hogy a típust felhasználó által definiált applier-ként kell regisztrálni. Ez az osztály nem örökölhető.

**Az SqlUserDefinedApplier** **nem kötelező** a felhasználó által definiált applier definícióhoz.


A fő programozhatósági objektumok a következők:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

A bemeneti sorkészletek bemenetként kerülnek átadásra. `IRow` A kimeneti sorok `IUpdatableRow` kimeneti felületként jönnek létre.

Az egyes oszlopnevek a `IRow` Sémametódus hívásával határozhatók meg.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

A tényleges adatértékek bekésezéséhez `IRow`használjuk a `IRow` Get() módszer felület.

```
mycolumn = row.Get<int>("mycolumn")
```

Vagy a sémaoszlop nevét használjuk:

```
row.Get<int>(row.Schema[0].Name)
```

A kimeneti értékeket `IUpdatableRow` a következő kimenettel kell beállítani:

```
output.Set<int>("mycolumn", mycolumn)
```

Fontos megérteni, hogy az egyéni appliers csak kimeneti `output.Set` oszlopok és értékek, amelyek a metódus hívása.

A tényleges kimenetet a `yield return output.AsReadOnly();`hívás váltja ki.

A felhasználó által definiált applier paraméterek et át lehet adni a konstruktornak. Applier adhat vissza változó számú oszlopok, amelyeket meg kell határozni az applier hívás során az alap U-SQL Script.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Íme a felhasználó által definiált applier példa:

```
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

A következőkben a felhasználó által definiált főalkalmazási szerepkör alap U-SQL-parancsfájlja látható:

```
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

Ebben a használati esetben a felhasználó által definiált nagyértékű a járműflotta tulajdonságainak vesszővel tagolt értékelemzőjeként működik. A bemeneti fájl sorai a következőkre néznek ki:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Ez egy tipikus tabulátorral tagolt TSV-fájl, amely olyan tulajdonságoszlopot tartalmaz, amely olyan autótulajdonságokat tartalmaz, mint a gyártmány és a modell. Ezeket a tulajdonságokat a táblázat oszlopaiban kell elemezni. A megadott applier lehetővé teszi, hogy dinamikus számú tulajdonságot hozzon létre az eredmény sorhalmazában, az átadott paraméter alapján. Létrehozhatja az összes tulajdonságot vagy csak egy adott tulajdonságkészletet.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

A felhasználó által definiált applier az applier objektum új példányaként hívható:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Vagy a wrapper gyári módszer meghívásával:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Felhasználó által definiált combineerek használata
A felhasználó által definiált combiner (UDC) lehetővé teszi a sorok kombinálását a bal és a jobb sorhalmazokból, egyéni logika alapján. A felhasználó által definiált combiner combine kifejezéssel használható.

A combiner meghívása a COMBINE kifejezéssel történik, amely biztosítja a szükséges információkat mind a bemeneti sorkészletekről, mind a csoportosítási oszlopokról, a várt eredménysémáról és a további információkról.

Ha egy u-SQL-parancsfájlban szeretne összeegycombineót hívni, a következő szintaxist használjuk:

```
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

További információ: [COMBINE Expression (U-SQL)](/u-sql/statements-and-expressions/combine-expression).

A felhasználó által definiált combiner definiálásához `ICombiner` létre kell`SqlUserDefinedCombiner`hoznunk a felületet a [ ] attribútummal, amely nem kötelező a felhasználó által definiált Combiner definícióhoz.

Alaposztály-definíció: `ICombiner`

```
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

Az `ICombiner` összeköttetés egyéni implementációjának tartalmaznia kell a `IEnumerable<IRow>` Combine felülbírálás definícióját.

```
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

Az **SqlUserDefinedCombiner** attribútum azt jelzi, hogy a típust felhasználó által definiált combinerként kell regisztrálni. Ez az osztály nem örökölhető.

**Az SqlUserDefinedCombiner** a Combiner mód tulajdonság definiálására szolgál. Ez egy nem kötelező attribútum a felhasználó által definiált combiner definícióhoz.

CombinerMode mód

A CombinerMode enum a következő értékeket veheti fel:

* Teljes (0) Minden kimeneti sor potenciálisan függ az összes bemeneti sorok bal és jobb azonos kulcsértékkel.

* Balra (1) Minden kimeneti sor balról egyetlen bemeneti sortól függ (és potenciálisan a jobb oldali összes sortól, azonos kulcsértékkel).

* Jobbra (2) Minden kimeneti sor függ egy bemeneti sor a jobb (és potenciálisan az összes sort a bal oldalon az azonos kulcs érték).

* Belső (3) Minden kimeneti sor egyetlen bemeneti sortól függ balról és jobbról, ugyanazzal az értékkel.

Példa:`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`[ ]


A fő programozhatósági objektumok a következők:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

A bemeneti sorkészletek **a kapcsolat bal** és **jobb** `IRowset` oldali típusaként kerülnek átadásra. A feldolgozáshoz mindkét sorkészletet fel kell sorolni. Az egyes felületeket csak egyszer lehet felsorolni, ezért szükség esetén fel kell sorolni és gyorsítótárazni kell.

Gyorsítótárazási célokra létrehozhatunk egy\<\> T-lista típusú memóriaszerkezetet a LINQ-lekérdezés végrehajtása, különösen a list<`IRow`>. A névtelen adattípus a felsorolás során is használható.

Az IEnumerable T felülettel kapcsolatos további információkért olvassa el [a LINQ-lekérdezések (C#) című](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) témakört, valamint az IEnumerable T felületről az [IEnumerable\<\> ](/dotnet/api/system.collections.generic.ienumerable-1) \<T\> felületről.

A tényleges adatértékek bekésezéséhez `IRowset`használjuk a `IRow` Get() módszer felület.

```
mycolumn = row.Get<int>("mycolumn")
```

Az egyes oszlopnevek a `IRow` Sémametódus hívásával határozhatók meg.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Vagy a sémaoszlop nevének használatával:

```
c# row.Get<int>(row.Schema[0].Name)
```

A LINQ általános felsorolása a következőképpen néz ki:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Miután mindkét sorhalmazt felsoroltuk, az összes sorban végigfogjuk hurkot. A bal oldali sorhalmaz minden egyes sorához megtaláljuk az összes olyan sort, amely megfelel a kombájnunk állapotának.

A kimeneti értékeket `IUpdatableRow` kimenettel kell beállítani.

```
output.Set<int>("mycolumn", mycolumn)
```

A tényleges kimenetet a `yield return output.AsReadOnly();`hívás a segítségével indítja el.

A következő egy combiner példa:

```
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

Ebben a használati esetben elemzési jelentést készítünk a kiskereskedő számára. A cél az, hogy megtalálja az összes terméket, hogy a költségek több mint $ 20,000, és hogy eladja a honlapon keresztül gyorsabb, mint a rendszeres kiskereskedő egy bizonyos időkereten belül.

Itt van az alap U-SQL script. Összehasonlíthatja a hagyományos JOIN és a combiner logikáját:

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

A felhasználó által definiált combiner az applier objektum új példányaként hívható meg:

```
USING new MyNameSpace.MyCombiner();
```


Vagy a wrapper gyári módszer meghívásával:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Felhasználó által definiált szűkítők használata

Az U-SQL lehetővé teszi egyéni sorhalmaz-szűkítők írását C# nyelven a felhasználó által definiált operátori bővíthetőségi keretrendszer használatával és az IReducer felület megvalósításával.

A felhasználó által definiált szűkítő (UDR) segítségével kiküszöbölhetők a felesleges sorok az adatok kinyerése (importálása) során. Sorok és oszlopok kezelésére és kiértékelésére is használható. A programozhatósági logika alapján azt is meghatározhatja, hogy mely sorokat kell kivonni.

UDR-osztály definiálásához létre kell `IReducer` hoznunk `SqlUserDefinedReducer` egy opcionális attribútummal rendelkező felületet.

Ennek az osztályfelületnek tartalmaznia kell az `IEnumerable` illesztő sorhalmaz-felülbírálásának definícióját.

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

Az **SqlUserDefinedReducer** attribútum azt jelzi, hogy a típust felhasználó által definiált szűkítőként kell regisztrálni. Ez az osztály nem örökölhető.
**Az SqlUserDefinedReducer** egy nem kötelező attribútum egy felhasználó által definiált szűkítő definícióhoz. Az IsRecursive tulajdonság meghatározására szolgál.

* bool IsRecursive    
* **true** = Azt jelzi, hogy ez a szűkítő asszociatív és kommutatív

A fő programozhatósági objektumok a **bemenetés** **a kimenet .** A bemeneti objektum a bemeneti sorok számbavételére szolgál. A kimenet a kimeneti sorok beállítására szolgál a tevékenység csökkentésének eredményeként.

A bemeneti sorok enumerálásához a `Row.Get` módszert használjuk.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

A `Row.Get` metódus paramétere egy olyan oszlop, amely `PRODUCE` az U-SQL alapparancsfájl `REDUCE` utasításosztályának részeként kerül átadásra. Itt is a megfelelő adattípust kell használnunk.

Kimenetesetén használja `output.Set` a módszert.

Fontos megérteni, hogy az egyéni szűkítő csak a `output.Set` metódushívással definiált értékeket adja ki.

```
output.Set<string>("mycolumn", guid);
```

A tényleges szűkítő kimenetét `yield return output.AsReadOnly();`a hívás váltja ki.

A következőkben egy szűkítő példa:

```
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

Ebben a használati esetben a szűkítő üres felhasználónévvel rendelkező sorokat hagy ki. A sorhalmaz minden egyes sorához beolvassa az egyes szükséges oszlopokat, majd kiértékeli a felhasználónév hosszát. Csak akkor adja ki a tényleges sort, ha a felhasználónév értéke hosszabb, mint 0.

A következőkben az alap U-SQL-parancsfájl, amely egyéni szűkítőt használ:

```
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
