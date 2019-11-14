---
title: Földrajzi útválasztás által használt ország/régió-hierarchia – Azure Traffic Manager
description: Ez a cikk felsorolja az Azure Traffic Manager földrajzi útválasztási típus által használt ország/régió hierarchiát
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: allensu
ms.openlocfilehash: 13f4101c17d78a59e8cde724863d237ba888e6a4
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037210"
---
# <a name="countryregion-hierarchy-used-by-azure-traffic-manager-for-geographic-traffic-routing-method"></a>Az Azure Traffic Manager által a földrajzi forgalom útválasztási módszeréhez használt ország/régió-hierarchia

Ez a cikk felsorolja azokat az országokat és régiókat, amelyeket a **földrajzi** forgalom útválasztási módszere használ az Azure Traffic Managerban. Ezeket az információkat programozott módon is beszerezheti az [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/)meghívásával. 

- WORLD(World)

    - GEO-EU (Európa)

        - AD(Andorra)

        - AL (Albánia)

        - Itt (Ausztria)

        - AX (Åland-szigetek)

        - BA (Bosznia-Hercegovina)

        - BE(Belgium)

        - BG (Bulgária)

        - (Fehéroroszország)

        - CH (Svájc)

        - CY (Ciprus)

        - CZ (Cseh Köztársaság)

        - NÉMET (németországi)

        - DK(Denmark)

        - EE (Észtország)

        - ES (Spanyolország)

        - FI (Finnország)

        - FO (Feröer-szigetek)

        - FR (Franciaország)

        - GB (Egyesült Királyság)

        - GG(Guernsey)

        - GI (Gibraltár)

        - GR (Görögország)

        - HR (Horvátország)

        - HU (Magyarország)

        - IE(Ireland)

        - IM (Man-sziget)

        - (Izland)

        - IT (Olaszország)

        - JE(Jersey)

        - LI(Liechtenstein)

        - LT (Litvánia)

        - LU (Luxemburg)

        - LV(Latvia)

        - MC(Monaco)

        - MD(Moldova)

        - ME (Montenegró)

        - MK (Észak-Macedónia)

        - MT(Malta)

        - NL (Hollandia)

        - NEM (Norvégia)

        - PL (Lengyelország)

        - PT (Portugália)

        - RO (Románia)

        - RS(Serbia)

        - RU (Oroszország)

        - SE(Sweden)

        - SI (Szlovénia)

        - SJ(Svalbard)

        - SK(Slovakia)

        - SM (San Marino)

        - UA (Ukrajna)
            - Krím régiója

        - VA (Vatikánváros)

        - XJ(Jan Mayen)

        - XK(Kosovo)

    - GEO-ME (közel-Kelet)

        - AE (Egyesült Arab Emírségek)

        - BH (Bahrein)

        - IL (Izrael)

        - IQ (Irak)

        - IR (Irán)

        - JO (Jordánia)

        - KW(Kuwait)

        - LB(Lebanon)

        - OM (Omán)

        - PS (palesztin hatóság)

        - QA (Katar)

        - SY(Syria)

        - SA(Saudi Arabia)

        - TR (Törökország)

        - YE (Jemen)

    - GEO-NA (Észak-Amerika/Közép-Amerika/Karib-térség)

        - AG (Antigua és Barbuda)

        - AI (Anguilla)

        - AW(Aruba)

        - BB(Barbados)

        - BL (Saint Barthélemy)

        - BM(Bermuda)

        - BQ(Bonaire)

        - BS(Bahamas)

        - BZ(Belize)

        - CA (Kanada)

            - CA-AB(Alberta)

            - CA-BC (British Columbia)

            - CA-MB (Manitoba)

            - CA-NB (új-Brunswick)

            - CA-NL (Fundland és Labrador)

            - CA-NS(Nova Scotia)

            - CA-NT (északnyugati területek)

            - CA-NU (Nunavut)

            - CA-ON (Ontario)

            - CA-PE (Prince Edward-sziget)

            - CA-QC(Québec)

            - CA-SK (Saskatchewan)

            - CA-YT (Yukon terület)

        - CR (Costa Rica)

        - CU (Kuba)

        - CW(CuraÃ§ao)

        - DM(Dominica)

        - DO (Dominikai Köztársaság)

        - GD (Grenada)

        - GL (Grönland)

        - GP(Guadeloupe)

        - GT(Guatemala)

        - HN(Honduras)

        - HT(Haiti)

        - JM(Jamaica)

        - KN (Saint Kitts és Nevis)

        - KY (Kajmán-szigetek)

        - LC(Saint Lucia)

        - MF (Saint Martin)

        - MQ(Martinique)

        - MS(Montserrat)

        - MX(Mexico)

        - NI(Nicaragua)

        - PA(Panama)

        - MINISZTERELNÖK (Saint-Pierre és Miquelon)

        - PR (Puerto Rico)

        - SV (Salvador)

        - SX (Sint Maarten)

        - TC (Turks-és Caicos-szigetek)

        - TT (Trinidad és Tobago)

        - UM (az Egyesült Államok lakatlan külbirtokai)

        - Egyesült Államok (Egyesült Államok)

            - US-AK(Alaska)

            - US-AL(Alabama)

            - USA – AR (Arkansas)

            - US-AZ(Arizona)

            - USA – CA (Kalifornia)

            - US-CO(Colorado)

            - USA – CT (Connecticut)

            - US-DC (Columbia körzet)

            - US-DE(Delaware)

            - US-FL(Florida)

            - US-GA(Georgia)

            - US-HI(Hawaii)

            - US-IA(Iowa)

            - US-ID(Idaho)

            - USA – IL (Illinois)

            - US-IN(Indiana)

            - US-KS(Kansas)

            - USA – KY (Kentucky)

            - USA – LA (Louisiana)

            - US-MA(Massachusetts)

            - US-MD (Maryland)

            - US-ME(Maine)

            - US-MI(Michigan)

            - US-MN(Minnesota)

            - US-MO(Missouri)

            - US-MS(Mississippi)

            - US-MT(Montana)

            - USA – NC (Észak-Karolina)

            - USA – ND (Észak-Dakota)

            - US-NE(Nebraska)

            - USA – NH (New Hampshire)

            - USA – NJ (New Jersey)

            - USA – NM (Új-Mexikó)

            - USA – NV (Nevada)

            - USA – NY (New York)

            - US-OH(Ohio)

            - US-OK(Oklahoma)

            - USA – vagy (Oregon)

            - US-PA(Pennsylvania)

            - USA – RI (Rhode Island)

            - US-SC(South Carolina)

            - USA – SD (Dél-Dakota)

            - USA – TN (Tennessee)

            - US-TX(Texas)

            - USA – UT (Utah)

            - USA – VA (Virginia)

            - US-VT(Vermont)

            - US-WA(Washington)

            - US-WI(Wisconsin)

            - USA – WV (Nyugat-Virginia)

            - USA – WY (Wyoming)

        - VC (Saint Vincent és Grenadine-szigetek)

        - VG (Brit Virgin-szigetek)

        - VI (Amerikai Virgin-szigetek)

        - XE (Sint Eustatius)

        - XS(Saba)

    - GEO-AS(Asia)

        - AF (Afganisztán)

        - AM (Örményország)

        - AZ (Azerbajdzsán)

        - BD (Banglades)

        - BN(Brunei)

        - BT (Bhután)

        - CC (Kókusz-szigetek)

        - CN(China)

        - CX (Karácsony-sziget)

        - GE (Georgia)

        - HK (Hongkong KKT)

        - AZONOSÍTÓ (Indonézia)

        - IN(India)

        - IO (brit indiai-óceáni terület)

        - JP (Japán)

        - KG (Kirgizisztán)

        - KH (Kambodzsa)

        - KP (Észak-Korea)

        - KR(Korea)

        - KZ(Kazakhstan)

        - LA(Laos)

        - LK(Sri Lanka)

        - PP (Mianmar)

        - MN (Mongólia)

        - MO (Makaó KKT)

        - MV(Maldives)

        - SAJÁT (Malajzia)

        - NP (Nepál)

        - PH (Fülöp-szigetek)

        - PK (Pakisztán)

        - SG(Singapore)

        - TH (Thaiföld)

        - TJ(Tajikistan)

        - TL(Timor_Leste)

        - TM (Türkmenisztán)

        - TW(Taiwan)

        - UZ (Üzbegisztán)

        - VN(Vietnam)

    - GEO-AF (Afrika)

        - AO(Angola)

        - BF(Burkina Faso)

        - BI(Burundi)

        - BJ(Benin)

        - BV (Bouvet-sziget)

        - BW (Botswana)

        - CD (Kongói Demokratikus Köztársaság)

        - CF (Közép-afrikai Köztársaság)

        - CI(Côte d’Ivoire)

        - CM (Kamerun)

        - CV (Cabo Verde)

        - DJ(Djibouti)

        - DZ(Algeria)

        - PÉLDÁUL (Egyiptom)

        - ER(Eritrea)

        - ET (Etiópia)

        - GA(Gabon)

        - GH (Ghána)

        - GM(Gambia)

        - GN(Guinea)

        - GQ (Egyenlítői-Guinea)

        - GW(Guinea_Bissau)

        - KE(Kenya)

        - KM (Comore-szigetek)

        - LR(Liberia)

        - LS(Lesotho)

        - LY (Líbia)

        - MA(Morocco)

        - MG(Madagascar)

        - ML(Mali)

        - Úr (Mauritánia)

        - MU (Mauritius)

        - MW(Malawi)

        - MZ(Mozambique)

        - NA (Namíbia)

        - NE(Niger)

        - NG(Nigeria)

        - RE(Réunion)

        - RW (Ruanda)

        - SC (Seychelle-szigetek)

        - SD(Sudan)

        - SH (Szent Ilona, Ascension, Tristan da Cunha)

        - SL(Sierra Leone)

        - SN(Senegal)

        - Tehát (Szomália)

        - SS (Dél-Szudán)

        - ST (São Tomé és Príncipe)

        - SZ(Swaziland)

        - TD (Csád)

        - TF (francia déli területek)

        - TG (Togo)

        - TN (Tunézia)

        - TZ(Tanzania)

        - UG (Uganda)

        - YT (Mayotte)

        - ZA (Dél-Afrika)

        - ZM(Zambia)

        - ZW(Zimbabwe)

    - GEO-AN(Antarctica)

        - AQ(Antarctica)

    - GEO-SA (Dél-Amerika)

        - AR (Argentína)

        - BO (Bolívia)

        - BR (Brazília)

        - CL (Chile)

        - CO (Kolumbia)

        - EK (Ecuador)

        - FK (Falkland-szigetek)

        - GF (Francia Guyana)

        - GS (Dél-Georgia és Déli-Sandwich-szigetek)

        - GY(Guyana)

        - PE(Peru)

        - (Paraguay)

        - SR(Suriname)

        - UY(Uruguay)

        - VE(Venezuela)

    - GEO-AP (Ausztrália/csendes-óceáni régió)

        - AS (Amerikai Szamoa)

        - AU (Ausztrália)

            - AU-CSELEKEDJ (Ausztráliai fővárosi terület)

            - AU-NSW (új-Dél-Wales)

            - AU-NT (északi terület)

            - AU-QLD (Queensland)

            - AU-SA (Dél-Ausztrália)

            - AU-TAS(Tasmania)

            - AU-VIC(Victoria)

            - AU-WA (Nyugat-Ausztrália)

        - CK (Cook-szigetek)

        - FJ(Fiji)

        - FM (Mikronézia)

        - GU(Guam)

        - HM (Heard-sziget és McDonald-szigetek)

        - Ki (Kiribati)

        - MH (Marshall-szigetek)

        - MP (Észak-Mariana-szigetek)

        - NC (Új-Kaledónia)

        - NF (Norfolk-sziget)

        - NR(Nauru)

        - NU(Niue)

        - NZ (új-zélandi)

        - PF (Francia Polinézia)

        - PG (Pápua Új-Guinea)

        - PN (Pitcairn-szigetek)

        - PW(Palau)

        - SB (Salamon-szigetek)

        - TK(Tokelau)

        - TO(Tonga)

        - TV (Tuvalu)

        - VU (Vanuatu)

        - WF (Wallis és Futuna)

        - WS(Samoa)

## <a name="next-steps"></a>Következő lépések

- További információ a [földrajzi forgalom útválasztási módszeréről az Azure Traffic Manager](traffic-manager-routing-methods.md#geographic).
