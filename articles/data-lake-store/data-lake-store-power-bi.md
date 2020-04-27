---
title: Azure Data Lake Storage Gen1 adatai elemzése a Power BI használatával | Microsoft Docs
description: Power BI használata a Azure Data Lake Storage Gen1 tárolt adatelemzéshez
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
ms.openlocfilehash: d8717b8f365e692b5f27bf8a04d65c5147b8f31b
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "65603214"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Azure Data Lake Storage Gen1i adatelemzés Power BI használatával
Ebből a cikkből megtudhatja, hogyan használhatja a Power BI Desktop a Azure Data Lake Storage Gen1 tárolt adatelemzéshez és megjelenítéséhez.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Data Lake Storage Gen1-fiók**. Kövesse a [Azure Data Lake Storage Gen1 használatának első lépései a Azure Portal használatával](data-lake-store-get-started-portal.md)című témakör utasításait. Ez a cikk azt feltételezi, hogy már létrehozott egy **myadlsg1**nevű Data Lake Storage Gen1 fiókot, és feltöltött egy minta adatfájlt (**Drivers. txt**). Ez a mintakód [Azure Data Lake git-tárházból](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)tölthető le.
* **Power bi Desktop**. Ezt letöltheti a [Microsoft letöltőközpontból](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Jelentés létrehozása a Power BI Desktopban
1. Power BI Desktop elindítása a számítógépen.
2. A **Kezdőlap** menüszalagon kattintson az **adatok lekérése**elemre, majd kattintson a továbbiak elemre. Az **adatlekérdezés** párbeszédpanelen kattintson az **Azure**elemre, majd a **Azure Data Lake Store**elemre, végül a **kapcsolat**elemre.
   
    ![Kapcsolódás Data Lake Storage Gen1hoz](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Kapcsolódás Data Lake Storage Gen1hoz")
3. Ha megjelenik egy párbeszédpanel arról, hogy az összekötő egy fejlesztési fázisban van, a folytatáshoz válassza a következőt:.
4. A **Azure Data Lake Store** párbeszédpanelen adja meg a Data Lake Storage Gen1-fiók URL-címét, majd kattintson az **OK**gombra.
   
    ![Data Lake Storage Gen1 URL-címe](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "Data Lake Storage Gen1 URL-címe")
5. A következő párbeszédpanelen kattintson a **Bejelentkezés** gombra a Data Lake Storage Gen1 fiókba való bejelentkezéshez. A rendszer átirányítja a szervezet bejelentkezési oldalára. Az utasításokat követve jelentkezzen be a fiókba.
   
    ![Bejelentkezés Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Bejelentkezés Data Lake Storage Gen1")
6. Miután sikeresen bejelentkezett, kattintson a **kapcsolat**gombra.
   
    ![Kapcsolódás Data Lake Storage Gen1hoz](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Kapcsolódás Data Lake Storage Gen1hoz")
7. A következő párbeszédpanel megjeleníti a Data Lake Storage Gen1-fiókjába feltöltött fájlt. Ellenőrizze az adatokat, majd kattintson a **Betöltés**elemre.
   
    ![Adatok betöltése a Data Lake Storage Gen1ból](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Adatok betöltése a Data Lake Storage Gen1ból")
8. Miután az adat sikeresen betöltődött Power BIba, a **mezők** lapon a következő mezők jelennek meg.
   
    ![Importált mezők](./media/data-lake-store-power-bi/imported-fields.png "Importált mezők")
   
    Az adat megjelenítéséhez és elemzéséhez azonban inkább az alábbi mezőkben elérhetővé tettük az adatmennyiséget.
   
    ![Kívánt mezők](./media/data-lake-store-power-bi/desired-fields.png "Kívánt mezők")
   
    A következő lépésekben frissíteni fogjuk a lekérdezést, hogy az importált adattípusokat a kívánt formátumban alakítsa át.
9. A **Kezdőlap** menüszalagon kattintson a **lekérdezések szerkesztése**elemre.
   
    ![Lekérdezések szerkesztése](./media/data-lake-store-power-bi/edit-queries.png "Lekérdezések szerkesztése")
10. A lekérdezés-szerkesztő **tartalom** oszlopában kattintson a **bináris**elemre.
    
    ![Lekérdezések szerkesztése](./media/data-lake-store-power-bi/convert-query1.png "Lekérdezések szerkesztése")
11. Egy fájl ikon jelenik meg, amely a feltöltött **Drivers. txt** fájlt jelöli. Kattintson a jobb gombbal a fájlra, majd kattintson a **CSV**elemre.    
    
    ![Lekérdezések szerkesztése](./media/data-lake-store-power-bi/convert-query2.png "Lekérdezések szerkesztése")
12. Az alább látható kimenetnek kell megjelennie. Az adatai mostantól a vizualizációk létrehozásához használható formátumban érhetők el.
    
    ![Lekérdezések szerkesztése](./media/data-lake-store-power-bi/convert-query3.png "Lekérdezések szerkesztése")
13. A **Kezdőlap** menüszalagon kattintson a **Bezárás**gombra, majd kattintson a **Bezárás**gombra, és válassza az alkalmaz elemet.
    
    ![Lekérdezések szerkesztése](./media/data-lake-store-power-bi/load-edited-query.png "Lekérdezések szerkesztése")
14. A lekérdezés frissítése után a **mezők** lapon megjelennek a vizualizációhoz elérhető új mezők.
    
    ![Frissített mezők](./media/data-lake-store-power-bi/updated-query-fields.png "Frissített mezők")
15. Hozzunk létre egy tortadiagramot, amely az egyes városokban lévő illesztőprogramokat jelöli az adott ország/régió számára. Ehhez végezze el a következő beállításokat.
    
    1. A vizualizációk lapon kattintson a tortadiagram szimbólumára.
       
        ![Tortadiagram létrehozása](./media/data-lake-store-power-bi/create-pie-chart.png "Tortadiagram létrehozása")
    2. A használni kívánt oszlopok a **4. oszlop** (a város neve) és a **7-es oszlop** (az ország/régió neve). Húzza ezeket az oszlopokat a **mezők** lapról a **vizualizációk** lapra az alább látható módon.
       
        ![Vizualizációk létrehozása](./media/data-lake-store-power-bi/create-visualizations.png "Vizualizációk létrehozása")
    3. A tortadiagramnak most az alább láthatóhoz hasonlónak kell lennie.
       
        ![Tortadiagram](./media/data-lake-store-power-bi/pie-chart.png "Vizualizációk létrehozása")
16. Ha kijelöl egy adott országot vagy régiót az oldal szintjének szűrők közül, akkor a kiválasztott ország/régió minden városában megjelenik az illesztőprogramok száma. Például a **vizualizációk** lap **oldal szintű szűrők**területén válassza a **Brazília**elemet.
    
    ![Ország kiválasztása](./media/data-lake-store-power-bi/select-country.png "Ország/régió kiválasztása")
17. A tortadiagramot a rendszer automatikusan frissíti a brazil városokban lévő illesztőprogramok megjelenítéséhez.
    
    ![Az országokban található illesztőprogramok](./media/data-lake-store-power-bi/driver-per-country.png "Illesztőprogramok országonként/régiónként")
18. A **fájl** menüben kattintson a **Mentés** gombra a vizualizáció Power bi Desktop fájlként való mentéséhez.

## <a name="publish-report-to-power-bi-service"></a>Jelentés közzététele Power BI szolgáltatás
Miután létrehozta a vizualizációkat a Power BI Desktopban, megoszthatja másokkal, ha közzéteszi azt a Power BI szolgáltatás. Ennek módjával kapcsolatos útmutatásért lásd: [közzététel Power bi Desktopról](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Lásd még
* [Data Lake Storage Gen1 adatai elemzése a Data Lake Analytics használatával](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

