---
title: Megismerheti az Azure Cost Management adatokat |} A Microsoft Docs
description: Ez a cikk segít jobban megismerheti, mit tartalmaz az Azure Cost Managementben, és hogy milyen gyakran a feldolgozása, összegyűjtött megjelenített és lezárt.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/01/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 44b95c92f51ca9782fca492f3dec3142087ecc91
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797021"
---
# <a name="understand-cost-management-data"></a>A Cost Management adatainak értelmezése

Ez a cikk segít jobban megismerheti az Azure költség- és használati adatokat, amely az Azure Cost Management része. Ismerteti, hogy milyen gyakran adatot dolgoz fel, összegyűjtött megjelenített és lezárt. A számlázás az Azure-használat havonta. Bár a számlázási ciklusa havi időszakokat, ciklus kezdő és záró dátumát régiónként eltérő előfizetés-típus. Különféle tényezők alapján, hogy milyen gyakran a Cost Management kap a használati adatok változik. E tényezők közé tartozik, mennyi ideig tart az adatok feldolgozásához, és hogy milyen gyakran Azure-szolgáltatások használati a számlázási rendszerhez gridre bocsáthatja ki.

A Cost Management minden használati és beszerzési, beleértve a foglalásokat és a nagyvállalati szerződés (EA) fiókok külső ajánlatokat tartalmaz. A Microsoft ügyfél-szerződés (MCA) fiókok és az utólagos elszámolású díjszabás szerint az egyes előfizetések csak az Azure és a Marketplace-szolgáltatások használati tartalmazza. Támogatás és más költségei nem szerepelnek. Költségek becslése mindaddig, amíg a számla alapján jön létre, és nem kéttényezős kredittel jutalmazzuk.

## <a name="supported-microsoft-azure-offers"></a>Támogatott Microsoft Azure-ajánlatok

