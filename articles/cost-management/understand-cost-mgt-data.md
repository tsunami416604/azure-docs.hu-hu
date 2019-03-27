---
title: Megismerheti az Azure Cost Management adatokat |} A Microsoft Docs
description: Ez a cikk segít jobban megismerheti, mit tartalmaz az Azure Cost Managementben, és hogy milyen gyakran a feldolgozása, összegyűjtött megjelenített és lezárt.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: f0632b0d6e651ba6e0b43586e60d582e29c5ce00
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500542"
---
# <a name="understand-cost-management-data"></a>A Cost Management adatainak értelmezése

Ez a cikk segít jobban megismerheti, mit tartalmaz az Azure Cost Managementben. És ismerteti, hogy milyen gyakran adatot dolgoz fel, összegyűjtött megjelenített és lezárt. A számlázás az Azure-használat havonta. Azonban az Azure-előfizetés típusa határozza meg, ha az elszámolási hónap vége. Különféle tényezők alapján, hogy milyen gyakran a Cost Management kap a használati adatok változik. E tényezők közé tartozik, mennyi ideig tart az adatok feldolgozásához, és hogy milyen gyakran Azure-szolgáltatások használati a számlázási rendszerhez gridre bocsáthatja ki.

## <a name="supported-microsoft-offers"></a>Támogatott Microsoft-ajánlatok

