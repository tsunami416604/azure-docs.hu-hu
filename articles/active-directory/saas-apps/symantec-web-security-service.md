---
title: 'Oktatóanyag: A Symantec Web Security Service (WSS) konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogy miként konfigurálhatja az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiállítsa a felhasználói fiókokat a Symantec Web Security Service (WSS) szolgáltatásba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: fbd105ca1623512a3c16f3b609374f5c4055898b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063118"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Oktatóanyag: A Symantec Web Security Service (WSS) konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Symantec Web Security Service (WSS) és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket, hogy az Azure AD automatikusan üzembe helyezze és deprovision felhasználókat és/vagy csoportokat a Symantec Web Security Service (WSS) szolgáltatásba.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [A Symantec Web Security Service (WSS) bérlője](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* A Symantec Web Security Service (WSS) rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Felhasználók hozzárendelése a Symantec Web Security Service (WSS) szolgáltatáshoz

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Symantec Web Security Service (WSS) szolgáltatáshoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Symantec Web Security Service (WSS) szolgáltatáshoz az alábbi utasításokat követve:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Fontos tippek a felhasználók Symantec Web Security Service (WSS) szolgáltatáshoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Symantec Web Security Service (WSS) szolgáltatáshoz az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Symantec Web Security Service (WSS) szolgáltatáshoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>A Symantec Web Security Service (WSS) telepítése kiépítéshez

Mielőtt konfigurálná a Symantec Web Security Service (WSS) automatikus felhasználói kiépítést az Azure AD-vel, engedélyeznie kell az SCIM-kiépítést a Symantec Web Security Service (WSS) szolgáltatásban.

1. Jelentkezzen be a [Symantec Web Security Service felügyeleti konzoljára.](https://portal.threatpulse.com/login.jsp) Keresse meg a **Megoldások** > **szolgáltatást.**

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/service.png)

2. Keresse meg a **Fiókkarbantartási** > integrációk > **új****integrációját.**

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Válassza **a Külső felhasználók & csoportok szinkronizálása lehetőséget.** 

    ![Symantec webbiztonsági szolgáltatás](media/symantec-web-security-service/third-party-users.png)

4.  Másolja az **SCIM URL-címét** és **a tokenjét.** Ezeket az értékeket a **bérlői URL-cím** és a **titkos jogkivonat** mezőben adja meg a Symantec Web Security Service (WSS) alkalmazás Kiépítése lapján az Azure Portalon.

    ![Symantec webbiztonsági szolgáltatás](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Symantec Web Security Service (WSS) hozzáadása a galériából

A Symantec Web Security Service (WSS) konfigurálásához az Azure AD-vel való automatikus felhasználói kiépítéshez hozzá kell adnia a Symantec Web Security Service (WSS) szolgáltatást az Azure AD alkalmazásgyűjteményből a felügyelt SaaS-alkalmazások listájához.

**Ha a Symantec Web Security Service (WSS) szolgáltatást az Azure AD alkalmazásgyűjteményből szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Symantec Web Security Service**kifejezést, válassza a **Symantec Web Security Service** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Symantec Web Security Service (WSS) az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Automatikus felhasználói kiépítés konfigurálása a Symantec Web Security Service (WSS) szolgáltatásba

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Symantec Web Security Service (WSS) szolgáltatásában az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Dönthet úgy is, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Symantec Web Security Service (WSS) szolgáltatáshoz, a [Symantec Web Security Service (WSS) egyszeri bejelentkezési oktatóanyagában](symantec-tutorial.md)található utasításokat követve. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>A Symantec Web Security Service (WSS) automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Symantec Web Security Service (Symantec Web Security Service) lehetőséget.**

    ![A Symantec Web Security Service (WSS) hivatkozása az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A rendszergazdai hitelesítő adatok szakaszban adja meg az **SCIM URL-címét** és **token** értékeit, amelyeket korábban a **bérlői URL-cím,** illetve a **titkos jogkivonat** korábbi részében. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Symantec Web Security Service szolgáltatáshoz. Ha a kapcsolat megszakad, győződjön meg arról, hogy a Symantec Web Security Service (WSS) fiók rendelkezik rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza az **Azure Active Directory felhasználóinak szinkronizálása a Symantec Web Security Service (WSS) szolgáltatással lehetőséget.**

    ![Symantec Web Security Service (WSS) felhasználói leképezései](media/symantec-web-security-service/usermapping.png)

9. Tekintse át az Azure AD és a Symantec Web Security Service (WSS) között szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Symantec Web Security Service (WSS) felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Symantec Web Security Service (WSS) felhasználói leképezései](media/symantec-web-security-service/userattribute.png)

10. A **Leképezések** csoportban válassza az **Azure Active Directory-csoportok szinkronizálása a Symantec webbiztonsági szolgáltatással**lehetőséget.

    ![Symantec Web Security Service (WSS) felhasználói leképezései](media/symantec-web-security-service/groupmapping.png)

11. Tekintse át az Azure AD és a Symantec Web Security Service (WSS) között szinkronizált csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Symantec Web Security Service (WSS) csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Symantec Web Security Service (WSS) felhasználói leképezései](media/symantec-web-security-service/groupattribute.png)

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a Symantec Web Security Service, módosítsa a **kiépítési állapot** **be van kapcsolva** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. A **Beállítások** szakasz **Hatókör** területén a kívánt értékek kiválasztásával határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Symantec Web Security Service (WSS) szolgáltatásba.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. Ha többet szeretne tudni arról, hogy mennyi ideig tart a felhasználók és/vagy csoportok kiépítése, lásd: [Mennyi ideig tart a felhasználók kiépítése.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

Az Aktuális **állapot** szakasz segítségével figyelheti az előrehaladást, és követheti a létesítési tevékenységjelentésére mutató hivatkozásokat, amelyek az Azure AD-kiépítési szolgáltatás által a Symantec Web Security Service (WSS) szolgáltatáson végrehajtott összes műveletet ismertetik. További információ: [A felhasználói kiépítés állapotának ellenőrzése.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) Az Azure AD kiépítési naplók olvasásához olvassa el [az Automatikus felhasználói fiók kiépítésről szóló jelentéskészítés című témakört.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
