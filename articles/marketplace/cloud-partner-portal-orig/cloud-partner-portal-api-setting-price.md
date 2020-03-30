---
title: A virtuális gépek ajánlatai díjszabása | Azure Piactér
description: A virtuális gépajánlatok díjszabásának három módszerét ismerteti.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: b96dcfa6a140d5c16208fd8183003a7462b1aa56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280439"
---
<a name="pricing-for-virtual-machine-offers"></a>Virtuálisgép-ajánlatok díjszabása
==================================

A virtuális gépajánlatok díjszabásának három módja van: személyre szabott alapdíjszabás, magonkénti díjszabás és táblázatkezelődíj.


<a name="customized-core-pricing"></a>Személyre szabott alapárképzés
-----------------------

Az árak az egyes régiókra és alapvető kombinációkra vonatkoznak. Az értékesítési lista minden régióját meg kell adni a **definition virtualMachinePricing**/**regionPrices** szakaszában.  Használja a megfelelő pénznemkódokat az egyes [régiókhoz](#regions) a kérelemben.  A következő példa a következő követelményeket mutatja be:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Magonkénti árképzés
----------------

Ebben az esetben a megjelenítők egy árat adnak meg USD-ben a termékváltozatukhoz, és az összes többi ár automatikusan generálódik. Az alaponkénti ár a kérelem **egyetlen** paraméterében van megadva.

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


<a name="spreadsheet-pricing"></a>Számolótábla-díjszabás
-------------------

A közzétevő is feltöltheti a díjszabási táblázatot egy ideiglenes tárolóhelyre, majd az URI-t is belefoglalhatja a kérelembe, mint más fájlösszetevőket. A számolótáblát ezután feltölti, lefordítja a megadott árütemezés kiértékeléséhez, és végül frissíti az ajánlatot az árképzési információkkal. Az ajánlat későbbi GET-kérelmei visszaadják a számolótábla URI-ját és a régió kiértékelt árait.

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

<a name="new-core-sizes-added-on-722019"></a>Új magméretek hozzáadva a 7/2/2019-en
---------------------------

2019. július 2-án a virtuális gép közzétevői értesítést kaptak az új Azure virtuálisgép-méretek új árainak hozzáadásáról (a magok száma alapján).  Az új árak a 10, 44, 48, 60, 120, 208 és 416 magméretekre vannak.  A meglévő virtuális gép kínál új árak at ezek a magok méretek automatikusan számított a jelenlegi árak alapján.  2019. augusztus 1-ig a kiadóknak át kell tekinteniük a további árakat, és el kell készíteniük a kívánt módosításokat.  Ezen időpont után, ha a kiadó még nem tette közzé újra, az új alapméretek automatikusan kiszámított árai lépnek érvénybe.


<a name="regions"></a>Régiók
-------

Az alábbi táblázat a testreszabott alapárképzéshez megadható különböző régiókat és a hozzájuk tartozó pénznemkódokat mutatja be.

| **Régió** | **Név**             | **Pénznemkód** |
|------------|----------------------|-------------------|
| DZ         | Algéria              | DZD               |
| AR         | Argentína            | ARS               |
| AU         | Ausztrália            | AUD               |
| AT         | Ausztria              | EUR               |
| BH         | Bahrein              | Bhd               |
| BY         | Belarusz              | RUB               |
| BE         | Belgium              | EUR               |
| BR         | Brazília               | USD               |
| BG         | Bulgária             | Bgn               |
| CA         | Kanada               | CAD               |
| CL         | Chile                | Clp               |
| CO         | Kolumbia             | Zsaru               |
| CR         | Costa Rica           | Crc               |
| HR         | Horvátország              | Hrk               |
| CY         | Ciprus               | EUR               |
| CZ         | Cseh Köztársaság       | Czk               |
| DK         | Dánia              | DKK               |
| DO         | Dominikai Köztársaság   | USD               |
| EC         | Ecuador              | USD               |
| EG         | Egyiptom                | Egp               |
| SV         | Salvador          | USD               |
| EE         | Észtország              | EUR               |
| FI         | Finnország              | EUR               |
| JK         | Franciaország               | EUR               |
| DE         | Németország              | EUR               |
| GR         | Görögország               | EUR               |
| GT         | Guatemala            | GTQ között               |
| HK         | Hongkong (KKT)        | HKD               |
| HU         | Magyarország              | Ft               |
| IS         | Izland              | Isk               |
| IN         | India                | INR               |
| ID (Azonosító)         | Indonézia            | IDR               |
| IE         | Írország              | EUR               |
| IL         | Izrael               | Ils               |
| IT         | Olaszország                | EUR               |
| JP         | Japán                | JPY               |
| JO         | Jordánia               | Jod               |
| KZ         | Kazahsztán           | KZT között               |
| KE         | Kenya                | Kes               |
| KR         | Dél-Korea                | KRW               |
| KW         | Kuvait               | KWD között               |
| LV         | Lettország               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Litvánia            | EUR               |
| LU         | Luxemburg           | EUR               |
| MK         | Észak-Macedónia      | Mkd               |
| MY         | Malajzia             | MYR               |
| MT         | Málta                | EUR               |
| MX         | Mexikó               | MXN               |
| ME         | Montenegró           | EUR               |
| MA         | Marokkó              | Őrült               |
| NL         | Hollandia          | EUR               |
| NZ         | Új-Zéland          | NZD               |
| NG         | Nigéria              | NGN               |
| NO         | Norvégia               | NOK               |
| OM         | Omán                 | OMR               |
| PK         | Pakisztán             | Pkr               |
| PA         | Panama               | USD               |
| PY         | Paraguay             | PYG között               |
| PE         | Peru                 | Toll               |
| PH         | Fülöp-szigetek          | PHP               |
| PL         | Lengyelország               | Pln               |
| PT         | Portugália             | EUR               |
| PR         | Puerto Rico          | USD               |
| QA         | Katar                | QAR között               |
| RO         | Románia              | Ron               |
| RU         | Oroszország               | RUB               |
| SA         | Szaúd-Arábia         | SAR               |
| RS         | Szerbia               | Rsd               |
| SG         | Szingapúr            | SGD               |
| SK         | Szlovákia             | EUR               |
| SI         | Szlovénia             | EUR               |
| ZA         | Dél-afrikai Köztársaság         | ZAR               |
| ES         | Spanyolország                | EUR               |
| LK         | Srí Lanka            | USD               |
| SE         | Svédország               | SEK               |
| CH         | Svájc          | CHF               |
| TW         | Tajvan               | TWD               |
| TH         | Thaiföld             | Thb               |
| TT         | Trinidad és Tobago  | Ttd               |
| TN         | Tunézia              | TND               |
| TR         | Törökország               | TRY               |
| UA         | Ukrajna              | Uah               |
| AE         | Egyesült Arab Emírségek | EUR               |
| GB         | Egyesült Királyság       | GBP               |
| USA         | Egyesült Államok        | USD               |
| UY         | Uruguay              | UYU               |
| VE         | Venezuela            | USD               |
|  |  |  |
