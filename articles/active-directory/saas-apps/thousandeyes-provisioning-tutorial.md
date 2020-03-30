---
title: 'Oktatóanyag: A ThousandEyes felhasználói kiépítése – Azure AD'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a ThousandEyes felhasználói fiókok automatikus kiépítésére és kiépítésének kiépítésének kiépítésével.
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
ms.date: 03/28/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: d87cffce636146eac3e557670ffc4fb2fc34ae38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062880"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Oktatóanyag: A ThousandEyes konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy megmutassa a ThousandEyes és az Azure AD által végrehajtandó lépéseket a felhasználói fiókok automatikus kiépítéséhez és a felhasználói fiókok azure-ad-ról ThousandEyes-ra történő automatikus kiépítéséhez és kiépítésének kiteljesítéséhez. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* Egy Azure Active Directory-bérlő
* A ThousandEyes bérlő a [Standard terv](https://www.thousandeyes.com/pricing) vagy jobban engedélyezve 
* A ThousandEyes rendszergazdai engedélyekkel rendelkező felhasználói fiókja 

> [!NOTE]
> Az Azure AD-kiépítési integráció a [ThousandEyes SCIM](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK)API-t, amely elérhető a ThousandEyes csapatok a standard csomag vagy annál jobb.

## <a name="assigning-users-to-thousandeyes"></a>Felhasználók hozzárendelése a ThousandEyes-hoz

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói fiók kiépítése, csak a felhasználók és csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálva van. 

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-ben mely felhasználók és/vagy csoportok képviselik azon felhasználókat, akiknek hozzáférésre van szükségük a ThousandEyes alkalmazáshoz. Miután úgy döntött, akkor hozzá ezeket a felhasználókat a ThousandEyes app az alábbi utasításokat itt:

[Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Fontos tippek a felhasználók ThousandEyes-hoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van hozzárendelve ThousandEyes a létesítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a ThousandEyes-hoz rendel, ki kell **választania** a Felhasználói szerepkört, vagy egy másik érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkör nem működik a kiépítéshez, és ezek a felhasználók kimaradnak.

## <a name="configuring-user-provisioning-to-thousandeyes"></a>Felhasználói kiépítés konfigurálása a ThousandEyes rendszerbe 

Ez a szakasz végigvezeti az Azure AD-nek a ThousandEyes felhasználói fiók létesítési API-jával való csatlakoztatásán, valamint a hozzárendelt felhasználói fiókok létrehozásához, frissítéséhez és letiltásához az Azure-fiókok hozásához, frissítéséhez és letiltásához az Azure AD-ben a felhasználó- és csoporthozzárendelés alapján. .

> [!TIP]
> Az [Azure Portalon](https://portal.azure.com)található utasításokat követve engedélyezheti az SAML-alapú egyszeri bejelentkezést a ThousandEyes számára. Egyszeri bejelentkezés konfigurálható az automatikus kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>A Felhasználói fiókok automatikus kiépítésének konfigurálása a ThousandEyes szolgáltatásba az Azure AD-ben

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Active Directory > Vállalati alkalmazások > az összes alkalmazás** szakaszt.

2. Ha már beállította ThousandEyes egyszeri bejelentkezés, keresse meg a példány thousandEyes a keresési mezőben. Ellenkező esetben válassza **a Hozzáadás** és keresés **thousandEyes** az alkalmazás galériában. Válassza ki ThousandEyes a keresési eredmények között, és add hozzá a listát az alkalmazások.

3. Válassza ki a ThousandEyes példányát, majd válassza **a Kiépítés** lapot.

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![ThousandEyes kiépítés](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. Az **Admin Hitelesítő adatok** szakaszban adja meg a ThousandEyes fiókja által létrehozott **OAuth Bearer Token-t** (megtalálhatja és vagy létrehozhat egy tokent a ThousandEyes **fiókprofil** szakaszában).

    ![ThousandEyes kiépítés](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. Az Azure Portalon kattintson a **Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a ThousandEyes alkalmazáshoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a ThousandEyes fiók rendszergazdai engedélyekkel rendelkezik, és próbálkozzon újra az 5.

7. Írja be annak a személynek vagy csoportnak az e-mail címét, akinek kiépítési hibaértesítéseket kell kapnia az **Értesítési e-mail** mezőben, és jelölje be a "E-mail értesítés küldése hiba esetén" jelölőnégyzetet.

8. Kattintson a **Mentés** gombra.

9. A Leképezések csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása a ThousandEyes szolgáltatással**lehetőséget.

10. Az **Attribútum-leképezések** szakaszban tekintse át az Azure AD és ThousandEyes között szinkronizált felhasználói attribútumokat. Az **egyező** tulajdonságokként kiválasztott attribútumok a ThousandEyes felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez kattintson a Mentés gombra.

11. Az Azure AD-létesítési szolgáltatás engedélyezéséhez thousandEyes, módosítsa a **kiépítési állapot** **be van kapcsolva** a **Beállítások** szakaszban

12. Kattintson a **Mentés** gombra.

Ez a művelet elindítja a felhasználók és/vagy csoportok hozrendelt ThousandEyes a felhasználók és csoportok szakaszban a kezdeti szinkronizálást. A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg a szolgáltatás fut. A Szinkronizálás **részletei** szakasz ban figyelheti az előrehaladást, és követheti a kiépítési tevékenységnaplókra mutató hivatkozásokat, amelyek a kiépítési szolgáltatás által végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
