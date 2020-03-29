---
title: Adatok megjelenítése a Microsoft Excelbe importált Azure Data Explorer Kusto lekérdezéssel
description: Ebből a cikkből megtudhatja, hogyan importálhat egy Azure Data Explorer Kusto-lekérdezést a Microsoft Excelbe.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4999000e2084922b43b8085034f545d4b5c644a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849088"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Adatok megjelenítése a Microsoft Excelbe importált Azure Data Explorer Kusto lekérdezéssel

Az Azure Data Explorer két lehetőséget biztosít az excelbeli adatokhoz való csatlakozáshoz: használja a natív összekötőt, vagy importáljon egy lekérdezést az Azure Data Explorerből. Ez a cikk bemutatja, hogyan importálhat egy lekérdezést az Azure Data Explorer ből az Excelbe az adatok megjelenítéséhez. Kusto-lekérdezés hozzáadása Excel-adatforrásként további számítások vagy vizualizációk elvégzéséhez az adatokon.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Egy szervezeti e-mail fiók, amely az Azure Active Directory tagja, így csatlakozhat az [Azure Data Explorer súgófürtjéhez](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>vagy</br>
* Hozzon létre [egy tesztfürtöt és adatbázist,](create-cluster-database-portal.md) és jelentkezzen be [az Azure Data Explorer web felhasználói felületi alkalmazásába.](https://dataexplorer.azure.com/)

## <a name="define-kusto-query-as-an-excel-data-source"></a>Kusto-lekérdezés definiálása Excel-adatforrásként

1. Az [Azure Data Explorer webes felhasználói felületén](https://dataexplorer.azure.com/clusters/help/databases/Samples)futtassa a lekérdezést, és ellenőrizze az eredményeket.

1. Jelölje ki a **Megosztás** lapot, és válassza **a Lekérdezés a Power BI-ba**lehetőséget.

    ![Webes felhasználói felület lekérdezése a Power BI-ba](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Egy ablak jelenik meg a következő értesítéssel:

    ![lekérdezés exportálása vágólapra](media/excel-blank-query/query-exported-to-clipboard.png)

1. Nyissa meg **a Microsoft Excelt**.

1. Az **Adatok** lapon válassza az Adatok >  **beszedése****más forrásokból** > **üres lekérdezés lehetőséget.**

    ![Adatok beszereznie és üres lekérdezés kiválasztása](media/excel-blank-query/get-data-blank-query.png)

1. Megnyílik **a Power Query Editor** ablaka. Az ablakban válassza a **Speciális szerkesztő**lehetőséget.

    ![A Power query szerkesztő ablaka](media/excel-blank-query/power-query-editor.png)

1. A **Speciális szerkesztő** ablakban illessze be a vágólapra exportált lekérdezést, és válassza a **Kész gombot.**

    ![Speciális szerkesztőlekérdezés](media/excel-blank-query/advanced-editor-query.png)    

1. A hitelesítéshez válassza **a Hitelesítő adatok szerkesztése**lehetőséget.

    ![Hitelesítő adatok szerkesztése](media/excel-blank-query/edit-credentials.png)

1. Válassza a **Szervezeti fiókot és** **a Bejelentkezés**lehetőséget. Fejezze be a bejelentkezési folyamatot, és válassza a **Csatlakozás lehetőséget.**

    ![Teljes bejelentkezés](media/excel-blank-query/complete-sign-in.png)

    További lekérdezések hozzáadásához ismételje meg az előző lépéseket. A lekérdezéseket átnevezheti értelmesebb nevekre.

1. Az adatok Excelbe való betöltése gombbal kattintson **a Bezárás & gombra.**

    ![Zár és betöltés kiválasztása](media/excel-blank-query/close-and-load.png)

1. Most az adatok az Excel-ben vannak. A lekérdezés frissítéséhez kattintson a **Frissítés** gombra.

    ![Adatok megtekintése az Excelben](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>További lépések

[Adatok megjelenítése az Azure Data Explorer excelhez készült összekötője segítségével](excel-connector.md)