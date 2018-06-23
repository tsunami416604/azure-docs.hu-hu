---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: ff36202b67f6262b7ba67fe48ef37f2b656b91fa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348186"
---
<a name="definitions"></a>
## <a name="definitions"></a>Meghatározások

<a name="point"></a>
### <a name="point"></a>Pont

|Name (Név)|Leírás|Séma|
|---|---|---|
|**Időbélyeg**  <br>*optional*|Az adatpont jelző időbélyegző. Győződjön meg arról, hogy igazodik a éjfél, és UTC dátum idő karakterlánc, például 2017-08-01T00:00:00Z.|karakterlánc (dátum, idő)|
|**Érték**  <br>*optional*|Mérték értéke.|szám (kétirányú)|


<a name="request"></a>
### <a name="request"></a>Kérés

|Name (Név)|Leírás|Séma|
|---|---|---|
|**Időtartam**  <br>*optional*|Az adatpontok időtartama. Ha az érték null, illetve nem jelent-e, az API-t automatikusan az időtartam határozza meg.|szám (kétirányú)|
|**Pontok**  <br>*optional*|A sorozat időadatok mutat. Az adatok az anomáliadetektálási eredmény kereséséhez növekvő időbélyeg kell rendezni. Ha az adatok helytelen sorrendben, vagy ismétlődő időbélyeg, az API-t észlelni fogja az anomáliadetektálási pontok megfelelően, de a pontokat ad vissza, amelyben a bemeneti sikerült nem is egyezik. Ebben az esetben egy hibaüzenet bekerül a válaszban.|< [mutasson](#point) > tömb|


<a name="response"></a>
### <a name="response"></a>Válasz

|Name (Név)|Leírás|Séma|
|---|---|---|
|**ExpectedValues**  <br>*optional*|Az előre jelzett érték által a tanulási modell alapú. A bemeneti adatpontok növekvő időbélyeg vannak rendezve, ha az index a tömb hozzárendelését a várt érték és az eredeti érték használható.|< szám (dupla) > tömb|
|**IsAnomaly**  <br>*optional*|Az adatpont van rendellenességek észlelését, vagy nem mindkét irányban (igényeiben jelentkező vagy immerzióban) eredményét. Igaz értéke azt jelenti, hogy a pont anomáliadetektálási, hamis azt jelenti, hogy a pontot nem anomáliadetektálási. A bemeneti adatpontok növekvő időbélyeg vannak rendezve, ha az index a tömb hozzárendelését a várt érték és az eredeti érték használható.|< boolean > tömb|
|**IsAnomaly_Neg**  <br>*optional*|Az eredmény az adatpont van rendellenességeket negatív irányban (immerzióban). Igaz azt jelenti, hogy az anomáliadetektálási irányának negatív. A bemeneti adatpontok növekvő időbélyeg vannak rendezve, ha az index a tömb hozzárendelését a várt érték és az eredeti érték használható.|< boolean > tömb|
|**IsAnomaly_Pos**  <br>*optional*|Az eredmény az adatpont van rendellenességeket pozitív irányban (igényeiben jelentkező). Igaz azt jelenti, hogy az anomáliadetektálási irányának pozitív. A bemeneti adatpontok növekvő időbélyeg vannak rendezve, ha az index a tömb hozzárendelését a várt és eredeti érték használható.|< boolean > tömb|
|**LowerMargin**  <br>*optional*|(ExpectedValue - LowerMargin) határozza meg, az alsó határ értékének adatpont van még re normál. A bemeneti adatpontok növekvő időbélyeg vannak rendezve, ha az index a tömb hozzárendelését a várt érték és az eredeti érték használható.|< szám (dupla) > tömb|
|**Időtartam**  <br>*optional*|Az időtartamot, ameddig az API-t használja az anomáliadetektálási pontok észleléséhez.|szám (float)|
|**UpperMargin**  <br>*optional*|A ExpectedValue és UpperMargin meghatározza, hogy a felső határa, amely adatpont van még re normál. A bemeneti adatpontok növekvő időbélyeg vannak rendezve, ha az index a tömb hozzárendelését a várt érték és az eredeti érték használható.|< szám (dupla) > tömb|
|**WarningText**  <br>*optional*|Ha a megadott bemeneti adatok pontok nem követi a szabályt, amely az API-t igényel, és az adatok továbbra is észlelik az API-t, az API-t elemezheti az adatokat, és a figyelmeztető információkat ebben a mezőben hozzáfűzése.|sztring|



