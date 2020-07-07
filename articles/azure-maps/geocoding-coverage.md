---
title: Helymeghatározáshoz lefedettség | Microsoft Azure térképek
description: A hely címe szélességi és hosszúsági koordinátákra történő átalakításának folyamata helymeghatározáshoz néven ismert. Ebben a cikkben megismerheti a helymeghatározáshoz-lefedettséggel rendelkező régiókat Microsoft Azure Maps-ben.
author: philmea
ms.author: philmea
ms.date: 12/31/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9836bd35b16c4c308b7c9d096b104c0cec68a34c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335656"
---
# <a name="azure-maps-geocoding-coverage"></a>Azure Maps helymeghatározáshoz lefedettsége

Ha Azure Mapskal rendelkező helyet keres, a keresési szolgáltatás, például a [keresési címek lekérése](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress), a keresési kifejezéseket veszi át, és visszaadja a szélességi és hosszúsági koordinátákat. Ezt a folyamatot helymeghatározáshoz nevezzük. Azure Maps azonban az összes régió és ország esetében nem rendelkezik azonos szintű információval és pontossággal. Ebből a cikkből megtudhatja, hogy milyen típusú helyszíneket megbízhatóan kereshet az egyes régiókban. 

Az országokban/régiókban való geocode a közúti adatlefedettségtől és a helymeghatározáshoz szolgáltatás helymeghatározáshoz pontosságtól függ. A következő kategorizálási szintek határozzák meg, hogy az egyes országokban/régiókban milyen szintű helymeghatározáshoz-támogatás van érvényben.
* **Címterület** – az adatcímek feloldhatók egy szélességi/hosszúsági koordinátákra a cím parcelláján (a tulajdonság határán) belül. Más néven "tetőtéri" pontosnak kell lennie. Ez a legmagasabb szintű pontosság a címek számára. 
* **Házszámok** – a címek az utca szélességi/hosszúsági koordinátáihoz vannak interpolált.
* **Utca szintű** – a címek a címet tartalmazó utca szélességi/hosszúsági koordinátáihoz vannak feloldva. Lehetséges, hogy a házszám nem dolgozható fel.
* A **városi szintű** nevek támogatottak.

## <a name="americas"></a>Észak-, Dél- és Közép-Amerika

| Ország/régió                                       | Címzési pontok | Házszámok | Utca szintje | Városi szint | Érdekes pontok |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Anguilla                                            |                 |                |              |      ✓     |          ✓         |
| Antarktisz                                          |                 |                |              |      ✓     |          ✓         |
| Antigua és Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentína                                           |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Bahama-szigetek                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbados                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermuda                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bolívia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Sint Eustatius és Saba                   |                 |                |              |      ✓     |          ✓         |
| Brazília                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kanada                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kajmán-szigetek                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Chile                                               |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Kolumbia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Costa Rica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Kuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominika                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Rigán                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Ecuador                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Salvador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Falkland-szigetek                                    |                 |                |              |      ✓     |          ✓         |
| Francia Guyana                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Grenada                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Guadeloupe                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Guyana                                              |                |             |           |      ✓     |                 |
| Haiti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamaica                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martinique                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mexikó                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nicaragua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panama                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paraguay                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Peru                                                |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Puerto Rico                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Saint Barthélemy                                    |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Kitts és Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Lucia                                         |                 |                |              |      ✓     |          ✓         |
| Saint Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Saint-Pierre és Miquelon                           |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Vincent és Grenadine-szigetek                    |                 |                |              |      ✓     |          ✓         |
| Sint Maarten                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Dél-Georgia és Déli-Sandwich-szigetek        |                 |                |              |      ✓     |          ✓         |
| Suriname                                            |                 |                |              |      ✓     |          ✓         |
| Trinidad és Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Egyesült Államok kisebb kifelé húzódó szigetek                |                 |                |              |      ✓     |          ✓         |
| Amerika Egyesült Államok                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguay                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Brit Virgin-szigetek                              |                 |                |              |      ✓     |          ✓         |
| Amerikai Virgin-szigetek                                 |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Ázsia és a Csendes-óceáni térség

| Ország/régió                                      | Címzési pontok |Házszámok | Utca szintje | Városi szint | Érdekes pontok |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Amerikai Szamoa                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Ausztrália                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Banglades                                          |                 |                |              |      ✓     |          ✓         |
| Bhután                                              |                 |                |              |      ✓     |          ✓         |
| Brit indiai-óceáni terület                      |                 |                |              |      ✓     |          ✓         |
| Brunei                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kambodzsa                                            |                 |                |              |      ✓     |          ✓         |
| Kína                                               |                 |                |              |      ✓     |          ✓         |
| Karácsony-sziget                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Cocos (Keeling)-szigetek                             |                 |                |              |      ✓     |          ✓         |
| Comore-szigetek                                             |                 |                |              |      ✓     |          ✓         |
| Cook-szigetek                                        |                 |                |              |      ✓     |          ✓         |
| Fidzsi                                                |                  |                |              |      ✓     |          ✓        |
| Francia Polinézia                                    |                 |                |              |      ✓     |          ✓         |
| Heard-sziget és McDonald-szigetek                   |                 |                |              |      ✓     |          ✓         |
| Hongkong (KKT)                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonézia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| India                                               |        ✓        |        ✓       |       ✓      |      ✓     |                   |
| Japán                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Dél-Korea                                         |                 |                |              |      ✓     |          ✓         |
| Laosz                                                |                 |                |              |      ✓     |          ✓         |
| Makaó (KKT)                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malajzia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mikronézia                                          |                 |                |              |      ✓     |          ✓         |
| Mongólia                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Nepál                                               |                 |                |              |      ✓     |          ✓         |
| Új-Kaledónia                                       |                 |                |              |      ✓     |          ✓         |
| Új-Zéland                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Norfolk-sziget                                      |                 |                |              |      ✓     |          ✓         |
| Észak-Korea                                         |                 |                |              |      ✓     |          ✓         |
| Északi Mariana-szigetek                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pakisztán                                            |                 |                |              |      ✓     |          ✓         |
| Palau                                               |                 |                |              |      ✓     |          ✓         |
| Pápua Új-Guinea                                    |                 |                |              |      ✓     |          ✓         |
| Fülöp-szigetek                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pitcairn-szigetek                                            |                 |                |              |      ✓     |          ✓         |
| Szamoa                                               |                 |                |              |      ✓     |          ✓         |
| Senkaku-szigetek                                     |        ✓        |                |              |      ✓     |          ✓         |
| Szingapúr                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Salamon-szigetek                                     |                 |                |              |      ✓     |          ✓         |
| Déli Kurils                                     |        ✓        |                |              |      ✓     |          ✓         |
| Srí Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Tajvan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Thaiföld                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Turks- és Caicos-szigetek                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Vietnam                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis és Futuna                                   |                 |                |              |      ✓     |          ✓         |

