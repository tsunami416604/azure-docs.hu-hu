---
title: Megtekintheti és letöltheti a Microsoft Azure-számla |} A Microsoft Docs
description: Ismerteti, hogyan lehet megtekintéséhez és a Microsoft Azure-számla letöltése
keywords: Hitelkártyás, számla letöltése, azure-számlát, azure-használat
services: billing
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: f71fe9b02765e0fc8fd5f3b7abbd54c87b08132f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264063"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>A Microsoft Azure-számla megtekintése és letöltése

A legtöbb előfizetés esetén töltse le a számla a [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) vagy e-mailben küldött nincs. Ha Ön Azure-ügyfelek a nagyvállalati szerződés (EA-ügyfél), nem lehet letölteni a szervezet számlákat. Számlák küldése a személy, aki be van állítva a beléptetési számlákat fogadásához.

Csak egyes szerepkörök engedélye, például az a fiók rendszergazdája vagy vállalati rendszergazdai számlák megtekintése. További információ a számlázási információhoz való hozzáférésről: [Az Azure-beli számlázáshoz való hozzáférés kezelése szerepkörök használatával](billing-manage-access.md).

Ha rendelkezik egy [Microsoft Ügyfélszerződéséhez](#check-your-access-to-a-microsoft-customer-agreement), csak egy számlázási profilja tulajdonos, közreműködő, olvasó, és számlázása a számlákon szerkesztését. További számlázási szerepkörökkel kapcsolatban a Microsoft ügyfél-megállapodások esetén, lásd: [számlázási profil szerepkörök és feladatok](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-your-azure-invoices-pdf"></a>Töltse le az Azure-számlák (.pdf)

A legtöbb előfizetés esetén töltse le a számla az Azure Portalról. Ha a Microsoft vevői szerződéssel rendelkezik, tekintse meg a letöltési számlázási profilhoz tartozó számlákat.

### <a name="download-invoices-for-an-individual-subscription"></a>Egy adott előfizetéshez tartozó számla letöltése

1. Válassza ki az előfizetését a [előfizetések lapon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) , az Azure Portalon [számlákat hozzáféréssel rendelkező felhasználó](billing-manage-access.md).

2. Válassza ki **számlákat**.

    ![A számlázás- és használati beállítás bemutató képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Kattintson a **számla letöltése** megtekintéséhez a PDF formátumú számlákat másolatát. Ugyanakkor **nem érhető el**, lásd: [miért nem látom számla alapján az előző elszámolási időszakban?](#noinvoice)

    ![Képernyőkép a elszámolási időszakok, a letöltési lehetőséget és a teljes díj minden elszámolási időszakban](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. A napi használat a számlázási időszak kattintva is megtekintheti.

A számla kapcsolatos további információkért lásd: [Microsoft Azure-hoz kapcsolódó számlák magyarázata](billing-understand-your-bill.md). A költségek kezelésével kapcsolatos útmutatásért lásd: [az Azure-elszámolással és költségkezeléssel váratlan költségek megelőzése](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Töltse le a Microsoft Ügyfélszerződéséhez számlák

Számlák jönnek létre az egyes [profil számlázási](billing-mca-overview.md#understand-billing-profiles) a Microsoft ügyfél-szerződés. Számlázási profil tulajdonos, közreműködő, olvasó, vagy a számlához manager számlákat letölteni az Azure Portalról.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Keresés **Cost Management és számlázás**.
1. Válassza ki a számlázási profilt. A hozzáférést, attól függően szükség lehet először ki kell választania egy számlázási fiókot.
1. Válassza ki **számlákat**.
1. A számla rács megkeresi a letölteni kívánt számla.
1. Kattintson a három (`...`) a sor végén található.
    ![A sor végén található három pontra bemutató képernyőkép](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. A letöltési helyi menüben válassza ki a **számla**.

    ![Képernyőkép a helyi menü](./media/billing-download-azure-invoice/contextmenu.png)

Ha számla alapján az előző elszámolási időszakban nem látja, tekintse meg [miért nem látom számla alapján az előző elszámolási időszakban?](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>A számla kapni e-mailben (.pdf)

Is engedélyezve van a hibajelentések, valamint fogadni az Azure további címzettek egy e-mailben számla. Ez a funkció nem lehet például a támogatási ajánlatok, a nagyvállalati szerződés keretében vagy az Azure in Open licencprogram bizonyos előfizetésekhez érhető el. Ha egy Microsoft Customer szerződéssel rendelkezik, tekintse meg a számlázási profilja számlák e-mailt a Get.

### <a name="get-your-subscriptions-invoices-in-email"></a>Az előfizetéshez tartozó számlákat kapni e-mailben

1. Válassza ki az előfizetését a [előfizetések lapon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Mindegyik a saját előfizetés kiválasztásához. Kattintson a **számlákat** majd **E-mail-a számla**.

    ![A szolgáltatás aktiválása folyamatot bemutató képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Kattintson a **engedélyezve van a hibajelentések** és fogadja el a feltételeket.

    ![Képernyőkép a választható flow 2. lépés](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Ha Ön már elfogadta a szerződést, további címzetteket is beállíthatja. Amikor eltávolít egy címzettet, az e-mail-cím már nem tárolja. Ha meggondolja magát, újra hozzá kell őket.

    ![Képernyőkép a választható folyamat 3. lépés](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Ha a lépések végrehajtása után nem kap egy e-mailt, győződjön meg arról, az e-mail-címét a megfelelő a [profilját a kommunikációs beállítások](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Tilthatják le az első az előfizetéshez tartozó számlákat e-mailben

Kikapcsolja a számla első e-mail gombra kattintva és a fenti lépések elvégzésével **tilthatják le az e-mailben elküldött számlákhoz**. Ez a beállítás eltávolít minden olyan e-mail-címet, amelyre be volt állítva a számlák küldése e-mailben. Amennyiben vissza a címzettek újrakonfigurálhatja az.

 ![A letiltási folyamat bemutató képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>A Microsoft Ügyfélszerződéséhez számlákat kapni e-mailben

Ha egy Microsoft Ügyfélszerződéséhez, kérheti a, a számla kapni egy e-mailben. Számlázási profil tulajdonosoknak, közreműködőknek, olvasó és számla ellenében kezelők a számlát fog kapni e-mailben. Olvasói az e-mailek számla szabályozó nem lehet frissíteni.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Keresés **Cost Management és számlázás**.
1. Válassza ki a számlázási profilt. A hozzáférést, attól függően szükség lehet először ki kell választania egy számlázási fiókot.
1. A **beállítások**válassza **tulajdonságok**.
1. Alatt **E-mail számla**válassza **Update e-mail számla beállítás**.

    ![Képernyőkép, amely bemutatja, e-mailes számla tulajdonságai](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Válassza ki **engedélyezve van a hibajelentések**.
1. Kattintson az **Update** (Frissítés) elemre.

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Tilthatják le az első a Microsoft Ügyfélszerződéséhez számlákat e-mailben

Kikapcsolja a számla első e-mail gombra kattintva és a fenti lépések elvégzésével **kikapcsolhatja az újat**. Tulajdonosoknak, közreműködőknek, olvasó és számla ellenében kezelők kívül a számlán túl első e-mail, a rendszer kilépteti. Ha egy olvasó, az e-mailek számla beállítás nem módosítható.

### <a name="noinvoice"></a> Miért nem látom számla alapján az előző elszámolási időszakban?

Számos oka lehet annak, hogy nem jelenik meg számla:

- 30 napnál kevesebb telt el az Azure-előfizetés életbe lépése óta.

- A rendszer még nem hozta létre a számlát. Várja meg a számlázási időszak végét.

- Nincs engedélye számlák megtekintése. Ha a Microsoft vevői szerződéssel rendelkezik, csak a számlázási profilja tulajdonos, közreműködő, olvasó, és manager számla. Másik előfizetés esetén Ön nem láthatja korábbi számlák nvdimm-n a fiók rendszergazdája. További információ a számlázási információhoz való hozzáférésről: [Az Azure-beli számlázáshoz való hozzáférés kezelése szerepkörök használatával](billing-manage-access.md).

- Ha ingyenes próbaidőszakot, vagy egy havi kreditösszeg erejéig-előfizetése, akkor nem lehet hosszabb, nem fog számlát kap, kivéve, ha a Microsoft vevői szerződéssel rendelkezik.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>A Microsoft vevői Szerződéssel való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

A számlák és a költségek kapcsolatos további információkért lásd:

- [A Microsoft Azure használati adatainak és díjainak megtekintése és letöltése](billing-download-azure-daily-usage.md)
- [A Microsoft Azure-hoz kapcsolódó számlák magyarázata](billing-understand-your-bill.md)
- [Az Azure-számla feltételeinek értelmezése](billing-understand-your-invoice.md)
- [A feltételeinek értelmezése a Microsoft Azure részletes használat](billing-understand-your-usage.md)
- [A szervezet az Azure díjszabásának megtekintése](billing-ea-pricing.md)

Ha a Microsoft vevői szerződéssel rendelkezik, tekintse meg:

- [A költségek, a számlán a számlázási profiljához ismertetése](billing-mca-understand-your-bill.md)
- [A számlázási profil a számla feltételeinek értelmezése](billing-mca-understand-your-invoice.md)
- [Megismerheti az Azure-beli használat és költségek fájlmegosztás a számlázási profiljához](billing-mca-understand-your-usage.md)
- [Megtekintheti, és a számlázási profiljához adó dokumentumok letöltése](billing-mca-download-tax-document.md)
- [A szervezet az Azure díjszabásának megtekintése](billing-ea-pricing.md)
