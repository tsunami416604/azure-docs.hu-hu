---
title: "Az Azure-beli hely geokódolás érvényességének alapú szolgáltatások |} Microsoft Docs"
description: "További tudnivalók a Geokódolás az Azure-beli hely lefedettségének Hídképzéssel alapú szolgáltatások"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: article
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: b2ae0f2cb8eba6ca42b3c82458d1fadf4ea93cdf
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="azure-location-based-services---geocoding-coverage"></a>Azure-beli hely alapú szolgáltatások - Geokódolás érvényességének
Azure hely-alapú szolgáltatásokhoz (LBS) címek, a helyek és az egyéb a világ különböző földrajzi entitások keresésekor geokódolás részletes információkat nyújt. Ez fontos a felhasználóknak, akik szeretnék tudni, ahonnan és nem várt hűség különböző szintű kereséskor a keresési szolgáltatás címek. Alacsony hűség érvényességének régiók keresésekor számíthat alacsonyabb abban, hogy keresési eredmények között. A következő táblázat az Azure LBS keresési Geokódolás érvényességének információkat szolgáltat.

| Régió             | Követelményre<sup>1</sup>|Számok House<sup>2</sup>| Utca szint | Város szint | Bizonyos elemek kikeresése |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| **Dél-Amerika**                                            |                 |                |              |            |                    |
| Anguilla                                            |                 |                |              |      ✓     |          ✓         |
| Antarktisz                                          |                 |                |              |      ✓     |          ✓         |
| Antigua és Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentína                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Bahama-szigetek                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbados                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermuda                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bolívia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Sint Eustatius és Saba|                 |                |              |      ✓     |          ✓         |
| Brazília                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kanada                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kajmán-szigetek                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Chile                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Clipperton-sziget                                   |                 |                |              |      ✓     |                    |
| Kolumbia                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Costa Rica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Kuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominika                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominicana                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Ecuador                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Salvador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Falkland-szigetek                                    |                 |                |              |      ✓     |          ✓         |
| Francia Guyana                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Grenada                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Guadeloupe|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Haiti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamaica                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martinique                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mexikó                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nicaragua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panama                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paraguay                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Peru                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Puerto Rico                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Saint Barthélemy                                    |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Kitts és Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Lucia                                         |                 |                |              |      ✓     |          ✓         |
| Saint Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Saint-Pierre és Miquelon|                 |                |       ✓      |      ✓     |          ✓         |
| Saint Vincent és Grenadine-szigetek                    |                 |                |              |      ✓     |          ✓         |
| Sint Maarten                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Déli-Georgia és Déli-Sandwich-szigetek        |                 |                |              |      ✓     |          ✓         |
| Suriname                                            |                 |                |              |      ✓     |          ✓         |
| Trinidad és Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Az Amerikai Egyesült Államok lakatlan külbirtokai                |                 |                |              |      ✓     |          ✓         |
| Amerikai Egyesült Államok                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguay                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Virgin-szigetek - brit                            |                 |                |              |      ✓     |          ✓         |
| Virgin-szigetek - Amerikai Egyesült Államok                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| |                 |                |              |            |                    |
| **Ázsia és a Csendes-óceáni térség**                                        |                 |                |              |            |                    |
| Amerikai Szamoa                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Ausztrália                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Banglades                                          |                 |                |              |      ✓     |          ✓         |
| Bhután                                              |                 |                |              |      ✓     |          ✓         |
| Brit Indiai-óceáni Terület                      |                 |                |              |      ✓     |          ✓         |
| Brunei                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kambodzsa                                            |                 |                |              |      ✓     |          ✓         |
| Kína |                 |                |              |      ✓     |          ✓         |
| Karácsony-sziget                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Cocos (Keeling)-szigetek|                 |                |              |      ✓     |          ✓         |
| Comore-szigetek|                 |                |              |      ✓     |          ✓         |
| Cook-szigetek                                        |                 |                |              |      ✓     |          ✓         |
| Fidzsi |                 |                |              |      ✓     |          ✓         |
| Francia Polinézia                                    |                 |                |              |      ✓     |          ✓         |
| Heard-sziget és McDonald-szigetek                   |                 |                |              |      ✓     |          ✓         |
| Hongkong                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonézia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Japán                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Laosz                                                |                 |                |              |      ✓     |          ✓         |
| Makao                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malajzia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mikronézia |                 |                |              |      ✓     |          ✓         |
| Mongólia                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Napal|                 |                |              |      ✓     |          ✓         |
| Új-Kaledónia                                       |                 |                |              |      ✓     |          ✓         |
| Új-Zéland                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Norfolk-sziget                                      |                 |                |              |      ✓     |          ✓         |
| Észak-Korea                                         |                 |                |              |      ✓     |          ✓         |
| Északi Mariana-szigetek                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pakisztán                                            |                 |                |              |      ✓     |          ✓         |
| Palau |                 |                |              |      ✓     |          ✓         |
| Pápua Új-Guinea                                    |                 |                |              |      ✓     |          ✓         |
| Paracel-szigetek                                     |                 |                |              |      ✓     |                    |
| Fülöp-szigetek                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pitcairn-szigetek                                            |                 |                |              |      ✓     |          ✓         |
| Szamoa                                               |                 |                |              |      ✓     |          ✓         |
| Senkaku-szigetek                                     |        ✓        |                |              |      ✓     |          ✓         |
| Szingapúr                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Salamon-szigetek                                     |                 |                |              |      ✓     |          ✓         |
| Dél-Korea                                         |                 |                |              |      ✓     |          ✓         |
| Déli Kurils                                     |        ✓        |                |              |      ✓     |          ✓         |
| Spratly szigetek                                     |                 |                |              |      ✓     |                    |
| Srí Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Tajvan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Thaiföld                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Turks- és Caicos-szigetek                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Vietnam                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis és Futuna|                 |                |              |      ✓     |          ✓         |
| |                 |                |              |            |                    |
| **Európa**                                              |                 |                |              |            |                    |
| Albánia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andorra                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Örményország                                             |                 |                |              |      ✓     |          ✓         |
| Ausztria                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Azerbajdzsán                                          |                 |                |              |      ✓     |          ✓         |
| Belgium                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bosznia-Hercegovina                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bulgária                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Belerus|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Horvátország                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ciprus                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Csehország                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Dánia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Észtország                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Feröer-szigetek                                       |                 |                |              |      ✓     |          ✓         |
| Finnország                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Franciaország                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Grúzia                                             |                 |                |              |      ✓     |          ✓         |
| Németország                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibralter                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Görögország                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Grönland                                           |                 |                |              |      ✓     |          ✓         |
| Guernsey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Magyarország                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Izland                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Írországban (Köztársaság)                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Man-sziget                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Olaszország                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kazahsztán                                          |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Koszovó                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Kirgizisztán                                          |                 |                |              |      ✓     |          ✓         |
| Lettország                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Liechtenstein                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Litvánia                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Luxemburg                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Macedónia                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Málta                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Moldova                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Monaco                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Montenegró                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Hollandia                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Norvégia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Lengyelország                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portugália                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Románia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Orosz Föderáció                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| San Marino                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Szerbia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Szlovákia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Szlovénia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Spanyolország                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Svédország                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Svájc                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tádzsikisztán                                          |                 |                |              |      ✓     |          ✓         |
| Törökország                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Türkmenisztán                                        |                 |                |              |      ✓     |          ✓         |
| Ukrajna                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Egyesült Királyság                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Üzbegisztán                                          |                 |                |              |      ✓     |          ✓         |
| Vatikán                                        |                 |                |       ✓      |      ✓     |          ✓         |
| |                 |                |              |            |                    |
| **Közel-Kelet Afrika**                                  |                 |                |              |            |                    |
| Afganisztán                                         |                 |                |              |      ✓     |          ✓         |
| Algéria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahrein                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Benin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botswana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Bouvet-sziget                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Kamerun                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Cabo Verde|                 |                |       ✓      |      ✓     |          ✓         |
| Közép-afrikai Köztársaság                            |                 |                |       ✓      |      ✓     |          ✓         |
| Csád                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Kongó                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Côte d’Ivoire                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Kongói Demokratikus Köztársaság                        |                 |                |       ✓      |      ✓     |          ✓         |
| Dzsibuti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Egyiptom                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Egyenlítői guineai, Köztársaság                      |                 |                |       ✓      |      ✓     |          ✓         |
| Eritrea                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Etiópia                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Francia Déli Területek|                 |                |              |      ✓     |          ✓         |
| Gabon                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gambia                                              |                 |                |              |      ✓     |          ✓         |
| Ghána                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Guinea                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bissau-Guinea                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Irán                                                |                 |                |              |      ✓     |          ✓         |
| Irak                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Izrael                                              |                 |                |              |      ✓     |          ✓         |
| Jordánia                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kenya                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Kuvait                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Libanon                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Lesotho                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libéria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Líbia|                 |                |       ✓      |      ✓     |          ✓         |
| Madagaszkár                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malawi                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Maldív-szigetek |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Marshall-szigetek |                 |                |              |      ✓     |          ✓         |
| Mauritánia                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Mauritius                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Mayotte                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Marokkó                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mozambik                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Mianmar                                             |                 |                |              |      ✓     |          ✓         |
| Namíbia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Niger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigéria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Omán                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Katar                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Réunion|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Ruanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Szent Ilona                                        |                 |                |              |      ✓     |          ✓         |
| Szaúd-Arábia                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Szenegál                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seychelle-szigetek                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Sierra Leone                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Szomália                                             |                 |                |              |      ✓     |          ✓         |
| Dél-Afrika                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Dél-Szudán                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Szudán                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Szváziföld                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Szíria                                               |                 |                |              |      ✓     |          ✓         |
| São Tomé és Príncipe, Demokratikus Köztársaság       |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzánia                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunézia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Egyesült Arab Emírségek                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Nyugati                                           |                 |                |              |      ✓     |          ✓         |
| Jemen                                               |                 |                |              |      ✓     |          ✓         |
| Zambia                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbabwe                                            |                 |                |       ✓      |      ✓     |          ✓         |


<br>
<br>




|Fogalmak|Meghatározások|
|--- |--- |
|**<sup>1</sup> -cím pont**|A pont címe index Online keresés származik. Ez a mező csak akkor támogatott, ha keresés és a Geokódolás támogatja.|
|**<sup>2</sup> -house száma**|A cím köztes index Online keresés származik. Ez a mező csak akkor támogatott, ha keresés és a Geokódolás támogatja.|
|**<sup>3</sup> -felhasználó által hozzárendelt kódot**|Online keresési adott kód, nem egy hivatalos ISO 3166-1.|
