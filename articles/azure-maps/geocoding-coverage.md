---
title: Geokódolási lefedettség | Microsoft Azure Maps
description: A helycím szélességi és hosszúsági koordinátákká történő átalakításának folyamatát geokódolásnak nevezzük. Ebben a cikkben a Microsoft Azure Maps geokódolási lefedettséggel rendelkező régiókról olvashat.
author: philmea
ms.author: philmea
ms.date: 12/31/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9836bd35b16c4c308b7c9d096b104c0cec68a34c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335656"
---
# <a name="azure-maps-geocoding-coverage"></a>Az Azure Maps geokódolási lefedettsége

Amikor az Azure Maps segítségével keres egy helyet, a keresési szolgáltatás, például a [Keresési cím betöltése](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress), a keresési kifejezéseket veszi fel, és visszaadja a szélességi és hosszúsági koordinátákat. Ezt a folyamatot geokódolásnak nevezzük. Az Azure Maps azonban nem rendelkezik azonos szintű információkkal és pontossággal minden régióban és országban. Ebben a cikkben határozhatja meg, hogy milyen helyeket kereshet megbízhatóan az egyes régiókban. 

Egy országban/régióban a geokódolni való képesség a közúti adatok lefedettségétől és a geokódolási szolgáltatás geokódolási pontosságától függ. A következő kategorizálások határozzák meg a geokódolási támogatás szintjét az egyes országokban/régiókban.
* **Címpontok** – A címek adatai feloldhatók a címparcellán (tulajdonsághatár) belüli szélességi/hosszúsági koordinátára. Néha a "Rooftop" pontos. Ez a címekhez elérhető legmagasabb pontossági szint. 
* **Házszámok** - A címek interpolálva vannak egy szélességi/hosszúsági koordináta szerint az utcán.
* **Utcaszint** – A címek feloldása a címet tartalmazó utca szélességi/hosszúsági koordinátáira lesz feloldva. A házszámot nem szabad feldolgozni.
* **Városszintű** – A városhelynevek támogatottak.

## <a name="americas"></a>Amerika

| Ország/régió                                       | Címpontok | Házszámok | Utcai szint | Város szint | Érdekes helyek |
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
| Dominikána                                          |                 |                |       ✓      |      ✓     |          ✓         |
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
| Szent Barthélemy                                    |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Kitts és Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Lucia                                         |                 |                |              |      ✓     |          ✓         |
| Saint Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Pierre és Miquelon                           |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Vincent és Grenadine-szigetek                    |                 |                |              |      ✓     |          ✓         |
| Sint Maarten                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Dél-Georgia és Déli-Sandwich-szigetek        |                 |                |              |      ✓     |          ✓         |
| Suriname                                            |                 |                |              |      ✓     |          ✓         |
| Trinidad és Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Egyesült Államok kisebb külső szigetek                |                 |                |              |      ✓     |          ✓         |
| Amerikai Egyesült Államok                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguay                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Brit Virgin-szigetek                              |                 |                |              |      ✓     |          ✓         |
| Amerikai Virgin-szigetek                                 |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Ázsia és a Csendes-óceáni térség

| Ország/régió                                      | Címpontok |Házszámok | Utcai szint | Város szint | Érdekes helyek |
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
| Dél-Kurils                                     |        ✓        |                |              |      ✓     |          ✓         |
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

| Ország/régió                                      | Címpontok |Házszámok | Utcai szint | Város szint | Érdekes helyek |
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
| Man-ise                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
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
| +Azori-szigetek és Madeira                                 |                 |                |       ✓      |      ✓     |          ✓         |
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

| Ország/régió                                      | Címpontok |Házszámok | Utcai szint | Város szint | Érdekes helyek |
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
| Egyenlítői-Guinea,                      |                 |                |       ✓      |      ✓     |          ✓         |
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

Az Azure Maps geokódolásáról további információt a [Keresési](https://docs.microsoft.com/rest/api/maps/search) referencialapok című témakörben talál.

Ismerje meg [a Térkép forgalmi szolgáltatás lefedettségi területeit.](traffic-coverage.md) 

