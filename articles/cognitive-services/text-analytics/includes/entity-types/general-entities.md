---
title: Általános névvel ellátott entitások
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: aahi
ms.openlocfilehash: e81a1de02c112abd5c52f0f83404a615d738e01c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73508066"
---
## <a name="general-entity-types"></a>Általános entitások típusai:

### <a name="person"></a>Személy
Felismert nevek és más személyek a szövegben.
Nyelvek
* Nyilvános előzetes verzió: `English`

| Altípus neve | Leírás             |
|--------------|-------------------------|
| –          | Felismert nevek, például `Bill Gates`, `Marie Curie` |

### <a name="location"></a>Földrajzi egység

Természetes és emberi által készített tereptárgyak, struktúrák és földrajzi funkciók.

Nyelvek


* Nyilvános előzetes verzió: `English`

| Altípus neve | Leírás                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| –          | helyszínek, például `Atlantic Ocean`, `library`, `Eiffel Tower`, `Statue of Liberty` |

### <a name="organization"></a>Szervezet  

Elismert szervezetek, vállalatok, ügynökségek és más csoportok. Például: vállalatok, politikai csoportok, zenei zenekarok, sport klubok, kormányzati szervek és nyilvános szervezetek. A nemzetiségek és a vallások nem szerepelnek ebben az entitás-típusban. Nyelvek 

* Nyilvános előzetes verzió: `English`

| Altípus neve | Leírás                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| –          | szervezetek, például `Microsoft`, `NASA` `National Oceanic and Atmospheric Administration` |

### <a name="phone-number"></a>Telefonszám

Telefonszámok. 

Nyelvek


* Nyilvános előzetes verzió: `English`

| Altípus neve | Leírás                                  |
|----------|----------------------------------------------|
| –         | Telefonszámok, például `+1 123-123-123`. |

### <a name="url"></a>URL-cím

Internetes URL-címek.

Nyelvek


* Nyilvános előzetes verzió: `English`

| Altípus neve | Leírás                                           |
|----------|-------------------------------------------------------|
| –         | Webhelyekhez tartozó URL-címek, például `https://www.bing.com`. |

###  <a name="number"></a>Szám

Számok és numerikus mennyiségek. 

Nyelvek


* Nyilvános előzetes verzió: `English`

| Altípus neve    | Példák                     |
|-------------|------------------------------|
| –         | `6`, `six`                   |
| Százalék  | `50%`, `fifty percent`       |
| Sorszám     | `2nd`, `second`              |
| Currency (Pénznem)    | `$10.99`, `€30.00`           |
| Dimenzió   | `10 miles`, `40 cm`          |
| Hőmérséklet | `32 degrees`, `10°C`         |
