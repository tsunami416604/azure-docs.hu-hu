---
title: Útválasztási lefedettség a Azure Mapsban | Microsoft Docs
description: További tudnivalók az útválasztási lefedettségről Azure Maps
author: jingjing-z
ms.author: jinzh
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 4ca837d871dc439cadd1ca5b69bf9c7a5d12fddc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75408356"
---
# <a name="azure-maps-routing-coverage"></a>Útválasztási lefedettség Azure Maps

Ez a cikk a Azure Maps útválasztásának lefedettségi információit tartalmazza. Ha az A helyről a B helyre keres optimális útvonalat, Azure Maps a pontos utazási idő, az élő frissített utazási információk és az útvonal-utasítások megadását is lehetővé teszi. Az útvonal figyelembe veszi a további keresési kifejezéseket, például a jelenlegi forgalmat, a jármű típusát és a kerülendő feltételeket. Az útvonal optimalizálásának lehetősége a régiótól függ, mivel Azure Maps különböző szintű információkkal és pontossággal rendelkezik a különböző régiókban. A következő táblázat felsorolja a régiókat és a rájuk vonatkozó információkat.
 
Tekintse meg a [**helymeghatározáshoz**](geocoding-coverage.md)lefedettségét.       
Tekintse meg a [**forgalom**](traffic-coverage.md)lefedettségét.  
Tekintse meg a [**rendering**](render-coverage.md)lefedettségét.
<br>  
**Jelmagyarázat**

| Szimbólum             | Jelentés                                |
|--------------------|----------------------------------------|
| ✓                  | Részletes információkkal ellátott ország/régió.   |
| Ø                  | Egyszerűsített adattal rendelkező ország/régió. |
| Hiányzik az ország | Nincs megadva az ország/régió információ.          |

<br>  

A következő táblázat a Azure Maps útválasztásának lefedettségi információit tartalmazza.
<br>  


## <a name="africa"></a>Afrika

| Ország/régió                 | Útvonaltervezés         | Útválasztás forgalommal | Tehergépkocsi-Útválasztás |
|--------------------------------|:---------------:|:--------------------:|:------------:|
| Algéria                        |        ✓       |                       |             |
| Angola                         |        ✓       |                       |             |
| Benin                          |        ✓       |                       |             |
| Botswana                       |        ✓       |                       |             |
| Burkina Faso                   |        ✓       |                       |             |
| Burundi                        |        ✓       |                       |             |
| Kamerun                       |        ✓       |                       |             |
| Cabo Verde                     |        ✓       |                       |             |
| Kongói Demokratikus Köztársaság – Brazzaville Kinshasa    |        ✓       |                       |             |
| Kongói Köztársaság – Kinshasa                |        ✓       |                       |             |
| elefántcsontparti                  |        ✓       |                       |             |
| Egyiptom                          |        ✓       |         ✓            |             |
| Gabon                          |        ✓       |                       |             |
| Gambia                         |        ✓       |                       |             |
| Ghána                          |        ✓       |                       |             |
| Kenya                          |        ✓       |         ✓            |             |
| Lesotho                        |        Ø        |         ✓            |             |
| Malawi                         |        ✓       |                       |              |
| Mali                           |        ✓       |                       |             |
| Mauritánia                     |        ✓       |                       |             |
| Mauritius, Mayotte és Réunion |        ✓      |                       |             |
| Marokkó                        |        ✓       |                       |             |
| Mozambik                     |        ✓       |         ✓            |             |
| Namíbia                        |        ✓       |                       |             |
| Niger                          |        ✓       |                       |             |
| Nigéria                        |        ✓       |         ✓            |             |
| Ruanda                         |        ✓       |                       |             |
| Szenegál                        |        ✓       |                       |             |
| Seychelle-szigetek                     |        ✓       |                       |             |
| Dél-Afrika                   |        ✓       ||         ✓            |             |
| Szváziföld                      |        ✓       |                       |             |
| Tanzánia                       |        ✓       |                       |             |
| Togo                           |        ✓       |                       |             |
| Tunézia                        |        ✓       |                       |             |
| Uganda                         |        ✓       |                       |             |
| Zambia                         |        ✓       |                       |             |
| Zimbabwe                       |        ✓       |                       |             |


## <a name="americas"></a>Észak-, Dél- és Közép-Amerika

