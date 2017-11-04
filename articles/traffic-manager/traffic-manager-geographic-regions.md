---
title: "Ország vagy régió hierarchia Azure Traffic Manager földrajzi útválasztási típus által használt |} Microsoft Docs"
description: "Ez a cikk felsorolja az ország vagy régió hierarchia használják az Azure Traffic Manager földrajzi útválasztási típus"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 93db099e9e25856e13c398c4a03a5728d178e5dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="countryregion-hierarchy-used-by-azure-traffic-manager-for-geographic-traffic-routing-method"></a>Ország vagy régió hierarchia Azure Traffic Manager által használt földrajzi forgalom-útválasztási módszert

Ez a cikk felsorolja a országokból és régiókból használják a **Geographic** forgalom-útválasztási módszer az Azure Traffic Managerben. Is beszerezhető ezt az információt programozott módon meghívásával a [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/). 

- WORLD(World)

    - GEO-EU(Europe)

        - AD(Andorra)

        - Al(Albania)

        - AT(Austria)

        - AX (Åland-szigetek)

        - BA (bosnyák)

        - BE(Belgium)

        - BG(Bulgaria)

        - BY(Belarus)

        - CH(Switzerland)

        - CY(Cyprus)

        - CZ (Cseh Köztársaság)

        - DE(Germany)

        - DK(Denmark)

        - EE(Estonia)

        - ES(Spain)

        - Fi(Finland)

        - FŐ (Feröer-szigetek)

        - FR(France)

        - GB (Egyesült Királyságban)

            - GB-eng(England)

            - GB-NIR(Northern Ireland)

            - GB-sct(Scotland)

            - GB-WLS(Wales)

        - GG(Guernsey)

        - GI(Gibraltar)

        - GR(Greece)

        - HR(Croatia)

        - HU(Hungary)

        - IE(Ireland)

        - Csevegés (Man-szigeti)

        - IS(Iceland)

        - IT(Italy)

        - JE(Jersey)

        - Li(Liechtenstein)

        - LT(Lithuania)

        - LU(Luxembourg)

        - LV(Latvia)

        - MC(Monaco)

        - MD(Moldova)

        - Me(Montenegro)

        - Az MK (Macedónia, FYRO)

        - MT(Malta)

        - NL(Netherlands)

        - No(Norway)

        - PL(Poland)

        - PT(Portugal)

        - RO(ROMANIA)

        - RS(Serbia)

        - RU(RUSSIA)

        - SE(Sweden)

        - SI(Slovenia)

        - SJ(Svalbard)

        - SK(Slovakia)

        - Service Manager (San Marino)

        - UA(Ukraine)

        - VA (Vatikán)

        - XJ (Jan Mayen)

        - Xk(Kosovo)

    - Földrajzi-ME(Middle East)

        - AE (Egyesült Arab emírségekbeli)

        - BH(Bahrain)

        - IL(Israel)

        - IQ(Iraq)

        - IR(Iran)

        - Jo(Jordan)

        - KW(Kuwait)

        - LB(Lebanon)

        - OM(OMAN)

        - PS (Palesztina)

        - QA(Qatar)

        - SY(Syria)

        - SA (Szaúd-Arábia)

        - TR(Turkey)

        - YE(Yemen)

    - Földrajzi-NA(North America / Central America / Caribbean)

        - Rendelkezésre állási csoport (Antigua és Barbuda)

        - AI(Anguilla)

        - AW(Aruba)

        - BB(Barbados)

        - Isallowed (Saint Barthélemy)

        - BM(Bermuda)

        - BQ(Bonaire)

        - BS(Bahamas)

        - BZ(Belize)

        - CA(Canada)

            - CA-AB(Alberta)

            - CA-BC(British Columbia)

            - CA-MB(Manitoba)

            - CA-NB(New Brunswick)

            - CA-NL(Newfoundland and Labrador)

            - CA-NS(Nova Scotia)

            - CA-NT(Northwest Territories)

            - CA-Nu(Nunavut)

            - CA-on(Ontario)

            - CA-PE(Prince Edward Island)

            - CA-QC(Québec)

            - CA-SK(Saskatchewan)

            - CA-YT(Yukon Territory)

        - Kocsivissza (hondurasi)

        - Cu(Cuba)

        - CW(CuraÃ§AO)

        - DM(Dominica)

        - Nincs (Dominikai Köztársaság)

        - GD(Grenada)

        - GL(GREENLAND)

        - GP(Guadeloupe)

        - GT(Guatemala)

        - HN(Honduras)

        - HT(haiti)

        - JM(Jamaica)

        - KN (Saint Kitts és Nevis)

        - Key (Kajmán-szigetek)

        - LC (Kékmaszkos)

        - MF (Saint Anna)

        - MQ(Martinique)

        - MS(Montserrat)

        - MX(MEXICO)

        - Ni(Nicaragua)

        - Pa(Panama)

        - PM (Saint-Pierre és Miquelon)

        - Törlés a ka (Puerto Rico)

        - SV (El Salvador)

        - SX (Sint Martin)

        - TC (Turks és Caicos-szigetek)

        - (Trinidad és Tobago) TT

        - SZÁZALÉKOS (EGYESÜLT ÁLLAMOK Külbirtokai)

        - US(United States)

            - US-ak(Alaska)

            - US-al(Alabama)

            - US-ar(Arkansas)

            - US-az(Arizona)

            - US-CA(California)

            - US-Co(Colorado)

            - US-CT(Connecticut)

            - USA-DC(District of Columbia)

            - US-de(Delaware)

            - US-FL(Florida)

            - US-GA(Georgia)

            - US-Hi(Hawaii)

            - US-IA(Iowa)

            - US-ID(Idaho)

            - US-il(Illinois)

            - US-in(Indiana)

            - US-KS(Kansas)

            - US-KY(Kentucky)

            - US-la(Louisiana)

            - US-ma(Massachusetts)

            - US-MD(Maryland)

            - US-Me(Maine)

            - US-mi(Michigan)

            - US-Mn(Minnesota)

            - US-MO(Missouri)

            - US-MS(Mississippi)

            - US-MT(Montana)

            - USA-NC(North Carolina)

            - USA-ND(North Dakota)

            - US-ne(Nebraska)

            - USA-NH(New Hampshire)

            - USA-NJ(New Jersey)

            - USA-NM(New Mexico)

            - US-NV(Nevada)

            - USA-NY(New York)

            - US-OH(Ohio)

            - US-ok(Oklahoma)

            - US-OR(Oregon)

            - US-Pa(Pennsylvania)

            - USA-RI(Rhode Island)

            - USA-SC(South Carolina)

            - USA-SD(South Dakota)

            - US-TN(Tennessee)

            - US-TX(Texas)

            - US-ut(Utah)

            - US-va(Virginia)

            - US-VT(Vermont)

            - US-WA(Washington)

            - US-Wi(Wisconsin)

            - USA-WV(West Virginia)

            - US-WY(Wyoming)

        - VC (Saint Vincent és Grenadine-szigetek)

        - VG (Brit Virgin-szigetek)

        - VI (EGYESÜLT ÁLLAMOK Szűz-szigetek)

        - XE (Saint Eustatius)

        - XS(Saba)

    - GEO-as(Asia)

        - AF(Afghanistan)

        - AM(Armenia)

        - AZ(Azerbaijan)

        - BD(Bangladesh)

        - BN(brunei)

        - BT(Bhutan)

        - Másolatot kap (Cocos (Keeling)-szigetek)

        - CN(China)

        - CX (Karácsony-sziget)

        - GE(Georgia)

        - HK (Hongkong (KKT)

        - ID(Indonesia)

        - IN(India)

        - IO (Brit indiai-óceáni terület)

        - JP(Japan)

        - KG(Kyrgyzstan)

        - KH(Cambodia)

        - KP (Észak-Korea)

        - KR(Korea)

        - KZ(Kazakhstan)

        - LA(Laos)

        - LK (Srí Lanka)

        - MM(Myanmar)

        - Mn(Mongolia)

        - MO (MAKAÓI)

        - MV(Maldives)

        - My(Malaysia)

        - NP(Nepal)

        - PH(Philippines)

        - PK(Pakistan)

        - SG(Singapore)

        - TH(Thailand)

        - TJ(Tajikistan)

        - TL(Timor_Leste)

        - TM(Turkmenistan)

        - TW(Taiwan)

        - UZ(Uzbekistan)

        - VN(Vietnam)

    - GEO-af(Africa)

        - AO(Angola)

        - BF (Burkina Faso)

        - BI(Burundi)

        - BJ(Benin)

        - BV (Bouvet-sziget)

        - BW(Botswana)

        - CD (Kongói))

        - CF (közép-afrikai Köztársaság)

        - CG(Congo)

        - CI (Côte d'Ivoire)

        - CM(Cameroon)

        - KtgE (Cabo Verde-i)

        - DJ(Djibouti)

        - DZ(Algeria)

        - EG(Egypt)

        - ER(Eritrea)

        - ET(Ethiopia)

        - GA(Gabon)

        - GH(Ghana)

        - GM(Gambia)

        - GN(Guinea)

        - GQ (Egyenlítői guineai)

        - GW(Guinea_Bissau)

        - KE(Kenya)

        - KM(comoros)

        - LR(Liberia)

        - LS(Lesotho)

        - LY(Libya)

        - Ma(Morocco)

        - MG(Madagascar)

        - ML(mali)

        - MR(Mauritania)

        - MU(Mauritius)

        - MW(Malawi)

        - MZ(MOZAMBIQUE)

        - NA(Namibia)

        - Ne(niger)

        - NG(Nigeria)

        - RE(Réunion)

        - RW(Rwanda)

        - SC(Seychelles)

        - SD(SUDAN)

        - SH (Szent Ilona, Ascension és Tristan da Cunha)

        - SA (Sierra Leone)

        - SN(Senegal)

        - So(Somalia)

        - SS (Dél-szudán)

        - St. (São Tomé és Príncipe)

        - SZ(Swaziland)

        - TD(Chad)

        - TF (francia déli területek)

        - TG(Togo)

        - TN(Tunisia)

        - TZ(Tanzania)

        - Ug(Uganda)

        - YT(Mayotte)

        - ZA (Dél-afrikai)

        - ZM(Zambia)

        - ZW(Zimbabwe)

    - GEO-an(Antarctica)

        - AQ(Antarctica)

    - Földrajzi-SA(South America)

        - AR(Argentina)

        - BO(Bolivia)

        - BR(brazil)

        - Cl(Chile)

        - CO(Colombia)

        - EC(Ecuador)

        - FK (Falkland-szigetek)

        - GF (Francia Guyana)

        - GS (Dél-Georgia és Déli-Sandwich-szigetek)

        - Gy(Guyana)

        - PE(Peru)

        - PY(Paraguay)

        - SR(Suriname)

        - UY(Uruguay)

        - VE(Venezuela)

    - Földrajzi-AP(Australia / Pacific)

        - AS(American Samoa)

        - AU(Australia)

            - AU-ACT(Australian Capital Territory)

            - AU-NSW(New South Wales)

            - AU-NT(Northern Territory)

            - AU-QLD(Queensland)

            - AU-SA(South Australia)

            - AU-TAS(Tasmania)

            - AU-Vic(Victoria)

            - AU-WA(Western Australia)

        - CK (Cook-szigetek)

        - FJ(Fiji)

        - FM(Micronesia)

        - GU(Guam)

        - HM (Heard-sziget és McDonald-szigetek)

        - KI(Kiribati)

        - MH (Marshall-szigetek)

        - Felügyeleti csomag (Északi Mariana-szigetek)

        - NC (Új-Kaledónia)

        - NF (Norfolk-sziget)

        - NR(Nauru)

        - Nu(Niue)

        - NZ (Új-Zéland)

        - PF (Francia Polinézia)

        - A védelmi (Pápua Új-guineai)

        - PN (Pitcairn-szigetek)

        - PW(Palau)

        - SB (Salamon-szigetek)

        - TK(Tokelau)

        - TO(Tonga)

        - TV(Tuvalu)

        - VU(Vanuatu)

        - A Windows Tűzfal (és Futuna)

        - WS(samoa)

## <a name="next-steps"></a>Következő lépések

- További információ [Geographic forgalom-útválasztási módszer az Azure Traffic Managerben](traffic-manager-routing-methods.md#geographic).
