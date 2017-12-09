---
title: "Módosítsa az Azure-előfizetés ajánlat |} Microsoft Docs"
description: "További tudnivalók az Azure-előfizetés módosítása, és váltson egy másik ajánlatra Azure Account Center használata"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: aae227b3-6d64-4550-a5b6-d359f53f0a59
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: 381994079b7bcaeff08802b06573b977bf631e9d
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="change-your-azure-pay-as-you-go-subscription-to-a-different-offer"></a>Módosítsa az Azure használatalapú fizetés előfizetés egy másik ajánlatra

Mint egy [használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/) vevő, váltson az Azure-előfizetéshez a másik ajánlatra a [Account Center](https://account.windowsazure.com/Subscriptions). Például használhatja ez a funkció előnyeit a [Visual Studio-előfizetők a havi kreditek](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). 

**Most frissíteni szeretné a ingyenes próbaverziójáról?** Lásd: [váltson használatalapú fizetés](billing-upgrade-azure-subscription.md).

## <a name="whats-supported"></a>Támogatott források és műveletek:

| Ettől | Művelet |
| --- | --- |
| Utólagos, használatalapú fizetés |[Fejlesztés/tesztelés – használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| Utólagos, használatalapú fizetés |[A Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| Utólagos, használatalapú fizetés |[A Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| Utólagos, használatalapú fizetés |[MSDN platformok](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| Utólagos, használatalapú fizetés |[A Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| Utólagos, használatalapú fizetés |[A Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> Más ajánlat módosítások [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="switch-subscription-offer"></a>Kapcsoló előfizetés ajánlat

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. Jelentkezzen be a(z) [Azure Account Center](https://account.windowsazure.com/Subscriptions).
1. Válassza a használatalapú fizetéses előfizetést.
1. Kattintson a **Váltás másik ajánlatra** elemre. A gomb csak akkor aktív, ha használat alapján fizet, és lezárult az első számlázási időszak.

   ![Figyelje meg az ajánlat váltás a lap jobb oldalán](./media/billing-how-to-switch-azure-offer/switchbutton.png)
1. **Válassza ki a kívánt ajánlatot** ajánlatok listája és az előfizetés bekapcsolható. Ebben a listában a fiókjához társított tagságát függ. Ha nem érhető el, ellenőrizze a [átválthat elérhető ajánlatok listájának](#whats-supported) , és győződjön meg arról, hogy a megfelelő tagságát. 

   ![Válassza ki, amelyet szeretne váltani egy ajánlatot](./media/billing-how-to-switch-azure-offer/selectoffer.png)
1. Attól függően, hogy az ajánlat vált a Váltás hatását megjegyzés jelenhet meg. Gondosan halad át a listában, és kövesse az utasításokat, mielőtt továbblép.

   ![Tekintse át a megjegyzések](./media/billing-how-to-switch-azure-offer/thingstonote.png)
1. Átnevezheti az előfizetéshez. Alapértelmezés szerint azt állítani az új ajánlat nevére. Kattintson a **kapcsoló kínálnak** a folyamat befejezéséhez.

   ![A zöld gombra.](./media/billing-how-to-switch-azure-offer/confirmpage.png)
1. Sikeres bejelentkezés. Az előfizetés már át lett váltva az új ajánlat.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-is-an-azure-offer"></a>Mi az az Azure-ajánlat?

Egy Azure-ajánlat a *típus* rendelkezik Azure-előfizetés. Például [használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure in Open licencprogram](https://azure.microsoft.com/offers/ms-azr-0111p/), és [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) van minden Azure ajánlatokat. Minden egyes ajánlat rendelkezik különböző [feltételek](https://azure.microsoft.com/support/legal/offer-details/) , és néhány speciális előnyeit. Az ajánlat az előfizetés a Account Center-előfizetés lapján találhatók. Kattintson a további részletek a ajánlat nevére.

   ![Az ajánlat hivatkozásra Account Center részleteinek](./media/billing-how-to-switch-azure-offer/offerlink.png)

### <a name="why-dont-i-see-the-button"></a>Miért nem látom, hogy a gomb?

Előfordulhat, nem látható a **Váltás másik ajánlatra** gombra kattint, ha:

* Ön nem on [használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/). Jelenleg csak a használatalapú fizetés előfizetések konvertálható másik ajánlatra.
  * Ha a számítógép [ingyenes](https://azure.microsoft.com/free/), megtudhatja, hogyan [váltson használatalapú fizetés](billing-upgrade-azure-subscription.md).
  * Egy másik előfizetésben ajánlatot váltani [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Ön továbbra is az első számlázási időszak; Várjon, amíg az első számlázott időszak előtt megváltoztathatja az ajánlatok befejezéséhez.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Miért látom azt "Nincsenek nincsenek ajánlatok elérhető az Ön régióban vagy ország most"?

* Nem lehet abban az esetben jogosult a ajánlat kapcsolóival. Ellenőrizze a [átválthat elérhető ajánlatok listájának](#whats-supported) és győződjön meg arról, hogy a megfelelő előnyei és a Visual Studio vagy Bizspark hogy aktiválta.
* Néhány ajánlatok nem érhetők el minden országban.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Váltás az Azure-ajánlatok tegye a szolgáltatás és a számlázási funkciója?

Mi történik, amikor az Azure kínál az Account Center a részleteit az alábbiakban.

#### <a name="no-service-downtime"></a>A szolgáltatás állásidő nélkül

Nincs állásidő nélkül szolgáltatás az előfizetéshez tartozó bármely felhasználó számára. Az ajánlat való váltás azonban korlátozások is rendelkezhet. Például, néhány ajánlatok éles környezetben való használathoz, amelyek így éles erőforrások áthelyezése egy másik előfizetést kell.

#### <a name="quota-increases-are-reset"></a>Kvóta növekszik állnak vissza.

Váltás során ajánlatokat, minden [korlátot vagy kvóta meghaladja az alapértelmezett növeli](../azure-supportability/resource-manager-core-quotas-request.md) állnak vissza. Nincs állásidő nélkül szolgáltatást, akkor is, ha az alapértelmezett határérték túl további erőforrások vannak. Például használata 200 magok az előfizetését, majd ajánlatok váltás visszaállítja a magok kvótája az alapértelmezett 20 mag. A 200 mag használó virtuális gépeket nem érinti, és továbbra is futtassa. Ha egy másik kvóta növelése a kérelem nem végez, azonban nem használhatók a bármely több maggal.

#### <a name="billing"></a>Számlázás

Váltás napon számla generál a rendszer minden díjhátralékot. Az előfizetés, majd az új ajánlat díjszabási feltételei / lesz számlázva. A dátum, amelyen ajánlatok módosította az előfizetés számlázási évforduló módosításait. Használati és számlázási adatok előtt az ajánlat nem maradnak, ezért azt javasoljuk, hogy töltse le a Másolás váltása előtt.

### <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>Telepíthetek át használatalapú fizetésre a [Cloud Solution Provider](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) vagy [nagyvállalati szerződés](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?

* Kriptográfiai Szolgáltató áttelepítéséhez lásd: [Azure szolgáltatói címei-,-akkor-Ugrás előfizetés áttelepítési CSP-hez](https://docs.microsoft.com/azure/cloud-solution-provider/migration/migration-from-payg-to-csp).
* EA át, a beléptetési rendszergazda adja hozzá a fiókot a EA rendelkezik. Kövesse az utasításokat a meghívó e-mailben a mellett EA beléptetési előfizetéssel rendelkezik. További tudnivalókért lásd: [társítja a meglévő fiók](https://ea.azure.com/helpdocs/associateExistingAccount) a EA portálon.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Telepíthetek át adatokat és szolgáltatásokat az új előfizetés?

* Telepítse át az erőforrásokat az új előfizetésbe való közvetlenül, lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md).
* Valaki más Azure-előfizetéssel, és minden benne tulajdonjogának átruházása, lásd: [Azure-előfizetés tulajdonjogának átadása](billing-subscription-transfer.md)

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha további kérdései további, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.