| Ország/régió                 | Útvonaltervezés         | Útválasztás forgalommal | Tehergépkocsi-Útválasztás |
|--------------------------------|:---------------:|:--------------------:|:------------:|
| Argentína                      |        ✓       |         ✓            |     ✓       |
| Antigua és Barbuda            |        ✓       |                      |             |
| Bahama-szigetek                        |        ✓       |                      |             |
| Barbados                       |        ✓       |                      |             |
| Belize                         |        ✓       |                      |             |
| Brazília                         |        ✓       |         ✓            |     ✓       | 
| Kanada                         |        ✓       |         ✓            |     ✓      |
| Chile                          |        ✓       |         ✓            |     ✓      |
| Kolumbia                       |        ✓       |         ✓            |             |
| Costa Rica                     |        ✓       |                      |             |
| Kuba                           |        ✓       |                      |             |
| Dominika                       |        ✓       |                      |             |
| Dominikai Köztársaság             |        ✓       |                      |             |
| Ecuador                        |        ✓       |                      |             |
| Salvador                    |        ✓       |                      |             |
| Francia Guyana                  |        ✓       |                      |             |
| Grenada                        |        ✓       |                      |             |
| Guatemala                      |        ✓       |                      |             |
| Guyana                         |        ✓       |                      |             |
| Haiti                          |        ✓       |                      |             |
| Honduras                       |        ✓       |                      |             |
| Jamaica                        |        ✓       |                      |             |
| Mexikó                         |        ✓       |         ✓            |     ✓      | 
| Nicaragua                      |        ✓       |                      |             |
| Panama                         |        ✓       |                      |             |
| Paraguay                       |        ✓       |                      |             |
| Peru                           |        ✓       |         ✓            |             |
| Saint Kitts és Nevis            |        ✓       |                      |             |
| St. Lucia                      |        ✓       |                      |             |
| Saint Vincent & Grenadine-szigetek       |        ✓       |                      |             |
| Suriname                       |        ✓       |                      |             |
| Trinidad & Tobago              |        ✓       |                      |             |
| Egyesült Államok                  |        ✓       |         ✓            |     ✓      | 
| \+ Amerikai Szamoa                |        ✓       |                      |             |
| \+ Északi Mariana-szigetek      |        ✓       |                      |             |
| \+ Puerto Rico                   |        ✓       |                      |             |
| \+ Amerikai Virgin-szigetek           |        ✓       |                      |             |
| Uruguay                        |        ✓       |         ✓            |     ✓      | 
| Venezuela                      |        ✓       |                      |             |


## <a name="asia-pacific"></a>Ázsia és a Csendes-óceáni térség

| Ország/régió                 | Útvonaltervezés         | Útválasztás forgalommal | Tehergépkocsi-Útválasztás |
|--------------------------------|:---------------:|:--------------------:|:------------:|
| Ausztrália                      |        ✓       |         ✓            |     ✓       |
| \+ Karácsony-sziget              |        ✓       |                      |             |
| Brunei                         |        ✓       |         ✓            |             |
| Kambodzsa                       |        ✓       |                      |             |
| Fidzsi                           |        ✓       |                      |             |
| Guam                           |        ✓       |                      |             |
| Hongkong KKT                  |        ✓       |         ✓            |             |
| India                          |        ✓       |         ✓            |             |
| Indonézia                      |        ✓       |         ✓            |             |
| Kazahsztán                |        ✓       |                     |             |
| Dél-Korea                          |        Ø       |                      |             |
| Laosz                           |        ✓       |                      |             |
| Makaó (KKT)                      |        ✓       |         ✓            |             |
| Malajzia                       |        ✓       |         ✓            |             |
| Mianmar                        |        ✓       |                      |             |
| Új-Zéland                    |        ✓       |         ✓            |     ✓       | 
| Fülöp-szigetek                    |        ✓       |         ✓            |             |
| Szingapúr                      |        ✓       |         ✓            |             |
| Tajvan                         |        ✓       |         ✓            |             |
| Thaiföld                       |        ✓       |         ✓            |             |
| Vietnam                        |        ✓       |         ✓            |             |


## <a name="europe"></a>Európa

