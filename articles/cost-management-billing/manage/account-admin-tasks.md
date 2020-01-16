---
title: Fiókadminisztrátori feladatok az Azure Portalon
description: Ismerteti, hogyan hajthatók végre fizetési műveletek az Azure Portalon
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: banders
ms.openlocfilehash: 2fadd0b6eb0951031a9d577b2fd3a3d497076dd8
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75990111"
---
# <a name="account-administrator-tasks-in-the-azure-portal"></a>Fiókadminisztrátori feladatok az Azure Portalon

Ez a cikk azt ismerteti, hogyan hajthatók végre a következő feladatok az Azure Portalon:
- Az előfizetés fizetési módjainak kezelése
- Az előfizetés költségkeretének eltávolítása
- Kreditek hozzáadása az Azure in Open-előfizetéshez

Ezen feladatok elvégzéséhez fiókadminisztrátornak kell lennie. 

## <a name="navigate-to-your-subscriptions-payment-methods"></a>Navigáljon az előfizetés fizetési módjaihoz

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) fiókadminisztrátorként.
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.
    
    ![Képernyőkép a keresőmezőről a költségkezelés + számlázás keresőkifejezéssel ](./media/account-admin-tasks/search-bar.png)

1. A **Saját előfizetések** listából válassza ki azt az előfizetést, amelyhez hozzá szeretné adni a hitelkártyát.
    ![Képernyőkép a saját előfizetések rácsának áttekintésével](./media/account-admin-tasks/cost-management-billing-overview-x.png)

1. Válassza a **Fizetési módok** elemet.

    ![Képernyőkép kiválasztott fizetési módok panellel.](./media/account-admin-tasks/subscription-payment-methods-blade.png)

Itt új hitelkártyát adhat hozzá, módosíthatja az aktív fizetési módszert, szerkesztheti a hitelkártya adatait, és hitelkártyákat törölhet. 

### <a name="change-active-payment-method"></a>Aktív fizetési mód módosítása

Az aktív fizetési mód módosításához hozzáadhat egy új hitelkártyát, vagy kiválaszthat egy már mentettet. Az aktív fizetési mód módosítása új hitelkártyára:

1. Hitelkártya hozzáadásához válassza a „+” gombot a bal felső sarokban.
    
    ![Képernyőkép a pluszjelről](./media/account-admin-tasks/subscription-payment-methods-plus.png)

1. Adja meg a hitelkártya adatait a jobb oldali űrlapon.

    ![A hitelkártya hozzáadása űrlapot megjelenítő képernyőkép.](./media/account-admin-tasks/subscription-add-payment-method-x.png)

1. Ha ezt a kártyát szeretné aktív fizetési módként beállítani, jelölje be az **Ez legyen az aktív fizetési mód** beállítás melletti jelölőnégyzetet az űrlap felett. Ez a kártya lesz innentől az aktív fizetési eszköz az összes olyan előfizetés esetében, amely a kiválasztott előfizetéssel azonos kártyát használ.

    ![Képernyőkép a kártyát aktív fizetési móddá tevő jelölőnégyzettel.](./media/account-admin-tasks/subscription-make-active-payment-method-x.png)

1. Kattintson a **Tovább** gombra.

Az aktív fizetési mód módosítása már mentett hitelkártyára:

