---
title: 'Oktatóanyag: LucidChart konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálja az Azure Active Directory automatikusan ellátásához, majd leépíti LucidChart felhasználói fiókokat.
services: active-directory
documentationcenter: ''
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
ms.openlocfilehash: c25e56d86be65de0394678db2f71dad31eabbc5a
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35927683"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés LucidChart konfigurálása


Ez az oktatóanyag célja a lépéseket kell elvégeznie a LucidChart és az Azure AD automatikus kiépítése és leépíti a felhasználói fiókok Azure ad-LucidChart mutatjuk be. 

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active directory-bérlő
*   Egy LucidChart bérlőt a [vállalati terv](https://www.lucidchart.com/user/117598685#/subscriptionLevel) vagy jobban engedélyezve 
*   A LucidChart rendszergazdai jogosultságokkal rendelkező felhasználói fiókot 

## <a name="assigning-users-to-lucidchart"></a>Felhasználók hozzárendelése LucidChart

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok "hozzárendelt" az Azure AD-alkalmazáshoz való szinkronizálása. 

A létesítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége döntse el, hogy mely felhasználók és/vagy az Azure AD-csoportok határoz meg a felhasználók, akik az LucidChart alkalmazásához való hozzáférést. Ha úgy döntött, itt cikk utasításait követve hozzárendelheti ezeket a felhasználókat az LucidChart alkalmazás:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Felhasználók hozzárendelése LucidChart fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve LucidChart teszteli a telepítési konfigurációt. További felhasználók és/vagy csoportok később is rendelhető.

*   Ha egy felhasználó hozzárendelése LucidChart, kell-e ki lehet a **felhasználói** szerepkör, vagy egy másik érvényes alkalmazásspecifikus szerepkör (ha elérhető) a hozzárendelés párbeszédpanelen. A **alapértelmezett hozzáférési** szerepkör nem működik a rendszerbe állításához, és ezek a felhasználók kimarad.


## <a name="configuring-user-provisioning-to-lucidchart"></a>A felhasználók átadása a LucidChart konfigurálása 

Ez a szakasz végigvezeti az Azure AD kapcsolódás LucidChart a felhasználói fiók kiépítése API és a létesítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a felhasználók és csoportok hozzárendelése az Azure AD-alapú LucidChart hozzárendelt felhasználói fiókok.

> [!TIP]
> Dönthet úgy is, SAML-alapú egyszeri bejelentkezést LucidChart engedélyezni, utasítások megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.


### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Automatikus felhasználói fiók kiépítés LucidChart az Azure AD konfigurálása


1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

2. Ha már konfigurált LucidChart egyszeri bejelentkezést, keresse meg a keresési mező LucidChart példányát. Máskülönben válassza **Hozzáadás** keresse meg a **LucidChart** az alkalmazás katalógusában. Válassza ki a LucidChart a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3. Jelölje ki a LucidChart példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**.

    ![LucidChart kiépítése](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. Alatt a **rendszergazdai hitelesítő adatokat** területen adjon meg a **jogkivonat titkos kulcs** állítja elő a LucidChart fiók (a token található a fiókjában: **Team**  >  **Alkalmazásintegráció** > **SCIM**). 

    ![LucidChart kiépítése](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. Az Azure portálon kattintson **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat az LucidChart alkalmazást. Ha nem sikerül, ellenőrizze a LucidChart fiókja rendszergazdai jogosultságokkal rendelkezik, és ismételje meg 5. lépés.

7. Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet a "E-mail értesítés küldése hiba esetén."

8. Kattintson a **Save** (Mentés) gombra. 

9. A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználókat LucidChart**.

10. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumok, az Azure AD LucidChart lettek szinkronizálva. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a LucidChart a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

11. Az Azure AD szolgáltatás LucidChart kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz

12. Kattintson a **Save** (Mentés) gombra. 

Ez a művelet bármilyen felhasználói és/vagy a felhasználók és csoportok szakaszban LucidChart rendelt csoportok a kezdeti szinkronizálás elindul. A kezdeti szinkronizálás végrehajtásához ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut-nál több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenységi naplóit, amelyek a létesítési szolgáltatás által végzett összes műveletet írják le.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](../active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Ismerje meg, tekintse át a naplók és jelentések készítése a kiépítés tevékenység](../active-directory-saas-provisioning-reporting.md)
