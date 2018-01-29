---
title: "Használati API kapcsolatos gyakori kérdések |} Microsoft Docs"
description: "Azure verem mérőszámok, az összehasonlítást, hogy az Azure használati API, használat és idő jelentett hibakódok listáját."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: alfredop
ms.openlocfilehash: 65b9ff0881e46836d9f19a04cf470835679e7b2f
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Gyakori kérdések az Azure-verem használata API
Ebben a cikkben megválaszolunk néhány kapcsolatos gyakori kérdések az Azure verem használati API-t.

## <a name="what-meter-ids-can-i-see"></a>Milyen mérő azonosítók látom?
A következő erőforrás-szolgáltató használata esetén jelentést kap:

| **Erőforrás-szolgáltató** | **A mérési azonosítója** | **A mérési neve** | **Unit** | **További információ** |
| --- | --- | --- | --- | --- |
| **Hálózat** |F271A8A388C44D93956A063E1D2FA80B |Statikus IP-cím használata |IP-címek| Használt száma az IP-címek |
| |9E2739BA86744796B465F64674B822BA |Dinamikus IP-cím használata |IP-címek| Használt száma az IP-címek |
| **Storage** |B4438D5D-453B-4EE1-B42A-DC72E377F1E4 |TableCapacity |GB\*üzemideje (óra) |Tábla által felhasznált teljes kapacitás |
| |B5C15376-6C94-4FDD-B655-1A69D138ACA3 |PageBlobCapacity |GB\*üzemideje (óra) |Lapblobokat által felhasznált teljes kapacitás |
| |B03C6AE7-B080-4BFA-84A3-22C800F315C6 |QueueCapacity |GB\*üzemideje (óra) |Várólista által felhasznált teljes kapacitás |
| |09F8879E-87E9-4305-A572-4B7BE209F857 |BlockBlobCapacity |GB\*üzemideje (óra) |Blokkblobok által felhasznált teljes kapacitás |
| |B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 |TableTransactions |10., 000's kérelmek száma |TABLE szolgáltatás kérelmek (10, 000's) |
| |50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D |TableDataTransIn |Érkező adatok GB-ban |TABLE szolgáltatás adatok érkező GB-ban |
| |1B8C1DEC-EE42-414B-AA36-6229CF199370 |TableDataTransOut |Kimenő forgalom GB-ban |TABLE szolgáltatás adatforgalommal GB-ban |
| |43DAF82B-4618-444A-B994-40C23F7CD438 |BlobTransactions |10., 000's kérelmek száma |BLOB szolgáltatás kérelmek (10, 000's) |
| |9764F92C-E44A-498E-8DC1-AAD66587A810 |BlobDataTransIn |Érkező adatok GB-ban |BLOB szolgáltatás adatok érkező GB-ban |
| |3023FEF4-ECA5-4D7B-87B3-CFBC061931E8 |BlobDataTransOut |Kimenő forgalom GB-ban |BLOB szolgáltatás adatforgalommal GB-ban |
| |EB43DD12-1AA6-4C4B-872C-FAF15A6785EA |QueueTransactions |10., 000's kérelmek száma |Várólista szolgáltatás kérelmek (10, 000's) |
| |E518E809-E369-4A45-9274-2017B29FFF25 |QueueDataTransIn |Érkező adatok GB-ban |Várólista szolgáltatás adatok érkező GB-ban |
| |DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2 |QueueDataTransOut |Kimenő forgalom GB-ban |Várólista szolgáltatás adatforgalommal GB-ban |
| **Sql RP**            | CBCFEF9A-B91F-4597-A4D3-01FE334BED82 | DatabaseSizeHourSqlMeter   | MB\*üzemideje (óra)   | Teljes DB-kapacitás létrehozását, minden órában jelentett.  |
| **MySql RP**          | E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3 | DatabaseSizeHourMySqlMeter | MB\*üzemideje (óra)    | Teljes DB-kapacitás létrehozását, minden órában jelentett. |
| **Számítás** |FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5 |Alapszintű VM-méret órák |Virtuális core perc | Virtuális magok száma alkalommal fordult elő a virtuális gép futott perc |
| |9CD92D4C-BAFD-4492-B278-BEDC2DE8232A |Windows virtuális gép mérete üzemideje (óra) |Virtuális core perc | Virtuális magok száma alkalommal fordult elő a virtuális gép futott perc |
| |6DAB500F-A4FD-49C4-956D-229BB9C8C793 |Virtuális gép mérete üzemideje (óra) |Virtuális gép üzemideje (óra) |Kiinduló és a Windows virtuális gép rögzíti. Nem igazítja a magok |
| **Key Vault** |EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 |Key Vault tranzakciók | 10., 000's kérelmek száma| Kulcstároló adatainak vezérlősík által fogadott REST API-kérelmek száma |
| **Az App service** |190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  | App Service   | Virtuális processzor-üzemóra  | Az app service futtatásához használt virtuális magok száma |
|             | 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE | Funkciók - számítási kérelmek      | 10 kérelmek              | Funkciók vonatkozik  |
|             | 957E9F36-2C14-45A1-B6A1-1723EF71A01D | Közös App Service üzemideje (óra)          | 1 óra                   |                       |
|             | 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9 | Ingyenes App Service üzemideje (óra)            | 1 óra                   |                       |
|             | 88039D51-A206-3A89-E9DE-C5117E2D10A6 | Kisméretű, Standard szintű App Service üzemideje (óra)  | 1 óra                   |                       |
|             | 83A2A13E-4788-78DD-5D55-2831B68ED825 | Standard szintű, közepes méretű App Service üzemideje (óra) | 1 óra                   |                       |
|             | 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6 | Nagyméretű, Standard szintű App Service üzemideje (óra)  | 1 óra                   |                       |
|             | 264ACB47-AD38-47F8-ADD3-47F01DC4F473 | SNI SSL – SNI-célú SSL-tanúsítvány                           | Egy SNI SSL-kötés      | App Service vonatkozik |
|             | 60B42D72-DC1C-472C-9895-6C516277EDB4 | IP SSL – IP-célú SSL-tanúsítvány                            | Egy IP-alapú SSL-kötés | App Service vonatkozik |

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Az API-k összehasonlítják Azure verem használati módja a [Azure használati API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (jelenleg a nyilvános előzetes verzió)?
* A bérlő használati API konzisztensek legyenek az Azure API, egy kivétellel: a *showDetails* jelző jelenleg nem támogatott Azure-készletben.
* A szolgáltató használati API csak Azure verem vonatkozik.
* Jelenleg a [RateCard API](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) , amely az Azure-ban rendelkezésre álló nincs Azure-készletben.

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
