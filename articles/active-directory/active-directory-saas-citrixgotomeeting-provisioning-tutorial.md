---
title: "Oktatóanyag: Azure Active Directoryval integrált Citrix GoToMeeting |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Citrix GoToMeeting között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 1ddfcd991431a11e5c3e306bd5905003d094ac18
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-configuring-citrix-gotomeeting-for-automatic-user-provisioning"></a>Oktatóanyag: Citrix GoToMeeting konfigurálása az automatikus felhasználó létesítése

Ez az oktatóanyag célja a lépéseket kell elvégeznie a Citrix GoToMeeting és az Azure AD automatikus kiépítése és leépíti a felhasználói fiókok Azure ad-Citrix GoToMeeting mutatjuk be.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active directory-bérlő.
*   A Citrix GoToMeeting egyszeri bejelentkezés engedélyezve van az előfizetésben.
*   Egy felhasználói fiókot a Citrix GoToMeeting Team rendszergazdai engedélyekkel.

## <a name="assigning-users-to-citrix-gotomeeting"></a>Felhasználók hozzárendelése Citrix GoToMeeting

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok "hozzárendelt" az Azure AD-alkalmazáshoz való szinkronizálása.

A létesítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége döntse el, hogy mely felhasználók és/vagy az Azure AD-csoportok határoz meg a felhasználók, akik a Citrix GoToMeeting alkalmazásához való hozzáférést. Ha úgy döntött, itt utasításokat követve hozzárendelheti ezeket a felhasználókat a Citrix GoToMeeting app:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-citrix-gotomeeting"></a>Felhasználók hozzárendelése Citrix GoToMeeting fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a Citrix GoToMeeting teszteli a telepítési konfigurációt. További felhasználók és/vagy csoportok később is rendelhető.

*   Amikor egy felhasználó rendel a Citrix GoToMeeting, ki kell választania egy érvényes felhasználói szerepkörnek. A "Default" szerepkör nem működik történő üzembe helyezéséhez.

## <a name="enable-automated-user-provisioning"></a>Az automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD kapcsolódás Citrix GoToMeeting felhasználói fiók kiépítése API és a létesítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a hozzárendelt felhasználói fiókok a Citrix GoToMeeting alapján a felhasználók és csoportok az Azure AD-hozzárendelés.

> [!TIP]
> Dönthet úgy is, engedélyezze SAML-alapú egyszeri bejelentkezést a Citrix GoToMeeting, utasítások megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurálása automatikus felhasználói fiók kiépítése:

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

2. Ha már konfigurált Citrix GoToMeeting egyszeri bejelentkezést, keresse meg a Citrix GoToMeeting használja a keresőmezőt példányát. Máskülönben válassza **Hozzáadás** keresse meg a **Citrix GoToMeeting** az alkalmazás katalógusában. Válassza ki a Citrix GoToMeeting a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3. Jelölje ki a Citrix GoToMeeting példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **kiépítés** módot **automatikus**. 

    ![Kiépítés](./media/active-directory-saas-citrixgotomeeting-provisioning-tutorial/provisioning.png)

5. A rendszergazdai hitelesítő adataival szakaszban a következő lépésekkel:
   
    a. Az a **Citrix GoToMeeting rendszergazda felhasználóneve** szövegmező, írja be a felhasználónevet, a rendszergazda.

    b. Az a **Citrix GoToMeeting rendszergazdai jelszó** szövegmező, a rendszergazdai jelszót.

6. Az Azure portálon kattintson **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat a Citrix GoToMeeting alkalmazást. Ha nem sikerül, győződjön meg arról, Citrix GoToMeeting fiókja Team rendszergazdai jogosultságokkal rendelkezik, majd próbálja meg a **"Rendszergazdai hitelesítő adatok"** léptessen ismét.

7. Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet.

8. Kattintson a **mentéséhez.**

9. A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználók a Citrix GoToMeeting.**

10. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumokat a Citrix GoToMeeting szinkronizált az Azure AD. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a Citrix GoToMeeting a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

11. Az Azure AD szolgáltatás a Citrix GoToMeeting kiépítését engedélyezéséhez módosítsa a **kiépítési állapot** való **a** beállításai szakaszában

12. Kattintson a **mentéséhez.**

A kezdeti szinkronizálás bármely felhasználói és/vagy a felhasználók és csoportok szakaszban Citrix GoToMeeting rendelt csoportok kezdődik. A kezdeti szinkronizálás végrehajtásához ezt követő szinkronizálások, amely körülbelül 20 percenként történik, amíg a szolgáltatás fut-nál több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához Tevékenységjelentések, amely a Citrix GoToMeeting app a létesítési szolgáltatás által végzett összes műveletet írják le.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
* [Egyszeri bejelentkezés konfigurálása](active-directory-saas-citrix-gotomeeting-tutorial.md)


