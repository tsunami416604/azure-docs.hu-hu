---
title: Az Azure-előfizetés lemondása
description: Ismerteti, hogyan mondható le az Azure-előfizetés, például az ingyenes próba előfizetése
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: banders
ms.openlocfilehash: 2d8b529683f4d07bc47010711d65ca40536823fb
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996565"
---
# <a name="cancel-your-azure-subscription"></a>Az Azure-előfizetés lemondása

Ha többé nincs szüksége Azure-előfizetésre, lemondhatja azt az Azure Portalon.

Bár nem kötelező, a Microsoft *javasolja*, hogy végezze el az alábbi műveleteket az előfizetés lemondása előtt:

* Készítsen az adatokról biztonsági másolatot. Ha például az Azure Storage-ban vagy SQL-ben tárol adatokat, töltsön le róluk egy másolatot. Ha virtuális géppel rendelkezik, helyben mentsen róla egy rendszerképet.
* Állítsa le a szolgáltatásokat. Lépjen a [felügyeleti portál erőforrások oldalára](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), és **állítsa le** az összes aktuálisan futó virtuális gépet, alkalmazást, és egyéb szolgáltatást.
* Fontolja meg adatai migrálását. Lásd [az erőforrások új erőforráscsoportba vagy előfizetésbe való áthelyezését](../../azure-resource-manager/management/move-resource-group-and-subscription.md) ismertető témakört.
* Törölje az összes erőforrást és erőforráscsoportot.
* Ha bármilyen egyéni szerepkörrel rendelkezik, amely erre az előfizetésre hivatkozik az `AssignableScopes` alatt, akkor az előfizetés eltávolításához frissítenie kell az egyéni szerepköröket. Ha az előfizetés lemondása után próbál frissíteni egy egyéni szerepkört, lehetséges, hogy hibaüzenetet kap. További információkért tekintse meg az [Egyéni szerepkörökkel felmerülő problémák hibaelhárítása](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) és az [Egyéni szerepkörök Azure-erőforrásokhoz](../../role-based-access-control/custom-roles.md) cikkeket.

Ha egy fizetett Azure-támogatási csomagot mond le, az előfizetési időszak hátralévő ideje továbbra is ki lesz számlázva. További információkért tekintse meg az [Azure támogatási csomagokat](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Előfizetés lemondása az Azure Portalon

1. Válassza ki előfizetését az [Azure Portal Előfizetések oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Válassza ki a lemondani kívánt előfizetést.
3. Válassza az **Áttekintés** lehetőséget, majd az**Előfizetés lemondása** elemet.
    ![A Lemondás gombot megjelenítő képernyőkép](./media/cancel-azure-subscription/cancel_ibiza.png)
3. Kövesse az utasításokat a lemondás befejezéséhez.

## <a name="who-can-cancel-a-subscription"></a>Ki mondhat le előfizetést?

Az alábbi táblázat ismerteti az előfizetés lemondásához szükséges engedélyeket.

|Előfizetés típusa     |Ki mondhatja le  |
|---------|---------|
|Az Azure webhelyen keresztül az Azure-ba való regisztrációkor létrehozott előfizetések. Erre példa az [ingyenes Azure-fiók](https://azure.microsoft.com/offers/ms-azr-0044p/), a [használatalapú fizetést használó fiók](https://azure.microsoft.com/offers/ms-azr-0003p/) és a [Visual Studio-előfizetés](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  A fiókadminisztrátor és az előfizetés tulajdonosai  |
|[Microsoft Nagyvállalati Szerződés](https://azure.microsoft.com/pricing/enterprise-agreement/) és [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  A fióktulajdonos és az előfizetés tulajdonosai       |
|[Azure-csomag](https://azure.microsoft.com/offers/ms-azr-0017g/) és [Azure-csomag DevTesthez](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Az előfizetés tulajdonosai      |

## <a name="what-happens-after-i-cancel-my-subscription"></a>Mi történik az előfizetés lemondása után?

A lemondás után a számlázás azonnal leáll. Akár 10 percet is igénybe vehet azonban, hogy a lemondás a Portalon is megjelenjen. Ha az előfizetést a számlázási időszak közepén mondja le, az utolsó számlát az időszak lejárta után, a szokásos számlázási időpontban küldjük el. 

A szolgáltatások a lemondás után le lesznek tiltva. Ez azt jelenti, hogy virtuális gépei felszabadultak, az ideiglenes IP-címek szabadok, a tárterület pedig csak olvasható.

A Microsoft 30–90 napot vár adatai törlésével, hogy szükség esetén hozzájuk tudjon férni, vagy arra az esetre, ha meggondolná magát. Adatai megőrzésért nem számolunk fel díjat. További tudnivalókért lásd a [Microsoft Adatvédelmi központ az adatok kezelését](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) ismertető cikkét.

## <a name="reactivate-subscription"></a>Előfizetés újraaktiválása

Ha véletlenül lemondja a használatalapú fizetéses előfizetését, [a Fiókközpontban újraaktiválhatja azt](subscription-disabled.md).

Ha előfizetése nem használatalapú fizetéses előfizetés, akkor a lemondás utáni 90 napon belül újraaktiválhatja előfizetését, ha felveszi a kapcsolatot az ügyfélszolgálattal.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- Szükség esetén újraaktiválhatja a használatalapú fizetéses előfizetést a [Fiókközpontban](subscription-disabled.md).