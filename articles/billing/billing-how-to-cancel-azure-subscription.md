---
title: Az Azure-előfizetés megszüntetése |} A Microsoft Docs
description: Ismerteti, hogyan lehet megszüntetni az Azure-előfizetéssel, mint például az ingyenes próba-előfizetéssel
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
ms.openlocfilehash: 8f4279d9ac085cdd1ded0dfdda4fad9d3fe12fb8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480232"
---
# <a name="cancel-your-subscription-for-azure"></a>Az Azure-előfizetés megszüntetése

Csak Azure-előfizetés [felhasználóifiók-adminisztrátor](billing-subscription-transfer.md#whoisaa) lemondhatja a szolgáltatásokat egy Azure-előfizetést. Azure-előfizetési rendszergazda is [rendelje hozzá az előfizetés-rendszergazdaként egy másik felhasználó](billing-add-change-azure-subscription-administrator.md#assign-a-user-as-an-administrator-of-a-subscription) úgy, hogy azok megszüntethet egy előfizetést. Ha lemondja az előfizetést, az Azure-szolgáltatásokhoz és erőforrásokhoz való hozzáférés véget ér.

Mielőtt az előfizetés megszüntetése:

* Készítsen biztonsági másolatot az adatairól. Ha az Azure storage vagy az SQL data tárolja, például egy másolatának letöltése. Ha egy virtuális gépet, mentse a rendszerkép helyi.
* Állítsa le a szolgáltatásokat. Nyissa meg a [erőforrások lapon, a felügyeleti portálon](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), és **leállítása** bármely futó virtuális gépek, alkalmazások és más szolgáltatásokat.
* Vegye figyelembe, hogy az adatok migrálását. Lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md).
* Törölnie kell az összes erőforrásra és erőforráscsoportra minden. Azok törlése előtt el kell azt megszüntethet egy előfizetést. Minden erőforráscsoport külön-külön kell törölni. Erőforrás-csoport törlése, során meg kell erősítenie törlését az erőforráscsoport nevének beírásával.
* Ha minden egyéni szerepkört ehhez az előfizetéshez hivatkozó `AssignableScopes`, frissítenie kell ezeket egyéni szerepkörök az előfizetés eltávolításához. Ha egy egyéni szerepkör frissítése egy előfizetés megszüntetése után meg, előfordulhat, hogy hibaüzenetet kap. További információkért lásd: [egyéni szerepkörökkel kapcsolatos problémák elhárítása](../role-based-access-control/troubleshooting.md#problems-with-custom-roles) és [egyéni szerepkörök az Azure-erőforrások](../role-based-access-control/custom-roles.md).

Ha megszakítja egy fizetős Azure-támogatási csomagra, meg kell fizetni az előfizetési időszak a többi. További információkért lásd: [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-using-the-azure-portal"></a>Előfizetés az Azure portal használatával

1. Válassza ki az előfizetését a [az Azure portal előfizetés oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Válassza ki az előfizetést, amely megszakítja.
3. Válassza ki **áttekintése**, majd válassza ki **előfizetés megszüntetése**.

    ![Képernyőkép a Mégse gomb](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Kövesse az utasításokat, és fejezze be a törlés.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Mi történik a lemondom az előfizetésemet?

Megszakítja a műveletet, miután a számlázás megáll azonnal. Azonban a megszüntetés a portálon megjeleníteni kívánt akár 10 percet is igénybe vehet. Ha megszakítja egy számlázási időszak közepén, számla küldünk a tipikus invoice Date a lejárta után.

Megszakítja a műveletet, miután a szolgáltatások le vannak tiltva. Ez azt jelenti, hogy a virtuális gépek felszabadítva, ideiglenes IP-címek felszabadítását, és tároló csak olvasható.

Hogy Várjon, amíg a 90 nappal korábbinak véglegesen törli az adatokat, abban az esetben kell-e férni, vagy megváltoztatja döntését. A Microsoft nem számítunk fel díjat, az adatok megőrzése. További tudnivalókért lásd: [Microsoft Trust Center – hogyan kezeljük adatait](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Előfizetés újraaktiválása

Ha véletlenül megszakítja a használatalapú fizetéses előfizetést, akkor [aktiválja a fiókok központban](billing-subscription-become-disable.md).

Ha az előfizetés nem használatalapú, forduljon az ügyfélszolgálathoz megszakítás és aktiválja újra előfizetését 90 napon belül.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
