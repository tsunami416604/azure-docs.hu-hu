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
ms.openlocfilehash: 80ec40a7411a370460d663084f9f7034b28e1a2e
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375754"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>A Microsoft Azure-számla megtekintése és letöltése

A legtöbb előfizetés esetében a számláját letöltheti az [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) felületéről, de e-mailben is megkaphatja. Amennyiben Ön Nagyvállalati Szerződéssel rendelkező Azure-ügyfél (EA-ügyfél), a szervezet számláit nem tudja letölteni. A rendszer annak küldi el a számlákat, akihez a regisztrációkor a számlák fogadása hozzá lett rendelve.

Csak bizonyos szerepkörök rendelkeznek a számlák megtekintéséhez szükséges engedéllyel. Ilyen szerepkör a fiókadminisztrátor és a vállalati rendszergazda. További információ a számlázási információhoz való hozzáférésről: [Az Azure-beli számlázáshoz való hozzáférés kezelése szerepkörök használatával](billing-manage-access.md).

Ha rendelkezik Microsoft-ügyfélszerződéssel (MCA), a következő szerepkörök egyikével is rendelkeznie kell a számlák lekéréséhez:

- Számlázásiprofil-tulajdonos
- Közreműködő
- Olvasó
- Számlakezelő

Ha Microsoft-partnerszerződéssel (MPA) rendelkezik, a partnerszervezet globális rendszergazdai vagy rendszergazdai ügynök szerepkörével kell rendelkeznie az Azure-számlák megtekintéséhez és letöltéséhez. [Ellenőrizze a számlázási fiókja típusát](#check-your-billing-account-type), hogy megtudja, mely jogosultságokra van szüksége. 

<!-- For more information about billing roles for Microsoft Customer Agreements, see [Billing profile roles and tasks](billing-understand-mca-roles.md#billing-profile-roles-and-tasks). -->

## <a name="noinvoice"></a> Lehetséges okok, amiért nem kaphatja meg a számlát

Számos oka lehet annak, hogy nem jelenik meg számla:

- 30 napnál kevesebb telt el az Azure-előfizetés életbe lépése óta.

- Az Azure a számlázási időszak végén küld Önnek számlát. Lehetséges, hogy még nem készült számla. Várja meg a számlázási időszak végét.

- Nem rendelkezik engedéllyel a számlák megtekintéséhez. Ha MCA- vagy MPA-fiókkal rendelkezik, a számlázási profil tulajdonosának, közreműködőjének, olvasójának vagy számlakezelőjének kell lennie. Más előfizetések esetén előfordulhat, hogy nem látja a régi számlákat, ha nem Ön a fiókadminisztrátor. További információ a számlázási információhoz való hozzáférésről: [Az Azure-beli számlázáshoz való hozzáférés kezelése szerepkörök használatával](billing-manage-access.md).

- Ha ingyenes próbaverzióval vagy a havi kreditösszeggel rendelkezik az előfizetésén, csak akkor fog számlát kapni, ha túllépi a havi kreditösszeget. Ha Microsoft-ügyfélszerződéssel vagy -partnerszerződéssel rendelkezik, minden esetben fog számlát kapni. 

## <a name="download-invoices-in-the-azure-portal"></a>Számlák letöltése az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a *Költségkezelés + számlázás* kifejezésre.
1. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot vagy számlázási profilt.
1. A bal oldali menüben válassza a **Számlázás** területen lévő **Számlák** elemet.
1. A számlák rácsos elrendezésében keresse meg a letölteni kívánt számla sorát.
1. Kattintson a sor végén található letöltés ikonra vagy három pontra (`...`).
1. A letöltési menüben válassza a **Számla** lehetőséget.

A számlájára vonatkozó további információért lásd [a Microsoft Azure-számla ismertetését](billing-understand-your-bill.md). A költségek kezelésével kapcsolatos segítségért lásd [az Azure-számlázással és -költségkezeléssel kapcsolatos váratlan költségek megelőzését](billing-getting-started.md) ismertető szakaszt.

## <a name="get-your-subscriptions-invoices-in-email"></a>Az előfizetése számláinak kérése e-mailben

További címzetteket is engedélyezhet és konfigurálhat, akik e-mailben megkapják az Azure-számlát. Előfordulhat, hogy ez a funkció nem érhető el bizonyos előfizetések esetén, például támogatási ajánlatoknál, Nagyvállalati Szerződéseknél vagy az Azure in Open licencprogramban.

1. Válassza ki az előfizetését az [Előfizetések lapon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Hagyja jóvá az összes előfizetését. Kattintson a **Számlák**, majd a **Számla küldése e-mailben** parancsra.

    ![A jóváhagyási folyamatot ábrázoló képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Kattintson a **jóváhagyási** lehetőségre, és fogadja el a feltételeket.

    ![A jóváhagyási folyamat második lépését ábrázoló képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. A megállapodás elfogadása után konfigurálhatja a további címzetteket. Miután eltávolított egy címzettet, a hozzá tartozó e-mail-cím nem lesz tovább tárolva. Ha meggondolja magát, újra hozzá kell adnia a címzettet.

    ![A jóváhagyási folyamat harmadik lépését ábrázoló képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Ha a lépések végrehajtása után nem kap e-mailt, ellenőrizze, hogy az e-mail-címe helyesen szerepel-e a [profil kommunikációs beállításai között](https://account.windowsazure.com/profile).

## <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Az előfizetése számláinak e-mailben történő kézbesítésének lemondása

A számlák e-mailben történő kézbesítését lemondhatja az alábbi lépések követésével és a **számlák e-mailben történő küldésének lemondására** való kattintással. Ez a beállítás eltávolít minden olyan e-mail-címet, amelyre be volt állítva a számlák küldése e-mailben. Ha újból jóváhagyja a szolgáltatást, újrakonfigurálhatja a címzetteket.

 ![A lemondási folyamatot megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

<!-- Does following section apply to MPA too? -->
## <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>A Microsoft-ügyfélszerződés számláinak e-mailben történő kézbesítése

Ha rendelkezik Microsoft-ügyfélszerződéssel, engedélyezheti a számlák e-mailes kézbesítését. A számlázási profil összes tulajdonosa, közreműködője, olvasója és számlakezelője e-mailben kapja meg a számlát. Az olvasók nem tudják frissíteni a számlák e-mailes kézbesítésének beállításait.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.
1. Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy először ki kell választania egy számlázási fiókot.
1. A **Beállítások** alatt válassza a **Tulajdonságok** elemet.
1. A **Számla küldése e-mailben** elem alatt válassza a **Számla küldése e-mailben beállítás frissítése** lehetőséget.

    ![Az e-mailben kapott számlák tulajdonságait ábrázoló képernyőkép](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Válassza a **jóváhagyási** lehetőséget.
1. Kattintson a **Frissítés** parancsra.

## <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>A számlák e-mailben történő kézbesítésének lemondása Microsoft-ügyfélszerződés esetében

A számlák e-mailben történő kézbesítését lemondhatja az alábbi lépések követésével és a **lemondásra** való kattintással. Egyik tulajdonos, közreműködő, olvasó és számlakezelő sem fogja e-mailben megkapni a számlát. Ha Ön olvasó, nem tudja megváltoztatni az e-mailes számlaküldés beállításait.

## <a name="check-your-billing-account-type"></a>A számlázási fiók típusának ellenőrzése
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

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
