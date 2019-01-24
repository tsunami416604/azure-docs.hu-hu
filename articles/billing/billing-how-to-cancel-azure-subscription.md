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
ms.date: 1/23/2019
ms.author: cwatson
ms.openlocfilehash: 88ea3a5daa1a740ab545201bc48b612ea6a56a44
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810329"
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
2. Válassza ki az előfizetést, amely megszakítja.
3. Válassza ki **áttekintése**, majd válassza ki **előfizetés megszüntetése**.

    ![Képernyőkép a Mégse gomb](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Kövesse az utasításokat, és fejezze be a törlés.

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
