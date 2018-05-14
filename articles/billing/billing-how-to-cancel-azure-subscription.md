---
title: Az Azure-előfizetést |} Microsoft Docs
description: Ismerteti, hogyan lehet Azure előfizetés, például az ingyenes próba-előfizetést
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 604e2ee6d2ac7e30813f8b4cd02253407c2b1e3f
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="cancel-your-subscription-for-azure"></a>Az Azure-előfizetés

Az Azure-előfizetéssel, visszavonhatja a [Fiókadminisztrátort](billing-subscription-transfer.md#whoisaa). Az előfizetés megszüntetése után megszűnik az Azure-szolgáltatásokhoz és erőforrásokhoz való hozzáférését.

Mielőtt az előfizetés megszüntetése:

* Készítsen biztonsági másolatot az adatairól. Például ha az Azure storage vagy SQL adatokat tárolja, töltse le. Ha egy virtuális gépet, mentse a lemezképet, helyileg.
* A szolgáltatások leállítása. Lépjen a [erőforrások lapon a kezelési portál](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), és **leállítása** minden futó virtuális gépek, alkalmazások vagy más szolgáltatásokkal.
* Vegye figyelembe az adatok áttelepítését. Lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md).

Ha megszakítja a fizetett [Azure támogatási csomag](https://azure.microsoft.com/support/plans/), akkor rendszer továbbra is a számlázás havonta a többi a 6 hónapos időszakra.

## <a name="cancel-subscription-using-the-azure-portal"></a>Az Azure portál használatával előfizetés megszüntetése

1. Válassza ki az előfizetést a [Azure-portálon az előfizetések oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Válassza ki az előfizetést, szakítsa meg, és kattintson a kívánt **előfizetés megszüntetése**.

    ![A Mégse gomb képernyőkép](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
1. Kövesse az utasításokat, és fejezze be a megszakítását.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Mi történik az I-előfizetés megszüntetése után?

Ha megszakítja, számlázási azonnal leáll. A portál azonban is igénybe vehet a megszakítási műsor akár 10 percet.

Ezt követően a szolgáltatások le vannak tiltva. Ez azt jelenti, hogy a virtuális gépeken való hozzárendelése, ideiglenes IP-címek felszabadítását, és tárolási csak olvasható.

Ha megszakítja a számlázott időszak közepén, küldünk a végső számlázási időszak lejárta után a szokásos számla napon. 

Várunk 90 nappal korábbinak véglegesen törli az adatokat, abban az esetben kell-e férni vagy meggondolja magát. Jelenleg nem díjat számítanak fel az adatok megőrzésével. További tudnivalókért lásd: [Microsoft Trust Center – hogyan azt kezeli az adatokat](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Előfizetés újraaktiválása

Ha véletlenül a használatalapú előfizetés megszüntetése, akkor [aktiválja a fiókok Center](billing-subscription-become-disable.md).

Ha az előfizetés nincs használatalapú fizetés, forduljon a támogatási szolgálathoz, az előfizetés újraaktiválásához törlését követő 90 napon belül.

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha további kérdései, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.
