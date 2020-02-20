---
title: A Microsoft Azure-számla megtekintése és letöltése
description: A cikk a Microsoft Azure-számlák megtekintését és letöltését mutatja be.
keywords: számlázás,számla,számla letöltése,azure-számla,azure-használat
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 691d27acebf238e84265870e8c01976bfc2412b2
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200265"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>A Microsoft Azure-számla megtekintése és letöltése

A legtöbb előfizetés esetében a számláját letöltheti az [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) felületéről, de e-mailben is megkaphatja. Amennyiben Ön Nagyvállalati Szerződéssel rendelkező Azure-ügyfél (EA-ügyfél), a szervezet számláit nem tudja letölteni. A rendszer annak küldi el a számlákat, akihez a regisztrációkor a számlák fogadása hozzá lett rendelve.

Csak bizonyos szerepkörök rendelkeznek a számlák megtekintéséhez szükséges engedéllyel. Ilyen szerepkör a fiókadminisztrátor és a vállalati rendszergazda. További információ a számlázási információhoz való hozzáférésről: [Az Azure-beli számlázáshoz való hozzáférés kezelése szerepkörök használatával](../manage/manage-billing-access.md).

Ha rendelkezik Microsoft-ügyfélszerződéssel (MCA), a következő szerepkörök egyikével is rendelkeznie kell a számlák lekéréséhez:

- Számlázásiprofil-tulajdonos
- Közreműködő
- Olvasó
- Számlakezelő

