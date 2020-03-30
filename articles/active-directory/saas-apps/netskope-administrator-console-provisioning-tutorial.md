---
title: 'Oktatóanyag: A Netskope felügyeleti konzol konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok hálózaton belüli automatikus kiépítésére és kiépítésének kimerülésével.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e99f0e0f-28d0-43c6-a52b-df873fb9d551
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: eaee8e3305572d696e52c3879be2e2b9924bc93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061250"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>Oktatóanyag: A Netskope felügyeleti konzol konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Netskope administrator console és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok netskope-i felügyeleti konzolra történő automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [Netskope felügyeleti konzol bérlője](https://www.netskope.com/)
* A Netskope felügyeleti konzol rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="assigning-users-to-netskope-administrator-console"></a>Felhasználók hozzárendelése a Netskope felügyeleti konzolhoz

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Netskope felügyeleti konzolhoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Netskope Felügyeleti konzolhoz az alábbi utasításokat követve:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>Fontos tippek a felhasználók Netskope felügyeleti konzolhoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van hozzárendelve a Netskope felügyeleti konzolaz automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Netskope felügyeleti konzolhoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>A Netskope felügyeleti konzol beállítása kiépítéshez

1. Jelentkezzen be a [Netskope felügyeleti konzol felügyeleti konzoljára](https://netskope.goskope.com/). Nyissa meg a **Kezdőlap > beállítások lapot.**

    ![Netskope felügyeleti konzol felügyeleti konzolja](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Nyissa meg az **Eszközök menüt.** Az **Eszközök** menüben keresse meg a **Könyvtáreszközök > scim integration**.

    ![Netskope felügyeleti konzol eszközei](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope felügyeleti konzol hozzáadása SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Görgessen le, és kattintson a **Token hozzáadása** gombra. Az **OAuth ügyfélnév hozzáadása** párbeszédpanelen adja meg **az ÜGYFÉL NEVÉT,** és kattintson a **Mentés** gombra.

    ![Netskope felügyeleti konzol token hozzáadása](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope felügyeleti konzol client neve](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Másolja az **SCIM-kiszolgáló URL-címét** és a **TOKEN -t.** Ezek az értékek a bérlői URL-cím és a titkos jogkivonat mezőkben, illetve a Netskope felügyeleti konzol alkalmazás kiépítés lapján az Azure Portalon.

    ![Netskope felügyeleti konzol token létrehozása](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Netskope felügyeleti konzol hozzáadása a galériából

A Netskope felügyeleti konzol konfigurálása az Azure AD automatikus felhasználói kiépítéséhez, hozzá kell adnia a Netskope felügyeleti konzolt az Azure AD alkalmazásgyűjteményből a felügyelt SaaS-alkalmazások listájához.

**Netskope felügyeleti konzol hozzáadása az Azure AD alkalmazásgyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Netskope Administrator Console**kifejezést, válassza a **Netskope felügyeleti konzol** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Netskope felügyeleti konzol az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>Automatikus felhasználói kiépítés konfigurálása a Netskope felügyeleti konzolra 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Netskope felügyeleti konzolon az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> A Netskope administrator console saml-alapú egyszeri bejelentkezését is engedélyezheti a Netskope Administrator Console alkalmazáshoz, ha követi a [Netskope Administrator Console Single sign-on oktatóanyagutasításait.](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial) Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

> [!NOTE]
> Ha többet szeretne megtudni a Netskope Administrator Console SCIM-végpontjáról, olvassa el [ezt](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon)a pontot.

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>A Netskope felügyeleti konzol automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Netskope felügyeleti konzol lehetőséget.**

    ![A Netskope felügyeleti konzol hivatkozása az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok** csoportban adja meg az **SCIM-kiszolgáló URL-értékét,** amelyet korábban olvas be a **bérlői URL-címben.** Adja meg a **titkos jogkivonat**korábbi tokenjeként beolvasott **TOKEN** értéket. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Netskope felügyeleti konzolhoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Netskope rendszergazdai konzolfiókja rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza az **Azure Active Directory felhasználóinak szinkronizálása netskope-i rendszergazdai konzollal**lehetőséget.

    ![Netskope rendszergazdai konzol felhasználói hozzárendelései](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD és a Netskope felügyeleti konzol között szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Netskope felügyeleti konzol felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Netskope rendszergazdai konzol felhasználói attribútumai](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása netskope-i rendszergazdai konzollal**lehetőséget.

    ![Netskope rendszergazdai konzolcsoport-hozzárendelések](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD és a Netskope felügyeleti konzol között szinkronizált csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Netskope felügyeleti konzol on-update műveletek csoportjainak egyezésére szolgálnak. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Netskope rendszergazdai konzolcsoport-attribútumok](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-létesítési szolgáltatás engedélyezéséhez a Netskope felügyeleti konzol, módosítsa a **kiépítési állapot** **be van kapcsolva** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Netskope felügyeleti konzolra a **Beállítások** szakasz **Hatókör** csoportjában a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Netskope felügyeleti konzolon végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

