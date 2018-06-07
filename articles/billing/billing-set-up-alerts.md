---
title: Az Azure-előfizetések számlázással vagy jóváírás riasztások beállítása |} Microsoft Docs
description: Ismerteti, hogyan állíthat be riasztásokat a az Azure számlázásának számlázási meglepetések számát elkerülése érdekében.
keywords: jóváírás riasztást, számlázási riasztás
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/9/2017
ms.author: adpick
ms.openlocfilehash: 981cb1153e0268e6572207f8d2401edb23485863
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607803"
---
# <a name="set-up-billing-or-credit-alerts-for-your-microsoft-azure-subscriptions"></a>A Microsoft Azure-előfizetések a számlázással vagy jóváírás riasztások beállítása
Ha a Fiókadminisztrátor az Azure-előfizetésre, az Azure számlázási értesítési szolgáltatás testreszabott létrehozásához használhatja számlázási riasztásokat, amelyek segítenek figyelése és kezelése az Azure-fiókra számlázási tevékenységet.

Ez a szolgáltatás a helyzet a képen az előzetes verziójú Funkciók lapon engedélyezni kell.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="set-the-alert-threshold-and-email-recipients"></a>A riasztási küszöbérték és e-mailek címzettjeinek beállítása
1. Látogasson el [az előzetes verziójú funkciók oldalon](https://account.windowsazure.com/PreviewFeatures) , és engedélyezze **értesítési szolgáltatás számlázási**.

1. Miután megkapta az e-mailes megerősítés, amely a számlázási szolgáltatás be van kapcsolva az előfizetéséhez, látogasson el a [az előfizetések oldalán](https://account.windowsazure.com/Subscriptions) a fiókportálon. Kattintson a figyelheti, és kattintson a kívánt előfizetést **riasztások**.

    ![Az előfizetések nézet az Azure Account center riasztások kiemelt – képernyőfelvétel][Image1]

2. Ezután kattintson **riasztási hozzáadása** az elsőt létrehozásához. Összesen előfizetésenként, egy másik küszöbérték és az egyes riasztások két e-mailek címzettjeinek legfeljebb öt számlázási riasztásokat állíthat be.

    ![A riasztások nézetben, ahol felveheti a riasztás képernyőképe][Image2]

3. Amikor egy riasztást, adjon neki egy egyedi nevet, választja a költségkeret küszöbértéket, és válassza ki az e-mail címeket, ha elküldi a riasztásokat. A küszöbérték beállításakor, vagy választhat egy **teljes számlázási** vagy egy **pénzügyi kreditet** a a **riasztási a** listája. Számlázási összesen riasztást küldi, amikor az előfizetés költségeik meghaladja a küszöbértéket. A pénzügyi kreditet riasztást küldi, amikor a pénzügyi kreditek meghaladja az vetett. Pénzügyi kreditek általában az ingyenes próbaverzió és a Visual Studio előfizetések vonatkozik.

    ![A riasztási hozzáadása nézet, ahol konfigurálhatja a címzettek képernyőképe][Image3]

Azure akármilyen e-mail címet támogatja, de nem győződjön meg arról, hogy az e-mail cím működésével úgy exportálást elírás eredménye.

## <a name="check-on-your-alerts"></a>A riasztások ellenőrzése
Miután beállította a riasztásokat, a Account Center sorolja fel azokat, és bemutatja, hogy hány több állíthat be. Az egyes riasztások látható, a dátumot és küldési időpontja, hogy egy riasztás teljes számlázási vagy pénzügyi kreditet-e, és a beállított korlátot. A dátum és idő formátuma 24 órás világidő koordinálják (UTC), és a dátum, éééé-hh-nn formátumban. Kattintson a plusz jelre riasztás szerkesztheti a listában, vagy kattintson a Kuka törli-e.

## <a name="delete-alerts-or-email-addresses-from-the-azure-billing-alert-service"></a>Riasztások vagy e-mail címekre törlése az Azure számlázási értesítési szolgáltatás
Ha valaha is el kell távolítania a semmilyen információt a szolgáltatásból, az e-mail cím, a fájl frissítése, vagy a riasztás teljes törlése.

   ![A riasztási törlés nézet, ahol eltávolíthatja a személyes adatok képernyőképe][Image4]

## <a name="billing-alerts-for-enterprise-agreement-ea-customers"></a>A nagyvállalati szerződés (EA) ügyfelek számlázási riasztások
EA előfizetések nem támogatja ezt a szolgáltatást, ehelyett EA igényelhető a riasztások alapján a beléptetési minden részleg számára úgy, hogy a kvóták kiadásokat. Lásd: [részleg költségeik kvóták](https://ea.azure.com/helpdocs/departmentSpendingQuotas) a kezdéshez EA portálon.

## <a name="learn-more-about-azure-cost-management"></a>További információ az Azure költség kezeléséről
- Becsült költségeit használatával a [árképzési Számológép](https://azure.microsoft.com/pricing/calculator/), [összköltsége tulajdonjoga Számológép](https://aka.ms/azure-tco-calculator), és egy szolgáltatás hozzáadásakor.
- [Tekintse át a használati és költségek az Azure portálon rendszeresen](billing-getting-started.md#costs).
- Kapcsolja be a [Azure Advisor költség javaslatok](../advisor/advisor-cost-recommendations.md).

További tudnivalókért lásd: [Azure költség felügyeleti útmutató](billing-getting-started.md).

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 
[Image4]: ./media/azure-billing-set-up-alerts/AlertsDeleteScreen1.PNG
