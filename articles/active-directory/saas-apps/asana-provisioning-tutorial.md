---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása az Asanában |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Asana felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: f35de1865f2e02331b8455bbdce92e8fe2f9a1b6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210475"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Asana konfigurálása

Ez az oktatóanyag célja, a lépéseket kell elvégeznie a Asana és az Azure Active Directory (Azure AD) való automatikus kiépítésének és megszüntetésének felhasználói fiókok Azure AD-ből Asana mutatni.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy Ön már rendelkezik a következőkkel:

*   Az Azure AD-bérlő
*   Az egy Asana-bérlőt egy [vállalati](https://www.asana.com/pricing) a terv és jobban engedélyezve 
*   Egy felhasználói fiók rendszergazdai jogosultságokkal rendelkezik az Asanában 

> [!NOTE] 
> Azure AD létesítési integrációs támaszkodik a [Asana API](https://asana.com/developers/api-reference/users), amely Asana rendelkezésére áll.

## <a name="assign-users-to-asana"></a>Felhasználók hozzárendelése az Asanában

Azure ad-ben más néven "hozzárendeléseket" fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus fióklétesítés kontextusában csak a az Azure AD-alkalmazáshoz hozzárendelt felhasználók szinkronizálása. 

Beállítása, és a kiépítési szolgáltatás engedélyezése előtt el kell döntenie, hogy mely felhasználók Azure AD-ben az Asanában alkalmazás hozzáférésre van szükségük. Ezután ezek a felhasználók utasításokat követve hozzárendelheti az Asanában alkalmazás:

[Felhasználó hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Felhasználók hozzárendelése Asana fontos tippek

Azt javasoljuk, hogy hozzárendelje egyetlen Azure AD-felhasználót az Asanában, az üzembe helyezési konfiguráció tesztelése. További felhasználók később is hozzárendelhető.

## <a name="configure-user-provisioning-to-asana"></a>Konfigurálnom a felhasználókiépítést az Asanában 

Ez a szakasz végigvezeti az Azure AD-csatlakozás üzembe helyezési API Asana-felhasználói fiókot. Is konfigurálhatja az eszközkiépítési szolgáltatás létrehozása, frissítése és tiltsa le a hozzárendelt felhasználói fiókok az Asanában, az Azure AD felhasználói hozzárendelések alapján.

> [!TIP]
> SAML-alapú egyszeri bejelentkezés az Asanában engedélyezéséhez kövesse az utasításokat a [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható az Automatikus kiépítés függetlenül azonban ezen két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Automatikus felhasználói fiók kiépítése Asanában, az Azure AD konfigurálása

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a **Azure Active Directory** > **vállalati alkalmazások** > **mindenalkalmazás** szakaszban.

1. Ha már konfigurálta az egyszeri bejelentkezés Asana, keresse meg az Asana-példány a keresőmező használatával. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **Asana** az alkalmazás-katalógusában. Válassza ki **Asana** a keresési eredmények között, és adja hozzá az alkalmazások listáját.

1. Asana-példányát, majd válassza ki és a **kiépítési** fülre.

1. Állítsa be **Kiépítési mód** való **automatikus**.

    ![Asana-kiépítés](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. Alatt a **rendszergazdai hitelesítő adataival** területén kövesse ezeket az utasításokat a jogkivonatot, és írja be a **titkos jogkivonat**:

    a. Jelentkezzen be a [Asana](https://app.asana.com) a rendszergazdai fiók használatával.

    b. Válassza ki profilképét a felső sávon, és válassza ki a jelenlegi cégnév beállításokat.

    c. Nyissa meg a **szolgáltatásfiókok** fülre.

    d. Válassza ki **szolgáltatásfiók hozzáadása**.

    e. Frissítés **neve** és **kapcsolatos** és a profilképet, igény szerint. Másolja a tokent az **Token**, és válassza ki azt a **módosítások mentése**.

1. Az Azure Portalon válassza ki a **kapcsolat tesztelése** annak érdekében, hogy az Azure AD az Asanában alkalmazáshoz kapcsolódik. Ha a kapcsolat hibája esetén, győződjön meg arról, hogy az Asanában rendszergazdai engedélyekkel rendelkezzen, majd próbálkozzon a **kapcsolat tesztelése** lépés újra.

1. Adja meg az e-mail-címét egy személy vagy csoport, amely az üzembe helyezési hiba értesítéseket kapni szeretné **értesítő e-mailt**. Jelölje be a jelölőnégyzetet alá.

1. Kattintson a **Mentés** gombra. 

1. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók az Asanában**.

1. Az a **attribútumleképezések** területen tekintse át a felhasználói attribútumok Asana szinkronizálását az Azure ad-ből. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókokat az Asanában a frissítési műveleteket. Válassza ki **mentése** véglegesíti a módosításokat. További információkért lásd: [felhasználó kiépítése attribútumleképezések testreszabása](../manage-apps/customize-application-attributes.md).

1. Ahhoz, hogy az Azure AD létesítési szolgáltatás az Asanában, az a **beállítások** területén módosíthatja **üzembe helyezési állapotra** való **a**.

1. Kattintson a **Mentés** gombra. 

Most a kezdeti szinkronizálás indítása az Asanában rendelt bármely felhasználó számára a **felhasználók** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut hosszabb időt vesz igénybe. Használja a **szinkronizálás részleteivel** előrehaladásának figyeléséhez, és kövesse a hivatkozásokat tevékenységeket tartalmazó naplók kiépítés szakaszt. A naplók a Asana-alkalmazásban a kiépítési szolgáltatás által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [automatikus felhasználói fiók üzembe helyezésének jelentést](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](asana-tutorial.md)
