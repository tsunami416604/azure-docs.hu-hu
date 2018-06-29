---
title: Használati API kapcsolatos gyakori kérdések |} Microsoft Docs
description: Azure verem mérőszámok, az összehasonlítást, hogy az Azure használati API, használat és idő jelentett hibakódok listáját.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: ac22ba34bff1d5321c05bc0a0a1b14ca742079a7
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051499"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Gyakori kérdések az Azure-verem használata API

Ebben a cikkben megválaszolunk néhány kapcsolatos gyakori kérdések az Azure verem használati API-t.

## <a name="what-meter-ids-can-i-see"></a>Milyen mérő azonosítók látom?
A következő erőforrás-szolgáltató használata esetén jelentést kap:

**Hálózat**  
  
**Azonosító mérni**: F271A8A388C44D93956A063E1D2FA80B  
**A mérési neve**: statikus IP-cím használata  
**Egység**: IP-címek  
**Megjegyzések**: száma az IP-címekre. Ha felhívja a használati API a napi részletességű, a mérő az órák számát szorozva IP-címet adja vissza.  
  
**Azonosító mérni**: 9E2739BA86744796B465F64674B822BA  
**A mérési neve**: dinamikus IP-cím használata  
**Egység**: IP-címek  
**Megjegyzések**: száma az IP-címekre. Ha felhívja a használati API a napi részletességű, a mérő az órák számát szorozva IP-címet adja vissza.  
  
**Storage**  
  
**Azonosító mérni**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**A mérési neve**: TableCapacity  
**Egység**: GB\*üzemideje (óra)  
**Megjegyzések**: teljes kapacitás táblák használni.  
  
**Azonosító mérni**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**A mérési neve**: PageBlobCapacity  
**Egység**: GB\*üzemideje (óra)  
**Megjegyzések**: teljes kapacitás lapblobokat használni.  
  
**Azonosító mérni**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**A mérési neve**: QueueCapacity  
**Egység**: GB\*üzemideje (óra)  
**Megjegyzések**: kapacitás várólista által felhasznált teljes.  
  
**Azonosító mérni**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**A mérési neve**: BlockBlobCapacity  
**Egység**: GB\*üzemideje (óra)  
**Megjegyzések**: blokkblobokat által használt kapacitás teljes.  
  
**Azonosító mérni**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**A mérési neve**: TableTransactions  
**Egység**: szám 10-ben 000's kérése  
**Megjegyzések**: Table szolgáltatási kérelmek (10 000 db).  
  
**Azonosító mérni**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**A mérési neve**: TableDataTransIn  
**Egység**: érkező adatok GB-ban  
**Megjegyzések**: Table service data érkező GB-ban.  
  
**Azonosító mérni**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**A mérési neve**: TableDataTransOut  
**Egység**: kilépő GB-ban  
**Megjegyzések**: Table szolgáltatás adatforgalommal GB-ban  
  
**Azonosító mérni**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**A mérési neve**: BlobTransactions  
**Egység**: 10, 000's kérelmek száma  
**Megjegyzések**: Blob szolgáltatási kérelmek (10 000 db).  
  
**Azonosító mérni**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**A mérési neve**: BlobDataTransIn  
**Egység**: érkező adatok GB-ban  
**Megjegyzések**: Blob szolgáltatás adatok érkező GB-ban.  
  
**Azonosító mérni**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**A mérési neve**: BlobDataTransOut  
**Egység**: kilépő GB-ban  
**Megjegyzések**: Blob szolgáltatás adatforgalommal GB-ban.  
  
**Azonosító mérni**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**A mérési neve**: QueueTransactions  
**Egység**: 10, 000's kérelmek száma  
**Megjegyzések**: várólistára szolgáltatási kérelmek (10 000 db).  
  
**Azonosító mérni**: E518E809-E369-4A45-9274-2017B29FFF25  
**A mérési neve**: QueueDataTransIn  
**Egység**: érkező adatok GB-ban  
**Megjegyzések**: várólista szolgáltatás adatok érkező GB-ban.  
  
**Azonosító mérni**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**A mérési neve**: QueueDataTransOut  
**Egység**: kilépő GB-ban  
**Megjegyzések**: várólista szolgáltatás adatforgalommal GB-ban  
  
**SQL RP**  
  
**Azonosító mérni**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**A mérési neve**: DatabaseSizeHourSqlMeter  
**Egység**: MB\*üzemideje (óra)  
**Megjegyzések**: teljes DB kapacitás a létrehozásakor. Ha felhívja a használati API a napi részletességű, a mérő az órák számát szorozva MB adja vissza.  
  
