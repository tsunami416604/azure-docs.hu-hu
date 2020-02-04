---
title: Azure-beli hitelkártya módosítása
description: A cikk ismerteti az Azure-előfizetéshez használt hitelkártya módosításának lépéseit.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: 6ae55e0075883de08af516b71089f00353975b34
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "75992841"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Hitelkártya hozzáadása, frissítése vagy eltávolítása az Azure-ban

Ez a cikk olyan ügyfelekre vonatkozik, akik hitelkártyával regisztráltak az Azure online-ra.

Az Azure Portalon új hitelkártyát is felvehet, frissítheti a meglévő hitelkártyát, vagy törölheti a nem használt hitelkártyát. A módosítások végrehajtásához [fiókadminisztrátornak](billing-subscription-transfer.md#whoisaa) kell lennie.

Ha rendelkezik [Microsoft-ügyfélszerződéssel](#check-access-to-a-microsoft-customer-agreement), a fizetési módok a számlázási profilokhoz vannak társítva. Útmutató a [számlázási profilhoz tartozó alapértelmezett fizetési mód megváltoztatásához](#change-payment-method-for-a-billing-profile). Csak az Azure-ra regisztrált felhasználó tudja frissíteni a fizetési módot.

**Szeretne számlás fizetésre váltani? (csekk/átutalás)** Lásd: [Azure-előfizetések fizetése számla alapján](pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Új hitelkártya hozzáadása az Azure-előfizetéshez

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) fiókadminisztrátorként.
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

    ![A keresést megjelenítő képernyőkép](./media/change-credit-card/search.png)

1. Válassza ki azt az előfizetést, amelyhez hozzá szeretné adni a hitelkártyát.
1. Válassza a **Fizetési módok** elemet.

    ![A kiválasztott Fizetési módok kezelése lehetőséget megjelenítő képernyőkép.](./media/change-credit-card/payment-methods-blade-x.png)

1. Kártya hozzáadásához válassza a „+” gombot a bal felső sarokban. A jobb oldalon megjelenik egy hitelkártyaűrlap.
1. Adja meg a hitelkártya adatait.

    ![Új kártya hozzáadását megjelenítő képernyőkép.](./media/change-credit-card/sub-add-new-x.png)

1. Ha ezt a kártyát szeretné aktív fizetési módként beállítani, jelölje be az **Ez legyen az aktív fizetési mód** beállítás melletti jelölőnégyzetet az űrlap felett. Ez a kártya lesz innentől az aktív fizetési eszköz az összes olyan előfizetés esetében, amely a kiválasztott előfizetéssel azonos kártyát használ.

1. Kattintson a **Tovább** gombra.

Ha hibaüzenetet kap a hitelkártya hozzáadása után, lásd a [hitelkártya az Azure-regisztrációkor történő elutasítását](../../billing/billing-credit-card-fails-during-azure-sign-up.md) ismertető cikket.

## <a name="update-existing-credit-card"></a>Meglévő hitelkártya frissítése

Ha a hitelkártya megújul, és a szám változatlan marad, frissítheti a meglévő hitelkártya-adatokat, például a lejárati dátumot. Ha a hitelkártyaszám megváltozik a kártya elvesztése, ellopása vagy lejárata miatt, kövesse a [hitelkártya fizetési módként történő hozzáadását](#addcard) ismertető szakasz lépéseit. A CVV-t nem szükséges frissíteni.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) fiókadminisztrátorként.
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

    ![A keresést megjelenítő képernyőkép](./media/change-credit-card/search.png)

1. Válassza a **Fizetési módok** elemet.

    ![A kiválasztott Fizetési módok kezelése lehetőséget megjelenítő képernyőkép.](./media/change-credit-card/payment-methods-blade-x.png)

1. Kattintson a szerkeszteni kívánt hitelkártyára. A jobb oldalon megjelenik egy hitelkártyaűrlap.

    ![A kiválasztott hitelkártyát megjelenítő képernyőkép.](./media/change-credit-card/edit-card-x.png)

1. Frissítse a hitelkártya adatait.
1. Kattintson a **Mentés** gombra.

## <a name="use-a-different-credit-card"></a>Másik hitelkártya használata

Ha egynél több, ugyanazt az aktív fizetési módot használó előfizetéssel rendelkezik, akkor az aktív fizetési mód az előfizetések bármelyikén való módosításával a többinél is frissíti az aktív fizetési módot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) fiókadminisztrátorként.
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

    ![A keresést megjelenítő képernyőkép](./media/change-credit-card/search.png)

1. Válassza ki azt az előfizetést, amelyhez hozzá szeretné adni a hitelkártyát.
1. Válassza a **Fizetési módok** elemet.

    ![A kiválasztott Fizetési módok kezelése lehetőséget megjelenítő képernyőkép.](./media/change-credit-card/payment-methods-blade-x.png)

1. Jelölje be az aktív fizetési módként beállítani kívánt kártya melletti jelölőnégyzetet.
1. Kattintson a **Beállítás aktívként** parancsra.
    ![A kiválasztott és aktívként beállított hitelkártyát megjelenítő képernyőkép.](./media/change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>Hitelkártya eltávolítása a fiókból

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) fiókadminisztrátorként.
1. Válassza ki a **Költségkezelés + számlázás** lehetőséget a lap bal oldalán.

    ![A keresést megjelenítő képernyőkép](./media/change-credit-card/search.png)

1. A **Számlázás** területen válassza a **Fizetési módok** elemet.

    ![A kiválasztott Fizetési módok kezelése lehetőséget megjelenítő képernyőkép.](./media/change-credit-card/payment-methods-blade-x.png)

1. Jelölje be az eltávolítani kívánt kártya melletti jelölőnégyzetet.
1. Kattintson a **Törlés** gombra.

Ha a hitelkártya a Microsoft-előfizetések bármelyikének aktív fizetési módja, nem távolíthatja el az Azure-fiókjából. Módosítsa az aktív fizetési módot az ehhez a hitelkártyához társított összes előfizetés esetében, majd próbálkozzon újra

## <a name="change-payment-method-for-a-billing-profile"></a>Számlázási profilhoz tartozó fizetési mód megváltoztatása

A számlázási profilhoz tartozó fizetési mód megváltoztatásához Önnek kellett regisztrálnia az Azure-fiókot.

Az alapértelmezett fizetési mód csekkre/átutalásra váltásához lásd [a számlázási profil csekkre/átutalásra váltását](pay-by-invoice.md) ismertető szakaszt.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Végezzen keresést a **Költségkezelés + Számlázás** oldalon.
1. A bal oldali menüben kattintson a **Számlázási profilok** elemre.

    ![Képernyőkép a menü számlázási profil eleméről](./media/change-credit-card/billing-profile.png)

1. Válasszon ki egy számlázási profilt.
1. A bal oldali menüben válassza a **Fizetési módok** lehetőséget.

   ![Képernyőkép a menü fizetési módok eleméről](./media/change-credit-card/billing-profile-payment-methods.png)

1. Az alapértelmezett fizetési mód felett kattintson a **Módosítás** gombra.

    ![A Módosítás gombot megjelenítő képernyőkép](./media/change-credit-card/customer-led-switch-credit-card.png)

1. Válasszon ki egy meglévő kártyát, vagy vegyen fel egy újat.

## <a name="troubleshooting"></a>Hibaelhárítás
Nem támogatjuk a virtuális kártyákat és a feltöltőkártyákat. Ha egy érvényes hitelkártya hozzáadása vagy frissítése során hibák lépnek fel, próbálja meg megnyitni a böngészőt inkognitó módban.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
A következő szakaszokból a hitelkártya-adatok módosítására vonatkozó gyakori kérdésekre kaphat választ.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Az előfizetésem le van tiltva. Miért nem tudom eltávolítani a hitelkártyámat?

Az előfizetés letiltását vagy törlését egy 90 napos türelmi idő követi, és ez idő alatt nem töröljük véglegesen az előfizetését. Erre az időre tovább tároljuk a fizetési mód adatait arra az esetre, ha az előfizetést újra szeretné aktiválni. Ezután az előfizetés véglegesen törlődik.

Ha a hitelkártyáját a 90 napos megőrzési időszak vége előtt el kell távolítania, [aktiválja újra az előfizetést](subscription-disabled.md). Ha nem tudja újraaktiválni, [forduljon az Azure ügyfélszolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Miért kapom a „Bejelentkezési munkamenet lejárt. Kattintson ide az ismétel bejelentkezéshez.” üzenetet?

Ha továbbra is ezt a hibaüzenetet kapja, még akkor is, ha már kijelentkezett, és újra bejelentkezett, próbálkozzon újra egy privát böngészési munkamenettel.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Hogyan használhatok különböző kártyákat az egyes előfizetéseimhez?

Ha az előfizetései már ugyanazt a kártyát használják, nem lehet szétválasztani őket, hogy különböző kártyákat használjanak. Amikor azonban új előfizetést regisztrál, kiválaszthat egy új fizetési módot ahhoz az előfizetéshez.

### <a name="how-do-i-make-payments"></a>Hogyan tudok fizetni?

Ha hitelkártyát állít be fizetési módként, akkor az egyes számlázási időszakok után automatikusan megterheljük a kártyát. Semmit sem kell tennie.

[Számlás fizetés](pay-by-invoice.md) esetén küldje az utalást a számla alján megadott helyre.

### <a name="how-do-i-change-the-tax-id"></a>Hogyan módosíthatom az adóazonosítót?

Adóazonosító hozzáadásához vagy frissítéséhez módosítsa a profilját az [Azure Fiókközpontban](https://account.azure.com/Profile), majd válassza az **Adórekord** lehetőséget. Az adóazonosító az adómentesség kiszámításához szükséges, és megjelenik a számlán.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- További tudnivalók az [Azure Reservations](../reservations/save-compute-costs-reservations.md) szolgáltatás költségtakarékos megoldásairól.
