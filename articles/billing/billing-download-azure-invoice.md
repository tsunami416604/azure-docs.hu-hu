---
title: A Microsoft Azure-számla megtekintése és letöltése
description: A cikk a Microsoft Azure-számlák megtekintését és letöltését mutatja be.
keywords: számlázás,számla,számla letöltése,azure-számla,azure-használat
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 862ec629504da4d8ee7eadd8f9b925984d96614c
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718893"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>A Microsoft Azure-számla megtekintése és letöltése

A legtöbb előfizetés esetében a számláját letöltheti az [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) felületéről, de e-mailben is megkaphatja. Amennyiben Ön Nagyvállalati Szerződéssel rendelkező Azure-ügyfél (EA-ügyfél), a szervezet számláit nem tudja letölteni. A rendszer annak küldi el a számlákat, akihez a regisztrációkor a számlák fogadása hozzá lett rendelve.

Csak bizonyos szerepkörök rendelkeznek a számlák megtekintéséhez szükséges engedéllyel. Ilyen szerepkör a fiókadminisztrátor és a vállalati rendszergazda. További információ a számlázási információhoz való hozzáférésről: [Az Azure-beli számlázáshoz való hozzáférés kezelése szerepkörök használatával](billing-manage-access.md).

Ha rendelkezik [Microsoft-ügyfélszerződéssel](#check-your-access-to-a-microsoft-customer-agreement), a következő szerepkörök egyikével is rendelkeznie kell a számlák lekéréséhez:

- Számlázásiprofil-tulajdonos
- Közreműködő
- Olvasó
- Számlakezelő

A Microsoft-ügyfélszerződések számlázási szerepköreivel kapcsolatos további információkért tekintse meg [a számlázási profil szerepköreit és feladatait](billing-understand-mca-roles.md#billing-profile-roles-and-tasks) ismertető szakaszt.

## <a name="noinvoice"></a> Lehetséges okok, amiért nem kaphatja meg a számlát

Számos oka lehet annak, hogy nem jelenik meg számla:

- 30 napnál kevesebb telt el az Azure-előfizetés életbe lépése óta.

- Az Azure a számlázási időszak végén küld Önnek számlát. Lehetséges, hogy még nem készült számla. Várja meg a számlázási időszak végét.

- Nem rendelkezik engedéllyel a számlák megtekintéséhez. Ha rendelkezik Microsoft-ügyfélszerződéssel, a számlázási profil tulajdonosának, közreműködőjének, olvasójának vagy számlakezelőjének kell lennie. Más előfizetések esetén előfordulhat, hogy nem látja a régi számlákat, ha nem Ön a fiókadminisztrátor. További információ a számlázási információhoz való hozzáférésről: [Az Azure-beli számlázáshoz való hozzáférés kezelése szerepkörök használatával](billing-manage-access.md).

- Ha ingyenes próbaverzióval vagy a havi kreditösszeggel rendelkezik az előfizetésén, csak akkor fog számlát kapni, ha túllépi a havi kreditösszeget. Ha Microsoft-ügyfélszerződéssel rendelkezik, minden esetben fog számlát kapni.

## <a name="download-your-azure-invoices-pdf"></a>Azure-számlák letöltése (.pdf)

A legtöbb előfizetés esetében a számláját letöltheti az Azure Portalról. Ha rendelkezik Microsoft-ügyfélszerződéssel, tekintse meg a [számlázási profilhoz tartozó számlák letöltésével](#download-invoices-for-a-microsoft-customer-agreement) foglalkozó szakaszt.

### <a name="download-invoices-for-an-individual-subscription"></a>Számlák letöltése önálló előfizetéshez

1. Válassza ki az előfizetését az Azure Portal [Előfizetések oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a [számlákhoz hozzáféréssel rendelkező felhasználóként](billing-manage-access.md).

2. Válassza ki a **Számlák** elemet.

    ![A számlázási és a használati lehetőséget megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Kattintson a **Számla letöltése** elemre a PDF formátumú számla másolatának megtekintéséhez. Ha a számla **Nem érhető el**, derítse ki, [miért nem látja a legutóbbi számlázási időszakra vonatkozó számlát](#noinvoice).

    ![A számlázási időszakokat, a letöltési lehetőséget és az egyes számlázási időszakok teljes díját ábrázoló képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. A számlázási időszakra kattintva a napi használati adatokat is megtekintheti.

A számlájára vonatkozó további információért lásd [a Microsoft Azure-számla ismertetését](billing-understand-your-bill.md). A költségek kezelésével kapcsolatos segítségért lásd [az Azure-számlázással és -költségkezeléssel kapcsolatos váratlan költségek megelőzését](billing-getting-started.md) ismertető szakaszt.

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez tartozó számlák letöltése

A Microsoft-ügyfélszerződésben lévő minden [számlázási profilhoz](billing-mca-overview.md#billing-profiles) számlát hoz létre a rendszer. A számlák Azure Portalról történő letöltéséhez a számlázási profil tulajdonosának, közreműködőjének, olvasójának, illetve számlakezelőjének kell lennie.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a *Költségkezelés + számlázás* kifejezésre.
1. Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy először ki kell választania egy számlázási fiókot.
1. Válassza ki a **Számlák** elemet.
1. A számlák rácsos elrendezésében keresse meg a letölteni kívánt számla sorát.
1. Kattintson a sor végén található három pontra (`...`).
    ![A sor végén található három pontot ábrázoló képernyőkép](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. A helyi letöltési menüben válassza a **Számla** lehetőséget.

    ![A helyi menüt ábrázoló képernyőkép](./media/billing-download-azure-invoice/contextmenu.png)

Amennyiben nem látja a legutóbbi számlázási időszakra vonatkozó számlát, tekintse meg az [ezzel a kérdéssel foglalkozó](#noinvoice) szakaszt.

## <a name="get-your-invoice-in-email-pdf"></a>Számla kérése e-mailben (.pdf)

További címzetteket is engedélyezhet és konfigurálhat, akik e-mailben megkapják az Azure-számlát. Előfordulhat, hogy ez a funkció nem érhető el bizonyos előfizetések esetén, például támogatási ajánlatoknál, Nagyvállalati Szerződéseknél vagy az Azure in Open licencprogramban. Ha rendelkezik Microsoft-ügyfélszerződéssel, tekintse meg a következő, [a számlázási profilja számláinak e-mailben történő kézbesítését](#get-your-subscriptions-invoices-in-email) ismertető szakaszt.

### <a name="get-your-subscriptions-invoices-in-email"></a>Az előfizetése számláinak kérése e-mailben

1. Válassza ki az előfizetését az [Előfizetések lapon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Hagyja jóvá az összes előfizetését. Kattintson a **Számlák**, majd a **Számla küldése e-mailben** parancsra.

    ![A jóváhagyási folyamatot ábrázoló képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Kattintson a **jóváhagyási** lehetőségre, és fogadja el a feltételeket.

    ![A jóváhagyási folyamat második lépését ábrázoló képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. A megállapodás elfogadása után konfigurálhatja a további címzetteket. Miután eltávolított egy címzettet, a hozzá tartozó e-mail-cím nem lesz tovább tárolva. Ha meggondolja magát, újra hozzá kell adnia a címzettet.

    ![A jóváhagyási folyamat harmadik lépését ábrázoló képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Ha a lépések végrehajtása után nem kap e-mailt, ellenőrizze, hogy az e-mail-címe helyesen szerepel-e a [profil kommunikációs beállításai között](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Az előfizetése számláinak e-mailben történő kézbesítésének lemondása

A számlák e-mailben történő kézbesítését lemondhatja az alábbi lépések követésével és a **számlák e-mailben történő küldésének lemondására** való kattintással. Ez a beállítás eltávolít minden olyan e-mail-címet, amelyre be volt állítva a számlák küldése e-mailben. Ha újból jóváhagyja a szolgáltatást, újrakonfigurálhatja a címzetteket.

 ![A lemondási folyamatot megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>A Microsoft-ügyfélszerződés számláinak e-mailben történő kézbesítése

Ha rendelkezik Microsoft-ügyfélszerződéssel, engedélyezheti a számlák e-mailes kézbesítését. A számlázási profil összes tulajdonosa, közreműködője, olvasója és számlakezelője e-mailben kapja meg a számlát. Az olvasók nem tudják frissíteni a számlák e-mailes kézbesítésének beállításait.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.
1. Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy először ki kell választania egy számlázási fiókot.
1. A **Beállítások** alatt válassza a **Tulajdonságok** elemet.
1. A **Számla küldése e-mailben** elem alatt válassza a **Számla küldése e-mailben beállítás frissítése** lehetőséget.

    ![Az e-mailben kapott számlák tulajdonságait ábrázoló képernyőkép](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Válassza a **jóváhagyási** lehetőséget.
1. Kattintson a **Frissítés** parancsra.

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>A számlák e-mailben történő kézbesítésének lemondása Microsoft-ügyfélszerződés esetében

A számlák e-mailben történő kézbesítését lemondhatja az alábbi lépések követésével és a **lemondásra** való kattintással. Egyik tulajdonos, közreműködő, olvasó és számlakezelő sem fogja e-mailben megkapni a számlát. Ha Ön olvasó, nem tudja megváltoztatni az e-mailes számlaküldés beállításait.



## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

A számlájával és a díjakkal kapcsolatos további információkért lásd:

- [A Microsoft Azure használati adatainak és díjainak megtekintése és letöltése](billing-download-azure-daily-usage.md)
- [Microsoft Azure-elszámolások értelmezése](billing-understand-your-bill.md)
- [Az Azure-számla feltételeinek értelmezése](billing-understand-your-invoice.md)
- [A Microsoft Azure részletes használati feltételeinek értelmezése](billing-understand-your-usage.md)
- [A szervezet Azure-díjszabásásnak megtekintése](billing-ea-pricing.md)

Ha Microsoft-ügyfélszerződéssel rendelkezik, tekintse meg a következő cikkeket:

- [A számlázási profilhoz tartozó számla díjainak ismertetése](billing-mca-understand-your-bill.md)
- [A számlázási profilhoz tartozó számla feltételeinek ismertetése](billing-mca-understand-your-invoice.md)
- [A számlázási profiljához tartozó, az Azure használati adatait és díjait tartalmazó fájl ismertetése](billing-mca-understand-your-usage.md)
- [A számlázási profil adózási dokumentumainak megtekintése és letöltése](billing-mca-download-tax-document.md)
- [A szervezet Azure-díjszabásásnak megtekintése](billing-ea-pricing.md)
