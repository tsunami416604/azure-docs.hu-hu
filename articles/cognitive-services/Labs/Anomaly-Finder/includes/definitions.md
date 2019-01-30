---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 5ad589c4adb60369f81979e214935f73d9eb0755
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228900"
---
<a name="definitions"></a>
## <a name="definitions"></a>Meghatározások

<a name="point"></a>
### <a name="point"></a>Pont

|Name (Név)|Leírás|Séma|
|---|---|---|
|**Időbélyeg**  <br>*optional*|Az adatpont jelölő időbélyegző. Ellenőrizze, hogy azt a éjfél és a egy UTC szerinti dátum-idő karakterláncot, például használata igazítja 2017-08-01T00:00:00Z.|karakterlánc (dátum-idő)|
|**Érték**  <br>*optional*|Mérték értéke.|szám (kétirányú)|


<a name="request"></a>
### <a name="request"></a>Kérés

|Name (Név)|Leírás|Séma|
|---|---|---|
|**Időszak**  <br>*optional*|Az adatpont az időtartam. Ha az érték null értékű vagy nincs jelen, az API automatikusan a időszak határozza meg.|szám (kétirányú)|
|**Pontok**  <br>*optional*|Az idősorozat-adatok mutat. Az adatok jelennek meg megfelelően az anomáliadetektálási eredmény növekvő-tárhelyek időbélyegző szerint. Az adatok nem megfelelően van rendezve, vagy ismétlődő időbélyeg, ha az API-t észlelni fogja az anomáliadetektálási pontok megfelelően, de sikerült nem jól egyezik a pontokat, a bemeneti hibát adott vissza. Ebben az esetben egy figyelmeztető üzenet bekerül a válaszban.|< [pont](#point) > tömb|


<a name="response"></a>
### <a name="response"></a>Válasz

|Name (Név)|Leírás|Séma|
|---|---|---|
|**ExpectedValues**  <br>*optional*|Az előre jelzett érték szerint a tanulási modell alapján. Ha a bemeneti adatok pontok növekvő-tárhelyek időbélyegző szerint vannak rendezve, az index a tömb való leképezéséhez a várt érték és az eredeti érték használható.|< szám (dupla) > tömb|
|**IsAnomaly**  <br>*optional*|Az eredmény, hogy-e az adatpontok rendellenességek vagy nem mindkét irányban (csúcsok és). Igaz értéket azt jelenti, hogy a pont anomáliadetektálási, false azt jelenti, hogy a pontot nem anomáliadetektálási. Ha a bemeneti adatok pontok növekvő-tárhelyek időbélyegző szerint vannak rendezve, az index a tömb való leképezéséhez a várt érték és az eredeti érték használható.|< boolean > tömb|
|**IsAnomaly_Neg**  <br>*optional*|Az eredmény a az adatpontok-e negatív iránya (DIP) a rendellenességeket. Igaz azt jelenti, hogy az anomáliadetektálási irányának értéke negatív. Ha a bemeneti adatok pontok növekvő-tárhelyek időbélyegző szerint vannak rendezve, az index a tömb való leképezéséhez a várt érték és az eredeti érték használható.|< boolean > tömb|
|**IsAnomaly_Pos**  <br>*optional*|Az adatpontok-e rendellenességek pozitív irányba (ugrásszerűen) eredménye. Igaz azt jelenti, hogy az anomáliadetektálási irányának pozitív. Ha a bemeneti adatok pontok növekvő-tárhelyek időbélyegző szerint vannak rendezve, az index a tömb való leképezéséhez a várt és eredeti érték használható.|< boolean > tömb|
|**LowerMargin**  <br>*optional*|(ExpectedValue - LowerMargin) meghatározza, hogy az alsó határ értékének adatpont van továbbra is úgy Gondoltuk szokásos módon. Ha a bemeneti adatok pontok növekvő-tárhelyek időbélyegző szerint vannak rendezve, az index a tömb való leképezéséhez a várt érték és az eredeti érték használható.|< szám (dupla) > tömb|
|**Időszak**  <br>*optional*|Az időszak, amely az API-t az anomáliadetektálási pontok észleléséhez használt.|szám (float)|
|**UpperMargin**  <br>*optional*|ExpectedValue és UpperMargin összege határozza meg, hogy a felső határa, amely adatpont van továbbra is úgy Gondoltuk szokásos módon. Ha a bemeneti adatok pontok növekvő-tárhelyek időbélyegző szerint vannak rendezve, az index a tömb való leképezéséhez a várt érték és az eredeti érték használható.|< szám (dupla) > tömb|
|**WarningText**  <br>*optional*|Ha a megadott bemeneti adatok pontok nem követi a szabályt, amely az API-t igényel, és az adatok továbbra is észlelik az API-t, az API az adatok elemzését, és fűzze hozzá a figyelmeztető információkat ebben a mezőben.|sztring|



