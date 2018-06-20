---
title: 'Oktatóanyag: ThousandEyes konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálja az Azure Active Directory automatikusan ellátásához, majd leépíti ThousandEyes felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.openlocfilehash: 71101ef119db719d281f3e6db4e0c3dee023c4d4
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210139"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés ThousandEyes konfigurálása


Ez az oktatóanyag célja a lépéseket kell elvégeznie a ThousandEyes és az Azure AD automatikus kiépítése és leépíti a felhasználói fiókok Azure ad-ThousandEyes mutatjuk be. 

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active directory-bérlő
*   Egy ThousandEyes bérlőt a [Standard csomag](https://www.thousandeyes.com/pricing) vagy jobban engedélyezve 
*   A ThousandEyes rendszergazdai jogosultságokkal rendelkező felhasználói fiókot 

> [!NOTE]
> Az Azure AD-integrációs kiépítés támaszkodik a [ThousandEyes SCIM API](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK), a Standard csomag ThousandEyes csoportokhoz érhető el vagy nagyobb.

## <a name="assigning-users-to-thousandeyes"></a>Felhasználók hozzárendelése ThousandEyes

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok "hozzárendelt" az Azure AD-alkalmazáshoz való szinkronizálása. 

A létesítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége döntse el, hogy mely felhasználók és/vagy az Azure AD-csoportok határoz meg a felhasználók, akik az ThousandEyes alkalmazásához való hozzáférést. Ha úgy döntött, itt cikk utasításait követve hozzárendelheti ezeket a felhasználókat az ThousandEyes alkalmazás:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Felhasználók hozzárendelése ThousandEyes fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve ThousandEyes teszteli a telepítési konfigurációt. További felhasználók és/vagy csoportok később is rendelhető.

*   Ha egy felhasználó hozzárendelése ThousandEyes, kell-e ki lehet a **felhasználói** szerepkör, vagy egy másik érvényes alkalmazásspecifikus szerepkör (ha elérhető) a hozzárendelés párbeszédpanelen. A **alapértelmezett hozzáférési** szerepkör nem működik a rendszerbe állításához, és ezek a felhasználók kimarad.


## <a name="configuring-user-provisioning-to-thousandeyes"></a>A felhasználók átadása a ThousandEyes konfigurálása 

Ez a szakasz végigvezeti az Azure AD kapcsolódás ThousandEyes a felhasználói fiók kiépítése API és a létesítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a felhasználók és csoportok hozzárendelése az Azure AD-alapú ThousandEyes hozzárendelt felhasználói fiókok.

> [!TIP]
> Dönthet úgy is, SAML-alapú egyszeri bejelentkezést ThousandEyes engedélyezni, utasítások megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.


### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Automatikus felhasználói fiók kiépítés ThousandEyes az Azure AD konfigurálása


1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

2. Ha már konfigurált ThousandEyes egyszeri bejelentkezést, keresse meg a keresési mező ThousandEyes példányát. Máskülönben válassza **Hozzáadás** keresse meg a **ThousandEyes** az alkalmazás katalógusában. Válassza ki a ThousandEyes a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3. Jelölje ki a ThousandEyes példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**.

    ![Kiépítés ThousandEyes](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. A a **rendszergazdai hitelesítő adataival** területen adjon meg a **titkos Token** állítja elő a ThousandEyes fiók (a jogkivonat az ThousandEyes fiókjában található: **biztonsági és hitelesítési**). 

    ![Kiépítés ThousandEyes](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. Az Azure portálon kattintson **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat az ThousandEyes alkalmazást. Ha nem sikerül, ellenőrizze a ThousandEyes fiókja rendszergazdai jogosultságokkal rendelkezik, és ismételje meg 5. lépés.

7. Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet a "E-mail értesítés küldése hiba esetén."

8. Kattintson a **Save** (Mentés) gombra. 

9. A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználókat ThousandEyes**.

10. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumok, az Azure AD ThousandEyes lettek szinkronizálva. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a ThousandEyes a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

11. Az Azure AD szolgáltatás ThousandEyes kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz

12. Kattintson a **Save** (Mentés) gombra. 

Ez a művelet bármilyen felhasználói és/vagy a felhasználók és csoportok szakaszban ThousandEyes rendelt csoportok a kezdeti szinkronizálás elindul. A kezdeti szinkronizálás végrehajtásához ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut-nál több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenységi naplóit, amelyek a létesítési szolgáltatás által végzett összes műveletet írják le.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](../active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Ismerje meg, tekintse át a naplók és jelentések készítése a kiépítés tevékenység](../active-directory-saas-provisioning-reporting.md)
