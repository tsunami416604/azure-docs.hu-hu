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
ms.openlocfilehash: 693a81cfb15407541311d7ab053bb2ab6a267b29
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799907"
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

Telefonszámok. 

Nyelvek


* Nyilvános előzetes verzió: `English`

| Altípus neve | Leírás                                  |
|----------|----------------------------------------------|
| N/A         | Telefonszámok, például `+1 123-123-123`. |

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
