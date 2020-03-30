---
title: 'Oktatóanyag: A BitaBIZ konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a Felhasználói fiókok bitabiz-i automatikus kiépítésére és kiépítésének kiépítésével.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d0d38abe-c041-482a-9d3f-ca340678c226
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: ad9176614c4a5235e5138444d4197286204a747f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059219"
---
# <a name="tutorial-configure-bitabiz-for-automatic-user-provisioning"></a>Oktatóanyag: A BitaBIZ konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a BitaBIZ és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok bitabiz-i automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [A BitaBIZ bérlő](https://bitabiz.dk/en/price/).
* A BitaBIZ rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="assigning-users-to-bitabiz"></a>Felhasználók hozzárendelése a BitaBIZ-hez

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a BitaBIZ-hez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a BitaBIZ-hez az alábbi utasításokat követve:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-bitabiz"></a>Fontos tippek a felhasználók BitaBIZ-hoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a BitaBIZ az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a BitaBIZ-hez rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="setup-bitabiz-for-provisioning"></a>A BitaBIZ beállítása a kiépítéshez

A BitaBIZ konfigurálása az Azure AD automatikus felhasználói kiépítéshez, engedélyeznie kell az SCIM-kiépítést a BitaBIZ-en.

1. Jelentkezzen be a [BitaBIZ Felügyeleti konzolba.](https://www.bitabiz.com/login?lang=en) Kattintson a **SETUP ADMIN gombra.**

    ![BitaBIZ felügyeleti konzol](media/bitabiz-provisioning-tutorial/setup-admin.png)

2.  Keresse meg az **INTEGRÁCIÓ**t.

    ![BitaBIZ felügyeleti konzol](media/bitabiz-provisioning-tutorial/integration.png)

2.  Keresse meg a **Microsoft Azure AD kiépítést.**  Válassza **az Engedélyezve lehetőséget** az Automatikus felhasználókiépítésben. Másolja az **SCIM kiépítési végpont URL-címének** és **a tulajdonosi jogkivonatnak az értékeit.** Ezek az értékek a bérlői URL-cím és a titkos jogkivonat mezőkben a BitaBIZ-alkalmazás kiépítés lapján az Azure Portalon.

    ![BitaBIZ Hozzáadása SCIM](media/bitabiz-provisioning-tutorial/authentication.png)


## <a name="add-bitabiz-from-the-gallery"></a>BitaBIZ hozzáadása a galériából

A BitaBIZ konfigurálásához az Azure AD automatikus felhasználói kiépítéséhez hozzá kell adnia a BitaBIZ-t az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni a BitaBIZ-t az Azure AD alkalmazásgyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **BitaBIZ**, bit a **BitaBIZ** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![BitaBIZ az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-bitabiz"></a>Automatikus felhasználói kiépítés konfigurálása a BitaBIZ szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a BitaBIZ-ben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Dönthet úgy is, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a BitaBIZ számára, a [BitaBIZ egyszeri bejelentkezési útmutatóutasításait](BitaBIZ-tutorial.md)követve. Az egyszeri bejelentkezés az automatikus felhasználói kiépítéstől függetlenül konfigurálható, bár ez a két funkció kiegészíti egymást

### <a name="to-configure-automatic-user-provisioning-for-bitabiz-in-azure-ad"></a>A BitaBIZ automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **BitaBIZ**lehetőséget.

    ![A BitaBIZ hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A rendszergazdai hitelesítő adatok szakaszban adja meg az **SCIM kiépítési végpont URL-címét** és **a tulajdonosi token** értékeket a bérlői URL-cím és a titkos jogkivonat korábbi részében. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a BitaBIZ-hez. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a BitaBIZ-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása a BitaBIZ szolgáltatással**lehetőséget.

    ![BitaBIZ felhasználói leképezések](media/bitabiz-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD és a BitaBIZ között szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a BitaBIZ felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![BitaBIZ felhasználói attribútumok](media/bitabiz-provisioning-tutorial/user-attribute.png)


10. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

11. Az Azure AD-kiépítési szolgáltatás engedélyezése a BitaBIZ, módosítsa a **kiépítési állapot** **be van kapcsolva** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a BitaBIZ-ba, ha a **Beállítások** szakasz **hatókörében** kiválasztja a kívánt értékeket.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a BitaBIZ-on végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai

* A BitaBIZ használatához **felhasználónév**, **e-mail**, **keresztnév** és **vezetéknév** kötelező attribútumként szükséges. 
* A BitaBIZ jelenleg nem támogatja a kemény törléseket.

## <a name="additional-resources"></a>További források

* [A vállalati alkalmazások felhasználói fiókkiépítési kezelésének kezelése.](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések becsatornázásáról.](../app-provisioning/check-status-user-account-provisioning.md)
