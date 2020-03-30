---
title: 'Oktatóanyag: A Velpic konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a Felhasználói fiókok velpic-be történő automatikus kiépítésére és kiépítésének kiépítésével.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b7a6c2c9b7ecb0b160f7481d95f7682f3f7a109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064121"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Oktatóanyag: Velpic konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy megmutassa a Velpic és az Azure AD által végrehajtandó lépéseket a felhasználói fiókok automatikus kiépítéséhez és a felhasználói fiókok azure-ad-ról Velpic-re történő automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* Egy Azure Active Directory-bérlő
* Velpic bérlő az [Enterprise csomaggal](https://www.velpic.com/pricing.html) vagy jobban van lehetővé
* A Velpic rendszergazdai engedélyekkel rendelkező felhasználói fiókja

## <a name="assigning-users-to-velpic"></a>Felhasználók hozzárendelése a Velpic-hez

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói fiók kiépítése, csak a felhasználók és csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálódnak. 

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-ben mely felhasználók és/vagy csoportok képviselik azon felhasználókat, akiknek hozzáférésre van szükségük a Velpic alkalmazáshoz. Miután eldöntötte, ezeket a felhasználókat hozzárendelheti a Velpic alkalmazáshoz az alábbi utasításokat követve:

[Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Fontos tippek a felhasználók Velpic hez való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Velpic hez a létesítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Velpic-hez rendel, a hozzárendelés idúrában ki kell **választania** a Felhasználói szerepkört vagy egy másik érvényes alkalmazásspecifikus szerepkört (ha elérhető). Vegye figyelembe, hogy az **alapértelmezett hozzáférési** szerepkör nem működik a kiépítéshez, és ezek a felhasználók kimaradnak.

## <a name="configuring-user-provisioning-to-velpic"></a>A felhasználó velpic-i kiépítésének konfigurálása

Ez a szakasz végigvezeti az Azure AD-nek a Velpic felhasználói fiók létesítési API-jával való csatlakoztatásán, valamint a kiépítési szolgáltatás konfigurálásán keresztül a hozzárendelt felhasználói fiókok létrehozásához, frissítéséhez és letiltásához a Velpicben az Azure AD-ben a felhasználó- és csoport-hozzárendelés alapján.

> [!TIP]
> Az [Azure Portalon](https://portal.azure.com)található utasításokat követve engedélyezheti az SAML-alapú egyszeri bejelentkezést a Velpic számára. Egyszeri bejelentkezés konfigurálható az automatikus kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>A Velpic automatikus felhasználói fiókkiépítéskonfigurálása az Azure AD-ben:

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Active Directory > Vállalati alkalmazások > az összes alkalmazás** szakaszt.

2. Ha már konfigurálta a Velpic-et egyszeri bejelentkezéshez, keresse meg a Velpic példányát a keresőmező használatával. Ellenkező esetben válassza a **Velpic** **hozzáadása** és keresése lehetőséget az alkalmazásgyűjteményben. Válassza a Velpic elemet a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

3. Válassza ki a Velpic példányát, majd válassza a **Kiépítés** lapot.

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Velpikus kiépítés](./media/velpic-provisioning-tutorial/Velpic1.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **bérlői URL-címet&velpic titkos tokenje.** (Ezeket az értékeket a Velpic fiók alatt találja:**Integrációs** > **plugin** > **SCIM** **kezelése)** > 

    ![Engedélyezési értékek](./media/velpic-provisioning-tutorial/Velpic2.png)

6. Az Azure Portalon kattintson a **Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Velpic alkalmazáshoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Velpic-fiók rendszergazdai engedélyekkel rendelkezik, és próbálkozzon újra az 5.

7. Adja meg annak a személynek vagy csoportnak az e-mail címét, akinek kiépítési hibaértesítéseket kell kapnia az **Értesítési e-mail** mezőben, és jelölje be az alábbi jelölőnégyzetet.

8. Kattintson a **Mentés** gombra.

9. A Leképezések szakaszban válassza **az Azure Active Directory felhasználóinak szinkronizálása velpic-re**lehetőséget.

10. Az **Attribútum-leképezések** szakaszban tekintse át az Azure AD-ről velpic-re szinkronizálandó felhasználói attribútumokat. Vegye figyelembe, hogy az **egyező** tulajdonságokként kiválasztott attribútumok a Velpic felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez kattintson a Mentés gombra.

11. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a Velpic, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban

12. Kattintson a **Mentés** gombra.

Ezzel elindítja a Velpic-hez rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását a Felhasználók és csoportok szakaszban. Vegye figyelembe, hogy a kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg a szolgáltatás fut. A Szinkronizálás **részletei** szakasz ban figyelheti az előrehaladást, és követheti a kiépítési tevékenységjelentésekre mutató hivatkozásokat, amelyek a kiépítési szolgáltatás által végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)