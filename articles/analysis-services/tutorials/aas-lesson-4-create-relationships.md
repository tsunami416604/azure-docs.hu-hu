---
title: 'Azure Analysis Services oktatóanyag – 4. lecke: Kapcsolatok létrehozása | Microsoft Docs'
description: A lecke a kapcsolatok létrehozását ismerteti az Azure Analysis Services oktatóprojektjében.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d8ee78ecb253e0b29fd0c2fee2388195c8aa6918
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596838"
---
# <a name="create-relationships"></a>Kapcsolatok létrehozása

Ebben a leckében ellenőrzi az adatok importálásakor automatikusan létrehozott kapcsolatokat, és új kapcsolatokat vesz fel a különböző táblák között. Az ilyen két tábla közti kapcsolatok az adott táblák adatainak korrelációját határozzák meg. Például a DimProduct és a DimProductSubcategory táblák kapcsolatának alapja, hogy mindegyik termék valamely alkategóriába tartozik. További tudnivalókért lásd a [kapcsolatokat](https://docs.microsoft.com/sql/analysis-services/tabular-models/relationships-ssas-tabular) ismertető cikket.
  
A lecke elvégzésének várható időtartama: **10 perc**.  
  
## <a name="prerequisites"></a>Előfeltételek  
Ez a témakör a táblázatos modellezéssel foglalkozó oktatóanyag részét képezi, amelyet a megfelelő sorrendben kell elvégezni. A leckében foglalt feladatok végrehajtása előtt el kell végeznie az előző leckét ([3. lecke: Megjelölés dátumtáblaként](../tutorials/aas-lesson-3-mark-as-date-table.md)). 
  
## <a name="review-existing-relationships-and-add-new-relationships"></a>Meglévő kapcsolatok áttekintése és új kapcsolatok létrehozása  
Amikor az Adatok lekérése utasítás használatával importálta az adatokat, hét táblát hozott létre az AdventureWorksDW2014 adatbázisból. Általában amikor relációs forrásból importál adatokat, a meglévő kapcsolatok automatikusan importálva lesznek az adatokkal együtt. Ahhoz, hogy az Adatok lekérése automatikusan létrehozza a kapcsolatokat az adatmodellben, az adatforrásban is kapcsolatoknak kell fennállnia a táblák között.

Mielőtt folytatná a modell elkészítését, ellenőriznie kell, hogy a táblák közötti kapcsolatok megfelelően lettek-e létrehozva. Ebben az oktatóanyagban emellett három új kapcsolatot is fel fog venni.  

  
#### <a name="to-review-existing-relationships"></a>Meglévő kapcsolatok áttekintése  
  
1.  Kattintson a **Modell** menü **Modellnézet** > **Diagramnézet** elemére.  

    A modelltervező ekkor Diagramnézetben jelenik meg, amely egy olyan grafikus formátum, ahol az importált táblák vonalakkal összekötve láthatók. A táblák közötti vonalak az adatok importálásakor automatikusan létrehozott kapcsolatokat jelölik.
    
    ![aas-lesson4-diagram](../tutorials/media/aas-lesson4-diagram.png)
  
    > [!NOTE]
    > Ha nem lát kapcsolatokat a táblák közt, valószínű, hogy az adatforráson sincs közöttük kapcsolat.

    A modelltervező jobb alsó sarkában lévő minitérkép-vezérlők használatával jeleníthet meg minél több táblát. A táblákra kattintva, és azokat a megfelelő helyre áthúzva közelebb húzhatja a táblákat egymáshoz, vagy adott sorrendbe rendezheti őket. A táblák áthelyezése a köztük meglévő kapcsolatokat nem befolyásolja. Ha egy adott tábla összes oszlopát meg szeretné tekinteni, a tábla szélére kattintva, és odébb húzva növelheti vagy csökkentheti a tábla méretét.  
  
2.  Kattintson a folytonos vonalra a **DimCustomer** és a **DimGeography** tábla között. A folytonos vonal a két tábla között azt mutatja, hogy a kapcsolat aktív, azaz hogy alapértelmezés szerint a rendszer ezt használja a DAX-képletek számításához.  
  
    Láthatja, hogy a **DimCustomer** tábla **GeographyKey** oszlopa és a **DimGeography** tábla **GeographyKey** oszlopa egy mezőben jelenik meg. A kapcsolat ezeket az oszlopokat használja. A kapcsolat tulajdonságai megjelennek a **Tulajdonságok** ablakban.  
  
    > [!TIP]  
    > Amellett, hogy a modelltervezőt megjelenítheti diagramnézetben, a Kapcsolatok kezelése párbeszédpanel használatával táblázatos formában jelenítheti meg az összes tábla minden kapcsolatát. A Tabular Model Explorerben kattintson a jobb gombbal a **Kapcsolatok** > **Kapcsolatok kezelése** elemre.
  
3.  Ellenőrizze, hogy az alábbi kapcsolatok létre lettek-e hozva az egyes táblák az AdventureWorksDW adatbázisból történő importálásakor:  
  
    |Aktív|Tábla|Kapcsolódó keresési tábla|  
    |----------|---------|------------------------|  
    |Igen|**DimCustomer [GeographyKey]**|**DimGeography [GeographyKey]**|  
    |Igen|**DimProduct [ProductSubcategoryKey]**|**DimProductSubcategory [ProductSubcategoryKey]**|  
    |Igen|**DimProductSubcategory [ProductCategoryKey]**|**DimProductCategory [ProductCategoryKey]**|  
    |Igen|**FactInternetSales [CustomerKey]**|**DimCustomer [CustomerKey]**|  
    |Igen|**FactInternetSales [ProductKey]**|**DimProduct [ProductKey]**|  
  
    Ha bármelyik kapcsolat hiányzik, ellenőrizze, hogy a modell tartalmazza-e a következő táblákat: DimCustomer, DimDate, DimGeography, DimProduct, DimProductCategory, DimProductSubcategory és FactInternetSales. Ha egyazon adatforrásból különböző időpontokban importál táblákat, az adott táblák közötti kapcsolatok nem lesznek létrehozva, és manuálisan kell létrehozni azokat. Ha nem láthatók kapcsolatok, ez azt jelenti, hogy az adatforrásban sincsenek. Manuálisan létrehozhatja őket az adatmodellben.

### <a name="take-a-closer-look"></a>Közelebbi vizsgálat
A Diagramnézetben észrevehet egy nyilat, egy csillagot és egy számot a táblák közötti kapcsolatokat mutató vonalakon.

![aas-lesson4-line](../tutorials/media/aas-lesson4-line.png)

A nyilak a szűrő irányát mutatják. A csillag mutatja, hogy az adott tábla több oldalt, az egyes szám, hogy az adott tábla egy oldalt jelent a kapcsolat számosságában. Ha valamely kapcsolatot szerkeszteni szeretné, például módosítani szeretné a szűrő irányát vagy a számosságot, kattintson duplán a kapcsolatot jelentő vonalra a Kapcsolat szerkesztése párbeszédpanel megnyitásához.

![aas-lesson4-edit](../tutorials/media/aas-lesson4-edit.png)

Ezek a funkciók fejlett adatmodellezéshez használhatók, és a jelen oktatóanyag nem tér ki rájuk. További tudnivalókért lásd [az Analysis Services táblázatos modelljeihez készült kétirányú keresztszűrők](https://docs.microsoft.com/sql/analysis-services/tabular-models/bi-directional-cross-filters-tabular-models-analysis-services) használatának ismertetését.

Egyes esetekben további kapcsolatokat kell létrehozni a modell táblái között bizonyos üzleti logikák támogatásához. Ebben az oktatóanyagban három további kapcsolatot kell majd létrehoznia a FactInternetSales és a DimDate tábla között.  
  
#### <a name="to-add-new-relationships-between-tables"></a>Új kapcsolatok létrehozása táblák között  
  
1.  A modelltervezőben kattintson az **OrderDate** oszlopra a **FactInternetSales** táblában, majd a gombot lenyomva tartva húzza a mutatót a **DimDate** tábla **Date** oszlopára, és engedje el.  

    Egy folytonos vonal jelenik meg, amely jelzi, hogy egy aktív kapcsolatot hozott létre az **Internet Sales** tábla **OrderDate** oszlopa és a **Date** tábla **Date** oszlopa között. 
  
      ![aas-lesson4-new](../tutorials/media/aas-lesson4-new.png) 
  
    > [!NOTE]  
    > A kapcsolatok létrehozásakor a rendszer automatikusan választja meg a számosságot és a szűrő irányát az elsődleges tábla és a kapcsolódó keresési tábla között.  
  
2.  Kattintson a **DueDate** oszlopra a **FactInternetSales** táblában, majd a gombot lenyomva tartva húzza a mutatót a **DimDate** tábla **Date** oszlopára, és engedje el.  
  
    Egy pontozott vonal jelenik meg, amely jelzi, hogy egy inaktív kapcsolatot hozott létre a **FactInternetSales** tábla **DueDate** oszlopa és a **DimDate** tábla **Date** oszlopa között. A táblák között több kapcsolattal is rendelkezhet, azonban egy időben csak egy kapcsolat lehet aktív. Az inaktív kapcsolatok aktívvá tehetők speciális összesítések egyéni DAX-kifejezésekben történő végrehajtásához.  
  
3.  Végül hozzon létre még egy kapcsolatot. Kattintson a **ShipDate** oszlopra a **FactInternetSales** táblában, majd a gombot lenyomva tartva húzza a mutatót a **DimDate** tábla **Date** oszlopára, és engedje el.  
    
     ![aas-lesson4-newinactive](../tutorials/media/aas-lesson4-newinactive.png)
  
## <a name="whats-next"></a>A következő lépések
[5. lecke: Számított oszlopok létrehozása](../tutorials/aas-lesson-5-create-calculated-columns.md)
  
  
  
