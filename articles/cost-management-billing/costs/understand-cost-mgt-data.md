---
title: Azure Cost Management-adatok ismertetése | Microsoft Docs
description: Ez a cikk segít az Azure Cost Managementben található adatok, valamint azok feldolgozási, gyűjtési, megjelenítési és lezárási gyakoriságának jobb megértésében.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: micflan
ms.custom: ''
ms.openlocfilehash: 156684676758d777231d3b159ba7bc4749b8582a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901758"
---
# <a name="understand-cost-management-data"></a>A Cost Management adatainak értelmezése

Ez a cikk segít az Azure az Azure Cost Managementben található költség- és használati adatainak jobb megértésében. Ismerteti az adatok feldolgozásának, gyűjtésének, megjelenítésének és lezárásának gyakoriságát. Az Azure-használat számlázása havonta történik. Bár a számlázási ciklusok havi időszakok, a ciklusok kezdő- és záródátuma előfizetési típusonként eltér. Az, hogy a Cost Management milyen gyakran kap használati adatokat, különböző tényezőktől függ. Az ilyen tényezők közé tartozik például az, hogy mennyi ideig tart az adatok feldolgozása, illetve hogy az Azure-szolgáltatások milyen gyakran szolgáltatnak használati adatokat a számlázási rendszernek.

A Cost Management magában foglalja az összes használatot és vásárlást, beleértve a Nagyvállalati Szerződéses (EA-) fiókokhoz tartozó foglalásokat és külső ajánlatokat. A Microsoft Ügyfélszerződéshez tartozó fiókok és használatalapú díjszabású egyéni előfizetések csak az Azure- és Marketplace-szolgáltatások használatát tartalmazzák. A támogatási és egyéb költségeket nem tartalmazza. A számla létrehozásáig a költségek csak becsült összegek, és nem veszik figyelembe a jóváírásokat.

## <a name="supported-microsoft-azure-offers"></a>Támogatott Microsoft Azure-ajánlatok

