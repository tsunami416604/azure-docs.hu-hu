---
title: Az Azure webalkalmazás-tűzfal (WAF) geoegyezésegyéni szabályai
description: Ez a cikk áttekintést nyújt a webalkalmazás-tűzfal (WAF) geoegyezésegyéni szabályairól az Azure Application Gateway-en.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/31/2020
ms.author: victorh
ms.openlocfilehash: 6725e1de21dbd103850071f7511e2800c6bd7b69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961087"
---
# <a name="geomatch-custom-rules-preview"></a>Földrajzi egyezés egyéni szabályai (előzetes verzió)

Az egyéni szabályok lehetővé teszik, hogy az alkalmazások és a biztonsági házirendek pontos igényeinek megfelelő, testre szabott szabályokat hozzon létre. Mostantól ország/régió szerint korlátozhatja a webes alkalmazásokhoz való hozzáférést. Mint minden egyéni szabályok, ez a logika is súlyosbítható más szabályokkal, hogy megfeleljen az alkalmazás igényeinek.

Geoszűrő egyéni szabály létrehozásához egyszerűen válassza a *Földrajzi hely* lehetőséget egyezési típusként, majd válassza ki azt az országot vagy országokat, amelyet engedélyezni/blokkolni szeretne az alkalmazásból. [Tekintse meg, hogyan hozhat létre egyéni szabályokat a Powershellben,](configure-waf-custom-rules.md) és további egyéni szabálypéldákat (create-custom-waf-rules.md) további információkért.

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="country-codes"></a>Országkódok

Ha a Geomatch operátort használja, a választók az alábbi kétjegyű országkódok bármelyike lehetnek. 

|Országhívószám | Ország neve |
| ----- | ----- |
| AD | Andorra |
| AE | Egyesült Arab Emírségek|
| AF | Afganisztán|
| AG | Antigua és Barbuda|
| AL | Albánia|
| AM | Örményország|
| AO | Angola|
| AR | Argentína|
| AS | Amerikai Szamoa|
| AT | Ausztria|
| AU | Ausztrália|
| AZ | Azerbajdzsán|
| BA | Bosznia-Hercegovina|
| BB | Barbados|
| BD | Banglades|
| BE | Belgium|
| BF | Burkina Faso|
| BG | Bulgária|
| BH | Bahrein|
| BI | Burundi|
| BJ | Benin|
| BL | Szent Barthélemy|
| BN | Brunei Szultanátus|
| BO | Bolívia|
| BR | Brazília|
| BS | Bahama-szigetek|
| BT | Bhután|
| BW | Botswana|
| BY | Belarusz|
| BZ | Belize|
| CA | Kanada|
| CD | Kongói Demokratikus Köztársaság|
| CF | Közép-afrikai Köztársaság|
| CH | Svájc|
| CI | Elefántcsontpart|
| CL | Chile|
| CM | Kamerun|
| CN | Kína|
| CO | Kolumbia|
| CR | Costa Rica|
| CU | Kuba|
| CV | Cabo Verde|
| CY | Ciprus|
| CZ | Cseh Köztársaság|
| DE | Németország|
| DK | Dánia|
| DO | Dominikai Köztársaság|
| DZ | Algéria|
| EC | Ecuador|
| EE | Észtország|
| EG | Egyiptom|
| ES | Spanyolország|
| ET | Etiópia|
| FI | Finnország|
| FJ | Fidzsi|
| FM | Mikronéziai Szövetséges Államok|
| JK | Franciaország|
| GB | Egyesült Királyság|
| GE | Grúzia|
| GF | Francia Guyana|
| GH | Ghána|
| GN | Guinea|
| GP | Guadeloupe|
| GR | Görögország|
| GT | Guatemala|
| GY | Guyana|
| HK | Hongkong (KKT)|
| HN | Honduras|
| HR | Horvátország|
| HT | Haiti|
| HU | Magyarország|
| ID (Azonosító) | Indonézia|
| IE | Írország|
| IL | Izrael|
| IN | India|
| IQ | Irak|
| IR | Iráni Iszlám Köztársaság|
| IS | Izland|
| IT | Olaszország|
| JM | Jamaica|
| JO | Jordánia|
| JP | Japán|
| KE | Kenya|
| KG | Kirgizisztán|
| KH | Kambodzsa|
| KI | Kiribati|
| KN | Saint Kitts és Nevis|
| KP | Koreai Népi Demokratikus Köztársaság|
| KR | Koreai Köztársaság|
| KW | Kuvait|
| KY | Kajmán-szigetek|
| KZ | Kazahsztán|
| LA | Laoszi Népi Demokratikus Köztársaság|
| LB | Libanon|
| LI | Liechtenstein|
| LK | Srí Lanka|
| LR | Libéria|
| LS | Lesotho|
| LT | Litvánia|
| LU | Luxemburg|
| LV | Lettország|
| LY | Líbia |
| MA | Marokkó|
| MD | Moldovai Köztársaság|
| MG | Madagaszkár|
| MK | Észak-Macedónia|
| ML | Mali|
| MM | Mianmar|
| MN | Mongólia|
| MO | Makaó (KKT)|
| MQ | Martinique|
| MR | Mauritánia|
| MT | Málta|
| MV | Maldív-szigetek|
| MW | Malawi|
| MX | Mexikó|
| MY | Malajzia|
| MZ | Mozambik|
| NA | Namíbia|
| NE | Niger|
| NG | Nigéria|
| NI | Nicaragua|
| NL | Hollandia|
| NO | Norvégia|
| NP | Nepál|
| NR | Nauru|
| NZ | Új-Zéland|
| OM | Omán|
| PA | Panama|
| PE | Peru|
| PH | Fülöp-szigetek|
| PK | Pakisztán|
| PL | Lengyelország|
| PR | Puerto Rico|
| PT | Portugália|
| PW | Palau|
| PY | Paraguay|
| QA | Katar|
| RE | Réunion|
| RO | Románia|
| RS | Szerbia|
| RU | Orosz Föderáció|
| RW | Ruanda|
| SA | Szaúd-Arábia|
| SD | Szudán|
| SE | Svédország|
| SG | Szingapúr|
| SI | Szlovénia|
| SK | Szlovákia|
| SN | Szenegál|
| SO | Szomália|
| SR | Suriname|
| SS | Dél-Szudán|
| SV | Salvador|
| SY | Szíriai Arab Köztársaság|
| SZ | Szváziföld|
| TC | Turks- és Caicos-szigetek|
| TG | Togo|
| TH | Thaiföld|
| TN | Tunézia|
| TR | Törökország|
| TT | Trinidad és Tobago|
| TW | Tajvan|
| TZ | Tanzániai Egyesült Köztársaság|
| UA | Ukrajna|
| UG | Uganda|
| USA | Egyesült Államok|
| UY | Uruguay|
| UZ | Üzbegisztán|
| VC | Saint Vincent és Grenadine-szigetek|
| VE | Venezuela|
| VG | Brit Virgin-szigetek|
| VI | Amerikai Virgin-szigetek|
| VN | Vietnam|
| ZA | Dél-afrikai Köztársaság|
| ZM | Zambia|
| ZW | Zimbabwe|

## <a name="next-steps"></a>További lépések

Miután tudomást szerzett az egyéni szabályokról, [hozzon létre saját egyéni szabályokat.](create-custom-waf-rules.md)
