---
title: Adatok elemzése az Azure Data Lake Storage Gen1 Power BI használatával |} A Microsoft Docs
description: Az Azure Data Lake Storage Gen1 tárolt adatok elemzése a Power BI használatával
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: b873d90dbc33aff27cd2fc3b5099b08eacf929b6
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58880066"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Adatok elemzése az Azure Data Lake Storage Gen1 Power BI használatával
Ebben a cikkben megtudhatja hogyan az Azure Data Lake Storage Gen1 tárolt adatok elemzése és megjelenítése a Power BI Desktop használatával.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **A Data Lake Storage Gen1 fiók**. Kövesse az utasításokat, [Azure Data Lake Storage Gen1 használatának első lépései az Azure portal használatával](data-lake-store-get-started-portal.md). Ez a cikk feltételezi, hogy már létrehozott egy Data Lake Storage Gen1 nevű fiókot **myadlsg1**, és a egy minta adatfájl feltöltése (**Drivers.txt**) azt. Ezt a mintafájlt a letölthető [Azure Data Lake Git-tárház](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Letöltheti ezt a [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Jelentés létrehozása a Power BI Desktopban
1. Indítsa el a Power BI Desktopban a számítógépen.
2. Az a **kezdőlap** menüszalagra, majd **adatok lekérése**, és kattintson a további. Az a **adatok lekérése** párbeszédpanelen kattintson a **Azure**, kattintson a **Azure Data Lake Store**, és kattintson a **Connect**.
   
    ![Csatlakozhat a Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account.png "csatlakozhat a Data Lake Storage Gen1")
3. Ha megjelenik egy párbeszédpanel, az összekötő folyamatban van egy fejlesztési fázisban kapcsolatos, részvétel továbbra is.
4. Az a **Azure Data Lake Store** párbeszédpanelen adja meg a Data Lake Storage Gen1 fiók URL-CÍMÉT, és kattintson **OK**.
   
    ![A Data Lake Storage Gen1 URL-cím](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "Data Lake Storage Gen1 URL-címe")
5. A következő párbeszédpanelen, kattintson a **jelentkezzen be a** bejelentkezni a Data Lake Storage Gen1 fiókjába. Átirányítjuk szervezete bejelentkezési oldal. Kövesse az utasításokat követve jelentkezzen be a fiókjába.
   
    ![Jelentkezzen be a Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "jelentkezzen be a Data Lake Storage Gen1")
6. Miután sikeresen bejelentkezett, kattintson a **Connect**.
   
    ![Csatlakozhat a Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "csatlakozhat a Data Lake Storage Gen1")
7. A következő párbeszédpanel megjeleníti a Data Lake Storage Gen1 fiókjába feltöltött fájlt. Ellenőrizze az adatokat, majd kattintson **terhelés**.
   
    ![Adatok betöltése a Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "adatok betöltése a Data Lake Storage Gen1")
8. Miután az adatok sikeresen betöltve a Power BI-ba, megjelenik a következő mezőket a **mezők** fülre.
   
    ![Mezők importált](./media/data-lake-store-power-bi/imported-fields.png "importált mezők")
   
    Azonban jelenítheti meg és elemezheti az adatokat, hogy inkább az adatok érhetők el a következő mezők száma
   
    ![Szükséges mezők](./media/data-lake-store-power-bi/desired-fields.png "szükséges mezők")
   
    A következő lépésben frissítjük a lekérdezés az importált adatok a kívánt formátumban.
9. Az a **kezdőlap** menüszalagra, majd **lekérdezések szerkesztése**.
   
    ![Lekérdezések szerkesztése](./media/data-lake-store-power-bi/edit-queries.png "lekérdezések szerkesztése")
10. A Lekérdezésszerkesztőben, alatt a **tartalom** oszlopot, kattintson a **bináris**.
    
    ![Lekérdezések szerkesztése](./media/data-lake-store-power-bi/convert-query1.png "lekérdezések szerkesztése")
11. Láthatja, hogy egy fájl ikon jelenik meg, amely jelöli az **Drivers.txt** feltöltött fájlt. Kattintson jobb gombbal a fájlra, majd kattintson **CSV**.    
    
    ![Lekérdezések szerkesztése](./media/data-lake-store-power-bi/convert-query2.png "lekérdezések szerkesztése")
12. Ahogy az alábbi kimenetnek kell megjelennie. Az adatok már elérhető használatával vizualizációkat hozhat létre olyan formátumban.
    
    ![Lekérdezések szerkesztése](./media/data-lake-store-power-bi/convert-query3.png "lekérdezések szerkesztése")
13. Az a **kezdőlap** menüszalagra, majd **bezárásához és a alkalmazni**, és kattintson a **bezárásához és a alkalmazni**.
    
    ![Lekérdezések szerkesztése](./media/data-lake-store-power-bi/load-edited-query.png "lekérdezések szerkesztése")
14. A lekérdezés frissítése után a **mezők** lapon jelennek meg az új mezők vizuális megjelenítésre.
    
    ![Mezők frissítése](./media/data-lake-store-power-bi/updated-query-fields.png "mezők frissítése")
15. Hozzunk létre, amelyek az illesztőprogramokat egy adott ország esetében minden egyes városban tortadiagram. Ehhez adja meg a következő beállításokat.
    
    1. A Vizualizációk lapon kattintson a kördiagramon.
       
        ![Tortadiagram létrehozása](./media/data-lake-store-power-bi/create-pie-chart.png "diagram létrehozása")
    2. Az oszlop, amely használni fogjuk **oszlop 4** (az városa neve) és **oszlop 7** (ország neve). Húzza az ezeket az oszlopokat **mezők** lapról **Vizualizációk** lapon az alább látható módon.
       
        ![Vizualizációk létrehozása](./media/data-lake-store-power-bi/create-visualizations.png "Vizualizációk létrehozása")
    3. A tortadiagram kell hasonlítania, mint például az alábbi képen látható.
       
        ![Tortadiagram](./media/data-lake-store-power-bi/pie-chart.png "Vizualizációk létrehozása")
16. Válassza ki az oldalszintű szűrőkkel egy adott országra, most már megtekintheti az egyes városokat a kiválasztott ország illesztőprogramok számát. Például alatt a **Vizualizációk** lap **lapszintű szűrők**válassza **Brazília**.
    
    ![Válasszon egy országot](./media/data-lake-store-power-bi/select-country.png "válasszon egy országot")
17. A tortadiagram automatikusan frissül az illesztőprogramokat a várost, Brazília megjelenítéséhez.
    
    ![Illesztőprogramok országban](./media/data-lake-store-power-bi/driver-per-country.png "illesztőprogramok országonként")
18. Az a **fájl** menüben kattintson a **mentése** Power BI Desktop-fájlként a vizualizációt menteni szeretné.

## <a name="publish-report-to-power-bi-service"></a>Jelentés közzététele a Power BI szolgáltatásban
Miután létrehozta a Vizualizációk a Power BI Desktopban, megoszthatja azt más közzétesszük azt a Power BI szolgáltatásban. Hogyan valósítható meg, lásd: [közzététel a Power BI Desktopból](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Lásd még
* [Data Lake Analytics használatával a Data Lake Storage Gen1 adatok elemzése](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

