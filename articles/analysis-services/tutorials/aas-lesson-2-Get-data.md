---
title: "Az Azure Analysis Services oktatóanyaga – 2. lecke: Az adatok beszerzése | Microsoft Docs"
description: "A lecke az adatok beszerzését és importálását ismerteti az Azure Analysis Services oktatóprojektjében."
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 0bf5eb51d3fea8ff4a62d9e7f6d76c771aaaaf77
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="lesson-2-get-data"></a>2. lecke: Az adatok beszerzése

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Ebben a leckében az SSDT Adatok lekérése utasításával csatlakozik az AdventureWorksDW2014 mintaadatbázishoz, kiválasztja az adatokat, megtekinti az előnézetüket, és szűri őket, majd importálja az adatokat a modell munkaterületére.  
  
Az Adatok lekérése használatával olyan források széles választékából importálhatók adatok mint: Azure SQL Database, Oracle, Sybase, OData Feed, Teradata, fájlok és egyéb lehetőségek. Az adatok emellett lekérdezhetők Power Query M-képletekkel is.

> [!NOTE]
> Az oktatóanyag feladatai és rendszerképei egy helyszíni kiszolgálón található AdventureWorksDW2014 adatbázishoz való csatlakozást mutatnak be. Egyes esetekben az Azure AdventureWorksDW2014 adatbázisa ettől eltérő lehet.
  
A lecke elvégzésének várható időtartama: **10 perc**.  
  
## <a name="prerequisites"></a>Előfeltételek  
Ez a témakör a táblázatos modellezéssel foglalkozó oktatóanyag részét képezi, amelyet a megfelelő sorrendben kell elvégezni. A leckében foglalt feladatok végrehajtása előtt el kell végeznie az előző leckét ([1. lecke: Új táblázatos modellprojekt létrehozása](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)).  
  
## <a name="create-a-connection"></a>Kapcsolat létrehozása  
  
#### <a name="to-create-a-connection-to-the-adventureworksdw2014-database"></a>Kapcsolat létrehozása az AdventureWorksDW2014 adatbázishoz  
  
1.  A Táblázatos modelltallózóban kattintson a jobb gombbal az **Adatforrások** > **Importálás adatforrásból** elemére.  
  
    Ez elindítja az Adatok lekérése funkciót, amely végigvezeti az adatforráshoz való kapcsolódás folyamatán. Ha a Táblázatos modelltallózó nem látható, a **Megoldáskezelőben** kattintson duplán a **Model.bim** elemre a modell a tervezőben történő megnyitásához. 
    
    ![aas-lesson2-getdata](../tutorials/media/aas-lesson2-getdata.png)
  
2.  Az Adatok lekérése felületen kattintson az **Adatbázis** > **SQL Server-adatbázis** > **Csatlakozás** elemre.  
  
3.  Az **SQL Server-adatbázis** párbeszédpanelen a **Kiszolgáló** mezőben adja meg azon kiszolgáló nevét, ahová az AdventureWorksDW2014 adatbázist telepítette, majd kattintson a **Csatlakozás** parancsra.  

4.  Amikor a rendszer a hitelesítő adatait kéri, az adatok importálásakor és feldolgozásakor az Analysis Services-kiszolgáló által az adatforráshoz való kapcsolódáshoz használt hitelesítő adatokat kell megadnia. A **Megszemélyesítési mód** alatt válassza a **Fiók megszemélyesítése** lehetőséget, majd adja meg a hitelesítő adatokat, és kattintson a **Csatlakozás** parancsra. Ajánlott olyan fiókot használnia, amelynek a jelszava nem jár le.

    ![aas-lesson2-account](../tutorials/media/aas-lesson2-account.png)
  
    > [!NOTE]  
    > A Windows-felhasználói fiókok és -jelszavak használatával kapcsolódhat legbiztonságosabban az adatforrásokhoz.
  
5.  A Kezelőben válassza ki az **AdventureWorksDW2014** adatbázist, majd kattintson az **OK** gombra. Így létrehozza a kapcsolatot az adatbázissal. 
  
6.  A Kezelőben jelölje be a következő táblák jelölőnégyzetét: **DimCustomer**, **DimDate**, **DimGeography**, **DimProduct**, **DimProductCategory**, **DimProductSubcategory** és **FactInternetSales**.  

    ![aas-lesson2-select-tables](../tutorials/media/aas-lesson2-select-tables.png)
  
