---
title: "Oktatóanyag: GitHub konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálja az Azure Active Directory automatikus kiépítése és leépíti a GitHub felhasználói fiókokat."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.openlocfilehash: 53eb2d0e76cb4765612dfd30226979d839a96235
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2018
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés GitHub konfigurálása


Ez az oktatóanyag célja a lépéseket kell elvégeznie a Githubon és az Azure AD automatikus kiépítése és leépíti a felhasználói fiókok Azure ad-GitHub mutatjuk be. 

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active directory-bérlő
*   Egy Github bérlőt a [üzleti terv](https://help.github.com/articles/organization-billing-plans/#business-plan) vagy jobban engedélyezve 
*   A Githubon rendszergazdai jogosultságokkal rendelkező felhasználói fiókot 

> [!NOTE]
> Az Azure AD-integrációs kiépítés támaszkodik a [GitHub SCIM API](https://developer.github.com/v3/scim/), az üzleti terv a Github-csapat rendelkezésére vagy nagyobb.

## <a name="assigning-users-to-github"></a>Felhasználók hozzárendelése a Githubon

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok "hozzárendelt" az Azure AD-alkalmazáshoz való szinkronizálása. 

A létesítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége döntse el, hogy mely felhasználók és/vagy az Azure AD-csoportok határoz meg a felhasználók, akik a Githubon alkalmazásához való hozzáférést. Ha úgy döntött, itt cikk utasításait követve ezek a felhasználók rendelhet a GitHub-alkalmazásba:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Felhasználók hozzárendelése GitHub fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a GitHub tesztelje a telepítési konfigurációt. További felhasználók és/vagy csoportok később is rendelhető.

*   Ha egy felhasználó hozzárendelése a Githubon, kell-e ki lehet a **felhasználói** szerepkör, vagy egy másik érvényes alkalmazásspecifikus szerepkör (ha elérhető) a hozzárendelés párbeszédpanelen. A **alapértelmezett hozzáférési** szerepkör nem működik a rendszerbe állításához, és ezek a felhasználók kimarad.


## <a name="configuring-user-provisioning-to-github"></a>A felhasználók átadása a GitHub konfigurálása 

Ez a szakasz végigvezeti az Azure AD csatlakozik a GitHub felhasználói fiók kiépítése API és a létesítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a Githubon az Azure AD-felhasználók és csoportok hozzárendelése alapján hozzárendelt felhasználói fiókok.

> [!TIP]
> Dönthet úgy is, engedélyezze SAML-alapú egyszeri bejelentkezést a Githubon, utasítások megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.


### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Automatikus felhasználói fiók kiépítése a Githubon az Azure AD konfigurálása


1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

2. Ha már konfigurált GitHub egyszeri bejelentkezést, keresse meg az használja a keresőmezőt GitHub-példány. Máskülönben válassza **Hozzáadás** keresse meg a **GitHub** az alkalmazás katalógusában. A keresési eredmények közül válassza ki a Githubon, és adja hozzá az alkalmazások listáját.

3. Jelölje ki a Githubon példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**.

    ![GitHub-kiépítés](./media/active-directory-saas-github-provisioning-tutorial/GitHub1.png)

5. Az a **rendszergazdai hitelesítő adataival** kattintson **engedélyezés**. Ez a művelet egy GitHub-engedélyezési párbeszédpanelt egy új böngészőablakban nyílik meg. 

6. Az új ablakban jelentkezzen be arra a Githubon a rendszergazda fiók használatával. Az eredményül kapott engedélyezési párbeszédpanelen a GitHub-csoport, amely engedélyezi a kiépítés, majd válassza ki és **engedélyezés**. Ezt követően térjen vissza az Azure-portálon a létesítési konfiguráció befejezéséhez.

    ![Engedélyezési párbeszédpanel](./media/active-directory-saas-github-provisioning-tutorial/GitHub2.png)

7. Az Azure-portálon bemeneti **bérlői URL-cím** kattintson **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat a GitHub-alkalmazást. Ha nem sikerül, győződjön meg arról, a GitHub-fiók rendszergazdai jogosultságokkal rendelkezik egy és **bérlői URL-cím** megfelelően van-e későbbinek, majd próbálja meg újból az "Engedélyezés" lépést (is jelentenek **bérlői URL-cím** szabály: "https://api.github.com/scim/v2/organizations/ + < Organizations_name > ", a szervezetek a GitHub-fiókjában található: **beállítások** > **szervezetek**).

    ![Engedélyezési párbeszédpanel](./media/active-directory-saas-github-provisioning-tutorial/GitHub3.png)

8. Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet a "E-mail értesítés küldése hiba esetén."

9. Kattintson a **Save** (Mentés) gombra. 

10. A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználók a GitHub**.

11. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumokat a GitHub szinkronizált Azure AD-ből. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a Githubon a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

12. Az Azure AD szolgáltatás a GitHub-kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz

13. Kattintson a **Save** (Mentés) gombra. 

Ez a művelet bármilyen felhasználói és/vagy a felhasználók és csoportok szakaszban GitHub rendelt csoportok a kezdeti szinkronizálás elindul. A kezdeti szinkronizálás végrehajtásához ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut-nál több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenységi naplóit, amelyek a létesítési szolgáltatás által végzett összes műveletet írják le.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-enterprise-apps-manage-provisioning.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>További lépések

* [Ismerje meg, tekintse át a naplók és jelentések készítése a kiépítés tevékenység](active-directory-saas-provisioning-reporting.md)