| Ország/régió                 | Útvonaltervezés         | Útválasztás forgalommal | Tehergépkocsi-Útválasztás |
|--------------------------------|:---------------:|:--------------------:|:------------:|
| Albánia                        |        ✓       |                      |             |
| Andorra                        |        ✓       |         ✓            |             |
| Ausztria                        |        ✓       |         ✓            |     ✓       | 
| Belarusz                        |        ✓       |                      |             |
| Belgium                        |        ✓       |         ✓            |     ✓      | 
| Bolívia                        |        ✓       |                      |             |
| Bosznia-Hercegovina             |        ✓       |                      |             |
| Bulgária                       |        ✓       |         ✓            |     ✓      | 
| Horvátország                        |        ✓       |         ✓            |     ✓      |
| Ciprus                         |        ✓       |                      |             |
| Csehország                 |        ✓       |         ✓            |     ✓      |
| Dánia                        |        ✓       |         ✓            |     ✓      |
| \+ Feröer-szigetek                 |        ✓       |                      |             |
| Észtország                        |        ✓       |         ✓            |     ✓      |
| Finnország                        |        ✓       |         ✓            |     ✓      |
| Franciaország                         |        ✓       |         ✓            |     ✓      | 
| \+ Guadeloupe                    |        ✓       |                      |             |
| \+ Martinique                    |        ✓       |                      |             |
| \+ St. Barthélemy                |        ✓       |                      |             |
| \+ St. Martin                    |        ✓       |                      |             |
| \+ St. Pierre & Miquelon         |        ✓       |                      |             |
| Grúzia                        |        ✓       |                      |             |
| Németország                        |        ✓       |         ✓            |     ✓      | 
| Görögország                         |        ✓       |         ✓            |     ✓      |  
| Guernsey                       |        ✓       |                      |             |
| Magyarország                        |        ✓       |         ✓            |     ✓      | 
| Izland                        |        ✓       |         ✓            |             |
| Írország                        |        ✓       |         ✓            |     ✓      | 
| Olaszország                          |        ✓       |         ✓            |     ✓      | 
| Jersey                         |        ✓       |                      |             |
| Lettország                         |        ✓       |         ✓            |     ✓      | 
| Liechtenstein                  |        ✓       |         ✓            |             |
| Litvánia                      |        ✓       |         ✓            |     ✓      | 
| Luxemburg                     |        ✓       |         ✓            |     ✓      | 
| Észak-Macedónia                |        ✓       |                      |             |
| Málta                          |        ✓       |         ✓            |             |
| Moldova                        |        ✓       |                      |             |
| Monaco                         |        ✓       |         ✓            |             |
| Montenegró                     |        ✓       |                      |     ✓      | 
| Hollandia                    |        ✓       |         ✓            |     ✓      | 
| \+ Aruba                         |        ✓       |                      |             | 
| \+ Caribbean holland         |        ✓       |                      |             |
| \+ Curaçao                       |        ✓       |                      |             |
| \+ Sint Maarten                  |        ✓       |                      |             |
| Norvégia                         |        ✓       |         ✓            |     ✓      | 
| Lengyelország                         |        ✓       |         ✓            |     ✓      | 
| Portugália                       |        ✓       |         ✓            |     ✓      |  
| Románia                        |        ✓       |         ✓            |     ✓      | 
| Orosz Föderáció             |        ✓       |         ✓            |     ✓      | 
| San Marino                     |        ✓       |         ✓            |             |
| \+ Sint Maarten                  |        ✓       |                      |             |
| Szerbia                         |        ✓       |                      |     ✓      | 
| Szlovákia                       |        ✓       |         ✓            |     ✓      | 
| Szlovénia                       |        ✓       |         ✓            |     ✓      |
| Spanyolország                          |        ✓       |         ✓            |     ✓      | 
| Svédország                         |        ✓       |         ✓            |     ✓      | 
| Svájc                    |        ✓       |         ✓            |     ✓      | 
| Törökország                         |        ✓       |         ✓            |     ✓      | 
| Ukrajna                        |        ✓       |         ✓            |             | 
| Egyesült Királyság                 |        ✓       |         ✓            |     ✓      | 
| \+ Anguilla                      |        ✓       |                      |             |
| \+ Bermuda                       |        ✓       |                      |             |
| \+ Brit Virgin-szigetek        |        ✓       |                      |             |
| \+ Kajmán-szigetek                |        ✓       |                      |             |
| \+ Gibraltár                     |        ✓       |         ✓            |             | 
| \+ Montserrat                    |        ✓       |                      |             |
| \+ Turks-és Caicos-szigetek      |        ✓       |                      |             |
| Vatikán                   |        ✓       |         ✓            |             |
| Man-sziget                    |        ✓       |                      |             |


## <a name="middle-east"></a>Közel-Kelet

| Ország/régió                 | Útvonaltervezés         | Útválasztás forgalommal | Tehergépkocsi-Útválasztás |
|--------------------------------|:---------------:|:--------------------:|:------------:|
| Bahrein                        |        ✓       |         ✓            |             |
| Irak                           |        ✓       |                      |             |
| Izrael                         |        ✓       |         ✓            |             |
| Jordánia                         |        ✓       |                      |             |
| Kuvait                         |        ✓       |         ✓            |             |
| Libanon                        |        ✓       |                      |             |
| Omán                           |        ✓       |         ✓            |             |
| Katar                          |        ✓       |         ✓            |             |
| Szaúd-Arábia                   |        ✓       |         ✓            |             |
| Egyesült Arab Emírségek           |        ✓       |         ✓            |             |
| Jemen                          |        ✓       |                      |             |


## <a name="next-steps"></a>Következő lépések

Azure Maps útválasztással kapcsolatos további információkért tekintse meg az [útválasztási](https://docs.microsoft.com/rest/api/maps/route) hivatkozási lapokat.
