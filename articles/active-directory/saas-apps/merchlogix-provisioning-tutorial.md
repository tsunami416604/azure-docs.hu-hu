---
title: 'Oktatóanyag: A MerchLogix konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a Felhasználói fiókok merchLogix-ra történő automatikus kiépítésére és kivonására.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 9df4c7c5-9a58-478e-93b7-2f77aae12807
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4aa60fb565552961a3c85346c39c318a90c8adc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061297"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Oktatóanyag: A MerchLogix konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a MerchLogix és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok MerchLogix-ba történő automatikus kiépítéséhez és kiépítésének kivonásához.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* MerchLogix-bérlő
* Technikai kapcsolattartó a MerchLogix-nál, aki meg tudja adni az SCIM végpont URL-címét és a felhasználó kiépítéséhez szükséges titkos jogkivonatot

## <a name="adding-merchlogix-from-the-gallery"></a>A MerchLogix hozzáadása a galériából

A MerchLogix konfigurálása az Azure AD automatikus felhasználói kiépítéséhez, hozzá kell adnia a MerchLogix-ot az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni a MerchLogix-ot az Azure AD alkalmazásgyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gombja][1]

2. Keresse meg a **Vállalati alkalmazások** > **Minden alkalmazást**.

    ![A vállalati alkalmazások szakasz][2]

3. MerchLogix hozzáadásához kattintson a párbeszédpanel tetején az **Új alkalmazás** gombra.

    ![Az Új alkalmazás gomb][3]

4. A keresőmezőbe írja be a **MerchLogix**kifejezést.

5. Az eredménypanelen válassza a **MerchLogix**lehetőséget, majd a **Hozzáadás** gombra kattintva adja hozzá a MerchLogix-ot a SaaS-alkalmazások listájához.

    ![MerchLogix kiépítés][4]

## <a name="assigning-users-to-merchlogix"></a>Felhasználók hozzárendelése a MerchLogix-hoz

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak a felhasználók és/vagy csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálva vannak. 

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a MerchLogix-hoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat a MerchLogix-hoz rendelheti az alábbi utasításokat követve:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Fontos tippek a felhasználók MerchLogix-hoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a MerchLogix a kezdeti automatikus felhasználói kiépítési konfiguráció teszteléséhez. A tesztek sikeresse után további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a MerchLogix-hoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Automatikus felhasználói kiépítés konfigurálása a MerchLogix rendszerbe 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a MerchLogix-ban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Dönthet úgy is, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a MerchLogix számára, a [MerchLogix egyszeri bejelentkezési oktatóanyag](merchlogix-tutorial.md)utasításait követve. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>A MerchLogix automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra,](https://portal.azure.com) és keresse meg az **Azure Active Directory > Vállalati alkalmazások > az összes alkalmazást.**

2. Válassza ki a MerchLogix-ot a SaaS-alkalmazások listájából.

3. Válassza a **Kiépítés** lapot.

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![MerchLogix kiépítés](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. A **Rendszergazdai hitelesítő adatok csoportban:**

    * A **Bérlő URL-cím** mezőjébe írja be a MerchLogix technikai kapcsolattartó jain megadott SCIM-végpont URL-címét.

    * A **Titkos jogkivonat** mezőbe írja be a MerchLogix technikai kapcsolattartó ja) által biztosított titkos jogkivonatot.

6. Az 5. **Test Connection** Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a MerchLogix-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

7. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

8. Kattintson a **Mentés** gombra.

9. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása a MerchLogix szolgáltatással**lehetőséget.

10. Tekintse át az Azure AD és a MerchLogix között szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a MerchLogix felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

11. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása a MerchLogix szolgáltatással**lehetőséget.

12. Tekintse át az Azure AD és a MerchLogix között szinkronizált csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a MerchLogix csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

13. Az Azure AD-létesítési szolgáltatás engedélyezéséhez a MerchLogix, módosítsa a **kiépítési állapot** **be van kapcsolva** a **Beállítások** szakaszban.

14. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a MerchLogix-on végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
