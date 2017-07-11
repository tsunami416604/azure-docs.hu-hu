---
title: "Az Azure Analysis Services oktatóanyaga – 10. lecke: Partíciók létrehozása | Microsoft Docs"
description: "A lecke a partíciók létrehozását ismerteti az Azure Analysis Services oktatóprojektjében."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: df74d9cbdcf4916c24955e491767589e72389155
ms.contentlocale: hu-hu
ms.lasthandoff: 06/03/2017

---
<a id="lesson-10-create-partitions" class="xliff"></a>

# 10. lecke: Partíciók létrehozása

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Ebben a leckében partíciókat hoz létre a FactInternetSales tábla kisebb logikai részekre való felosztásához, amelyek a többi partíciótól függetlenül dolgozhatók fel (frissíthetők). Alapértelmezés szerint a modellekben foglalt táblák mindegyike egy partícióval rendelkezik, amely az adott tábla összes oszlopát és sorát tartalmazza. A FactInternetSales tábla esetében az adatokat éves bontásban szeretnénk kezelni, a táblában szereplő mind az öt év esetében évenként egy-egy partícióban. Ezután mindegyik partíció egymástól függetlenül kezelhető. További tudnivalókért lásd a [partíciókat](https://docs.microsoft.com/sql/analysis-services/tabular-models/partitions-ssas-tabular) ismertető cikket. 
  
A lecke elvégzésének várható időtartama: **15 perc**.  
  
<a id="prerequisites" class="xliff"></a>

## Előfeltételek  
Ez a témakör a táblázatos modellezéssel foglalkozó oktatóanyag részét képezi, amelyet a megfelelő sorrendben kell elvégezni. A leckében foglalt feladatok végrehajtása előtt el kell végeznie az előző leckét ([9. lecke: Hierarchiák létrehozása](../tutorials/aas-lesson-9-create-hierarchies.md)).  
  
<a id="create-partitions" class="xliff"></a>

## Partíciók létrehozása  
  
<a id="to-create-partitions-in-the-factinternetsales-table" class="xliff"></a>

#### A FactInternetSales tábla partícióinak létrehozása  
  
1.  A Tabular Model Explorerben bontsa ki a **Táblák** fát, és kattintson a jobb gombbal a **FactInternetSales** > **Partíciók** elemre.  
  
2.  A partíciókezelőben kattintson a **Másolás** parancsra, és változtassa a nevet **FactInternetSales2010** értékre.
  
    Mivel a partícióba csak egy adott időszakhoz (a 2010-es évhez) tartozó sorokat szeretne belefoglalni, módosítania kell a lekérdezés kifejezését.
  
4.  Kattintson a **Tervezés** gombra a lekérdezésszerkesztő megnyitásához, majd kattintson a **FactInternetSales2010** lekérdezésre.

5.  Az előnézetben kattintson az **OrderDate** sor fejlécére, majd a **Dátum-/időszűrők** > **Között** elemére.

    ![aas-lesson10-query-editor](../tutorials/media/aas-lesson10-query-editor.png)

6.  A Sorok szűrése párbeszédpanelen, a **Sorok mutatása, ahol: OrderDate** mezőnél hagyja meg az **ez utáni vagy egyenlő** beállítást, majd a dátummezőben adja meg az **1/1/2010** értéket. Hagyja az **És** operátort kiválasztva, majd válassza a **korábbi a következőnél** beállítást, a dátummezőben adja meg az **1/1/2011** értéket, és kattintson az **OK** gombra.

    ![aas-lesson10-filter-rows](../tutorials/media/aas-lesson10-filter-rows.png)
    
    Észreveheti, hogy a lekérdezésszerkesztőben az ALKALMAZOTT LÉPÉSEK között egy további lépés látható, Szűrt sorok néven. Ezzel a szűrővel a 2010-es dátummal rendelkező megrendelésekre lehet szűrni.

8.  Kattintson az **Importálás** gombra.

    A partíciókezelőben látható, hogy a lekérdezési kifejezés egy újabb szűrt sorok záradékkal bővült.

    ![aas-lesson10-query](../tutorials/media/aas-lesson10-query.png)
  
    Ez az utasítás meghatározza, hogy a partíció csak olyan sorok adatait tartalmazhatja, ahol az OrderDate a 2010-es naptári évre esik, ahogy azt a szűrt sorok záradék megadja.  
  
  
<a id="to-create-a-partition-for-the-2011-year" class="xliff"></a>

#### Partíció létrehozása a 2011-es évhez  
  
1.  A partíciók listájában kattintson a korábban létrehozott **FactInternetSales2010** partícióra, majd a **Másolás** parancsra.  Módosítsa a partíció nevét a **FactInternetSales2011** értékre. 

    A lekérdezésszerkesztőben nem kell új szűrt sorok záradékot létrehoznia. Mivel létrehozta a 2010-es lekérdezés másolatát, csupán minimálisan kell módosítania azt, a 2011-es évre vonatkozóan.
  
2.  Annak érdekében, hogy a partíció csak a 2011-es év sorait tartalmazza, a **Lekérdezés kifejezésében** cserélje le a megfelelő évszámokat a Szűrt sorok záradékban a **2011** és **2012** értékre, ahogy az alábbiakban is látható:  
  
    ```  
    let
        Source = #"SQL/localhost;AdventureWorksDW2014",
        dbo_FactInternetSales = Source{[Schema="dbo",Item="FactInternetSales"]}[Data],
        #"Removed Columns" = Table.RemoveColumns(dbo_FactInternetSales,{"OrderDateKey", "DueDateKey", "ShipDateKey"}),
        #"Filtered Rows" = Table.SelectRows(#"Removed Columns", each [OrderDate] >= #datetime(2011, 1, 1, 0, 0, 0) and [OrderDate] < #datetime(2012, 1, 1, 0, 0, 0))
    in
        #"Filtered Rows"
   
    ```  
  
<a id="to-create-partitions-for-2012-2013-and-2014" class="xliff"></a>

#### Partíció létrehozása a 2012-es, 2013-as és 2014-es évhez  
  
- A fenti lépéseket követve hozzon létre partíciókat a 2012-es, 2013-as és 2014-es évhez, és az évszámokat a Szűrt sorok záradékban a megfelelő évnek megfelelően módosítsa. 
  

<a id="delete-the-factinternetsales-partition" class="xliff"></a>

## A FactInternetSales partíció törlése
Most, hogy minden évhez létrehozott egy partíciót, törölheti a FactInternetSales partíciót, így megelőzi az átfedéseket az Összes feldolgozása parancs a partíciók feldolgozása során történő végrehajtásakor.

<a id="to-delete-the-factinternetsales-partition" class="xliff"></a>

#### A FactInternetSales partíció törlése
-  Kattintson a FactInternetSales partícióra, majd a **Törlés** parancsra.



<a id="process-partitions" class="xliff"></a>

## Partíciók feldolgozása  
A partíciókezelőben láthatja, hogy az egyes létrehozott partíciók **Utoljára feldolgozva** oszlopában látható, hogy a partíciók még soha nem lettek feldolgozva. A partíciók létrehozásakor érdemes futtatnia egy Partíciók feldolgozása vagy Tábla feldolgozása műveletet az adott partíciók adatainak frissítéséhez.  
  
<a id="to-process-the-factinternetsales-partitions" class="xliff"></a>

#### A FactInternetSales-partíciók feldolgozása  
  
1.  A partíciókezelő bezárásához kattintson az **OK** gombra.  
  
2.  Kattintson a **FactInternetSales** táblára, majd a **Modell** menü **Feldolgozás** > **Partíciók feldolgozása** elemére.  
  
3.  A Partíciók feldolgozása párbeszédpanelen bizonyosodjon meg róla, hogy a **Mód** **Alapértelmezés feldolgozása** értékre van állítva.  
  
4.  Jelölje be a jelölőnégyzetet a **Feldolgozás** oszlopban mind az öt létrehozott partíciónál, majd kattintson az **OK** gombra.  

    ![aas-lesson10-process-partitions](../tutorials/media/aas-lesson10-process-partitions.png)
  
    Ha a rendszer bekéri a megszemélyesítési hitelesítő adatait, adja meg a 2. leckében megadott Windows-felhasználónevet és -jelszót.  
  
    Megjelenik az **Adatfeldolgozás** párbeszédpanel, amelyen az egyes partíciók feldolgozásának részletei láthatók. Láthatja, hogy az egyes partíciók esetében eltérő mennyiségű sor átvitele történik meg. Mindegyik partíció csak az SQL-utasítás WHERE záradékában megadott évhez tartozó sorokat tartalmazza. A feldolgozás végeztével nyugodtan zárja be az Adatfeldolgozás párbeszédpanelt.  
  
    ![aas-lesson10-process-complete](../tutorials/media/aas-lesson10-process-complete.png)
  
<a id="whats-next" class="xliff"></a>

 ## A következő lépések
Tovább a következő leckére: [11. lecke: Szerepkörök létrehozása](../tutorials/aas-lesson-11-create-roles.md). 

