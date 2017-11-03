---
title: "Azure Data Lake Analytics-feladatok U-SQL-szerelvények kidolgozása |} Microsoft Docs"
description: "Megtudhatja, hogyan kell használni, és fel újra a Data Lake Analytics-feladatok szerelvények fejlesztéséhez. "
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/30/2016
ms.author: jejiang
ms.openlocfilehash: c49f80f8dcd330d7f46726241e7178351b9cc28f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="develop-u-sql-assemblies-for-azure-data-lake-analytics-jobs"></a>Azure Data Lake Analytics-feladatok U-SQL-szerelvények fejlesztése
Megtudhatja, hogyan kell használni, és fel újra a Data Lake Analytics-feladatok szerelvényeket a háttérkód kapcsolja. 

U-SQL egyszerűen a saját egyéni kód hozzáadása a .net-nyelveket, például a C#, VB.Net vagy F #. A saját futásidejű más nyelv támogatása is telepíthet.

A legegyszerűbben úgy egyéni kód használata a Data Lake Tools használatához a Visual Studio háttérkód képességeinek. További információkért lásd: [oktatóanyag: Data Lake Tools for Visual Studio használatával U-SQL-parancsfájlok fejlesztése](data-lake-analytics-data-lake-tools-get-started.md). Van néhány hátrányai háttérkód használatával:

- A forráskód minden parancsfájl benyújtására lekérdezi feltöltve.
- háttérkód nem osztható meg más feladatok.

Ezek hátrányai megoldására háttérkód ikonná szerelvényeket, és regisztrálja a szerelvényeket a Data Lake Analytics-katalógus.

## <a name="prerequisites"></a>Előfeltételek
* A Visual Studio 2017, a Visual Studio 2015, Visual Studio 2013 4. frissítéssel vagy Visual Studio 2012 és telepített Visual C++
* A Microsoft Azure SDK for .NET 2.5-ös verzió vagy újabb.  Telepítse a webplatform-telepítővel vagy a Visual Studio-telepítő
* Data Lake Analytics-fiók.  Lásd: [Az Azure Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md).
* Lépkedjen végig a [Ismerkedés az Azure Data Lake Analytics U-SQL Studio](data-lake-analytics-u-sql-get-started.md) oktatóanyag.
* Csatlakozás az Azure-bA.
* Az adatok feltöltése című [Ismerkedés az Azure Data Lake Analytics U-SQL Studio](data-lake-analytics-u-sql-get-started.md). 

## <a name="develop-assemblies-for-u-sql"></a>A U-SQL szerelvények fejlesztése

**Létrehozásához és elküldéséhez a U-SQL-feladatot:**

1. Kattintson a **File** (Fájl) menüben a **New** (Új), majd a **Project** (Projekt) elemre.
2. Bontsa ki a **telepített**, **sablonok**, **Azure Data Lake**, **U-SQL(ADLA)**, jelölje be a **Class Library (U-SQL-alkalmazás)** sablont, és kattintson **OK**.
3. Írja be a kódot Class1.cs.  A kód a minta a következő:

        using Microsoft.Analytics.Interfaces;

        namespace USQLApplication_codebehind
        {
            [SqlUserDefinedProcessor]
            public class MyProcessor : IProcessor
            {
                public override IRow Process(IRow input, IUpdatableRow output)
                {
                    output.Set(0, input.Get<string>(0));
                    output.Set(0, input.Get<string>(0));
                    return output.AsReadOnly();
                }
            }
        }
4. Kattintson a **Build** menüben, majd kattintson **megoldás fordítása** a dll létrehozása.

## <a name="register-assemblies"></a>Szerelvények regisztrálása

Lásd: [használata Data Lake Analytics(U-SQL) katalógus](data-lake-analytics-use-u-sql-catalog.md).


## <a name="use-the-assemblies"></a>A szerelvények használata

Lásd: [használja az Azure Data Lake Tools Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

## <a name="see-also"></a>Lásd még:
* [A Data Lake Analytics PowerShell használatának első lépései](data-lake-analytics-get-started-powershell.md)
* [Ismerkedés a Data Lake Analytics az Azure portál használatával](data-lake-analytics-get-started-portal.md)
* [Használja a Data Lake Tools for Visual Studio a U-SQL-alkalmazások fejlesztésével](data-lake-analytics-data-lake-tools-get-started.md)
* [Használja a Data Lake Analytics(U-SQL) katalógus](data-lake-analytics-use-u-sql-catalog.md)
* [Az Azure Data Lake Tools for Visual Studio kód használata](data-lake-analytics-data-lake-tools-for-vscode.md)