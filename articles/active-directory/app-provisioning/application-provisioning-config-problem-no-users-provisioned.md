---
title: Egyetlen felhasználó sincs kiépítve egy Azure AD Gallery-alkalmazásba
description: Gyakori hibák elhárítása, ha nem jelenik meg a felhasználók számára az Azure AD-vel való üzembe helyezéshez konfigurált Azure AD Gallery-alkalmazásban megjelenő felhasználó
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b60261d63e1bcb75aea9d2e8a6b74902520f391
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522917"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Egyetlen felhasználó sincs kiépítve egy Azure AD Gallery-alkalmazásba
Miután az automatikus kiépítés konfigurálva lett egy alkalmazáshoz (beleértve annak ellenőrzését, hogy az Azure AD-hoz az alkalmazáshoz való kapcsolódáshoz megadott hitelesítő adatok érvényesek-e), a felhasználók és/vagy csoportok pedig az alkalmazáshoz vannak kiépítve. Az üzembe helyezést a következő dolgok határozzák meg:

-   Mely felhasználók és csoportok lettek **hozzárendelve** az alkalmazáshoz. Vegye figyelembe, hogy a beágyazott csoportok vagy az Office 365-csoportok kiépítés nem támogatott. További információ a hozzárendelésről: [felhasználó vagy csoport hozzárendelése vállalati alkalmazásokhoz Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md).
-   Az attribútum- **hozzárendelések** engedélyezve vannak-e, és úgy vannak konfigurálva, hogy az Azure ad-ből az alkalmazáshoz való érvényes attribútumokat szinkronizálják. Az attribútumok hozzárendelésével kapcsolatos további információkért lásd: a [felhasználói kiépítési attribútumok leképezésének testreszabása az SaaS-alkalmazásokhoz Azure Active Directoryban](customize-application-attributes.md).
-   Azt határozza meg, hogy van-e olyan **hatóköri szűrő** , amely adott attribútumérték alapján szűri a felhasználókat. A szűrők hatókörével kapcsolatos további információkért lásd: [attribútum-alapú alkalmazás kiépítés hatókör-szűrőkkel](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
  
Ha azt észleli, hogy a felhasználók nincsenek kiépítve, tekintse meg az Azure AD-beli [kiépítési naplókat (előzetes verzió)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) . Egy adott felhasználó bejegyzéseinek keresése.

A Azure Portal kiépítési naplóit a **tevékenység** szakaszban **Azure Active Directory** &gt; **vállalati alkalmazások** &gt; **kiépítési naplók (előzetes verzió)** lehetőség kiválasztásával érheti el. A kiépítési adat a felhasználó neve vagy a forrásrendszer vagy a célként megadott rendszer alapján is megkereshető. Részletekért lásd: [kiépítési naplók (előzetes verzió)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

A kiépítési naplók rögzítik a kiépítési szolgáltatás által végrehajtott összes műveletet, beleértve az Azure AD lekérdezését olyan hozzárendelt felhasználók számára, akik a kiépítés hatókörét képezik, a célalkalmazás lekérdezése a felhasználók létezéséhez, a felhasználói objektumok összehasonlítása a rendszeren. Ezt követően az összehasonlítás alapján hozzáadhatja, frissítheti vagy letilthatja a felhasználói fiókot a megcélzott rendszeren.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Általános problémás területek az üzembe helyezéssel
Az alábbi lista az általános problémás területeket sorolja fel, amelyeket részletesen bemutathat, ha van ötlete, hogy hol kezdjen hozzá.

- [A kiépítési szolgáltatás nem jelenik meg a Start menüben](#provisioning-service-does-not-appear-to-start)
- [A naplók kiosztása azt jelenti, hogy a felhasználók kimaradnak, és nincsenek kiépítve, még akkor is, ha hozzájuk vannak rendelve](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>A kiépítési szolgáltatás nem jelenik meg a Start menüben
Ha úgy állítja be a **kiépítési állapotot** , hogy be legyen **kapcsolva** a **Azure Active Directory &gt; vállalati alkalmazások &gt; \[alkalmazás neve\] &gt;kiépítési** szakasza Azure Portal. A további állapotok azonban nem jelennek meg az oldalon az újratöltést követően, ezért valószínű, hogy a szolgáltatás fut, de még nem hajtotta végre a kezdeti ciklust. Ellenőrizze a fentiekben ismertetett **kiépítési naplókat (előzetes verzió)** , és állapítsa meg, hogy milyen műveleteket végez a szolgáltatás, és hogy vannak-e hibák.

>[!NOTE]
>Egy kezdeti ciklus akár 20 perctől akár több óráig is eltarthat, az Azure AD-címtár méretétől és a kiépítés hatókörében lévő felhasználók számától függően. A következő szinkronizálások gyorsabbak lesznek, mivel a kiépítési szolgáltatás a kezdeti ciklus után mindkét rendszer állapotát jelképező vízjeleket tárolja. A kezdeti ciklus javítja a későbbi szinkronizálások teljesítményét.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>A naplók kiosztása azt jelenti, hogy a felhasználók kimaradnak, és nem lettek kiépítve annak ellenére, hogy hozzájuk vannak rendelve

Ha egy felhasználó "kihagyva" állapotba kerül a kiépítési naplókban, fontos, hogy a napló **lépések** lapján ellenőrizze az okot. Az alábbi gyakori okok és megoldások:

- **Egy hatókör-szűrő konfigurálva** **van, amely egy attribútumérték alapján szűri ki a felhasználót**. A szűrők hatókörével kapcsolatos további információkért lásd: [hatóköri szűrők](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
- **A felhasználó "nem lesz ténylegesen jogosult".** Ha ezt a hibaüzenetet látja, az azért van, mert probléma van az Azure AD-ben tárolt felhasználói hozzárendelési rekorddal. A probléma megoldásához szüntesse meg a felhasználó (vagy csoport) hozzárendelését az alkalmazásból, majd ismét rendelje hozzá újra. További információ a hozzárendelésről: [felhasználók vagy csoportok hozzáférésének hozzárendelése](../manage-apps/assign-user-or-group-access-portal.md).
- **Egy kötelező attribútum hiányzik vagy nincs feltöltve a felhasználó számára.** A kiépítés beállításakor megfontolandó szempont, hogy áttekintse és konfigurálja azokat az attribútum-hozzárendeléseket és munkafolyamatokat, amelyek meghatározzák, hogy mely felhasználói (vagy csoport-) tulajdonságokat kell az Azure AD-ből az alkalmazásba áthelyezni. Ez a konfiguráció magában foglalja a "megfelelő tulajdonság" beállítását, amely a felhasználók és csoportok egyedi azonosítására és a két rendszer közötti egyeztetésére szolgál. A fontos folyamattal kapcsolatos további információkért lásd: a [felhasználói kiépítési attribútumok leképezésének testreszabása az SaaS-alkalmazásokhoz Azure Active Directoryban](customize-application-attributes.md).
- **Csoportok attribútumainak hozzárendelése:** A csoport neve és a csoport adatainak kiépítés a tagokon kívül, ha egyes alkalmazások esetében támogatott. A funkció engedélyezéséhez vagy letiltásához engedélyezze vagy tiltsa le a **kiépítés** lapon megjelenő csoportosítási objektumok **leképezését** . Ha a létesítési csoportok engedélyezve vannak, tekintse át az attribútumok leképezéseit, és győződjön meg arról, hogy a megfelelő mező használatban van a "megfeleltetési azonosító" beállításnál. A megfelelő azonosító lehet a megjelenítendő név vagy az e-mail-alias. A csoport és a tagjai nincsenek kiépítve, ha a megfelelő tulajdonság üres vagy nincs feltöltve egy csoportra az Azure AD-ben.

## <a name="next-steps"></a>Következő lépések

[Azure AD Connect szinkronizálás: a deklaratív kiépítés ismertetése](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
