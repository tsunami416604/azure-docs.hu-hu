---
title: Azure-beli hitelkártya módosítása
description: A cikk ismerteti az Azure-előfizetéshez használt hitelkártya módosításának lépéseit.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 99b6d86a65ec05a1299abfffae779113e573310c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834968"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Hitelkártya hozzáadása, frissítése vagy eltávolítása az Azure-ban

Ez a cikk olyan ügyfelekre vonatkozik, akik hitelkártyával regisztráltak az Azure online-ra.

Az Azure Portalon módosíthatja a fizetés alapértelmezett módját új hitelkártyára, frissítheti a meglévő hitelkártya adatait, vagy törölheti a nem használt hitelkártyákat. A módosítások végrehajtásához [fiókadminisztrátornak](billing-subscription-transfer.md#whoisaa) kell lennie.

A Microsoft Azure a hitelkártyás és csekkes/banki átutalásos fizetési módokat támogatja. Ha szeretne jogosulttá válni a csekkes/banki átutalásos fizetési mód használatára, tekintse meg a következőt: [Azure-előfizetések fizetése számla alapján](pay-by-invoice.md).

Ha Microsoft Ügyfélszerződéssel rendelkezik, a fizetési módok a számlázási profilokhoz vannak társítva. Ismerje meg, hogyan [ellenőrizheti a Microsoft Ügyfélszerződéshez való hozzáférést](#check-the-type-of-your-account). Ha rendelkezik Microsoft Ügyfélszerződéssel, ugorjon a [Hitelkártyák kezelése Microsoft Ügyfélszerződés esetében](#manage-credit-cards-for-a-microsoft-customer-agreement) című részhez.

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>Hitelkártyák kezelése Azure-előfizetés esetében

A következő szakaszok azokra az ügyfelekre érvényesek, akik számlázási fiókkal rendelkeznek a Microsoft Online Services Programhoz. Ismerje meg, hogyan [ellenőrizheti számlázási fiókjának típusát](#check-the-type-of-your-account). Ha a számlázási fiók típusa Microsoft Online Services Program, a fizetési módok egyéni Azure-előfizetésekhez vannak társítva. Ha hibaüzenetet kap a hitelkártya hozzáadása után, lásd a [hitelkártya az Azure-regisztrációkor történő elutasítását](../../billing/billing-credit-card-fails-during-azure-sign-up.md) ismertető cikket.

### <a name="change-credit-card-for-a-subscription-by-adding-a-new-credit-card"></a>Módosíthatja az előfizetéshez tartozó hitelkártyát új hitelkártya hozzáadásával.

Az Azure-előfizetéshez tartozó alapértelmezett hitelkártyát az Azure Portalon módosíthatja új, illetve korábban mentett hitelkártyára. A hitelkártya módosításához fiókadminisztrátornak kell lennie. Ha egynél több, ugyanazt az aktív fizetési módot használó előfizetéssel rendelkezik, akkor az aktív fizetési mód az előfizetések bármelyikén való módosításával a többinél is frissíti az aktív fizetési módot.


Az előfizetéshez tartozó alapértelmezett hitelkártya helyett az alábbi lépésekkel állíthat be újat:

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

### <a name="change-credit-card-for-a-subscription-to-a-previously-saved-credit-card"></a>Előfizetéshez tartozó hitelkártya módosítása korábban mentett hitelkártyára

Az előfizetéshez tartozó alapértelmezett hitelkártya helyett az alábbi lépésekkel állíthat be egy, a fiókhoz korábban már rögzített hitelkártyát:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) fiókadminisztrátorként.
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

    ![A keresést megjelenítő képernyőkép](./media/change-credit-card/search.png)

1. Válassza ki azt az előfizetést, amelyhez hozzá szeretné adni a hitelkártyát.
1. Válassza a **Fizetési módok** elemet.

    ![A kiválasztott Fizetési módok kezelése lehetőséget megjelenítő képernyőkép.](./media/change-credit-card/payment-methods-blade-x.png)

1. Jelölje be az aktív fizetési módként beállítani kívánt kártya melletti jelölőnégyzetet.
1. Kattintson a **Beállítás aktívként** parancsra.
    ![A kiválasztott és aktívként beállított hitelkártyát megjelenítő képernyőkép.](./media/change-credit-card/sub-change-active-x.png)

### <a name="edit-credit-card-details"></a>Hitelkártya adatainak szerkesztése

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

### <a name="delete-a-credit-card-from-the-account"></a>Hitelkártya törlése a fiókból

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) fiókadminisztrátorként.
1. Válassza ki a **Költségkezelés + számlázás** lehetőséget a lap bal oldalán.

    ![A keresést megjelenítő képernyőkép](./media/change-credit-card/search.png)

1. A **Számlázás** területen válassza a **Fizetési módok** elemet.

    ![A kiválasztott Fizetési módok kezelése lehetőséget megjelenítő képernyőkép.](./media/change-credit-card/payment-methods-blade-x.png)

1. Jelölje be az eltávolítani kívánt kártya melletti jelölőnégyzetet.
1. Kattintson a **Törlés** gombra.

Ha a hitelkártya a Microsoft-előfizetések bármelyikének aktív fizetési módja, nem távolíthatja el az Azure-fiókjából. Módosítsa az aktív fizetési módot az ehhez a hitelkártyához társított összes előfizetés esetében, majd próbálkozzon újra

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>Hitelkártyák kezelése Microsoft Ügyfélszerződés esetében

A következő szakaszok olyan ügyfelekre vonatkozik, akik Microsoft Ügyfélszerződéssel rendelkeznek, valamint hitelkártyával regisztráltak az Azure-ba online. [Ismerje meg, hogyan ellenőrizheti, hogy Microsoft Ügyfélszerződéssel rendelkezik-e](#check-the-type-of-your-account).

### <a name="change-default-credit-card"></a>Alapértelmezett hitelkártya módosítása

Ha Microsoft Ügyfélszerződéssel, hitelkártyája egy számlázási profilhoz van társítva. A számlázási profilhoz tartozó fizetési mód megváltoztatásához Önnek kellett regisztrálnia az Azure-fiókot, illetve létrehoznia a számlázási fiókot.

Ha számlázási fiókjának alapértelmezett fizetési módját csekkes/banki átutalásos fizetésre kívánja módosítani, tekintse meg a következőt: [Azure-előfizetések fizetése számla alapján](pay-by-invoice.md).

Hitelkártya módosításához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Végezzen keresést a **Költségkezelés + Számlázás** oldalon.
1. A bal oldali menüben kattintson a **Számlázási profilok** elemre.
1. Válasszon ki egy számlázási profilt.
1. A bal oldali menüben válassza a **Fizetési módok** lehetőséget.

   ![Képernyőkép a menü fizetési módok eleméről](./media/change-credit-card/payment-methods-tab-mca.png)

1. Az **Alapértelmezett fizetési mód** szakaszban kattintson a **Módosítás** elemre.

    ![A Módosítás gombot megjelenítő képernyőkép](./media/change-credit-card/change-payment-method-mca.png)

1. A jobb oldalon található új panelen válasszon ki egy már létező kártyát a legördülő listából, vagy kattintson a kék „Új fizetési mód hozzáadás” hivatkozásra egy új hozzáadásához.

### <a name="edit-or-delete-a-credit-card"></a>Hitelkártya szerkesztése vagy törlése

A hitelkártya adatainak szerkesztését (pl. a lejárati dátum frissítését) vagy a kártya a fiókból való törlését az Azure Portalon végezheti el. Csak olyan hitelkártyát törölhet, amely egyetlen Azure-előfizetéshez vagy fizetési profilhoz sincs társítva. Ha a kártya egy letiltott Azure-előfizetéshez van társítva, meg kell várnia az előfizetés törlését (30-90 nappal a lemondás után), mielőtt törölhetné.

Hitelkártya szerkesztéséhez vagy törléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Végezzen keresést a **Költségkezelés + Számlázás** oldalon.
1. A bal oldali menüben kattintson a **Számlázási profilok** elemre.
1. Válasszon ki egy számlázási profilt.
1. A bal oldali menüben válassza a **Fizetési módok** lehetőséget.

   ![Képernyőkép a menü fizetési módok eleméről](./media/change-credit-card/payment-methods-tab-mca.png)

1. A **Saját hitelkártyák** szakaszban keresse meg a szerkeszteni vagy törölni kívánt hitelkártyát.
1. Válassza ki a sor végén található három pontot (`...`).

    ![Képernyőkép a három pontról](./media/change-credit-card/edit-delete-credit-card-mca.png)

1. A hitelkártya adatainak szerkesztéséhez válassza a **Szerkesztés** lehetőséget a helyi menüből.
1. A hitelkártya törléséhez válassza a **Törlés** lehetőséget a helyi menüből.

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

## <a name="check-the-type-of-your-account"></a>A fiók típusának megtekintése

[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- További tudnivalók az [Azure Reservations](../reservations/save-compute-costs-reservations.md) szolgáltatás költségtakarékos megoldásairól.
