---
title: Az Azure-előfizetések számlázási vagy -kreditemet értesítések beállítása |} A Microsoft Docs
description: Ismerteti, hogyan állíthat be riasztásokat az Azure-elszámolások a számlázási meglepetések elkerülése érdekében.
keywords: kredit riasztást, számlázási riasztás
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
ms.date: 06/15/2018
ms.author: adpick
ms.openlocfilehash: 094bfe041ae04e52b6d998ccfd1d964abf192d6a
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47095099"
---
# <a name="set-up-billing-or-credit-alerts-for-your-microsoft-azure-subscriptions"></a>A Microsoft Azure-előfizetések számlázási vagy -kreditemet riasztásainak beállítása
Ha Ön a Fiókadminisztrátor az Azure-előfizetéssel, használja az Azure számlázási értesítési szolgáltatás elkészíthető testre szabott számlázási értesítőkkel figyelése és kezelése az Azure-fiókokhoz kapcsolódó számlázási tevékenység.

Ez a szolgáltatás az előzetes verzióban, ezért az előzetes verziójú szolgáltatásainak lapján a engedélyeznie kell.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="set-the-alert-threshold-and-email-recipients"></a>Állítsa be a Figyelmeztetési küszöbérték és e-mail címzettek
1. Látogasson el [az előzetes verziójú szolgáltatásainak lapján](https://account.windowsazure.com/PreviewFeatures) , és engedélyezze **számlázási értesítési szolgáltatás**.

1. Miután megkapta a számlázási szolgáltatás be van kapcsolva megerősítő e-mailt az előfizetéshez tartozó, látogasson el a [az előfizetések lap](https://account.windowsazure.com/Subscriptions) a fiókportálon. Kattintson a figyelése, és kattintson a kívánt előfizetésre **riasztások**.

    ![Az Azure Account center, az előfizetések nézet riasztások kiemelésével – képernyőfelvétel][Image1]

2. Ezután kattintson **riasztás hozzáadása** hozhat létre az elsőt. Előfizetésenként legfeljebb két e-mail címzettjei az egyes riasztások és a egy másik küszöbértékkel öt számlázási figyelmeztetéseket összesen beállíthat.

    ![A riasztások nézetben, melyben hozzáadhatja a riasztás képernyőképe][Image2]

3. Amikor hozzáad egy riasztást, adjon meg egy egyedi nevet, válassza ki a költségkeret küszöbértéket, és válassza ki az e-mail-címeket, ahol a rendszer küld-e a riasztásokat. Amikor állítja be a küszöbértéket, vagy választhat egy **számlázás összesen** vagy egy **fizetésre használható Kredit** származó a **riasztási a** listája. Számlázási összesen riasztást küldi, amikor előfizetés-kiadások meghaladja a küszöbértéket. A fizetésre használható kredit riasztást küldi, amikor a fizetésre felhasználható kreditjeinek száma a határérték. Ingyenes próbaverzió és a Visual Studio-előfizetések fizetésre havonta felhasználható kreditek általában vonatkoznak.

    ![A riasztás hozzáadása nézet, amelyen konfigurálhatja a címzettek képernyőképe][Image3]

Az Azure támogatja a bármilyen e-mail-címmel, de nem ellenőrzi, hogy az e-mail cím működik, így gondosan benne elgépelések.

## <a name="check-on-your-alerts"></a>A riasztások ellenőrzése
Riasztások beállítása után az Account Center sorolja fel őket, és megtekintheti, hogy hány több állíthat be. Minden riasztás esetében megjelenik a dátum és idő küldték, legyen szó egy riasztás teljes számlázási és a fizetésre használható Kredit és a beállított korlátot. A dátum és idő formátuma 24 órás világidő koordinálása (UTC), és a dátum formátuma éééé-hh-nn. Kattintson a plusz jelre egy riasztás szerkesztheti a listában, vagy kattintson a Kuka törli-e.

## <a name="delete-alerts-or-email-addresses-from-the-azure-billing-alert-service"></a>Riasztások vagy e-mail-címek törlése az Azure számlázási értesítési szolgáltatás
Minden eddiginél kell eltávolítani az adatokat a szolgáltatásból, ha az e-mail-cím, a fájl frissítése, vagy a riasztás törlésével a teljes egészében.

   ![Képernyőkép a törlési riasztás nézet, ahol eltávolíthatja a személyes adatok][Image4]

## <a name="billing-alerts-for-enterprise-agreement-ea-customers"></a>Számlázási értesítések az ügyfelek a nagyvállalati szerződés (EA)
EA-előfizetések nem támogatja ezt a szolgáltatást, ehelyett nagyvállalati szerződéssel rendelkező ügyfelek is értesítéseket kaphat az egyes részlegek egy Enrollment kvóták kiadások beállításával. Lásd: [részleg költségkeret-beállítási kvóták](https://ea.azure.com/helpdocs/departmentSpendingQuotas) az első lépések az EA-portálon.

## <a name="learn-more-about-azure-cost-management"></a>További információ az Azure cost managementbe
- Becsült költségek használatával a [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/), [összköltsége tulajdonjog Számológép](https://aka.ms/azure-tco-calculator), és a egy szolgáltatás hozzáadásakor.
- [Tekintse át a használat és költségek az Azure Portalon rendszeresen](billing-getting-started.md#costs).
- Kapcsolja be a [az Azure Advisor ajánlásait költség](../advisor/advisor-cost-recommendations.md).

További tudnivalókért lásd: [Azure cost management útmutatást](billing-getting-started.md).

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 
[Image4]: ./media/azure-billing-set-up-alerts/AlertsDeleteScreen1.PNG
