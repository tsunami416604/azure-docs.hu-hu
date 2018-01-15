---
title: "Azure Analysis Services oktatóanyag - 6. lecke: Mértékek létrehozása | Microsoft Docs"
description: "A lecke a mértékek létrehozását ismerteti az Azure Analysis Services oktatóprojektjében."
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
ms.date: 01/08/2018
ms.author: owend
ms.openlocfilehash: fa47d4ea9aa019464e465c051b016dac7c224dc9
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2018
---
# <a name="create-measures"></a>Mértékek létrehozása

Ebben a leckében a modellbe foglalandó mértékeket hoz majd létre. A létrehozott számított oszlopokhoz hasonlóan a mérték is egy DAX-képlet használatával készített számítás. A számított oszlopokkal ellentétben azonban a mértékek kiértékelése a felhasználó által kiválasztott *szűrőkkel* történik. Például egy kimutatás Sorfeliratok mezőjéhez adott oszloppal vagy szeletelővel. Az alkalmazott mérték a szűrő mindegyik cellájára számít egy értéket. A mértékek hatékony, rugalmas számítások, amelyeket szinte minden táblázatos modellbe érdemes bevenni, ha dinamikus számítások numerikus adatokon történő végrehajtására van szükség. További tudnivalókért lásd a [mértékek](https://docs.microsoft.com/sql/analysis-services/tabular-models/measures-ssas-tabular) ismertetését.
  
A mértékek létrehozásához a *Mérték rácsot* használhatja. Alapértelmezés szerint mindegyik tábla rendelkezik egy üres mérték ráccsal, azonban általában nem mindegyik táblához szokás mértéket létrehozni. A mérték rács a modellszerkesztő adatnézetében a tábla alatt látható. Az egyes táblákhoz tartozó mérték rácsok elrejtéséhez vagy megjelenítéséhez kattintson a **Tábla** menüre, majd a **Mérték rács megjelenítése** parancsra.  
  
Mérték létrehozásához kattintson a mérték rács egy üres cellájára, majd írja be a DAX-képletet a szerkesztőlécbe. Amikor lenyomja az ENTER billentyűt, a mérték megjelenik a cellában. Mértékek standard aggregátumfüggvényekkel is létrehozhatók valamely oszlopra, majd az eszköztáron az AutoSzum gombra (**∑**) kattintva. Az AutoSzum függvénnyel létrehozott mértékek közvetlenül a mérték rács az oszlop alatt lévő cellájában jelennek meg, azonban innen áthelyezhetők.  
  
Ebben a leckében mértékeket hoz létre DAX-képletek megadásával a szerkesztőlécben vagy az AutoSzum függvény használatával.  
  
A lecke elvégzésének várható időtartama: **30 perc**.  
  
## <a name="prerequisites"></a>Előfeltételek  
Ez a témakör a táblázatos modellezéssel foglalkozó oktatóanyag részét képezi, amelyet a megfelelő sorrendben kell elvégezni. A leckében foglalt feladatok végrehajtása előtt el kell végeznie az előző leckét ([5. lecke: Számított oszlopok létrehozása](../tutorials/aas-lesson-5-create-calculated-columns.md)).  
  
## <a name="create-measures"></a>Mértékek létrehozása  
  
#### <a name="to-create-a-dayscurrentquartertodate-measure-in-the-dimdate-table"></a>DaysCurrentQuarterToDate mérték létrehozása a DimDate táblában  
  
1.  A modelltervezőben kattintson a **DimDate** táblára.  
  
2.  A mértékrácson kattintson a bal felső üres cellára.  
  
3.  A képletsávban gépelje be a következő képletet:  
  
    ```
    DaysCurrentQuarterToDate:=COUNTROWS( DATESQTD( 'DimDate'[Date])) 
    ```
  
    Láthatja, hogy a bal felső cella most egy mérték nevét tartalmazza (**DaysCurrentQuarterToDate**), amelyet egy eredmény követ (**92**). Az eredmény jelenleg nem releváns, mivel nem lett alkalmazva felhasználói szűrő.
    
      ![aas-lesson6-newmeasure](../tutorials/media/aas-lesson6-newmeasure.png) 
    
    A számított oszlopoktól eltérően a mértékek képletei esetén beírhatja a mérték nevét, majd kettőspontot követően magát a képletet.

  
#### <a name="to-create-a-daysincurrentquarter-measure-in-the-dimdate-table"></a>DaysInCurrentQuarter mérték létrehozása a DimDate táblában  
  
1.  Miközben a **DimDate** tábla még aktív a modellszerkesztőben, a mérték rácsban kattintson egy üres cellára a létrehozott mérték alatt.  
  
2.  A képletsávban gépelje be a következő képletet:  
  
    ```
    DaysInCurrentQuarter:=COUNTROWS( DATESBETWEEN( 'DimDate'[Date], STARTOFQUARTER( LASTDATE('DimDate'[Date])), ENDOFQUARTER('DimDate'[Date])))
    ```
  
    Egy hiányos időszak és a megelőző időszak közötti összehasonlítási arány létrehozásakor. A képletnek ki kell számítania az időszak az időszakból eltelt rész arányát, és össze kell vetnie azt a megelőző időszak azonos arányú részével. Ebben az esetben a [DaysCurrentQuarterToDate]/[DaysInCurrentQuarter] képlet adja meg az aktuális időszak már eltelt részének arányát.  
  
#### <a name="to-create-an-internetdistinctcountsalesorder-measure-in-the-factinternetsales-table"></a>InternetDistinctCountSalesOrder mérték létrehozása a FactInternetSales táblában  
  
1.  Kattintson a **FactInternetSales** táblára.   
  
2.  Kattintson a **SalesOrderNumber** oszlop fejlécére.  
  
3.  Az eszköztáron kattintson a lefelé mutató nyílra az AutoSzum (**∑**) gomb mellett, majd válassza a **DistinctCount** elemet.  
  
    Az AutoSzum függvény automatikusan létrehoz egy mértéket a választott oszlophoz a DistinctCount standard aggregátumképlet használatával.  
    
       ![aas-lesson6-newmeasure2](../tutorials/media/aas-lesson6-newmeasure2.png)
  
4.  A mérték rácsban kattintson az új mértékre, majd a **Tulajdonságok** ablak **Mérték neve** mezőjében nevezze át a mértéket az **InternetDistinctCountSalesOrder** névre. 
 
  
#### <a name="to-create-additional-measures-in-the-factinternetsales-table"></a>További mértékek létrehozása a FactInternetSales táblában  
  
1.  Az AutoSzum függvény használatával hozza létre és nevezze el az alábbi mértékeket:  

    |Oszlop|Mérték neve|AutoSzum (∑)|Képlet|  
    |----------------|----------|-----------------|-----------|  
    |SalesOrderLineNumber|InternetOrderLinesCount|Darabszám|=DARAB2([SalesOrderLineNumber])|  
    |OrderQuantity|InternetTotalUnits|Összeg|=SZUM([OrderQuantity])|  
    |DiscountAmount|InternetTotalDiscountAmount|Összeg|=SZUM([DiscountAmount])|  
    |TotalProductCost|InternetTotalProductCost|Összeg|=SZUM([TotalProductCost])|  
    |SalesAmount|InternetTotalSales|Összeg|=SZUM([SalesAmount])|  
    |Margin|InternetTotalMargin|Összeg|=SZUM([Margin])|  
    |TaxAmt|InternetTotalTaxAmt|Összeg|=SZUM([TaxAmt])|  
    |Freight|InternetTotalFreight|Összeg|=SZUM([Freight])|  
  
2.  Kattintson a mérték rács egy üres cellájára, és a szerkesztőléc segítségével hozza létre sorrendben az alábbi egyéni mértékeket:  
  
      ```
      InternetPreviousQuarterMargin:=CALCULATE([InternetTotalMargin],PREVIOUSQUARTER('DimDate'[Date]))
      ```
      
      ```
      InternetCurrentQuarterMargin:=TOTALQTD([InternetTotalMargin],'DimDate'[Date])
      ```
  
      ```
      InternetPreviousQuarterMarginProportionToQTD:=[InternetPreviousQuarterMargin]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
      ```
      InternetPreviousQuarterSales:=CALCULATE([InternetTotalSales],PREVIOUSQUARTER('DimDate'[Date]))
      ```
  
      ```
      InternetCurrentQuarterSales:=TOTALQTD([InternetTotalSales],'DimDate'[Date])
      ```
      
      ```
      InternetPreviousQuarterSalesProportionToQTD:=[InternetPreviousQuarterSales]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
A FactInternetSales táblához létrehozott mértékek használatával elemezhetők az olyan kiemelten fontos információk, mint az értékesítések, a költségek és nyereségek a felhasználó által kiválasztott szűrőben meghatározott tételekhez.  
  
## <a name="whats-next"></a>A következő lépések
[7. lecke: Fő teljesítménymutatók létrehozása](../tutorials/aas-lesson-7-create-key-performance-indicators.md)  

  