**MySql RP**  
  
**Azonosító mérni**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**A mérési neve**: DatabaseSizeHourMySqlMeter  
**Egység**: MB\*üzemideje (óra)  
**Megjegyzések**: teljes DB kapacitás a létrehozásakor. Ha felhívja a használati API a napi részletességű, a mérő az órák számát szorozva MB adja vissza.  
  
**Számítás**  
  
**Azonosító mérni**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**A mérési neve**: Alap virtuális gép mérete üzemideje (óra)  
**Egység**: virtuális processzor-üzemóra  
**Megjegyzések**: virtuális magok száma és az óra, a virtuális gép futott.  
  
**Azonosító mérni**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**A mérési neve**: Windows virtuális gép mérete üzemideje (óra)  
**Egység**: virtuális processzor-üzemóra  
**Megjegyzések**: virtuális magok száma és a virtuális gép futott óra.  
  
**Azonosító mérni**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**A mérési neve**: virtuális gép mérete üzemideje (óra)  
**Egység**: virtuális gép üzemideje (óra)  
**Megjegyzések**: Base és a Windows virtuális gép rögzíti. Nem igazítja a magok.  
  
**Key Vault**  
  
**Azonosító mérni**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**A mérési neve**: Key Vault tranzakciók  
**Egység**: szám 10-ben 000's kérése  
**Megjegyzések**: Key Vault adatok vezérlősík által fogadott számát a REST API-kérés.  
  
**Azonosító mérni**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**A mérési neve**: speciális kulcsok tranzakciók  
**Egység**: 10e tranzakció  
**Megjegyzések**: RSA 3-K vagy 4 KB-os, ECC-kulcsok tranzakciók. (előzetes verzió).  
  
*Az App service**  
  
**Azonosító mérni**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**A mérési neve**: az App Service  
**Egység**: virtuális processzor-üzemóra  
**Megjegyzések**: alkalmazás szolgáltatás futtatásához használt virtuális magok száma. Megjegyzés: A Microsoft használja a mérő az App Service, Azure veremben díjat számítanak. A Felhőszolgáltatóknál segítségével az App Service (lent) mérőszámok használat számítása a bérlők számára.  
  
**Azonosító mérni**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**A mérési neve**: funkciók kérelmek  
**Egység**: 10 kérelmek  
**Megjegyzések**: kért végrehajtások (/ 10 végrehajtások) teljes száma. Végrehajtások minden alkalommal, amikor egy függvény fut az adott esemény bekövetkezésekor, vagy akkor váltódik ki, egy kötés bájtjai számítanak.  
  
**Azonosító mérni**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**A mérési neve**: funkciók - számítási  
**Egység**: GB-s  
**Megjegyzések**: erőforrás-felhasználás másodpercben gigabájt (GB/s). **Erőforrás-felhasználás megfigyelt** ideje ezredmásodpercben, a függvény végrehajtásához szükséges átlagos memória méretét GB-ban megszorozzuk kiszámítása. Egy függvény által használt memória kerekítése a legközelebbi 128 MB, legfeljebb 1 536 MB maximális memória mérete legfeljebb kerekítési által kiszámított végrehajtási idővel kell mérni a legközelebbi 1 legfeljebb ms. A minimális végrehajtási ideje és a memória egy egyetlen függvény végrehajtásra 100 ms és 128 mb kulcsattribútumokkal.  
  
**Azonosító mérni**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**A mérési neve**: közös App Service üzemideje (óra)  
**Egység**: 1 óra  
**Megjegyzések**: egy App Service-csomag shard óra használatát. Tervek mérése / alkalmazás alapon.  
  
**Azonosító mérni**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**A mérési neve**: ingyenes App Service üzemideje (óra)  
**Egység**: 1 óra  
**Megjegyzések**: / óra használata ingyenes App Service-csomag. Tervek mérése / alkalmazás alapon.  
  
**Azonosító mérni**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**A mérési neve**: kis Standard App Service üzemideje (óra)  
**Egység**: 1 óra  
**Megjegyzések**: számított alapján méretét és példányok számát.  
  
**Azonosító mérni**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**A mérési neve**: Közepes Standard App Service üzemideje (óra)  
**Egység**: 1 óra  
**Megjegyzések**: számított alapján méretét és példányok számát.  
  
**Azonosító mérni**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**A mérési neve**: nagy Standard App Service üzemideje (óra)  
**Egység**: 1 óra  
**Megjegyzések**: számított alapján méretét és példányok számát.  
  
**Egyéni munkavégző rétegek**  
  