Az alábbi információkat jeleníti meg a jelenleg támogatott [Microsoft Azure-ajánlatokról](https://azure.microsoft.com/support/legal/offer-details/) az Azure Cost Managementben.  Azure-ajánlatot az Azure-előfizetés, amely rendelkezik a típusa.

| Kategória  | **Ajánlat neve** | **Kvóta azonosítója** | **Csomag száma** |
| --- | --- | --- | --- |
| **Azure Germany** | [Azure Germany Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P |
| **Nagyvállalati Szerződés (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P |
| **Nagyvállalati Szerződés (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P |
| **A Microsoft Developer Network (MSDN)** | [MSDN-platformok](https://azure.microsoft.com/offers/ms-azr-0062p) | MSDN_2014-09-01 | MS-AZR-0062P |
| **Pay-As-You-Go** | [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p)                       | PayAsYouGo_2014-09-01 | MS-AZR-0003P |
| **Pay-As-You-Go** | [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p)              | MSDNDevTest_2014-09-01 | MS-AZR-0023P |
| **Pay-As-You-Go** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)           | MPN_2014-09-01 | MS-AZR-0025P |
| **Pay-As-You-Go** | [Ingyenes próbaverzió](https://azure.microsoft.com/offers/ms-azr-0044p)                          | FreeTrial_2014-09-01 | MS-AZR-0044P |
| **Pay-As-You-Go** | [Azure in Open licencprogram](https://azure.microsoft.com/offers/ms-azr-0111p)                       | AzureInOpen_2014-09-01 | MS-AZR-0111P |
| **Pay-As-You-Go** | [Az Azure diákoknak](https://azure.microsoft.com/offers/ms-azr-0170p)                  | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Pay-As-You-Go** | Azure Pass                                                                             | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P |
| **Visual Studio** | [A Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)      | MPN_2014-09-01 | MS-AZR-0029P |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)          | MSDN_2014-09-01 | MS-AZR-0059P |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)     | MSDNDevTest_2014-09-01 | MS-AZR-0060P |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)            | MSDN_2014-09-01 | MS-AZR-0063P |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)  | MSDN_2014-09-01 | MS-AZR-0064P |

Az alábbi táblázat az ajánlatok nem támogatott.

| Kategória  | **Ajánlat neve** | **Kvóta azonosítója** | **Csomag száma** |
| --- | --- | --- | --- |
| **Cloud Solution Provider (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Cloud Solution Provider (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Cloud Solution Provider (CSP)** | Azure Germany a Microsoft Cloud Germany felhőszolgáltató programjában   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Pay-As-You-Go**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Pay-As-You-Go**                 | [A Microsoft Azure szponzorálás](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Támogatási csomagok** | Standard szintű támogatás                    | Default_2014-09-01 | MS-AZR-0041P |
| **Támogatási csomagok** | A közvetlen professzionális támogatás         | Default_2014-09-01 | MS-AZR-0042P |
| **Támogatási csomagok** | A fejlesztői támogatás                   | Default_2014-09-01 | MS-AZR-0043P |
| **Támogatási csomagok** | A Németországi támogatási csomag                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Támogatási csomagok** | Standard szintű Azure Government-támogatás   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Támogatási csomagok** | Az Azure Government Pro-Direct Support | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Támogatási csomagok** | Az Azure Government fejlesztői támogatás  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

Használatalapú fizetés, az MSDN és a Visual Studio ajánlat kategóriák élvező vásárlók esetében az adatok a Cost Management 2018-10-02-től érhető el. Mielőtt 2018-10-02-előfizetéséhez tartozó adatok eléréséhez használható a [Azure Account Center](https://account.azure.com/subscriptions) részletesen a használati adatok letöltése CSV-fájl, vagy használhatja a [használati részletek API](/rest/api/consumption/usagedetails).

## <a name="determine-your-offer-type"></a>Az ajánlat típusát határozza meg
Ha nem lát adatokat egy előfizetéshez, és meg szeretné határozni, ha az előfizetés a támogatott ajánlatok hatálya alá tartozik, ellenőrizheti, hogy az előfizetés támogatott. Ellenőrzése, hogy támogatott-e egy Azure-előfizetést, jelentkezzen be a [az Azure portal](https://portal.azure.com). Válassza ki **minden szolgáltatás** a bal oldali menü ablaktáblán. A szolgáltatások listájában jelölje ki **előfizetések**. Előfizetési lista menüjében kattintson az ellenőrizni kívánt előfizetést. Az előfizetés az Áttekintés lap jelenik meg, és láthatja a **ajánlat** és **Ajánlatazonosító**. Az alábbi képen egy példa látható.

![Az előfizetés – Áttekintés lap ajánlat és Ajánlatazonosító – példa](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Cost Management költségeket

Az alábbi táblázatokban foglalt, vagy nem Költségkezelés az adatokat.

**Költség- és használati adatok**

| **Foglalt** | **Nem tartalmazza** |
| --- | --- |
| Azure-szolgáltatás használati<sup>1</sup> | Foglalás vásárlások – további információkért lásd: [API-k a foglalást az Azure automation](../billing/billing-reservation-apis.md). |
| Marketplace-ajánlat használati | Piactér-beli vásárlásokra – további információkért lásd: [külső szolgáltatási díjakkal](../billing/billing-understand-your-azure-marketplace-charges.md). |
|   | Díjak – támogatja a további információt találhat [feltételek magyarázni a számlához](../billing/billing-understand-your-invoice.md). |
|   | Adók – további információkért lásd: [feltételek magyarázni a számlához](../billing/billing-understand-your-invoice.md). |
|   | Kreditek – további információkért lásd: [feltételek magyarázni a számlához](../billing/billing-understand-your-invoice.md). |

<sup>1</sup> azure-szolgáltatás használati foglalás alapul, és egyeztetett árak.

**Metadata**

| **Foglalt** | **Nem tartalmazza** |
| --- | --- |
| Erőforráscímkék<sup>2</sup> | erőforráscímkék csoport |

<sup>2</sup> erőforráscímkék érvényesek minden egyes szolgáltatás által kibocsátott használat szerint, és nem érhetők el visszamenőlegesen a korábbi használati.

## <a name="rated-usage-data-refresh-schedule"></a>Névleges használati adatfrissítési ütemterv

Felhasználási és érhetők el az adatok Költségkezelés + számlázás az Azure Portalon, és [API-kat támogató](index.yml). A következő szempontokat tartsa szem előtt, a költségek áttekintése:

- Becsült költségek a jelenlegi számlázási időszak napi 6 alkalommal frissülnek.
- Becsült költségek a jelenlegi számlázási időszak szerint további használati díjak módosíthatók.
- Minden egyes frissítés összegző és a sor-elemek és a korábbi frissítés adatait tartalmazza.
- Azure finalizes vagy _bezárja_ a jelenlegi számlázási időszak akár 72 órát (három naptári napig) a számlázási időszak vége után.

Az alábbi példák bemutatják, hogyan a számlázási időszak vége sikerült-e.

Előfizetések nagyvállalati szerződéssel (EA) – Ha a Számlázási hónap vége. március 31-én becsült költségek frissülnek fel, hogy 72 óra múlva. Ebben a példában április 4 éjfélkor (UTC) szerint.

Használatalapú fizetéses előfizetések – Ha az elszámolási hónap május 15-én ér véget, majd a becsült költségek előfordulhat, hogy várakozott mentése 72 óra múlva. Ebben a példában május 19 éjfélkor (UTC) szerint.

### <a name="rerated-data"></a>Rerated adatok

Ha használja a [Cost Management API-k](https://aka.ms/costmgmt/docs), a Power BI, vagy az Azure Portalon adatok lekéréséhez, várható első újra értékelik, és ennek megfelelően módosítja, a számla bezárásáig a jelenlegi számlázási időszak fel díjat.

## <a name="usage-data-update-frequency-varies"></a>Használati adatok frissítési gyakoriság változik

A Cost Management felmerült használati adatok rendelkezésre állását több tényezőtől függ:

- Az Azure-szolgáltatások (például a tárolás, számítás, CDN és SQL) milyen gyakran küldik a használat.
- A használati adatok feldolgozása a minősítés motoron keresztül, és a költségek kezelése a folyamatok az idő.

Egyes szolgáltatások gyakoribb a többinél generuje használat. Tehát hamarabb más szolgáltatásokkal, amelyek gyakran küldik az adatokat kevesebb, mint láthatja a Cost Management adatokat az egyes szolgáltatások. Általában a szolgáltatások használati megjelennek a Cost Management 8 – 24 óra alatt. Vegye figyelembe az adatok esetében, mert az összegző frissítésekről további használati díjak egy nyílt hónap frissül.

## <a name="see-also"></a>Lásd még

- Ha még nem elvégezte már az első rövid Költségkezelés, olvassa el a [elemezheti a költségek](quick-acm-cost-analysis.md).
