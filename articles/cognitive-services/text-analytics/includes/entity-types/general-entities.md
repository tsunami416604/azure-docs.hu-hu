---
title: Általános névvel ellátott entitások
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: ba516a548fc8385ca86526a7f2dd082b27e53208
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77211408"
---
## <a name="general-entity-types"></a>Általános entitások típusai:

### <a name="person"></a>Személy

Nevek felismerése szövegben.

Nyelvek:
* Nyilvános előnézet: `Arabic`, `Czech` `Chinese-Simplified` `Danish` `Dutch` `English` `Finnish` `French` `German` `Swedish` ,, `Spanish`,,,,,,,,,,,,,,,, és `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian``Turkish`

| Altípus neve | Leírás                                                      | A modell verziójától kezdődően érhető el |
|--------------|------------------------------------------------------------------|---------------------------------------|
| N/A          | Felismert személyek neve, például `Bill Gates`:`Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>PersonType
Egy személy által birtokolt feladat típusa vagy szerepköre.

Nyelvek:
* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                                                                | A modell verziójától kezdődően érhető el |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| N/A          | Feladattípusok például `civil engineer` `salesperson` `chef`:,, `librarian`,`nursing aide` | `2020-02-01`                           |

### <a name="location"></a>Hely

Természetes és emberi által készített tereptárgyak, struktúrák, földrajzi funkciók és geopolitikai entitások.

Nyelvek:

* Nyilvános előnézet: `Arabic`, `Czech` `Chinese-Simplified` `Danish` `Dutch` `English` `Finnish` `French` `German` `Swedish` ,, `Spanish`,,,,,,,,,,,,,,,, és `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian``Turkish`

| Altípus neve              | Leírás                                                                              | A modell verziójától kezdődően érhető el |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| N/A                       | helyszínek, például `Atlantic Ocean` `library`:, `Eiffel Tower`,`Statue of Liberty`  | `2019-10-01`                           |
| Geopolitikai entitás (GPE) – csak angol nyelven| Városok, országok, Államok, például `Seattle` `Pennsylvania` `South Africa`:,,`Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>Szervezet  

Elismert szervezetek, vállalatok, ügynökségek és más csoportok. Például: vállalatok, politikai csoportok, zenei zenekarok, sport klubok, kormányzati szervek és nyilvános szervezetek. A nemzetiségek és a vallások nem szerepelnek ebben az entitás-típusban. 

Nyelvek: 

* Nyilvános előnézet: `Arabic`, `Czech` `Chinese-Simplified` `Danish` `Dutch` `English` `Finnish` `French` `German` `Swedish` ,, `Spanish`,,,,,,,,,,,,,,,, és `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian``Turkish`

| Altípus neve | Leírás                                                                                             | A modell verziójától kezdődően érhető el |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| N/A          | szervezetek, például `Microsoft` `NASA` `National Oceanic and Atmospheric Administration`:,,`VOA` | `2019-10-01`                           |

### <a name="event"></a>Esemény  

Történelmi, társadalmi és természeti események.  

Nyelvek: 

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                                            | A modell verziójától kezdődően érhető el |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| N/A          | Események `wedding`, például, `hurricane` `car accident`,, `solar eclipse`,`American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Product  

Különböző kategóriák fizikai objektumai.  

Nyelvek: 

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                                                        | A modell verziójától kezdődően érhető el |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| N/A          | Például `Microsoft Surface laptop` `sunglasses`:,, `motorcycle`,, `bag``Xbox` | `2020-02-01`                           |
| Számítás    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>Ügyességi  

Egy képességet vagy szakértelmet leíró entitás.  

Nyelvek: 

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                                                 | A modell verziójától kezdődően érhető el |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| N/A          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>Telefonszám

Telefonszámok (csak az Egyesült államokbeli telefonszámok). 

Nyelvek:

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                    | A modell verziójától kezdődően érhető el |
|--------------|------------------------------------------------|----------------------------------------|
| N/A          | Egyesült államokbeli telefonszámok, például`(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>E-mail

E-mail-cím. 

Nyelvek:

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                      | A modell verziójától kezdődően érhető el |
|--------------|--------------------------------------------------|----------------------------------------|
| N/A          | E-mail-cím, például:`support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>URL-cím

Internetes URL-címek.

Nyelvek:

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                          | A modell verziójától kezdődően érhető el |
|--------------|------------------------------------------------------|----------------------------------------|
| N/A          | Webhelyek URL-címei, például`https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>IP-cím

Internet Protocol címe

Nyelvek:

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                              | A modell verziójától kezdődően érhető el |
|--------------|------------------------------------------|----------------------------------------|
| N/A          | Példa hálózati címekre`10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

Dátum és idő entitások. 

* A modell verziójától kezdődően érhető el`2019-10-01`

Nyelvek:

* Nyilvános előzetes verzió `Chinese-Simplified`: `English` `French`,, `German` , és`Spanish`

| Altípus neve    | Példák                     |
|-------------|------------------------------|
| N/A         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Dátum  | `May 2nd, 2017`, `05/02/2017`       |
| Time     | `8:15`, `6AM`              |
| DateRange    | `August 2nd to August 5th`         |
| TimeRange   | `4-6PM`, `10:00AM to Noon`          |
| Időtartam | `2.5 minutes`, `one and a half hours`         |
| Beállítás | `every Saturday`         |

###  <a name="quantity"></a>Mennyiség

Számok és numerikus mennyiségek. 

* A modell verziójától kezdődően érhető el`2019-10-01`

Nyelvek:

* Nyilvános előzetes verzió `Chinese-Simplified`: `English` `French`,, `German` , és`Spanish`

| Altípus neve    | Példák                     |
|-------------|------------------------------|
| Szám         | `6`, `six`                   |
| Százalék  | `50%`, `fifty percent`       |
| Sorszámok     | `2nd`, `second`              |
| Kor         | `90 day old`, `30 years old` |
| Currency (Pénznem)    | `$10.99`, `€30.00`           |
| Dimenzió   | `10 miles`, `40 cm`          |
| Hőmérséklet | `32 degrees`, `10°C`         |