**Azonosító mérni**: *egyéni munkavégző rétegek*  
**A mérési neve**: egyéni munkavégző rétegek  
**Egység**: üzemideje (óra)  
**Megjegyzések**: Determinisztikus mérő azonosító SKU és egyéni munkavégző réteg neve alapján jön létre. A mérési azonosító: minden egyéni munkavégző szinthez egyedi.  
  
**Azonosító mérni**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**A mérési neve**: SNI SSL  
**Egység**: / SNI SSL-kötés  
**Megjegyzések**: az App Service támogatja az SSL-kapcsolatok kétféle: SSL-kapcsolatok kiszolgálónév jelzése (SNI) és IP-cím SSL-kapcsolatokat. Az SNI-alapú SSL a modern böngészőkben használható, az IP-alapú SSL mindegyikben.  
  
**Azonosító mérni**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**A mérési neve**: IP-SSL  
**Egység**: / IP-alapú SSL-kötés  
**Megjegyzések**: az App Service támogatja az SSL-kapcsolatok kétféle: SSL-kapcsolatok kiszolgálónév jelzése (SNI) és IP-cím SSL-kapcsolatokat. Az SNI-alapú SSL a modern böngészőkben használható, az IP-alapú SSL mindegyikben.  
  
**Azonosító mérni**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**A mérési neve**: webes folyamat  
**Egység**:  
**Megjegyzések**: aktív webhelyenként óránként számítja ki.  
  
**Azonosító mérni**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**A mérési neve**: külső kimenő sávszélesség  
**Egység**: GB  
**Megjegyzések**: válasz bájt + bájt + teljes bejövő FTP válasz bájt + teljes beérkező webes kérelmek teljes kimenő kérelem központi telepítése a kérelem-válasz bájtok teljes bejövő kérelem.  
  

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Az API-k összehasonlítják Azure verem használati módja a [Azure használati API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (jelenleg a nyilvános előzetes verzió)?
* A bérlő használati API konzisztensek legyenek az Azure API, egy kivétellel: a *showDetails* jelző jelenleg nem támogatott Azure-készletben.
* A szolgáltató használati API csak Azure verem vonatkozik.
* Jelenleg a [RateCard API](https://msdn.microsoft.com/library/azure/mt219004.aspx) , amely az Azure-ban rendelkezésre álló nincs Azure-készletben.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Mi az a használati és a jelentett idő közötti különbség?
Használati adatok jelentéseinek két fő időértékek rendelkezik:

* **Jelentett idő**. Az idő, amikor a használati esemény megadott a használati rendszer
* **Aktív időszakra**. Ha az Azure-verem erőforrás volt felhasznált idő

A konkrét használati esemény értékek eltérést használati és jelentett idő jelenhet meg. Lehet, hogy a késés, amíg minden környezetben több óra.

Jelenleg, lekérheti csak *jelentett idő*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Mit jelentenek a használati API hibakódok?
| **HTTP-állapotkód:** | **Hibakód:** | **Leírás** |
| --- | --- | --- |
| 400 vagy hibás kérés |*NoApiVersion* |A *api-version* lekérdezési paraméter hiányzik. |
| 400 vagy hibás kérés |*InvalidProperty* |Tulajdonság hiányzik vagy érvénytelen értékkel rendelkezik. Az üzenet válasz törzsében. a hibakód a hiányzó tulajdonság azonosítja. |
| 400 vagy hibás kérés |*RequestEndTimeIsInFuture* |A következő *ReportedEndTime* a jövőben van. A jövőben nem megengedettek ehhez az argumentumhoz. |
| 400 vagy hibás kérés |*SubscriberIdIsNotDirectTenant* |A szolgáltató API-hívásának egy előfizetés-azonosító, amely a hívó érvényes bérlő nincs használatban van. |
| 400 vagy hibás kérés |*SubscriptionIdMissingInRequest* |A hívó előfizetés-azonosító nem található. |
| 400 vagy hibás kérés |*InvalidAggregationGranularity* |A kért egy érvénytelen összesítési granularitási. Érvényes értékek: napi és óránként. |
| 503 |*ServiceUnavailable* |Újrapróbálkozást lehetővé tevő hiba történt, mert a szolgáltatás foglalt, vagy a hívás szabályozása folyamatban van. |

## <a name="next-steps"></a>További lépések
[Számlázási ügyfél és a jóváírással Azure verem](azure-stack-billing-and-chargeback.md)

[Szolgáltató Erőforrás kihasználtsága API](azure-stack-provider-resource-api.md)

[A bérlői API-erőforrás-használat](azure-stack-tenant-resource-usage-api.md)