Miután az OK gombra kattintott, megnyílik a Lekérdezésszerkesztő. A követező szakaszban csak az importálni kívánt adatokat jelölje ki.

  
## <a name="filter-the-table-data"></a>Táblák adatainak szűrése  
Az AdventureWorksDW2014 mintaadatbázisban lévő táblák olyan adatokat is tartalmaznak, amelyeket nem feltétlenül szükséges belefoglalni a modellbe. Amikor csak lehetséges, a szükségtelen adatokat érdemes kiszűrni, hogy csökkenthető legyen a modell által elfoglalt memória mérete. Ha egyes oszlopokat kiszűr a táblákból, azokat a rendszer nem importálja a munkaterület adatbázisába, illetve az üzembe helyezést követően a modell adatbázisába. 
  
#### <a name="to-filter-the-table-data-before-importing"></a>Táblák adatainak szűrése importálás előtt  
  
1.  A Lekérdezésszerkesztőben válassza ki a **DimCustomer** táblát. Megjelenik a DimCustomer tábla adatforrás-oldali (az AdventureWorksDW2014 mintaadatbázis) nézete. 
  
2.  Válassza ki egyszerre (Ctrl + kattintással) a **SpanishEducation**, **FrenchEducation**, **SpanishOccupation** és **FrenchOccupation** táblát, majd kattintson valamelyikre a jobb gombbal, és válassza az **Oszlopok eltávolítása** parancsot. 

    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-columns.png)
  
    Mivel ezeknek az oszlopoknak az értékei nem relevánsak az internetes értékesítések elemzésekor, nem szükséges importálni őket. A szükségtelen oszlopok eltávolításával csökken a modell mérete, és a modell hatékonyabbá válik.  

    > [!TIP]
    > Ha hibázna, az **ALKALMAZOTT LÉPÉSEK** listában lévő lépések törlésével visszaállíthatja a törölt oszlopokat.   
    
    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-step.png)

  
4.  Szűrje meg a többi táblát is a következő oszlopok az összes érintett táblából történő eltávolításával:  
    
    **DimDate**
    
      |Oszlop|  
      |--------|  
      |**DateKey**|  
      |**SpanishDayNameOfWeek**|  
      |**FrenchDayNameOfWeek**|  
      |**SpanishMonthName**|  
      |**FrenchMonthName**|  
  
    **DimGeography**
  
      |Oszlop|  
      |-------------|  
      |**SpanishCountryRegionName**|  
      |**FrenchCountryRegionName**|  
      |**IpAddressLocator**|  
  
    **DimProduct**
  
      |Oszlop|  
      |-----------|  
      |**SpanishProductName**|  
      |**FrenchProductName**|  
      |**FrenchDescription**|  
      |**ChineseDescription**|  
      |**ArabicDescription**|  
      |**HebrewDescription**|  
      |**ThaiDescription**|  
      |**GermanDescription**|  
      |**JapaneseDescription**|  
      |**TurkishDescription**|  
  
    **DimProductCategory**
  
      |Oszlop|  
      |--------------------|  
      |**SpanishProductCategoryName**|  
      |**FrenchProductCategoryName**|  
  
    **DimProductSubcategory**
  
      |Oszlop|  
      |-----------------------|  
      |**SpanishProductSubcategoryName**|  
      |**FrenchProductSubcategoryName**|  
  
    **FactInternetSales**
  
      Nem lettek oszlopok törölve.
  
## <a name="Import"></a>A kiválasztott táblák és oszlopok adatainak importálása  
Most, hogy megtekintette és kiszűrte a szükségtelen adatokat, a mér csak a szükséges adatokat fogja importálni. A varázsló a táblák adatait a táblák közötti kapcsolatokkal együtt importálja. Új táblák és oszlopok jönnek létre a modellben, és a rendszer a kiszűrt adatokat nem importálja.  
  
#### <a name="to-import-the-selected-tables-and-column-data"></a>A kiválasztott táblák és oszlopok adatainak importálása  
  
1.  Tekintse át a kiválasztott elemeket. Ha mindent rendben talál, kattintson az **Importálás** parancsra. Az Adatfeldolgozás párbeszédpanel az adatforrásból a munkaterület adatbázisába importált adatok állapotát mutatja.
  
    ![aas-lesson2-success](../tutorials/media/aas-lesson2-success.png) 
  
2.  Kattintson a **Bezárás** gombra.  

  
## <a name="save-your-model-project"></a>A modellprojekt mentése  
Fontos, hogy gyakran mentse a modellprojektet.  
  
#### <a name="to-save-the-model-project"></a>A modellprojekt mentése  
  
-   Kattintson a **Fájl** > **Az összes mentése** elemre.  
  
## <a name="whats-next"></a>A következő lépések
[3. lecke: Megjelölés dátumtáblaként](../tutorials/aas-lesson-3-mark-as-date-table.md)

  
  
