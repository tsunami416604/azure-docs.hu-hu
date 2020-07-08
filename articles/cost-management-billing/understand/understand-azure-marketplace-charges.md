---
title: Az Azure külső szolgáltatásaival kapcsolatos díjak ismertetése | Microsoft Docs
description: Megismerheti a külső szolgáltatások (korábbi nevén Marketplace) számlázását és díjait az Azure-ban.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 120f60698851bcdaf39f989b4d36c0436b716833
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86117865"
---
# <a name="understand-your-azure-external-services-charges"></a>Az Azure külső szolgáltatásaival kapcsolatos díjak ismertetése
A külső szolgáltatásokat külső szoftverszállítók teszik közzé az Azure Marketplace-en. A SendGrid például egy olyan külső szolgáltatás, amelyet megvásárolhat az Azure-ban, de nem a Microsoft adta ki. Emellett azonban bizonyos Microsoft-termékek értékesítése is az Azure Marketplace-en történik.

## <a name="how-external-services-are-billed"></a>A külső szolgáltatások számlázása

- Ha Microsoft-ügyfélszerződéssel (MCA) vagy Microsoft Partnerszerződéssel (MPA) rendelkezik, a külső szolgáltatások számlázása a többi Azure-szolgáltatással együtt történik. [Ellenőrizze a számlázási fiókja típusát](#check-billing-account-type), hogy megtudja, van-e hozzáférése MCA-hoz vagy MPA-hoz.
- Ha nem rendelkezik MCA-val vagy MPA-val, a külső szolgáltatások számlázása a többi Azure-szolgáltatástól külön történik. Minden számlázási periódusban két számlát fog kapni: egy az Azure-szolgáltatásokra vonatkozik, a másik a Marketplace-vásárlásokra.
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

## <a name="external-spending-for-ea-customers"></a>Külső költségek EA-ügyfelek esetén

Az EA-ügyfelek megtekinthetik a külső szolgáltatások költségeit, és jelentéseket tölthetnek le az EA Portalról. Az első lépésekkel kapcsolatban tekintse meg [az EA-ügyfelek számára elérhető Azure Marketplace-t](https://ea.azure.com/helpdocs/azureMarketplace).

## <a name="view-and-download-invoices-for-external-services"></a>Számlák megtekintése és letöltése külső szolgáltatásokhoz

Ha Microsoft-ügyfélszerződéssel (MCA) vagy Microsoft Partnerszerződéssel (MPA) rendelkezik, a külső szolgáltatások számlázása a többi Azure-szolgáltatással együtt, egyetlen számlán történik. [Ellenőrizze a számlázási fiókja típusát](#check-billing-account-type), hogy megtudja, van-e hozzáférése MCA-hoz vagy MPA-hoz. Ha van, akkor a külső szolgáltatások díjainak megtekintéséhez olvassa el a [számlák Azure Portalon történő megtekintését és letöltését](download-azure-invoice.md) ismertető cikket.

Ha nem rendelkezik MCA-val vagy MPA-val, a külső szolgáltatások díjairól külön számlát kap. 

Az Azure Marketplace díjai a helyi pénznemben jelennek meg.

Az Azure Marketplace-számlák Azure Portalon történő megtekintéséhez és letöltéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.
1. A bal oldali menüben válassza a **Számlák** elemet.
1. Az előfizetés legördülő szűrőjében válassza ki a Marketplace-szolgáltatásokhoz tartozó előfizetést.
1. A számlák listájában tekintse át a **Típus** oszlopot. Ha a számla egy Marketplace-szolgáltatáshoz tartozik, a típusa **Azure Marketplace és foglalások** lesz. 

    ![képernyőkép az Azure Marketplace típusról a számlák táblázatában](./media/understand-azure-marketplace-charges/marketplace-type-twd.png)

1. Ha csak az Azure Marketplace-hez és a foglalásokhoz kapcsolódó számlákat kívánja látni, válassza ki a **Típus** szűrőt a típus szerinti szűréshez. Ezután válassza ki az **Azure Marketplace és foglalások** elemet a legördülő listában.

    ![képernyőkép a kiválasztott Típus szűrőről, miközben az Azure Marketplace és foglalások elem van kiválasztva a legördülő listában](./media/understand-azure-marketplace-charges/type-filter.png)

1. Válassza ki a letölteni kívánt számla jobb oldalán található letöltés ikont.

    ![a kijelölt számla melletti letöltési ikont megjelenítő képernyőkép](./media/understand-azure-marketplace-charges/download-icon-marketplace.png)

1. A **Számla** elem alatt válassza ki a kék **Letöltés** gombot.

    ![a helyi panelen a számlához tartozó letöltési gombot megjelenítő képernyőkép](./media/understand-azure-marketplace-charges/invoice-download-marketplace.png)

## <a name="pay-for-external-services-in-the-azure-portal"></a>Fizetés külső szolgáltatásokért az Azure Portalon

Ha Microsoft-ügyfélszerződéssel (MCA) vagy Microsoft Partnerszerződéssel (MPA) rendelkezik, a külső szolgáltatások számlázása a többi Azure-szolgáltatással együtt történik. [Ellenőrizze a számlázási fiókja típusát](#check-billing-account-type), hogy megtudja, van-e hozzáférése MCA-hoz vagy MPA-hoz. Ha igen, kifizetheti a teljes számláját az Azure Portalon a [Microsoft Azure-számlák befizetése](pay-bill.md) lépéseinek végrehajtásával.

Ha nem rendelkezik MCA vagy MPA szerződéssel, a Marketplace-számlákat a következő lépéseket követve egyenlítheti ki az Azure Portalon:

1. Marketplace-számláinak megtekintéséhez kövesse az előző szakaszban ([Számlák megtekintése és letöltése külső szolgáltatásokhoz](#view-and-download-invoices-for-external-services)) leírt lépéseket.
1. Válassza ki a kifizetni kívánt számlához tartozó kék **Fizetés most** hivatkozást.

    ![a számlák táblázatában kiválasztott fizetés most hivatkozást ábrázoló képernyőkép](./media/understand-azure-marketplace-charges/pay-now-twd.png)

    >[!NOTE]
    > A **Fizetés most** hivatkozás csak akkor jelenik meg, ha a számla típusa **Azure Marketplace és foglalások**, és a számla esedékes vagy lejárt.

1. Az új lapon kattintson a **Fizetési mód kiválasztása** hivatkozásra.

    ![a fizetési mód kiválasztására szolgáló kiválasztott hivatkozást megjelenítő képernyőkép](./media/understand-azure-marketplace-charges/select-payment-method-pay-now-twd.png)

1. A fizetési mód kiválasztása után kattintson a lap bal alsó sarkában található kék **Fizetés most** gombra.
    ![a kiválasztott fizetés most gombot ábrázoló képernyőkép](./media/understand-azure-marketplace-charges/pay-now-button-twd.png)

## <a name="change-default-payment-for-external-services"></a>Az alapértelmezett fizetési mód megváltoztatása külső szolgáltatásokhoz

Külső szolgáltatás vásárlásakor kiválaszt egy Azure-előfizetést az erőforráshoz. A külső szolgáltatás fizetési módja azonos lesz a kiválasztott Azure-előfizetésével. A külső szolgáltatás fizetési módjának módosításához [módosítania kell annak az Azure-előfizetésnek a fizetési módját](../manage/change-credit-card.md), amely a külső szolgáltatáshoz kapcsolódik. Ha szeretné kideríteni, hogy a külső szolgáltatás melyik előfizetéshez kapcsolódik, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. A bal oldali navigációs menüben kattintson a **Minden erőforrás** elemre.
     ![képernyőkép a menü Minden erőforrás eleméről](./media/understand-azure-marketplace-charges/all-resources.png)
1. Keresse meg a külső szolgáltatást.
1. Az **Előfizetés** oszlopban keresse meg az előfizetés nevét.
    ![képernyőkép az erőforráshoz tartozó előfizetés nevéről](./media/understand-azure-marketplace-charges/sub-selected.png)
1. Kattintson az előfizetés nevére, és [frissítse az aktív fizetési módot](../manage/change-credit-card.md).

## <a name="cancel-an-external-service-order"></a>Külső szolgáltatás lemondása

Ha le szeretné mondani a külső szolgáltatást, törölje az erőforrást az [Azure Portalon](https://portal.azure.com).

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. A bal oldali navigációs menüben kattintson a **Minden erőforrás** elemre.
    ![Képernyőkép a menü Minden erőforrás eleméről](./media/understand-azure-marketplace-charges/all-resources.png)
1. Keresse meg a külső szolgáltatást.
1. Jelölje be a törölni kívánt erőforrás melletti jelölőnégyzetet.
1. A parancssávon válassza a **Törlés** gombot.
    ![Képernyőkép a Törlés gombról](./media/understand-azure-marketplace-charges/delete-button.png)
1. A megerősítési párbeszédpanelen írja be az *Igen* kifejezést.
    ![Erőforrás törlése](./media/understand-azure-marketplace-charges/delete-resource.PNG)
1. Kattintson a **Delete** (Törlés) gombra.

## <a name="check-billing-account-type"></a>A számlázási fiók típusának ellenőrzése
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Következő lépések
- [Költségelemzés indítása](../costs/quick-acm-cost-analysis.md)