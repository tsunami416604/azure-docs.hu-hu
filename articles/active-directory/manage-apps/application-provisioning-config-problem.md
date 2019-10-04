---
title: Probléma van a felhasználó üzembe helyezésének egy Azure AD Gallery-alkalmazásban való konfigurálásakor | Microsoft Docs
description: Az Azure AD-alkalmazás-katalógusban már felsorolt alkalmazások felhasználó általi üzembe helyezésének konfigurálásakor felmerülő gyakori problémák elhárítása
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a8eaa46b46551f9b6075ec10b38de80f84c22a0
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034155"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Hiba történt a felhasználók Azure AD Gallery-alkalmazásba való konfigurálásának beállításakor

Az [automatikus felhasználó-kiépítés](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) beállítása egy alkalmazáshoz (ahol támogatott) a szükséges, hogy az alkalmazás előkészítése az automatikus kiépítés előtt történjen. Ezután a Azure Portal használatával konfigurálhatja a kiépítési szolgáltatást, hogy szinkronizálja a felhasználói fiókokat az alkalmazással.

Az alkalmazáshoz való kiépítés beállítására vonatkozó beállítási oktatóanyag megkeresésével mindig érdemes kezdeni. Ezután kövesse az alábbi lépéseket az alkalmazás és az Azure AD konfigurálásához a létesítési kapcsolatok létrehozásához. Az alkalmazás-oktatóanyagok listája az [SaaS-alkalmazások Azure Active Directory használatával történő integrálását ismertető oktatóanyagokban](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)található.

## <a name="how-to-see-if-provisioning-is-working"></a>Hogyan lehet megtekinteni, hogy működik-e a kiépítés 

A szolgáltatás konfigurálása után a szolgáltatás működésével kapcsolatos legtöbb betekintést két helyről lehet kirajzolni:

-   **Kiépítési naplók (előzetes verzió)** – a [kiépítési naplók](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) rögzítik a kiépítési szolgáltatás által végrehajtott összes műveletet, beleértve az Azure ad lekérdezését a kiépítés hatókörében lévő hozzárendelt felhasználók számára. A cél alkalmazás lekérdezése a felhasználók létezéséhez, a rendszer közötti felhasználói objektumok összehasonlításával. Ezt követően az összehasonlítás alapján hozzáadhatja, frissítheti vagy letilthatja a felhasználói fiókot a megcélzott rendszeren. A Azure Portal kiépítési naplóit a **tevékenység** szakaszban **Azure Active Directory** &gt; **vállalati alkalmazások** &gt; **kiépítési naplói (előzetes verzió)** lehetőség kiválasztásával érheti el.

-   **Aktuális állapot –** Egy adott alkalmazás utolsó kiépítési futtatásának összefoglalása látható a  **&gt; Azure Active Directory Enterprise apps &gt; \[alkalmazás\] neve &gt;kiépítési** szakaszban, a képernyőn a szolgáltatás beállításai alatt. A jelenlegi állapot szakasz azt mutatja, hogy a létesítési ciklus megkezdte-e a felhasználói fiókok kiépítési folyamatát. Megtekintheti a ciklus előrehaladását, és megtekintheti, hogy hány felhasználó és csoport lett kiépítve, és hogy hány szerepkört hozott létre. Ha bármilyen hiba merül fel, a részletek a [kiépítési naplókban (előzetes verzió)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)találhatók.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Általános problémás területek az üzembe helyezéssel

Az alábbi lista az általános problémás területeket sorolja fel, amelyeket részletesen bemutathat, ha van ötlete, hogy hol kezdjen hozzá.