1. Jelölje be az aktív fizetési módként beállítani kívánt kártya melletti jelölőnégyzetet.

    ![Képernyőkép a hitelkártya mellett bejelölt jelölőnégyzettel](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Kattintson a parancssáv **Beállítás aktívként** elemére.

    ![Képernyőkép a Beállítás aktívként gombról](./media/account-admin-tasks/subscription-checked-payment-method-set-active.png)

### <a name="edit-credit-card-details"></a>Hitelkártya adatainak szerkesztése

A hitelkártya adatainak, például a lejárati dátum vagy a cím szerkesztéséhez kattintson a szerkeszteni kívánt hitelkártyára. A jobb oldalon megjelenik egy hitelkártyaűrlap.

![A kiválasztott hitelkártyát megjelenítő képernyőkép](./media/account-admin-tasks/subscription-edit-payment-method-x.png)

Frissítse a hitelkártya adatait, és kattintson **Mentés** gombra.

### <a name="remove-a-credit-card-from-the-account"></a>Hitelkártya eltávolítása a fiókból

1. Jelölje be a törölni kívánt kártya melletti jelölőnégyzetet.

    ![Képernyőkép a hitelkártya mellett bejelölt jelölőnégyzettel](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. A parancssávon kattintson a **Törlés** gombra.

    ![Képernyőkép a törlés gombról](./media/account-admin-tasks/subscription-checked-payment-method-delete.png)

Ha a hitelkártya a Microsoft-előfizetések bármelyikének aktív fizetési módja, nem távolíthatja el az Azure-fiókjából. Módosítsa az aktív fizetési módot az ehhez a hitelkártyához társított összes előfizetés esetében, majd próbálkozzon újra.

### <a name="switch-to-invoice-payment"></a>Váltás számlás fizetésre

Ha jogosult számlás fizetésre (csekk/banki átutalás), akkor az Azure Portalon válthatja át az előfizetését számlás fizetésre (csekk/banki átutalás).

1. Válassza a **Fizetés számla alapján** lehetőséget a parancssorban.

    ![Képernyőkép kiválasztott fizetési módok panellel.](./media/account-admin-tasks/subscription-payment-methods-pay-by-invoice.png)

1. Adja meg a számlás fizetési mód címét.
1. Kattintson a **Tovább** gombra.

Ha szeretne jogosulttá válni a számlás fizetésre, tekintse meg [a számla alapján történő fizetést ismertető témakört](pay-by-invoice.md).

### <a name="edit-invoice-payment-address"></a>Számla fizetési címének szerkesztése

Ha szerkeszteni szeretné a számlás fizetési mód címét, kattintson az előfizetés fizetési módjainak listájában lévő **Számla** lehetőségre. A jobb oldalon megnyílik a cím űrlap. 

## <a name="remove-spending-limit"></a>Költségkeret eltávolítása

Az Azure-költségkerettel elkerülheti a kreditek mennyiségét meghaladó kiadásokat. A költségkeretet bármikor eltávolíthatja mindaddig, amíg az Azure-előfizetéséhez érvényes fizetési mód van társítva. A több hónapnyi kreditet tartalmazó előfizetési típusok, például a Visual Studio Enterprise és Visual Studio Professional esetében a következő számlázási időszak elején újra engedélyezheti a költségkeretet.

A költségkeret nem érhető el hűségszerződéses csomagokat és használatalapú fizetéses csomagokat tartalmazó előfizetésekhez. Tekintse meg [az Azure-előfizetések típusait és a költségkeret elérhetőségét](https://azure.microsoft.com/support/legal/offer-details/) ismertető cikket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) fiókadminisztrátorként.
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

    ![Képernyőkép a keresőmezőről a költségkezelés + számlázás keresőkifejezéssel ](./media/account-admin-tasks/search-bar.png)

1. A **Saját előfizetések** listában válassza ki a Visual Studio Enterprise-előfizetését.
    
    ![Képernyőkép a saját előfizetések rácsának áttekintésével](./media/account-admin-tasks/cost-management-overview-msdn-x.png)

1. Az előfizetés áttekintésében kattintson a narancssárga szalagcímre a költségkeret eltávolításához.
    
    ![Képernyőkép a költségkeret eltávolítása szalagcímről](./media/account-admin-tasks/msdn-remove-spending-limit-banner-x.png)

1. Válassza ki, hogy határozatlan időre vagy csak az aktuális számlázási időszakra szeretné-e eltávolítani a költségkeretet.

   ![Képernyőkép a költségkeret eltávolítása panelről](./media/account-admin-tasks/remove-spending-limit-blade-x.png)

1. Kattintson a **Fizetési mód kiválasztása** lehetőségre az előfizetés fizetési módjának kiválasztásához. Ez lesz az előfizetése aktív fizetési módja.

1. Kattintson a **Befejezés** gombra.

## <a name="add-credits-to-azure-in-open-subscription"></a>Kreditek hozzáadása az Azure in Open-előfizetéshez

Ha Azure in Open licencelési előfizetéssel rendelkezik, az Azure Portalon krediteket adhat hozzá az előfizetéshez egy termékkulcs beváltásával vagy a kreditek hitelkártyával való megvásárlásával.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) fiókadminisztrátorként.
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

    ![Képernyőkép a keresőmezőről a költségkezelés + számlázás keresőkifejezéssel ](./media/account-admin-tasks/search-bar.png)

1. A **Saját előfizetések** listában válassza ki Azure in Open-előfizetését.
   
    ![Képernyőkép a saját előfizetések rácsának áttekintésével](./media/account-admin-tasks/cost-management-overview-aio-x.png)

1. Válassza a **Kreditelőzmények** lehetőséget.
    
    ![A kreditelőzményeket bemutató képernyőkép](./media/account-admin-tasks/aio-credit-history-blade.png)

1. További kreditek hozzáadásához válassza a „+” gombot a bal felső sarokban.

    ![A kreditek hozzáadása gombot megjelenítő képernyőkép](./media/account-admin-tasks/aio-credit-history-plus.png)

1. Válassza ki a fizetési mód típusát a legördülő menüből. Hozzáadhat egy termékkulcsot, vagy hitelkártyával krediteket vásárolhat.
    
    ![Képernyőkép a kreditek hozzáadása panel fizetési mód legördülő menüjéről](./media/account-admin-tasks/add-credits-select-payment-method.png)

1. Ha a termékkulcsot választotta:
    - Adja meg a termékkulcsot
    - Kattintson az **Érvényesítés** lehetőségre

1. Ha a hitelkártyát választotta:
    - Kattintson a **Fizetési mód kiválasztása** lehetőségre egy hitelkártya hozzáadásához vagy egy meglévő kiválasztásához.
    - Adja meg a hozzáadni kívánt kreditek mennyiségét.

1. Kattintson az **Alkalmaz** gombra

## <a name="troubleshooting"></a>Hibaelhárítás
Nem támogatjuk a virtuális kártyákat és a feltöltőkártyákat. Ha egy érvényes hitelkártya hozzáadása vagy frissítése során hibák lépnek fel, próbálja meg megnyitni a böngészőt inkognitó módban.

## <a name="next-steps"></a>Következő lépések
- További információ a [váratlan költségek elemzéséről és megelőzéséről az Azure Portalon](getting-started.md)
