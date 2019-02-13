---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory a Salesforce beállítása |} A Microsoft Docs'
description: Ismerje meg, hogyan egyszeri bejelentkezés konfigurálása Azure Active Directory és a Salesforce között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 712cc5ce62225987f8cc3ea13b5e4fd10a7d5eaf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172716"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása a Salesforce beállítása

Ez az oktatóanyag célja, a Salesforce és az Azure AD automatikus kiépítésére végrehajtásához szükséges lépéseket és megszüntetni hozzárendeléseket felhasználói fiókok Azure AD-ből a Salesforce-hoz.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy Ön már rendelkezik a következőkkel:

* Az Azure Active directory-bérlő
* A Salesforce.com-bérlőben

> [!IMPORTANT]
> Ha a Salesforce.com-próbafiókot használ, majd fogjuk automatizált felhasználókiépítés konfigurálása nem sikerült. Próbaverziós fiók nem rendelkezik a szükséges API-hozzáférés engedélyezve van, amíg azok vásárolhatók. Kérheti a korlátozás az ingyenes [fejlesztői fiók](https://developer.salesforce.com/signup) az oktatóanyag elvégzéséhez.

Ha egy Salesforce próbakörnyezetben használ, tekintse meg a [Salesforce védőfal integrációs oktatóanyagát](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Felhasználók hozzárendelése a Salesforce-hoz

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználói fiók kiépítése kontextusában csak a felhasználók és csoportok rendelt "", az alkalmazások az Azure AD szinkronizálása.

A kiépítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége dönthet arról, hogy mely felhasználók vagy csoportok Azure AD-ben a Salesforce alkalmazást hozzáférésre van szükségük. Miután végrehajtotta ezt a döntést, hozzárendelheti ezeket a felhasználókat a Salesforce alkalmazást a következő témakör utasításait követve [egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>A Salesforce-hoz való hozzárendelése a felhasználók fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a Salesforce-hoz az üzembe helyezési konfiguráció tesztelése. További felhasználók és csoportok később is rendelhető.

* Amikor egy felhasználó hozzárendelése a Salesforce-hoz, ki kell választania egy érvényes felhasználói szerepkörnek. Az "Alapértelmezett hozzáférés" szerepkör nem működik az üzembe helyezés

    > [!NOTE]
    > Ez az alkalmazás profilok importálja, a Salesforce-ból, amelyeket érdemes lehet az ügyfél akkor válassza, ha a felhasználók hozzárendelése az Azure ad-ben a kiépítési folyamat részeként. Vegye figyelembe, hogy a profilokat, amelyek a Salesforce-ból lesz importálva jelennek meg szerepkörök az Azure ad-ben.

## <a name="enable-automated-user-provisioning"></a>Az automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-csatlakozás a Salesforce a felhasználói fiók üzembe helyezési API és az eszközkiépítési szolgáltatás létrehozása, konfigurálása frissíteni, és tiltsa le a hozzárendelt felhasználói fiókok, a Salesforce-ban az Azure ad-ben a felhasználó és csoport-hozzárendelések alapján.

> [!Tip]
> Előfordulhat, hogy meg az SAML-alapú egyszeri bejelentkezés engedélyezve van, a Salesforce-hoz, a biztonsági utasítások megadott [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül az automatikus kiépítést, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="configure-automatic-user-account-provisioning"></a>Automatikus felhasználói fiók üzembe helyezésének konfigurálása

Ez a szakasz célja kidolgozására engedélyezése a felhasználó kiépítése az Active Directory felhasználói fiókokat a Salesforce-hoz.

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás** szakaszban.

2. Ha már konfigurálta a Salesforce az egyszeri bejelentkezés, keresse meg a Salesforce, a keresőmező használatával példányát. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **Salesforce** az alkalmazás-katalógusában. A keresési eredmények közül válassza ki a Salesforce-ban, és adja hozzá az alkalmazások listáját.

3. Válassza ki a Salesforce-példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![kiépítés folyamatban](./media/salesforce-provisioning-tutorial/provisioning.png)

5. Alatt a **rendszergazdai hitelesítő adataival** területén adja meg a következő beállításokat:

    a. Az a **rendszergazdai felhasználónév** szövegmezőbe írja be a Salesforce-fiók, amelynek neve a **rendszergazda** a Salesforce.com-on hozzárendelt profil.

    b. Az a **rendszergazdai jelszó** szövegmezőbe írja be a fiókhoz tartozó jelszót.

6. A Salesforce-ban biztonsági jogkivonatának beszerzéséhez, nyisson meg egy új lapon, és jelentkezzen be ugyanazt a Salesforce-ban rendszergazdai fiókot. Az a lap jobb felső sarkában kattintson a nevére, és kattintson **beállítások**.

    ![Felhasználók automatikus kiépítés engedélyezése](./media/salesforce-provisioning-tutorial/sf-my-settings.png "engedélyezése a felhasználók automatikus átadása")

7. A bal oldali navigációs panelén kattintson **saját személyes adatok** bontsa ki a kapcsolódó csomópontot, majd **alaphelyzetbe a saját biztonsági jogkivonat**.
  
    ![Felhasználók automatikus kiépítés engedélyezése](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "engedélyezése a felhasználók automatikus átadása")

8. A a **alaphelyzetbe állítása a biztonsági jogkivonat** kattintson **alaphelyzetbe állítása a biztonsági jogkivonat** gombra.

    ![Felhasználók automatikus kiépítés engedélyezése](./media/salesforce-provisioning-tutorial/sf-reset-token.png "engedélyezése a felhasználók automatikus átadása")

9. Ellenőrizze az e-mailben kapják a rendszergazdai fiókhoz rendelni. Keresse meg a Salesforce.com, az új biztonsági jogkivonatot tartalmazó e-mailt.

10. Jogkivonat másolása nyissa meg az Azure AD-ablakot, és illessze be azt a **titkos jogkivonat** mező.

11. A **bérlői URL-cím** kell megadni, ha a Salesforce-példány a Salesforce kormányzati felhő. Ellenkező esetben ez nem kötelező. A bérlői URL-cím a következő formátumban adja meg "https://\<a példány\>. my.salesforce.com," cseréje \<a példány\> a Salesforce-példány nevét.

12. Az Azure Portalon kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD csatlakozhat a Salesforce alkalmazást.

13. Az a **értesítő e-mailt** mezőbe írjon be egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket kapni, és jelölje be a jelölőnégyzetet az alábbi e-mail-címét.

14. Kattintson a **mentéséhez.**  

15. A leképezések szakasz alatt válassza ki a **szinkronizálása az Azure Active Directory-felhasználók a Salesforce-hoz.**

16. Az a **attribútumleképezések** területen tekintse át a Salesforce-bA az Azure ad-ből szinkronizált felhasználói attribútumok. Vegye figyelembe, hogy a kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókokat, a Salesforce-ban a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

17. Az Azure AD létesítési szolgáltatás a Salesforce-hoz engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** beállítások szakaszában

18. Kattintson a **mentéséhez.**

Ezzel elindítja a kezdeti szinkronizálás, a felhasználók és/vagy a Salesforce-hoz, a felhasználók és csoportok szakaszban hozzárendelt csoportok. Vegye figyelembe, hogy a kezdeti szinkronizálás hosszabb időt vesz igénybe ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut, mint végrehajtásához. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységeket tartalmazó naplók, amelyek leírják a Salesforce alkalmazásban a kiépítési szolgáltatás által végrehajtott összes műveletet.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
