---
title: Földrajzi útválasztás által használt ország/régió hierarchia – Azure Traffic Manager
description: Ez a cikk az Azure Traffic Manager Geographic útválasztási típusa által használt ország-/régióhierarchiát sorolja fel
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: rohink
ms.openlocfilehash: 19445c06ccf08d2d7916545ad495c56883616c7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938649"
---
# <a name="countryregion-hierarchy-used-by-azure-traffic-manager-for-geographic-traffic-routing-method"></a>Az Azure Traffic Manager által földrajzi forgalom-útválasztási módszerhez használt ország-/régióhierarchia

Ez a cikk felsorolja az Azure Traffic Manager **földrajzi** forgalomútválasztási módszer által használt országokat és régiókat. Ezeket az információkat programozott módon is beszerezheti az [Azure Traffic Manager REST API-jának felhívásával.](https://docs.microsoft.com/rest/api/trafficmanager/) 

- VILÁG(Világ)

    - GEO-EU(Európa)

        - AD(Andorra)

        - AL(Albánia)

        - AT(Ausztria)

        - AX(Åland-szigetek)

        - BA (Bosznia-Hercegovina)

        - BE(Belgium)

        - BG(Bulgária)

        - BY(Fehéroroszország)

        - CH(Svájc)

        - CY (Ciprus)

        - CZ(Cseh Köztársaság)

        - DE(Németország)

        - DK(Dánia)

        - EE (Észtország)

        - ES(Spanyolország)

        - FI(Finnország)

        - FO(Feröer-szigetek)

        - FR(Franciaország)

        - GB(Egyesült Királyság)

        - GG(Guernsey)

        - GI(Gibraltár)

        - GR(Görögország)

        - HR(Horvátország)

        - HU(Magyarország)

        - IE(Írország)

        - IM (Man-szigetek)

        - IS(Izland)

        - IT(Olaszország)

        - JE(Mez)

        - LI(Liechtenstein)

        - LT(Litvánia)

        - LU(Luxemburg)

        - LV(Lettország)

        - MC(Monaco)

        - MD(Moldova)

        - ME(Montenegró)

        - MK(Észak-Macedónia)

        - MT(Málta)

        - NL(Hollandia)

        - (Norvégia)

        - PL(Lengyelország)

        - PT(Portugália)

        - RO(Románia)

        - RS(Szerbia)

        - RU(Oroszország)

        - SE(Svédország)

        - SI(Szlovénia)

        - SJ (Svalbard)

        - SK(Szlovákia)

        - SM(San Marino)

        - UA(Ukrajna)
            - Krím régió

        - VA (Vatikán)

        - XJ(Jan Mayen)

        - XK(Koszovó)

    - GEO-ME(Közel-Kelet)

        - AE (Egyesült Arab Emírségek)

        - BH(Bahrein)

        - IL(Izrael)

        - IQ(Irak)

        - IR(Irán)

        - JO(Jordánia)

        - KW(Kuvait)

        - LB(Libanon)

        - OM(Omán)

        - PS (Palesztin Hatóság)

        - Minőségbiztosítás (Katar)

        - SY(Szíria)

        - SA (Szaúd-Arábia)

        - TR(Törökország)

        - YE(Jemen)

    - GEO-NA(Észak-Amerika / Közép-Amerika / Karib-térség)

        - AG (Antigua és Barbuda)

        - AI(Anguilla)

        - AW(Aruba)

        - BB(Barbados)

        - BL(Szent Barthélemia)

        - BM(Bermuda)

        - BQ(Bonaire)

        - BS(Bahamák)

        - BZ(Belize)

        - CA(Kanada)

            - CA-AB(Alberta)

            - CA-BC (Brit Columbia)

            - CA-MB(Manitoba)

            - CA-NB(Új-Brunswick)

            - CA-NL (Új-Fundland és Labrador)

            - CA-NS (Új-Skócia)

            - CA-NT (északnyugati területek)

            - CA-NU(Nunavut)

            - CA-ON(Ontario)

            - CA-PE (Edward herceg-sziget)

            - CA-QC(Québec)

            - CA-SK(Saskatchewan)

            - CA-YT(Yukon terület)

        - CR (Costa Rica)

        - CU(Kuba)

        - TÉNYLEGES SÚLY (CuraÃ§ao)

        - DM (Dominika)

        - DO (Dominikai Köztársaság)

        - GD(Grenada)

        - GL(Grönland)

        - GP(Guadeloupe)

        - GT(Guatemala)

        - HN(Honduras)

        - HT (Haiti)

        - JM(Jamaica)

        - KN (Saint Kitts és Nevis)

        - KY (Kajmán-szigetek)

        - LC(Saint Lucia)

        - MF(Szent Márton)

        - MQ (Martinique)

        - MS(Montserrat)

        - MX(Mexikó)

        - NI(Nicaragua)

        - PA(Panama)

        - PM (Saint Pierre és Miquelon)

        - PR(Puerto Rico)

        - SV(El Salvador)

        - SX (Sint Maarten)

        - TC (Turks- és Caicos-szigetek)

        - TT (Trinidad és Tobago)

        - UM(Amerikai Egyesült Államok külső szigetei)

        - USA (Egyesült Államok)

            - US-AK(Alaszka)

            - US-AL(Alabama)

            - US-AR(Arkansas)

            - US-AZ(Arizona)

            - US-CA (Kalifornia)

            - US-CO(Colorado)

            - AMERIKAI CT (Connecticut)

            - US-DC (Columbia körzet)

            - US-DE(Delaware)

            - US-FL (Florida)

            - US-GA(Grúzia)

            - US-HI (Hawaii)

            - US-IA(Iowa)

            - US-ID (Idaho)

            - US-IL(Illinois)

            - US-IN(Indiana)

            - US-KS(Kansas)

            - US-KY (Kentucky)

            - AMERIKAI-LA(Louisiana)

            - AMERIKAI(Massachusetts)

            - AMERIKAI MD (Maryland)

            - US-ME(Maine)

            - US-MI (Michigan)

            - US-MN(Minnesota)

            - US-MO(Missouri)

            - USA-MS (Mississippi)

            - US-MT(Montana)

            - USA-NC (Észak-Karolina)

            - US-ND (Észak-Dakota)

            - US-NE(Nebraska)

            - AMERIKAI-NH(New Hampshire)

            - EGYESÜLT ÁLLAMOK-NJ (New Jersey)

            - US-NM (Új-Mexikó)

            - US-NV(Nevada)

            - US-NY (New York)

            - US-OH (Ohio)

            - US-OK (Oklahoma)

            - US-OR(Oregon)

            - US-PA (Pennsylvania)

            - US-RI (Rhode Island)

            - US-SC(Dél-Karolina)

            - US-SD (Dél-Dakota)

            - AMERIKAI-TN(Tennessee)

            - US-TX (Texas)

            - USA-UT(Utah)

            - AMERIKAI VA(Virginia)

            - US-VT(Vermont)

            - US-WA(Washington)

            - AMERIKAI-WI (Wisconsin)

            - US-WV (Nyugat-Virginia)

            - US-WY(Wyoming)

        - VC (Saint Vincent és Grenadine-szigetek)

        - VG (Brit Virgin-szigetek)

        - VI(Amerikai Virgin-szigetek)

        - XE(Sint Eustatius)

        - XS(Saba)

    - GEO-AS(Ázsia)

        - AF (Afganisztán)

        - AM (Örményország)

        - AZ(Azerbajdzsán)

        - BD(Banglades)

        - BN(Brunei)

        - BT (Bhután)

        - CC(Kókusz-szigetek)

        - CN(Kína)

        - CX (Karácsony-sziget)

        - GE (Grúzia)

        - HK (Hongkong KKT)

        - Azonosító(Indonézia)

        - IN(India)

        - IO (Brit Indiai-óceáni Terület)

        - JP(Japán)

        - KG(Kirgizisztán)

        - KH(Kambodzsa)

        - KP (Észak-Korea)

        - KR(Korea)

        - KZ(Kazahsztán)

        - LA(Laosz)

        - LK(Srí Lanka)

        - MM(Mianmar)

        - Mongólia)

        - MO(Makaó KKT)

        - MV(Maldív-szigetek)

        - MY(Malajzia)

        - NP (Nepál)

        - PH(Fülöp-szigetek)

        - PK(Pakisztán)

        - SG (Szingapúr)

        - TH(Thaiföld)

        - TJ(Tádzsikisztán)

        - TL(Timor_Leste)

        - TM (Türkmenisztán)

        - TW(Tajvan)

        - UZ (Üzbegisztán)

        - VN(Vietnam)

    - GEO-AF(Afrika)

        - AO(Angola)

        - BF(Burkina Faso)

        - BI(Burundi)

        - BJ(Benin)

        - BV(Bouvet-sziget)

        - BW(Botswana)

        - CD(Kongó (KDK))

        - KTF (Közép-afrikai Köztársaság)

        - CI(Elefántcsontpart)

        - CM(Kamerun)

        - Önéletrajz(Cabo Verde)

        - DJ (Dzsibuti)

        - DZ(Algéria)

        - EG(Egyiptom)

        - ER(Eritrea)

        - ET(Etiópia)

        - GA(gabon)

        - GH(Ghána)

        - GM(Gambia)

        - GN(Guinea)

        - GQ(Egyenlítői-Guinea)

        - GW(Guinea_Bissau)

        - KE (Kenya)

        - KM(Cososz)

        - LR(Libéria)

        - LS (Lesotho)

        - LY(Líbia)

        - MA(Marokkó)

        - MG(Madagaszkár)

        - ML(Mali)

        - MR(Mauritánia)

        - MU(Mauritius)

        - MW(Malawi)

        - MZ(Mozambik)

        - NA(Namíbia)

        - NE(Niger)

        - NG (Nigéria)

        - RE(Réunion)

        - RW(Ruanda)

        - SC (Seychelle-szigetek)

        - SD(Szudán)

        - SH(Szent Ilona, Mennybemenetel, Tristan da Cunha)

        - SL(Sierra Leone)

        - SN(Szenegál)

        - SO(Szomália)

        - SS(Dél-Szudán)

        - ST(São Tomé és Príncipe)

        - SZ(Szváziföld)

        - TD (Csád)

        - TF (francia déli területek)

        - TG(Togo)

        - TN (Tunézia)

        - TZ(Tanzánia)

        - UG(Uganda)

        - YT(Mayotte)

        - ZA(Dél-Afrika)

        - ZM(Zambia)

        - ZW(Zimbabwe)

    - GEO-AN(Antarktisz)

        - AQ (Antarktisz)

    - GEO-SA(Dél-Amerika)

        - AR(Argentína)

        - BO(Bolívia)

        - BR(Brazília)

        - CL(Chile)

        - CO(Kolumbia)

        - EK(Ecuador)

        - FK(Falkland-szigetek)

        - GF (Francia Guyana)

        - GS (Dél-Georgia és Dél-Sandwich-szigetek)

        - GY (Guyana)

        - PE(Peru)

        - PY(Paraguay)

        - SR(Suriname)

        - UY(Uruguay)

        - VE (Venezuela)

    - GEO-AP(Ausztrália / Csendes-óceáni)

        - AS (Amerikai Szamoa)

        - AU(Ausztrália)

            - AU-ACT (ausztrál fővárosi terület)

            - AU-NSW (Új-Dél-Wales)

            - AU-NT (Északi terület)

            - AU-QLD(Queensland)

            - AU-SA (Dél-Ausztrália)

            - AU-TAS(Tasmánia)

            - AU-VIC(Viktória)

            - AU-WA (Nyugat-Ausztrália)

        - CK(Cook-szigetek)

        - FJ(Fidzsi-szigetek)

        - FM(Mikronézia)

        - GU(Guam)

        - HM (Heard-sziget és McDonald-szigetek)

        - KI(Kiribati)

        - MH(Marshall-szigetek)

        - MP (Északi-Mariana-szigetek)

        - NC(Új-Kaledónia)

        - NF(Norfolk-sziget)

        - NR(Nauru)

        - NU(Niue)

        - ÚJ-Zéland (Új-Zéland)

        - PF(Francia Polinézia)

        - PG (Pápua Új-Guinea)

        - PN(Pitcairn-szigetek)

        - PW(Palau)

        - SB(Salamon-szigetek)

        - TK(Tokelau)

        - TO(Tonga)

        - TV(Tuvalu)

        - JE(Vanuatu)

        - WF(Wallis és Futuna)

        - WS(Szamoa)

## <a name="next-steps"></a>További lépések

- További információ a [földrajzi forgalomútválasztási módszeréről az Azure Traffic Managerben.](traffic-manager-routing-methods.md#geographic)
