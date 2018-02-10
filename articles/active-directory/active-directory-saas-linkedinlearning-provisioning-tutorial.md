---
title: "Oktatóanyag: LinkedIn tanulási konfigurálása az automatikus felhasználó-átadási az Azure Active Directoryval |} Microsoft Docs"
description: "Konfigurálja az Azure Active Directory automatikus kiépítése és leépíti a felhasználói fiókok LinkedIn tanulási útmutató."
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
ms.openlocfilehash: f2ef582bd3a5ecd837dd053067cae6c1ea47b6f8
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-configure-linkedin-learning-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés LinkedIn tanulási konfigurálása


Ez az oktatóanyag célja a lépéseket kell elvégeznie a LinkedIn-tanulási és az Azure AD automatikus kiépítése és leépíti a felhasználói fiókok Azure ad-LinkedIn tanulási mutatjuk be. 

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active Directory-bérlő
*   A LinkedIn tanulási bérlő 
*   A LinkedIn Account Center hozzáféréssel rendelkező LinkedIn szeretnének rendszergazdai fiók

> [!NOTE]
> Az Azure Active Directory integrálható LinkedIn tanulás használatával az [SCIM](http://www.simplecloud.info/) protokoll.

## <a name="assigning-users-to-linkedin-learning"></a>Felhasználók hozzárendelése LinkedIn tanulás

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok "hozzárendelt" az Azure AD-alkalmazáshoz való szinkronizálásra kerül. 

A létesítési szolgáltatás engedélyezése és konfigurálása, előtt kell döntse el, hogy mely felhasználók és/vagy az Azure AD-csoportok határoz meg a felhasználók, akik LinkedIn tanulási elérésére. Ha úgy döntött, itt utasításokat követve hozzárendelheti ezek a felhasználók LinkedIn tanulási:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-learning"></a>Felhasználók hozzárendelése LinkedIn tanulás fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó hozzárendelni LinkedIn tanulási teszteli a telepítési konfigurációt. További felhasználók és/vagy csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése LinkedIn tanulási, ki kell választania a **felhasználói** szerepkör-hozzárendelés párbeszédpanelen. A "Default" szerepkör nem működik történő üzembe helyezéséhez.


## <a name="configuring-user-provisioning-to-linkedin-learning"></a>A felhasználók átadása a LinkedIn tanulási konfigurálása

Ez a szakasz végigvezeti az Azure AD kapcsolódás LinkedIn tanulási SCIM felhasználói fiók kiépítése API, és a LinkedIn tanulási alapján a felhasználó- és felhasználói fiókok létrehozásához, frissítéséhez és tiltsa le a létesítési szolgáltatás konfigurálása hozzárendelve az Azure AD-hozzárendelés.

> [!TIP]
> Dönthet úgy is, LinkedIn tanulási SAML-alapú egyszeri bejelentkezést engedélyezni, utasítások megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, abban az esetben, ha ez a két funkció egészítik ki egymást.


### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-learning-in-azure-ad"></a>Konfigurálása automatikus felhasználói fiók kiépítés LinkedIn Learning Azure AD-ben:


Az első lépés a LinkedIn-jogkivonatot lekérdezni. Ha a vállalati rendszergazdák, önálló megadhat egy hozzáférési jogkivonatot. A fiók központban navigáljon **beállítások &gt; globális beállítások** , és nyissa meg a **SCIM telepítő** panel.

> [!NOTE]
> Ha az account center érik el, hanem közvetlenül mutató hivatkozás, csatlakozni tud hozzá az alábbi lépéseket követve.

1)  Jelentkezzen be Center fiók.

2)  Válassza ki **Admin &gt; rendszergazdai beállítások** .

3)  Kattintson a **speciális integrációja** a bal oldali oldalsávon. Az account center van irányítva.

4)  Kattintson a **+ Hozzáadás új SCIM konfigurációs** és hajtsa végre az eljárást minden mező kitöltésével.

> Autoassign licencek nincs engedélyezve, az azt jelenti, hogy csak a felhasználói adatok van-e szinkronizálva.

![Kiépítés tanulási LinkedIn](./media/active-directory-saas-linkedinlearning-provisioning-tutorial/linkedin_1.PNG)

> Ha autolicense hozzárendelés engedélyezve van, jegyezze fel az alkalmazáspéldány és licenc típusa kell. Elsőként érkezett a hozzárendelt licenceket, először kiszolgálására alapján, addig, amíg a licencek kerül.

![Kiépítés tanulási LinkedIn](./media/active-directory-saas-linkedinlearning-provisioning-tutorial/linkedin_2.PNG)

5)  Kattintson a **Generate token**. A hozzáférési token megjelenítési alatt kell megjelennie a **hozzáférési jogkivonat** mező.

6)  Az oldal elhagyása előtt mentse a vágólapra vagy a számítógép a hozzáférési jogkivonat.

7) Ezután jelentkezzen be a [Azure-portálon](https://portal.azure.com), és keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

8) Ha már konfigurált LinkedIn tanulási egyszeri bejelentkezést, keresse meg a példányát használja a keresőmezőt LinkedIn tanulás. Máskülönben válassza **Hozzáadás** keresse meg a **LinkedIn tanulási** az alkalmazás katalógusában. Válassza ki a LinkedIn tanulási a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

9)  Jelölje ki a LinkedIn tanulási példányát, majd válassza ki a **kiépítési** fülre.

10) Állítsa be a **kiépítési üzemmódját** való **automatikus**.

![Kiépítés tanulási LinkedIn](./media/active-directory-saas-linkedinlearning-provisioning-tutorial/linkedin_3.PNG)

11)  Töltse ki a következő mezőket a **rendszergazdai hitelesítő adataival** :

* Az a **bérlői URL-cím** mezőbe írja be a https://api.linkedin.com.

* Az a **titkos Token** mezőben adja meg az 1. lépésben létrehozott jogkivonat, és kattintson a **kapcsolat tesztelése** .

* A portál upperright oldalán egy sikeres következő értesítésnek kell megjelennie.

12) Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be az alábbi jelölőnégyzetet.

13) Kattintson a **Save** (Mentés) gombra. 

14) Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói és csoportattribútum, amely szinkronizálva lesznek az Azure AD az LinkedIn tanulás. Vegye figyelembe, hogy az attribútumok választotta **egyező** tulajdonságok felel meg a felhasználói fiókokat és csoportokat LinkedIn szeretnének frissítés műveletekhez használható. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

![Kiépítés tanulási LinkedIn](./media/active-directory-saas-linkedinlearning-provisioning-tutorial/linkedin_4.PNG)

15) Az Azure AD LinkedIn Learning szolgáltatáshoz kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz

16) Kattintson a **Save** (Mentés) gombra. 

Elindítja a kezdeti szinkronizálás bármely felhasználói és/vagy a felhasználók és csoportok szakaszban LinkedIn tanulási rendelt csoportok. Figyelje meg, hogy a kezdeti szinkronizálás hosszabb ideig tart hajtsa végre ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut-nál. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenységi naplóit, amely a LinkedIn tanulási app a létesítési szolgáltatás által végzett összes műveletet írják le.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-enterprise-apps-manage-provisioning.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
