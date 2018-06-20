---
title: 'Oktatóanyag: Asana konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálja az Azure Active Directory automatikusan ellátásához, majd leépíti Asana felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 2abd10f90d2e6d86d7f82aa43016a31bfdbd324b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231423"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés Asana konfigurálása

Ez az oktatóanyag célja a lépéseket kell elvégeznie a Asana és az Azure Active Directory (Azure AD) automatikusan ellátásához, majd leépíti a felhasználói fiókok Azure ad-Asana mutatjuk be.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure AD-bérlő
*   Az Asana bérlő a egy [vállalati](https://www.asana.com/pricing) a terv és jobban engedélyezve 
*   A Asana rendszergazdai jogosultságokkal rendelkező felhasználói fiókot 

> [!NOTE] 
> Az Azure AD-integrációs kiépítés támaszkodik a [Asana API](https://app.asana.com/api/1.0/scim/Users), amely Asana rendelkezésére áll.

## <a name="assign-users-to-asana"></a>Felhasználók hozzárendelése Asana

Az Azure AD egy fogalom, más néven "hozzárendeléseket" alapján határozza meg, mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a kérelmet az Azure ad-ben rendelt felhasználók szinkronizálása. 

Konfigurálását megelőzően, illetve a létesítési szolgáltatás engedélyezése, el kell döntenie, mely felhasználóknak az Azure AD hozzá kell férniük a Asana alkalmazásba. Ezután ezek a felhasználók itt utasításokat követve hozzárendelheti a Asana alkalmazás:

[Felhasználó hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Felhasználók hozzárendelése Asana fontos tippek

Azt javasoljuk, hogy egyetlen rendelje Asana a kiépítési konfigurációjának tesztelése az Azure AD-felhasználó. További felhasználók később is hozzárendelhető.

## <a name="configure-user-provisioning-to-asana"></a>A felhasználók átadása a Asana konfigurálása 

Ez a szakasz végigvezeti az Azure AD API kiépítés Asana felhasználói fiókhoz való kapcsolódás. Az üzembe helyezési szolgáltatást létrehozása, frissítése és tiltsa le a hozzárendelt felhasználói fiókok Azure AD-ben a felhasználó-hozzárendeléseket alapján Asana is konfigurálja.

> [!TIP]
> SAML-alapú egyszeri bejelentkezésre vonatkozó Asana engedélyezéséhez kövesse az utasításokat a [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítik ki egymást.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Automatikus felhasználói fiók kiépítés Asana az Azure AD konfigurálása

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory** > **vállalati alkalmazások** > **összes alkalmazás** szakasz.

2. Ha már konfigurált az egyszeri bejelentkezés Asana, keresni Asana példányát használja a keresőmezőt. Máskülönben válassza **Hozzáadás** keresse meg a **Asana** az alkalmazás katalógusában. Válassza ki **Asana** a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3. Jelölje ki a Asana példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be **kiépítési üzemmódját** való **automatikus**.

    ![Asana kiépítése](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

5. Az a **rendszergazdai hitelesítő adataival** területen hozza létre a jogkivonatot, és írja be a következő lépések követésével **titkos Token**:

    a. Jelentkezzen be [Asana](https://app.asana.com) a rendszergazdai fiók használatával.

    b. Válassza ki a felső sávon a profilképet, és válassza ki a jelenlegi szervezetnév beállításokat.

    c. Lépjen a **szolgáltatásfiókok** fülre.

    d. Válassza ki **szolgáltatásfiók hozzáadása**.

    e. Frissítés **neve** és **kapcsolatos** és a profilképet, igény szerint. Másolja a lexikális elem szerepel az **Token**, és válassza ki azt a **módosítások mentése**.

6. Válassza ki az Azure-portálon **kapcsolat tesztelése** annak érdekében, hogy az Azure AD az Asana alkalmazáshoz csatlakozhat. Ha nem sikerül, győződjön meg arról, hogy a Asana fiókja rendelkezik-e rendszergazdai jogosultságokkal, és próbálja meg a **kapcsolat tesztelése** léptessen ismét.

7. Adja meg az e-mail cím vagy egy csoportot, amelybe az üzembe helyezési hiba értesítéseket **értesítő e-mailt**. Jelölje be a jelölőnégyzetet alatt.

8. Kattintson a **Mentés** gombra. 

9. Az a **hozzárendelések** szakaszban jelölje be **szinkronizálása Azure Active Directory-felhasználókat Asana**.

10. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumok Asana az Azure ad-szinkronizálandó. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a Asana a frissítési műveleteket. Válassza ki **mentése** véglegesíteni a módosításokat. További információkért lásd: [testreszabása felhasználói rendelkezés attribútum-leképezésekhez](../active-directory-saas-customizing-attribute-mappings.md).

11. Ahhoz, hogy az Azure AD a Asana, a szolgáltatás kiépítését a **beállítások** területen módosítsa **kiépítési állapot** való **a**.

12. Kattintson a **Mentés** gombra. 

Most a kezdeti szinkronizálás indítása a Asana rendelt bármely felhasználó számára a **felhasználók** szakasz. A kezdeti szinkronizálás végrehajtásához ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut-nál több időt vesz igénybe. Használja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve a tevékenységi naplóit kiépítésére. A felügyeleti naplók az Asana alkalmazás a létesítési szolgáltatás által végzett összes műveletet írják le.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentést](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](asana-tutorial.md)
