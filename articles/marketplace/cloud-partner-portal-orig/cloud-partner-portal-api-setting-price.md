---
title: A virtuálisgép-ajánlatok díjszabása |} A Microsoft Docs
description: A három módszert a virtuális gép ajánlatok díjszabása ismerteti.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: a029477dfd8046863ebfe34cd839562a0b1f3d87
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607794"
---
<a name="pricing-for-virtual-machine-offers"></a>Virtuálisgép-ajánlatok díjszabása
==================================

Adja meg a virtuális gép ajánlatok díjszabásával kapcsolatos három módja van: testre szabott core díjszabás, magonként díjszabás és díjszabási táblázat.


<a name="customized-core-pricing"></a>Testre szabott core díjszabása
-----------------------

Díjszabása az egyes régió és alapvető kombinációjával. Minden régióhoz az értékesítési listában meg kell adni a **virtualMachinePricing**/**regionPrices** a definíció szakasza.  Használja a megfelelő Pénznemkód minden [régió](#regions) a kérelemből.  A következő példa bemutatja ezeket a követelményeket:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 2,
                    "1core": 2,
                    "2core": 3,
                    "4core": 4,
                    "6core": 5,
                    "8core": 2,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 4,
                    "36core": 4,
                    "40core": 4,
                    "64core": 4,
                    "128core": 4
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Magonként díjszabása
----------------

Ebben az esetben a termékváltozat a kiadó meghatározhatja az USD-ben árú, és minden más díj automatikusan jönnek létre. A díj / processzormag megadott a **egyetlen** paraméter a kérésben.

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```


<a name="spreadsheet-pricing"></a>Táblázat díjszabása
-------------------

A közzétevő előfordulhat, hogy is töltse fel a díjszabási táblázatot egy ideiglenes tárolási helyet, majd az URI-t tartalmazza a többi fájl összetevők például a kérésben. A táblázat majd fel, a megadott ár ütemezés szerint értékelheti ki a lefordított, és végül frissíti az ajánlat az árképzési információkat. Az ajánlat GET-kérésekhez adja vissza a számolótábla URI-t és a kiértékelt árak a régió.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

<a name="regions"></a>Régiók
-------

Az alábbi táblázat a különböző régiókban is megadható a testre szabott core díjszabás és a megfelelő pénznemkód.

| **Régió** | **Name (Név)**             | **Pénznemkód** |
|------------|----------------------|-------------------|
| DZ         | Algéria              | DZD               |
| AR         | Argentína            | ARS               |
| AU         | Ausztrália            | AUD               |
| AT         | Ausztria              | EUR               |
| BH         | Bahrein              | BHD               |
| BY         | Belarusz              | RUB               |
| BE         | Belgium              | EUR               |
| BR         | Brazília               | USD               |
| BG         | Bulgária             | BGN               |
| CA         | Kanada               | CAD               |
| CL         | Chile                | CLP               |
| CO         | Kolumbia             | COP               |
| CR         | Costa Rica           | CRC               |
| HR         | Horvátország              | HRK               |
| CY         | Ciprus               | EUR               |
| CZ         | Cseh Köztársaság       | CZK               |
| DK         | Dánia              | DKK               |
| DO         | Dominikai Köztársaság   | USD               |
| EC         | Ecuador              | USD               |
| EG         | Egyiptom                | EGP               |
| SV         | Salvador          | USD               |
| EE         | Észtország              | EUR               |
| FI         | Finnország              | EUR               |
| JK         | Franciaország               | EUR               |
| DE         | Németország              | EUR               |
| GR         | Görögország               | EUR               |
| GT         | Guatemala            | GTQ               |
| HK         | Hongkong (KKT)        | HKD               |
| HU         | Magyarország              | FORINTBAN               |
| IS         | Izland              | EMEZFOGLALÁS               |
| IN         | India                | INR               |
| ID (Azonosító)         | Indonézia            | IDR               |
| IE         | Írország              | EUR               |
| IL         | Izrael               | ILS               |
| IT         | Olaszország                | EUR               |
| JP         | Japán                | JPY               |
| JO         | Jordánia               | JOD               |
| KZ         | Kazahsztán           | KZT               |
| KE         | Kenya                | KES               |
| KR         | Korea                | KRW               |
| KW         | Kuvait               | KWD               |
| LV         | Lettország               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Litvánia            | EUR               |
| LU         | Luxemburg           | EUR               |
| MK         | Észak-Macedónia      | MKD               |
| MY         | Malajzia             | MYR               |
| MT         | Málta                | EUR               |
| MX         | Mexikó               | MXN               |
| ME         | Montenegró           | EUR               |
| MA         | Marokkó              | MAD               |
| NL         | Hollandia          | EUR               |
| NZ         | Új-Zéland          | NZD               |
| NG         | Nigéria              | NGN               |
| NO         | Norvégia               | NOK               |
| OM         | Omán                 | OMR               |
| PK         | Pakisztán             | PKR               |
| PA         | Panama               | USD               |
| PY         | Paraguay             | PYG               |
| PE         | Peru                 | TOLLAL               |
| PH         | Fülöp-szigetek          | PHP               |
| PL         | Lengyelország               | PLN               |
| PT         | Portugália             | EUR               |
| PR         | Puerto Rico          | USD               |
| QA         | Katar                | KÁR               |
| RO         | Románia              | RON               |
| RU         | Oroszország               | RUB               |
| SA         | Szaúd-Arábia         | SAR               |
| RS         | Szerbia               | RSD               |
| SG         | Szingapúr            | SGD               |
| SK         | Szlovákia             | EUR               |
| SI         | Szlovénia             | EUR               |
| ZA         | Dél-Afrika         | ZAR               |
| ES         | Spanyolország                | EUR               |
| LK         | Srí Lanka            | USD               |
| SE         | Svédország               | SEK               |
| CH         | Svájc          | CHF               |
| TW         | Tajvan               | TWD               |
| TH         | Thaiföld             | THB               |
| TT         | Trinidad és Tobago  | TTD               |
| TN         | Tunézia              | TND               |
| TR         | Törökország               | TRY               |
| UA         | Ukrajna              | UAH               |
| AE         | Egyesült Arab Emírségek | EUR               |
| GB         | Egyesült Királyság       | GBP               |
| USA         | Egyesült Államok        | USD               |
| UY         | Uruguay              | UYU               |
| VE         | Venezuela            | USD               |
|  |  |  |
