---
title: 'Oktatóanyag: Replicon konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálja az Azure Active Directory automatikusan ellátásához, majd leépíti Replicon felhasználói fiókokat.
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
ms.date: 02/20/2018
ms.author: v-ant
ms.openlocfilehash: 0ff0bcffae506e593f8ddc95886142df30164e65
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293279"
---
# <a name="tutorial-configure-replicon-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés Replicon konfigurálása

Ez az oktatóanyag célja az Replicon és Azure Active Directory (Azure AD) és az Azure AD konfigurálása automatikusan létesítsen és leépíti a felhasználók és/vagy csoportok Replicon végrehajtandó lépések bemutatása.

> [!NOTE]
> Ez az oktatóanyag leírja egy összekötőt, az Azure AD-felhasználó kiépítési Service platformra épül. Ez a szolgáltatás funkciója, hogyan működik, és gyakran ismételt kérdések fontos tudnivalókat tartalmaz [felhasználói kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz az Azure Active Directoryval történő automatizálásához](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure AD-bérlő
*   Egy Replicon bérlőt a [Plus](https://www.replicon.com/time-bill-pricing/) a terv és jobban engedélyezve
*   A Replicon rendszergazdai jogosultságokkal rendelkező felhasználói fiókot

> [!NOTE]
> Az Azure AD-integrációs kiépítés támaszkodik a [Replicon API](https://www.replicon.com/help/developers), amely Replicon csapat rendelkezésére áll a plusz a terv és jobb.

## <a name="adding-replicon-from-the-gallery"></a>A gyűjteményből Replicon hozzáadása
Replicon konfigurálása az Azure AD automatikus felhasználólétesítés, előtt kell az Azure AD application gallery Replicon hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD application gallery Replicon hozzáadásához hajtsa végre az alábbi lépéseket:**

1. A a  **[Azure-portálon](https://portal.azure.com)**, a bal oldali navigációs panelen kattintson a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások** > **összes alkalmazás**.

    ![A vállalati alkalmazások szakasz][2]
    
3. Replicon hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Replicon**.

    ![Replicon alkalmazás keresése](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearch.png)

5. Az eredmények panelen válassza ki a **Replicon**, majd kattintson a **hozzáadása** Replicon hozzá SaaS-alkalmazások listájának gombra.

    ![Replicon keresési eredmények](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearchResults.png)

    ![Replicon alkalmazás létrehozása](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppCreate.png)


## <a name="assigning-users-to-replicon"></a>Felhasználók hozzárendelése Replicon

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználókiépítése keretében csak a felhasználók és/vagy csoportok "hozzárendelt" egy az Azure AD alkalmazás szinkronizálva.

Automatikus felhasználó-kiépítés engedélyezése és konfigurálása, előtt meg kell határoznia, melyik felhasználói és/vagy az Azure AD-csoportok Replicon hozzáférésre van szükségük. Ha úgy döntött, hozzárendelheti a felhasználók és/vagy csoportok Replicon itt cikk utasításait követve:

*   [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-replicon"></a>Felhasználók hozzárendelése Replicon fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Replicon konfigurálása kiosztás automatikus felhasználó teszteléséhez. További felhasználók és/vagy csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése Replicon, a hozzárendelés párbeszédpanelen válassza ki érvényes alkalmazás-specifikus szerepköröket (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem kiépítés tartoznak.

## <a name="configuring-automatic-user-provisioning-to-replicon"></a>Felhasználólétesítés automatikus Replicon konfigurálása 

Ez a szakasz végigvezeti az Azure AD szolgáltatás kiépítését, módosítása, és tiltsa le a felhasználók konfigurálásának lépéseit és/vagy az Azure AD felhasználói és/vagy a csoport-hozzárendelések alapján Replicon csoportok.

> [!TIP]
> Választhatja azt is, SAML-alapú egyszeri bejelentkezés engedélyezése a Replicon, utasítások találhatók a [Replicon egyszeri bejelentkezés az oktatóanyag](active-directory-saas-replicon-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül automatikus a felhasználók átadása, bár ez a két funkció egészítse ki egymást.

### <a name="to-configure-automatic-user-provisioning-for-replicon-in-azure-ad"></a>Konfigurálása automatikus felhasználókiépítése Replicon az Azure AD-ben:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) és keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás**.

2. Válassza ki a Replicon Szolgáltatottszoftver-alkalmazáshoz a listából.

    ![Replicon kiépítése](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon2.png)

3. Válassza ki a **kiépítési** fülre.

    ![Replicon kiépítése](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningTab.png)

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**.

    ![Replicon kiépítése](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon1.png)

5. Az a **rendszergazdai hitelesítő adataival** területen adjon meg a **rendszergazda felhasználóneve**, **rendszergazdai jelszó**, **CompanyId**, és **tartomány**  a Replicon fiók. Például a következő értékek a következők:

    *   Az a **rendszergazda felhasználóneve** mezőbe feltöltése a Replicon tenant a rendszergazdai fiók felhasználónevét. Példa: contosoadmin.

    *   Az a **rendszergazdai jelszó** mezőbe a jelszót a rendszergazdai felhasználónév megfelelő feltöltéséhez.

    *   Az a **CompanyId** mezőbe Replicon bérlője CompanyId feltöltéséhez. Példa: A napló a alatt alapján CompanyID Contoso.

    ![Replicon bejelentkezés](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconLogin.png)

    *   Az a **tartomány** mezőben, a tartomány feltöltéséhez, 6. lépésben leírtak szerint.
    
6. Szerezzen be **serviceEndpointRootURL** a Replicon a bérlői fiókjához említett lépések alapján [Replicon szolgáltatás segítségével](https://www.replicon.com/help/determining-the-url-for-your-service-calls). Az URL-címet, amint a **tartomány** lenne a altartománya **serviceEndpointRootURL** szerint kijelölt. 

    ![Replicon kiépítése](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconEndpoint.png)

7. A program kitölti a mezőket az 5. lépés, után kattintson a **kapcsolat tesztelése** biztosításához az Azure AD Replicon kapcsolódhatnak. Ha nem sikerül, ellenőrizze a Replicon fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Replicon kiépítése](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconTestConnection.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail cím vagy egy csoportot, a létesítési hiba értesítéseket, és jelölje be a jelölőnégyzetet kell **e-mailben értesítést küld, ha hiba lép fel**.

    ![Replicon kiépítése](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconNotificationEmail.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Az a **hozzárendelések** szakaszban jelölje be **szinkronizálása Azure Active Directory-felhasználókat Replicon**.
    
    ![Replicon kiépítése](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMapping.png)

11. Tekintse át a felhasználói attribútumok, az Azure AD a Replicon lettek szinkronizálva a **attribútum leképezési** szakasz. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a Replicon a frissítési műveleteket. Válassza ki a **mentése** gombra a módosítások véglegesítéséhez.

    ![Replicon kiépítése](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMappingAtrributes.png)

12. Hatókörként szűrők konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](./active-directory-saas-scoping-filters.md).

13. Az Azure AD szolgáltatás Replicon kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz.

    ![Replicon kiépítése](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningStatus.png)

14. Válassza ki a kívánt értékeket a Replicon azokat a felhasználók és/vagy csoportok, amelyeket meg szeretne meghatározásához **hatókör** a a **beállítások** szakasz.

    ![Replicon kiépítése](./media/active-directory-saas-replicon-provisioning-tutorial/UserGroupSelection.png)

15. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Replicon kiépítése](./media/active-directory-saas-replicon-provisioning-tutorial/SaveProvisioning.png)

Ez a művelet elindítja a kezdeti szinkronizálás az összes felhasználót és/vagy csoportok meghatározott **hatókör** a a **beállítások** szakasz. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, körülbelül 40 percenként történik, amíg az Azure AD szolgáltatás kiépítését fut-e hosszabb időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenység jelentést, amely ismerteti a Replicon szolgáltatás kiépítését az Azure AD által végzett összes műveletet.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Ismerje meg, tekintse át a naplók és jelentések készítése a kiépítés tevékenység](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_03.png
