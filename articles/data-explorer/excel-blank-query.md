---
title: A Microsoft Excelben importált Azure Adatkezelő Kusto-lekérdezés használatával megjelenítheti az adatmegjelenítést
description: Ebből a cikkből megtudhatja, hogyan importálhat Azure Adatkezelő Kusto-lekérdezést a Microsoft Excelben.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4999000e2084922b43b8085034f545d4b5c644a9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849088"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>A Microsoft Excelben importált Azure Adatkezelő Kusto-lekérdezés használatával megjelenítheti az adatmegjelenítést

Az Azure Adatkezelő két lehetőséget kínál az adatokhoz való csatlakozásra az Excelben: használja a natív összekötőt, vagy importáljon egy lekérdezést az Azure Adatkezelőból. Ebből a cikkből megtudhatja, hogyan importálhat egy lekérdezést az Azure Adatkezelőról az Excelbe az adatok megjelenítéséhez. Kusto-lekérdezés hozzáadása Excel-adatforrásként további számítások vagy vizualizációk megadásához az adathalmazon.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Egy olyan szervezeti e-mail fiók, amely az Azure Active Directory tagja, így kapcsolódhat az [azure adatkezelő Súgó fürthöz](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>vagy</br>
* Hozzon létre [egy tesztelési fürtöt és adatbázist](create-cluster-database-portal.md) , és jelentkezzen be [Az Azure adatkezelő webes felhasználói felületi alkalmazásba](https://dataexplorer.azure.com/).

## <a name="define-kusto-query-as-an-excel-data-source"></a>Kusto-lekérdezés definiálása Excel-adatforrásként

1. Az [Azure adatkezelő webes felhasználói felületén](https://dataexplorer.azure.com/clusters/help/databases/Samples)futtassa a lekérdezést, és tekintse át az eredményeket.

1. Válassza a **megosztás** fület, és válassza a **lekérdezés lehetőséget Power bi**.

    ![Webes felhasználói felület lekérdezése Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Megjelenik egy ablak a következő értesítéssel:

    ![lekérdezés exportálása a vágólapra](media/excel-blank-query/query-exported-to-clipboard.png)

1. Nyissa meg a **Microsoft Excelt**.

1. Az **adatok** lapon válassza az **adatok beolvasása** **más forrásokból** >  > **üres lekérdezés**lehetőséget.

    ![Adatkérés és üres lekérdezés kiválasztása](media/excel-blank-query/get-data-blank-query.png)

1. Megnyílik a **Power Query-szerkesztő** ablak. Az ablakban válassza a **speciális szerkesztő**lehetőséget.

    ![Power Query-szerkesztő ablak](media/excel-blank-query/power-query-editor.png)

1. Illessze be a vágólapra az exportált lekérdezést a **speciális szerkesztő** ablakban, majd válassza a **kész**lehetőséget.

    ![Speciális szerkesztői lekérdezés](media/excel-blank-query/advanced-editor-query.png)    

1. A hitelesítéshez válassza a **hitelesítő adatok szerkesztése**lehetőséget.

    ![Hitelesítő adatok szerkesztése](media/excel-blank-query/edit-credentials.png)

1. Válassza a **szervezeti fiók** lehetőséget, és **Jelentkezzen**be. Fejezze be a bejelentkezési folyamatot, majd válassza a **kapcsolat**lehetőséget.

    ![Bejelentkezés befejezése](media/excel-blank-query/complete-sign-in.png)

    További lekérdezések hozzáadásához ismételje meg az előző lépéseket. A lekérdezéseket több értelmes névre is átnevezheti.

1. A **bezárás & betöltés** gombra kattintva beolvashatja az adatait az Excel programba.

    ![Bezárás és betöltés kiválasztása](media/excel-blank-query/close-and-load.png)

1. Most az adatai az Excelben vannak. A lekérdezés frissítéséhez kattintson a **frissítés** gombra.

    ![Az Excelben tárolt adatmegjelenítés](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>Következő lépések

[Az Excelhez készült Azure Adatkezelő Connector használatával megjelenítheti az adatmegjelenítést](excel-connector.md)