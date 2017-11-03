---
title: "U-SQL felhasználói operátorok (udo-k) kidolgozása |} Microsoft Docs"
description: "Megtudhatja, hogyan kell használni, és fel újra a Data Lake Analytics-feladatok felhasználói operátorok fejlesztése. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.openlocfilehash: fdee02fb60b633c26704fc1774dfc3a7825b5e0d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>U-SQL felhasználói operátorok (udo-k) fejlesztése
Ismerje meg, hogyan fejleszthet operátorok felhasználói fel adatokat a U-SQL-feladatot.

Általános célú szerelvényeket a U-SQL fejlesztésével, további információkért lásd: [fejlesztése U-SQL-szerelvények Azure Data Lake Analytics-feladatok](data-lake-analytics-u-sql-develop-assemblies.md)

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

## <a name="see-also"></a>Lásd még:
* [A Data Lake Analytics PowerShell használatának első lépései](data-lake-analytics-get-started-powershell.md)
* [Ismerkedés a Data Lake Analytics az Azure portál használatával](data-lake-analytics-get-started-portal.md)
* [Használja a Data Lake Tools for Visual Studio a U-SQL-alkalmazások fejlesztésével](data-lake-analytics-data-lake-tools-get-started.md)
