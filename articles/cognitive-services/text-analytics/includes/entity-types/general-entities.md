---
title: Általánosan elnevezett entitások
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: ba516a548fc8385ca86526a7f2dd082b27e53208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77211408"
---
## <a name="general-entity-types"></a>Általános entitástípusok:

### <a name="person"></a>Személy

Személyek nevének felismerése a szövegben.

Nyelvek:
* Nyilvános `Arabic`előnézet: `Czech` `Chinese-Simplified`, `Danish` `Dutch`, `English` `Finnish`, `French` `German`, `Hungarian` `Italian` `Japanese` `Korean`, , `Norwegian (Bokmål)` `Polish`, `Portuguese (Portugal)` `Portuguese (Brazil)`, `Russian` `Spanish`, `Swedish` , , , , , , , , , , és`Turkish`

| Altípus neve | Leírás                                                      | Modellverzióval kezdve elérhető |
|--------------|------------------------------------------------------------------|---------------------------------------|
| N/A          | Elismert személynevek, `Bill Gates`például ,`Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>Személytípus
Egy személy által betöltött feladattípus vagy szerepkör.

Nyelvek:
* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                                                                | Modellverzióval kezdve elérhető |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| N/A          | Feladattípusok, `civil engineer`például `chef` `librarian`, `salesperson`, ,`nursing aide` | `2020-02-01`                           |

### <a name="location"></a>Hely

Természeti és emberi gyártmányú tereptárgyak, struktúrák, földrajzi jellemzők és geopolitikai entitások.

Nyelvek:

* Nyilvános `Arabic`előnézet: `Czech` `Chinese-Simplified`, `Danish` `Dutch`, `English` `Finnish`, `French` `German`, `Hungarian` `Italian` `Japanese` `Korean`, , `Norwegian (Bokmål)` `Polish`, `Portuguese (Portugal)` `Portuguese (Brazil)`, `Russian` `Spanish`, `Swedish` , , , , , , , , , , és`Turkish`

| Altípus neve              | Leírás                                                                              | Modellverzióval kezdve elérhető |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| N/A                       | helyeken, `Atlantic Ocean`például `library` `Eiffel Tower`, , ,`Statue of Liberty`  | `2019-10-01`                           |
| Geopolitikai Entitás (GPE) - csak angol| Városok, országok, államok, `Pennsylvania` `South Africa`például `Seattle`, ,`Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>Szervezet  

Elismert szervezetek, vállalatok, ügynökségek és más embercsoportok. Például: vállalatok, politikai csoportok, zenei zenekarok, sportklubok, kormányzati szervek és állami szervezetek. A nemzetiségek és vallások nem tartoznak ebbe a jogalanytípusba. 

Nyelvek: 

* Nyilvános `Arabic`előnézet: `Czech` `Chinese-Simplified`, `Danish` `Dutch`, `English` `Finnish`, `French` `German`, `Hungarian` `Italian` `Japanese` `Korean`, , `Norwegian (Bokmål)` `Polish`, `Portuguese (Portugal)` `Portuguese (Brazil)`, `Russian` `Spanish`, `Swedish` , , , , , , , , , , és`Turkish`

| Altípus neve | Leírás                                                                                             | Modellverzióval kezdve elérhető |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| N/A          | szervezetek, például `Microsoft` `NASA`, `National Oceanic and Atmospheric Administration`,`VOA` | `2019-10-01`                           |

### <a name="event"></a>Esemény  

Történelmi, társadalmi és természetes események.  

Nyelvek: 

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                                            | Modellverzióval kezdve elérhető |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| N/A          | események, `wedding`mint `hurricane` `car accident`például , , , `solar eclipse``American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Product  

Különböző kategóriájú fizikai tárgyak.  

Nyelvek: 

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                                                        | Modellverzióval kezdve elérhető |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| N/A          | Például, `Microsoft Surface laptop` `sunglasses`, `motorcycle` `bag`, ,`Xbox` | `2020-02-01`                           |
| Számítás    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>Ügyességi  

Egy képességet vagy szakértelmet leíró entitás.  

Nyelvek: 

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                                                 | Modellverzióval kezdve elérhető |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| N/A          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>Telefonszám

Telefonszámok (csak amerikai telefonszámok). 

Nyelvek:

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                    | Modellverzióval kezdve elérhető |
|--------------|------------------------------------------------|----------------------------------------|
| N/A          | Amerikai telefonszámok, például`(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>E-mail

E-mail-cím. 

Nyelvek:

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                      | Modellverzióval kezdve elérhető |
|--------------|--------------------------------------------------|----------------------------------------|
| N/A          | E-mail cím, például`support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>URL-cím

internetes URL-eket.

Nyelvek:

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                          | Modellverzióval kezdve elérhető |
|--------------|------------------------------------------------------|----------------------------------------|
| N/A          | URL-címek a weboldalak, például`https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>IP-cím

IP-cím

Nyelvek:

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                              | Modellverzióval kezdve elérhető |
|--------------|------------------------------------------|----------------------------------------|
| N/A          | Hálózati cím például`10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

Dátum és idő entitások. 

* Modellverzióval kezdve elérhető`2019-10-01`

Nyelvek:

* Nyilvános előzetes `Chinese-Simplified` `English`verzió: , , `French`, `German` és`Spanish`

| Altípus neve    | Példák                     |
|-------------|------------------------------|
| N/A         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Dátum  | `May 2nd, 2017`, `05/02/2017`       |
| Time     | `8:15`, `6AM`              |
| Dátumtartomány    | `August 2nd to August 5th`         |
| Időtartomány   | `4-6PM`, `10:00AM to Noon`          |
| Időtartam | `2.5 minutes`, `one and a half hours`         |
| Beállítás | `every Saturday`         |

###  <a name="quantity"></a>Mennyiség

Számok és numerikus mennyiségek. 

* Modellverzióval kezdve elérhető`2019-10-01`

Nyelvek:

* Nyilvános előzetes `Chinese-Simplified` `English`verzió: , , `French`, `German` és`Spanish`

| Altípus neve    | Példák                     |
|-------------|------------------------------|
| Szám         | `6`, `six`                   |
| Százalék  | `50%`, `fifty percent`       |
| Ordinális     | `2nd`, `second`              |
| Kor         | `90 day old`, `30 years old` |
| Currency (Pénznem)    | `$10.99`, `€30.00`           |
| Dimenzió   | `10 miles`, `40 cm`          |
| Hőmérséklet | `32 degrees`, `10°C`         |
