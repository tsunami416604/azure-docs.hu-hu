---
title: Az Azure számlázási és napi használati adatainak letöltése | Microsoft Docs
description: A cikk azt ismerteti, hogyan töltheti le vagy tekintheti meg az Azure számlázási és napi használati adatait.
keywords: számla,számla letöltése,azure számla,azure használat
services: billing
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 16b2eaef74a7aa0e3e28bfcbb6dbd9da568db6cf
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491409"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Az Azure számlázási és napi használati adatainak letöltése vagy megtekintése

A legtöbb előfizetés esetében a számláját letöltheti az [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) felületéről, de e-mailben is megkaphatja. Amennyiben Ön Nagyvállalati Szerződéssel rendelkező Azure-ügyfél (EA-ügyfél), a szervezet számláit nem tudja letölteni. A rendszer annak küldi el a számlákat, akihez a regisztrációkor a számlák fogadása hozzá lett rendelve.

Amennyiben Ön EA-ügyfél, vagy rendelkezik [Microsoft-ügyfélszerződéssel](#check-access-to-a-microsoft-customer-agreement), a használati adatokat letöltheti az [Azure Portal](https://portal.azure.com/) felületéről. Ha más előfizetéssel rendelkezik, lépjen az [Azure Fiókközpontba](https://account.azure.com/Subscriptions) a használati adatok letöltéséhez.

Csak egyes szerepkörök, például a fiókadminisztrátorok és a vállalati rendszergazdák rendelkeznek engedéllyel a számlák és a használati adatok lekérdezéséhez. További információ a számlázási információhoz való hozzáférésről: [Az Azure-beli számlázáshoz való hozzáférés kezelése szerepkörök használatával](billing-manage-access.md).

Ha rendelkezik Microsoft-ügyfélszerződéssel, a számlázási profil tulajdonosának, közreműködőjének, olvasójának vagy számlakezelőjének kell lennie a számlázási és használati adatok megtekintéséhez. A Microsoft-ügyfélszerződések számlázási szerepköreivel kapcsolatos további információért lásd a [számlázási profil szerepköreit és feladatait](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>Azure-számlák letöltése (.pdf)

A legtöbb előfizetés esetében a számláját letöltheti az Azure Portalról. Ha rendelkezik Microsoft-ügyfélszerződéssel, tekintse meg a számlázási profilhoz tartozó számlák letöltésével foglalkozó szakaszt.

### <a name="download-invoices-for-an-individual-subscription"></a>Számlák letöltése önálló előfizetéshez

1. Válassza ki előfizetését az Azure Portal [Előfizetések oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a [számlákhoz hozzáféréssel rendelkező felhasználóként](billing-manage-access.md).

2. Válassza ki a **Számlák** elemet.

    ![A számlázási és a használati lehetőséget megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Kattintson a **Számla letöltése** elemre a PDF formátumú számla másolatának megtekintéséhez. Ha a számla **Nem érhető el**, derítse ki, [miért nem látja a legutóbbi számlázási időszakra vonatkozó számlát](#noinvoice).

    ![A számlázási időszakokat, a letöltési lehetőséget és az egyes számlázási időszakok teljes díját megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. A számlázási időszakra kattintva a napi használati adatokat is megtekintheti.

A számlájára vonatkozó további információért lásd [a Microsoft Azure-számla ismertetését](billing-understand-your-bill.md). A költségek kezelésével kapcsolatos segítségért lásd [az Azure-számlázással és -költségkezeléssel kapcsolatos váratlan költségek megelőzését](billing-getting-started.md) ismertető szakaszt.

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez tartozó számlák letöltése

A Microsoft-ügyfélszerződésben lévő minden [számlázási profilhoz](billing-mca-overview.md#billing-profiles) számlát hoz létre a rendszer. A számlák Azure Portalról történő letöltéséhez a számlázási profil tulajdonosának, közreműködőjének, olvasójának, illetve számlakezelőjének kell lennie.

1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.
2. Válasszon ki egy számlázási profilt.
3. Válassza ki a **Számlák** elemet.
4. A számlák rácsos elrendezésében keresse meg a letölteni kívánt számla sorát.
5. Kattintson a sor végén található három pontra (`...`).
6. A helyi letöltési menüben válassza a **Számla** lehetőséget.

Amennyiben nem látja a legutóbbi számlázási időszakra vonatkozó számlát, tekintse meg a **további információkat** ismertető szakaszt. <!-- Fix this -->
### <a name="noinvoice"></a> Miért nem látom a legutóbbi számlázási időszakra vonatkozó számlát?

Számos oka lehet annak, hogy nem jelenik meg számla:

- 30 napnál kevesebb telt el az Azure-előfizetés életbe lépése óta.

- A rendszer még nem hozta létre a számlát. Várja meg a számlázási időszak végét.

- Nem rendelkezik engedéllyel a számlák megtekintéséhez. Ha rendelkezik Microsoft-ügyfélszerződéssel, a számlázási profil tulajdonosának, közreműködőjének, olvasójának vagy számlakezelőjének kell lennie. Más előfizetések esetén előfordulhat, hogy nem látja a régi számlákat, ha nem Ön a fiókadminisztrátor. További információ a számlázási információhoz való hozzáférésről: [Az Azure-beli számlázáshoz való hozzáférés kezelése szerepkörök használatával](billing-manage-access.md).

- Ha ingyenes próbaverziós előfizetéssel rendelkezik, vagy nem haladta meg az előfizetésében foglalt jóváírás havi keretét, csak abban az esetben fog számlát kapni, ha Microsoft-ügyfélszerződéssel rendelkezik.

## <a name="get-your-invoice-in-email-pdf"></a>Számla kérése e-mailben (.pdf)

További címzetteket is engedélyezhet és konfigurálhat, akik e-mailben megkapják az Azure-számlát. Előfordulhat, hogy ez a funkció nem érhető el bizonyos előfizetések esetén, például támogatási ajánlatoknál, Nagyvállalati Szerződéseknél vagy az Azure in Open licencprogramban. Ha rendelkezik Microsoft-ügyfélszerződéssel, tekintse meg a számlázási profilja számláinak e-mailben történő kézbesítését ismertető szakaszt.

### <a name="get-your-subscriptions-invoices-in-email"></a>Az előfizetése számláinak kérése e-mailben

1. Válassza ki az előfizetését az [Előfizetések lapon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Hagyja jóvá az összes előfizetését. Kattintson a **Számlák**, majd a **Számla küldése e-mailben** parancsra.

    ![A jóváhagyási folyamatot megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Kattintson a **jóváhagyási** lehetőségre, és fogadja el a feltételeket.

    ![A jóváhagyási folyamat második lépését megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. A megállapodás elfogadása után konfigurálhatja a további címzetteket. Miután eltávolított egy címzettet, a hozzá tartozó e-mail-cím nem lesz tovább tárolva. Ha meggondolja magát, újra hozzá kell adnia a címzettet.

    ![A jóváhagyási folyamat harmadik lépését megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Ha a lépések végrehajtása után nem kap e-mailt, ellenőrizze, hogy az e-mail-címe helyesen szerepel-e a [profil kommunikációs beállításai között](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Az előfizetése számláinak e-mailes kézbesítésének lemondása

Lemondhatja a számlák e-mailes kézbesítését az alábbi lépések követésével, és a **számlák e-mailben történő küldésének lemondására** való kattintással. Ez a beállítás eltávolít minden olyan e-mail-címet, amelyre be volt állítva a számlák küldése e-mailben. Ha újból jóváhagyja a szolgáltatást, újrakonfigurálhatja a címzetteket.

 ![A lemondási folyamatot megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>A Microsoft-ügyfélszerződés számláinak e-mailben történő kézbesítése

Ha rendelkezik Microsoft-ügyfélszerződéssel, engedélyezheti a számlák e-mailes kézbesítését. A számlázási profil összes tulajdonosa, közreműködője, olvasója és számlakezelője e-mailben kapja meg a számlát. Az olvasók nem tudják frissíteni a számlák e-mailes kézbesítésének beállításait.

1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.
1. Válasszon ki egy számlázási profilt.
1. A **Beállítások** alatt válassza a **Tulajdonságok** elemet.
1. A **Számla küldése e-mailben** lehetőség alatt válassza a **Számla küldése e-mailben beállítás frissítése** lehetőséget.
1. Válassza a **jóváhagyási** lehetőséget.
1. Kattintson a **Frissítés** parancsra.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>A számlázási profil számláinak e-mailes kézbesítésének lemondása

Lemondhatja a számlák e-mailes kézbesítését az alábbi lépések követésével, és az **elutasítási** lehetőségre való kattintással. Egyik tulajdonos, közreműködő, olvasó és számlakezelő sem fogja e-mailben megkapni a számlát. Ha Ön olvasó, nem tudja megváltoztatni az e-mailes számlaküldés beállításait.

## <a name="download-usage"></a>Használati adatok letöltése

 A legtöbb előfizetés esetén a részletes napi használatot bemutató fájlt az [Azure Fiókközpontban](https://account.azure.com/Subscriptions) találhatja. Amennyiben Ön EA-ügyfél, vagy rendelkezik Microsoft-ügyfélszerződéssel, a használati adatokat letöltheti az [Azure Portal](https://portal.azure.com/) felületéről. <!-- TO DO: update PayG experience to Ibiza once it ships-->

### <a name="download-usage-from-the-account-center-csv"></a>Használati adatok letöltése a Fiókközpontból (.csv)

1. Jelentkezzen be fiókadminisztrátorként az [Azure Fiókközpontba](https://account.windowsazure.com/subscriptions).

2. Válassza ki azt az előfizetést, amelynek a számláját és a használattal kapcsolatos adatait le szeretné kérni.

3. Válassza a **SZÁMLATÖRTÉNET** lehetőséget.

    ![A számlatörténet lehetőséget megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Megtekintheti az utolsó hat számlázási időszakra és az aktuális, nem számlázott időszakra vonatkozó kivonatokat.

    ![A számlázási időszakokat, a számlák és napi használati adatok letöltési lehetőségeit és az egyes számlázási időszakok teljes díját megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Válassza az **Aktuális számla megtekintése** lehetőséget a költségek azon becslésének megtekintéséhez, amely a becslés létrehozásakor jött létre. A becslések csak naponta egyszer frissülnek, ezért nem feltétlenül tartalmazzák az összes használati adatot. A havi számlája eltérhet ettől a becsléstől.

    ![Az aktuális kivonat megtekintésére szolgáló lehetőséget megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![A becsült jelenlegi költségeket megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Válassza a **Használati adatok letöltése** lehetőséget a napi használati adatok letöltéséhez CSV-fájlként. Ha két elérhető verziót lát, a 2. verziót töltse le.

    ![A használati adatok letöltésére szolgáló lehetőséget megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Csak a fiókadminisztrátor férhet hozzá az Azure Fiókközponthoz. A többi számlázási adminisztrátor, például a tulajdonosok a [Billing API-k](billing-usage-rate-card-overview.md) segítségével kaphatják meg a használati adatokat.

A napi használati adatokra vonatkozó további információért lásd: [Microsoft Azure számlájának megértése](billing-understand-your-bill.md). A költségek kezelésével kapcsolatos segítségért lásd [az Azure-számlázással és -költségkezeléssel kapcsolatos váratlan költségek megelőzését](billing-getting-started.md) ismertető szakaszt.

### <a name="download-usage-for-ea-customers"></a>Használati adatok letöltése EA-ügyfelek esetén

A használati adatok EA-ügyfélként történő megtekintéséhez és letöltéséhez vállalati rendszergazdának, fióktulajdonosnak vagy részlegszintű rendszergazdának kell lennie, és engedélyezni kell a költségek megtekintésére vonatkozó szabályzatot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a *Költségkezelés + számlázás* kifejezésre.

    ![Képernyőkép az Azure Portal keresőmezőjéről](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Válassza a **Felhasználás + díjak** lehetőséget.
1. Válassza ki a **Letöltés** elemet a letölteni kívánt hónapnál.

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>A Microsoft-ügyfélszerződéshez tartozó használati adatok letöltése

Egy számlázási profil használati adatainak megtekintéséhez és letöltéséhez a számlázási profil tulajdonosának, közreműködőjének, olvasójának, illetve számlakezelőjének kell lennie.

#### <a name="download-usage-for-billed-charges"></a>Használati adatok letöltése kiszámlázott díjak esetén

1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.
2. Válasszon ki egy számlázási profilt.
3. Válassza ki a **Számlák** elemet.
4. A számlák rácsos elrendezésében keresse meg a letölteni kívánt használati adatnak megfelelő számla sorát.
5. Kattintson a sor végén található három pontra (`...`).
6. A helyi letöltési menüben válassza az **Azure-használat és -díjak** lehetőséget.

#### <a name="download-usage-for-open-charges"></a>Használati adatok letöltése még nem számlázott díjak esetén

Letöltheti a jelenlegi számlázási időszak aktuális hónapjának használati adatait is, ami azokat a díjakat tartalmazza, amelyek még nem lettek kiszámlázva.

1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.
2. Válasszon ki egy számlázási profilt.
3. Az **Áttekintés** panelen kattintson **Az Azure használati adatainak és díjainak letöltése** parancsra.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

A számlájával és a díjakkal kapcsolatos további információkért lásd:

- [Microsoft Azure-elszámolások értelmezése](billing-understand-your-bill.md)
- [Az Azure-számla feltételeinek értelmezése](billing-understand-your-invoice.md)
- [A Microsoft Azure részletes használati feltételeinek értelmezése](billing-understand-your-usage.md)
- [A szervezet Azure-díjszabásának megtekintése](billing-ea-pricing.md)

Ha Microsoft-ügyfélszerződéssel rendelkezik, tekintse meg a következő cikkeket:

- [A számlázási profilhoz tartozó számla díjainak ismertetése](billing-mca-understand-your-bill.md)
- [A számlázási profilhoz tartozó számla feltételeinek ismertetése](billing-mca-understand-your-invoice.md)
- [A számlázási profiljához tartozó, az Azure használati adatait és díjait tartalmazó fájl ismertetése](billing-mca-understand-your-usage.md)
- [A számlázási profil adózási dokumentumainak megtekintése és letöltése](billing-mca-download-tax-document.md)
- [A szervezet Azure-díjszabásának megtekintése](billing-ea-pricing.md)