Az alábbi információkat jeleníti meg a jelenleg támogatott [Microsoft Azure-ajánlatokról](https://azure.microsoft.com/support/legal/offer-details/) az Azure Cost Managementben. Azure-ajánlatot az Azure-előfizetés, amely rendelkezik a típusa. Cost Management-től érhetők el az adatok a **-adatok a** dátum. Ha egy előfizetési ajánlatok megváltozik, a költségek az ajánlat dátuma előtt nem lesz elérhető. 

| **Kategória**  | **Ajánlat neve** | **Kvóta azonosítója** | **Csomag száma** | **A rendelkezésre álló adatok** |
| --- | --- | --- | --- | --- |
| **Azure Germany** | [Azure Germany Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P | 2018. október 2<sup>2</sup> |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | 2014. május<sup>1</sup> |
| **Nagyvállalati Szerződés (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | 2014. május<sup>1</sup> |
| **Nagyvállalati Szerződés (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | 2014. május<sup>1</sup> |
| **A Microsoft ügyfél-szerződés** | [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | – | Március 2019<sup>3</sup> |
| **A Microsoft ügyfél-szerződés** | [Microsoft Azure-csomag fejlesztéshez és teszteléshez](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | – | Március 2019<sup>3</sup> |
| **A Microsoft Developer Network (MSDN)** | [MSDN-platformok](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2018. október 2<sup>2</sup> |
| **Pay-As-You-Go** | [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2018. október 2<sup>2</sup> |
| **Pay-As-You-Go** | [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2018. október 2<sup>2</sup> |
| **Pay-As-You-Go** | [A Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2018. október 2<sup>2</sup> |
| **Pay-As-You-Go** | [Az ingyenes próbaidőszak](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2018. október 2<sup>2</sup> |
| **Pay-As-You-Go** | [Azure in Open licencprogram](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2018. október 2<sup>2</sup> |
| **Pay-As-You-Go** | [Az Azure diákoknak](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P | 2018. október 2<sup>2</sup> |
| **Pay-As-You-Go** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P | 2018. október 2<sup>2</sup> |
| **Visual Studio** | [A Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2018. október 2<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2018. október 2<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2018. október 2<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2018. október 2<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2018. október 2<sup>2</sup> |

_<sup>**1** </sup> adatokat, mielőtt a 2014. május, látogasson el a [Azure Enterprise portálon](https://ea.azure.com)._

_<sup>**2** </sup> adatokat, mielőtt 2018. október 2., látogasson el a [Azure Account Center](https://account.azure.com/subscriptions)._

_<sup>**3** </sup> a Microsoft ügyfél-szerződések március 2019 használatába, és nem kell semmilyen előzményadatokat a pont előtt._

_<sup>**4** </sup> előzményadatok kredit-alapú, és előre fizetési előfizetés esetében nem egyeznek meg a számlán. Lásd: [előzményadatok nem feltétlenül egyeznek számla](#historical-data-might-not-match-invoice) alatt._

Az alábbi táblázat az ajánlatokat, amelyek még nem támogatott.

| Category  | **Ajánlat neve** | **Kvóta azonosítója** | **Csomag száma** |
| --- | --- | --- | --- |
| **Azure Germany** | [Azure Germany Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
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

## <a name="determine-your-offer-type"></a>Az ajánlat típusát határozza meg
Ha nem lát adatokat egy előfizetéshez, és meg szeretné határozni, ha az előfizetés a támogatott ajánlatok hatálya alá tartozik, ellenőrizheti, hogy az előfizetés támogatott. Ellenőrzése, hogy támogatott-e egy Azure-előfizetést, jelentkezzen be a [az Azure portal](https://portal.azure.com). Válassza ki **minden szolgáltatás** a bal oldali menü ablaktáblán. A szolgáltatások listájában jelölje ki **előfizetések**. Előfizetési lista menüjében kattintson az ellenőrizni kívánt előfizetést. Az előfizetés az Áttekintés lap jelenik meg, és láthatja a **ajánlat** és **Ajánlatazonosító**. Az alábbi képen egy példa látható.

![Az előfizetés – Áttekintés lap ajánlat és Ajánlatazonosító – példa](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Cost Management költségeket

Az alábbi táblázatokban foglalt, vagy nem Költségkezelés az adatokat. Minden költséget összegek becslések, amíg számla alapján jön létre. Látható költségek nem tartalmazzák az ingyenes és az előre fizetett kreditek.

**Költség- és használati adatok**

| **Foglalt** | **Nem tartalmazza** |
| --- | --- |
| Azure-szolgáltatás használati<sup>5</sup>        | Díjak – támogatja a további információt találhat [feltételek magyarázni a számlához](../billing/billing-understand-your-invoice.md). |
| Marketplace-ajánlat használati<sup>6</sup> | Adók – további információkért lásd: [feltételek magyarázni a számlához](../billing/billing-understand-your-invoice.md). |
| Piactér-beli vásárlásaimat<sup>6</sup>      | Kreditek – további információkért lásd: [feltételek magyarázni a számlához](../billing/billing-understand-your-invoice.md). |
| Foglalás vásárlások<sup>7</sup>      |  |

_<sup>**5** </sup> azure-szolgáltatás használati foglalás alapul, és egyeztetett árak._

_<sup>**6** </sup> marketplace-használat és a vásárlások nem érhetők el a használatalapú fizetés, MSDN-Előfizetők, és a Visual Studio jelenleg kínál._

_<sup>**7** </sup> foglalás vásárlások jelenleg csak nagyvállalati szerződéssel (EA) fiókokhoz érhető el._

**Metadata**

| **Foglalt** | **Nem tartalmazza** |
| --- | --- |
| Erőforráscímkék<sup>8</sup> | erőforráscímkék csoport |

_<sup>**8** </sup> erőforráscímkék érvényesek minden egyes szolgáltatás által kibocsátott használat szerint, és nem érhetők el visszamenőlegesen a korábbi használati._

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

Ha használja a [Cost Management API-k](index.yml), Power bi-ban, vagy az Azure Portalon adatok lekéréséhez, várható első újra értékelik, és ennek megfelelően módosítja, a számla bezárásáig a jelenlegi számlázási időszak fel díjat.

## <a name="usage-data-update-frequency-varies"></a>Használati adatok frissítési gyakoriság változik

A Cost Management felmerült használati adatok rendelkezésre állását több tényezőtől függ:

- Az Azure-szolgáltatások (például a tárolás, számítás, CDN és SQL) milyen gyakran küldik a használat.
- A használati adatok feldolgozása a minősítés motoron keresztül, és a költségek kezelése a folyamatok az idő.

Egyes szolgáltatások gyakoribb a többinél generuje használat. Tehát hamarabb más szolgáltatásokkal, amelyek gyakran küldik az adatokat kevesebb, mint láthatja a Cost Management adatokat az egyes szolgáltatások. Általában a szolgáltatások használati megjelennek a Cost Management 8 – 24 óra alatt. Vegye figyelembe az adatok esetében, mert az összegző frissítésekről további használati díjak egy nyílt hónap frissül.

## <a name="historical-data-might-not-match-invoice"></a>Korábbi adatok nem egyeznek meg a számlán

Előzményadatok kredit-alapú, és előre fizetési ajánlat esetén előfordulhat, hogy nem egyezik a számlán. Néhány használatalapú Azure-előfizetés, az MSDN és a Visual Studio kínál is az Azure-kreditek és a speciális kifizetések alkalmazta a számlán. A Cost Management látható előzményadatok azonban csak a becsült használati díjak alapján. A Cost Management előzményadatok nagyságú befizetések és jóváírások nem tartalmazza. Ennek eredményeképpen a korábbi adatok jelennek meg az alábbi ajánlatok nem felel meg pontosan az a számla.

- Az Azure diákoknak (MS-AZR - 0170P)
- Azure in Open licencprogram (MS-AZR - 0111P)
- Azure Pass (MS-AZR - 0120P, MS - AZR - 0123P, MS - AZR - 0125P, MS - AZR - 0128P, MS - AZR - 0129P)
- Ingyenes próbaverzió (MS-AZR - 0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Lásd még

- Ha még nem elvégezte már az első rövid Költségkezelés, olvassa el a [elemezheti a költségek](quick-acm-cost-analysis.md).
