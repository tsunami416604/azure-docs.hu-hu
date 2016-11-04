---
title: Jelentések kezelése a JavaScript API használatával | Microsoft Docs
description: Power BI Embedded, jelentések kezelése a JavaScript API használatával
services: power-bi-embedded
documentationcenter: ''
author: guyinacube
manager: erikre
editor: ''
tags: ''

ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 10/04/2016
ms.author: asaxton

---
# Power BI-jelentések kezelése a JavaScript API használatával
A Power BI JavaScript API segítségével egyszerűen beágyazhatja a Power BI-jelentéseket az alkalmazásokba. Az API használatával az alkalmazások programozott módon képesek az olyan jelentéselemekkel való interakcióra, mint az oldalak és a szűrők. Ez az interaktivitás a Power BI-jelentéseket az alkalmazás még szervesebb részévé teszi.

A Power BI-jelentések olyan iframe elemek használatával ágyazhatók be az alkalmazásokba, amelyek üzemeltetése az alkalmazások részeként történik. Az iframe elemek az alkalmazás és a jelentés közötti határként viselkednek, ahogyan az a következő képen is látható. 

![A Power BI beágyazott iframe eleme JavaScript API nélkül](media\\powerbi-embedded-interact-with-reports\\powerbi-embedded-interact-report-1.png)

Az iframe elemek lényegesen leegyszerűsítik a beágyazási folyamatot, a JavaScript API nélkül azonban a jelentés és az alkalmazás nem képesek együttműködni. Az interaktivitás hiánya miatt úgy tűnhet, mintha a jelentés valójában nem lenne az alkalmazás része. Szükséges, hogy a jelentés és az alkalmazás képesek legyenek a kétirányú kommunikációra, ahogyan az a következő képen is látható.

![A Power BI beágyazott iframe eleme JavaScript API-val](media\\powerbi-embedded-interact-with-reports\\powerbi-embedded-interact-report-2.png)

A Power BI JavaScript API segítségével olyan kód írható, amely biztonságosan áthalad az iframe-határolón. Ez teszi lehetővé az alkalmazás számára a műveletek programozott módon történő végrehajtását a jelentésekben, valamint olyan műveleti események figyelését, amelyeket a felhasználók a jelentésen belül létrehoznak.

## Mire használható a Power BI JavaScript API?
A JavaScript API segítségével jelentéseket kezelhet, a jelentések adott lapjaira navigálhat, szűrheti a jelentéseket, és kezelheti a beágyazási eseményeket is. Az alábbi ábrán az API szerkezete látható.

![A Power BI JavaScript API-t bemutató ábra](media\\powerbi-embedded-interact-with-reports\\powerbi-embedded-interact-report-3.png)

### Jelentések kezelése
A JavaScript API-val az alábbi jelentés- és lapszintű viselkedések kezelhetők:

* Adott Power BI-jelentés biztonságos beágyazása az alkalmazásba – próbálja ki a [beágyazási bemutatóalkalmazást](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  * Hozzáférési jogkivonat beállítása
* A jelentés konfigurálása
  * A szűrőpanel és a lapnavigációs panel engedélyezése és letiltása – próbálja ki a [beállításfrissítési bemutatóalkalmazást](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  * Alapértelmezett beállítások megadása a lapok és a szűrők számára – próbálja ki az [alapértelmezett értékek megadására szolgáló bemutatóalkalmazást](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
* Teljes képernyős mód vagy kilépés a teljes képernyős módból

[További információ jelentések beágyazásáról](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)

### Navigálás egy jelentés adott lapjaira
A JavaScript API segítségével a jelentések összes lapját áttekintheti, és beállíthatja az aktuális lapot. Próbálja ki a [navigációs bemutatóalkalmazást](http://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[További információ a lapok közötti navigálásról](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### Jelentés szűrése
A JavaScript API a beágyazott jelentésekhez és azok lapjaihoz alapszintű és speciális szűrési képességeket biztosít. Próbálja ki a [szűrési bemutatóalkalmazást](http://azure-samples.github.io/powerbi-angular-client/#/scenario4), és tekintsen át néhány bevezető jellegű kódot.  

#### Alapszintű szűrők
Az alapszintű szűrők oszlop- vagy hierarchiaszinten vannak elhelyezve, és tartalmazzák a felvenni vagy kizárni kívánt értékek listáját.

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```


#### Speciális szűrők
A speciális szűrők AND vagy OR logikai operátorokat használnak, és egy vagy több feltételt fogadnak el, azok operátoraival és értékeivel együtt. Támogatott feltételek:

* None
* LessThan
* LessThanOrEqual
* GreaterThan
* GreaterThanOrEqual
* Contains
* DoesNotContain
* StartsWith
* DoesNotStartWith
* Is
* IsNot
* IsBlank
* IsNotBlank

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```
[További információk a szűrésről](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)

### Események kezelése
Az iframe elembe történő információküldés mellett az alkalmazás az alábbi, az iframe elemből érkező eseményekhez kapcsolódó információk fogadására is képes:

* Beágyazás
  * loaded
  * error
* Jelentések
  * pageChanged
  * dataSelected (hamarosan)

[További tudnivalók az események kezeléséről](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)

## Következő lépések
Az alábbi hivatkozásokra kattintva további információkhoz juthat a Power BI JavaScript API-ról:

* [JavaScript API wiki](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
* [Objektummodell-hivatkozás](https://microsoft.github.io/powerbi-models/modules/_models_.html)
* Példák
  * [Angular](http://azure-samples.github.io/powerbi-angular-client)
  * [Ember](https://github.com/Microsoft/powerbi-ember)
* [Élő bemutató](https://microsoft.github.io/PowerBI-JavaScript/demo/)

<!--HONumber=Oct16_HO3-->


