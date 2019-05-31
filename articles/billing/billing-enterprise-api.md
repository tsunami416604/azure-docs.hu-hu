---
title: Az Azure Enterprise API-k számlázási |} A Microsoft Docs
description: További információ a Reporting API-k, amelyek lehetővé teszik a nagyvállalati Azure-ügyfelek használati adatok programozott módon kérhetők.
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
ms.author: erikre
ms.openlocfilehash: 5722e05e5a5e3a57b4d12b70b14f8674364f824b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244817"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>A vállalati ügyfelek a Reporting API-k áttekintése
A Reporting API-k engedélyezése a nagyvállalati Azure-ügyfelek használati és számlázási adatok programozott módon lekérni az előnyben részesített adatok elemzésére szolgáló eszközöket. A vállalati felhasználók regisztráltak- [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) az Azure-ral az egyeztetett pénzügyi kötelezettségvállalások és egyéni díjszabás az Azure-erőforrásokhoz való hozzáférést.

## <a name="enabling-data-access-to-the-api"></a>Adatelérési API engedélyezése
* **Hozza létre, vagy kérje le az API-kulcsot** -jelentkezzen be a vállalati portált, és keresse meg a jelentéseket > használati adatok letöltése > API hozzáférési kulcsa létrehozni vagy beolvasni az API-kulcsot.
* **Kulcsok átadása az API-ban** – az API-kulcsot kell átadni a hitelesítéshez és engedélyezéshez minden egyes híváshoz. A következő tulajdonságot kell lennie, a HTTP-fejlécek

|Fejléc kulcs kérése | Érték|
|-|-|
|Engedélyezés| Adja meg az értéket a következő formátumban: **tulajdonosi {API_KEY}** <br/> Példa: tulajdonosi eyr... 09| 

## <a name="consumption-apis"></a>Szolgáltatáshasználati API-jai
A Swagger-végpont érhető el [Itt](https://consumption.azure.com/swagger/ui/index) esetében az API-k leírt, amely alatt könnyen önelemzési API- és ügyféloldali SDK-k használatával képességét engedélyezze [AutoRest](https://github.com/Azure/AutoRest) vagy [Swagger CodeGen](https://swagger.io/swagger-codegen/). 2014. május 1-én kezdődő adatokat az API-n keresztül érhető el. 

* **Egyenleg és összegzése** – a [egyenlegét és az API összefoglaló](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) kínál a jóváírásokat összefoglaló balances, új vásárlások, Azure Marketplace szolgáltatási díjai, módosítását és kereten túli díjak havi összegzését.

* **Használat részletei** – a [használatának részletes API](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) kínál a felhasznált mennyiségek és becsült költségek szerint regisztrációs napi bontásban. Az eredmény példányok, mérőszámok és szervezeti információkat is biztosít. Az API-t lekérdezhetők, számlázási időszak, vagy egy megadott kezdő és záró dátumát. 

* **Marketplace-en Store díj** – a [Marketplace Store díj API](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) adja vissza a piactér használat alapú költségek bontás naponta az adott számlázási időszak vagy kezdő és záró dátuma (egyszeri díjak nem szerepelnek).

* **Árlista** – a [ár lap API](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) felbontáson a megadott regisztrációs és számlázási időszak a alkalmazni sebesség biztosít.

* **Fenntartott példány részletei** – a [fenntartott példány használata API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) adja vissza, a fenntartott példányok használatának való vásárlásra. A [fenntartott példány díjak API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) jeleníti meg a létrehozott számlázási tranzakciókat. 

## <a name="data-freshness"></a>Adatok frissessége
A válasz a fenti API az ETag lesznek visszaadva. Etag módosítása azt jelzi, hogy az adatok frissítve lett.  Az ezt követő azonos paraméterekkel azonos API-hívás adja át a rögzített Etag a kulccsal "If-None-Match" http-kérés fejlécében. A válaszként kapott állapotkód "NotModified" lenne, ha az adatok további nem frissültek, és nem az adatok visszaadásához fog. API a teljes adatkészletet ad vissza, a szükséges időszak, amikor egy etag változik.

## <a name="helper-apis"></a>Segédlet az API-k
 **Számlázási időszak listában** – a [elszámolási időszakok API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) elszámolási időszakok, hogy egy használati adatokat a megadott regisztrációs fordított időrendben listáját adja vissza. Minden időszakban mutat a négy adatkészletek - BalanceSummary, UsageDetails, piactér-díjak és árlista API útvonala tulajdonságot tartalmaz.


## <a name="api-response-codes"></a>API-válaszkódok   
|Válasz állapotkódja|Message|Leírás|
|-|-|-|
|200| OK|Nincs hiba|
|401| Nem engedélyezett| API-kulcs nem található, érvénytelen, lejárt stb.|
|404| Nem elérhető| A jelentés a végpont nem található|
|400| Hibás kérelem| Érvénytelen paraméterek – dátumtartományokat, nagyvállalati szerződéssel rendelkező számokat stb.|
|500| Hiba történt az adatkiszolgálón| Váratlan hiba történt a kérelem feldolgozása| 









