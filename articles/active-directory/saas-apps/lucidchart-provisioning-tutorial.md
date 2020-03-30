---
title: 'Oktatóanyag: Felhasználói kiépítés a LucidChart programhoz – Azure AD'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok lucidchart-ba való automatikus kiépítésére és kiépítésének kiépítésével.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5d946c6e257c7676178f9bc3c234f66ba6fe622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057328"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Oktatóanyag: A LucidChart konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy megmutassa a LucidChart és az Azure AD által végrehajtandó lépéseket a felhasználói fiókok automatikus kiépítéséhez és a felhasználói fiókok azure AD-ről lucidchart-ra történő automatikus kiépítéséhez és kiépítésének kiteljesítéséhez. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* Egy Azure Active Directory-bérlő
* LucidChart-bérlő a [vállalati csomaggal](https://www.lucidchart.com/user/117598685#/subscriptionLevel) vagy a jobban engedélyezett
* Rendszergazdai engedélyekkel rendelkező felhasználói fiók a LucidChart-ban

## <a name="assigning-users-to-lucidchart"></a>Felhasználók hozzárendelése a LucidChart-hoz

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói fiók kiépítése, csak a felhasználók és csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálva van.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-ben mely felhasználók és/vagy csoportok képviselik azokat a felhasználókat, akiknek hozzáférésre van szükségük a LucidChart alkalmazáshoz. Miután eldöntötte, ezeket a felhasználókat hozzárendelheti a LucidChart alkalmazáshoz az alábbi utasításokat követve:

[Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Fontos tippek a felhasználók LucidChart-hoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve LucidChart a létesítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a LucidChart-hoz rendel, a hozzárendelés párbeszédpanelen ki kell **választania** a Felhasználói szerepkört vagy egy másik érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkör nem működik a kiépítéshez, és ezek a felhasználók kimaradnak.

## <a name="configuring-user-provisioning-to-lucidchart"></a>A felhasználó lucidchart-ra való kiépítésének konfigurálása

Ez a szakasz végigvezeti az Azure AD-nek a LucidChart felhasználói fiók létesítési API-jával való csatlakoztatásával, valamint a kiépítési szolgáltatás konfigurálásával a hozzárendelt felhasználói fiókok létrehozásához, frissítéséhez és letiltásához a LucidChart-ban az Azure AD-ben a felhasználó- és csoporthozzárendelés alapján.

> [!TIP]
> Az [Azure Portalon](https://portal.azure.com)található utasításokat követve engedélyezheti az SAML-alapú egyszeri bejelentkezést a LucidChart számára. Egyszeri bejelentkezés konfigurálható az automatikus kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Automatikus felhasználói fiók-kiépítés konfigurálása lucidchart-ra az Azure AD-ben

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Active Directory > Vállalati alkalmazások > az összes alkalmazás** szakaszt.

2. Ha már konfigurálta a LucidChart-ot egyszeri bejelentkezéshez, keresse meg a LucidChart példányát a keresőmező használatával. Ellenkező esetben válassza a **LucidChart** **hozzáadása** és keresése lehetőséget az alkalmazásgyűjteményben. Válassza a LucidChart elemet a keresési eredmények között, és adja hozzá az alkalmazások listájához.

3. Válassza ki a LucidChart példányát, majd válassza a **Kiépítés** lapot.

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![LucidChart kiépítés](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. A **Rendszergazdai hitelesítő adatok** szakaszban adja meg a LucidChart fiókja által létrehozott **titkos jogkivonatot** (a token a fiókjában található: **Team** > **App Integration** > **SCIM**).

    ![LucidChart kiépítés](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. Az Azure Portalon kattintson a **Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a LucidChart alkalmazáshoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy lucidchart fiókja rendelkezik rendszergazdai engedélyekkel, és próbálkozzon újra az 5.

7. Írja be annak a személynek vagy csoportnak az e-mail címét, akinek kiépítési hibaértesítéseket kell kapnia az **Értesítési e-mail** mezőben, és jelölje be a "E-mail értesítés küldése hiba esetén" jelölőnégyzetet.

8. Kattintson a **Mentés** gombra.

9. A Leképezések csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása lucidchart-tal**lehetőséget.

10. Az **Attribútum-leképezések** szakaszban tekintse át az Azure AD és lucidchart között szinkronizált felhasználói attribútumokat. Az **Egyező** tulajdonságokként kijelölt attribútumok a LucidChart felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez kattintson a Mentés gombra.

11. Az Azure AD-kiépítési szolgáltatás lucidchart engedélyezéséhez módosítsa a **Kiépítés állapotát** **be beállításra** a **Beállítások** szakaszban

12. Kattintson a **Mentés** gombra.

Ez a művelet elindítja a LucidChart-hoz rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását a Felhasználók és csoportok szakaszban. A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg a szolgáltatás fut. A Szinkronizálás **részletei** szakasz ban figyelheti az előrehaladást, és követheti a kiépítési tevékenységnaplókra mutató hivatkozásokat, amelyek a kiépítési szolgáltatás által végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
