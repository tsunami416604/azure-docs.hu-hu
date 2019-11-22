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
ms.openlocfilehash: a4c911fa077c2ec332974fb2f0c5abcdef21307c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284120"
---
## <a name="general-entity-types"></a>Általános entitások típusai:

### <a name="person"></a>Személy
Felismert nevek és más személyek a szövegben.
Nyelvek
* Nyilvános előzetes verzió: `English`

| Altípus neve | Leírás             |
|--------------|-------------------------|
| N/A          | Felismert nevek, például `Bill Gates`, `Marie Curie` |

### <a name="location"></a>Hely

Természetes és emberi által készített tereptárgyak, struktúrák és földrajzi funkciók.

Nyelvek


* Nyilvános előzetes verzió: `English`

| Altípus neve | Leírás                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N/A          | helyszínek, például `Atlantic Ocean`, `library`, `Eiffel Tower`, `Statue of Liberty` |

### <a name="organization"></a>Szervezet  

Elismert szervezetek, vállalatok, ügynökségek és más csoportok. Például: vállalatok, politikai csoportok, zenei zenekarok, sport klubok, kormányzati szervek és nyilvános szervezetek. A nemzetiségek és a vallások nem szerepelnek ebben az entitás-típusban. Nyelvek 

* Nyilvános előzetes verzió: `English`

| Altípus neve | Leírás                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N/A          | szervezetek, például `Microsoft`, `NASA` `National Oceanic and Atmospheric Administration` |

### <a name="phone-number"></a>Telefonszám

Telefonszámok (csak az Egyesült államokbeli telefonszámok). 

Nyelvek


* Nyilvános előzetes verzió: `English`

| Altípus neve | Leírás                                  |
|----------|----------------------------------------------|
| N/A         | Egyesült államokbeli telefonszámok, például `(312) 555-0176`. |

### <a name="email"></a>E-mail

E-mail-cím. 

Nyelvek


* Nyilvános előzetes verzió: `English`

| Altípus neve | Leírás                                  |
|----------|----------------------------------------------|
| N/A         | E-mail-cím, például `support@contoso.com` |

### <a name="url"></a>URL-cím

Internetes URL-címek.

Nyelvek


* Nyilvános előzetes verzió: `English`

| Altípus neve | Leírás                                           |
|----------|-------------------------------------------------------|
| N/A         | Webhelyekhez tartozó URL-címek, például `https://www.bing.com`. |

###  <a name="number"></a>Szám

Számok és numerikus mennyiségek. 

Nyelvek


* Nyilvános előzetes verzió: `English`

| Altípus neve    | Példák                     |
|-------------|------------------------------|
| N/A         | `6`, `six`                   |
| Százalék  | `50%`, `fifty percent`       |
| Sorszám     | `2nd`, `second`              |
| Currency (Pénznem)    | `$10.99`, `€30.00`           |
| Dimenzió   | `10 miles`, `40 cm`          |
| Hőmérséklet | `32 degrees`, `10°C`         |
