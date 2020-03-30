---
title: 'Oktatóanyag: A Soloinsight-CloudGate egyszeri szolgáltatások konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiirtsa a felhasználói fiókokat a Soloinsight-CloudGate Egyszeri szolgáltatásba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 07558ceb-d406-40e7-90b8-1b40fdc829e7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 6ab90a6aea262d5c7067f9f41b9ddfc090b7371d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063194"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Oktatóanyag: A Soloinsight-CloudGate egyszeri szolgáltatás konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a lépéseket kell végrehajtani a Soloinsight-CloudGate SSO és az Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítése és a kiépítés a felhasználók és/vagy csoportok Soloinsight-CloudGate SSO.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [Soloinsight-CloudGate SSO-bérlő](https://www.soloinsight.com/)
* A Soloinsight-CloudGate SSO felügyeleti engedélyekkel rendelkező felhasználói fiókja.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Felhasználók hozzárendelése a Soloinsight-CloudGate Egyszeri bejelentkezéshez

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Soloinsight-CloudGate egyszeri bejelentkezéshez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Soloinsight-CloudGate SSO-hoz az itt található utasítások követésével:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Fontos tippek a felhasználók Soloinsight-CloudGate SSO-hoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van hozzárendelve a Soloinsight-CloudGate Egyszeri szolgáltatáshoz az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Soloinsight-CloudGate SSO-hoz rendel hozzá, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>A Soloinsight-CloudGate Egyszeri szolgáltatás beállítása a kiépítéshez

1. Jelentkezzen be a [Soloinsight-CloudGate SSO felügyeleti konzolra.](https://soloinsight.sigateway.com/login) Nyissa meg **a Felügyeleti > rendszerbeállítások at.**

    ![Soloinsight-CloudGate SSO felügyeleti konzol](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Keresse meg az **Általános**.

    ![Soloinsight-Cloudgate SSO hozzáadása SCIM](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Görgessen le a lap végéig a **bérlői URL és** a **titkos jogkivonat lekéréséhez.** Másolja a **titkos tokent**. Ezt az értéket a Soloinsight-CloudGate SSO-alkalmazás Azure Portalon a Kiépítés lapján a Titkos jogkivonat mezőben adja meg.

    ![Soloinsight-CloudGate Egyszeri szolgáltatás token létrehozása](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Soloinsight-CloudGate SSO hozzáadása a galériából

A Soloinsight-CloudGate Egyszeri szolgáltatás konfigurálása az Azure AD-vel való automatikus felhasználói kiépítéshez, hozzá kell adnia a Soloinsight-CloudGate Egyszeri szolgáltatást az Azure AD alkalmazásgyűjteményből a felügyelt SaaS-alkalmazások listájához.

**Soloinsight-CloudGate Egyszeri szolgáltatás hozzáadása az Azure AD alkalmazásgyűjteményből, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Soloinsight-CloudGate SSO**kifejezést, válassza a **Soloinsight-CloudGate SSO** lehetőséget az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Soloinsight-CloudGate Egyszeri bejelentkezés az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Automatikus felhasználói kiépítés konfigurálása a Soloinsight-CloudGate Egyszeri szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Soloinsight-CloudGate Egyszeri bejelentkezésben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Dönthet úgy is, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Soloinsight-CloudGate SSO-hoz, a [Soloinsight-CloudGate Egyszeri bejelentkezési oktatóanyag](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial)utasításait követve. Az egyszeri bejelentkezés az automatikus felhasználói kiépítéstől függetlenül konfigurálható, bár ez a két funkció kiegészíti egymást

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>A Soloinsight-CloudGate Egyszeri bejelentkezés automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Soloinsight-CloudGate SSO**lehetőséget.

    ![A Soloinsight-CloudGate SSO hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://sigateway.com/scim/v2/sync/serviceproviderconfig` csoportban adja meg a **bérlői URL-címet.** Adja meg a **titkos jogkivonat**korábbi, beolvasott **SCIM hitelesítési token** értékét. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Soloinsight-CloudGate egyszeri szolgáltatóhoz. Ha a kapcsolat sikertelen, győződjön meg arról, hogy a Soloinsight-CloudGate SSO-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** szakaszban válassza **az Azure Active Directory felhasználóinak szinkronizálása a Soloinsight-CloudGate egyszeri bejelentkezést.**

    ![Soloinsight-CloudGate egyszeri szolgáltatás felhasználói leképezései](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD és a Soloinsight-CloudGate Egyszeri bejelentkezés az **Attribútumleképezés** szakaszban szinkronizált felhasználói attribútumokat. Az **egyező** tulajdonságokként kiválasztott attribútumok a Soloinsight-CloudGate Egyszeri bejelentkezés felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Soloinsight-CloudGate Egyszeri szolgáltatások felhasználói attribútumai](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása a Soloinsight-CloudGate Egyszeri bejelentkezés**t.

    ![Soloinsight-CloudGate SSO-csoport leképezései](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD és a Soloinsight-CloudGate Egyszeri bejelentkezés között az **Attribútumleképezés** szakaszban szinkronizált csoportattribútumokat. Az **egyező** tulajdonságokként kiválasztott attribútumok a Soloinsight-CloudGate Egyszeri bejelentkezés csoportban lévő csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Soloinsight-CloudGate SSO-csoport attribútumai](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-létesítési szolgáltatás engedélyezéséhez a Soloinsight-CloudGate Egyszeri bejelentkezés, módosítsa a **kiépítés állapota** **be** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket a Soloinsight-CloudGate Egyszeri bejelentkezéshez szeretne kiépíteni a **Beállítások** szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-létesítési szolgáltatás által a Soloinsight-CloudGate egyszeri szolgáltatón végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

