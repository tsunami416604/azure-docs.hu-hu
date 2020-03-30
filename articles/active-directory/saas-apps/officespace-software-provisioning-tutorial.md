---
title: 'Oktatóanyag: Az OfficeSpace szoftver konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiállítsa a felhasználói fiókokat az OfficeSpace Szoftverbe.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f832a0a6-ad0a-453f-a747-9cd717e11181
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: Zhchia
ms.openlocfilehash: 3d472b300400cf230773ba01f3f4362988c34e81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063411"
---
# <a name="tutorial-configure-officespace-software-for-automatic-user-provisioning"></a>Oktatóanyag: Az OfficeSpace szoftver konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa az OfficeSpace Software és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket, hogy az Azure AD automatikusan kiépítse és dekonalizálja a felhasználókat és/vagy csoportokat az OfficeSpace Szoftverbe.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [OfficeSpace szoftveres bérlő](https://www.officespacesoftware.com/)
* Az OfficeSpace Szoftver rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="assigning-users-to-officespace-software"></a>Felhasználók hozzárendelése az OfficeSpace szoftverhez

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük az OfficeSpace szoftverhez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti az OfficeSpace Szoftverhez az alábbi utasítások szerint:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-officespace-software"></a>Fontos tippek a felhasználók OfficeSpace szoftverhez való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve az OfficeSpace Szoftver az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót az OfficeSpace Szoftverhez rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-officespace-software-for-provisioning"></a>Az OfficeSpace szoftver beállítása kiépítéshez

1. Jelentkezzen be az [OfficeSpace szoftverfelügyeleti konzoljára.](https://support.officespacesoftware.com/hc) Nyissa meg a **Beállítások > összekötők lehetőséget.**

    ![OfficeSpace szoftverfelügyeleti konzol](media/officespace-software-provisioning-tutorial/settings.png)

2.  Nyissa meg a **Címtár-szinkronizálási > SCIM-et.**

    ![OfficeSpace szoftver hozzáadása SCIM](media/officespace-software-provisioning-tutorial/scim.png)

3.  Másolja az **SCIM hitelesítési tokenre**. Ez az érték az OfficeSpace szoftveralkalmazás kiépítés lapján az Azure Portalon található Titkos jogkivonat mezőben lesz megadva.

    ![OfficeSpace szoftver létrehozása token](media/officespace-software-provisioning-tutorial/token.png)

## <a name="add-officespace-software-from-the-gallery"></a>OfficeSpace szoftver hozzáadása a galériából

Mielőtt konfigurálna az OfficeSpace szoftvert az Azure AD-vel való automatikus felhasználói kiépítéshez, hozzá kell adnia az OfficeSpace Szoftvert az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához.

**Ha az OfficeSpace szoftvert az Azure AD alkalmazásgyűjteményből szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **OfficeSpace Software**kifejezést, és válassza az **OfficeSpace Szoftver** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![OfficeSpace Szoftver az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-officespace-software"></a>Automatikus felhasználói kiépítés konfigurálása az OfficeSpace szoftverbe 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le az OfficeSpace Szoftverben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Az OfficeSpace [Software egyszeri bejelentkezési útmutatójában](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-tutorial)található utasításokat követve engedélyezheti az SAML-alapú egyszeri bejelentkezést is az OfficeSpace Szoftver egyszeri bejelentkezési útmutatójában. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-officespace-software-in-azure-ad"></a>Az OfficeSpace Szoftver automatikus üzembe iszsalékozásának konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **OfficeSpace Software lehetőséget.**

    ![Az OfficeSpace Szoftver hivatkozása az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://<subdomain>.officespacesoftware.com/api/scim/v2/` csoportban adja meg az URL-formátumot **a bérlői URL-címben.** Például: `https://contoso.officespacesoftware.com/api/scim/v2/`. Adja meg a **titkos jogkivonat**korábbi, beolvasott **SCIM hitelesítési token** értékét. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud az OfficeSpace szoftverhez. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy az OfficeSpace szoftverfiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza az **Azure Active Directory felhasználóinak szinkronizálása az OfficeSpace szoftverrel**lehetőséget.

    ![OfficeSpace szoftverfelhasználói leképezései](media/officespace-software-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD és az OfficeSpace Software között szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kiválasztott attribútumok az OfficeSpace Software felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![OfficeSpace szoftver felhasználói attribútumai](media/officespace-software-provisioning-tutorial/userattributes.png)

11. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

12. Az OfficeSpace szoftver Azure AD-kiépítési szolgáltatásának engedélyezéséhez módosítsa a **Kiépítés állapotát** **bekapcsolva** állapotra a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

13. A **Beállítások** szakasz **Hatókör területén** a kívánt értékek kiválasztásával határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni az OfficeSpace Szoftverbe.

    ![Kiépítési hatókör](common/provisioning-scope.png)

14. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amelyek az OfficeSpace szoftveren az Azure AD-kiépítési szolgáltatás által végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

