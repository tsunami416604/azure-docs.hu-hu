---
title: Az Azure Traffic Manager földrajzi útválasztási típus által használt ország/régió-hierarchia |} A Microsoft Docs
description: Ez a cikk felsorolja az Azure Traffic Manager földrajzi útválasztási típus által használt ország/régió-hierarchia
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 26e54ba507ecab5d12ceb4169baaa52f99c20792
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232430"
---
# <a name="countryregion-hierarchy-used-by-azure-traffic-manager-for-geographic-traffic-routing-method"></a>A földrajzi forgalom-útválasztási módszer az Azure Traffic Manager által használt ország/régió-hierarchia

Ez a cikk felsorolja az országok és régiók használják a **Geographic** forgalom-útválasztási módszer az Azure Traffic Managerben. Is beszerezhető ezek az információk programozott módon meghívásával a [Azure Traffic Manager REST API-val](https://docs.microsoft.com/rest/api/trafficmanager/). 

- WORLD(World)

    - GEO-EU(Europe)

        - AD(Andorra)

        - Al(Albania)

        - AT(Austria)

        - Az AX (Aland-szigetek)

        - BA (Bosznia-Hercegovina)

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

        - FO (Feröer-szigetek)

        - FR(France)

        - GB-os (Egyesült Királyság)

        - GG(Guernsey)

        - GI(Gibraltar)

        - GR(Greece)

        - HR(Croatia)

        - HU(Hungary)

        - IE(Ireland)

        - Csevegési (Man-Man)

        - IS(Iceland)

        - IT(Italy)

        - JE(Jersey)

        - LI(Liechtenstein)

        - LT(Lithuania)

        - LU(Luxembourg)

        - LV(Latvia)

        - MC(Monaco)

        - MD(Moldova)

        - Me(Montenegro)

        - Az MK (Macedónia, V.J.K.)

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

        - Service Manager (a San Marino)

        - UA(Ukraine)

        - Sebezhetőség-Felmérési (Vatikánváros)

        - XJ (Jan Mayen)

        - XK(Kosovo)

    - GEO-ME(Middle East)

        - AE (Egyesült Arab Emírségek)

        - BH(Bahrain)

        - IL(Israel)

        - IQ(Iraq)

        - IR(Iran)

        - Jo(Jordan)

        - KW(Kuwait)

        - LB(Lebanon)

        - OM(OMAN)

        - PS (palesztin nemzeti hatóság)

        - QA(Qatar)

        - SY(Syria)

        - SA(Saudi Arabia)

        - TR(Turkey)

        - YE(Yemen)

    - GEO-NA(North America / Central America / Caribbean)

        - Rendelkezésre állási csoport (Antigua és Barbuda)

        - AI(Anguilla)

        - AW(Aruba)

        - BB(Barbados)

        - BL (Saint Barthélemy)

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

        - Létrehozva (Costa Rica)

        - Cu(Cuba)

        - CW(CuraÃ§AO)

        - DM(Dominica)

        - Hajtsa végre (Dominikai Köztársaság)

        - GD(Grenada)

        - GL(GREENLAND)

        - GP(Guadeloupe)

        - GT(Guatemala)

        - HN(Honduras)

        - HT(Haiti)

        - JM(Jamaica)

        - KN (Saint Kitts és Nevis)

        - KY (Kajmán-szigetek)

        - LC(Saint Lucia)

        - MF (Saint-Martin)

        - MQ(Martinique)

        - MS(Montserrat)

        - MX(Mexico)

        - NI(Nicaragua)

        - PA(Panama)

        - DU. (Saint Pierre és Miquelon)

        - Pull-kérelem (Puerto Rico)

        - SV (El Salvador)

        - SX (Sint Maarten)

        - TC (a Turks és Caicos-szigetek)

        - TT (Trinidad és Tobago)

        - UM (EGYESÜLT ÁLLAMOK Külbirtokai)

        - US(United States)

            - US-ak(Alaska)

            - US-AL(Alabama)

            - US-ar(Arkansas)

            - US-AZ(Arizona)

            - US-CA(California)

            - US-Co(Colorado)

            - US-CT(Connecticut)

            - USA-DC(District of Columbia)

            - US-DE(Delaware)

            - US-FL(Florida)

            - US-GA(Georgia)

            - US-HI(Hawaii)

            - US-IA(Iowa)

            - US-ID(Idaho)

            - US-il(Illinois)

            - US-in(Indiana)

            - US-KS(Kansas)

            - US-KY(Kentucky)

            - US-la(Louisiana)

            - US-MA(Massachusetts)

            - US-MD(Maryland)

            - US-Me(Maine)

            - US-mi(Michigan)

            - US-MN(Minnesota)

            - US-MO(Missouri)

            - US-MS(Mississippi)

            - US-MT(Montana)

            - USA-NC(North Carolina)

            - USA-ND(North Dakota)

            - US-NE(Nebraska)

            - USA-NH(New Hampshire)

            - USA-NJ(New Jersey)

            - USA-NM(New Mexico)

            - US-NV(Nevada)

            - USA-NY(New York)

            - US-OH(Ohio)

            - US-OK(Oklahoma)

            - US-OR(Oregon)

            - US-PA(Pennsylvania)

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

        - VI(U.S. Szűz-szigetek)

        - XE (Sint Eustatius)

        - XS(Saba)

    - GEO-as(Asia)

        - AF(Afghanistan)

        - AM(Armenia)

        - AZ(Azerbaijan)

        - BD(Bangladesh)

        - BN(Brunei)

        - BT(Bhutan)

        - CC (Kókusz (Keeling)-szigetek)

        - CN(China)

        - A CX (Karácsony-sziget)

        - GE(Georgia)

        - HK (Hongkong KKT)

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

        - LK(Sri Lanka)

        - MM(Myanmar)

        - Mn(Mongolia)

        - Hónap (Makaó (KKT))

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

        - BF(Burkina Faso)

        - BI(Burundi)

        - BJ(Benin)

        - BV (Bouvet-sziget)

        - BW(Botswana)

        - CD (Kongó (KDK))

        - CF-hez (közép-afrikai Köztársaság)

        - CG(Congo)

        - Konfigurációelem (rendelkező d'Ivoire)

        - CM(Cameroon)

        - CV (Cabo Verde)

        - DJ(Djibouti)

        - DZ(Algeria)

        - EG(Egypt)

        - ER(Eritrea)

        - ET(Ethiopia)

        - GA(Gabon)

        - GH(Ghana)

        - GM(Gambia)

        - GN(Guinea)

        - GQ (Egyenlítői-Guinea)

        - GW(Guinea_Bissau)

        - KE(Kenya)

        - KM(comoros)

        - LR(Liberia)

        - LS(Lesotho)

        - LY(Libya)

        - MA(Morocco)

        - MG(Madagascar)

        - ML(Mali)

        - MR(Mauritania)

        - MU(Mauritius)

        - MW(Malawi)

        - MZ(Mozambique)

        - NA(Namibia)

        - NE(Niger)

        - NG(Nigeria)

        - RE(Réunion)

        - RW(Rwanda)

        - SC(Seychelles)

        - SD(SUDAN)

        - SH (Szent Ilona, Ascension és Tristan da Cunha)

        - SL(Sierra Leone)

        - SN(Senegal)

        - So(Somalia)

        - SS (Dél-szudán)

        - St. (Sao Tome és Principe)

        - SZ(Swaziland)

        - TD(Chad)

        - TF (francia déli területek)

        - TG(Togo)

        - TN(Tunisia)

        - TZ(Tanzania)

        - Ug(Uganda)

        - YT(Mayotte)

        - ZA (Dél-Afrika)

        - ZM(Zambia)

        - ZW(Zimbabwe)

    - GEO-AN(Antarctica)

        - AQ(Antarctica)

    - GEO-SA(South America)

        - AR(Argentina)

        - BO(Bolivia)

        - BR(brazil)

        - Cl(Chile)

        - CO(Colombia)

        - EC(Ecuador)

        - FK (Falkland-szigetek)

        - GF (Francia Guyana)

        - A GS (Dél-Georgia és Déli-Sandwich-szigetek)

        - GY(Guyana)

        - PE(Peru)

        - PY(Paraguay)

        - SR(Suriname)

        - UY(Uruguay)

        - VE(Venezuela)

    - GEO-AP(Australia / Pacific)

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

        - Mart LOCK (Cook-szigetek)

        - FJ(Fiji)

        - FM(Micronesia)

        - GU(Guam)

        - HM (Heard-sziget és McDonald-szigetek)

        - KI(Kiribati)

        - MH (Marshall-szigetek)

        - Felügyeleti csomag (Észak-Mariana-szigetek)

        - Hálózati vezérlő (Új-Kaledónia)

        - NF (Norfolk-sziget)

        - NR(Nauru)

        - Nu(Niue)

        - NZ (Új-Zéland)

        - PF (Francia Polinézia)

        - PG (Pápua Új-Guinea)

        - PN (Pitcairn-szigetek)

        - PW(Palau)

        - SB (Salamon-szigetek)

        - TK(Tokelau)

        - TO(Tonga)

        - TV(Tuvalu)

        - VU(Vanuatu)

        - A Windows Tűzfal (Wallis és Futuna)

        - WS(Samoa)

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Geographic forgalom-útválasztási módszer az Azure Traffic Managerben](traffic-manager-routing-methods.md#geographic).