## <a name="europe"></a>Európa

| Ország/régió                                      | Címzési pontok |Házszámok | Utca szintje | Városi szint | Érdekes pontok |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Albánia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andorra                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Örményország                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Ausztria                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Azerbajdzsán                                          |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Belgium                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bosznia-Hercegovina                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bulgária                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Belarusz                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Horvátország                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ciprus                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Cseh Köztársaság                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Dánia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Észtország                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Feröer szigetek                                       |                 |                |              |      ✓     |          ✓         |
| Finnország                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Franciaország                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Grúzia                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Németország                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibraltár                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Görögország                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Grönland                                           |                 |                |              |      ✓     |          ✓         |
| Guernsey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Magyarország                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Izland                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Írország                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Man-sziget                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Olaszország                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kazahsztán                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Koszovó                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Kirgizisztán                                          |                 |                |              |      ✓     |          ✓         |
| Lettország                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Liechtenstein                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Litvánia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Luxemburg                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Észak-Macedónia                                     |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Málta                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Moldova                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Monaco                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Montenegró                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Hollandia                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Norvégia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Lengyelország                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portugália                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| + Azori-szigetek és Madeira                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Románia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Orosz Föderáció                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| San Marino                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Szerbia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Szlovákia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Szlovénia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Spanyolország                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Svédország                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Svájc                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tádzsikisztán                                          |                 |                |              |      ✓     |          ✓         |
| Törökország                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Türkmenisztán                                        |                 |                |              |      ✓     |          ✓         |
| Ukrajna                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Egyesült Királyság                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Üzbegisztán                                          |                 |                |              |      ✓     |          ✓         |
| Vatikán                                        |                 |                |       ✓      |      ✓     |          ✓         |


## <a name="middle-east-and-africa"></a>Közel-Kelet és Afrika

| Ország/régió                                      | Címzési pontok |Házszámok | Utca szintje | Városi szint | Érdekes pontok |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Afganisztán                                         |                 |                |              |      ✓     |          ✓         |
| Algéria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahrein                                             |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Benin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botswana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Bouvet-sziget                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Kamerun                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Cabo Verde                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Közép-afrikai Köztársaság                            |                 |                |       ✓      |      ✓     |          ✓         |
| Csád                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Kongó                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Côte d'Ivoire                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Kongói Demokratikus Köztársaság                    |                 |                |       ✓      |      ✓     |          ✓         |
| Dzsibuti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Egyiptom                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Egyenlítői-Guinea, Köztársaság                      |                 |                |       ✓      |      ✓     |          ✓         |
| Eritrea                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Etiópia                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Francia Déli Területek|                        |                |              |      ✓     |          ✓         |
| Gabon                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gambia                                              |                 |                |              |      ✓     |          ✓         |
| Ghána                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Guinea                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bissau-Guinea                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Irán                                                |                 |                |              |      ✓     |          ✓         |
| Irak                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Izrael                                              |        ✓        |       ✓        |              |      ✓     |          ✓         |
| Jordánia                                              |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Kenya                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Kuvait                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Libanon                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Lesotho                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libéria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Líbia                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Madagaszkár                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malawi                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Maldív-szigetek                                            |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Marshall-szigetek                                    |                 |                |              |      ✓     |          ✓         |
| Mauritánia                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Mauritius                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Mayotte                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Marokkó                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mozambik                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Mianmar                                             |                 |                |              |      ✓     |          ✓         |
| Namíbia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Niger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigéria                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Omán                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Katar                                               |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Réunion                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Ruanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Szent Ilona                                        |                 |                |              |      ✓     |          ✓         |
| Szaúd-Arábia                                        |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Szenegál                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seychelle-szigetek                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Sierra Leone                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Szomália                                             |                 |                |              |      ✓     |          ✓         |
| Dél-afrikai Köztársaság                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Dél-Szudán                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Szudán                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Szváziföld                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Szíria                                               |                 |                |              |      ✓     |          ✓         |
| São Tomé és Príncipe                               |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzánia                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunézia                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Egyesült Arab Emírségek                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jemen                                               |                 |                |              |      ✓     |          ✓         |
| Zambia                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbabwe                                            |                 |                |       ✓      |      ✓     |          ✓         |



## <a name="next-steps"></a>További lépések

Azure Maps helymeghatározáshoz kapcsolatos további információkért tekintse meg a [keresési](https://docs.microsoft.com/rest/api/maps/search) segédletek oldalain.

Ismerje meg a [Maps Traffic szolgáltatás lefedettségi területeit](traffic-coverage.md). 

