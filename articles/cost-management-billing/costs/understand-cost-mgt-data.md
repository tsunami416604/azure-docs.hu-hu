---
title: Azure Cost Management-adatok ismertetése | Microsoft Docs
description: Ez a cikk segít az Azure Cost Managementben található adatok, valamint azok feldolgozási, gyűjtési, megjelenítési és lezárási gyakoriságának jobb megértésében.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/21/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: micflan
ms.custom: ''
ms.openlocfilehash: 44953a3986b5c03afa9cc4668e2563c5c5cd6c46
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560609"
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

## <a name="how-tags-are-used-in-cost-and-usage-data"></a>Címkék használata költség- és használati adatokban

Az Azure Cost Management a címkéket az egyes szolgáltatások által küldött használati adatok részeként fogadja. Ezekre a címkékre a következő korlátozások érvényesek:

- A címkéket közvetlenül az erőforrásokra kell alkalmazni, és nem öröklődnek implicit módon a szülő erőforráscsoportból.
- Az erőforráscímkék csak az erőforráscsoportokban üzembe helyezett erőforrások esetén támogatottak.
- Előfordulhat, hogy néhány üzembe helyezett erőforrás nem támogatja a címkéket vagy nem tartalmaz címkéket a használati adatokban – lásd: [Címkék támogatása az Azure-erőforrásokban](../../azure-resource-manager/tag-support.md).
- Az erőforráscímkék csak a címke alkalmazásának idején szerepelnek a használati adatokban, így az előzményadatokra nem lesznek alkalmazva.
- Az erőforráscímkék csak az adatok frissítése után érhetők el a Cost Managementben – lásd: [A használati adatok frissítési gyakorisága változó](#usage-data-update-frequency-varies).
- Az erőforráscímkék csak akkor érhetők el a Cost Managementben, ha az erőforrás aktív/fut, és használati adatokat hoz létre (például akkor nem, ha a virtuális gép felszabadított állapotban van).
- A címkék kezeléséhez minden erőforrás esetében közreműködői hozzáférés szükséges.
- A címkeszabályzatok kezeléséhez tulajdonosi vagy szabályzat-közreműködői hozzáférés szükséges egy felügyeleti csoporthoz, egy előfizetéshez vagy egy erőforráscsoporthoz.
    
Ha nem talál egy adott címkét a Cost Managementben, vegye figyelembe a következőket:

- A címke közvetlenül az erőforrásra lett alkalmazva?
- A címke több mint 24 órával ezelőtt lett alkalmazva? Lásd: [A használati adatok frissítési gyakorisága változó](#usage-data-update-frequency-varies)
- Támogatja az erőforrás típusa a címkéket? Az alábbi erőforrástípusok 2019. december 1-től nem támogatják a címkéket a használati adatokban. A támogatott címkék teljes listája: [Címkék támogatása az Azure-erőforrásokban](../../azure-resource-manager/tag-support.md).
    - Azure Active Directory B2C-címtárak
    - Azure-tűzfalak
    - Azure NetApp Files
    - Data Factory
    - Databricks
    - Terheléselosztók
    - Network Watcher
    - Notification Hubs
    - Service Bus
    - Time Series Insights
    - VPN-átjáró
    
Íme, néhány tipp a címkék használatához:

- Előre tervezhet és meghatározhat egy címkézési stratégiát, amely lehetővé teszi a költségek szervezet, alkalmazás, környezet stb. szerinti lebontását.
- Az Azure Policy használatával az erőforráscsoportok címkéit egyéni erőforrásokra másolhatja, és biztosíthatja a címkézési stratégia betartását.
- A Tags API és a Query vagy a UsageDetails API együttes használatával az összes költség lekérhető az aktuális címkék alapján.


## <a name="free-trial-to-pay-as-you-go-upgrade"></a>Ingyenes próbalehetőség használatalapú fizetésre való frissítéshez

Ha többet szeretne tudni az ingyenes szintű szolgáltatások rendelkezésre állásáról, miután frissített az ingyenes próbaverzióról a használatalapú fizetéses verzióra, tekintse meg az [ingyenes Azure-fiókkal kapcsolatos gyakori kérdéseket](https://azure.microsoft.com/free/free-account-faq/).

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

## <a name="cost-rounding"></a>Költségek kerekítése

A Cost Managementben megjelenített költségek kerekítve vannak. A Query API által visszaadott költségek nincsenek kerekítve. Például:

- Költségelemzés az Azure Portalon – A díjak kerekítése standard kerekítési szabályok szerint történik: a 0,5-nél nagyobb értékeket felfelé, az egyéb költségeket lefelé kerekíti a rendszer. Kerekítés csak értékek megjelenítésekor történik. Az adatfeldolgozás és -összesítés során nem történik kerekítés. A költségelemzés a következőképpen összesíti a költségeket:
  - 1\. díj: 0,004 dollár
  - 2\. díj: 0,004 dollár
  - A díjak összesítésének megjelenítése: 0,004 + 0,004 = 0,008. A megjelenő díj 0,01 dollár.
- Query API – A díj nyolc tizedesjegyig jelenik meg, és nem történik kerekítés.


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
