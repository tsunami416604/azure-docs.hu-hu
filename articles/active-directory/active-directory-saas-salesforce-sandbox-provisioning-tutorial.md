---
title: 'Oktatóanyag: Salesforce védőfal konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Salesforce védőfal között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bab73fda-6754-411d-9288-f73ecdaa486d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 71a7c06309f52fc2f89852a3426cbb4d69248c5d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés Salesforce védőfal konfigurálása

Ez az oktatóanyag célja a lépéseket kell elvégeznie a Salesforce védőfal és az Azure AD automatikus kiépítése és leépíti a felhasználói fiókok Azure ad-Salesforce védőfal mutatjuk be.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active directory-bérlő.
*   Egy érvényes bérlőt Salesforce védőfal munka vagy a Salesforce védőfal oktatási célokra. Egy ingyenes próbafiók vagy a szolgáltatás segítségével.
*   Egy felhasználói fiókot a Salesforce védőfal Team rendszergazdai engedélyekkel.

## <a name="assigning-users-to-salesforce-sandbox"></a>Felhasználók hozzárendelése Salesforce védőfal

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok számára "rendelt" az Azure AD alkalmazás szinkronizálva.

A létesítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége döntse el, hogy mely felhasználók vagy csoportok az Azure ad-ben a Salesforce védőfal alkalmazásba hozzáférésre van szükségük. Ehhez a döntéshez elkészítése után rendelhet ezeket a felhasználókat a Salesforce védőfal alkalmazásba utasításait követve [egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Felhasználók hozzárendelése Salesforce védőfal fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Salesforce védőfal teszteli a telepítési konfigurációt. További felhasználók és/vagy csoportok később is rendelhető.

* Amikor egy felhasználó rendel a Salesforce védőfal, ki kell választania egy érvényes felhasználói szerepkörnek. A "Default" szerepkör nem működik történő üzembe helyezéséhez.

> [!NOTE]
> Ez az alkalmazás egyéni szerepkörök importálja a Salesforce védőfal a telepítési folyamatot, amely az ügyfél esetleg szeretné kiválasztani a felhasználók hozzárendelésekor részeként.

## <a name="enable-automated-user-provisioning"></a>Az automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD kapcsolódás Salesforce védőfal felhasználói fiók kiépítése API és a létesítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a hozzárendelt felhasználói fiókok a Salesforce védőfal alapján a felhasználók és csoportok az Azure AD-hozzárendelés.

>[!Tip]
>Választhatja azt is, SAML-alapú egyszeri bejelentkezés Salesforce védőfal engedélyezve van, az utasításokat követve megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurálja az automatikus felhasználói fiók kiépítése

Ez a szakasz célja felvázoló engedélyezése a felhasználók átadása, az Active Directory felhasználói fiókoknak Salesforce védőfal felé.

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

2. Ha az egyszeri bejelentkezés Salesforce védőfal már beállította, keresse meg a Salesforce védőfal használja a keresőmezőt példányát. Máskülönben válassza **Hozzáadás** keresse meg a **Salesforce védőfal** az alkalmazás katalógusában. Válassza ki a Salesforce védőfal a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3. Jelölje ki a Salesforce védőfal példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**.

    ![kiépítés folyamatban](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/provisioning.png)

5. Az a **rendszergazdai hitelesítő adataival** területen adja meg a következő konfigurációs beállításokat:
   
    a. Az a **rendszergazda felhasználóneve** szövegmezőhöz Salesforce védőfalat a fióknevet, amelynek típusa a **rendszergazda** Salesforce.com rendelt profillal.
   
    b. Az a **rendszergazdai jelszó** szövegmező, írja be a fiókhoz tartozó jelszót.

6. A Salesforce védőfal biztonsági jogkivonatának beszerzéséhez, nyisson meg egy új lapon és a bejelentkezés Salesforce védőfal egy rendszergazdai fiókhoz. Az oldal jobb felső sarkában kattintson a nevére, és kattintson a **beállítások**.

     ![Az automatikus felhasználó-kiépítés engedélyezése](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "automatikus felhasználó-kiépítés engedélyezése")

7. A bal oldali navigációs ablaktábláján kattintson **saját személyes adatok** bontsa ki a kapcsolódó csomópontot, majd **alaphelyzetbe állítani a biztonsági jogkivonat**.
  
    ![Az automatikus felhasználó-kiépítés engedélyezése](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "automatikus felhasználó-kiépítés engedélyezése")

8. Az a **alaphelyzetbe állítani a biztonsági jogkivonat** lapján kattintson a **alaphelyzetbe állítani a biztonsági jogkivonat** gombra.

    ![Az automatikus felhasználó-kiépítés engedélyezése](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "automatikus felhasználó-kiépítés engedélyezése")

9. Ellenőrizze a rendszergazdai fiókhoz tartozó e-mailben kapják. Keresse meg a Salesforce Sandbox.com az új biztonsági jogkivonatot tartalmazó e-mailt.

10. Másolja a token nyissa meg az Azure AD ablakba, és illessze be azt a **titkos Token** mező.

11. Az Azure portálon kattintson **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat a Salesforce védőfal alkalmazást.

12. Az a **értesítő e-mailt** mezőbe írja be az e-mail cím vagy egy csoportot ki kell üzembe helyezési hiba értesítéseket, és jelölje be a jelölőnégyzetet.

13. Kattintson a **mentéséhez.**  
    
14.  A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználók Salesforce védőfal felé.**

15. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumok szinkronizált Azure ad-Salesforce védőfal felé. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a Salesforce védőfal a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

16. Az Azure AD szolgáltatás Salesforce védőfal kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** beállításai szakaszában

17. Kattintson a **mentéséhez.**

A kezdeti szinkronizálás bármely felhasználói és/vagy a felhasználók és csoportok szakaszban Salesforce védőfal rendelt csoportok kezdődik. A kezdeti szinkronizálás végrehajtásához ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut-nál több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenységi naplóit, amelynek Salesforce védőfal app a létesítési szolgáltatás által végzett összes műveletet írják le.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
