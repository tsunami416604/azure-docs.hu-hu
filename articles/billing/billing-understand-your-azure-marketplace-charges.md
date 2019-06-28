---
title: Az Azure külső szolgáltatási díjak értelmezése |} A Microsoft Docs
description: Ismerje meg a külső szolgáltatások, korábbi nevén a Marketplace-en a költségeket az Azure számlázását.
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 90753c6046425b60fda04fa99b2952e706d9c0e5
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311984"
---
# <a name="understand-your-azure-external-services-charges"></a>A külső szolgáltatások az Azure-költségek ismertetése
Külső szolgáltatások az Azure Marketplace-en külső szoftvergyártók által közzétett. Ha például a SendGrid egy külső szolgáltatás, amely az Azure-ban is vásárolhat, de nem a Microsoft által közzétett. Bizonyos Microsoft-termékek vannak az Azure Marketplace-en árult túl.

## <a name="how-external-services-are-billed"></a>Hogyan külső szolgáltatások számlázása

- Ha rendelkezik egy [Microsoft Ügyfélszerződéséhez](#check-access-to-a-microsoft-customer-agreement), a külső szolgáltatások számlázása a többi Azure-szolgáltatását.
- Ha nem rendelkezik a Microsoft ügyfél-szerződés, a külső szolgáltatások elkülönítve számlázzuk az Azure-szolgáltatások.
- Minden külső szolgáltatásnak van egy másik számlázási modellt. Bizonyos szolgáltatások a használatalapú fizetést biztosító módon elszámolással számlázzuk, míg mások megoldottuk a havi díjak.
- Havi ingyenes kreditjeinek külső szolgáltatások nem használható. Ha használ, amely tartalmazza az Azure-előfizetéssel [ingyenes krediteket](https://azure.microsoft.com/pricing/spending-limits/), azok nem alkalmazható díjak a külső szolgáltatások. Amikor üzembe helyezi egy új külső szolgáltatáshoz vagy erőforráshoz, egy figyelmeztetés jelenik meg:

    ![Marketplace-en beszerzési figyelmeztetés](./media/billing-understand-your-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/): 

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.
   
    ![Select Subscriptions in the Hub menu](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.
   
    ![Select a subscription in the billing blade](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.
   
    ![Select an external service](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.
   
    ![View external services billing history](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-azure-marketplace-invoices"></a>Megtekintése és letöltése az Azure Marketplace-en számlák

Ha rendelkezik egy [Microsoft Ügyfélszerződéséhez](#check-access-to-a-microsoft-customer-agreement), a harmadik fél díjakat az ugyanazon a, az Azure-szolgáltatások díjait számlán. Ismerje meg, hogyan [megtekintése és letöltése az Azure-számla](billing-download-azure-invoice.md) a külső díjak megtekintéséhez az Azure Portalról.

Ha nem rendelkezik a Microsoft Ügyfélszerződéséhez, azok külön számlák harmadik féltől eredő díjakat kiszámlázzuk. Megtekintheti és az Azure Marketplace-en számlákat letölteni az Azure Portalról az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Keresés **Cost Management és számlázás**.
1. A bal oldali menüben válassza ki a **számlákat**.
1. Kattintson a **Azure Marketplace-en és foglalások** fülre.  ![Az Azure Marketplace-en és foglalások lap képe](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. Az előfizetés legördülő válassza ki az előfizetést, amely tartalmazza a külső szolgáltatások meg szeretné tekinteni a számlákat.

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Az ügyfelek a nagyvállalati szerződés (EA) kiadások külső szolgáltatás megtekintése

Nagyvállalati szerződéssel rendelkező ügyfelek tekintheti külső szolgáltatás költségeit és a nagyvállalati szerződések portáljának a jelentések letöltése. Lásd: [nagyvállalati szerződéssel rendelkező ügyfeleink az Azure Marketplace-en](https://ea.azure.com/helpdocs/azureMarketplace) a kezdéshez.

## <a name="manage-payment-methods-for-external-service-orders"></a>Külső szolgáltatás rendelések fizetési módok kezelése

Ha a szolgáltatást egy külső szolgáltatás, válassza ki az erőforrás Azure-előfizetést. A kiválasztott Azure-előfizetés fizetési mód lesz a fizetési módot, a külső szolgáltatáshoz. Ha módosítani szeretné egy külső szolgáltatás a fizetési módot, kell [a fizetési módot, az Azure-előfizetés](billing-how-to-change-credit-card.md) vannak kötve, hogy külső szolgáltatáshoz. Ismerhetik meg, hogy melyik előfizetéssel, a külső szolgáltatás sorrendben vannak kötve, az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a **összes erőforrás** a bal oldali navigációs menüben.
     ![Képernyőkép az összes erőforrás menüpont](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Keresse meg a külső szolgáltatáshoz.
1. Keresse meg az előfizetés nevét a **előfizetés** oszlop.
    ![Képernyőkép az előfizetés-erőforrás neve](./media/billing-understand-your-azure-marketplace-charges/sub-selected.png)
1. Kattintson a az előfizetés nevét és [frissíteni az aktív fizetési mód](billing-how-to-change-credit-card.md).
 
<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)
   
    ![Select marketplace in the account center](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage
   
    ![Select the external service you want to manage](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.
   
    ![Order summary](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.
   
    ![Select edit info](./media/billing-understand-your-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>Egy külső szolgáltatás megrendelés törlése
Biztosan megszakítja a külső szolgáltatás rendeléséhez, ha törli az erőforrás a [az Azure portal](https://portal.azure.com).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a **összes erőforrás** a bal oldali navigációs menüben.
    ![Képernyőkép az összes erőforrás menüpont](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Keresse meg a külső szolgáltatáshoz.
1. A törölni kívánt erőforrás melletti jelölőnégyzetet.
1. Válassza ki **törlése** a parancssávon.
    ![Képernyőkép a Törlés gombra.](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. Típus *"Igen"* a megerősítési panelen.
    ![Erőforrás törlése](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. Kattintson a **Törlés** gombra.



## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

