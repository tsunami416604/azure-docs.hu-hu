---
title: A megadott bankkártyára módosítása az Azure-hoz |} A Microsoft Docs
description: Ismerteti, hogyan lehet hogyan lehet Azure-előfizetés díjfizetéséhez használt bankkártya módosítása
services: ''
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/13/2019
ms.author: banders
ms.openlocfilehash: b910cb3061b1451ea80b9843e2aa4047a784548f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57876867"
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Hozzáadása, frissítése vagy hitelkártya vagy bankkártya eltávolítása az Azure-hoz

Az Azure Portalon adjon hozzá egy új hitelkártya, frissítse a meglévő bankkártyák, vagy törölje a nem használt bankkártya. Kell egy [felhasználóifiók-adminisztrátor](billing-subscription-transfer.md#whoisaa) módosítások végrehajtásához.

<!-- If your Angola, Belize, Algeria, Vietnam, or Virgin Islands. -->

**Váltson át a számla ellenében szeretné?** Lásd: [Azure előfizetések fizetése számla](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card-to-an-azure-subscription"></a>Új hitelkártya vagy bankkártya kártya hozzáadása egy Azure-előfizetéshez

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) fiók rendszergazdaként.
1. A Keresés **Cost Management és számlázás**.

    ![Keresés bemutató képernyőkép](./media/billing-how-to-change-credit-card/search.png)

1. Válassza ki az előfizetést, amelyet szeretne a hitelkártya vagy bankkártya kártya hozzáadása.
1. Válassza ki **fizetési módok**.

    ![Képernyőkép a kezelés fizetési módszereket lehetőség van kijelölve.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. A bal felső sarokban, válassza a "+", adjon hozzá egy kártyát. Hitelkártya űrlap a jobb oldalon jelenik meg.
1. Adja meg a hitel- vagy bankkártya adatait.

    ![Képernyőkép egy új kártya hozzáadása.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Legyen ez a kártya az aktív fizetési módot, jelölje be a jelölőnégyzetet a **legyen ez az aktív fizetési mód** az űrlap felett. Ez a kártya használata a kártyának a kiválasztott előfizetés összes előfizetéshez tartozó active fizetési válnak.

1. Kattintson a **Tovább** gombra.

Miután hozzáadta a hitelkártya hibaüzenetet kap, ha [Azure-előfizetés elutasított bankkártya](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Meglévő jóváírás vagy bankkártyával frissítése

Ha hitelkártyáját megújítani lekérdezi és változatlan marad, a szám, frissítse a meglévő hitelkártya adatait, például a lejárati dátum. Ha a hitelkártya száma módosításokat, mert a kártya elveszik, ellopják, vagy lejárt, kövesse a a [hitelkártya hozzáadásához a fizetési módként alkalmazott](#addcard) szakaszban. Nem kell frissíteni a CVV.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) fiók rendszergazdaként.
1. A Keresés **Cost Management és számlázás**.

    ![Keresés bemutató képernyőkép](./media/billing-how-to-change-credit-card/search.png)

1. Válassza ki **fizetési módok**.

    ![Képernyőkép a kezelés fizetési módszereket lehetőség van kijelölve.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Kattintson a kreditet, vagy a szerkeszteni kívánt kártya tartozik. Hitelkártya űrlap a jobb oldalon jelenik meg.

    ![A kiválasztott bankkártyával, illetve kártya megjelenítő képernyőkép.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Frissítse a hitelkártya vagy bankkártya vonatkozó információkat.
1. Kattintson a **Mentés** gombra.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Más hitelkártya használata az Azure-előfizetés

Ha egynél több előfizetés nincs aktív fizetési módját, majd módosítja az aktív fizetési mód bármelyik ezeket az előfizetéseket is frissíti a többinél pedig az aktív fizetési módot.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) fiók rendszergazdaként.
1. A Keresés **Cost Management és számlázás**.

    ![Keresés bemutató képernyőkép](./media/billing-how-to-change-credit-card/search.png)

1. Válassza ki az előfizetést, amelyet szeretne a hitelkártya vagy bankkártya kártya hozzáadása.
1. Válassza ki **fizetési módok**.

    ![Képernyőkép a kezelés fizetési módszereket lehetőség van kijelölve.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. A kártya, győződjön meg arról, az aktív fizetési mód szeretné melletti jelölőnégyzetet.
1. Kattintson a **beállítás aktívként**.
    ![Képernyőkép a hitelkártya vagy bankkártya kiválasztva, és állítsa aktív.](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Hitelkártya vagy bankkártya eltávolítása a fiókból

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) fiók rendszergazdaként.
1. Válassza ki **Költségkezelés + számlázás** az oldal bal oldalán.

    ![Keresés bemutató képernyőkép](./media/billing-how-to-change-credit-card/search.png)

1. A **számlázási**válassza **fizetési módok**.

    ![Képernyőkép a kezelés fizetési módszereket lehetőség van kijelölve.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Az eltávolítani kívánt kártya melletti jelölőnégyzetet.
1. Kattintson a **Törlés** gombra.

Ha a megadott bankkártyára az aktív fizetési módot, a Microsoft-előfizetések egyike, nem távolítható el az Azure-fiókjával. E bankkártya vagy egy hitelkártya kapcsolódó összes előfizetéshez tartozó a aktív fizetési módot, és próbálkozzon újra
<!-- # Add, update, or remove a credit or debit card for Azure

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
## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Az előfizetés le van tiltva. Miért nem távolítható el fognak pénzt levonni most?

Után az előfizetés le van tiltva, vagy meg lett szakítva, hogy Várjon, amíg 90 nappal korábbinak végleges törlése az előfizetésében. Mindent megteszünk a fizetési módot, a fájl a megőrzési időszak alatt szeretne az előfizetés újraaktiválásához. Ezt követően az előfizetés véglegesen törlődik.

Ha el kell távolítania a bankkártya vagy egy hitelkártya, mielőtt a 90 napos megőrzési időszak végén, [az előfizetés újraaktiválásához](billing-subscription-become-disable.md). Nem lehet aktiválni, ha [lépjen kapcsolatba az Azure-támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Miért kapok folyton "a bejelentkezési munkamenet lejárt. Ide kattintva jelentkezzen be újra"?

Ha ez a hibaüzenet továbbra is megkapja, még akkor is, ha már bejelentkezett és vissza a, próbálja meg újra a privát böngészési munkamenetet.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Hogyan használhatom egy másik kártyát minden egyes előfizetés van?

Sajnos Ha az előfizetések ugyanahhoz a kártyához már használ, azt, nem külön azokat különböző kártyák használatára. Azonban amikor regisztrál egy új előfizetést, választhat új fizetési mód használatára az adott előfizetéshez tartozó.

### <a name="how-do-i-make-payments"></a>Hogyan ellenőrizhetem, kifizetések?

Ha beállította hitelkártyával vagy bankkártyával kártya a fizetési módként alkalmazott, automatikusan díját számoljuk fel a kártya minden elszámolási időszak után. Nem kell semmit.

Ha Ön [szerinti fizetésre](billing-how-to-pay-by-invoice.md), a fizetési a helyet, a számla alján felsorolt küldése.

### <a name="how-do-i-make-a-one-time-payment"></a>Hogyan ellenőrizhetem, egyszeri fizetéssel?

Sajnos az Azure jelenleg nem támogatja az egyszeri kifizetések bankkártyával, illetve a kártyák. 

### <a name="how-do-i-change-the-tax-id"></a>Hogyan változtatható az Adóazonosító?

Hozzáadásához, vagy frissítési adó azonosítója, látogasson el a [ **profil** Azure Account Center webhelyen](https://account.azure.com/Profile), majd **rekord adó**. Az adóazonosító az adómentesség kiszámításához szükséges, és megjelenik a számlán.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
