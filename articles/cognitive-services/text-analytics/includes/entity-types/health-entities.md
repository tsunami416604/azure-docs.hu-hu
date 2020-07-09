---
title: Elnevezett entitások egészségügyi ellátáshoz
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/15/2020
ms.author: aahi
ms.openlocfilehash: 906e8e9e6cde80a2151c6b8671f64854cb431250
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108497"
---
## <a name="health-entity-categories"></a>Állapot entitások kategóriái:

A következő entitás-kategóriákat adja vissza a [text Analytics for Health](../../how-tos/text-analytics-for-health.md).  Vegye figyelembe, hogy ebben a tároló-előnézetben csak az angol nyelvű szöveg támogatott, és az egyes tárolók rendszerképében csak egyetlen modell-verzió van megadva.

### <a name="named-entity-recognition"></a>Megnevezett entitások felismerése

|Kategória  |Leírás   |
|----------|--------------|
| KOR | Alábbi korhatárt szabja. |
| BODY_STRUCTURE | Az emberi törzs részei, beleértve a szerveket és más struktúrákat. | 
| CONDITION_QUALIFIER | Feltétel szintjei, például *enyhe*, *bővített*vagy *diffúz*. | 
| DIAGNOSZTIKAI | Egészségügyi feltételek. Például *magas vérnyomás* . | 
| IRÁNYBA | Irányok, például *balra* vagy *anterior*. | 
| ADAGOLÁS | A gyógyszer mérete vagy mennyisége.  | 
| EXAMINATION_NAME | Egy vizsgálati módszer vagy eljárás. | 
| EXAMINATION_RELATION | egy mérési egység és egy vizsgálat közötti társítás.  | 
| EXAMINATION_UNIT | Egy mérési egység a vizsgálathoz. | 
| EXAMINATION_VALUE | A vizsgálati mérési egység értéke. | 
| FAMILY_RELATION | Egy családi kapcsolat, például *testvér*.  | 
| FREKVENCIA | Gyakoriságokat.   | 
| NEMEK | Nemek. | 
| GÉN | Egy gén entitás, például *TP53*.   | 
| MEDICATION_CLASS | Gyógyszeres osztályok. Például *antibiotikumok*.  | 
| MEDICATION_NAME  | Általános és márka nevű gyógyszer.| 
| ROUTE_OR_MODE  | A gyógyszer felügyeletének módszere. | 
| SYMPTOM_OR_SIGN  | Megbetegedés tünetei. | 
| TIME  | Alkalommal. Ilyen például a "8 év" vagy a "2:07:30 ma reggel" |
| TREATMENT_NAME  | A kezelési nevek. | 
| VARIANT  | A gén entitás genetikai változata | 

### <a name="relation-extraction"></a>Kapcsolatok kibontása

A kapcsolat kibontása azonosítja a szövegben említett fogalmak közötti értelmes kapcsolatokat. A "feltétel időpontja" típusú kapcsolatok például a feltétel nevének egy időponthoz társításával találhatók meg. Az állapot Text Analytics a következő kapcsolatokat tudja azonosítani:

* DIRECTION_OF_BODY_STRUCTURE  
* TIME_OF_CONDITION
* QUALIFIER_OF_CONDITION  
* DOSAGE_OF_MEDICATION 
* FORM_OF_MEDICATION  
* ROUTE_OR_MODE_OF_MEDICATION   
* STRENGTH_OF_MEDICATION 
* ADMINISTRATION_RATE_OF_MEDICATION   
* FREQUENCY_OF_MEDICATION 
* TIME_OF_MEDICATION 
* TIME_OF_TREATMENT 
* FREQUENCY_OF_TREATMENT  
* VALUE_OF_EXAMINATION
* UNIT_OF_EXAMINATION 
* RELATION_OF_EXAMINATION 
* TIME_OF_EXAMINATION  
* RÖVIDÍTÉSE 
