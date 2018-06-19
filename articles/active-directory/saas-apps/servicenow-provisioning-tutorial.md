---
title: 'Oktatóanyag: ServiceNow konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs'
description: Megtudhatja, hogyan automatikusan ellátásához, majd leépíti a felhasználói fiókok Azure ad-ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 7f51367b13e6ced626a766cf46e0ed4d033813a6
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35925981"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Oktatóanyag: ServiceNow konfigurálása az automatikus felhasználó-átadási az Azure Active Directoryval

Ez az oktatóanyag célja a lépéseket kell elvégeznie a ServiceNow és az Azure AD automatikus kiépítése és leépíti a felhasználói fiókok Azure ad-ServiceNow mutatjuk be.

> [!NOTE]
> Ez az oktatóanyag leírja egy összekötőt, az Azure AD-felhasználó kiépítési Service platformra épül. Ez a szolgáltatás funkciója, hogyan működik, és gyakran ismételt kérdések fontos tudnivalókat tartalmaz [felhasználói kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz az Azure Active Directoryval történő automatizálásához](./../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a ServiceNow, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A ServiceNow, egy példány vagy bérlő ServiceNow, Calgary verzió vagy újabb
- A ServiceNow expressz, ServiceNow kifejezett, Helsinki verzió példányának vagy újabb

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).


## <a name="assigning-users-to-servicenow"></a>A ServiceNow felhasználók hozzárendelése

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok "hozzárendelt" az Azure AD-alkalmazáshoz való szinkronizálása.

A létesítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége döntse el, hogy mely felhasználók és/vagy az Azure AD-csoportok határoz meg a felhasználók, akik a ServiceNow alkalmazásához való hozzáférést. Ha úgy döntött, rendelhet ezeket a felhasználókat a ServiceNow app utasítások itt: [egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a ServiceNow teszteli a telepítési konfigurációt. További felhasználók és/vagy csoportok később is rendelhető.
>*   Amikor egy felhasználó rendel a ServiceNow, ki kell választania egy érvényes felhasználói szerepkörnek. A "Default" szerepkör nem működik történő üzembe helyezéséhez.

## <a name="enable-automated-user-provisioning"></a>Az automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD kapcsolódás ServiceNow a felhasználói fiók kiépítése API és a létesítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a hozzárendelt felhasználói fiókok a ServiceNow alapján a felhasználók és csoportok hozzárendelése az Azure AD.

> [!TIP]
>Dönthet úgy is, engedélyezze SAML-alapú egyszeri bejelentkezést a ServiceNow, utasítások megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurálja az automatikus felhasználói fiók kiépítése

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

2. Ha már konfigurált ServiceNow egyszeri bejelentkezést, keresse meg a példányát használja a keresőmezőt ServiceNow. Máskülönben válassza **Hozzáadás** keresse meg a **ServiceNow** az alkalmazás katalógusában. Válassza ki a ServiceNow a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3. Jelölje ki a ServiceNow példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **kiépítés** módot **automatikus**. 

    ![kiépítés folyamatban](./media/servicenow-provisioning-tutorial/provisioning.png)

5. A rendszergazdai hitelesítő adataival szakaszban a következő lépésekkel:
   
    a. Az a **ServiceNow példánynév** szövegmező, írja be a ServiceNow-példány nevét.

    b. Az a **ServiceNow rendszergazda felhasználóneve** szövegmező, írja be a felhasználónevet, a rendszergazda.

    c. Az a **ServiceNow rendszergazdai jelszó** szövegmező, a rendszergazdai jelszót.

6. Az Azure portálon kattintson **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat a ServiceNow alkalmazást. Ha nem sikerül, gondoskodjon a ServiceNow-fiók Team rendszergazdai jogosultságokkal, majd próbálkozzon a **"Rendszergazdai hitelesítő adatok"** léptessen ismét.

7. Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet.

8. Kattintson a **mentéséhez.**

9. A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználók számára a ServiceNow.**

10. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumokat a ServiceNow szinkronizált Azure AD-ből. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a ServiceNow a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

11. A ServiceNow szolgáltatás kiépítését az Azure AD engedélyezéséhez módosítsa a **kiépítési állapot** való **a** beállításai szakaszában

12. Kattintson a **mentéséhez.**

A kezdeti szinkronizálás bármely felhasználói és/vagy a felhasználók és csoportok szakaszban ServiceNow rendelt csoportok kezdődik. A kezdeti szinkronizálás végrehajtásához ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut-nál több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenységi naplóit, amely a ServiceNow app a létesítési szolgáltatás által végzett összes műveletet írják le.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](servicenow-tutorial.md)


