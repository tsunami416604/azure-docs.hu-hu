---
title: U-SQL felhasználói operátorok (udo-k) az Azure Data Lake Analytics fejlesztése
description: Ismerje meg, hogyan fejleszthet operátorok felhasználói használt és az Azure Data Lake Analytics-feladatok fel újra.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8d8937a6132f770e989d7595883b2c5cf804c44f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623855"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>U-SQL felhasználói operátorok (udo-k) fejlesztése
A cikkből megtudhatja, hogyan fejleszthet operátorok felhasználói fel adatokat a U-SQL-feladatot.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>A felhasználó által definiált operátor használata U-SQL és megadása
**Létrehozásához és elküldéséhez a U-SQL-feladatot:**

1. A Visual Studio válassza **fájl > Új > Projekt > U-SQL projekt**.
2. Kattintson az **OK** gombra. A Visual Studio létrehoz egy megoldást Script.usql fájllal.
3. A **Megoldáskezelőben**, bontsa ki a Script.usql, és kattintson duplán **Script.usql.cs**.
4. A fájlba illessze be a következő kódot:

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
6. Nyissa meg **Script.usql**, és illessze be az alábbi U-SQL parancsfájlt:

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
10. Ha még nem kapcsolódik az Azure-előfizetéssel, kérni fogja az Azure-fiók hitelesítő adatait.
11. Kattintson a **nyújt**. Után az eredmények és a feladatra mutató hivatkozás esetén érhetők el az eredmények ablakában az elküldés.
12. Kattintson a **frissítése** gombra kattintva megtekintheti a legutóbbi feladat állapota, és frissítse a képernyőt.

**A kimenet megtekintéséhez**

1. A **Server Explorer**, bontsa ki a **Azure**, bontsa ki a **Data Lake Analytics**, bontsa ki a Data Lake Analytics-fiókjait, bontsa ki a **Tárfiókok**, kattintson a jobb gombbal az alapértelmezett tároló, és kattintson a **Explorer**.
2. Bontsa ki a mintákat, bontsa ki a kimenetek, és kattintson duplán **Drivers.csv**.

## <a name="see-also"></a>Lásd még
* [U-SQL kifejezések felhasználói kóddal kiterjesztése](https://msdn.microsoft.com/library/azure/mt621316.aspx)
* [Használja a Data Lake Tools for Visual Studio a U-SQL-alkalmazások fejlesztésével](data-lake-analytics-data-lake-tools-get-started.md)
