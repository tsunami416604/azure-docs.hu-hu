---
title: Azure-előfizetési ajánlat módosítása
description: Megtudhatja, hogyan módosíthatja Azure-előfizetését és válthat egy másik ajánlatra az Azure Fiókközpont használatával.
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 8e87bcb9f7d05fd5041623693c96f8c737758c32
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490986"
---
# <a name="change-your-azure-subscription-to-a-different-offer"></a>Azure-előfizetés egy másik ajánlatra történő módosítása

[Használatalapú fizetéses díjakat használó, egyéni előfizetéses](https://azure.microsoft.com/offers/ms-azr-0003p/) ügyfélként a [Fiókközpontban](https://account.windowsazure.com/Subscriptions) másik ajánlatra módosíthatja Azure-előfizetését. Ezzel a funkcióval például kihasználhatja a [Visual Studio-előfizetőknek járó havi jóváírást](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

**Egyszerűen csak frissíteni szeretne az ingyenes próbaverzióról?** Tekintse meg [az előfizetés frissítését](billing-upgrade-azure-subscription.md) ismertető cikket.

## <a name="whats-supported"></a>A támogatott lehetőségek:

Használatalapú fizetéses egyéni előfizetésről a következőkre válthat:

- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [MSDN-platformok](https://azure.microsoft.com/offers/ms-azr-0062p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)

> [!NOTE]
> Egyéb ajánlatmódosításokért [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="switch-subscription-offer"></a>Váltás előfizetési ajánlatok között

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. Jelentkezzen be az [Azure Fiókközpontba](https://account.windowsazure.com/Subscriptions).
1. Válassza ki a használatalapú fizetéses egyéni előfizetését.
1. Kattintson a **Váltás másik ajánlatra** elemre. A lehetőség csak akkor érhető el, ha van egy használatalapú fizetéses egyéni előfizetése, és az első számlázási időszak már lezárult.

   ![Figyelje meg az oldal jobb oldalán található Ajánlatváltás gombot](./media/billing-how-to-switch-azure-offer/switchbutton.png)
1. **Válassza ki a kívánt ajánlatot** az előfizetés-váltásra alkalmas ajánlatok listájából. A lista azon tagság alapján változik, amelyhez a fiók társítva van. Ha nincs elérhető lehetőség, tekintse meg [azon elérhető ajánlatok listáját, amelyekre átválthat](#whats-supported), és győződjön meg arról, hogy a megfelelő tagságokkal rendelkezik.

   ![Válassza ki azt az ajánlatot, amelyre váltani szeretne](./media/billing-how-to-switch-azure-offer/selectoffer.png)
1. Attól függően, hogy milyen ajánlatra vált, a váltás következményeiről is láthat egy megjegyzést. A továbblépés előtt nézze át alaposan a listát, és kövesse az utasításokat.

   ![Tekintse át a megjegyzéseket](./media/billing-how-to-switch-azure-offer/thingstonote.png)
1. Átnevezheti az előfizetését. Alapértelmezés szerint az előfizetés neve az új ajánlat neve lesz. A folyamat befejezéséhez kattintson az **Ajánlatváltás** elemre.

   ![Kattintson a zöld gombra](./media/billing-how-to-switch-azure-offer/confirmpage.png)
1. Sikerült! Az előfizetése át lett váltva az új ajánlatra.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
Az alábbi szakaszokban a gyakori kérdések válaszait találja.

### <a name="what-is-an-azure-offer"></a>Mi az az Azure-ajánlat?

Az Azure-ajánlat az Ön Azure-előfizetésének *típusa*. Azure-ajánlat például [egy használatalapú fizetéses díjakon alapuló előfizetés](https://azure.microsoft.com/offers/ms-azr-0003p/), [az Azure in Open licencprogram](https://azure.microsoft.com/offers/ms-azr-0111p/) és [a Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/). Az egyes ajánlatoknak eltérő [feltételei](https://azure.microsoft.com/support/legal/offer-details/) vannak, némelyik pedig különleges előnyökkel is rendelkezik. Az előfizetése ajánlatát a Fiókközpont Előfizetés lapján találja. További részletekért kattintson az ajánlat nevére.

   ![További részletekért kattintson az Ajánlat hivatkozásra a Fiókközpontban](./media/billing-how-to-switch-azure-offer/offerlink.png)

### <a name="why-dont-i-see-the-button"></a>Miért nem látom a gombot?

Ha nem látja a **Váltás másik ajánlatra** lehetőséget, annak oka a következő lehet:

* Nem [használatalapú fizetéses díjakon alapuló előfizetéssel](https://azure.microsoft.com/offers/ms-azr-0003p/) rendelkezik. Jelenleg csak a használatalapú fizetéses díjakon alapuló előfizetések alakíthatók át másik ajánlatra.
  * Ha [ingyenes próbaverziót](https://azure.microsoft.com/free/) használ, megtudhatja, hogyan [frissíthet használatalapú fizetésre](billing-upgrade-azure-subscription.md).
  * Ha egy másik előfizetésről szeretne ajánlatot váltani, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Még az első számlázási időszakban van, így az ajánlat váltása előtt meg kell várnia az első számlázási időszak végét.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Miért látom „Az Ön régiójában vagy országában jelenleg nincsenek elérhető ajánlatok” üzenetet?

* Előfordulhat, hogy Ön nem jogosult ajánlatváltásra. Tekintse meg [azon elérhető ajánlatok listáját, amelyekre átválthat](#whats-supported), és győződjön meg arról, hogy aktiválta a Visual Studióval vagy a BizSparkkal járó megfelelő előnyöket.
* Előfordulhat, hogy bizonyos ajánlatok nem érhetők el minden országban vagy régióban.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Hogyan befolyásolja a másik Azure-ajánlatra történő váltás a szolgáltatásokat és a számlázást?

Az alábbiakban azt láthatja, hogy mi történik, ha másik Azure-ajánlatra vált a Fiókközpontban.

#### <a name="no-service-downtime"></a>Nincs szolgáltatáskiesés

Nem jelentkezik szolgáltatáskiesés az előfizetéshez társított felhasználók számára. Előfordulhat azonban, hogy az az ajánlat, amelyre váltani szeretne, korlátozásokkal rendelkezik. Egyes ajánlatok például tiltják az éles környezetben történő használatot, így az éles erőforrásokat át kellene helyeznie egy másik előfizetésbe.

#### <a name="quota-increases-are-reset"></a>A kvótanövelések alaphelyzetbe állnak

Ha másik ajánlatra vált, a rendszer alaphelyzetbe állítja [az alapértelmezett korlátot túllépő korlát- vagy kvótanöveléseket](../azure-supportability/resource-manager-core-quotas-request.md). Nem jelentkezik szolgáltatáskiesés még akkor sem, ha az alapértelmezett korlátnál több erőforrással rendelkezik. Ha például 200 magot használ az előfizetésében, a másik ajánlatra történő váltás visszaállítja a magok kvótáját az alapértelmezett 20 magra. A 200 magot használó virtuális gépeket ez nem érinti, és továbbra is futni fognak. Ha azonban nem kér újabb kvótanövelést, nem építhet ki további magokat.

#### <a name="billing"></a>Számlázás

A váltás napján létrejön egy számla a fennálló tartozásokról. Ezután az új ajánlat díjszabási feltételei szerint megtörténik az előfizetés számlázása. Az előfizetés számlázási évfordulója az ajánlatváltás dátumára módosul. A rendszer nem őrzi meg az ajánlatváltás előtti használati és számlázási adatokat, ezért javasoljuk, hogy a váltás előtt töltsön le egy másolatot.

### <a name="can-i-migrate-from-a-subscription-with-pay-as-you-go-rates-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>Migrálhatok egy használatalapú fizetéses díjakon alapuló előfizetésből egy [Felhőszolgáltatóra](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) vagy egy [Nagyvállalati Szerződésre](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?

* A felhőszolgáltatóra történő migrálással kapcsolatban lásd a [használatalapú fizetéses Azure-előfizetés felhőszolgáltatóra történő migrálását](https://docs.microsoft.com/azure/cloud-solution-provider/migration/migration-from-payg-to-csp) ismertető cikket.
* A Nagyvállalati Szerződésre történő migráláshoz kérje meg a regisztrációs adminisztrátort, hogy adja hozzá a fiókját a Nagyvállalati Szerződéshez. Az előfizetései nagyvállalati szerződéses regisztrációba történő áthelyezéséhez kövesse a meghívó e-mailjében található utasításokat. További információkért tekintse meg a [meglévő fiókok társítását](https://ea.azure.com/helpdocs/associateExistingAccount) ismertető cikket az EA Portalon.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Migrálhatom az adatokat és a szolgáltatásokat egy új előfizetésbe?

* Az erőforrásokat közvetlenül az új előfizetésbe migrálhatja. Ehhez tekintse meg az [erőforrások új erőforráscsoportba vagy előfizetésbe történő áthelyezését](../azure-resource-manager/resource-group-move-resources.md) ismertető cikket.
* Az Azure-előfizetések tulajdonjogának és tartalmának átadásával kapcsolatos információkért tekintse meg az [Azure-előfizetés tulajdonjogának másik fióknak történő átadását](billing-subscription-transfer.md) ismertető cikket.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- [Költségelemzés indítása](../cost-management/quick-acm-cost-analysis.md)
