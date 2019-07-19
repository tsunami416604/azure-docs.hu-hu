---
title: A Microsoft Azure-számla megtekintése és letöltése
description: Útmutatás a Microsoft Azure-számla megtekintéséhez és letöltéséhez.
keywords: számlázási számla, számla letöltése, Azure-számla, Azure-használat
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: banders
ms.openlocfilehash: 4dc5f88f7a5994e5bcd50d71c86bf1ba35b10734
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321773"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>A Microsoft Azure-számla megtekintése és letöltése

A legtöbb előfizetés esetében letöltheti a számláját a [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) , vagy elküldheti e-mailben. Ha Ön Azure-ügyfél, és Nagyvállalati Szerződés (EA-ügyfél), nem töltheti le a szervezete számláit. A rendszer elküldi a számlákat a beléptetéshez beállított számlák fogadására.

Csak bizonyos szerepkörök jogosultak a számlák megtekintésére. Például a fiók rendszergazdája vagy a vállalati rendszergazda. További információ a számlázási információk eléréséről: az Azure- [számlázáshoz való hozzáférés kezelése szerepkörök használatával](billing-manage-access.md).

Ha rendelkezik Microsoft- [ügyfél](#check-your-access-to-a-microsoft-customer-agreement)-szerződéssel, a számlák beszerzéséhez a következő szerepkörök egyikével kell rendelkeznie:

- Számlázási profil tulajdonosa
- Közreműködő
- Olvasó
- Számla-kezelő

A Microsoft ügyfél-szerződések számlázási szerepköreivel kapcsolatos további információkért tekintse meg a [Számlázási profilok szerepkörei és feladatai](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)című témakört.

## <a name="noinvoice"></a>Miért nem lehet számlát beolvasni

Számos oka lehet annak, hogy nem jelenik meg számla:

- 30 napnál kevesebb telt el az Azure-előfizetés életbe lépése óta.

- Az Azure a számlázási időszak végén számlázza. Tehát előfordulhat, hogy egy számla még nem lett létrehozva. Várja meg a számlázási időszak végét.

- Nincs engedélye a számlák megtekintésére. Ha rendelkezik Microsoft-ügyfél-szerződéssel, akkor a számlázási profil tulajdonosa, a közreműködő, az olvasó vagy a számla kezelője kell, hogy legyen. Más előfizetések esetén előfordulhat, hogy nem látja a régi számlákat, ha Ön nem a fiók rendszergazdája. További információ a számlázási információhoz való hozzáférésről: [Az Azure-beli számlázáshoz való hozzáférés kezelése szerepkörök használatával](billing-manage-access.md).

- Ha az előfizetése ingyenes próbaidőszakot vagy havi kreditet tartalmaz, akkor csak a havi kreditek összegének meghaladása után kap számlát. Ha van Microsoft-ügyfél szerződése, akkor mindig megkapja a számlát.

## <a name="download-your-azure-invoices-pdf"></a>Azure-számlák letöltése (. pdf)

A legtöbb előfizetés esetében letöltheti a számláját a Azure Portalból. Ha rendelkezik Microsoft-ügyfél-szerződéssel, tekintse meg [a Microsoft-ügyfél szerződése számláinak letöltése](#download-invoices-for-a-microsoft-customer-agreement)című témakört.

### <a name="download-invoices-for-an-individual-subscription"></a>Egyéni előfizetés számláinak letöltése

1. Válassza ki az előfizetését a Azure Portal előfizetések [lapján](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a [számlákhoz hozzáféréssel rendelkező felhasználóként](billing-manage-access.md).

2. Válassza ki a **Számlák** elemet.

    ![A számlázási & használati lehetőséget megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. A PDF-számla másolatának megtekintéséhez kattintson a **számla letöltése** elemre. Ha **nem érhető**el, akkor [Miért nem látok számlát az utolsó számlázási időszakra vonatkozóan?](#noinvoice)

    ![A számlázási időszakokat, a letöltési lehetőséget és az egyes számlázási időszakok teljes díját bemutató képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. A napi használatot a számlázási időszakra kattintva is megtekintheti.

A számlával kapcsolatos további információkért lásd: [Microsoft Azure számlájának ismertetése](billing-understand-your-bill.md). A költségek kezelésével kapcsolatos segítségért tekintse [meg a váratlan költségek megelőzése az Azure-számlázással és](billing-getting-started.md)a költséghatékonysággal foglalkozó témakört.

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Microsoft ügyfél-szerződéshez tartozó számlák letöltése

Az egyes [Számlázási profilokhoz](billing-mca-overview.md#billing-profiles) tartozó számlákat a Microsoft ügyfél szerződése alapján hozza létre a rendszer. A Azure Portal-ból származó számlák letöltéséhez számlázási profil tulajdonosának, közreműködőnek, olvasójának vagy számlafogadó-kezelőnek kell lennie.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá *Cost Management + számlázásra*.
1. Válasszon számlázási profilt. A hozzáféréstől függően előfordulhat, hogy először ki kell választania egy számlázási fiókot.
1. Válassza ki a **Számlák** elemet.
1. A számla rácson keresse meg a letölteni kívánt számla sorát.
1. Kattintson a sor végén`...`található három pontra ().
    ![A sor végén található három pontot ábrázoló képernyőkép](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. A letöltési környezet menüben válassza a **számla**elemet.

    ![A helyi menüt bemutató képernyőkép](./media/billing-download-azure-invoice/contextmenu.png)

Ha nem jelenik meg az utolsó számlázási időszakra vonatkozó számla, olvassa el a következőt: [Miért nem látok számlát az utolsó számlázási időszakra vonatkozóan?](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>Számla beszerzése e-mailben (. pdf)

További címzetteket is megadhat, és beállíthatja, hogy az Azure-számla egy e-mailben legyen elküldve. Előfordulhat, hogy ez a funkció nem érhető el bizonyos előfizetésekhez, például támogatási ajánlatokhoz, nagyvállalati szerződésekhez vagy Azure in Open licencprogram. Ha rendelkezik Microsoft-ügyfél-szerződéssel, tekintse meg a következő szakaszt a [Számlázási profilhoz tartozó számlák beszerzése e-mailben](#get-your-subscriptions-invoices-in-email).

### <a name="get-your-subscriptions-invoices-in-email"></a>Az előfizetés számláinak beolvasása e-mailben

1. Válassza ki az előfizetését az előfizetések [lapon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Minden Ön által birtokolt előfizetés esetében válassza a megfelelőt. Kattintson a **számlák** , majd **a számla e-mail küldése**elemre.

    ![A bekapcsolási folyamatot bemutató képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Kattintson a **Letiltás** elemre, és fogadja el a feltételeket.

    ![A 2. lépés elküldését bemutató képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. A szerződés elfogadását követően további címzetteket is beállíthat. A címzettek eltávolítását követően az e-mail-cím már nem tárolódik. Ha meggondolja magát, el kell olvasnia őket.

    ![A 3. lépés a beléptetési folyamatot bemutató képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Ha a lépések követése után nem kap e-mailt, győződjön meg róla, hogy az e-mail-címe helyes a profiljában lévő [kommunikációs beállításokban](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Az előfizetés számláinak lekérése e-mailben

Ha szeretné letiltani a számla e-mailben való lekérését, kövesse az előző lépéseket, és kattintson **az e-mailben küldött számlák**letiltására. Ez a beállítás eltávolít minden olyan e-mail-címet, amelyre be volt állítva a számlák küldése e-mailben. A címzettek újrakonfigurálásával visszatérhet a szolgáltatásba.

 ![A letiltási folyamatot bemutató képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Microsoft Customer Agreement-számlák beszerzése e-mailben

Ha rendelkezik Microsoft-ügyfél-szerződéssel, bekapcsolhatja a számlát e-mailben. Az összes számlázási profil tulajdonosa, közreműködője, olvasója és számlázási kezelője e-mailben megkapja a számlát. Az olvasók nem tudják frissíteni az e-mail számla beállításait.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá **Cost Management + számlázásra**.
1. Válasszon számlázási profilt. A hozzáféréstől függően előfordulhat, hogy először ki kell választania egy számlázási fiókot.
1. A **Beállítások**területen válassza a **Tulajdonságok**lehetőséget.
1. Az **E-mail számla**területen válassza az **E-mail számla beállításainak frissítése**lehetőséget.

    ![Az e-mail számla tulajdonságait megjelenítő képernyőkép](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Válassza **a**bekapcsolás lehetőséget.
1. Kattintson az **Update** (Frissítés) elemre.

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Letilthatja a Microsoft ügyfél-szerződési számláinak beszerzését e-mailben

Ha szeretné letiltani a számla e-mailben való lekérését, kövesse az előző lépéseket, és kattintson a **Letiltás**lehetőségre. Az összes tulajdonos, közreműködő, olvasó és számlázási kezelő a számla e-mailben való beszerzését választotta ki. Ha Ön olvasó, az e-mail számla beállítását nem módosíthatja.



## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfél szerződéshez való hozzáférésének megkeresése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

A számlával és a díjakkal kapcsolatos további tudnivalókért tekintse meg a következőt:

- [Microsoft Azure használat és díjak megtekintése és letöltése](billing-download-azure-daily-usage.md)
- [Microsoft Azure-elszámolások értelmezése](billing-understand-your-bill.md)
- [Az Azure-számlán szereplő feltételek ismertetése](billing-understand-your-invoice.md)
- [A Microsoft Azure részletes használati feltételeinek megismerése](billing-understand-your-usage.md)
- [A szervezet Azure-díjszabásának megtekintése](billing-ea-pricing.md)

Ha rendelkezik Microsoft-ügyfél-szerződéssel, tekintse meg a következőt:

- [A számlázási profilhoz tartozó számla díjainak megismerése](billing-mca-understand-your-bill.md)
- [A számlázási profilhoz tartozó számla feltételeinek megismerése](billing-mca-understand-your-invoice.md)
- [Az Azure használati és díjszabási fájljának ismertetése a számlázási profilhoz](billing-mca-understand-your-usage.md)
- [A számlázási profilhoz tartozó adózási dokumentumok megtekintése és letöltése](billing-mca-download-tax-document.md)
- [A szervezet Azure-díjszabásának megtekintése](billing-ea-pricing.md)
