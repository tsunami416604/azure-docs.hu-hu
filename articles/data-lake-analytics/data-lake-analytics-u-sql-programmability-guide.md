---
title: U-SQL programozástámogatási útmutató az Azure Data Lake |} Microsoft Docs
description: További tudnivalók az Azure Data Lake szolgáltatások, amelyek lehetővé teszik, hogy hozzon létre egy felhőalapú big Data típusú adatok platform készletét.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: saveenr
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/30/2017
ms.author: saveenr
ms.openlocfilehash: 400057b5ce79cdcf6c7651462e9f497bf647e930
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="u-sql-programmability-guide"></a>U-SQL programozástámogatási útmutató

U-SQL lekérdezésnyelvet, amely a big data-típusú munkaterhelések célja. A U-SQL egyedi jellemzői egyike a bővíthetőség és a C# által biztosított programozhatóság deklaratív SQL-szerű nyelv kombinációja. Ebben az útmutatóban azt koncentrálni bővíthetőségi és a U-SQL nyelv szerint C# engedélyezett programozhatóság.

## <a name="requirements"></a>Követelmények

Töltse le és telepítse [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Ismerkedés a U-SQL  

Tekintse meg az alábbi U-SQL-parancsfájlt:

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

Ez a parancsfájl határozza meg két sorkészletek: `@a` és `@results`. A sorhalmaz `@results` van definiálva a `@a`.

## <a name="c-types-and-expressions-in-u-sql-script"></a>C# típusok és kifejezések a U-SQL-parancsfájl

U-SQL-kifejezést, például a U-SQL logikai műveletekkel együtt egy C# kifejezés `AND`, `OR`, és `NOT`. Válassza ki, a KINYERÉSI, U-SQL kifejezések használhatók WHERE, GROUP BY és DEKLARÁLHATÓ. Például a következő parancsfájl egy karakterlánc elemzi a DateTime értéknek.

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Az alábbi részlet egy karakterlánc elemez egy DECLARE utasítást DateTime értékként.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>C# kifejezések használhatók adatok típuskonverziók

A következő példa bemutatja, hogyan teheti a datetime adatok átalakítás C# kifejezések használatával. Ez az adott esetben karakterlánc datetime adatok éjfél 00:00:00 idő jelölése szabványos datetime lesz konvertálva.

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

### <a name="use-c-expressions-for-todays-date"></a>C# kifejezések használhatók mai dátum

Lekéréses mai dátum, azt használja az alábbi C# kifejezés: `DateTime.Now.ToString("M/d/yyyy")`

Itt látható egy példa egy parancsfájlban a kifejezés használatával:

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

U-SQL-kiterjeszthetőségi modell fokozottan támaszkodik a .NET-szerelvények egyéni kód hozzáadását. 

### <a name="register-a-net-assembly"></a>A .NET-szerelvény regisztrálása

Használja a `CREATE ASSEMBLY` utasítás helyezhető el egy .NET-szerelvény egy U-SQL-adatbázisba. Ezután U-SQL-parancsfájlok is az adott szerelvény használatával a `REFERENCE ASSEMBLY` utasítást. 

A következő kód bemutatja, hogyan kell regisztrálni egy szerelvény:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

A következő kód bemutatja, hogyan való hivatkozáshoz egy szerelvény:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Tekintse át a [szerelvény regisztrációs utasítások](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) , hogy ez a témakör részletesebben foglalkozik.


### <a name="use-assembly-versioning"></a>Használja a szerelvény verziószámozása
U-SQL jelenleg a .NET-keretrendszer 4.5-ös verzióját használja. Ezért figyeljen oda arra, hogy a saját szerelvények kompatibilisek, hogy a futtatókörnyezet verziója.

A korábbiak U-SQL kódot futtat egy 64 bites (x 64) formátumban. Ezért győződjön meg arról, hogy a kód fordítását, akkor a x64 futtatásához. Ellenkező esetben kap a korábban bemutatott helytelen formátuma hibás.

Minden egyes szerelvény dll-fájl feltöltése és erőforrás-fájl, például egy másik futásidejű, natív szerelvény vagy egy konfigurációs fájl legfeljebb 400 MB lehet. A telepített erőforrások telepítése erőforrás keresztül vagy keresztül szerelvények és a további fájlok teljes mérete nem haladhatja meg a 3 GB.

Végül vegye figyelembe, hogy minden U-SQL-adatbázist csak tartalmazhat bármely adott valamely verzióját. Például mind a 7-es verzió, és a NewtonSoft Json.Net függvénytár 8-as verzió van szükség, ha szüksége regisztrálja őket a két különböző adatbázist. Továbbá minden parancsprogram csak hivatkozhat valamely verzióját egy adott DLL-Fájljában. Ebben a tekintetben a U-SQL követi a C# szerelvény felügyeleti és versioning szemantikáját.

## <a name="use-user-defined-functions-udf"></a>Felhasználó által definiált függvények: UDF-ben
U-SQL-felhasználó által definiált függvények, illetve UDF, vannak programozási rutinok fogadnak el paramétereket, egy műveletet (például egy bonyolult számításhoz), és az érték a művelet eredményét adja vissza. Az UDF visszatérési értéke csak lehet egyetlen skaláris. U-SQL UDF U-SQL alapvető parancsfájlban, mint bármely más C# skaláris függvényt hívható.

Azt javasoljuk, hogy a U-SQL felhasználó által definiált függvények, inicializálni **nyilvános** és **statikus**.

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

Első létrehozásának egy UDF egyszerű példa vizsgáljuk meg.

Ilyenkor használati eset kell meghatározni az időszak, beleértve a pénzügyi negyedév és az első bejelentkezés az adott felhasználó pénzügyi hónapja. A mi esetünkben az év első pénzügyi hónapja. június.

Pénzügyi időszak kiszámításához, a következő C# függvény bemutatása után:

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

Egyszerűen csak a pénzügyi hónap és negyedév számítja ki, és egy karakterláncértéket ad vissza. A júniusi az első olyan hónap első pénzügyi negyedév használjuk "Q1:P1". Júliusi "Q1:P2" felhasználása, és így tovább.

Ez az a rendszeres C# függvény fogjuk használni a U-SQL projekt.

Ez a háttérkód szakasz megjelenését az ebben a forgatókönyvben:

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

Most fogjuk ezt a függvényt hívja az alap U-SQL parancsfájl. Ehhez az szükséges, adjon meg egy teljesen minősített nevet a funkciót, beleértve a névtér, amely a jelen esetben ez NameSpace.Class.Function(parameter) kell.

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Az alábbiakban látható a tényleges alap U-SQL-parancsfájlt:

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

Az alábbiakban látható a kimeneti fájl, a parancsfájl végrehajtásának:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Ez a példa azt mutatja be, a egyszerű beágyazott UDF U-SQL-használat.

### <a name="keep-state-between-udf-invocations"></a>Között UDF állapotban tartása
U-SQL C# programozhatóság objektumok is lehet több kifinomult, interaktív keresztül a háttérkód globális változókat használ. A következő üzleti használati eset forgatókönyv vizsgáljuk meg.

Nagy méretű szervezeteknek, a felhasználók belső alkalmazások fajtáinak válthat. Ezek lehetnek Microsoft Dynamics CRM-, Power BI, és így tovább. Az ügyfelek szeretné alkalmazni a telemetriai adatok elemzését hogyan felhasználók váltogatnak a különböző alkalmazások között, a használati trendeket Mik, és így tovább. A vállalati célja, hogy az alkalmazás használatának optimalizálása. Akkor érdemes is kombinálhatja a különböző alkalmazások vagy adott bejelentkezés rutinok.

E cél eléréséhez van a munkamenet-azonosítókat, és időbeli között a legutóbbi munkamenet történt.

Igazolnia kell a korábbi bejelentkezés található, és hozzárendelheti a bejelentkezés az azonos alkalmazáshoz létrehozott összes munkamenet. A rendszer az első ellenőrzést, hogy alap U-SQL-parancsfájl nem lehetővé teszik a számunkra számítások alkalmazni a LAG függvény már számított oszlopok keresztül. A második kihívás, hogy tudunk-e az adott munkamenet összes munkamenetek azonos idő alatt tartani.

A probléma megoldásához, a Microsoft globális változók használata a háttérkód szakaszon belül: `static public string globalSession;`.

A globális változót a teljes sorhalmaz alkalmazzák a parancsfájl végrehajtása közben.

A U-SQL program háttérkód szakasza a következő:

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

Ez a példa bemutatja a globális változó `static public string globalSession;` használni a `getStampUserSession` és az első újrainicializálni minden alkalommal, amikor a munkamenet-paraméter módosul.

A kiinduló U-SQL-parancsfájl a következőképpen történik:

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

Függvény `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` ide a második memória sorhalmaz számítás során nevezik. Adja át a `UserSessionTimestamp` oszlop értékének a visszaadása, amíg és `UserSessionTimestamp` megváltozott.

A kimeneti fájl a következőképpen történik:

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

Ez a példa bemutatja egy bonyolultabb használati eset forgatókönyvet, amely a teljes memória sorhalmaz alkalmazott a háttérkód szakaszon belül egy globális változó használjuk.

## <a name="use-user-defined-types-udt"></a>Felhasználó által definiált tárolótípus: UDT
Felhasználó által definiált típusok vagy UDT, egy másik programozástámogatás az U-SQL. U-SQL UDT úgy viselkedik, mint egy normál C# felhasználó által definiált típus. C# egy szigorú típusmegadású nyelv, amely lehetővé teszi a beépített és egyéni, felhasználó által definiált típusok használatát.

U-SQL implicit módon nem szerializálható vagy deszerializálható tetszőleges UDTs, ha az UDT a csúcsban lévő készletek között. Ez azt jelenti, hogy a felhasználó rendelkezik-e adjon meg egy explicit formázó IFormatter felület használatával. Ez a szerializálása biztosít a U-SQL és deszerializálni az UDT a módszerek.

> [!NOTE]
> U-SQL beépített vagyis és outputters jelenleg nem szerializálható vagy deszerializálható UDT adatok vagy a fájlok a IFormatter beállítása mellett is. Ezért amikor UDT adatainak írása a kimeneti kivonat fájlba, vagy az egy kivonatoló adatellenőrzés, akkor egy karakterlánc- vagy bájt tömbként adja át. Majd hívja a szerializálás és explicit módon deszerializálási kódot (Ez azt jelenti, hogy az UDT ToString() módszer). Vagyis felhasználói és outputters, másrészt, is olvasási és írási UDTs.

Ha azt próbálja UDT készülék vagy OUTPUTTER (kívül előző SELECT), a használandó, ahogy az itt látható:

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Jelenleg a következő hibaüzenet jelenik meg:

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

Szeretne dolgozni az UDT outputter, vagy tudunk string ToString() módszerrel, vagy hozzon létre egy egyéni outputter szerializálni.

UDTs jelenleg nem használható GROUP BY. Ha UDT használja a GROUP BY, a következő hiba történt:

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

Egy UDT definiálásához kell:

* A következő névterek:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Adja hozzá `Microsoft.Analytics.Interfaces`, ami azonban szükséges az UDT csatolók. Ezenkívül `System.IO` meghatározhatja a IFormatter felületet szükség lehet.

* Adja meg a SqlUserDefinedType attribútum által definiált típus.

**SqlUserDefinedType** segítségével jelölje meg a felhasználó által definiált típus (UDT) U-SQL-szerelvények típusdefinícióban. Az attribútum tulajdonságainak tükrözze az UDT fizikai tulajdonságait. Ez az osztály nem örökölhető.

SqlUserDefinedType UDT definíciójának egyik kötelező attribútuma.

A konstruktor osztály:  

* SqlUserDefinedTypeAttribute (típus formázó)

* Írja be a formázó: kötelező paraméter az UDT formázó--, megadhatók a típusú a `IFormatter` felület itt kell átadni.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Tipikus UDT IFormatter felület meghatározását is szükséges, a következő példában látható módon:

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

A `IFormatter` felület rendezi sorba, és vonja rendezi sorba a legfelső szintű típusú objektumdiagram \<typeparamref name = "T" >.

\<típusparaméter neve = "T" > szerializáltuk és a diagramot a legfelső szintű típusát.

* **Deszerializálni**: deszerializálni rendezi sorba az adatokat a megadott adatfolyamon, és a graph-objektumok reconstitutes.

* **Szerializálható**: egy objektumot, vagy graph-objektumok, a megadott adatfolyam adott gyökérrel rendelkező rendezi sorba.

`MyType` példány: a típus példánya.  
`IColumnWriter` író / `IColumnReader` olvasó: az alapul szolgáló folyam oszlop.  
`ISerializationContext` a környezetben: származó jelzők egy csoportját, amely megadja a cél- és környezetben az adatfolyam a szerializálás során határozza meg.

* **Köztes**: Megadja, hogy a cél- és a környezetben nem megőrzött tároló.

* **Adatmegőrzési**: Megadja, hogy a cél- és környezeti egy megőrzött tárolóban.

Rendszeres C# típus, egy U-SQL UDT definition például felülbírálások az operátorok, mint / == /! =. Statikus metódusok is tartalmazhatja. Például, ha azt tervezi használni az UDT egy U-SQL MIN összesítő függvény paramétereként, azt kell meghatároznia < operátor felülbírálás.

Ez az útmutató során korábban küldje el azt egy például pénzügyi időszak azonosításához formátumban megadott dátumtól `Qn:Pn (Q1:P10)`. A következő példa bemutatja, hogyan adhat meg egy egyéni típus a pénzügyi küszöbidőszakok értékeit.

Az alábbiakban látható egy példa egy egyéni UDT és IFormatter felülettel háttérkód szakaszt:

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

A megadott típus két számot tartalmaz: negyedév, hónap és. Operátorok `==/!=/>/<` és statikus metódus `ToString()` az itt megadott.

Amint azt korábban említettük, az UDT kiválasztási kifejezések használhatók, de nem használható OUTPUTTER/KIVONATOLÓ egyedi szerializálás nélkül. A string típusúként szerializálandó vagy rendelkezik `ToString()` vagy egy egyéni OUTPUTTER KIVONATOLÓ használni.

Most tegyük a UDT használatát tárgyalja. A háttérkód szakasz úgy módosítottuk, a GetFiscalPeriod függvény a következő:

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

Ahogy látja, a FiscalPeriod típusú értéket adja vissza.

Itt nyújtunk használatával tovább alap U-SQL-parancsfájl egy példát. Ebben a példában a U-SQL parancsfájl UDT foglalja különböző űrlapok mutatja be.

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

Íme egy példa a teljes háttérkód szakasz:

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
Felhasználó által definiált összesítések bármely összesítési funkciókat, amelyek nem képezte a a-kész U-SQL. A példa lehet az összesítő kifejezés egyéni matematikai számítások, karakterláncok összefűzése, karakterláncok, vagy más műveletek végrehajtásához.

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

**SqlUserDefinedAggregate** azt jelzi, hogy a típus, egy felhasználó által definiált összesítő kell regisztrálni. Ez az osztály nem örökölhető.

Az attribútum SqlUserDefinedType **választható** UDAGG definíciójának.


Az alaposztály lehetővé teszi a három absztrakt paraméterek: két bemeneti paraméterek, a másik eredményezi. Az adattípusok változóak, és meg kell határozni az osztályöröklődést során.

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

* **Init** hív meg, egyszer az egyes csoportok számítás során. Egy inicializálási rutin biztosít az egyes összesítést.  
* **Felhalmozhat** minden egyes értékre egyszer végrehajtja. A fő funkciókat biztosít a összesítési algoritmus. Az összesített értékek különböző adattípusokkal osztályöröklődést során meghatározott használható. Változó adattípust két paramétert fogadja a.
* **Állítsa le** csoportonként összesítési feldolgozása végén az eredmény az egyes csoportokhoz való egyszer végrehajtja.

Megfelelő bemeneti és kimeneti adattípus deklarálnia, az osztálykiterjesztések definíciója a következőképpen használhatja:

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1 kapcsolaton keresztül: Felhalmozhat első paramétert
* T2: Felhalmozhat első paramétert
* TResult: Visszatérési típusa megszakítás miatt

Példa:

```
public class GuidAggregate : IAggregate<string, int, int>
```

vagy

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>A U-SQL UDAGG használata
UDAGG használatához először határozza meg azt a háttérkód vagy a létező programozhatóság DLL-ből hivatkozni rá, a fentiekben taglaltak.

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

És a következő alap U-SQL-parancsfájlt:

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

Használati eset ebben a forgatókönyvben azt összefűzésére osztály GUID azoknak a felhasználóknak.

## <a name="use-user-defined-objects-udo"></a>Felhasználó által definiált-objektumokat használ: UDO
U-SQL lehetővé teszi, hogy adhatók meg egyéni programozhatóság objektumokat, amely a felhasználói objektumok vagy UDO nevezzük.

A következő egy U-SQL UDO listáját:

* Vagyis felhasználói
    * Soronként extract
    * Adatok kinyerése egyéni strukturált fájlokból végrehajtásához használt

* Felhasználó által definiált outputters
    * Kimeneti soronként
    * Kimeneti egyéni adattípus vagy egyéni fájl használt formátum

* Felhasználó által definiált processzor
    * Egy olyan sor, majd előállítanak egy sor
    * Csökkentse az oszlopok számát, vagy létrehozhat egy meglévő oszlop készletből származó értékekkel új oszlopok használatával

* Felhasználó által definiált appliers
    * Egy olyan sor, majd előállítanak n sorokra 0
    * KÜLSŐ/határokon ALKALMAZ együtt

* Felhasználó által definiált combiners
    * Egyesíti a sorkészletek – felhasználó által definiált illesztéseket

* Felhasználó által definiált szűkítő
    * N sort, majd előállítanak egy sor
    * A sorok számának csökkentése érdekében használja

UDO neve általában explicit módon a U-SQL-parancsfájlt az alábbi U-SQL-utasítások részeként:

* EXTRACT
* KIMENETI
* FOLYAMAT
* CSOPORTOSÍTÁS
* CSÖKKENTÉSE

> [!NOTE]  
> UDO által korlátozott 0,5 Gb memóriát használ.  Ez a memória-korlátozás nem vonatkozik a helyi végrehajtások.

## <a name="use-user-defined-extractors"></a>Felhasználó által definiált vagyis használata
U-SQL külső adatimportálás kivonat utasítás segítségével teszi lehetővé. Egy KIVONATOT utasítás beépített UDO vagyis használhatja:  

* *Extractors.Text()*: különböző kódolások kivonása a tagolt szövegfájlok biztosít.

* *Extractors.Csv()*: kibontási vesszővel tagolt (CSV) fájljai különböző kódolások biztosít.

* *Extractors.Tsv()*: biztosít lapon tagolt kibontási különböző kódolások (TSV) fájljait.

Egy egyéni kivonatoló fejlesztéséhez hasznos lehet. Ez hasznos lehet adatok importálása során Ha azt szeretné, hogy hajtsa végre a következő feladatokat:

* Módosítsa a bemeneti adatok felosztása oszlopok, és egyedi értékek módosítása. A PROCESSZOR funkcióinak jobb az oszlopok kombinálásával.
* Strukturálatlan adatok, például a weblapok és e-maileket vagy félig strukturált adatok, például az XML/JSON elemezni.
* A kódolás nem támogatott adatok elemzése.

A felhasználói készülék, vagy a szükséges meghatározásához, létre kell hoznunk egy `IExtractor` felületet. Az összes bemeneti paramétereit, és a kivonatoló oszlop vagy sor egyes elválasztó karakterek, például és kódolást kell meghatározni az osztály konstruktorában. A `IExtractor` felületet is tartalmaznia kell a következő definícióját: a `IEnumerable<IRow>` bírálja felül az alábbiak szerint:

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

A **SqlUserDefinedExtractor** attribútum azt jelzi, hogy a típus, egy felhasználó által definiált kivonatoló kell regisztrálni. Ez az osztály nem örökölhető.

SqlUserDefinedExtractor szükséges definíciója nem kötelező attribútum értéke. Azt határozza meg a szükséges vonatkozó AtomicFileProcessing tulajdonság használatával.

* logikai AtomicFileProcessing   

* **Igaz** = annak jelzése, hogy a kivonatoló igényel atomi bemeneti fájlok (JSON, XML,...)
* **hamis** = annak jelzése, hogy a kivonatoló tudnak kezelni felosztása / elosztott fájlok (CSV, SEQ,...)

A fő szükséges programozhatóság objektumok **bemeneti** és **kimeneti**. A bemeneti objektum használt operációs rendszer bemeneti adatok `IUnstructuredReader`. A kimeneti objektum lehet beállítani a kivonatoló tevékenység eredményeként kimeneti adatokat.

A bemeneti adatok rendszeren keresztül elérhető `System.IO.Stream` és `System.IO.StreamReader`.

Bemeneti oszlopok számbavétel azt először ossza fel a bemeneti adatfolyam egy sor elválasztó használatával.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Ezt követően további felosztása bemeneti sor oszlop részre.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Kimeneti adatainak beállításához használjuk a `output.Set` metódust.

Fontos megérteni, hogy az egyéni kivonatoló csak adja kimenetként, oszlopok és megadott értékek a kimenet. Állítsa be a metódus hívása.

```
output.Set<string>(count, part);
```

A tényleges kivonatoló kimeneti kiváltásáról meghívásával `yield return output.AsReadOnly();`.

Az alábbiakban a kivonatoló példa látható:

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

Használati eset ebben a forgatókönyvben a kivonatoló újragenerálja a "guid" oszlophoz tartozó GUID, és nagybetűsre alakítja át a "user" oszlopban levő értékeket. Egyéni vagyis is eredményt bonyolultabb bemeneti adatok elemzése és módosítja azokat.

Az alábbiakban olvashat egy egyéni kivonatoló használó alap U-SQL-parancsfájlt:

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
Felhasználó által definiált outputter egy másik U-SQL UDO, amely lehetővé teszi beépített U-SQL bővíthetők. A kivonatoló hasonlóan, több beépített outputters vannak.

* *Outputters.Text()*: a különböző kódolások tagolt szövegfájlok írja az adatokat.
* *Outputters.Csv()*: írja az adatokat a különböző kódolások vesszővel tagolt (CSV) fájlok.
* *Outputters.Tsv()*: írja az adatokat a különböző kódolások lapon elválasztott értékeket (TSV) fájlok.

Egyéni outputter lehetővé teszi az adatok írása az adott formátumban. Ez lehet hasznos, ha a következő feladatokat:

* Írás a félig strukturált vagy strukturálatlan fájlokat.
* Írás a nem támogatott kódolások.
* Kimeneti adatok módosítása vagy hozzáadása az egyéni attribútumok.

Felhasználó által definiált outputter meghatározásához, létre kell hoznunk a `IOutputter` felületet.

Az alábbiakban látható a következő `IOutputter` osztály végrehajtására:

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

A outputter oszlop vagy sor elválasztó karaktert, kódolási és így tovább, például az összes bemeneti paramétereket kell meghatározni az osztály konstruktorában. A `IOutputter` felületet is tartalmaznia kell a következő definícióját: `void Output` bírálja felül. Az attribútum `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` atomi üzenetfájl-feldolgozása nem kötelezően állítható be. További információkért tekintse meg az alábbi részleteit.

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

* `Output` minden egyes bemeneti sorára neve. Azt adja vissza a `IUnstructuredWriter output` sorkészlete.
* A konstruktor osztály paraméterek átadása a felhasználó által definiált outputter szolgál.
* `Close` opcionálisan bírálja felül, ha a kiadási költséges állapotát, vagy határozza meg, ha az utolsó sor írt szolgál.

**SqlUserDefinedOutputter** attribútum azt jelzi, hogy a típus, egy felhasználó által definiált outputter kell regisztrálni. Ez az osztály nem örökölhető.

SqlUserDefinedOutputter egy nem kötelező attribútum felhasználó által definiált outputter meghatározása. Adja meg a AtomicFileProcessing tulajdonság szolgál.

* logikai AtomicFileProcessing   

* **Igaz** = annak jelzése, hogy a outputter igényel atomi kimeneti fájlok (JSON, XML,...)
* **hamis** = annak jelzése, hogy a outputter tudnak kezelni felosztása / elosztott fájlok (CSV, SEQ,...)

A fő programozhatóság objektumok **sor** és **kimeneti**. A **sor** objektum szolgál kimenetet számbavétele `IRow` felületet. **Kimeneti** kimeneti adatainak beállításához a célfájl szolgál.

A kimeneti adatok keresztül érhető el a `IRow` felületet. Kimeneti adatok átadása egy sor egyszerre.

Az egyedi értékek vannak számba veszi a Get metódust, az IRow illesztőfelületet:

```
row.Get<string>("column_name")
```

Egyedi oszlopnevek meghívásával lehet meghatározni `row.Schema`:

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Ez a megközelítés lehetővé teszi egy rugalmas outputter minden metaadat-séma összeállítását.

A kimeneti adatok íródik a következő fájl használatával `System.IO.StreamWriter`. Az adatfolyam-paraméter értéke `output.BaseStrea` részeként `IUnstructuredWriter output`.

Vegye figyelembe, hogy fontos ürítse ki a puffer a fájl minden egyes sorára iteráció után. Emellett a `StreamWriter` objektum attribútummal jelölték őket a rendelkezésre álló engedélyezve (alapértelmezett), és együtt kell használni a **használatával** kulcsszó:

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Ellenkező esetben Flush() metódus hívható explicit módon mindegyik iteráció után. Ez az alábbi példa azt megjelenítése.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>A felhasználó által definiált outputter fejlécek és láblécek beállítása
A fejléc használja egy iteráció végrehajtási folyamata.

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

A kódot az első `if (isHeaderRow)` blokk csak egyszer végrehajtja.

A lábléchez használja a rendszer az példányát `System.IO.Stream` objektum (`output.BaseStream`). Írja be a lábléc Close() módszer a `IOutputter` felületet.  (További információkért lásd az alábbi példa.)

Az alábbiakban látható egy példa egy felhasználó által definiált outputter:

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

És alap U-SQL-parancsfájlt:

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

Ez az egy HTML outputter, amely hoz létre egy HTML-fájlba tábla adatai.

### <a name="call-outputter-from-u-sql-base-script"></a>A U-SQL alapvető parancsprogramból hívás outputter
Egy egyéni outputter hívja az alap U-SQL parancsfájlt, az új példány outputter objektum-nek hozható létre.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Az objektum példányának létrehozásakor alap parancsfájlban elkerüléséhez vannak, létrehozható egy függvény burkoló a korábbi példában látható módon:

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

Ebben az esetben az eredeti hívás a következő:

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Felhasználó által definiált processzor
Processzor felhasználói vagy UDP, egyfajta U-SQL UDO, amely lehetővé teszi a bejövő sorok feldolgozni programozhatóság szolgáltatások alkalmazásával. UDP oszlopok kombinálhatja, értékeket módosíthatja, és szükség esetén új oszlopok hozzáadása teszi lehetővé. Alapvetően ennek segítségével feldolgozni egy sorhalmaz kötelező adatelemek létrehozásához.

Egy UDP meghatározásához, létre kell hoznunk egy `IProcessor` kommunikáljanak a `SqlUserDefinedProcessor` attribútumot, amely nem kötelező UDP-hez.

Ez az interfész vonatkozó definíciójának tartalmaznia kell a `IRow` felület sorhalmaz bírálja felül, a következő példában látható módon:

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

**SqlUserDefinedProcessor** azt jelzi, hogy a típus, felhasználó által definiált processzort kell regisztrálni. Ez az osztály nem örökölhető.

A SqlUserDefinedProcessor attribútum **választható** UDP definíciójához.

A fő programozhatóság objektumok **bemeneti** és **kimeneti**. A bemeneti objektum oszlopok bemeneti és kimeneti számbavétele, és állítsa be a kimeneti adatok miatt a processzortevékenység szolgál.

Bemeneti oszlopok számbavétel használjuk a `input.Get` metódust.

```
string column_name = input.Get<string>("column_name");
```

A paramétert a `input.Get` metódus részeként átadott oszlop a `PRODUCE` záradékában a `PROCESS` utasítás a U-SQL-parancsprogram. Igazolnia kell a megfelelő adattípusú itt használja.

A kimeneti, használja a `output.Set` metódust.

Fontos megjegyezni, hogy egyéni termelő csak kimenete, oszlopok és értékek rendelkező a `output.Set` metódus hívása.

```
output.Set<string>("mycolumn", mycolumn);
```

A tényleges processzor kimeneti kiváltásáról meghívásával `return output.AsReadOnly();`.

Az alábbiakban egy processzor példa látható:

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

Használati eset ebben a forgatókönyvben a processzor a meglévő oszlopok – ebben az esetben a "user" nagybetű, és a "des" kombinálásával "full_description" nevű új oszlopot hoz létre. Újragenerálja egy GUID Azonosítót és a adja vissza az eredeti és az új GUID-értékeket.

Ahogy az előző példából látja, hívása során módszerek C# `output.Set` metódus hívása.

Az alábbiakban látható használó egyéni alap U-SQL-parancsfájl egy példát:

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
A U-SQL-felhasználó által definiált applier lehetővé teszi a minden egyes sorára, a külső tábla kifejezés egy lekérdezés által visszaadott egyéni C# függvényt. A bal oldali bemeneti minden egyes sorára kiértékeli a jobb oldali bemeneti, és a végső kimenetet a sorok, amelyekre előállítása kombinálva. A listában, amelyik az APPLY operátor szükséges hozzák létre a bal és jobb oldali bemeneti oszlopok csoportja állnak.

Felhasználó által definiált applier van meghívott USQL válasszon kifejezés részeként.

A felhasználó által definiált applier tipikus hívása a következőképpen néznek:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Válassza ki a kifejezésben appliers használatával kapcsolatos további információkért lásd: [U-SQL KERESZT-alkalmazás és külső alkalmazás kiválasztása kijelölésével](https://msdn.microsoft.com/library/azure/mt621307.aspx).

A felhasználó által definiált applier alap osztálydefiníció a következőképpen történik:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

A felhasználó által definiált applier meghatározásához, létre kell hoznunk a `IApplier` kommunikáljanak a [`SqlUserDefinedApplier`] attribútumot, amely nem kötelező megadni egy felhasználói applier definíciójának.

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

* Alkalmazza a külső tábla minden sorára nevezik. Azt adja vissza a `IUpdatableRow` sorhalmaz kimeneti.
* A konstruktor osztály paraméterek átadása a felhasználó által definiált applier szolgál.

**SqlUserDefinedApplier** azt jelzi, hogy a típus, egy felhasználó által definiált applier kell regisztrálni. Ez az osztály nem örökölhető.

**SqlUserDefinedApplier** van **választható** felhasználói applier meghatározása.


A fő programozhatóság objektumok a következők:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Bemeneti sorkészletek átadása pedig `IRow` bemeneti. A kimeneti sorok akkor jönnek létre, mint a `IUpdatableRow` kimeneti felületet.

Egyedi oszlopnevek meghívásával lehet meghatározni a `IRow` séma metódust.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

A tényleges adatok értékek lekérése a bejövő `IRow`, Get() módszert használjuk `IRow` felületet.

```
mycolumn = row.Get<int>("mycolumn")
```

Vagy oszlopban a sémanév használjuk:

```
row.Get<int>(row.Schema[0].Name)
```

A kimeneti értéket be kell állítani a `IUpdatableRow` kimenete:

```
output.Set<int>("mycolumn", mycolumn)
```

Fontos tudni, hogy egyéni appliers csak kimeneti oszlopok és értékek rendelkező `output.Set` metódus hívása.

A tényleges kimeneti kiváltásáról meghívásával `yield return output.AsReadOnly();`.

A felhasználó által definiált applier paraméterek átadhatók a konstruktor. Applier adhat vissza oszlopokat kell meghatározni az alap U-SQL parancsfájl applier hívás során változó számú.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Ez a felhasználó által definiált applier példa:

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

Az alábbiakban látható a alap U-SQL-parancsfájlt a felhasználó által definiált applier:

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

A felhasználási forgatókönyve, a felhasználó által definiált applier egy vesszővel elválasztott értéket elemző a car flotta tulajdonságok funkcionál. A bemeneti fájl sorok a következőhöz hasonló:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Shevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Tipikus tabulátorral tagolt TSV fájl car tulajdonságait, például gyártmányának és modelljének tulajdonságai oszlopa. Ezek a tulajdonságok a tábla oszlopaihoz kell értelmezni. A megadott applier is lehetővé teszi tulajdonságok dinamikus száma létrehozni az eredmény sorhalmaz, az átadott paraméter alapján. Az összes tulajdonság vagy a tulajdonságok csak egy meghatározott is létrehozhat.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

A felhasználó által definiált applier applier objektum egy új példányt hívható meg:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

Vagy a gyári burkoló metódus hívásához:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Felhasználó által definiált combiners használata
Felhasználó által definiált egyesítő vagy UDC, lehetővé teszi kombinálhatja a bal és jobb sorkészletek, egyéni logikán alapuló sorát. Felhasználó által definiált egyesítő EGYESÍTÉSE kifejezéssel szolgál.

A KOMBINÁCIÓBA kifejezéssel, mind a bemeneti sorkészletek, a csoportosító oszlopokra, a várt eredmény séma és további információ a szükséges információt nyújt egy egyesítő van meghívott.

Egy egyesítő alap U-SQL parancsfájl-hívás, azt a következő szintaxissal:

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

További információkért lásd: [EGYESÍTÉSE kifejezés (U-SQL)](https://msdn.microsoft.com/library/azure/mt621339.aspx).

A felhasználó által definiált egyesítő meghatározásához, létre kell hoznunk a `ICombiner` kommunikáljanak a [`SqlUserDefinedCombiner`] attribútumot, amely nem kötelező megadni egy felhasználói egyesítő definíciójának.

Alap `ICombiner` osztály definíciója:

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

Egyéni végrehajtásának egy `ICombiner` felület vonatkozó definíciójának tartalmaznia kell egy `IEnumerable<IRow>` kombinálhatja a felülbírálás.

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

A **SqlUserDefinedCombiner** attribútum azt jelzi, hogy a típus, egy felhasználó által definiált egyesítő kell regisztrálni. Ez az osztály nem örökölhető.

**SqlUserDefinedCombiner** a egyesítő mód tulajdonság azonosítására szolgál. A felhasználó által definiált egyesítő definíciója nem kötelező attribútum is.

CombinerMode mód

CombinerMode felsorolás a következő értékeket veheti:

* Teljes (0) minden kimenő sor potenciálisan függ az összes bemeneti sor balról jobbra pedig azonos kulcs értéke.

* Balra (1) minden kimenő sor egyetlen bemeneti sor balra (és a jobb oldali kulcs azonos értékű potenciálisan összes sorát) függ.

* Jobb (2) minden kimenő sor egyetlen bemeneti sor jobb (és a bal oldali kulcs azonos értékű potenciálisan összes sorát) függ.

* Belső (3) minden kimenő sor bal és jobb azonos értékű egyetlen bemeneti sor függ.

Példa: [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


A fő programozhatóság objektumok a következők:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Bemeneti sorkészletek átadása pedig **bal oldali** és **jobb** `IRowset` interfész típusa. Mindkét sorkészletek kell számba feldolgozásra. Csak enumerálása csatolóhoz egyszer, így azt kell felsorolni és gyorsítótárazása azt, ha szükséges.

Gyorsítótárazás céljából, létrehozhatjuk listáját\<T\> memória struktúra típusú következtében LINQ lekérdezés végrehajtása, kifejezetten lista <`IRow`>. A névtelen adattípus használható, valamint a számbavételi művelet során.

Lásd: [LINQ-lekérdezések (C#) bemutatása](https://msdn.microsoft.com/library/bb397906.aspx) LINQ-lekérdezések kapcsolatos további információk és [IEnumerable\<T\> felület](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx) IEnumerable illesztőfelületet tájékozódhat\<T\> felületet.

A tényleges adatok értékek lekérése a bejövő `IRowset`, Get() módszert használjuk `IRow` felületet.

```
mycolumn = row.Get<int>("mycolumn")
```

Egyedi oszlopnevek meghívásával lehet meghatározni a `IRow` séma metódust.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Vagy oszlopban a sémanév használatával:

```
c# row.Get<int>(row.Schema[0].Name)
```

A LINQ általános enumerálása a következőképpen néznek:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Mindkét sorkészletek enumerálása, után fogjuk ismétlése minden sor. A bal oldali sorhalmaz minden egyes sorára fogjuk található összes sorát, amelyek megfelelnek a egyesítő feltétele.

A kimeneti értéket be kell állítani a `IUpdatableRow` kimeneti.

```
output.Set<int>("mycolumn", mycolumn)
```

A tényleges kimeneti váltja ki a hívása `yield return output.AsReadOnly();`.

Az alábbiakban egy egyesítő példa látható:

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

Használati eset ebben a forgatókönyvben a Microsoft által létrehozott az elemzési jelentést a közvetítő a. A cél, termékek, amelyek több mint 20 000 $ költségeket, és, amelyek a gyorsabb, mint a megadott időszakon belül rendszeres közvetítő keresztül webhelyen keresztül értékesítés kereséséhez.

Ez a alap U-SQL-parancsfájlt. Összehasonlíthatja az közötti reguláris ILLESZTÉSI és egy egyesítő logikai:

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

A felhasználó által definiált egyesítő egy új példányt a applier objektum hívható meg:

```
USING new MyNameSpace.MyCombiner();
```


Vagy a gyári burkoló metódus hívásához:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Felhasználó által definiált szűkítő használata

U-SQL egyéni sorhalmaz szűkítő írása C# nyelven íródtak a operátor felhasználó által definiált bővítési keretrendszert használja, és az IReducer illesztőfelületet megvalósító teszi lehetővé.

Felhasználó által definiált nyomáscsökkentő vagy UDR, szükségtelen sorok megszüntetése során az adatok kinyerése (importálás) használható. Azt is segítségével kezelhetők, és értékelje ki a sorok és oszlopok. Programozható logikán alapuló, azt is meghatározhat sorokat szeretne kinyerni.

Egy UDR osztály meghatározásához, létre kell hoznunk egy `IReducer` felületet és egy opcionális `SqlUserDefinedReducer` attribútum.

Ez az osztály az interfész tartalmaznia kell a következő definícióját: a `IEnumerable` felület sorhalmaz bírálja felül.

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

A **SqlUserDefinedReducer** attribútum azt jelzi, hogy a típus, egy felhasználó által definiált nyomáscsökkentő kell regisztrálni. Ez az osztály nem örökölhető.
**SqlUserDefinedReducer** egy nem kötelező attribútum felhasználó által definiált nyomáscsökkentő meghatározása. IsRecursive tulajdonság meghatározására szolgál.

* logikai IsRecursive    
* **Igaz** = annak jelzése, hogy a nyomáscsökkentő társuló és commutative

A fő programozhatóság objektumok **bemeneti** és **kimeneti**. A bemeneti objektum használt bemeneti sor enumerálása. Kimeneti kimeneti sorok tevékenység csökkentése miatt beállítására használatos.

Bemeneti sor számbavétel használjuk a `Row.Get` metódust.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

A paramétert a `Row.Get` metódus részeként átadott oszlop a `PRODUCE` az osztály a `REDUCE` utasítás a U-SQL-parancsprogram. A megfelelő adattípusú itt között is használnia kell.

A kimeneti, használja a `output.Set` metódust.

Fontos megérteni, hogy egyéni nyomáscsökkentő csak a meghatározott értékek kiírja a `output.Set` metódus hívása.

```
output.Set<string>("mycolumn", guid);
```

A tényleges nyomáscsökkentő kimeneti kiváltásáról meghívásával `yield return output.AsReadOnly();`.

Az alábbiakban egy nyomáscsökkentő példa látható:

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

Használati eset ebben a forgatókönyvben a nyomáscsökkentő átugorja a sorokat egy üres felhasználónévvel. Sorhalmaz minden egyes sorára minden szükséges oszlop beolvassa, ezután kiértékeli a felhasználó nevének hossza. Az aktuális sor azt kimenete, csak ha érték hossza 0-nál nagyobb.

Az alábbiakban olvashat egy egyéni nyomáscsökkentő használó alap U-SQL-parancsfájlt:

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
