---
title: "Oktatóanyag: Azure Active Directoryval integrált Salesforce |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Salesforce között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: 1c7d029515f27610e5d24c56ec1c60d6830e9e45
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2017
---
# <a name="tutorial-configuring-salesforce-for-automatic-user-provisioning"></a>Oktatóanyag: Salesforce konfigurálása az automatikus felhasználó létesítése

Ez az oktatóanyag célja a Salesforce és az Azure AD automatikus kiépítéséhez elvégzéséhez szükséges lépéseket és deaktiválás rendelkezés felhasználói fiókok Azure ad-Salesforce megjelenítése.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active directory-bérlő.
*   Rendelkeznie kell egy érvényes bérlőt Salesforce a munkahelyén vagy a Salesforce oktatási célokra. Egy ingyenes próbafiók vagy a szolgáltatás segítségével.
*   Egy felhasználói fiókot a Salesforce-ban Team rendszergazdai engedélyekkel.

## <a name="assigning-users-to-salesforce"></a>Felhasználók hozzárendelése Salesforce

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok "hozzárendelt" az Azure AD-alkalmazáshoz való szinkronizálása.

A létesítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége döntse el, hogy mely felhasználók vagy csoportok az Azure ad-ben a Salesforce alkalmazáshoz hozzáférésre van szükségük. Ez a döntés elkészítése után rendelhet ezeket a felhasználókat a Salesforce alkalmazáshoz utasításait követve [egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Felhasználók hozzárendelése Salesforce fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Salesforce teszteli a telepítési konfigurációt. További felhasználók és/vagy csoportok később is rendelhető.

*  Amikor egy felhasználó rendel a Salesforce, ki kell választania egy érvényes felhasználói szerepkörnek. A "Default" szerepkör nem működik történő üzembe helyezéséhez

    > [!NOTE]
    > Ez az alkalmazás egyéni szerepkörök importál a telepítési folyamatot, amely az ügyfél esetleg szeretné kiválasztani a felhasználók hozzárendelésekor részeként Salesforce

## <a name="enable-automated-user-provisioning"></a>Az automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD kapcsolódás Salesforce a felhasználói fiók kiépítése API és a létesítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a hozzárendelt felhasználói fiókok a Salesforce alapján a felhasználók és csoportok hozzárendelése az Azure AD.

>[!Tip]
>Dönthet úgy is, SAML-alapú egyszeri bejelentkezés Salesforce engedélyezni, utasítások megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurálja az automatikus felhasználói fiók kiépítése

Ez a szakasz célja felvázoló engedélyezése a felhasználók átadása Salesforce Active Directory felhasználói fiókokat.

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

2. Ha már beállította az egyszeri bejelentkezés Salesforce, keresse meg az használja a keresőmezőt Salesforce-példány. Máskülönben válassza **Hozzáadás** keresse meg a **Salesforce** az alkalmazás katalógusában. Válassza ki a Salesforce a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3. Jelölje ki a Salesforce példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**.

    ![kiépítés folyamatban](./media/active-directory-saas-salesforce-provisioning-tutorial/provisioning.png)

5. Az a **rendszergazdai hitelesítő adataival** területen adja meg a következő konfigurációs beállításokat:
   
    a. Az a **rendszergazda felhasználóneve** szövegmezőhöz a Salesforce-fióknév, amelynek típusa a **rendszergazda** Salesforce.com rendelt profillal.
   
    b. Az a **rendszergazdai jelszó** szövegmező, írja be a fiókhoz tartozó jelszót.

6. A Salesforce biztonsági jogkivonatának beszerzéséhez, nyisson meg egy új lapon és a bejelentkezés Salesforce egy rendszergazdai fiókhoz. Az oldal jobb felső sarkában kattintson a nevére, és kattintson a **beállítások**.

     ![Az automatikus felhasználó-kiépítés engedélyezése](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-my-settings.png "automatikus felhasználó-kiépítés engedélyezése")

7. A bal oldali navigációs ablaktábláján kattintson **saját személyes adatok** bontsa ki a kapcsolódó csomópontot, majd **alaphelyzetbe állítani a biztonsági jogkivonat**.
  
    ![Az automatikus felhasználó-kiépítés engedélyezése](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-personal-reset.png "automatikus felhasználó-kiépítés engedélyezése")

8. A a **alaphelyzetbe állítani a biztonsági jogkivonat** kattintson **alaphelyzetbe állítani a biztonsági jogkivonat** gombra.

    ![Az automatikus felhasználó-kiépítés engedélyezése](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-reset-token.png "automatikus felhasználó-kiépítés engedélyezése")

9. Ellenőrizze a rendszergazdai fiókhoz tartozó e-mailben kapják. Keresse meg a Salesforce.com az új biztonsági jogkivonatot tartalmazó e-mailt.

10. Másolja a token nyissa meg az Azure AD ablakba, és illessze be azt a **titkos Token** mező.

11. A **bérlői URL-cím** kell adni, ha az adott Salesforce-példány a Salesforce kormányzati felhő. Ellenkező esetben nem kötelező. Adja meg a bérlői URL-cím a következő formátumban https://your-instance.my.salesforce.com, az a példány cseréje a Salesforce-példány nevét.

12. Az Azure portálon kattintson **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat a Salesforce alkalmazást.

13. Az a **értesítő e-mailt** mezőbe írja be az e-mail cím vagy egy csoportot ki kell üzembe helyezési hiba értesítéseket, és jelölje be az alábbi jelölőnégyzetet.

14. Kattintson a **mentéséhez.**  
    
15.  A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználók a Salesforce.**

16. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumokat a Salesforce szinkronizált Azure AD-ből. Vegye figyelembe, hogy az attribútumok választotta **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a Salesforce-ban a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

17. Az Azure AD szolgáltatás a Salesforce-kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** beállításai szakaszában

18. Kattintson a **mentéséhez.**

Ezzel elindítja a kezdeti szinkronizálás bármely felhasználói és/vagy a felhasználók és csoportok szakaszban Salesforce rendelt csoportok. Figyelje meg, hogy a kezdeti szinkronizálás végrehajtásához bekövetkező körülbelül 20 percenként, mindaddig, amíg a szolgáltatás fut. ezt követő szinkronizálások hosszabb időbe telik. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenység jelentéseit, amelyek a Salesforce alkalmazást a létesítési szolgáltatás által végzett összes műveletet írják le.

Mostantól létrehozhat egy olyan fiókot. Akár 20 percig várjon győződjön meg arról, hogy a fiók a Salesforce lett szinkronizálva.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
* [Egyszeri bejelentkezés konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
