---
title: 'Oktatóanyag: Pingboard konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálja az Azure Active Directory automatikusan ellátásához, majd leépíti Pingboard felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 0c135af0341e5f4644961b5859574b334048543e
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35927557"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés Pingboard konfigurálása

Ez az oktatóanyag célja a lépéseket kell elvégeznie ahhoz, hogy az automatikus üzembe helyezést és megszüntetést felhasználói fiókok az Azure Active Directory (Azure AD) a Pingboard mutatjuk be.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure AD-bérlő
*   A Pingboard bérlő [Pro-fiók](https://pingboard.com/pricing) 
*   A Pingboard rendszergazdai jogosultságokkal rendelkező felhasználói fiókot 

> [!NOTE] 
> Az Azure AD-integrációs kiépítés támaszkodik a [Pingboard API](`https://your_domain.pingboard.com/scim/v2`), amely érhető el a fiókjához.

## <a name="assign-users-to-pingboard"></a>Felhasználók hozzárendelése Pingboard

Az Azure AD egy fogalom, más néven "hozzárendeléseket" alapján határozza meg, mely felhasználóknak kell kapniuk az alkalmazások elérésére. Automatikus fiók felhasználókiépítése keretében csak a kérelmet az Azure ad-ben rendelt felhasználók szinkronizálása. 

Konfigurálását megelőzően, illetve a létesítési szolgáltatás engedélyezése, el kell döntenie, mely felhasználóknak az Azure AD hozzá kell férniük a Pingboard alkalmazásba. Ezután ezek a felhasználók itt utasításokat követve hozzárendelheti a Pingboard alkalmazás:

[Felhasználó hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Felhasználók hozzárendelése Pingboard fontos tippek

Azt javasoljuk, hogy egyetlen rendelje Pingboard a kiépítési konfigurációjának tesztelése az Azure AD-felhasználó. További felhasználók később is hozzárendelhető.

## <a name="configure-user-provisioning-to-pingboard"></a>A felhasználók átadása a Pingboard konfigurálása 

Ez a szakasz végigvezeti az Azure AD a kiépítés API Pingboard felhasználói fiókhoz való kapcsolódás. Az üzembe helyezési szolgáltatást létrehozása, frissítése és tiltsa le a hozzárendelt felhasználói fiókok Azure AD-ben a felhasználó-hozzárendeléseket alapján Pingboard is konfigurálja.

> [!TIP]
> SAML-alapú egyszeri bejelentkezésre vonatkozó Pingboard engedélyezéséhez kövesse az utasításokat a [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítik ki egymást.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Automatikus felhasználói fiók kiépítés Pingboard az Azure AD konfigurálása

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory** > **vállalati alkalmazások** > **összes alkalmazás** szakasz.

2. Ha már konfigurált az egyszeri bejelentkezés Pingboard, keresni Pingboard példányát használja a keresőmezőt. Máskülönben válassza **Hozzáadás** keresse meg a **Pingboard** az alkalmazás katalógusában. Válassza ki **Pingboard** a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3. Jelölje ki a Pingboard példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be **kiépítési üzemmódját** való **automatikus**.

    ![Pingboard kiépítése](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)
    
5. Az a **rendszergazdai hitelesítő adataival** területen tegye a következőket:

    a. A **bérlői URL-cím**, adja meg `https://your_domain.pingboard.com/scim/v2`, és a "adott_tartomány" cserélje le a valós tartomány.

    b. Jelentkezzen be [Pingboard](https://pingboard.com/) a rendszergazdai fiók használatával.

    c. Válassza ki **bővítmények** > **integrációja** > **az Azure Active Directory**.

    d. Lépjen a **konfigurálása** lapot, és jelölje be **engedélyezése a felhasználók átadása az Azure-ból**.

    e. Másolja a lexikális elem szerepel az **OAuth tulajdonosi jogkivonat**, és adja meg a **titkos Token**.

6. Válassza ki az Azure-portálon **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat az Pingboard alkalmazást. Ha nem sikerül, győződjön meg arról, hogy a Pingboard fiókja rendelkezik-e rendszergazdai jogosultságokkal, és próbálja meg a **kapcsolat tesztelése** léptessen ismét.

7. Adja meg az e-mail cím vagy egy csoportot, amelybe az üzembe helyezési hiba értesítéseket **értesítő e-mailt**. Jelölje be a jelölőnégyzetet alatt.

8. Kattintson a **Mentés** gombra. 

9. Az a **hozzárendelések** szakaszban jelölje be **szinkronizálása Azure Active Directory-felhasználókat Pingboard**.

10. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumok Pingboard az Azure ad-szinkronizálandó. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a Pingboard a frissítési műveleteket. Válassza ki **mentése** véglegesíteni a módosításokat. További információkért lásd: [testreszabása felhasználói attribútum-leképezésekhez kiépítés](../active-directory-saas-customizing-attribute-mappings.md).

11. Ahhoz, hogy az Azure AD a Pingboard, a szolgáltatás kiépítését a **beállítások** területen módosítsa **kiépítési állapot** való **a**.

12. Válassza ki **mentése** Pingboard rendelt felhasználók a kezdeti szinkronizálást.

A kezdeti szinkronizálás végrehajtásához ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut-nál több időt vesz igénybe. Használja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve a tevékenységi naplóit kiépítésére. A naplók az Pingboard alkalmazás a létesítési szolgáltatás által végzett összes műveletet írják le.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentést](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](pingboard-tutorial.md)
