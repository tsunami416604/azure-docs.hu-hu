---
title: Elnevezett entitások egészségügyi ellátáshoz
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 6880391fb54791fe5f597de2305d24f8c0e47ec6
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122361"
---
## <a name="health-entity-categories"></a>Állapot entitások kategóriái:

A következő entitás-kategóriákat adja vissza a [text Analytics for Health](../../how-tos/text-analytics-for-health.md).  Vegye figyelembe, hogy ebben a tároló-előnézetben csak az angol nyelvű szöveg támogatott, és az egyes tárolók rendszerképében csak egyetlen modell-verzió van megadva.

### <a name="named-entity-recognition"></a>Megnevezett entitások felismerése

|Kategória  |Leírás   |
|----------|--------------|
| Kor | Alábbi korhatárt szabja. Például *30 éves*. |
| AdministrativeEvent | Egy felügyeleti esemény. |
| BodyStructure | Az emberi törzs részei, beleértve a szerveket és más struktúrákat. Például *ARM*vagy *Heart*. | 
| CareEnvironment | Az a környezet, amelyben az ellátást vagy a kezelést felügyelik. Például *vészhelyzeti helyiség* | 
| ConditionQualifier | Feltétel szintjei. Például *enyhe*, *kiterjesztett*vagy *diffúz*. | 
| Diagnosztikai | Egészségügyi feltételek. Például *magas vérnyomás*. | 
| Irány | Irányban. Például *balra* vagy *anterior*. | 
| Adagolás | A gyógyszer mérete vagy mennyisége. Például *25mg*.  | 
| ExaminationName | Egy vizsgálati módszer vagy eljárás. Például: *X-ray*. | 
| RelationalOperator | Egy operátor, amely két entitás közötti kapcsolatot határoz meg. Például *kisebb, mint*, vagy `>=` .  | 
| MeasurementUnit | Egy mérési egység (például százalék). | 
| MeasurementValue | Egy mérési egység numerikus értéke. | 
| FamilyRelation | Egy családi kapcsolat. Például: *Sister*.  | 
| Frequency | Gyakoriságokat.   | 
| Nem | Nemek. | 
| Gén | Egy gén entitás, például *TP53*.   | 
| HealthcareProfession | A gyógyszer felügyeletének módszere. Például: *szóbeli felügyelet*. | 
| MedicationClass | Gyógyszeres osztályok. Például *antibiotikumok*.  | 
| MedicationForm | A gyógyszert egy formája. Például: *kapszula*. | 
| MedicationName  | Általános és márka nevű gyógyszer. Például az *ibuprofen*. | 
| MedicationRoute | A gyógyszer felügyeletének módszere. Például: *szóbeli felügyelet*. | 
| SymptomOrSign  | Megbetegedés tünetei. Például *fáj a torka*. | 
| Idő | Alkalommal. Például: *8 év* , vagy *2. reggel* |
| TreatmentName  | A kezelési nevek. Például: *terápia*. | 
| Változat | A gén entitás genetikai változata. | 

### <a name="relation-extraction"></a>Kapcsolatok kibontása

A kapcsolat kibontása azonosítja a szövegben említett fogalmak közötti értelmes kapcsolatokat. A "feltétel időpontja" típusú kapcsolatok például a feltétel nevének egy időponthoz társításával találhatók meg. Az állapot Text Analytics a következő kapcsolatokat tudja azonosítani:

|Kategória  |Leírás   |
|----------|--------------|
| DirectionOfBodyStructure | A törzs struktúrájának iránya. |
| DirectionOfCondition | Egy feltétel iránya. |
| DirectionOfExamination | Egy vizsgálat iránya. |
| DirectionOfTreatment | A kezelés iránya. |
| TimeOfCondition | Egy feltétel kialakulásához kapcsolódó idő. |
| QualifierOfCondition | Egy feltétel társított minősítője. |
| DosageOfMedication | A gyógyszer adagolása. |
| FormOfMedication | A gyógyszert egy formája. |
| RouteOfMedication | Egy gyógyszer fogyasztásának útvonala vagy módja. Például: *szóbeli*. |
| FrequencyOfMedication | A gyógyszer felhasználásának gyakorisága. | 
| ValueOfCondition | Egy feltétellel társított numerikus érték. |
| UnitOfCondition | Egy adott feltételhez társított egység (például idő). |
| TimeOfMedication | Az az idő, amikor a gyógyszert felhasználták. |
| TimeOfTreatment | A kezelés felügyeletének időpontja. | 
| FrequencyOfTreatment | A kezelés felügyeletének gyakorisága. |
| ValueOfExamination | Egy vizsgálathoz rendelt numerikus érték. | 
| UnitOfExamination | Egy olyan egység (például százalék), amely egy vizsgálathoz van társítva. |
| RelationOfExamination | Egy entitás és egy vizsgálat közötti kapcsolat. | 
| TimeOfExamination | A vizsgálathoz kapcsolódó idő. |
| Rövidítés | Egy rövidítés.  | 
