---
title: U-SQL-felhasználó által definiált operátorok fejlesztése (udo-k) az Azure Data Lake Analytics
description: Ismerje meg, hogyan hozhat létre felhasználó által definiált operátorok kell felhasználni, és újra felhasználható az Azure Data Lake Analytics-feladatok.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 122a4b6af78a22f74d5057da75767077f8d9b978
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496146"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Fejlesztés a U-SQL-felhasználó által definiált operátorok (udo-k)
Ez a cikk ismerteti, hogyan hozhat létre felhasználó által definiált operátorok az U-SQL-feladatok adatok feldolgozását.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Határozza meg, és a egy felhasználó által definiált operátor használata U-SQL
**Az egy U-SQL-feladat létrehozása és elküldése**

1. A Visual Studióban válassza **File > New > Project > U-SQL projekt**.
2. Kattintson az **OK** gombra. A Visual Studio létrehoz egy megoldást egy Script.usql fájllal.
3. A **Megoldáskezelőben**, bontsa ki a Script.usql, és kattintson duplán **Script.usql.cs**.
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
6. Nyissa meg **Script.usql**, és illessze be az alábbi U-SQL-parancsfájlt:

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
10. Ha még nem csatlakozott az Azure-előfizetéshez, az Azure-fiók hitelesítő adatainak megadását kéri.
11. Kattintson a **Submit** (Küldés) gombra. Eredmény és a feladatra mutató hivatkozás érhető el a találatokat tartalmazó ablakot, ha a beküldés sikeres volt.
12. Kattintson a **frissítése** tekintse meg a feladat legfrissebb állapotának képernyő frissítése gombra.

**A kimenet megtekintéséhez**

1. A **Server Explorer**, bontsa ki a **Azure**, bontsa ki a **Data Lake Analytics**, bontsa ki a Data Lake Analytics-fiók, **Storage-fiókok**, kattintson a jobb gombbal az alapértelmezett tároló, és kattintson **Explorer**.
2. Bontsa ki a mintákat, bontsa ki a kimeneteket, és kattintson duplán **Drivers.csv**.

## <a name="see-also"></a>Lásd még
* [U-SQL-kifejezések felhasználói kóddal kiterjesztése](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [A Data Lake Tools for Visual Studio használata a U-SQL-alkalmazások fejlesztéséhez](data-lake-analytics-data-lake-tools-get-started.md)
