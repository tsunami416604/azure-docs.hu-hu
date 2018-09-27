---
title: Módosítsa az Azure-előfizetési ajánlatok között |} A Microsoft Docs
description: További tudnivalók az Azure-előfizetést, és váltson át egy másik ajánlatra az Azure Account Center használatával
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing,top-support-issue
ms.assetid: aae227b3-6d64-4550-a5b6-d359f53f0a59
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: cwatson
ms.openlocfilehash: 9cb415a84a61212b8c8e3840bdc85429b5d5308c
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47391874"
---
# <a name="change-your-azure-pay-as-you-go-subscription-to-a-different-offer"></a>Az Azure használatalapú fizetéses előfizetésre váltani egy másik ajánlatra

Mint egy [utólagos elszámolású](https://azure.microsoft.com/offers/ms-azr-0003p/) ügyfél, átválthat az Azure-előfizetés egy másik ajánlatra a a [Account Center](https://account.windowsazure.com/Subscriptions). Például használhatja-e ez a funkció kihasználásához a [Visual Studio-előfizetőknek biztosított havi kreditek](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). 

**Egyszerűen szeretne váltani ingyenes próbaverzió?** Lásd: [használatalapú fizetésre frissítési](billing-upgrade-azure-subscription.md).

## <a name="whats-supported"></a>Mit támogat:

| Ettől: | Művelet |
| --- | --- |
| Utólagos, használatalapú fizetés |[Fejlesztés/tesztelés használatalapú fizetéssel](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| Utólagos, használatalapú fizetés |[Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| Utólagos, használatalapú fizetés |[Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| Utólagos, használatalapú fizetés |[MSDN-platformok](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| Utólagos, használatalapú fizetés |[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| Utólagos, használatalapú fizetés |[A Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> Más ajánlat módosítások [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="switch-subscription-offer"></a>Váltás előfizetési ajánlatok között

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. Jelentkezzen be a következő [Azure Account Center](https://account.windowsazure.com/Subscriptions).
1. Válassza a használatalapú fizetéses előfizetést.
1. Kattintson a **Váltás másik ajánlatra** elemre. A gomb csak akkor aktív, ha használat alapján fizet, és lezárult az első számlázási időszak.

   ![Figyelje meg, hogy a kapcsoló ajánlat gombra a lap jobb oldalán](./media/billing-how-to-switch-azure-offer/switchbutton.png)
1. **Válassza ki a kívánt ajánlatot** ajánlatok listájáról az előfizetés és bekapcsolható. A fiókjához tartozó tagságok attól függően változik, a listában. Ha semmi nem érhető el, ellenőrizze a [átválthat az elérhető ajánlatok listájának](#whats-supported) , és ellenőrizze, hogy a megfelelő tagságát. 

   ![Válassza ki, amelyeket szeretne váltani egy ajánlatra](./media/billing-how-to-switch-azure-offer/selectoffer.png)
1. Megjegyzés a Váltás hatásának látni az ajánlatra való váltás, attól függően. Alaposan meg ezt a listát, és kövesse az utasításokat, mielőtt továbblépne.

   ![Tekintse át a megjegyzések](./media/billing-how-to-switch-azure-offer/thingstonote.png)
1. Az előfizetés is át lehet nevezni. Alapértelmezés szerint be van állítva, az új ajánlat nevét. Kattintson a **kapcsoló ajánlat** a folyamat befejezéséhez.

   ![Kattintson a zöld gomb](./media/billing-how-to-switch-azure-offer/confirmpage.png)
1. Sikerült! Az előfizetés már át lett váltva az új ajánlat.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-is-an-azure-offer"></a>Mi az Azure-ajánlatot?

Az Azure-ajánlat a *típus* rendelkezik Azure-előfizetés. Például [utólagos elszámolású](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure in Open licencprogram](https://azure.microsoft.com/offers/ms-azr-0111p/), és [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) minden Azure-ajánlatok is. Minden ajánlat esetében különböző [feltételek](https://azure.microsoft.com/support/legal/offer-details/) , és néhány speciális előnyeit. Az ajánlat az előfizetés az Account Center-előfizetés lapján található. Kattintson a további részletekért ajánlat nevét.

   ![Kattintson az Account Center webhelyen további információért ajánlat hivatkozásra](./media/billing-how-to-switch-azure-offer/offerlink.png)

### <a name="why-dont-i-see-the-button"></a>Miért nem látom, hogy a gomb?

Ön nem láthatja a **Váltás másik ajánlatra** gomb, amennyiben:

* Ön nem on [utólagos elszámolású](https://azure.microsoft.com/offers/ms-azr-0003p/). Jelenleg csak a használatalapú fizetéses előfizetést egy másik ajánlatra konvertálhatók.
  * Ha a számítógép [ingyenes próbaverzió](https://azure.microsoft.com/free/), megtudhatja, hogyan [használatalapú fizetésre frissítési](billing-upgrade-azure-subscription.md).
  * Váltson egy másik előfizetésből ajánlat [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Az első számlázási időszak; továbbra is használ meg kell várnia, amíg az első számlázási időszak vége előtt válthat az ajánlatok.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Miért látok a "Nem találhatók nem ajánlat elérhető ország vagy régió jelenleg"?

* Ön nem jogosult bármely ajánlat kapcsolók. Ellenőrizze a [átválthat az elérhető ajánlatok listájának](#whats-supported) , és győződjön meg arról, hogy a megfelelő előnyei és a Visual Studio- vagy Bizspark aktiválta-e.
* Néhány ajánlat előfordulhat, hogy nem érhető el minden országban.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Azure-ajánlatok do átváltása szolgáltatás és a számlázási funkciója?

Az alábbiakban, mi történik, amikor az Azure kínál az Account Center webhelyen.

#### <a name="no-service-downtime"></a>A szolgáltatás leállása nélkül

Nem service jár az előfizetéshez tartozó bármely felhasználó számára. Az ajánlatra vált, azonban előfordulhat, hogy rendelkezik korlátozások. Például néhány ajánlat tiltják éles környezetben, így éles erőforrások áthelyezése egy másik előfizetésben kell.

#### <a name="quota-increases-are-reset"></a>Kvótanövelésre alapértékre állnak vissza.

Váltás során biztosít bármely [korlátot vagy kvóta növeli az alapértelmezett korlát feletti](../azure-supportability/resource-manager-core-quotas-request.md) alapértékre állnak vissza. Nem service jár, ha már rendelkezik az alapértelmezett korlát a további erőforrások. Például 200 magot használ az előfizetéséhez, majd magkvótája ajánlatok váltás visszaállítja az alapértelmezett 20 mag. A 200 magot használó virtuális érinti, és továbbra is futtassa. Ha nem választja ki egy másik kvóta növelése kérelem, azonban nem használhatók a bármely több processzormag.

#### <a name="billing"></a>Számlázás

Váltson át a nap, a számla alapján az összes ki nem egyenlített díjakat jön létre. Az előfizetés ezt követően az új ajánlat díjszabási feltételei díjszabása. A dátum, amelyen ajánlatok módosította az előfizetés számlázási évfordulós változik. Használati és számlázási adatok nem őrződnek meg az ajánlat módosítása, mielőtt ezért azt javasoljuk, hogy mielőtt átvált egy másolatának letöltése.

### <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>Át tudom telepíteni a használatalapú fizetésre, és [Cloud Solution Provider](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) vagy [nagyvállalati szerződés](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?

* CSP-hez áttelepítéséhez lásd: [CSP-hez az Azure utólagos elszámolású előfizetés Migrálása](https://docs.microsoft.com/azure/cloud-solution-provider/migration/migration-from-payg-to-csp).
* Nagyvállalati szerződéssel rendelkező át, a beléptetési rendszergazda adja hozzá a fiókot a nagyvállalati szerződés rendelkezik. Kövesse az utasításokat a meghívó e-mailben az EA-regisztrációhoz mellett előfizetéssel rendelkezik. További tudnivalókért lásd: [meglévő fiók társításához](https://ea.azure.com/helpdocs/associateExistingAccount) az EA-portálon.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Áttelepíthetek adatait és szolgáltatásait egy új előfizetést?

* Telepítse át az erőforrásokat az új előfizetés közvetlenül az is, lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md).
* Valaki más Azure-előfizetéssel, és minden benne lévő tulajdonjogának átruházása, lásd: [Azure-előfizetés tulajdonjogának átadása](billing-subscription-transfer.md)

## <a name="need-help-contact-support"></a>Segítség Forduljon az ügyfélszolgálathoz.

Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
