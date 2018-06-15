---
title: 'Oktatóanyag: GoToMeeting konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és GoToMeeting között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: cbf53904c5fbf4ddfeb8c9d72efcf4549b1d7a7a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34348111"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés GoToMeeting konfigurálása

Ez az oktatóanyag célja a lépéseket kell elvégeznie a GoToMeeting és az Azure AD automatikus kiépítése és leépíti a felhasználói fiókok Azure ad-GoToMeeting mutatjuk be.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active directory-bérlő.
*   Egy GoToMeeting egyszeri bejelentkezés engedélyezve van az előfizetés.
*   Egy felhasználói fiókot az GoToMeeting Team rendszergazdai engedélyekkel.

## <a name="assigning-users-to-gotomeeting"></a>Felhasználók hozzárendelése GoToMeeting

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok "hozzárendelt" az Azure AD-alkalmazáshoz való szinkronizálása.

A létesítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége döntse el, hogy mely felhasználók és/vagy az Azure AD-csoportok határoz meg a felhasználók, akik az GoToMeeting alkalmazásához való hozzáférést. Ha úgy döntött, itt cikk utasításait követve hozzárendelheti ezeket a felhasználókat az GoToMeeting alkalmazás:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Felhasználók hozzárendelése GoToMeeting fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve GoToMeeting teszteli a telepítési konfigurációt. További felhasználók és/vagy csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése GoToMeeting, ki kell választania egy érvényes felhasználói szerepkörnek. A "Default" szerepkör nem működik történő üzembe helyezéséhez.

## <a name="enable-automated-user-provisioning"></a>Az automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD kapcsolódás GoToMeeting a felhasználói fiók kiépítése API és a létesítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a felhasználók és csoportok hozzárendelése az Azure AD-alapú GoToMeeting hozzárendelt felhasználói fiókok.

> [!TIP]
> Dönthet úgy is, SAML-alapú egyszeri bejelentkezést GoToMeeting engedélyezni, utasítások megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurálása automatikus felhasználói fiók kiépítése:

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

2. Ha már konfigurált GoToMeeting egyszeri bejelentkezést, keresse meg a keresési mező GoToMeeting példányát. Máskülönben válassza **Hozzáadás** keresse meg a **GoToMeeting** az alkalmazás katalógusában. Válassza ki a GoToMeeting a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3. Jelölje ki a GoToMeeting példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **kiépítés** módot **automatikus**. 

    ![kiépítés folyamatban](./media/active-directory-saas-citrixgotomeeting-provisioning-tutorial/provisioning.png)

5. A rendszergazdai hitelesítő adataival szakaszban a következő lépésekkel:
   
    a. Az a **GoToMeeting rendszergazda felhasználóneve** szövegmező, írja be a felhasználónevet, a rendszergazda.

    b. Az a **GoToMeeting rendszergazdai jelszó** szövegmező, a rendszergazdai jelszót.

6. Az Azure portálon kattintson **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat az GoToMeeting alkalmazást. Ha nem sikerül, győződjön meg arról, GoToMeeting fiókja Team rendszergazdai jogosultságokkal rendelkezik, majd próbálja meg a **"Rendszergazdai hitelesítő adatok"** léptessen ismét.

7. Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet.

8. Kattintson a **mentéséhez.**

9. A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználókat GoToMeeting.**

10. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumok, az Azure AD GoToMeeting lettek szinkronizálva. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a GoToMeeting a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

11. Az Azure AD szolgáltatás GoToMeeting kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** beállításai szakaszában

12. Kattintson a **mentéséhez.**

A kezdeti szinkronizálás bármely felhasználói és/vagy a felhasználók és csoportok szakaszban GoToMeeting rendelt csoportok kezdődik. A kezdeti szinkronizálás végrehajtásához ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut-nál több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenységi naplóit, amelyek ismertetik a GoToMeeting app a létesítési szolgáltatás által végzett összes műveletet.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


