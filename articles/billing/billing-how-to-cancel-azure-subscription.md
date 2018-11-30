---
title: Az Azure-előfizetés megszüntetése |} A Microsoft Docs
description: Ismerteti, hogyan lehet megszüntetni az Azure-előfizetéssel, mint például az ingyenes próba-előfizetéssel
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: cwatson
ms.openlocfilehash: de5fd6e52ab93132920b1c98188cbea92b9900b5
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584349"
---
# <a name="cancel-your-subscription-for-azure"></a>Az Azure-előfizetés megszüntetése

Az Azure-előfizetéshez megszakíthatja a [felhasználóifiók-adminisztrátor](billing-subscription-transfer.md#whoisaa). Ha lemondja az előfizetést, az Azure-szolgáltatásokhoz és erőforrásokhoz való hozzáférés véget ér.

Mielőtt az előfizetés megszüntetése:

* Készítsen biztonsági másolatot az adatairól. Ha az Azure storage vagy az SQL data tárolja, például egy másolatának letöltése. Ha egy virtuális gépet, mentse a rendszerkép helyi.
* Állítsa le a szolgáltatásokat. Nyissa meg a [erőforrások lapon, a felügyeleti portálon](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), és **leállítása** bármely futó virtuális gépek, alkalmazások és más szolgáltatásokat.
* Vegye figyelembe, hogy az adatok migrálását. Lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md).

Ha megszakítja egy fizetős Azure-támogatási csomagra, meg kell fizetni az előfizetési időszak a többi. További információkért lásd: [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-using-the-azure-portal"></a>Előfizetés az Azure portal használatával

1. Válassza ki az előfizetését a [az Azure portal előfizetés oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Válassza ki az előfizetést, szakítsa meg és kattintson a kívánt **előfizetés megszüntetése**.

    ![Képernyőkép a Mégse gomb](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
1. Kövesse az utasításokat, és fejezze be a törlés.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Mi történik a lemondom az előfizetésemet?

Ha megszakítja a műveletet, a számlázás megáll azonnal. Azonban a megszüntetés a portálon megjeleníteni kívánt akár 10 percet is igénybe vehet.

Ezt követően a szolgáltatások le vannak tiltva. Ez azt jelenti, hogy a virtuális gépek felszabadítva, ideiglenes IP-címek felszabadítását, és tároló csak olvasható.

Ha megszakítja egy számlázási időszak közepén, számla küldünk a tipikus invoice Date a lejárta után. 

Hogy Várjon, amíg a 90 nappal korábbinak véglegesen törli az adatokat, abban az esetben kell-e férni, vagy megváltoztatja döntését. A Microsoft nem számítunk fel díjat, az adatok megőrzése. További tudnivalókért lásd: [Microsoft Trust Center – hogyan kezeljük adatait](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Előfizetés újraaktiválása

Ha véletlenül megszakítja a használatalapú fizetéses előfizetést, akkor [aktiválja a fiókok központban](billing-subscription-become-disable.md).

Ha az előfizetés nem használatalapú, forduljon az ügyfélszolgálathoz megszakítás és aktiválja újra előfizetését 90 napon belül.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
