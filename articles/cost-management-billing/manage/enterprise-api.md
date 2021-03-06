---
title: Azure Billing Enterprise API-k
description: Ismerje meg azokat a jelentéskészítési API-kat, amelyekkel a nagyvállalati Azure-ügyfelek programozott módon kérhetik le a használati adatokat.
author: mumami
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: reference
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b78b046e2991fa4ebfb575e4f6a1cc900df0d298
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686904"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>A nagyvállalati ügyfeleknek elérhető jelentéskészítési API-k áttekintése

> [!Note]
> A Microsoft már nem frissíti az Azure Billing – Nagyvállalati jelentéskészítő API-kat. Az [Azure használati API-kat](/rest/api/consumption) használja helyettük.

A jelentéskészítési API-kkal a nagyvállalati Azure-ügyfelek programozott módon kérhetnek le használati és számlázási adatokat az előnyben részesített adatelemző eszközökbe. A nagyvállalati ügyfelek aláírtak egy [Nagyvállalati Szerződést (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) az Azure-ral, amelyben egyeztetett pénzügyi kötelezettségeket állapítanak meg annak érdekében, hogy az Azure-erőforrások egyéni díjszabására legyenek jogosultak.

Az API-khoz szükséges minden dátum- és időparamétert az Egyezményes világidő (UTC) szerinti kombinált értékként kell megjeleníteni. Az API-k által visszaadott értékek UTC-formátumban jelennek meg.

## <a name="enabling-data-access-to-the-api"></a>Adathozzáférés biztosítása az API-knak
* **Az API-kulcs létrehozása vagy lekérése** – Jelentkezzen be az Enterprise Portalra, és lépjen a Jelentések > Használati adatok letöltése > API-hozzáférési kulcs területre az API-kulcs létrehozásához vagy lekéréséhez.
* **Kulcsok átadása az API-ban** – Az API-kulcsot minden hitelesítési és engedélyezési híváshoz át kell adni. A következő tulajdonságnak kell szerepelnie a HTTP-fejlécekben

|Kérelemfejléc kulcsa | Érték|
|-|-|
|Engedélyezés| Adja meg az értéket ebben a formátumban: **bearer {API-KULCS}** <br/> Példa: bearer eyr....09|

## <a name="consumption-based-apis"></a>Használatalapú API-k
[Itt](https://consumption.azure.com/swagger/ui/index) talál egy Swagger-végpontot az alább leírt API-khoz, amely lehetővé teszi az API egyszerű vizsgálatát és az ügyfél SDK-k létrehozását [AutoRest](https://github.com/Azure/AutoRest) vagy [Swagger CodeGen](https://swagger.io/swagger-codegen/) használatával. A 2014. május 1. utáni adatok érhetők el ezen az API-n keresztül.

* **Egyenleg és összefoglalás** – A [Balance and Summary API](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) havi összefoglaló információkat nyújt az egyenlegekről, az új vásárlásokról, az Azure Marketplace szolgáltatási díjairól, a kiigazításokról és a többletköltségekről.

* **Használati adatok** – A [Usage Details API](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) a felhasznált mennyiségek napi részletezését és az adott regisztrációhoz kapcsolódó becsült költségeket jeleníti meg. Az eredmény a példányokkal, mérőszámokkal és részlegekkel kapcsolatos információkat is tartalmazza. Az API lekérdezhető számlázási időszak vagy egy megadott kezdő és záró dátum alapján.

* **Marketplace-díjak** – A [Marketplace Store Charge API](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) a megadott számlázási időszakra, illetve a kezdési és befejezési dátumokra vonatkozóan napi bontásban adja vissza a használaton alapuló Marketplace-díjakat (az egyszeri díjakat nem tartalmazza).

* **Árlista** – A [Price Sheet API](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) a megadott regisztrációs és számlázási időszakra vonatkozóan az egyes mérők esetében érvényes díjszabást biztosítja.

* **Fenntartott példány részletei** – A [Reserved Instance usage API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) a fenntartott példányok vásárlásának használati adatait adja vissza. A [Reserved Instance charges API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) az elvégzett számlázási tranzakciókat jeleníti meg.

## <a name="data-freshness"></a>Adatok frissessége
Az összes fenti API válaszában ETagek szerepelnek. Az ETagek változása azt jelenti, hogy az adatok frissültek.  Ha később hívásokat intéz ugyanarra az API-ra ugyanazokkal a paraméterekkel, a rögzített ETaget úgy adja át, hogy a HTTP-kérelem fejlécében az „If-None-Match” kulcs szerepeljen. A válasz állapotkódja „NotModified”, ha az adatok nem frissültek tovább, és a rendszer nem ad vissza adatokat. Az API visszaadja a lekérdezett időszak teljes adathalmazát, amikor egy ETag módosul.

## <a name="helper-apis"></a>Segítő API-k
 **Számlázási időszakok listázása** – A [Billing Periods API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) olyan számlázási időszakok listáját adja vissza, amelyekben a megadott regisztráció használati adatai szerepelnek fordított időrendi sorrendben. Minden időszak tartalmazza a négy következő adathalmaz API-útvonalára mutató egyik tulajdonságot: BalanceSummary, UsageDetails, Marketplace Charges és Price Sheet.


## <a name="api-response-codes"></a>API-válaszkódok   
|Válasz állapotkódja|Üzenet|Leírás|
|-|-|-|
|200| OK|Nincs hiba|
|400| Hibás kérés| Érvénytelen paraméterek – dátumtartományok, EA-számok stb.|
|401| Nem engedélyezett| Az API-kulcs nem található, érvénytelen, lejárt stb.|
|404| Nem érhető el| A jelentésvégpont nem található|
|429 | TooManyRequests | A rendszer szabályozta a kérelmet. Várjon a(z) <code>x-ms-ratelimit-microsoft.consumption-retry-after</code> fejlécben megadott ideig, majd próbálkozzon újra.|
|500| Kiszolgálóhiba| Váratlan hiba a kérelem feldolgozása során|
| 503 | ServiceUnavailable | A szolgáltatás átmenetileg nem érhető el. Várjon a(z) <code>Retry-After</code> fejlécben megadott ideig, majd próbálkozzon újra.|
