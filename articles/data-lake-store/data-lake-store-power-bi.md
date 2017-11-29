---
title: "A Data Lake Store adatok elemzése a Power BI használatával |} Microsoft Docs"
description: "Azure Data Lake Store-ban tárolt adatok elemzésére a Power BI használatával"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 0c77ccd29e3e22005c3339ed4e89dccfed725fa0
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="analyze-data-in-data-lake-store-by-using-power-bi"></a>A Data Lake Store adatok elemzése a Power BI használatával
Ebben a cikkben megtudhatja a Power BI Desktop segítségével elemezheti és az Azure Data Lake Store-ban tárolt adatok megjelenítése.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Store-fiók**. Kövesse [Az Azure Data Lake Store használatának első lépései az Azure Portal használatával](data-lake-store-get-started-portal.md) című témakör utasításait. Ez a cikk feltételezi, hogy már létrejött egy Data Lake Store-fiókot, úgynevezett **mybidatalakestore**, és fel kell tölteni egy minta adatfájl (**Drivers.txt**) azt. Ezt a mintafájlt letölthető [Azure Data Lake Git-tárház](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **A Power BI Desktopban**. Letöltheti ezt a [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Hozzon létre egy jelentést a Power BI Desktop
1. Indítsa el a Power BI Desktop a számítógépen.
2. Az a **Home** menüszalag, kattintson a **adatok beolvasása**, és kattintson a több. Az a **adatok beolvasása** párbeszédpanel, kattintson a **Azure**, kattintson a **Azure Data Lake Store**, és kattintson a **Connect**.
   
    ![Data Lake Store-csatlakozás](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Data Lake Store-csatlakozás")
3. Ha megjelenik egy párbeszédpanel, az összekötő fázisban vannak fejlesztés alatt álló kapcsolatos, dönthet, hogy továbbra is.
4. Az a **Microsoft Azure Data Lake Store** párbeszédpanelen adja meg a Data Lake Store-fiók URL-CÍMÉT, és kattintson **OK**.
   
    ![A Data Lake Store URL-cím](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "a Data Lake Store URL-címe")
5. A következő párbeszédpanelen, kattintson a **bejelentkezés** való bejelentkezés Data Lake Store-fiókba. A szervezete bejelentkezési oldalára irányítja. Kövesse az utasításokat bejelentkezni a fiókjába.
   
    ![Jelentkezzen be a Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "jelentkezzen be a Data Lake Store")
6. Miután sikeresen bejelentkezett, kattintson **Connect**.
   
    ![Data Lake Store-csatlakozás](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Data Lake Store-csatlakozás")
7. A következő párbeszédpanel a fájlt a Data Lake Store-fiók feltöltött jeleníti meg. Ellenőrizze az adatokat, majd kattintson **terhelés**.
   
    ![Adatok betöltése az Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "adatok betöltése a Data Lake Store-ból")
8. Miután az adatok sikeresen betöltve a Power bi-ba, a következő mezők megjelenik-e a **mezők** fülre.
   
    ![Importált mezők](./media/data-lake-store-power-bi/imported-fields.png "importált mezők")
   
    Azonban megjelenítheti és elemezheti az adatokat, hogy azt inkább az adatok a következő mezők kiszolgálónként
   
    ![Szükségeskonfiguráció-mezők](./media/data-lake-store-power-bi/desired-fields.png "szükséges mezők")
   
    A következő lépésekben frissítjük a lekérdezés konvertálni az importált adatokat a kívánt formátumban.
9. Az a **Home** menüszalag, kattintson a **szerkesztése lekérdezések**.
   
    ![Szerkessze a lekérdezések](./media/data-lake-store-power-bi/edit-queries.png "lekérdezések szerkesztése")
10. A lekérdezés-szerkesztő alatt a **tartalom** oszlopban kattintson **bináris**.
    
    ![Szerkessze a lekérdezések](./media/data-lake-store-power-bi/convert-query1.png "lekérdezések szerkesztése")
11. A fájl ikonjára, látni fogja a **Drivers.txt** feltöltött fájl. Kattintson jobb gombbal a fájlra, és kattintson a **CSV**.    
    
    ![Szerkessze a lekérdezések](./media/data-lake-store-power-bi/convert-query2.png "lekérdezések szerkesztése")
12. Alább látható módon kimenetnek kell megjelennie. Az adatok segítségével képi megjelenítéseket készíthet formátumú most érhető el.
    
    ![Szerkessze a lekérdezések](./media/data-lake-store-power-bi/convert-query3.png "lekérdezések szerkesztése")
13. Az a **Home** menüszalag, kattintson a **zárja be, és alkalmazni**, és kattintson a **zárja be, és alkalmazni**.
    
    ![Szerkessze a lekérdezések](./media/data-lake-store-power-bi/load-edited-query.png "lekérdezések szerkesztése")
14. Ha a lekérdezés frissül, a **mezők** lap megjeleníti a képi megjelenítéshez tartozó elérhető új mezők.
    
    ![Mezők frissítése](./media/data-lake-store-power-bi/updated-query-fields.png "mezők frissítése")
15. Ossza meg velünk az illesztőprogramokat egy adott országban az egyes városban képviselő kördiagram létrehozása. Ehhez az szükséges, adja meg a következő beállításokat.
    
    1. A képi megjelenítések lapján kattintson a tortadiagram a szimbólum.
       
        ![A tortadiagram létrehozása](./media/data-lake-store-power-bi/create-pie-chart.png "kördiagram létrehozása")
    2. Az oszlop használni fogjuk **oszlop 4** (a város neve) és **oszlop 7** (ország neve). Ezekben az oszlopokban a húzza **mezők** beállítást **képi megjelenítések** lapon a lent látható módon.
       
        ![Képi megjelenítéseket készíthet](./media/data-lake-store-power-bi/create-visualizations.png "képi megjelenítéseket készíthet")
    3. A tortadiagram például az alábbihoz kell hasonlítania.
       
        ![A tortadiagram](./media/data-lake-store-power-bi/pie-chart.png "képi megjelenítéseket készíthet")
16. Egy adott országban szintű Lapszűrők való kiválasztással most már megtekintheti a kijelölt ország minden városban illesztőprogramok száma. Például az a **képi megjelenítések** lap **szintű szűrők lapon**, jelölje be **brazíliai**.
    
    ![Válassza ki a ország](./media/data-lake-store-power-bi/select-country.png "ország kiválasztása")
17. A tortadiagram automatikusan frissül az illesztőprogramok megjelenő Brazília a város tartozik.
    
    ![Illesztőprogramok országban](./media/data-lake-store-power-bi/driver-per-country.png "országonkénti illesztőprogramok")
18. Az a **fájl** menüben kattintson a **mentése** a képi megjelenítés egy Power BI Desktop-fájlba menti.

## <a name="publish-report-to-power-bi-service"></a>Jelentés közzététele a Power BI-ban
Miután létrehozta a képi megjelenítések a Power BI Desktopban, megoszthatja azt másokkal a Power BI szolgáltatásba történő közzétételével. Ehhez útmutatást lásd: [a Power BI Desktop közzététele](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Lásd még:
* [Adatok elemzése a Data Lake Analytics használatával a Data Lake Store az](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

