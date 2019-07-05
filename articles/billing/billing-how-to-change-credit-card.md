---
title: A megadott bankkártyára módosítása az Azure-hoz
description: Ismerteti, hogyan lehet Azure-előfizetés díjfizetéséhez használt bankkártya módosítása.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 7c04e33d6199d3930be28ce84458e9c3a743eb8a
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491329"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Hozzáadása, módosítása és bankkártya eltávolítása az Azure-hoz

Az Azure Portalon adjon hozzá egy új hitelkártya, frissítse a meglévő bankkártyák, vagy törölje a nem használt bankkártya. Kell egy [felhasználóifiók-adminisztrátor](billing-subscription-transfer.md#whoisaa) módosítások végrehajtásához.

Ha rendelkezik egy [Microsoft Ügyfélszerződéséhez](#check-access-to-a-microsoft-customer-agreement), fizetési módok társítva, a számlázási profilok. Ismerje meg, hogyan [számlázási profilja alapértelmezett fizetési módjának módosítása](#change-payment-method-for-a-billing-profile). Csak az Azure-ban regisztrált felhasználó frissítheti a fizetési módot.

**Szeretne váltani a számla ellenében (ellenőrzés/átviteli átvitel)?** Lásd: [Azure előfizetések fizetése számla](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Új hitelkártya felvétele egy Azure-előfizetéshez

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) fiók rendszergazdaként.
1. Keresse meg **Cost Management és számlázás**.

    ![Keresés bemutató képernyőkép](./media/billing-how-to-change-credit-card/search.png)

1. Válassza ki az előfizetést, amelyet szeretne hozzáadni a hitelkártyára.
1. Válassza a **Fizetési módok** elemet.

    ![Képernyőkép a kezelés fizetési módszereket lehetőség van kijelölve.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Kártya hozzáadásához válassza a „+” gombot a bal felső sarokban. A jobb oldalon megjelenik egy hitelkártyaűrlap.
1. Adja meg a hitelkártya adatait.

    ![Képernyőkép egy új kártya hozzáadása.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Legyen ez a kártya az aktív fizetési módot, jelölje be a jelölőnégyzetet a **legyen ez az aktív fizetési mód** az űrlap felett. Ez a kártya lesz innentől az aktív fizetési eszköz az összes olyan előfizetés esetében, amely a kiválasztott előfizetéssel azonos kártyát használ.

1. Kattintson a **Tovább** gombra.

Miután hozzáadta a hitelkártya hibaüzenetet kap, ha [Azure-előfizetés elutasított bankkártya](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-card"></a>Meglévő hitelkártya módosítása

Ha hitelkártyáját megújítani lekérdezi és változatlan marad, a szám, frissítse a meglévő hitelkártya adatait, például a lejárati dátum. Ha a hitelkártya száma módosításokat, mert a kártya elveszik, ellopják, vagy lejárt, kövesse a a [hitelkártya hozzáadásához a fizetési módként alkalmazott](#addcard) szakaszban. Nem kell frissíteni a CVV.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) fiók rendszergazdaként.
1. Keresse meg **Cost Management és számlázás**.

    ![Keresés bemutató képernyőkép](./media/billing-how-to-change-credit-card/search.png)

1. Válassza a **Fizetési módok** elemet.

    ![Képernyőkép a kezelés fizetési módszereket lehetőség van kijelölve.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Kattintson a szerkeszteni kívánt hitelkártya. A jobb oldalon megjelenik egy hitelkártyaűrlap.

    ![Képernyőkép a kiválasztott hitelkártya.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Frissítse a hitelkártya-adatait.
1. Kattintson a **Mentés** gombra.

## <a name="use-a-different-credit-card"></a>Más hitelkártya használatára

Ha egynél több előfizetés nincs aktív fizetési módját, majd módosítja az aktív fizetési mód bármelyik ezeket az előfizetéseket is frissíti a többinél pedig az aktív fizetési módot.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) fiók rendszergazdaként.
1. Keresse meg **Cost Management és számlázás**.

    ![Keresés bemutató képernyőkép](./media/billing-how-to-change-credit-card/search.png)

1. Válassza ki az előfizetést, amelyet szeretne hozzáadni a hitelkártyára.
1. Válassza a **Fizetési módok** elemet.

    ![Képernyőkép a kezelés fizetési módszereket lehetőség van kijelölve.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Jelölje be a kártya, győződjön meg arról, az aktív fizetési mód szeretné melletti.
1. Kattintson a **beállítás aktívként**.
    ![Hitelkártya kiválasztva, és állítsa be az aktív megjelenítő képernyőkép.](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>Bankkártya eltávolítása a fiókból

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) fiók rendszergazdaként.
1. Válassza ki **Költségkezelés + számlázás** az oldal bal oldalán.

    ![Keresés bemutató képernyőkép](./media/billing-how-to-change-credit-card/search.png)

1. A **számlázási**válassza **fizetési módok**.

    ![Képernyőkép a kezelés fizetési módszereket lehetőség van kijelölve.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Válassza ki az eltávolítani kívánt kártya melletti jelölőnégyzetbe.
1. Kattintson a **Törlés** gombra.

Ha a megadott bankkártyára az aktív fizetési módot, a Microsoft-előfizetések egyike, nem távolítható el az Azure-fiókjával. A hitelkártyához társított összes előfizetéshez tartozó a aktív fizetési módot, és próbálkozzon újra
<!-- # Add, update, or remove a credit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## Add a new credit or debit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit or debit card details.
1. Select **Save**.

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit or debit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit or debit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card.

## Remove a credit or debit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->

## <a name="change-payment-method-for-a-billing-profile"></a>A számlázási profil fizetési mód megváltoztatása

Ha módosítani szeretné a fizetési módot, a számlázási profil, a személy, aki regisztrált az Azure kell lennie.

Ha szeretne váltani, az alapértelmezett fizetési mód ellenőrzése/átviteli átvitel, ismerje meg, hogyan [számlázási ellenőrzése/átviteli átviteli profilt váltson](billing-how-to-pay-by-invoice.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Keresés **Cost Management és számlázás**.
1. A bal oldali menüben kattintson a **számlázási profilok**.

    ![Képernyőkép a számlázási profilja menü](./media/billing-how-to-change-credit-card/billing-profile.png)

1. Válassza ki a számlázási profilt.
1. A bal oldali menüben válassza **fizetési módok**.

   ![Képernyőkép a fizetési módok menü](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. Kattintson fent az alapértelmezett fizetési mód **módosítás**.

    ![Képernyőkép a gomb módosítása](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. Válasszon ki egy meglévő kártya, vagy adjon hozzá egy új.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
Az alábbi szakaszok a hitelkártya vagy bankkártya kártya adatait megváltoztatásával kapcsolatos gyakori kérdések megválaszolásával.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Az előfizetés le van tiltva. Miért nem távolítható el fognak pénzt levonni most?

Után az előfizetés le van tiltva, vagy meg lett szakítva, hogy Várjon, amíg 90 nappal korábbinak végleges törlése az előfizetésében. Mindent megteszünk a fizetési módot, a fájl a megőrzési időszak alatt szeretne az előfizetés újraaktiválásához. Ezt követően az előfizetés véglegesen törlődik.

Ha el kell távolítania a bankkártyáját a 90 napos megőrzési időszak vége előtt [az előfizetés újraaktiválásához](billing-subscription-become-disable.md). Nem lehet aktiválni, ha [lépjen kapcsolatba az Azure-támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Miért kapok folyton "a bejelentkezési munkamenet lejárt. Ide kattintva jelentkezzen be újra"?

Ha ez a hibaüzenet továbbra is megkapja, még akkor is, ha már bejelentkezett és vissza a, próbálja meg újra a privát böngészési munkamenetet.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Hogyan használhatom egy másik kártyát minden egyes előfizetés van?

Sajnos Ha az előfizetések ugyanahhoz a kártyához már használ, azt, nem külön azokat különböző kártyák használatára. Azonban amikor regisztrál egy új előfizetést, választhat új fizetési mód használatára az adott előfizetéshez tartozó.

### <a name="how-do-i-make-payments"></a>Hogyan ellenőrizhetem, kifizetések?

Ha hitelkártyával beállítása a fizetési módként alkalmazott, automatikusan díját számoljuk fel a kártya minden elszámolási időszak után. Nem kell semmit.

Ha Ön [szerinti fizetésre](billing-how-to-pay-by-invoice.md), a fizetési a helyet, a számla alján felsorolt küldése.

### <a name="how-do-i-change-the-tax-id"></a>Hogyan változtatható az Adóazonosító?

Adja hozzá, vagy frissíteni a Adóazonosító, frissítse a profilját a [Azure Account Center](https://account.azure.com/Profile), majd **rekord adó**. Az adóazonosító az adómentesség kiszámításához szükséges, és megjelenik a számlán.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- Ismerje meg [Azure foglalások](billing-save-compute-costs-reservations.md) megtekintheti, ha azok pénzt takaríthat meg.
