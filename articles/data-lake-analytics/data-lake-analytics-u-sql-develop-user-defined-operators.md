---
title: Felhasználó által definiált U-SQL-operátorok fejlesztése – Azure Data Lake Analytics
description: Megtudhatja, hogyan fejleszthet felhasználó által definiált operátorokat Azure Data Lake Analytics feladatokban felhasználható és újra felhasználni.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: b2d1293b06b4d8791138ed666bc3cb4abe3adf40
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316536"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Felhasználó által definiált U-SQL-operátorok fejlesztése (Udo)
Ez a cikk azt ismerteti, hogyan lehet felhasználó által definiált operátorokat feldolgozni egy U-SQL-feladatokban.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Felhasználó által definiált operátor definiálása és használata az U-SQL-ben
**U-SQL-feladatok létrehozása és elküldése**

1. A Visual Studióban válassza a **fájl > új > project > U-SQL-projekt**elemet.
2. Kattintson az **OK** gombra. A Visual Studio létrehoz egy megoldást egy script. usql fájllal.
3. A **megoldáskezelő**bontsa ki a script. usql elemet, majd kattintson duplán a **script.usql.cs**elemre.
4. Illessze be a következő kódot a fájlba:

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;

        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Suisse", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };

                public override IRow Process(IRow input, IUpdatableRow output)
                {

                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");

                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);

                    return output.AsReadOnly();
                }
            }
        }
6. Nyissa meg a **script. usql**, és illessze be a következő U-SQL-parancsfájlt:

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);

        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    

        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);
7. Adja meg a Data Lake Analytics-fiókot, -adatbázist és -sémát.
8. A **Solution Explorer** eszközben kattintson a jobb gombbal a **Script.usql** fájlra, majd kattintson a **Build Script** (Parancsfájl létrehozása) elemre.
9. A **Solution Explorer** eszközben kattintson a jobb gombbal a **Script.usql** fájlra, majd kattintson a **Submit Script** (Parancsfájl elküldése) lehetőségre.
10. Ha még nem csatlakozott az Azure-előfizetéséhez, a rendszer kérni fogja az Azure-fiók hitelesítő adatainak megadását.
11. Kattintson a **Submit** (Küldés) gombra. A beküldési eredmények és a feladatok hivatkozása az eredmények ablakban érhető el, amikor a Küldés befejeződött.
12. Kattintson a **frissítés** gombra a feladatok legutóbbi állapotának megtekintéséhez és a képernyő frissítéséhez.

**A kimenet megtekintéséhez**

1. A **Server Explorerben**bontsa ki az **Azure**elemet, bontsa ki **Data Lake Analytics**, bontsa ki a Data Lake Analytics fiókot, bontsa ki a Storage- **fiókok**csomópontot, kattintson a jobb gombbal az alapértelmezett tárolóra, majd kattintson a
2. Bontsa ki a minták, majd a kimenetek csomópontot, majd kattintson duplán az **Drivers. csv**fájlra.

## <a name="see-also"></a>Lásd még
* [U-SQL-kifejezések kiterjesztése felhasználói kóddal](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [A Visual studióhoz készült Data Lake Tools használata az U-SQL-alkalmazások fejlesztéséhez](data-lake-analytics-data-lake-tools-get-started.md)