Az alábbi információk azokat a [Microsoft Azure-ajánlatokat](https://azure.microsoft.com/support/legal/offer-details/) jelenítik meg, amelyek az Azure Cost Managementben jelenleg támogatottak. Az Azure-ajánlat az Ön Azure-előfizetésének típusa. Az adatok az **Adatok elérhetőségének kezdete** dátumtól kezdődően érhetők el a Cost Managementben. Ha egy előfizetés másik ajánlatra vált, akkor a másik ajánlatra való váltás dátuma előtti költségek nem érhetők el.

| **Kategória**  | **Ajánlat neve** | **Kvótaazonosító** | **Ajánlatszám** | **Adatok elérhetőségének kezdete** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | 2014. május<sup>1</sup> |
| **Nagyvállalati Szerződés (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | 2014. május<sup>1</sup> |
| **Nagyvállalati Szerződés (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | 2014. május<sup>1</sup> |
| **Microsoft-ügyfélszerződés** | [Microsoft Azure-csomag](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N/A | 2019. március<sup>3</sup> |
| **Microsoft-ügyfélszerződés** | [Microsoft Azure-csomag fejlesztéshez/teszteléshez](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | N/A | 2019. március<sup>3</sup> |
| **Partnerek által támogatott Microsoft Ügyfélszerződés** | Microsoft Azure-csomag | CSP_2015-05-01, CSP_MG_2017-12-01 és CSPDEVTEST_2018-05-01<br><br>A rendszer a Microsoft Ügyfélszerződéses és az örökölt CSP-előfizetések esetében újrahasználja a kvótaazonosítót. Jelenleg csak a Microsoft Ügyfélszerződéses előfizetések támogatottak. | N/A | 2019. október |
| **Microsoft Developer Network (MSDN)** | [MSDN-platformok](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2018. október 2.<sup>2</sup> |
| **Használatalapú fizetés** | [Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2018. október 2.<sup>2</sup> |
| **Használatalapú fizetés** | [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2018. október 2.<sup>2</sup> |
| **Használatalapú fizetés** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2018. október 2.<sup>2</sup> |
| **Használatalapú fizetés** | [Ingyenes próba](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2018. október 2.<sup>2</sup> |
| **Használatalapú fizetés** | [Azure in Open licencprogram](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2018. október 2.<sup>2</sup> |
| **Használatalapú fizetés** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P – MS-AZR-0125P, MS-AZR-0128P – MS-AZR-0130P | 2018. október 2.<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2018. október 2.<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2018. október 2.<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2018. október 2.<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2018. október 2.<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2018. október 2.<sup>2</sup> |

_<sup>**1**</sup> A 2014 májusa előtti adatokért látogasson el az [Azure Enterprise Portalra](https://ea.azure.com)._

_<sup>**2**</sup> A 2018. október 2. előtti adatokért látogasson el az [Azure Fiókközpontba](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> A Microsoft Ügyfélszerződések 2019 márciusában kezdődtek, így ennél korábbi adatokkal nem rendelkeznek._

_<sup>**4**</sup> Előfordulhat, hogy a kreditalapú és előre fizetett előfizetések korábbi adatai nem egyeznek meg a számlán szereplő adatokkal. Lásd lejjebb: [Előfordulhat, hogy a korábbi adatok nem egyeznek meg a számlán szereplő adatokkal](#historical-data-might-not-match-invoice)._

Az alábbi ajánlatok még nem támogatottak:

| Kategória  | **Ajánlat neve** | **Kvótaazonosító** | **Ajánlatszám** |
| --- | --- | --- | --- |
| **Azure Germany** | [Azure Germany – használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure Government – használatalapú fizetés | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **Felhőszolgáltató (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Felhőszolgáltató (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Felhőszolgáltató (CSP)** | Azure Germany – CSP for Microsoft Cloud Germany   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Használatalapú fizetés**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Használatalapú fizetés** | [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Használatalapú fizetés**                 | [Microsoft Azure szponzorálás](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Támogatási csomagok** | Standard szintű támogatás                    | Default_2014-09-01 | MS-AZR-0041P |
| **Támogatási csomagok** | Közvetlen professzionális támogatás         | Default_2014-09-01 | MS-AZR-0042P |
| **Támogatási csomagok** | Fejlesztői támogatás                   | Default_2014-09-01 | MS-AZR-0043P |
| **Támogatási csomagok** | Németországi támogatási csomag                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Támogatási csomagok** | Azure Government Standard Support   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Támogatási csomagok** | Azure Government Pro-Direct Support | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Támogatási csomagok** | Azure Government Developer Support  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Az ajánlattípus meghatározása
Ha nem látja az előfizetéshez tartozó adatokat, és meg szeretné állapítani, hogy az előfizetése a támogatott ajánlatok közé tartozik-e, ellenőrizheti az előfizetés támogatottságát. Annak ellenőrzéséhez, hogy egy adott Azure-előfizetés támogatott-e, jelentkezzen be az [Azure Portalra](https://portal.azure.com). A bal oldali menüpanelen válassza a **Minden szolgáltatás** lehetőséget. A szolgáltatások listájában válassza az **Előfizetések** elemet. Az előfizetések listájának menüjében válassza ki az ellenőrizni kívánt előfizetést. Az előfizetés megjelenik az Áttekintés lapon, ahol az **Ajánlatot** és az **Ajánlat azonosítóját** is láthatja. Az alábbi képen egy példa látható.

![Az előfizetés Áttekintés lapját az Ajánlattal és az Ajánlat azonosítójával együtt mutató példa](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>A Cost Managementben elérhető költségek

Az alábbi táblázatok azt mutatják, hogy a Cost Management mely adatokat tartalmazza, illetve melyeket nem. A számla létrehozásáig minden költség csak becsült költség. A feltüntetett költségek nem tartalmazzák az ingyenes és előre fizetett jóváírásokat.

**Költség- és használati adatok**

| **Tartalmazza** | **Nem tartalmazza** |
| --- | --- |
| Azure-szolgáltatás használata<sup>5</sup>        | Támogatási díjak – További információért tekintse meg a [számlában használatos kifejezések ismertetését](../understand/understand-invoice.md). |
| Marketplace-ajánlatok használati adatai<sup>6</sup> | Adók – További információért tekintse meg a [számlában használatos kifejezések ismertetését](../understand/understand-invoice.md). |
| Marketplace-vásárlások<sup>6</sup>      | Jóváírások – További információért tekintse meg a [számlában használatos kifejezések ismertetését](../understand/understand-invoice.md). |
| Foglalásvásárlások<sup>7</sup>      |  |
| A foglalásvásárlások amortizációja<sup>7</sup>      |  |

_<sup>**5**</sup> Az Azure-szolgáltatás használata a foglaláson és a megegyezés szerinti árakon alapul._

_<sup>**6**</sup> A Marketplace-vásárlások jelenleg nem érhetők el használatalapú fizetéses, MSDN- és Visual Studio-ajánlatokhoz._

_<sup>**7**</sup> A foglalásvásárlások jelenleg csak Nagyvállalati Szerződéses (EA-) fiókokhoz érhetők el._

**Metaadatok**

| **Tartalmazza** | **Nem tartalmazza** |
| --- | --- |
| Erőforráscímkék<sup>8</sup> | Erőforráscsoport-címkék |

_<sup>**8**</sup> A rendszer a használati adatok egyes szolgáltatásokból való kibocsátásával egyidejűleg alkalmazza az erőforráscímkéket, amelyek nem érhetők el visszamenőlegesen a korábbi használathoz._

**Ingyenes próbalehetőség használatalapú fizetésre való frissítéshez**

Az ingyenes próbalehetőségre vonatkozó ajánlattal (044P) rendelkező, használatalapú fizetéses ajánlatra (003P) váltó ügyfelek megtekinthetik az ingyenes próbaidőszak alatti használatot. A váltás után azonban nem fogják látni az ingyenes próbaidőszak használati adatait. A váltás után csak a használatalapú fizetéses használati adatok és költségek jelennek meg a Cost Managementben.

## <a name="rated-usage-data-refresh-schedule"></a>Kiszámított használati adatok frissítési ütemterve

A költség- és használati adatok a Költségkezelés + számlázás területen érhetők el az Azure Portalon és a [támogató API-kban](../index.yml). A költségek áttekintésekor tartsa szem előtt a következő szempontokat:

- Az aktuális számlázási időszak becsült költségei naponta hatszor frissülnek.
- Az egyre több használattal az aktuális számlázási időszak becsült költségei változhatnak.
- Az egyes frissítések kumulatívak, így az előző frissítésből származó összes sorelemet és információt tartalmazzák.
- Az Azure a számlázási időszak vége után legfeljebb 72 órával véglegesíti vagy _zárja le_ az aktuális számlázási időszakot.

Az alábbi példák bemutatják, hogyan érhetnek véget a számlázási időszakok.

Nagyvállalati Szerződéses (EA-) előfizetések – Ha a számlázási hónap május 31-én ér véget, a becsült díjak legfeljebb 72 órával később frissülnek. Ebben a példában április 4. éjfélig (UTC).

Használatalapú fizetéses előfizetések – Ha a számlázási hónap május 15-én ér véget, előfordulhat, hogy a becsült díjak legfeljebb 72 órával később frissülnek. Ebben a példában május 19. éjfélig (UTC).

### <a name="rerated-data"></a>Újraszámolt adatok

Függetlenül attól, hogy a [Cost Management API-k](../index.yml), a Power BI vagy az Azure Portal használatával kéri le az adatokat, számítson arra, hogy a jelenlegi számlázási időszakhoz tartozó díjakat a rendszer újraszámolja, ezért azok módosulnak, amíg a rendszer le nem zárja a számlát.

## <a name="usage-data-update-frequency-varies"></a>A használati adatok frissítési gyakorisága eltér

A felmerült használati adatok Cost Management-beli elérhetősége több tényezőtől függ, például:

- Attól, hogy az Azure-szolgáltatások (például a Storage, a Compute, a CDN és az SQL) milyen gyakran bocsátanak ki használati adatokat.
- A használati adatok számítási motoron és költségkezelési folyamaton keresztül történő feldolgozásához szükséges időtől.

Egyes szolgáltatások gyakrabban szolgáltatnak használati adatokat, mint mások. Így előfordulhat, hogy egyes szolgáltatások esetében hamarabb látja az adatokat a Cost Managementben, mint azok esetében, amelyek ritkábban bocsátanak ki használati adatokat. A szolgáltatások használati adatai általában 8-24 óra alatt jelennek meg a Cost Managementben. Ne feledje, hogy az egyre több használattal a nyílt hónapokhoz tartozó adatok frissülnek, mivel a frissítések kumulatívak.

## <a name="historical-data-might-not-match-invoice"></a>Előfordulhat, hogy a korábbi adatok nem egyeznek meg a számlán szereplő adatokkal

Előfordulhat, hogy a kreditalapú és előre fizetett ajánlatok korábbi adatai nem egyeznek meg a számlán szereplő adatokkal. Bizonyos Azure-beli használatalapú fizetéses, MSDN- és Visual Studio-ajánlatok esetében lehetséges, hogy Azure-kreditek és előzetes kifizetések vannak alkalmazva a számlára. A Cost Managementben megjelenített korábbi adatok azonban csak a becsült használati díjakon alapulnak. A Cost Management korábbi adatai nem tartalmazzák a kifizetéseket és krediteket. Így előfordulhat, hogy az alábbi ajánlatok korábbi adatai nem egyeznek meg pontosan a számlán szereplő adatokkal.

- Azure for Students (MS-AZR-0170P)
- Azure in Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Ingyenes próba (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Lásd még

- Ha még nem végezte el a Cost Management első lépéseit, itt megtekintheti a [költségelemzés elkezdésének](../../cost-management/quick-acm-cost-analysis.md) lépéseit.
