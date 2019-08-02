---
title: Azure-előfizetés megszüntetése | Microsoft Docs
description: Útmutató az Azure-előfizetés lemondásához, például az ingyenes próbaverziós előfizetéshez
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: banders
ms.openlocfilehash: 7756174f01e3fede17bec3e2ac185e89caddc097
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666431"
---
# <a name="cancel-your-azure-subscription"></a>Azure-előfizetés megszakítása

Ha már nincs szüksége az előfizetésre, a Azure Portal megszakíthatja az Azure-előfizetését. 

Az előfizetés megszakítása előtt:
* Biztonsági másolat készítése az adatairól. Ha például az Azure Storage-ban vagy az SQL-ben tárol adattárolást, töltsön le egy másolatot. Ha rendelkezik virtuális géppel, mentse helyi rendszerképét.
* Állítsa le a szolgáltatásokat. Nyissa meg a [felügyeleti portál erőforrások lapját](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), és **állítsa le** a futó virtuális gépeket, alkalmazásokat és egyéb szolgáltatásokat.
* Érdemes lehet áttelepíteni az adatait. Lásd: [erőforrások áthelyezése új erőforrás-csoportba vagy](../azure-resource-manager/resource-group-move-resources.md)előfizetésbe.
* Törölje az összes erőforrást és erőforráscsoportot. Az előfizetés lemondása előtt törölni kell őket. Minden erőforráscsoportot külön kell törölni. Az erőforráscsoport törlése során meg kell erősítenie a törlést az erőforráscsoport nevének beírásával.
* Ha olyan egyéni szerepkörökkel rendelkezik `AssignableScopes`, amelyek erre az előfizetésre hivatkoznak, frissítse ezeket az egyéni szerepköröket az előfizetés eltávolításához. Ha egy előfizetés megszakítása után megpróbál frissíteni egy egyéni szerepkört, hibaüzenetet kaphat. További információ: egyéni szerepkörökkel és [Egyéni szerepkörökkel](../role-based-access-control/custom-roles.md)kapcsolatos [problémák elhárítása](../role-based-access-control/troubleshooting.md#problems-with-custom-roles) az Azure-erőforrásokhoz.

Ha megszakít egy fizetős Azure-támogatási csomagot, az előfizetési időszak hátralévő részéért díjat számítunk fel. További információ: Azure- [támogatási csomagok](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Előfizetés megszakítása a Azure Portal

1. Válassza ki az előfizetést a [Azure Portal előfizetések lapján](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Válassza ki a megszüntetni kívánt előfizetést.
3. Válassza az **Áttekintés**lehetőséget, majd válassza az **előfizetés megszüntetése**lehetőséget.
    ![A Mégse gombot megjelenítő képernyőkép](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Kövesse az utasításokat, és fejezze be a megszakítást.


## <a name="who-can-cancel-a-subscription"></a>Ki törölhet egy előfizetést?

Az alábbi táblázat az előfizetés megszüntetéséhez szükséges engedélyeket ismerteti.

|Előfizetés típusa     |Ki törölhető  |
|---------|---------|
|Az Azure webhelyről való regisztráció során létrehozott előfizetések. Ha például egy [ingyenes Azure](https://azure.microsoft.com/offers/ms-azr-0044p/)-fiókra regisztrál, az utólagos elszámolású díjszabással vagy a [Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) [-](https://azure.microsoft.com/offers/ms-azr-0003p/) előfizetővel kell eljárnia. |  Az előfizetés fiókjának rendszergazdája, tulajdonosai és közreműködői  |
|[Microsoft nagyvállalati szerződés](https://azure.microsoft.com/pricing/enterprise-agreement/) és [Enterprise dev/test](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Az előfizetés fiókjának tulajdonosa, tulajdonosa és közreműködői       |
|[Azure-csomag](https://azure.microsoft.com/offers/ms-azr-0017g/) és [Azure-DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Az előfizetés tulajdonosai és közreműködői      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>Mi történik az előfizetés megszakítása után?

A megszakítás után a számlázás azonnal leállt. Azonban akár 10 percet is igénybe vehet, amíg a törlés megjelenik a portálon. Ha egy számlázási időszak közepén szakítja meg a megszakítást, az időszak lejárta után a szokásos számlázási dátummal elküldjük a végső számlát.

A megszakítást követően a szolgáltatások le vannak tiltva. Ez azt jelenti, hogy a virtuális gépek le vannak szabadítva, az ideiglenes IP-címek, a tárterület pedig csak olvasható.

Várjon 90 nappal az adatai végleges törlése előtt, ha hozzá kell férnie, vagy meggondolja magát. Nem számítunk fel díjat az adat megőrzésére. További információért lásd: [Microsoft adatvédelmi központ – az adatkezelés módja](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Előfizetés újraaktiválása

Ha véletlenül megszakítja az előfizetését az utólagos elszámolású díjszabással, akkor újra [aktiválhatja azt a accounts Centerben](billing-subscription-become-disable.md).

Ha az előfizetése nem az utólagos elszámolású díjszabású előfizetés, az előfizetés újraaktiválásához forduljon az ügyfélszolgálathoz 90 napon belül.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
