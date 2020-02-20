---
title: Az Azure külső szolgáltatásaival kapcsolatos díjak ismertetése | Microsoft Docs
description: Megismerheti a külső szolgáltatások (korábbi nevén Marketplace) számlázását és díjait az Azure-ban.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ce385de408d4b0a8cf1531f762a126a6b9b2c126
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199075"
---
# <a name="understand-your-azure-external-services-charges"></a>Az Azure külső szolgáltatásaival kapcsolatos díjak ismertetése
A külső szolgáltatásokat külső szoftverszállítók teszik közzé az Azure Marketplace-en. A SendGrid például egy olyan külső szolgáltatás, amelyet megvásárolhat az Azure-ban, de nem a Microsoft adta ki. Emellett azonban bizonyos Microsoft-termékek értékesítése is az Azure Marketplace-en történik.

## <a name="how-external-services-are-billed"></a>A külső szolgáltatások számlázása

- Ha Microsoft-ügyfélszerződéssel (MCA) vagy Microsoft Partnerszerződéssel (MPA) rendelkezik, a külső szolgáltatások számlázása a többi Azure-szolgáltatással együtt történik. [Ellenőrizze a számlázási fiókja típusát](#check-billing-account-type), hogy megtudja, van-e hozzáférése MCA-hoz vagy MPA-hoz.
- Ha nem rendelkezik MCA-val vagy MPA-val, a külső szolgáltatások számlázása a többi Azure-szolgáltatástól külön történik.
- Minden egyes külső szolgáltatás eltérő számlázási modellel rendelkezik. Bizonyos szolgáltatások számlázása használatalapú fizetésessel történik, míg más szolgáltatásokért fix havi díjat kell fizetni.
- A külső szolgáltatásokhoz nem használhatók fel az ingyenes havi kreditek. Ha [ingyenes krediteket](https://azure.microsoft.com/pricing/spending-limits/) tartalmazó Azure-előfizetést használ, a külső szolgáltatások díjaira nem alkalmazhatja azokat. Új külső szolgáltatás vagy erőforrás kiépítésekor megjelenik egy figyelmeztetés:

    ![Marketplace vásárlási figyelmeztetése](./media/understand-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/understand-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/understand-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/understand-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/understand-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-invoices-for-external-services"></a>Számlák megtekintése és letöltése külső szolgáltatásokhoz

Ha Microsoft-ügyfélszerződéssel (MCA) vagy Microsoft Partnerszerződéssel (MPA) rendelkezik, a külső szolgáltatások számlázása a többi Azure-szolgáltatással együtt történik. [Ellenőrizze a számlázási fiókja típusát](#check-billing-account-type), hogy megtudja, van-e hozzáférése MCA-hoz vagy MPA-hoz. Ha van, akkor a külső szolgáltatások díjainak megtekintéséhez olvassa el a [számlák Azure Portalon történő megtekintését és letöltését](download-azure-invoice.md) ismertető cikket.

Ha nem rendelkezik MCA-val vagy MPA-val, a külső szolgáltatások díjairól külön számlát kap. Az Azure Marketplace-számlák Azure Portalon történő megtekintéséhez és letöltéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.
1. A bal oldali menüben válassza a **Számlák** elemet.
1. Kattintson az **Azure Marketplace és foglalások** lapra.  ![Kép az Azure Marketplace és foglalások lapról](./media/understand-azure-marketplace-charges/invoice-tabs.png)
1. Az előfizetéseket tartalmazó legördülő listából válassza ki az azon külső szolgáltatásokat tartalmazó előfizetést, amelyek számláit meg szeretné tekinteni.

## <a name="external-spending-for-ea-customers"></a>Külső költségek EA-ügyfelek esetén

Az EA-ügyfelek megtekinthetik a külső szolgáltatások költségeit, és jelentéseket tölthetnek le az EA Portalról. Az első lépésekkel kapcsolatban tekintse meg [az EA-ügyfelek számára elérhető Azure Marketplace-t](https://ea.azure.com/helpdocs/azureMarketplace).

## <a name="manage-payment-for-external-services"></a>A külső szolgáltatásokkal kapcsolatos kifizetések kezelése

Külső szolgáltatás vásárlásakor kiválaszt egy Azure-előfizetést az erőforráshoz. A külső szolgáltatás fizetési módja azonos lesz a kiválasztott Azure-előfizetésével. A külső szolgáltatás fizetési módjának módosításához [módosítania kell annak az Azure-előfizetésnek a fizetési módját](../manage/change-credit-card.md), amely a külső szolgáltatáshoz kapcsolódik. Ha szeretné kideríteni, hogy a külső szolgáltatás melyik előfizetéshez kapcsolódik, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs menüben kattintson a **Minden erőforrás** elemre.
     ![képernyőkép a menü Minden erőforrás eleméről](./media/understand-azure-marketplace-charges/all-resources.png)
1. Keresse meg a külső szolgáltatást.
1. Az **Előfizetés** oszlopban keresse meg az előfizetés nevét.
    ![képernyőkép az erőforráshoz tartozó előfizetés nevéről](./media/understand-azure-marketplace-charges/sub-selected.png)
1. Kattintson az előfizetés nevére, és [frissítse az aktív fizetési módot](../manage/change-credit-card.md).

<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)

    ![Select marketplace in the account center](./media/understand-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage

    ![Select the external service you want to manage](./media/understand-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.

    ![Order summary](./media/understand-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.

    ![Select edit info](./media/understand-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>Külső szolgáltatás lemondása
Ha le szeretné mondani a külső szolgáltatást, törölje az erőforrást az [Azure Portalon](https://portal.azure.com).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs menüben kattintson a **Minden erőforrás** elemre.
    ![Képernyőkép a menü Minden erőforrás eleméről](./media/understand-azure-marketplace-charges/all-resources.png)
1. Keresse meg a külső szolgáltatást.
1. Jelölje be a törölni kívánt erőforrás melletti jelölőnégyzetet.
1. A parancssávon válassza a **Törlés** gombot.
    ![Képernyőkép a Törlés gombról](./media/understand-azure-marketplace-charges/delete-button.png)
1. A megerősítési párbeszédpanelen írja be az *Igen* kifejezést.
    ![Erőforrás törlése](./media/understand-azure-marketplace-charges/delete-resource.PNG)
1. Kattintson a **Törlés** gombra.

## <a name="check-billing-account-type"></a>A számlázási fiók típusának ellenőrzése
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- [Költségelemzés indítása](../costs/quick-acm-cost-analysis.md)
