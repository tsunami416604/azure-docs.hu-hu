---
title: "Oktatóanyag: Samanage konfigurálása az automatikus felhasználó-átadási az Azure Active Directoryval |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálja az Azure Active Directory automatikusan ellátásához, majd leépíti Samanage felhasználói fiókokat."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: asmalser-msft
ms.openlocfilehash: 3f3b2b01d909b5c1d9373a33644e661723178dee
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="tutorial-configuring-samanage-for-automatic-user-provisioning"></a>Oktatóanyag: Samanage konfigurálása az automatikus felhasználó létesítése


Ez az oktatóanyag célja a lépéseket kell elvégeznie a Samanage és az Azure AD automatikus kiépítése és leépíti a felhasználói fiókok Azure ad-Samanage mutatjuk be. 

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active directory-bérlő
*   Egy Samanage bérlőt a [szakmai terv](https://www.samanage.com/pricing/) vagy jobban engedélyezve 
*   A Samanage rendszergazdai jogosultságokkal rendelkező felhasználói fiókot 

> [!NOTE]
> Az Azure AD-integrációs kiépítés támaszkodik a [Samanage REST API](https://www.samanage.com/api/), a Professional terven Samanage csoportokhoz érhető el vagy nagyobb.

## <a name="assigning-users-to-samanage"></a>Felhasználók hozzárendelése Samanage

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok "hozzárendelt" az Azure AD-alkalmazáshoz való szinkronizálása. 

A létesítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége döntse el, hogy mely felhasználók és/vagy az Azure AD-csoportok határoz meg a felhasználók, akik az Samanage alkalmazásához való hozzáférést. Ha úgy döntött, itt cikk utasításait követve hozzárendelheti ezeket a felhasználókat az Samanage alkalmazás:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-samanage"></a>Felhasználók hozzárendelése Samanage fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Samanage teszteli a telepítési konfigurációt. További felhasználók és/vagy csoportok később is rendelhető.

*   Ha egy felhasználó hozzárendelése Samanage, kell-e ki lehet a **felhasználói** szerepkör, vagy egy másik érvényes alkalmazásspecifikus szerepkör (ha elérhető) a hozzárendelés párbeszédpanelen. A **alapértelmezett hozzáférési** szerepkör nem működik a rendszerbe állításához, és ezek a felhasználók kimarad.

> [!NOTE]
> Hozzáadott szolgáltatás a létesítési szolgáltatás beolvassa a Samanage definiált egyéni szerepköröket, és importálja azokat az Azure AD, ha azok választható ki a szerepkör kiválasztása párbeszédpanel. Ezek a szerepkörök lesz látható az Azure portálon, a létesítési szolgáltatás engedélyezve van, és egy szinkronizálási ciklust befejeződése után.

## <a name="configuring-user-provisioning-to-samanage"></a>A felhasználók átadása a Samanage konfigurálása 

Ez a szakasz végigvezeti az Azure AD kapcsolódás Samanage a felhasználói fiók kiépítése API és a létesítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a felhasználók és csoportok hozzárendelése az Azure AD-alapú Samanage hozzárendelt felhasználói fiókok.

> [!TIP]
> Dönthet úgy is, SAML-alapú egyszeri bejelentkezést Samanage engedélyezni, utasítások megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.


### <a name="configure-automatic-user-account-provisioning-to-samanage-in-azure-ad"></a>Konfigurálja az automatikus felhasználói fiók kiépítése Samanage az Azure AD-ben:


1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

2. Ha már konfigurált Samanage egyszeri bejelentkezést, keresse meg a keresési mező Samanage példányát. Máskülönben válassza **Hozzáadás** keresse meg a **Samanage** az alkalmazás katalógusában. Válassza ki a Samanage a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3. Jelölje ki a Samanage példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**.

    ![Samanage kiépítése](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage1.png)

5. Az a **rendszergazdai hitelesítő adataival** területen adjon meg a **rendszergazda felhasználónevét és a rendszergazdai jelszó** a Samanage fiók. 

6. Az Azure portálon kattintson **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat az Samanage alkalmazást. Ha nem sikerül, ellenőrizze a Samanage fiókja rendszergazdai jogosultságokkal rendelkezik, és ismételje meg 5. lépés.

7. Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet a "E-mail értesítés küldése hiba esetén."

8. Kattintson a **Save** (Mentés) gombra. 

9. A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználókat Samanage**.

10. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumok, az Azure AD Samanage lettek szinkronizálva. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a Samanage a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

11. Az Azure AD szolgáltatás Samanage kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz

12. Kattintson a **Save** (Mentés) gombra. 

Ez a művelet bármilyen felhasználói és/vagy a felhasználók és csoportok szakaszban Samanage rendelt csoportok a kezdeti szinkronizálás elindul. A kezdeti szinkronizálás végrehajtásához ezt követő szinkronizálások, amely körülbelül 20 percenként történik, amíg a szolgáltatás fut-nál több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához Tevékenységjelentések, amelyek a létesítési szolgáltatás által végzett összes műveletet írják le.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).


## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-enterprise-apps-manage-provisioning.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Következő lépések

* [Ismerje meg, tekintse át a naplók és jelentések készítése a kiépítés tevékenység](active-directory-saas-provisioning-reporting.md)
