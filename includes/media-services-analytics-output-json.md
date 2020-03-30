---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 065cb4daa9501ee658d364dad43b9e03798e4083
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179533"
---
A feladat létrehoz egy JSON kimeneti fájlt, amely metaadatokat tartalmaz az észlelt és nyomon követett lapokról. A metaadatok tartalmazzák az arcok helyét jelző koordinátákat, valamint egy arcazonosító számot, amely jelzi az adott személy nyomon követését. Az arcazonosító-számok olyan körülmények között állíthatók alaphelyzetbe, amikor az elülső felület elvész vagy átfedi egymást a keretben, ami azt eredményezi, hogy egyes személyek több azonosítót kapnak.

A kimeneti JSON a következő elemeket tartalmazza:

### <a name="root-json-elements"></a>Gyökér JSON-elemek

| Elem | Leírás |
| --- | --- |
| version |Ez a videó API verziójára vonatkozik. |
| Időskála |"Kullancsok" másodpercenként a videó. |
| offset |Ez az időbélyegek időeltolása. A videoAPI-k 1.0-s verziójában ez mindig 0 lesz. Az általunk támogatott jövőbeli forgatókönyvekben ez az érték változhat. |
| szélesség, magasság |A kimeneti videoképszélesség és magasság képpontban.|
| Frameráta |Képkockák másodpercenkénti száma a videóban. |
| [Töredékek](#fragments-json-elements) |A metaadatok különböző szegmensekbe, úgynevezett töredékekbe vannak felosztva. Minden töredék tartalmaz kezdési időpontot, időtartamot, intervallumszámot és esemény(eke)t. |

### <a name="fragments-json-elements"></a>Töredékek JSON elemek

|Elem|Leírás|
|---|---|
| start |Az első esemény kezdési időpontja a "kullancsokban". |
| duration |A töredék hossza a "kullancsokban". |
| Index | (Csak az Azure Media Redactor esetén) határozza meg az aktuális esemény keretindexét. |
| interval |A töredéken belüli egyes eseménybejegyzések időköze a "ticks" mezőben. |
| események |Minden esemény tartalmazza az észlelt és nyomon követett arcokat az adott időtartamon belül. Ez egy sor esemény. A külső tömb egy időintervallumot jelöl. A belső tömb 0 vagy több eseményből áll, amelyek az adott időpontban történtek. Az üres zárójel [] azt jelenti, hogy a rendszer nem észlelt arcokat. |
| id |A nyomon követett arc azonosítója. Ez a szám véletlenül megváltozhat, ha egy arc észrevétlenné válik. Egy adott személynek ugyanazt az azonosítót kell kapnia a teljes videóban, de ez nem garantálható az észlelési algoritmus korlátai miatt (elzáródás stb.). |
| x, y |Az archatároló keret bal felső X és Y koordinátái 0,0 és 1,0 között normalizált léptékben. <br/>-Az X és Y koordináták mindig relatívak a fekvő tájoláshoz képest, így ha van egy portré videód (vagy fejjel lefelé, iOS esetén), akkor ennek megfelelően kell átültetned a koordinátákat. |
| szélesség, magasság |A laphatároló keret szélessége és magassága 0,0 és 1,0 között normalizált léptékben. |
| facesDetected |Ez a JSON-eredmények végén található, és összefoglalja az algoritmus által a videó során észlelt arcok számát. Mivel az azonosítók véletlenül visszaállíthatók, ha egy arc észrevétlenné válik (pl. az arc eltűnik a képernyőről, elnéz), ez a szám nem mindig egyezik meg a videóban szereplő arcok valódi számával. |
