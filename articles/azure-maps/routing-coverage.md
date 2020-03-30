---
title: Útvonal-lefedettség | Microsoft Azure Maps
description: Ebben a cikkben a Microsoft Azure Maps útválasztási lefedettséggel rendelkező régiókról olvashat.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: b59816abf0cd03450c9fc4ebb8417c62df7e3288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529391"
---
# <a name="azure-maps-routing-coverage"></a>Az Azure Maps útválasztási lefedettsége

Ez a cikk az Azure Maps útválasztáslefedettségi információkat tartalmaz. Keresési lekérdezés esetén az Azure Maps egy optimális útvonalat ad vissza Az A helyről a B helyre. Pontos utazási időt, az utazási információk élő frissítéseit és az útvonalutasításokat biztosítjuk. További keresési paramétereket is hozzáadhat, például az aktuális forgalmat, a járműtípusát és a elkerülendő feltételeket. Az útvonal optimalizálása a régiótól függ. Ennek az az oka, hogy az Azure Maps különböző szintű információkkal és pontossággal rendelkezik a különböző régiókban. Az alábbi táblázat felsorolja a régiókat és azt, hogy milyen típusú információkat kérhet tőlük.
 
Nézze meg lefedettség [**geokódolás**](geocoding-coverage.md).       
Nézze meg lefedettség [**Forgalom**](traffic-coverage.md).  
Nézze meg lefedettség [**Render**](render-coverage.md).
<br>  
**Legenda**

| Szimbólum             | Jelentés                                |
|--------------------|----------------------------------------|
| ✓                  | Ország/régió részletes adatokkal ellátva.   |
| Ø                  | Egyszerűsített adatokkal ellátott ország/régió. |
| Az ország hiányzik | Ország-/régióadatok nem állnak rendelkezésre.          |

<br>  

Az alábbi táblázat az Azure Maps útválasztásának lefedettségi adatait tartalmazza.
<br>  


## <a name="africa"></a>Afrika

| Ország/régió                 | Útválasztás         | Útválasztás forgalommal | Teherautó-útvonal |
|--------------------------------|:---------------:|:--------------------:|:------------:|
| Algéria                        |        ✓       |                       |             |
| Angola                         |        ✓       |                       |             |
| Benin                          |        ✓       |                       |             |
| Botswana                       |        ✓       |                       |             |
| Burkina Faso                   |        ✓       |                       |             |
| Burundi                        |        ✓       |                       |             |
| Kamerun                       |        ✓       |                       |             |
| Cabo Verde                     |        ✓       |                       |             |
| Kongó- Brazzaville Kinshasa    |        ✓       |                       |             |
| Kongó- Kinshasa                |        ✓       |                       |             |
| elefántcsontpart                  |        ✓       |                       |             |
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
| Dél-afrikai Köztársaság                   |        ✓       ||         ✓            |             |
| Szváziföld                      |        ✓       |                       |             |
| Tanzánia                       |        ✓       |                       |             |
| Togo                           |        ✓       |                       |             |
| Tunézia                        |        ✓       |                       |             |
| Uganda                         |        ✓       |                       |             |
| Zambia                         |        ✓       |                       |             |
| Zimbabwe                       |        ✓       |                       |             |


## <a name="americas"></a>Amerika

| Ország/régió                 | Útválasztás         | Útválasztás forgalommal | Teherautó-útvonal |
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
| St. Kitts és Nevis            |        ✓       |                      |             |
| Szent Lucia                      |        ✓       |                      |             |
| Szent Vincent & Grenadine-szigetek       |        ✓       |                      |             |
| Suriname                       |        ✓       |                      |             |
| Trinidad & Tobagó              |        ✓       |                      |             |
| Egyesült Államok                  |        ✓       |         ✓            |     ✓      | 
| +Amerikai Szamoa                |        ✓       |                      |             |
| +Északi Mariana-szigetek      |        ✓       |                      |             |
| +Puerto Rico                   |        ✓       |                      |             |
| +Amerikai Virgin-szigetek           |        ✓       |                      |             |
| Uruguay                        |        ✓       |         ✓            |     ✓      | 
| Venezuela                      |        ✓       |                      |             |