* [A kiépítési szolgáltatás nem jelenik meg a Start menüben](#provisioning-service-does-not-appear-to-start)
* Nem lehet menteni a konfigurációt, mert az alkalmazás hitelesítő adatai nem működnek
* [A naplók kiosztása azt jelenti, hogy a felhasználók "kimaradnak", és nem lettek kiépítve, még akkor is, ha hozzá vannak rendelve](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>A kiépítési szolgáltatás nem jelenik meg a Start menüben

Ha úgy állítja be **a kiépítési állapotot** , hogy be legyen **kapcsolva** a  **&gt; Azure Active Directory &gt; vállalati alkalmazások\] \[alkalmazás neve &gt;kiépítési** szakaszban, a Azure Portal . A későbbi Újratöltés után azonban nem jelennek meg más állapotadatok az oldalon. Valószínű, hogy a szolgáltatás fut, de még nem hajtotta végre a kezdeti ciklust. A fent ismertetett **kiépítési naplók** alapján állapítsa meg, hogy milyen műveleteket végez a szolgáltatás, és hogy vannak-e hibák.

>[!NOTE]
>Egy kezdeti ciklus akár 20 perctől akár több óráig is eltarthat, az Azure AD-címtár méretétől és a kiépítés hatókörében lévő felhasználók számától függően. A későbbi szinkronizálások a kezdeti ciklus után gyorsabbak lesznek, mivel a kiépítési szolgáltatás a kezdeti ciklust követően mindkét rendszer állapotát jelképező vízjeleket tárolja, így javítja a későbbi szinkronizálások teljesítményét.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Nem lehet menteni a konfigurációt, mert az alkalmazás hitelesítő adatai nem működnek

Ahhoz, hogy a kiépítés működjön, az Azure AD-nek érvényes hitelesítő adatokra van szüksége, amelyek lehetővé teszik az IT számára az alkalmazás által biztosított felhasználói felügyeleti API-hoz való kapcsolódást. Ha ezek a hitelesítő adatok nem működnek, vagy nem tudja, mi a teendő, tekintse át az alkalmazás beállítását ismertető oktatóanyagot a korábban leírtak szerint.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>A naplók kiosztása azt jelenti, hogy a felhasználók kimaradnak, és nem lettek kiépítve annak ellenére, hogy hozzájuk vannak rendelve

Ha egy felhasználó "kihagyva" állapotba kerül a kiépítési naplókban, nagyon fontos, hogy a naplófájlban részletesen olvassa el a kibővített részleteket az ok megállapításához. Az alábbi gyakori okok és megoldások:

- **Be van állítva egy hatókör-szűrő** **Ez az attribútum értéke alapján szűri ki a felhasználót**. A szűrők hatókörével kapcsolatos további információkért lásd <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>:.

- **A felhasználó "nem lesz ténylegesen jogosult".** Ha ezt a hibaüzenetet látja, az azért van, mert probléma van az Azure AD-ben tárolt felhasználói hozzárendelési rekorddal. A probléma megoldásához szüntesse meg a felhasználó (vagy csoport) hozzárendelését az alkalmazásból, majd újból rendelje hozzá újra. További információ a hozzárendelésről: <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

- **Egy kötelező attribútum hiányzik vagy nincs feltöltve a felhasználó számára.** A kiépítés beállításakor megfontolandó szempont, hogy áttekintse és konfigurálja azokat az attribútum-hozzárendeléseket és munkafolyamatokat, amelyek meghatározzák, hogy mely felhasználói (vagy csoport-) tulajdonságokat kell az Azure AD-ből az alkalmazásba áthelyezni. Ebbe beletartozik a "megfeleltetés" tulajdonság beállítása, amely a felhasználók/csoportok egyedi azonosítására és a két rendszer közötti egyeztetésére használható. További információ erről a fontos folyamatról: <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

  * **Csoportok attribútumainak hozzárendelése:** A csoport neve és a csoport adatainak kiépítés a tagokon kívül, ha egyes alkalmazások esetében támogatott. A funkció engedélyezéséhez vagy letiltásához engedélyezze vagy tiltsa le a **kiépítés** lapon megjelenő csoportosítási objektumok **leképezését** . Ha a létesítési csoportok engedélyezve vannak, tekintse át az attribútumok leképezéseit, és győződjön meg arról, hogy a megfelelő mező használatban van a "megfeleltetési azonosító" beállításnál. Ez lehet a megjelenítendő név vagy az e-mail-alias, mivel a csoport és a tagjai nem lesznek kiépítve, ha a megfelelő tulajdonság üres vagy nincs feltöltve az Azure AD-beli csoportokra.

## <a name="next-steps"></a>További lépések
[Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](user-provisioning.md)
