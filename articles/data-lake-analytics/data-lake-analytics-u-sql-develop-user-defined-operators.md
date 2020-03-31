---
title: U-SQL felhasználó által definiált operátorok fejlesztése – Azure Data Lake Analytics
description: Ismerje meg, hogyan fejleszthet felhasználó által definiált operátorokat az Azure Data Lake Analytics-feladatokban való használathoz és újrafelhasználhatókhoz.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: b2d1293b06b4d8791138ed666bc3cb4abe3adf40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71316536"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>U-SQL felhasználó által definiált operátorok (ISO-k) fejlesztése
Ez a cikk azt ismerteti, hogyan fejleszthet felhasználó által definiált operátorokat az U-SQL-feladatban lévő adatok feldolgozásához.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Felhasználó által definiált operátor definiálása és használata az U-SQL-ben
**U-SQL feladat létrehozása és elküldése**

1. A Visual Studio alkalmazásban válassza a **Fájl > új > Project > u-SQL Project lehetőséget.**
2. Kattintson az **OK** gombra. A Visual Studio egy Script.usql fájllal hoz létre megoldást.
3. A **Solution Explorer programban**bontsa ki a Script.usql csomópontot, majd kattintson duplán a **Script.usql.cs**.
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
6. Nyissa meg **a Script.usql fájlt,** és illessze be a következő U-SQL parancsfájlt:

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
10. Ha nem csatlakozott az Azure-előfizetéséhez, a rendszer kérni fogja az Azure-fiók hitelesítő adatainak megadását.
11. Kattintson a **Küldés gombra.** A beküldési eredmények és a feladathivatkozás az Eredmények ablakban érhetők el, amikor a beküldés befejeződik.
12. A **Frissítés** gombra kattintva megtekintheti a feladat legfrissebb állapotát, és frissítheti a képernyőt.

**A kimenet megtekintése**

1. A **Kiszolgálókezelőből**bontsa ki az **Azure**csomópontot, bontsa ki a **Data Lake Analytics**csomópontot, bontsa ki a Data Lake Analytics-fiók csomópontot, bontsa ki a **Tárfiókok csomópontot,** kattintson a jobb gombbal az Alapértelmezett tárház elemre, majd kattintson az **Intéző**parancsra.
2. Bontsa ki a Minták csomópontot, bontsa ki a Kimenetek csomópontot, majd kattintson duplán **az Illesztőprogramok.csv**elemre.

## <a name="see-also"></a>Lásd még
* [Az U-SQL kifejezések kiterjesztése felhasználói kóddal](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [A Data Lake Tools for Visual Studio használata U-SQL alkalmazások fejlesztéséhez](data-lake-analytics-data-lake-tools-get-started.md)
