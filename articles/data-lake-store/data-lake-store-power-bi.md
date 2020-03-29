---
title: Adatok elemzése az Azure Data Lake Storage Gen1 szolgáltatásban a Power BI használatával | Microsoft dokumentumok
description: Az Azure Data Lake Storage Gen1-ben tárolt adatok elemzéséhez használja a Power BI-t
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65603214"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Adatok elemzése az Azure Data Lake Storage Gen1 szolgáltatásban a Power BI használatával
Ebből a cikkből megtudhatja, hogy miként elemezheti és jelenítheti meg az Azure Data Lake Storage Gen1 szolgáltatásban tárolt adatokat a Power BI Desktop használatával.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **A Data Lake Storage Gen1 fiók**. Kövesse az [Azure Data Lake Storage Gen1 használatának első lépéseit az Azure Portal használatával című útmutatóban.](data-lake-store-get-started-portal.md) Ez a cikk feltételezi, hogy már létrehozott egy Data Lake Storage Gen1 fiókot, az úgynevezett **myadlsg1,** és feltöltött egy minta adatfájlt **(Drivers.txt).** Ez a mintafájl letölthető az [Azure Data Lake Git repository-ból.](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)
* **Power BI Desktop**. Ezt a [Microsoft letöltőközpontból](https://www.microsoft.com/en-us/download/details.aspx?id=45331)töltheti le. 

## <a name="create-a-report-in-power-bi-desktop"></a>Jelentés létrehozása a Power BI Desktopban
1. Indítsa el a Power BI Desktopot a számítógépen.
2. A **Kezdőlap** menüszalagon kattintson az **Adatok bekerülése**gombra, majd az Egyebek parancsra. Az **Adatok bekerülése** párbeszédpanelen kattintson az **Azure**, majd **az Azure Data Lake Store**, majd a **Csatlakozás**gombra.
   
    ![Csatlakozás a Data Lake Storage Gen1 szolgáltatáshoz](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Csatlakozás a Data Lake Storage Gen1 szolgáltatáshoz")
3. Ha megjelenik egy párbeszédpanel arról, hogy az összekötő fejlesztési fázisban van, válassza a folytatást.
4. Az **Azure Data Lake Store** párbeszédpanelen adja meg a Data Lake Storage Gen1-fiókjának URL-címét, majd kattintson az **OK**gombra.
   
    ![A Data Lake Storage Gen1 URL-címe](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "A Data Lake Storage Gen1 URL-címe")
5. A következő párbeszédpanelen kattintson a **Bejelentkezés** gombra a Data Lake Storage Gen1 fiókba való bejelentkezéshez. A készülék átirányítja a szervezet bejelentkezési oldalára. Kövesse az utasításokat a fiókba való bejelentkezéshez.
   
    ![Bejelentkezés a Data Lake Storage Gen1 szolgáltatásba](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Bejelentkezés a Data Lake Storage Gen1 szolgáltatásba")
6. Miután sikeresen bejelentkezett, kattintson a **Csatlakozás gombra.**
   
    ![Csatlakozás a Data Lake Storage Gen1 szolgáltatáshoz](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Csatlakozás a Data Lake Storage Gen1 szolgáltatáshoz")
7. A következő párbeszédpanelen látható a Data Lake Storage Gen1 fiókba feltöltött fájl. Ellenőrizze az adatokat, majd kattintson a **Betöltés gombra.**
   
    ![Adatok betöltése a Data Lake Storage Gen1 szolgáltatásból](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Adatok betöltése a Data Lake Storage Gen1 szolgáltatásból")
8. Miután az adatok sikeresen betöltődtek a Power BI-ba, a Következő mezők jelennek meg a **Mezők** lapon.
   
    ![Importált mezők](./media/data-lake-store-power-bi/imported-fields.png "Importált mezők")
   
    Az adatok megjelenítéséhez és elemzéséhez azonban azt javasoljuk, hogy az adatok a következő mezőkön
   
    ![Kívánt mezők](./media/data-lake-store-power-bi/desired-fields.png "Kívánt mezők")
   
    A következő lépésekben frissítjük a lekérdezést, hogy az importált adatokat a kívánt formátumban konvertáljuk.
9. A **Kezdőlap** menüszalagon kattintson a **Lekérdezések szerkesztése gombra.**
   
    ![Lekérdezések szerkesztése](./media/data-lake-store-power-bi/edit-queries.png "Lekérdezések szerkesztése")
10. A Lekérdezésszerkesztő Tartalom **oszlopában** kattintson a **Bináris gombra.**
    
    ![Lekérdezések szerkesztése](./media/data-lake-store-power-bi/convert-query1.png "Lekérdezések szerkesztése")
11. Megjelenik egy fájlikon, amely a feltöltött **Drivers.txt** fájlt jelöli. Kattintson a jobb gombbal a fájlra, és válassza a **CSV**..    
    
    ![Lekérdezések szerkesztése](./media/data-lake-store-power-bi/convert-query2.png "Lekérdezések szerkesztése")
12. Meg kell jelennie egy kimenetnek az alábbiak szerint. Az adatok mostantól olyan formátumban érhetők el, amelyekkel vizualizációkat hozhat létre.
    
    ![Lekérdezések szerkesztése](./media/data-lake-store-power-bi/convert-query3.png "Lekérdezések szerkesztése")
13. A **Kezdőlap** menüszalagon kattintson a **Bezárás és az Alkalmaz**gombra, majd a **Bezárás és az Alkalmaz parancsra.**
    
    ![Lekérdezések szerkesztése](./media/data-lake-store-power-bi/load-edited-query.png "Lekérdezések szerkesztése")
14. A lekérdezés frissítése után a **Mezők** lapon megjelennek a vizualizációra rendelkezésre álló új mezők.
    
    ![Frissített mezők](./media/data-lake-store-power-bi/updated-query-fields.png "Frissített mezők")
15. Hozzunk létre egy kördiagramot, amely az egyes országok/régiók illesztőprogramjait ábrázolja. Ehhez a következő beállításokat kell megválasztania.
    
    1. A Vizualizációk lapon kattintson a kördiagram szimbólumára.
       
        ![Kördiagram létrehozása](./media/data-lake-store-power-bi/create-pie-chart.png "Kördiagram létrehozása")
    2. Az oszlopok, amelyeket használni fogunk, **a 4 oszlop** (a város neve) és a 7 **oszlop** (az ország/régió neve). Húzza ezeket az oszlopokat a **Mezők** lapról a **Képimegjelenítések** lapra az alábbi ábrán látható módon.
       
        ![Vizualizációk létrehozása](./media/data-lake-store-power-bi/create-visualizations.png "Vizualizációk létrehozása")
    3. A kördiagram most az alábbihoz hasonló.
       
        ![Kördiagram](./media/data-lake-store-power-bi/pie-chart.png "Vizualizációk létrehozása")
16. Ha kiválaszt egy adott országot/régiót az oldalszintű szűrőkközül, most már láthatja az illesztőprogramok számát a kiválasztott ország/régió egyes városaiban. A **Vizualizációk** lap **Oldalszintű szűrők**csoportjában például válassza a **Brazília**lehetőséget.
    
    ![Ország kiválasztása](./media/data-lake-store-power-bi/select-country.png "Ország/régió kiválasztása")
17. A kördiagram automatikusan frissül, hogy brazíliai városokban jelenjenek meg az illesztőprogramok.
    
    ![Sofőrök egy országban](./media/data-lake-store-power-bi/driver-per-country.png "Sofőrök országonként/régiónként")
18. A **Fájl** menü **Mentés parancsára** kattintva mentse a vizualizációt Power BI Asztali fájlként.

## <a name="publish-report-to-power-bi-service"></a>Jelentés közzététele a Power BI szolgáltatásban
Miután létrehozta a vizualizációkat a Power BI Desktopban, megoszthatja azt másokkal, ha közzéteszi azt a Power BI szolgáltatásban. Ennek módjáról a Közzététel [a Power BI Desktopról](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/)témakörben talál.

## <a name="see-also"></a>Lásd még
* [Adatok elemzése a Data Lake Storage Gen1 szolgáltatásban a Data Lake Analytics használatával](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

