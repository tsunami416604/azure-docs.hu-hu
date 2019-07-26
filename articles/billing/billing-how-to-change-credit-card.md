---
title: Hitelkártya módosítása az Azure-ban
description: Ismerteti, hogyan lehet módosítani az Azure-előfizetés fizetéséhez használt bankkártyát.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: 7719ae83525883a6d3f014dbb99877b7319f2ccd
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383662"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Azure-beli bankkártya hozzáadása, frissítése vagy eltávolítása

A Azure Portal hozzáadhat egy új bankkártyát, frissítheti a meglévő bankkártyákat, vagy törölheti a nem használt bankkártyát is. A módosítások elvégzéséhez [fiók](billing-subscription-transfer.md#whoisaa) -rendszergazdának kell lennie.

Ha rendelkezik Microsoft- [ügyfél](#check-access-to-a-microsoft-customer-agreement)-szerződéssel, a fizetési módszerek a számlázási profilokhoz vannak társítva. Megtudhatja, hogyan [módosíthatja a számlázási profil alapértelmezett fizetési módját](#change-payment-method-for-a-billing-profile). Csak az Azure-ra regisztrált felhasználó tudja frissíteni a fizetési módot.

**Szeretne számlát váltani (ellenőrzés/átutalás)?** Lásd: Azure-előfizetések [fizetése számla alapján](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Új bankkártya hozzáadása egy Azure-előfizetéshez

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) a fiók rendszergazdájaként.
1. Keressen rá **Cost Management + számlázásra**.

    ![A keresést bemutató képernyőkép](./media/billing-how-to-change-credit-card/search.png)

1. Válassza ki azt az előfizetést, amelyhez hozzá szeretné adni a hitelkártyát.
1. Válassza a **Fizetési módok** elemet.

    ![A kiválasztott fizetési módok kezelése lehetőséget megjelenítő képernyőkép.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Kártya hozzáadásához válassza a „+” gombot a bal felső sarokban. A jobb oldalon megjelenik egy hitelkártyaűrlap.
1. Adja meg a bankkártya adatait.

    ![Képernyőkép, amely egy új kártya hozzáadását mutatja be.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Ha ezt a kártyát aktív fizetési módszerként szeretné kijelölni, jelölje be ezt a jelölőnégyzetet, ha azt szeretné, hogy **Ez az aktív fizetési mód** az űrlap fölött legyen. Ez a kártya lesz innentől az aktív fizetési eszköz az összes olyan előfizetés esetében, amely a kiválasztott előfizetéssel azonos kártyát használ.

1. Kattintson a **Tovább** gombra.

Ha a bankkártya hozzáadása után hibaüzenetet kap, tekintse meg az [Azure-regisztráció során visszautasított hitelkártya](billing-credit-card-fails-during-azure-sign-up.md)című témakört.

## <a name="update-existing-credit-card"></a>Meglévő hitelkártya frissítése

Ha a bankkártya megújul, és a szám változatlan marad, frissítse a meglévő bankkártyás adatokat, például a lejárati dátumot. Ha a hitelkártyaszám megváltozása miatt megszakad a kártya elvesztése, ellopása vagy lejárta, kövesse a [bankkártya hozzáadása fizetési módként](#addcard) című szakasz lépéseit. Nem kell frissítenie a CVV-t.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) a fiók rendszergazdájaként.
1. Keressen rá **Cost Management + számlázásra**.

    ![A keresést bemutató képernyőkép](./media/billing-how-to-change-credit-card/search.png)

1. Válassza a **Fizetési módok** elemet.

    ![A kiválasztott fizetési módok kezelése lehetőséget megjelenítő képernyőkép.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Kattintson a szerkeszteni kívánt hitelkártyára. A jobb oldalon megjelenik egy hitelkártyaűrlap.

    ![A bankkártya kiválasztását bemutató képernyőkép.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. A bankkártya adatainak frissítése.
1. Kattintson a **Mentés** gombra.

## <a name="use-a-different-credit-card"></a>Használjon másik bankkártyát

Ha az előfizetések több mint egy aktív fizetési móddal rendelkeznek, akkor az aktív fizetési mód Ezen előfizetések bármelyikén való módosítása az aktív fizetési módot is frissíti a többinél.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) a fiók rendszergazdájaként.
1. Keressen rá **Cost Management + számlázásra**.

    ![A keresést bemutató képernyőkép](./media/billing-how-to-change-credit-card/search.png)

1. Válassza ki azt az előfizetést, amelyhez hozzá szeretné adni a hitelkártyát.
1. Válassza a **Fizetési módok** elemet.

    ![A kiválasztott fizetési módok kezelése lehetőséget megjelenítő képernyőkép.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Jelölje be azon kártya melletti jelölőnégyzetet, amelyre az aktív fizetési módot szeretné tenni.
1. Kattintson az **aktív beállítása**elemre.
    ![Képernyőfelvétel, amely a kiválasztott bankkártyát és az aktív beállítást mutatja.](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>Hitelkártya eltávolítása a fiókból

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) a fiók rendszergazdájaként.
1. A lap bal oldalán válassza a **Cost Management + számlázás** lehetőséget.

    ![A keresést bemutató képernyőkép](./media/billing-how-to-change-credit-card/search.png)

1. A **számlázás**területen válassza a **fizetési módok**lehetőséget.

    ![A kiválasztott fizetési módok kezelése lehetőséget megjelenítő képernyőkép.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Jelölje be az eltávolítani kívánt kártya melletti jelölőnégyzetet.
1. Kattintson a **Törlés** gombra.

Ha a bankkártya a Microsoft-előfizetések bármelyikének aktív fizetési módja, nem távolíthatja el az Azure-fiókjából. Módosítsa az aktív fizetési módot az ehhez a hitelkártyához társított összes előfizetés esetében, és próbálkozzon újra
<!-- # Add, update, or remove a credit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## Add a new credit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit card details.
1. Select **Save**.

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card.

## Remove a credit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->

## <a name="change-payment-method-for-a-billing-profile"></a>Számlázási profil fizetési módjának módosítása

A számlázási profil fizetési módjának módosításához az Azure-ra regisztrált személynek kell lennie.

Ha át szeretné váltani az alapértelmezett fizetési módot az ellenőrzések és a huzalok átadására, megtudhatja, hogyan [válthat át számlázási profilt az ellenőrzések és](billing-how-to-pay-by-invoice.md)a huzalok átvitelére.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Keresés **Cost Management és számlázás**.
1. A bal oldali menüben kattintson a **Számlázási profilok**elemre.

    ![a számlázási profilt bemutató képernyőkép a menüben](./media/billing-how-to-change-credit-card/billing-profile.png)

1. Válasszon számlázási profilt.
1. A bal oldali menüben válassza a **fizetési módok**lehetőséget.

   ![A fizetési módokat bemutató képernyőkép a menüben](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. Az alapértelmezett fizetési mód felett kattintson a **módosítás**gombra.

    ![A Change (módosítás) gombot megjelenítő képernyőkép](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. Válasszon ki egy meglévő kártyát, vagy vegyen fel egy újat.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
A következő fejezetek a hitelkártya-információk módosításával kapcsolatos gyakori kérdésekre adnak választ.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Az előfizetésem le van tiltva. Miért nem tudom törölni a bankkártyát?

Az előfizetés letiltását vagy megszakadása után várjon 90 nappal az előfizetés végleges törlése előtt. A fizetési módot a megőrzési időszak során tartjuk a fájlon, ha újra szeretné aktiválni az előfizetést. Ezt követően véglegesen törlődik az előfizetés.

Ha el kell távolítania a hitelkártyáját a 90 napos megőrzési időszak lejárta előtt, [aktiválja újra az](billing-subscription-become-disable.md)előfizetését. Ha nem tud újraaktiválni, [forduljon az Azure ügyfélszolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Miért tartom, hogy "a bejelentkezési munkamenet lejárt. Kattintson ide a bejelentkezéshez?

Ha továbbra is ezt a hibaüzenetet használja, még akkor is, ha már kijelentkezett, és ismét be van jelentkezve, próbálkozzon újra egy privát böngészési munkamenettel.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Hogyan az egyes előfizetésekhez külön kártyát használok?

Sajnos, ha az előfizetései már ugyanazt a kártyát használják, nem lehet elkülöníteni őket különböző kártyák használatára. Ha azonban új előfizetésre regisztrál, dönthet úgy, hogy új fizetési módot használ ehhez az előfizetéshez.

### <a name="how-do-i-make-payments"></a>Hogyan a kifizetéseket?

Ha hitelkártyát állít be fizetési módszerként, akkor az egyes számlázási időszakok után automatikusan felszámítjuk a kártyát. Semmit nem kell tennie.

Ha számlán [fizet](billing-how-to-pay-by-invoice.md), küldje el a befizetését a számla alján felsorolt helyre.

### <a name="how-do-i-change-the-tax-id"></a>Hogyan megváltoztatja az adórendszer AZONOSÍTÓját?

Az adóazonosító hozzáadásához vagy frissítéséhez frissítse a profilt a [Azure Fiókközpont](https://account.azure.com/Profile), majd válassza az **adó rekord**elemet. Az adóazonosító az adómentesség kiszámításához szükséges, és megjelenik a számlán.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft ügyfél-szerződéshez való hozzáférés engedélyezése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- Ismerje meg az [Azure](billing-save-compute-costs-reservations.md) -foglalásokat, és ellenőrizze, hogy tud-e pénzt takaríthat meg.