## <a name="asia-pacific"></a>Ázsia és a Csendes-óceáni térség

| Ország/régió                 | Útválasztás         | Útválasztás forgalommal | Teherautó-útvonal |
|--------------------------------|:---------------:|:--------------------:|:------------:|
| Ausztrália                      |        ✓       |         ✓            |     ✓       |
| +Karácsony-sziget              |        ✓       |                      |             |
| Brunei                         |        ✓       |         ✓            |             |
| Kambodzsa                       |        ✓       |                      |             |
| Fidzsi                           |        ✓       |                      |             |
| Guam                           |        ✓       |                      |             |
| Hongkong (KKT)                  |        ✓       |         ✓            |             |
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

| Ország/régió                 | Útválasztás         | Útválasztás forgalommal | Teherautó-útvonal |
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
| Cseh Köztársaság                 |        ✓       |         ✓            |     ✓      |
| Dánia                        |        ✓       |         ✓            |     ✓      |
| +Feröer-szigetek                 |        ✓       |                      |             |
| Észtország                        |        ✓       |         ✓            |     ✓      |
| Finnország                        |        ✓       |         ✓            |     ✓      |
| Franciaország                         |        ✓       |         ✓            |     ✓      | 
| +Guadeloupe                    |        ✓       |                      |             |
| +Martinique                    |        ✓       |                      |             |
| +Szent Barthélemy                |        ✓       |                      |             |
| +Szent Márton                    |        ✓       |                      |             |
| +St. Pierre & Miquelon         |        ✓       |                      |             |
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
| +Aruba                         |        ✓       |                      |             | 
| +Karib-térség Hollandia         |        ✓       |                      |             |
| +Curaçao                       |        ✓       |                      |             |
| +Sint Maarten                  |        ✓       |                      |             |
| Norvégia                         |        ✓       |         ✓            |     ✓      | 
| Lengyelország                         |        ✓       |         ✓            |     ✓      | 
| Portugália                       |        ✓       |         ✓            |     ✓      |  
| Románia                        |        ✓       |         ✓            |     ✓      | 
| Orosz Föderáció             |        ✓       |         ✓            |     ✓      | 
| San Marino                     |        ✓       |         ✓            |             |
| Szerbia                         |        ✓       |                      |     ✓      | 
| Szlovákia                       |        ✓       |         ✓            |     ✓      | 
| Szlovénia                       |        ✓       |         ✓            |     ✓      |
| Spanyolország                          |        ✓       |         ✓            |     ✓      | 
| Svédország                         |        ✓       |         ✓            |     ✓      | 
| Svájc                    |        ✓       |         ✓            |     ✓      | 
| Törökország                         |        ✓       |         ✓            |     ✓      | 
| Ukrajna                        |        ✓       |         ✓            |             | 
| Egyesült Királyság                 |        ✓       |         ✓            |     ✓      | 
| +Anguilla                      |        ✓       |                      |             |
| +Bermudák                       |        ✓       |                      |             |
| +Brit Virgin-szigetek        |        ✓       |                      |             |
| +Kajmán-szigetek                |        ✓       |                      |             |
| +Gibraltár                     |        ✓       |         ✓            |             | 
| +Montserrat                    |        ✓       |                      |             |
| +Turks- és Caicos-szigetek      |        ✓       |                      |             |
| Vatikán                   |        ✓       |         ✓            |             |
| Man-sziget                    |        ✓       |                      |             |


## <a name="middle-east"></a>Közel-Kelet

| Ország/régió                 | Útválasztás         | Útválasztás forgalommal | Teherautó-útvonal |
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


## <a name="next-steps"></a>További lépések

Az Azure Maps útválasztásról további információt az [Útválasztási](https://docs.microsoft.com/rest/api/maps/route) referencialapok című témakörben talál.
