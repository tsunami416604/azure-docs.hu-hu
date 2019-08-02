---
title: Azure számlázási nagyvállalati API-k | Microsoft Docs
description: Ismerje meg azokat a jelentési API-kat, amelyek lehetővé teszik a vállalati Azure-ügyfelek számára a használati információk programozott módon történő lekérését.
services: ''
documentationcenter: ''
author: mumami
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: banders
ms.openlocfilehash: f706ad86493981d5b38248ec209a7c8b936f6817
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443215"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>A vállalati ügyfelek jelentéskészítési API-jai – áttekintés
A jelentéskészítési API-k lehetővé teszik a nagyvállalati Azure-ügyfelek számára, hogy programozott módon lehívhatják a használati és számlázási adataikat az előnyben részesített A nagyvállalati ügyfelek aláírtak egy [nagyvállalati szerződést (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) az Azure-ban, hogy egyeztetett pénzügyi kötelezettségeket hozzanak, és hozzáférjenek az Azure-erőforrások egyéni díjszabásához.

## <a name="enabling-data-access-to-the-api"></a>Az API elérésének engedélyezése
* **Az** API-kulcs előállítása vagy beolvasása – jelentkezzen be az Enterprise Portalra, és navigáljon a jelentések > a használati > API-elérési kulcs letöltése az API-kulcs létrehozásához vagy lekéréséhez.
* **Kulcsok átadása az API-ban** – az API-kulcsot át kell adni a hitelesítéshez és az engedélyezéshez szükséges minden egyes híváshoz. A következő tulajdonságnak a HTTP-fejléceknek kell lennie

|Kérelem fejlécének kulcsa | Value|
|-|-|
|Authorization| Az érték megadása a következő formátumban: **tulajdonos {API_KEY}** <br/> Példa: tulajdonosi eyr... 09| 

## <a name="consumption-apis"></a>Felhasználási API-k
Az alább ismertetett API-k [számára elérhető egy](https://consumption.azure.com/swagger/ui/index) hencegő végpont, amely lehetővé teszi az API egyszerű beadását és az ügyféloldali SDK-k létrehozását az autorest vagy a hencegő [](https://github.com/Azure/AutoRest) [CodeGen](https://swagger.io/swagger-codegen/)használatával. Az 2014-es év május 1-től az API-n keresztül érhető el. 

* **Egyenleg és összefoglalás** – az [egyenleg és az összefoglalás API](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) havi összefoglaló információkat nyújt az egyenlegekről, az új vásárlásokról, az Azure Marketplace szolgáltatási díjairól, a kiigazításokról és a túlterhelésről.

* **Használat részletei** – a [használati részletek API](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) napi bontásban kínálja a felhasznált mennyiségeket és a becsült díjakat egy beléptetéssel. Az eredmény a példányok, a mérőórák és a részlegek információit is tartalmazza. Az API-t számlázási időszak vagy a megadott kezdési és befejezési dátum alapján kérdezheti le. 

* **Marketplace Store-díj** – a [Marketplace Store Charge API](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) a megadott számlázási időszakra, illetve a kezdési és befejezési dátumokra vonatkozóan napi bontásban adja vissza a használaton alapuló Marketplace-díjakat (az egyszeri díjat nem tartalmazza).

* **Árlista** – a [árlista API](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) a megadott regisztrációs és számlázási időszakra vonatkozóan az egyes fogyasztásmérők esetében érvényes díjszabást biztosít.

* **Fenntartott példány részletei** – a fenntartott példányok [használati API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) a fenntartott példányok vásárlásának használatát adja vissza. A [fenntartott példány díja API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) a számlázási tranzakciókat jeleníti meg. 

## <a name="data-freshness"></a>Adatok frissessége
A etagek az összes fenti API válaszában lesz visszaadva. A ETAG változása azt jelzi, hogy az Adatfrissítés megtörtént.  Ha ugyanezt a paramétereket ugyanazzal az API-val kéri, a HTTP-kérelem fejlécében adja át a rögzített ETAG a "If-None-Match" kulcs használatával. A válasz állapotkód "NotModified" lesz, ha az adott Adatfrissítés még nem történt meg, és a rendszer nem ad vissza semmilyen adatforrást. Az API a szükséges időszak teljes adatkészletét visszaküldi, valahányszor ETAG változik.

## <a name="helper-apis"></a>Segítő API-k
 **Számlázási időszakok listázása** – a [Számlázási időszakok API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) olyan számlázási időszakok listáját adja vissza, amelyekben a megadott regisztráció felhasználási adatokkal fordított időrendi sorrendben szerepel. Minden egyes időszak tartalmaz egy tulajdonságot, amely az API-útvonalra mutat a négy BalanceSummary, a UsageDetails, a Piactéri díjak és az árlista számára.


## <a name="api-response-codes"></a>API-válaszok kódjai   
|Válasz állapotkód|Message|Leírás|
|-|-|-|
|200| OK|Nincs hiba|
|401| Nem engedélyezett| Az API-kulcs nem található, érvénytelen, lejárt stb.|
|404| Nem elérhető| A jelentési végpont nem található|
|400| Hibás kérelem| Érvénytelen paraméterek – dátumtartomány, EA-számok stb.|
|500| Kiszolgálóhiba| Váratlan hiba-feldolgozási kérelem| 









