---
title: 'Oktatóanyag: Konfigurálja automatikus felhasználói történő üzembe helyezéséhez az Azure Active Directoryval OnDemand xMatters |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálja az Azure Active Directory automatikus kiépítése és leépíti xMatters OnDemand felhasználói fiókok.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: v-ant-msft
ms.openlocfilehash: 52fe23f5353a68e3afbac6a91955a61c7b8fb44c
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292670"
---
# <a name="tutorial-configure-xmatters-ondemand-for-automatic-user-provisioning"></a>Oktatóanyag: Konfigurálja automatikus felhasználói kialakítási OnDemand xMatters

Ez az oktatóanyag célja xMatters OnDemand és az Azure Active Directory (Azure AD) konfigurálása az Azure AD-be automatikusan létesítsen és leépíti a felhasználók és/vagy csoportok xMatters OnDemand végrehajtandó lépések bemutatása.

> [!NOTE]
> Ez az oktatóanyag leírja egy összekötőt, az Azure AD-felhasználó kiépítési Service platformra épül. Ez a szolgáltatás funkciója, hogyan működik, és gyakran ismételt kérdések fontos tudnivalókat tartalmaz [felhasználói kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz az Azure Active Directoryval történő automatizálásához](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő:

*   Az Azure AD-bérlő
*   Egy xMatters OnDemand bérlőt a [alapszintű](https://www.xmatters.com/pricing) a terv és jobban engedélyezve 
*   OnDemand xMatters rendszergazdai engedélyekkel rendelkező felhasználói fiók

> [!NOTE]
> Az Azure AD-integrációs kiépítés támaszkodik a [xMatters OnDemand API](https://help.xmatters.com/xmAPI), ez az alapszintű terven xMatters OnDemand csoportokhoz érhető el vagy jobb.

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>XMatters OnDemand hozzáadása a gyűjteményből

Mielőtt konfigurálná a xMatters OnDemand kiépítése az Azure AD automatikus felhasználóhoz, kell az Azure AD application gallery xMatters OnDemand hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD application gallery xMatters OnDemand hozzáadásához hajtsa végre az alábbi lépéseket:**

1. A a  **[Azure-portálon](https://portal.azure.com)**, a bal oldali navigációs panelen kattintson a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások** > **összes alkalmazás**.

    ![A vállalati alkalmazások szakasz][2]
    
3. XMatters OnDemand hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **xMatters OnDemand**.

    ![OnDemand-kiépítés xMatters](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearch.png)

5. Az eredmények panelen válassza ki a **xMatters OnDemand**, és kattintson a **Hozzáadás** xMatters OnDemand hozzá SaaS-alkalmazások listájának gombra.

    ![OnDemand-kiépítés xMatters](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearchResults.png)

    ![OnDemand-kiépítés xMatters](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-xmatters-ondemand"></a>Felhasználók hozzárendelése xMatters kötegmérete

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználókiépítése keretében csak a felhasználók és/vagy csoportok "hozzárendelt" egy az Azure AD alkalmazás szinkronizálva.

Automatikus felhasználó-kiépítés engedélyezése és konfigurálása, előtt meg kell határoznia, melyik felhasználói és/vagy az Azure AD-csoportok xMatters OnDemand hozzáférésre van szükségük. Ha úgy döntött, hozzárendelheti a felhasználók és/vagy csoportok xMatters OnDemand itt cikk utasításait követve:

*   [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-xmatters-ondemand"></a>Felhasználók hozzárendelése xMatters OnDemand fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve xMatters OnDemand konfigurálása kiosztás automatikus felhasználó teszteléséhez. További felhasználók és/vagy csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése xMatters OnDemand, a hozzárendelés párbeszédpanelen válassza ki érvényes alkalmazás-specifikus szerepköröket (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem kiépítés tartoznak.

## <a name="configuring-automatic-user-provisioning-to-xmatters-ondemand"></a>Felhasználólétesítés automatikus xMatters OnDemand konfigurálása 

Ez a szakasz végigvezeti az Azure AD szolgáltatás kiépítését, módosítása, és tiltsa le a felhasználók és/vagy a felhasználó alapján OnDemand xMatters csoportokat és/vagy az Azure AD-csoport-hozzárendelések konfigurálásának lépéseit.

> [!TIP]
> Ismertetett utasítások is választhatja, hogy SAML-alapú egyszeri bejelentkezés az xMatters OnDemand engedélyezéséhez, a [xMatters OnDemand egyszeri bejelentkezést az oktatóanyag](active-directory-saas-xmatters-ondemand-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül automatikus a felhasználók átadása, bár ez a két funkció egészítse ki egymást.

### <a name="to-configure-automatic-user-provisioning-for-xmatters-ondemand-in-azure-ad"></a>Konfigurálása automatikus felhasználókiépítése xMatters OnDemand az Azure AD-ben:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) és keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás**.

2. Válassza ki a xMatters OnDemand Szolgáltatottszoftver-alkalmazáshoz a listából.
 
    ![OnDemand-kiépítés xMatters](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ApplicationInstanceSearch.png)

3. Válassza ki a **kiépítési** fülre.
    
    ![OnDemand-kiépítés xMatters](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**.

    ![OnDemand-kiépítés xMatters](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Az a **rendszergazdai hitelesítő adataival** területen adjon meg a **rendszergazda felhasználóneve**, **rendszergazdai jelszó**, és **tartomány** a xMatters OnDemand a fiók.

    *   Az a **rendszergazda felhasználóneve** mezőbe feltöltése a xMatters OnDemand tenant a rendszergazdai fiók felhasználónevét. Példa: admin@contoso.com.

    *   Az a **rendszergazdai jelszó** mezőbe a jelszót a rendszergazdai felhasználónév megfelelő feltöltéséhez.

    *   Az a **tartomány** mezőbe xMatters OnDemand bérlője altartomány feltöltéséhez.
    Például: A fiók bérlői URL-cím https://my-tenant.xmatters.com, a altartomány lenne **saját bérlőt**.

6. Után a program kitölti a mezőket az 5. lépés, kattintson a **kapcsolat tesztelése** biztosításához az Azure AD xMatters OnDemand kapcsolódhatnak. Ha nem sikerül, győződjön meg arról, a xMatters OnDemand fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![OnDemand-kiépítés xMatters](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/TestConnection.png)
    
7. Az a **értesítő e-mailt** mezőbe írja be az e-mail cím vagy egy csoportot kell az üzembe helyezési hiba értesítéseket, és jelölje be a jelölőnégyzetet - **e-mailben értesítést küld, ha hiba lép fel**.

    ![OnDemand-kiépítés xMatters](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/EmailNotification.png)

8. Kattintson a **Save** (Mentés) gombra.

9. Az a **hozzárendelések** szakaszban jelölje be **szinkronizálása Azure Active Directory-felhasználók számára xMatters OnDemand**.

    ![OnDemand-kiépítés xMatters](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncUsers.png)

10. Tekintse át a felhasználói attribútumok, az Azure AD xMatters OnDemand lettek szinkronizálva a a **attribútum leképezési** szakasz. A kiválasztott attribútumok **egyező** tulajdonságok felel meg a felhasználói fiókok, a frissítési műveletek OnDemand xMatters szolgálnak. Válassza ki a **mentése** gombra a módosítások véglegesítéséhez.

    ![OnDemand-kiépítés xMatters](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/UserAttributes.png)

11. Az a **hozzárendelések** szakaszban jelölje be **szinkronizálása Azure Active Directory-csoportot xMatters OnDemand**.

    ![OnDemand-kiépítés xMatters](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncGroups.png)

12. Tekintse át a xMatters OnDemand a szinkronizált Azure ad-csoport attribútumait a a **attribútum leképezési** szakasz. A kiválasztott attribútumok **egyező** tulajdonságok felel meg a frissítési műveletek OnDemand xMatters csoportok használhatók. Válassza ki a **mentése** gombra a módosítások véglegesítéséhez.

    ![OnDemand-kiépítés xMatters](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/GroupAttributes.png)

13. Hatókörként szűrők konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](./active-directory-saas-scoping-filters.md).

14. Az Azure AD szolgáltatás xMatters OnDemand-kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz.

    ![OnDemand-kiépítés xMatters](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningStatus.png)

15. Az xMatters OnDemand azokat a felhasználók és/vagy csoportok, amelyeket meg szeretne megadása válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakasz.

    ![OnDemand-kiépítés xMatters](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ScopeSelection.png)

16. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![OnDemand-kiépítés xMatters](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SaveProvisioning.png)

Ez a művelet elindítja a kezdeti szinkronizálás az összes felhasználót és/vagy csoportok meghatározott **hatókör** a a **beállítások** szakasz. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, körülbelül 40 percenként történik, amíg az Azure AD szolgáltatás kiépítését fut-e hosszabb időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenység jelentést, amely ismerteti a xMatters OnDemand szolgáltatás kiépítését az Azure AD által végzett összes műveletet.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Ismerje meg, tekintse át a naplók és jelentések készítése a kiépítés tevékenység](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_03.png