Ha Microsoft-partnerszerződéssel (MPA) rendelkezik, a partnerszervezet globális rendszergazdai vagy rendszergazdai ügynök szerepkörével kell rendelkeznie az Azure-számlák megtekintéséhez és letöltéséhez. [Ellenőrizze a számlázási fiókja típusát](#check-your-billing-account-type), hogy megtudja, mely jogosultságokra van szüksége.

<!-- For more information about billing roles for Microsoft Customer Agreements, see [Billing profile roles and tasks](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks). -->

## <a name="noinvoice"></a> Lehetséges okok, amiért nem kaphatja meg a számlát

Számos oka lehet annak, hogy nem jelenik meg számla:

- 30 napnál kevesebb telt el az Azure-előfizetés életbe lépése óta.

- Az Azure a számlázási időszak végén küld Önnek számlát. Lehetséges, hogy még nem készült számla. Várja meg a számlázási időszak végét.

- Nem rendelkezik engedéllyel a számlák megtekintéséhez. Ha MCA- vagy MPA-fiókkal rendelkezik, a számlázási profil tulajdonosának, közreműködőjének, olvasójának vagy számlakezelőjének kell lennie. Más előfizetések esetén előfordulhat, hogy nem látja a régi számlákat, ha nem Ön a fiókadminisztrátor. További információ a számlázási információhoz való hozzáférésről: [Az Azure-beli számlázáshoz való hozzáférés kezelése szerepkörök használatával](../manage/manage-billing-access.md).

- Ha ingyenes próbaverzióval vagy a havi kreditösszeggel rendelkezik az előfizetésén, csak akkor fog számlát kapni, ha túllépi a havi kreditösszeget. Ha Microsoft-ügyfélszerződéssel vagy -partnerszerződéssel rendelkezik, minden esetben fog számlát kapni.

## <a name="download-invoices-in-the-azure-portal"></a>Számlák letöltése az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a *Költségkezelés + számlázás* kifejezésre.
1. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot vagy számlázási profilt.
1. A bal oldali menüben válassza a **Számlázás** területen lévő **Számlák** elemet.
1. A számlák rácsos elrendezésében keresse meg a letölteni kívánt számla sorát.
1. Kattintson a sor végén található letöltés ikonra vagy három pontra (`...`).
1. A letöltési menüben válassza a **Számla** lehetőséget.

A számlájára vonatkozó további információért lásd [a Microsoft Azure-számla ismertetését](review-individual-bill.md). A költségek kezelésével kapcsolatos segítségért lásd [az Azure-számlázással és -költségkezeléssel kapcsolatos váratlan költségek megelőzését](../manage/getting-started.md) ismertető szakaszt.

## <a name="get-your-subscriptions-invoices-in-email"></a>Az előfizetése számláinak kérése e-mailben

További címzetteket is engedélyezhet és konfigurálhat, akik e-mailben megkapják az Azure-számlát. Előfordulhat, hogy ez a funkció nem érhető el bizonyos előfizetések esetén, például támogatási ajánlatoknál, Nagyvállalati Szerződéseknél vagy az Azure in Open licencprogramban.

1. Válassza ki az előfizetését az [Előfizetések lapon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Hagyja jóvá az összes előfizetését. Kattintson a **Számlák**, majd a **Számla küldése e-mailben** parancsra.

    ![A jóváhagyási folyamatot ábrázoló képernyőkép](./media/download-azure-invoice/invoicesdeeplink01.png)

2. Kattintson a **jóváhagyási** lehetőségre, és fogadja el a feltételeket.

    ![A jóváhagyási folyamat második lépését ábrázoló képernyőkép](./media/download-azure-invoice/invoicearticlestep02.png)

3. A megállapodás elfogadása után konfigurálhatja a további címzetteket. Miután eltávolított egy címzettet, a hozzá tartozó e-mail-cím nem lesz tovább tárolva. Ha meggondolja magát, újra hozzá kell adnia a címzettet.

    ![A jóváhagyási folyamat harmadik lépését ábrázoló képernyőkép](./media/download-azure-invoice/invoicearticlestep03.png)

Ha a lépések végrehajtása után nem kap e-mailt, ellenőrizze, hogy az e-mail-címe helyesen szerepel-e a [profil kommunikációs beállításai között](https://account.windowsazure.com/profile).

## <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Az előfizetése számláinak e-mailben történő kézbesítésének lemondása

A számlák e-mailben történő kézbesítését lemondhatja az alábbi lépések követésével és a **számlák e-mailben történő küldésének lemondására** való kattintással. Ez a beállítás eltávolít minden olyan e-mail-címet, amelyre be volt állítva a számlák küldése e-mailben. Ha újból jóváhagyja a szolgáltatást, újrakonfigurálhatja a címzetteket.

 ![A lemondási folyamatot megjelenítő képernyőkép](./media/download-azure-invoice/invoicearticlestep04.png)

<!-- Does following section apply to MPA too? -->
## <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>A Microsoft-ügyfélszerződés számláinak e-mailben történő kézbesítése

Ha rendelkezik Microsoft Ügyfélszerződéshez tartozó számlázási fiókkal, engedélyezheti a számlák e-mailes kézbesítését. A számlázási profil esetében tulajdonos, közreműködő, olvasó, illetve számlakezelő szerepkörhöz tartozó felhasználók egyaránt megkapják a számlát e-mailben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az előfizetés kifejezés kereséséről a portálon](./media/download-azure-invoice/search-cmb.png)

1. Válassza a **Számlázási profilok** lehetőséget a bal oldalon. A számlázási profilok listájában válasszon ki egy számlázási profilt, hogy az ahhoz tartozó számlákat e-mailben megkaphassa.

   [![Képernyőkép a számlázási profilok listájáról](./media/download-azure-invoice/mca-select-profile.png)](./media/download-azure-invoice/mca-select-profile-zoomed-in.png#lightbox)

1. A bal oldalon válassza ki a **Tulajdonságok**, majd a **Számla küldése e-mailben beállítás frissítése** elemet.

   [![Képernyőkép a számlázási profilok listájáról](./media/download-azure-invoice/mca-select-update-email-preferences.png)](./media/download-azure-invoice/mca-select-update-email-preferences.png#lightbox)

1. Válassza a **Jóváhagyás** lehetőséget, majd kattintson a **Frissítés** elemre.

   [![Képernyőkép a számlázási profilok listájáról](./media/download-azure-invoice/mca-select-email-opt-in.png)](./media/download-azure-invoice/mca-select-email-opt-in.png#lightbox)

## <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>A számlák e-mailben történő kézbesítésének lemondása Microsoft-ügyfélszerződés esetében

A számlák e-mailben történő kézbesítését lemondhatja az alábbi lépések követésével és a **lemondásra** való kattintással. A tulajdonos, közreműködő, olvasó, illetve számlakezelő szerepkörökhöz tartozó felhasználók nem kapják meg e-mailben a számlát.

## <a name="give-others-access-to-your-microsoft-customer-agreement-invoices"></a>Hozzáférés megadása másoknak a Microsoft Ügyfélszerződéshez tartozó számláihoz

Engedélyezheti másoknak a számlák megtekintését, letöltését és befizetését, amennyiben számlakezelő szerepkört rendel hozzájuk a számlázási profilra vonatkozóan. Ha jogosult arra, hogy megkapja a számlát e-mailben, ezek a felhasználók szintén jogosulttá válnak rá.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az előfizetés kifejezés kereséséről a portálon](./media/download-azure-invoice/search-cmb.png)

1. Válassza a **Számlázási profilok** lehetőséget a bal oldalon. A számlázási profilok listájában válassza ki azt a számlázási profilt, amelyhez számlakezelő szerepkört szeretne rendelni.

   [![Képernyőkép a számlázási profilok listájáról](./media/download-azure-invoice/mca-select-profile.png)](./media/download-azure-invoice/mca-select-profile-zoomed-in.png#lightbox)

1. A bal oldalon válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget, majd a lap tetején található **Hozzáadás** elemet.

   [![Képernyőkép a hozzáférés-vezérlési lapról](./media/download-azure-invoice/mca-select-access-control.png)](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png#lightbox)

1. A Szerepkör legördülő listában válassza a **Számlakezelő** elemet. Adja meg annak a felhasználónak az e-mail-címét, aki számára hozzáférést kíván biztosítani. A szerepkör hozzárendeléséhez válassza a **Mentés** lehetőséget.

   [![Képernyőkép egy felhasználó számlakezelőként való hozzáadásáról](./media/download-azure-invoice/mca-added-invoice-manager.png)](./media/download-azure-invoice/mca-added-invoice-manager.png#lightbox)

## <a name="check-your-billing-account-type"></a>A számlázási fiók típusának ellenőrzése
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

A számlájával és a díjakkal kapcsolatos további információkért lásd:

- [A Microsoft Azure használati adatainak és díjainak megtekintése és letöltése](download-azure-daily-usage.md)
- [Microsoft Azure-elszámolások értelmezése](review-individual-bill.md)
- [Az Azure-számla feltételeinek értelmezése](understand-invoice.md)
- [A Microsoft Azure részletes használati feltételeinek értelmezése](understand-usage.md)
- [A szervezet Azure-díjszabásásnak megtekintése](../manage/ea-pricing.md)

Ha Microsoft-ügyfélszerződéssel rendelkezik, tekintse meg a következő cikkeket:

- [A számlázási profilhoz tartozó számla díjainak ismertetése](review-customer-agreement-bill.md)
- [A számlázási profilhoz tartozó számla feltételeinek ismertetése](mca-understand-your-invoice.md)
- [A számlázási profiljához tartozó, az Azure használati adatait és díjait tartalmazó fájl ismertetése](mca-understand-your-usage.md)
- [A számlázási profil adózási dokumentumainak megtekintése és letöltése](mca-download-tax-document.md)
- [A szervezet Azure-díjszabásásnak megtekintése](../manage/ea-pricing.md)
