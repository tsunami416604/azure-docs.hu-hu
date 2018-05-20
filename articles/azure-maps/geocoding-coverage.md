---
title: A Maps-Azure geokódolás érvényességének |} Microsoft Docs
description: További tudnivalók a Maps-Azure Geokódolás érvényességének
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: a7dc7a1dbf024b6d334ec374f78d5e912db3835c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="azure-maps-geocoding-coverage"></a>Az Azure Maps geokódolás érvényességének

Egy olyan hellyel, amely Azure Maps keresésekor a keresési szolgáltatás időt vesz igénybe a keresési feltételeket, és visszaadja a szélességi és hosszúsági koordinátákkal geokódolás nevezett folyamat. Leképezések azonban nem rendelkezik azonos szintű információkat és az összes régiók és országok pontosságát. Ez a cikk segítségével meghatározhatja, hogy milyen típusú helyeket megbízhatóan kereshet minden régióban. 

## <a name="americas"></a>Amerika

| Régió             | Cím pontok | Ház számok | Utca szint | Város szint | Bizonyos elemek kikeresése |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
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
| Saint-Barthélemy                                    |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Kitts és Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Lucia                                         |                 |                |              |      ✓     |          ✓         |
| Saint-Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
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

## <a name="asia-pacific"></a>Ázsia és a Csendes-óceáni térség

| Régió             | Cím pontok |Ház számok | Utca szint | Város szint | Bizonyos elemek kikeresése |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
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

## <a name="europe"></a>Európa

| Régió             | Cím pontok |Ház számok | Utca szint | Város szint | Bizonyos elemek kikeresése |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
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
| Vatikánváros                                        |                 |                |       ✓      |      ✓     |          ✓         |


## <a name="middle-east-and-africa"></a>Közel-Kelet és Afrika

| Régió             | Cím pontok |Ház számok | Utca szint | Város szint | Bizonyos elemek kikeresése |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
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



## <a name="next-steps"></a>További lépések

Azure-leképezések geokódolás kapcsolatos további információkért tekintse meg a [keresési](https://docs.microsoft.com/rest/api/maps/search) lapok hivatkoznak.

További tudnivalók a [a Maps érvényességének területek forgalom szolgáltatás](traffic-coverage.md). 

