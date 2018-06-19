---
title: 'Oktatóanyag: BlueJeans konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálja az Azure Active Directory automatikusan ellátásához, majd leépíti BlueJeans felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: v-ant
ms.openlocfilehash: f66576946559caf99d9d9c5cd8590b3b43900d65
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923653"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés BlueJeans konfigurálása

Ez az oktatóanyag célja az BlueJeans és Azure Active Directory (Azure AD) és az Azure AD konfigurálása automatikusan létesítsen és leépíti a felhasználók és/vagy csoportok BlueJeans végrehajtandó lépések bemutatása.

> [!NOTE]
> Ez az oktatóanyag leírja egy összekötőt, az Azure AD-felhasználó kiépítési Service platformra épül. Ez a szolgáltatás funkciója, hogyan működik, és gyakran ismételt kérdések fontos tudnivalókat tartalmaz [felhasználói kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz az Azure Active Directoryval történő automatizálásához](./../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő:

*   Az Azure AD-bérlő
*   Egy BlueJeans bérlőt a [saját vállalati](https://www.BlueJeans.com/pricing) a terv és jobban engedélyezve
*   A BlueJeans rendszergazdai jogosultságokkal rendelkező felhasználói fiókot

> [!NOTE]
> Az Azure AD-integrációs kiépítés támaszkodik a [BlueJeans API](https://BlueJeans.github.io/developer), a Standard csomag BlueJeans csoportokhoz érhető el vagy nagyobb.

## <a name="adding-bluejeans-from-the-gallery"></a>A gyűjteményből BlueJeans hozzáadása
BlueJeans konfigurálása az Azure AD automatikus felhasználólétesítés, előtt kell az Azure AD application gallery BlueJeans hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD application gallery BlueJeans hozzáadásához hajtsa végre az alábbi lépéseket:**

1. A a  **[Azure-portálon](https://portal.azure.com)**, a bal oldali navigációs panelen kattintson a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások** > **összes alkalmazás**.

    ![A vállalati alkalmazások szakasz][2]
    
3. BlueJeans hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **BlueJeans**.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansAppSearch.png)

5. Az eredmények panelen válassza ki a **BlueJeans**, majd kattintson a **hozzáadása** BlueJeans hozzá SaaS-alkalmazások listájának gombra.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansAppSearchResults.png)

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansAppCreate.png)
    
## <a name="assigning-users-to-bluejeans"></a>Felhasználók hozzárendelése BlueJeans

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználókiépítése keretében csak a felhasználók és/vagy csoportok "hozzárendelt" egy az Azure AD alkalmazás szinkronizálva.

Automatikus felhasználó-kiépítés engedélyezése és konfigurálása, előtt meg kell határoznia, melyik felhasználói és/vagy az Azure AD-csoportok BlueJeans hozzáférésre van szükségük. Ha úgy döntött, hozzárendelheti a felhasználók és/vagy csoportok BlueJeans itt cikk utasításait követve:

*   [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Felhasználók hozzárendelése BlueJeans fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve BlueJeans konfigurálása kiosztás automatikus felhasználó teszteléséhez. További felhasználók és/vagy csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése BlueJeans, a hozzárendelés párbeszédpanelen válassza ki érvényes alkalmazás-specifikus szerepköröket (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem kiépítés tartoznak.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Felhasználólétesítés automatikus BlueJeans konfigurálása

Ez a szakasz végigvezeti az Azure AD szolgáltatás kiépítését, módosítása, és tiltsa le a felhasználók konfigurálásának lépéseit és/vagy az Azure AD felhasználói és/vagy a csoport-hozzárendelések alapján BlueJeans csoportok.

> [!TIP]
> Választhatja azt is, SAML-alapú egyszeri bejelentkezés engedélyezése a BlueJeans, utasítások találhatók a [BlueJeans egyszeri bejelentkezés az oktatóanyag](bluejeans-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül automatikus a felhasználók átadása, bár ez a két funkció egészítse ki egymást.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Konfigurálása automatikus felhasználókiépítése BlueJeans az Azure AD-ben:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) és keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás**.

2. Válassza ki a BlueJeans Szolgáltatottszoftver-alkalmazáshoz a listából.
 
    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/Bluejeans2.png)

3. Válassza ki a **kiépítési** fülre.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. Az a **rendszergazdai hitelesítő adataival** területen adjon meg a **rendszergazda felhasználóneve**, és **rendszergazdai jelszó** BlueJeans fiókja. Például a következő értékek a következők:

    *   Az a **rendszergazda felhasználóneve** mezőbe feltöltése a BlueJeans tenant a rendszergazdai fiók felhasználónevét. Példa: admin@contoso.com.

    *   Az a **rendszergazdai jelszó** mezőbe a jelszót a rendszergazdai felhasználónév megfelelő feltöltéséhez.

6. A program kitölti a mezőket az 5. lépés, után kattintson a **kapcsolat tesztelése** biztosításához az Azure AD BlueJeans kapcsolódhatnak. Ha nem sikerül, ellenőrizze a BlueJeans fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. Az a **értesítő e-mailt** mezőbe írja be az e-mail cím vagy egy csoportot kell az üzembe helyezési hiba értesítéseket, és jelölje be a jelölőnégyzetet - **e-mailben értesítést küld, ha hiba lép fel**.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Kattintson a **Save** (Mentés) gombra.

9. Az a **hozzárendelések** szakaszban jelölje be **szinkronizálása Azure Active Directory-felhasználókat BlueJeans**.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Tekintse át a felhasználói attribútumok, az Azure AD a BlueJeans lettek szinkronizálva a **attribútum leképezési** szakasz. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a BlueJeans a frissítési műveleteket. Válassza ki a **mentése** gombra a módosítások véglegesítéséhez.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Hatókörként szűrők konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](./../active-directory-saas-scoping-filters.md).

12. Az Azure AD szolgáltatás BlueJeans kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Válassza ki a kívánt értékeket a BlueJeans azokat a felhasználók és/vagy csoportok, amelyeket meg szeretne meghatározásához **hatókör** a a **beállítások** szakasz.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Ez a művelet elindítja a kezdeti szinkronizálás az összes felhasználót és/vagy csoportok meghatározott **hatókör** a a **beállítások** szakasz. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, körülbelül 40 percenként történik, amíg az Azure AD szolgáltatás kiépítését fut-e hosszabb időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenység jelentést, amely ismerteti a BlueJeans szolgáltatás kiépítését az Azure AD által végzett összes műveletet.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](../active-directory-saas-provisioning-reporting.md).

## <a name="connector-limitations"></a>Összekötő korlátozásai

* Bluejeans nem engedélyezi a felhasználónevek 30 karakternél hosszabb.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Ismerje meg, tekintse át a naplók és jelentések készítése a kiépítés tevékenység](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
