---
title: Az Azure Data Lake U-SQL programozhatósági útmutató
description: További információ az Azure Data Lake Analytics, amelyek lehetővé teszik, hogy hozzon létre egy felhőalapú big data platform verziója.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: 9ff75cbd0a4915cdf7045be9a45d11075dda15bd
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402319"
---
# <a name="u-sql-programmability-guide"></a>U-SQL programozhatósági útmutató

U-SQL lekérdezésnyelvet, amely a big data-típusú számítási feladatok célja. U-SQL egyedi jellemzőinek egyike a bővíthetőség és a C# által biztosított programozhatóság deklaratív SQL-szerű nyelv együttes használata. Ebben az útmutatóban azt az alábbiakra koncentráljon a bővíthetőség és programozhatóság a U-SQL nyelv, amely szerint a C# engedélyezve van.

## <a name="requirements"></a>Követelmények

Töltse le és telepítse [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Ismerkedés a U-SQL  

Keresse meg az alábbi U-SQL-parancsfájlt:

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

Ez a szkript két sorkészletek meghatározása: `@a` és `@results`. Sorhalmaz `@results` van definiálva a `@a`.

## <a name="c-types-and-expressions-in-u-sql-script"></a>C#-típusok és kifejezések a U-SQL-szkript

U-SQL-kifejezést a C#-kifejezést kombinálva U-SQL logikai operations például `AND`, `OR`, és `NOT`. Válassza ki, KINYERÉSI, U-SQL-kifejezések használhatók, ahol, csoportosítás és DEKLARÁLHATÓ. Például a következő parancsfájl egy karakterláncot egy dátum/idő értékként elemzi.

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Az alábbi kódrészlet egy karakterláncot egy DECLARE utasítást a dátum/idő értékként elemzi.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>C# kifejezések használata az adatok típuskonverziók

A következő példa bemutatja, hogyan teheti egy dátum és idő adatok átalakítása a C# kifejezések használatával. Ez az adott esetben karakterlánc dátum és idő adatok alakítja át a szabványos dátum-idő a éjfél 00:00:00 idő jelölés használatával.

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

### <a name="use-c-expressions-for-todays-date"></a>C# kifejezések használhatók a mai napig

A mai napig lekérni, használhatjuk a következő C#-kifejezést: `DateTime.Now.ToString("M/d/yyyy")`

A következő példa bemutatja, hogyan egy parancsfájlt a következő kifejezést fogjuk használni:

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

A U-SQL-bővíthetőségi modell lehetővé teszi, hogy egyéni kódot .NET-szerelvények a fokozottan támaszkodik. 

### <a name="register-a-net-assembly"></a>.NET-szerelvény regisztrálása

Használja a `CREATE ASSEMBLY` utasítás egy .NET-szerelvény egy U-SQL-adatbázisba helyezi el. Ezt követően, U-SQL-parancsfájlok segítségével is használhatók e szerelvények a `REFERENCE ASSEMBLY` utasítást. 

A következő kód bemutatja, hogyan regisztrálhat egy szerelvény:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

A következő kód bemutatja, hogyan való hivatkozáshoz egy szerelvény:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Tekintse át a [szerelvény regisztrációs utasítások](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) , hogy ez a témakör részletesebben foglalkozik.


### <a name="use-assembly-versioning"></a>Szerelvény verziószámozást használni
Jelenleg a U-SQL a .NET-keretrendszer 4.5-ös verzióját használja. Ezért győződjön meg arról, hogy a saját szerelvényeket, hogy a futtatókörnyezet verziójának kompatibilisek legyenek-e.

Ahogy korábban említettük, U-SQL kódot futtat egy 64 bites (x 64) formátumban. Ezért ügyeljen arra, hogy a kód fordítását x64 futtathatók. Ellenkező esetben a korábban bemutatott helytelen formátumú hibát kap.

Minden egyes feltöltött szerelvény dll-fájl és erőforrás-fájl, például egy másik modult, natív szerelvény vagy egy konfigurációs fájl legfeljebb 400 MB lehet. Üzembe helyezett erőforrásokat, vagy üzembe helyezése erőforrás vagy keresztül hivatkozások szerelvényeket és a további fájlokra, amelyek összméretén legfeljebb 3 GB-os lehet.

Végül vegye figyelembe, hogy minden U-SQL-adatbázist csak egy verzióját minden olyan adott szerelvény is tartalmazhatja. Például ha 7-es verzió és a 8-as verzió NewtonSoft Json.Net-függvénytár, meg kell két külön adatbázis regisztrálja őket. Továbbá minden parancsprogram csak hivatkozhatnak egy adott szerelvény DLL egyik verziójának. Ebben a tekintetben a U-SQL C# szerelvény felügyeleti és verziókezelését szemantikáját követi.

## <a name="use-user-defined-functions-udf"></a>Felhasználó által definiált függvények használata: UDF
U-SQL-felhasználó által definiált függvények vagy UDF, vannak programozási rutinjait, amely fogad el paramétereket, egy műveletet (például egy összetett számítás) és egy értéket, a művelet eredményét adja vissza. Az UDF visszaadott értéke csak lehet egyetlen skaláris. U-SQL UDF a U-SQL alapvető szkriptet, mint bármely más C# skaláris függvény nem hívható meg.

Azt javasoljuk, hogy a U-SQL-felhasználó által definiált függvények, mint inicializálása **nyilvános** és **statikus**.

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

Először nézzük, egy UDF létrehozása egyszerű példát.

A használatieset-forgatókönyvek a kell meghatározni az időszak, többek között a pénzügyi negyedév és az első bejelentkezés az adott felhasználó pénzügyi hónap. Ebben az esetben az év első pénzügyi hónap június.

Pénzügyi időszak kiszámításához, az alábbi C#-függvény bevezettünk:

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

Egyszerűen pénzügyi hónap és negyedév számítja ki, és egy karakterláncértéket ad vissza. Június az első pénzügyi negyedév első hónapjához használ "Q1:P1". Júliusi hogy "Q1:P2" használja, és így tovább.

Ez a szokásos C#-függvény, amely fogjuk használni az U-SQL-projektben.

Itt látható, hogyan néz ki a háttérkód szakasz ebben a forgatókönyvben:

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

Most már fogjuk Ez a függvény hívása az alapvető U-SQL-szkriptet. Ehhez adja meg a függvényt, többek között a névtér, amely ebben az esetben NameSpace.Class.Function(parameter) teljesen minősített nevét kell.

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Következő a tényleges alap U-SQL-parancsfájl:

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

Következő a kimeneti fájlt a parancsfájl végrehajtása a következő:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Ebben a példában a beágyazott UDF U-SQL egy egyszerű használatát mutatja be.

### <a name="keep-state-between-udf-invocations"></a>Őrzik meg állapotukat UDF között
U-SQL C# programozhatóság objektumokat is lehet több kifinomult, a háttérkód globális változókat – interaktivitási felhasználásával. Tekintsük át az alábbi üzleti használatieset-forgatókönyvet.

A nagy szervezetek is használnak a felhasználók belső alkalmazások fajtái között válthat. Ezek lehetnek a Microsoft Dynamics CRM, Power bi és így tovább. Az ügyfeleknek érdemes a alkalmazni a telemetria elemzése hogyan felhasználók váltogatnak a különböző alkalmazások között, a használati trendeket Mik, és így tovább. A vállalati célja, hogy az alkalmazás használatának optimalizálásához. Akkor is érdemes egyesítheti a különböző alkalmazások vagy az adott bejelentkezési rutinokat.

E cél eléréséhez, azt kell meghatározni a munkamenet-azonosítók és időbeli között a legutóbbi munkamenet bekövetkezett.

Igazolnia kell a korábbi bejelentkezési keresése, és hozzárendelheti a jelentkezzen be az összes, ugyanazt az alkalmazást létrehozó munkamenet. A rendszer az első ellenőrzést, hogy alap U-SQL parancsfájl nem teszi lehetővé számunkra, hogy a alkalmazni a számítások felett már számított oszlopokat a LAG függvény. A második kihívás abban áll, hogy, hogy az adott munkamenet összes-munkamenetet a azonos időn belül van.

A probléma megoldásához, a háttérkód szakaszon belül használjuk egy globális változó: `static public string globalSession;`.

A teljes sorkészlete a globális változót alkalmazza a rendszer a parancsfájl végrehajtása közben.

Íme a háttérkód szakasz a U-SQL-program:

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

Ez a példa bemutatja a globális változó `static public string globalSession;` belül használt a `getStampUserSession` függvényt, és minden alkalommal, amikor megváltozik a munkamenet paraméter első újrainicializálva.

Az U-SQL alapvető szkriptet a következőképpen történik:

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

Függvény `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` Itt a második memória sorkészlet számítás során nevezzük. Továbbítja a `UserSessionTimestamp` oszlop és visszaadja az értéket, amíg `UserSessionTimestamp` megváltozott.

A kimeneti fájlt a következőképpen történik:

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

Ez a példa bemutatja egy bonyolultabb használatieset-forgatókönyvek, amelyek a alkalmazni a teljes memória sorkészlete háttérkód szakaszon belül egy globális változó használjuk.

## <a name="use-user-defined-types-udt"></a>Használja a felhasználó által meghatározott típusok: UDT
Felhasználó által definiált típusa, illetve UDT, egy másik programozástámogatás U-SQL. U-SQL UDT ugyanúgy viselkedik, mint a rendszeres C# felhasználó által meghatározott típus. C# egy szigorú típusmegadású nyelv, amely engedélyezi a beépített és egyéni, felhasználó által definiált típus.

U-SQL implicit módon nem szerializálható és deszerializálható alapján tetszőleges UDT-ket, ha a UDT sorkészletek csúcspontok között átadott. Ez azt jelenti, hogy a felhasználó rendelkezik-e, adjon meg egy explicit formázó a IFormatter felhasználói felületéről. Ez a U-SQL biztosít a szerializálása, és deszerializálható módszer esetében a UDT.

> [!NOTE]
> A U-SQL beépített információkinyerőkre és kiírókra jelenleg nem lehet szerializálni vagy deszerializálni UDT-adatok még az IFormatter együtt fájlok. Tehát amikor UDT adatok írása a kimeneti utasítással egy fájlba, vagy egy mappába a adatellenőrzés, akkor egy karakterlánc- vagy bájt tömbként adja át azt. Ezután hívja a szerializálási, és explicit módon deszerializálási kódot (azaz a UDT ToString() módszer). Felhasználó által definiált információkinyerőkre és kiírókra, másrészről, olvashat és írhat alapján UDT-ket.

Ha azt próbálja használni UDT INFORMÁCIÓKINYERŐ vagy OUTPUTTER (összesen: előző kijelölés), az itt látható módon:

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Mi a következő hibaüzenetet kapja:

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

UDT dolgozni a outputter, vagy rendelkezünk, string ToString() módszerrel, vagy hozzon létre egy egyéni outputter szerializálásához.

Alapján UDT-ket jelenleg nem használható CSOPORTOSÍTÁSI szempont. Ha a GROUP BY UDT használja, a következő hiba lépett fel:

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

Egy UDT meghatározásához, hogy kell:

* Adja hozzá a következő névterek:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Adjon hozzá `Microsoft.Analytics.Interfaces`, azért szükség a UDT-felületeihez. Emellett `System.IO` meghatározásához a IFormatter felületen lehet szükség.

* A használt által meghatározott típus SqlUserDefinedType attribútummal határozza meg.

**SqlUserDefinedType** segítségével jelölje meg a felhasználó által definiált típus (UDT-KET) a U-SQL-szerelvény definíciójában. Az attribútum tulajdonságainak tükrözik a UDT fizikai jellemzőit. Ez az osztály nem örökölhetők.

SqlUserDefinedType UDT-definíció kötelező attribútuma.

Az osztály a konstruktort:  

* SqlUserDefinedTypeAttribute (típus formátovací modul)

* Formátovací modul írja be: Paraméterrel egy UDT formázó – pontosabban típusát kötelező a `IFormatter` felület itt kell átadni.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Tipikus UDT is szükség van a definíció a IFormatter felület, az alábbi példában látható módon:

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

A `IFormatter` felület szerializálja és megszüntetéséhez szerializálja a legfelső szintű típusát egy objektumdiagram \<typeparamref name = "T" >.

\<típusparaméter name = "T" > a legfelső szintű típus az objektum gráf szerializálható és deszerializálható.

* **Deszerializálni**: Vonja szerializálja a megadott streamet az adatok és a graph-objektumok reconstitutes.

* **Szerializálható**: Szerializálja az objektumot, vagy objektumokat, a megadott legfelső szintű, a megadott adatfolyam-grafikon.

`MyType` Példány: A típus példányát.  
`IColumnWriter` író / `IColumnReader` olvasó: Az alapjául szolgáló oszlop adatfolyam.  
`ISerializationContext` Környezet: Amely határozza meg, amely meghatározza a forrás vagy cél környezet a Stream a szerializálás során fellépett jelzők enumerálása.

* **Köztes**: Itt adhatja meg, hogy a forrás vagy cél környezet ne legyen-e egy állandó tárolóban.

* **Adatmegőrzés**: Megadja, hogy a forrás vagy cél környezet egy állandó tárolóban.

Rendszeres C# típus, egy U-SQL UDT-definíció tartalmazhatnak felülbírálásokat adhatnak az operátoroknak például +/ == /! =. Statikus módszereit is tartalmazhat. Például a UDT használata a U-SQL minimális összesítő függvény paramétereként fogjuk, ha azt kell meghatároznia < operátor felülbírálás.

Ez az útmutató korábbi részében azt bizonyított formátumban megadott dátum, a pénzügyi időszak azonosító példa `Qn:Pn (Q1:P10)`. Az alábbi példa bemutatja, hogyan határozza meg egy egyéni típus a pénzügyi küszöbidőszakok értékeit.

Következő egy példa egy egyéni UDT és IFormatter felülettel háttérkód szakaszban:

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

Az adott típusú tartalmaz két szám: negyedév és a hónap. Operátorok `==/!=/>/<` és statická metoda `ToString()` Itt vannak megadva.

Ahogy korábban említettük, UDT VÁLASSZA kifejezésében is használható, de nem használható OUTPUTTER/INFORMÁCIÓKINYERŐ nélkül egyéni sorba rendezésre. Karakterláncként kell szerializálni vagy rendelkezik `ToString()` vagy egy egyéni OUTPUTTER INFORMÁCIÓKINYERŐT használják.

Most vizsgáljuk UDT használatát. A háttérkód a szakaszban a következő úgy módosítottuk a GetFiscalPeriod függvény:

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

Ahogy láthatjuk, hogy FiscalPeriod típusa értékét adja vissza.

Itt egy példa bemutatja, hogyan használhatja tovább az U-SQL alapvető szkriptet biztosítunk. Ez a példa bemutatja a U-SQL parancsfájl UDT hívásának különböző űrlapokat.

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

Íme egy példa a teljes háttérkód szakaszban:

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

## <a name="use-user-defined-aggregates-udagg"></a>Felhasználó által definiált összesítések használata: UDAGG
Felhasználó által definiált összesítések rendszer minden összesítést kapcsolatos funkciók, amelyek nem tartalmazza a szükséges a-beépített U-SQL-lel. A példa lehet egyéni matematikai számítások, karakterláncok összefűzése, feldolgozás a karakterláncokat, és így tovább végrehajtásához összesítést.

A felhasználó által definiált összesítő alaposztály definíciója a következőképpen történik:

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

**SqlUserDefinedAggregate** azt jelzi, hogy a típus, egy felhasználó által meghatározott összesítés kell regisztrálni. Ez az osztály nem örökölhetők.

SqlUserDefinedType attribútum **választható** UDAGG definíciójában.


Az alaposztály alaposztályát lehetővé teszi, hogy három absztrakt paraméterek: két bemeneti paramétereket és a egy eredményezi. Az adattípusok változóak, és meg kell határozni az osztályöröklődés során.

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

* **Init** hív meg egyszer minden csoport számítás során. Az inicializálás rutinja minden összesítést csoport biztosítja.  
* **Hogyan gyűlnek** mindig egyszer minden egyes értékhez. Az összesítési algoritmus a fő funkciókat biztosít. A különféle adattípusokkal osztályöröklődés során meghatározott összesített értékeket is használható. El tudja fogadni a változó adattípusok két paramétert.
* **Leállítja** mindig egyszer végrehajtja csoportonként összesítési feldolgozás végén a kimenetben az eredmény az egyes csoportokhoz.

Deklarálja a megfelelő bemeneti és kimeneti adattípus, az alábbiak szerint alkalmazza az osztálykiterjesztések definíciója:

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Első paraméterként gyűlnek össze
* T2: Első paraméterként gyűlnek össze
* TResult: Typ vrácené hodnoty Leállítás

Példa:

```
public class GuidAggregate : IAggregate<string, int, int>
```

vagy

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>A U-SQL UDAGG használata
UDAGG használatához először definiálja azt a háttérkód vagy a meglévő programozhatóság DLL-ből hivatkozni rá, taglaltak szerint.

Ezután használja a következő szintaxist:

```
AGG<UDAGG_functionname>(param1,param2)
```

Íme egy példa UDAGG:

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

És alapvető U-SQL-szkriptet:

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

Használati eset ebben a forgatókönyvben azt concatenate osztály GUID-azonosítói a megadott felhasználók számára.

## <a name="use-user-defined-objects-udo"></a>Felhasználó által definiált objektumokat használ: UDO
U-SQL lehetővé teszi, hogy egyéni programozhatóság objektumokat, amelyeket a rendszer meghív, felhasználó által definiált objektumokat vagy UDO definiálja.

A következő egy U-SQL UDO listáját:

* Felhasználó által definiált információkinyerők
    * Kinyerési soronként
    * Adatok kinyerése az egyéni strukturált fájlok megvalósításához használt

* Felhasználó által definiált kiírókra
    * Kimeneti soronként
    * Kimeneti egyéni adattípus vagy egyéni fájl használt formátum

* Felhasználó által definiált processzorok
    * Igénybe vehet egy olyan sor és a egy sor
    * Csökkentse az oszlopok számát, vagy egy meglévő oszlopkészlet származó értékekkel új oszlopok létrehozásához

* Felhasználó által definiált appliers
    * Igénybe vehet egy olyan sor és a 0-tól n sorok
    * A külső/CROSS APPLY használt

* Felhasználó által definiált combiners
    * Egyesíti a sorkészletek – felhasználó által definiált illesztéseket

* Felhasználó által definiált csökkentő
    * N sorok igénybe és a egy sor
    * Sorok számának csökkentése érdekében használja

UDO neve általában kifejezetten a U-SQL-szkript az alábbi U-SQL-utasítások részeként:

* EXTRACT
* KIMENET
* FOLYAMAT
* ÖSSZEVONÁS
* CSÖKKENTÉSE

> [!NOTE]  
> A UDO korlátozódik 0,5 Gb memóriát.  Ez a memória-korlátozás nem vonatkozik a helyi végrehajtást.

## <a name="use-user-defined-extractors"></a>Felhasználó által definiált információkinyerők használata
U-SQL lehetővé teszi, hogy a külső adatok importálása egy KIVONATOT utasítás használatával. Egy KIVONATOT utasítás beépített UDO információkinyerők használhatja:  

* *Extractors.Text()*: Itt eltérő kódolásokat kivonása elválasztójellel tagolt szöveges fájlok.

* *Extractors.Csv()*: Kibontási vesszővel tagolt (CSV) fájljai különböző kódolást biztosít.

* *Extractors.Tsv()*: Kibontási lapon tagolt (TSV) fájljai különböző kódolásokat biztosít.

Egyéni információkinyerőt fejleszthet hasznos lehet. Ez hasznos lehet adatok importálása során szeretnénk hajtsa végre a következő feladatok közül:

* Módosítsa a bemeneti adatok oszlopok felosztása, és egyedi értékek módosítása. A PROCESSZOR funkcióinak szerkezet alkalmasabb a oszlopok egyesítése.
* Strukturálatlan adatok, például a weblapok és e-mailek vagy részben strukturált adatok, például XML vagy JSON elemzése.
* Elemezni az adatokat a kódolás nem támogatott.

Egy felhasználó által definiált információkinyerő vagy előtti parancshoz szükséges definiálásához, létre kell hozni egy `IExtractor` felületet. Összes bemeneti paramétereket a információkinyerőt például oszlop vagy sor elválasztó karakterek, és a kódolást kell meghatározni az osztály a konstruktor. A `IExtractor` felületet is tartalmaznia kell a definícióját a `IEnumerable<IRow>` bírálja felül az alábbiak szerint:

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

A **SqlUserDefinedExtractor** attribútum jelzi, hogy a típus, egy felhasználó által megadott mappába kell regisztrálni. Ez az osztály nem örökölhetők.

SqlUserDefinedExtractor előtti parancshoz szükséges definíció nem kötelező attribútum. Ez előtti parancshoz szükséges objektum AtomicFileProcessing tulajdonság meghatározásához.

* logikai AtomicFileProcessing   

* **Igaz** = jelzi, hogy ez információkinyerő igényel atomi bemeneti fájlok (JSON, XML,...)
* **hamis** = jelzi, hogy ez információkinyerő kezelhető felosztása / elosztott fájlok (CSV, SEQ,...)

A fő előtti parancshoz szükséges programozhatóság objektumok **bemeneti** és **kimeneti**. A bemeneti objektum szolgál, a bemeneti adatok enumerálása `IUnstructuredReader`. A kimeneti objektum segítségével állítsa be a kimeneti adatokat kinyerő tevékenység eredményeként.

A bemeneti adatok keresztül elérhető `System.IO.Stream` és `System.IO.StreamReader`.

A bemeneti oszlopok enumerálás először osztottuk fel a bemeneti stream egy sor elválasztó használatával.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Ezt követően további ossza fel a bemeneti sor oszlop részei.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Kimeneti adatok megadásához használjuk a `output.Set` metódust.

Fontos tudni, hogy az egyéni információkinyerőt és a kimenet csak megjeleníti-e oszlopok és megadott értéket. Állítsa be a metódushívás.

```
output.Set<string>(count, part);
```

Akkor aktiválódik, a tényleges információkinyerő kimeneti meghívásával `yield return output.AsReadOnly();`.

Következő egy információkinyerő példa:

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

A használatieset-forgatókönyvek a információkinyerő újra létrehozza a "guid" oszlophoz tartozó GUID és nagybetűs alakítja át a "user" oszlopban levő értékeket. Egyéni információkinyerők bonyolultabb eredmények a bemeneti adatok elemzése és módosítása, hozhat létre.

Következő által használt egyéni információkinyerőt alap U-SQL-parancsfájl:

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

## <a name="use-user-defined-outputters"></a>Használja a felhasználó által definiált kiírókra
Felhasználó által definiált outputter egy másik U-SQL UDO, amely lehetővé teszi, hogy terjessze ki a U-SQL beépített funkciókkal. A kivonatoló hasonlóan, több beépített kiírókra vannak.

* *Outputters.Text()*: Írja az adatokat a különböző kódolású, elválasztójellel tagolt szöveges fájlok.
* *Outputters.Csv()*: Írja az adatokat a különböző kódolásokat fájljai vesszővel tagolt (CSV).
* *Outputters.Tsv()*: Írja az adatokat a különböző kódolásokat érték tabulátorral tagolt (TSV) fájljait.

Egyéni outputter lehetővé teszi az adatok írása a meghatározott formátumban. Ez lehet hasznos, ha a következő feladatokat:

* Részben strukturált vagy strukturálatlan fájlok írja az adatokat.
* Kódolásokat írja az adatokat nem támogatott.
* Kimeneti adatok módosítását, vagy egyéni attribútumok hozzáadását.

Felhasználó által definiált outputter definiálásához, létre kell hozni a `IOutputter` felületet.

Az alábbiakban a alap `IOutputter` osztály implementációját:

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

A outputter, például az oszlop vagy sor elválasztó karakterek, kódolási és így tovább, az összes bemeneti paramétereket kell meghatározni az osztály a konstruktor. A `IOutputter` felületet is tartalmaznia kell a definíciót a `void Output` felülbírálása. Az attribútum `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` igény szerint állíthat be atomi üzenetfájl-feldolgozása. További információkért lásd az alábbi részleteket.

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

* `Output` az egyes bemeneti sorok nevezzük. Visszaadja a `IUnstructuredWriter output` sorkészlete.
* A konstruktor osztály paraméterek átadása a felhasználó által definiált outputter szolgál.
* `Close` szükség esetén bírálja felül, ha költséges állapot kiadás vagy határozza meg, amikor az utolsó sor írásának szolgál.

**SqlUserDefinedOutputter** attribútum jelzi, hogy a típus, egy felhasználó által definiált outputter kell regisztrálni. Ez az osztály nem örökölhetők.

SqlUserDefinedOutputter egy olyan nem kötelező attribútum felhasználó által definiált outputter definícióját. A AtomicFileProcessing tulajdonság meghatározására szolgál.

* logikai AtomicFileProcessing   

* **Igaz** = jelzi, hogy ez outputter igényel atomi kimeneti fájlok (JSON, XML,...)
* **hamis** = jelzi, hogy ez outputter kezelhető felosztása / elosztott fájlok (CSV, SEQ,...)

A fő programozhatóság objektumok **sor** és **kimeneti**. A **sor** objektumot használja, a kimeneti adatok enumerálása `IRow` felületet. **Kimeneti** használja a cél fájl kimeneti adatok beállításához.

A kimeneti adatokat keresztül érhető el a `IRow` felületet. Kimeneti adatok átadása egy sor egyszerre.

Nelze získat rozhraní IRow, Get metódus meghívásának megjelenik az egyes értékeket:

```
row.Get<string>("column_name")
```

Egyedi oszlopnevek meghívásával lehet meghatározni `row.Schema`:

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Ez a megközelítés egy rugalmas outputter minden metaadat-séma létrehozását teszi lehetővé.

A fájl használatával írja a kimeneti adatokat `System.IO.StreamWriter`. A stream paraméter értéke `output.BaseStream` részeként `IUnstructuredWriter output`.

Vegye figyelembe, hogy fontos, hogy Ürítsen ki adatpuffer a fájl minden egyes sor ismétlés után. Emellett a `StreamWriter` attribútummal a rendelkezésre álló engedélyezve (alapértelmezett), és az objektum kell használni a **használatával** kulcsszó:

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Ellenkező esetben külön hívja meg a Flush() metódus egyes ismétlései után. Bemutatjuk, ez a következő példában.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>A felhasználó által definiált outputter fejlécek és láblécek beállítása
Fejléc beállításához használja az iteráció egyetlen végrehajtási folyamat.

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

A kód az első `if (isHeaderRow)` letiltása csak egyszer hajtja végre.

Élőlábként, használja a rendszer a példányra `System.IO.Stream` objektum (`output.BaseStream`). Írja be a lábléc Close() módszer a `IOutputter` felületet.  (További információkért lásd az alábbi példát.)

Következő egy példa egy felhasználó által definiált outputter:

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

És a U-SQL alapvető szkriptet:

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

Ez az egy HTML outputter, amely egy HTML-fájlt hoz létre táblák adatainak.

### <a name="call-outputter-from-u-sql-base-script"></a>U-SQL alapvető szkriptet, hívja meg outputter
Egy egyéni outputter hívása az alapvető U-SQL-szkriptet, hogy a outputter objektum új példányát létrehozni rendelkezik.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Kerülje az objektum egy példányát az alapvető szkriptet, hogy létrehozhatunk egy függvény burkoló a korábbi példában látható módon:

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

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Felhasználó által definiált processzorok használata
Processzor felhasználói vagy UDP, egyfajta U-SQL UDO, amely lehetővé teszi a programozhatóság funkciók alkalmazásával a bejövő sorok feldolgozását. UDP lehetővé teszi, hogy az oszlopok egyesítése, módosítsa az értékeket, és új oszlopok hozzáadása, ha szükséges. Alapvetően segít a sorhalmaz létrehozásához szükséges adatok feldolgozásához.

Annak meghatározásához, egy UDP, létre kell hozni egy `IProcessor` az illesztő a `SqlUserDefinedProcessor` attribútuma, amely nem kötelező UDP-hez.

Ez az interfész definíciójának tartalmaznia kell a `IRow` felület sorkészlet bírálja felül, a következő példában látható módon:

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

**SqlUserDefinedProcessor** azt jelzi, hogy egy felhasználó által definiált processzor, a típus kell regisztrálni. Ez az osztály nem örökölhetők.

Az attribútum a SqlUserDefinedProcessor **választható** UDP-definíció.

A fő programozhatóság objektumok **bemeneti** és **kimeneti**. A bemeneti objektum használja bemeneti oszlopok és a kimeneti felsorolása, és beállíthatja az eredményeként a processzor tevékenység kimeneti adatokat.

Bemeneti oszlopok enumerálás, használ a `input.Get` metódust.

```
string column_name = input.Get<string>("column_name");
```

A paraméter `input.Get` módszer a következő részeként átadott oszlop a `PRODUCE` záradékában a `PROCESS` rendszerállapot-kimutatását a U-SQL alapvető szkriptet. Itt a megfelelő adattípust használni kell.

A kimenet, használja a `output.Set` metódust.

Fontos megjegyezni, hogy egyéni termelő csak kimenete, oszlopok és a megadott értéket a `output.Set` metódus hívása.

```
output.Set<string>("mycolumn", mycolumn);
```

Akkor aktiválódik, a tényleges processzor kimeneti meghívásával `return output.AsReadOnly();`.

Következő a processzor-példa:

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

Használati eset ebben a forgatókönyvben a processzor a meglévő oszlopok – ebben az esetben a nagybetűs és a "des" a "user" kombinálásával "full_description" nevű új oszlopot hoz létre. Újragenerálja a GUID Azonosítót és a eredeti és az új GUID-értékeit adja eredményül.

Amint az előző példában is látható, meghívhatja a C# módszerek során `output.Set` metódus hívása.

Következő példája egy egyéni processzort használó alapvető U-SQL-szkriptet:

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
Egy felhasználó által definiált U-SQL applier lehetővé teszi egyéni C# függvény hívása minden egyes sorára, a külső tábla kifejezés egy lekérdezés által visszaadott. A jobb oldali bemeneti értékeli ki a bal oldali bemeneti minden egyes sorára, és a sorokat, amelyek előállítják a végeredmény kombinálva. Az APPLY operátor által készített oszlopok listája a bal és jobb oldali bemeneti oszlopainak halmaza együttes használata.

Felhasználó által definiált applier meghívásainak folyamatban van a USQL kiválasztása kifejezés részeként.

A felhasználó által definiált applier tipikus hívása a következőhöz hasonlóan néz ki:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Válassza ki a kifejezésben appliers használatával kapcsolatos további információkért lásd: [U-SQL kiválasztása kiválasztása a CROSS APPLY és OUTER APPLY operátort](https://msdn.microsoft.com/library/azure/mt621307.aspx).

A felhasználó által definiált applier alaposztály definíciója a következőképpen történik:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Egy felhasználó által definiált applier definiálásához, létre kell hozni a `IApplier` kommunikáljanak a [`SqlUserDefinedApplier`] attribútuma, amely egy felhasználó által definiált applier definíció esetén nem kötelező.

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

* Alkalmazza a külső tábla minden egyes sorára nevezzük. Visszaadja a `IUpdatableRow` sorkészlet kimeneti.
* A konstruktor osztály paraméterek átadása a felhasználó által definiált applier szolgál.

**SqlUserDefinedApplier** azt jelzi, hogy a típus, egy felhasználó által definiált applier kell regisztrálni. Ez az osztály nem örökölhetők.

**SqlUserDefinedApplier** van **választható** egy felhasználó által definiált applier definíciójában.


A fő programozhatóság objektumok a következők:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

A bemeneti sorkészletek adhatók be `IRow` bemeneti. A kimeneti sorok akkor jönnek létre, mint `IUpdatableRow` felület.

Egyedi oszlopnevek meghívásával lehet meghatározni a `IRow` séma metódust.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

A bejövő adatok tényleges értékének lekéréséhez `IRow`, Get() módszert használjuk `IRow` felületet.

```
mycolumn = row.Get<int>("mycolumn")
```

Vagy a sémanév oszlopot használjuk:

```
row.Get<int>(row.Schema[0].Name)
```

A kimeneti értékeket kell beállítani `IUpdatableRow` kimenete:

```
output.Set<int>("mycolumn", mycolumn)
```

Fontos tudni, hogy egyéni appliers csak kimeneti oszlopok és a megadott értéket `output.Set` metódus hívása.

Akkor aktiválódik, a tényleges kimeneti meghívásával `yield return output.AsReadOnly();`.

A felhasználó által definiált applier lehet átadni a konstruktor. Applier adhat vissza oszlopokat, amelyeket kell meghatározni az alapvető U-SQL-szkriptet a applier hívás során változó számú.

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

A következő az alapvető U-SQL-szkriptet a felhasználó által definiált applier a következő:

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

A használatieset-forgatókönyvek a felhasználó által definiált applier egy vesszővel tagolt érték típusú elemzőt flotta autó-tulajdonságokat funkcionál. A bemeneti fájl sorainak a következőhöz hasonló:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Egy tipikus tabulátorral tagolt TSV-fájlt az autó tulajdonságai, például a gyártmány és modell tulajdonságai oszlopa. Azokat a tulajdonságokat kell elemezni, a tábla oszlopait. A megadott applier azt is lehetővé teszi, hogy az eredmény sorhalmaz, az átadott paraméter alapján hozzon létre dinamikus számos tulajdonságot. Az összes tulajdonság vagy a tulajdonságok csak egy adott készletét hozhatja létre.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

A felhasználó által definiált applier applier objektum új példányát, nem hívható meg:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

Vagy egy burkoló gyári metódus meghívását:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Felhasználó által definiált combiners használata
Felhasználó által definiált egyesítő vagy UDC, segítéségével kombinálhatja a bal és jobb sorhalmaz alapján egyéni logikát sorait. Felhasználó által definiált egyesítő ÖSSZEVONÁS kifejezés használnak.

Egy egyesítő meghívása folyamatban van az ÖSSZEVONÁS kifejezés, amely mind a bemeneti sorkészletek, a csoportosító oszlopokra, a várt eredményt séma és további információ a szükséges információkat biztosít.

Egy egyesítő hívja meg a alapvető U-SQL-szkriptet, azt használja a következő szintaxist:

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

További információkért lásd: [(U-SQL) kifejezés KOMBINÁLÁSA](https://msdn.microsoft.com/library/azure/mt621339.aspx).

Egy felhasználó által definiált egyesítő definiálásához, létre kell hozni a `ICombiner` kommunikáljanak a [`SqlUserDefinedCombiner`] attribútuma, amely egy felhasználó által definiált egyesítő definíció esetén nem kötelező.

Alap `ICombiner` osztálydefiníciót:

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

Az egyéni megvalósítását egy `ICombiner` felület tartalmaznia kell a definíció egy `IEnumerable<IRow>` felülbírálás egyesítése.

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

A **SqlUserDefinedCombiner** attribútum jelzi, hogy a típus, egy felhasználó által definiált egyesítő kell regisztrálni. Ez az osztály nem örökölhetők.

**SqlUserDefinedCombiner** a egyesítő mód tulajdonságot meghatározására szolgál. Egy felhasználó által definiált egyesítő definíciója nem kötelező attribútum legyen.

CombinerMode mód

CombinerMode enum hajthatja végre a következő értékeket:

* Teljes (0) minden kimeneti sor esetleg az összes bemeneti sor bal és jobb ugyanazt a kulcsérték függ.

* Balra (1) a bal oldalon (és ugyanazt a kulcsérték-jobb oldalról potenciálisan minden sort) egyetlen bemeneti sor függ, hogy minden kimeneti sor.

* Jogosultság (2) a jobb oldalon (és ugyanazt a kulcsérték a bal oldali potenciálisan minden sort) egyetlen bemeneti sor függ, hogy minden kimeneti sor.

* Belső (3) minden kimeneti sor függ, hogy egyetlen bemeneti sor bal és jobb ugyanazt az értéket.

Példa: [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


A fő programozhatóság objektumok a következők:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

A bemeneti sorkészletek adhatók be **bal oldali** és **megfelelő** `IRowset` interfész típusa. Mindkét sorkészletek kell enumerálni a feldolgozáshoz. Csak enumerálása mindegyik adapterhez egyszer, hogy ne számba vehesse és gyorsítótárazza azt szükség esetén.

Gyorsítótárazás célokra, létrehozhatunk egy lista\<T\> memória struktúra típusú eredményeként egy LINQ-lekérdezés végrehajtása, kifejezetten a listában <`IRow`>. A névtelen adattípus felsorolásakor is használható.

Lásd: [Bevezetés a LINQ-lekérdezések (C#)](https://msdn.microsoft.com/library/bb397906.aspx) további információt a LINQ-lekérdezések és [IEnumerable\<T\> felület](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx) IEnumerablebővebben\<T\> felületet.

A bejövő adatok tényleges értékének lekéréséhez `IRowset`, Get() módszert használjuk `IRow` felületet.

```
mycolumn = row.Get<int>("mycolumn")
```

Egyedi oszlopnevek meghívásával lehet meghatározni a `IRow` séma metódust.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Vagy a séma oszlopnév használatával:

```
c# row.Get<int>(row.Schema[0].Name)
```

Az általános enumerálás a LINQ to a következőhöz hasonlóan néz ki:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Mindkét sorkészletek enumerálása, után fogjuk az összes sor hurkot. A bal oldali sorkészlete minden egyes sorára fogjuk keresse meg a feltételnek, a egyesítő azokat a sorokat.

A kimeneti értékeket kell beállítani `IUpdatableRow` kimeneti.

```
output.Set<int>("mycolumn", mycolumn)
```

A tényleges kimeneti a hívó által aktivált `yield return output.AsReadOnly();`.

Következő egy egyesítő példát:

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

A használatieset-forgatókönyvek a készítünk egy analitikai jelentés a közvetítő számára. A célja, hogy tartalmaz minden olyan terméket, amely több mint 20 000 költség és, értékesítésére, a gyorsabb, mint a rendszeres kereskedő időszakon belül keresztül webhelyen keresztül.

Ez a alapvető U-SQL-szkriptet. Összehasonlíthatja a logikai reguláris ILLESZTÉSI és a egy egyesítő között:

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

Egy felhasználó által definiált egyesítő a applier objektum új példányát, nem hívható meg:

```
USING new MyNameSpace.MyCombiner();
```


Vagy egy burkoló gyári metódus meghívását:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Felhasználó által definiált csökkentő használata

U-SQL lehetővé teszi egyéni sorkészlet csökkentő írhat C# használatával a felhasználó által definiált operátor bővíthetőségi keretrendszerét, és a egy IReducer felületet megvalósító.

Felhasználó által definiált nyomáscsökkentő vagy udr-t, a szükségtelen sorok kiküszöböléséhez adatkinyerés (importálás) során használható. Azt is segítségével kiértékelheti a sorok és oszlopok módosíthatók és jelenthetők. Programozhatóság logika alapján, azt is megadhatja, hogy mely sorokat kell frissítőcsomagot.

Egy UDR osztály definiálásához, létre kell hozni egy `IReducer` egy nem kötelező felületet `SqlUserDefinedReducer` attribútum.

Ez az osztály az interfész tartalmaznia kell a definícióját a `IEnumerable` felület sorkészlet felülbírálása.

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

A **SqlUserDefinedReducer** attribútum jelzi, hogy a típus, egy felhasználó által definiált nyomáscsökkentő kell regisztrálni. Ez az osztály nem örökölhetők.
**SqlUserDefinedReducer** egy nem kötelező attribútum felhasználó által definiált nyomáscsökkentő definíciófrissítés számára. IsRecursive tulajdonság meghatározására szolgál.

* logikai IsRecursive    
* **Igaz** = jelzi-e a nyomáscsökkentő társuló és commutative

A fő programozhatóság objektumok **bemeneti** és **kimeneti**. A bemeneti objektum segítségével bemeneti sorok számbavétele. Kimeneti eredményeként csökkenti a tevékenység kimeneti sorok beállítására használatos.

A bemeneti sorok enumerálás, használjuk a `Row.Get` metódust.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

A paraméter a `Row.Get` módszer a következő részeként átadott oszlop a `PRODUCE` osztályát az `REDUCE` rendszerállapot-kimutatását a U-SQL alapvető szkriptet. Használja a megfelelő adattípust itt is kell.

A kimenet, használja a `output.Set` metódust.

Fontos megérteni, hogy egyéni nyomáscsökkentő csak a megadott értéket adja vissza a `output.Set` metódus hívása.

```
output.Set<string>("mycolumn", guid);
```

Akkor aktiválódik, a tényleges nyomáscsökkentő kimeneti meghívásával `yield return output.AsReadOnly();`.

Következő egy nyomáscsökkentő példát:

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

Használati eset ebben a forgatókönyvben a nyomáscsökkentő kihagyja a sorokat egy üres felhasználónévvel. Sorhalmaz minden egyes sorára olvassa be minden szükséges oszlopot, ezután kiértékeli a felhasználónév hossza. Ez a tényleges sort jelenít meg, csak akkor, ha a felhasználó neve érték hossza 0-nál nagyobb.

Következő által használt egyéni nyomáscsökkentő alap U-SQL-parancsfájl:

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
