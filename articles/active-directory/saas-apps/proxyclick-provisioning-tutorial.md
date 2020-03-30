---
title: 'Oktatóanyag: Proxyclick konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a Proxyclick felhasználói fiókok automatikus kiépítésére és kiépítésének kiépítésével.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 95cb0371c4b2181d8f09991fe6e652c0e939f3e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063356"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Oktatóanyag: Proxyclick konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Proxyclick és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok proxykattintási automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [Proxyclick-bérlő](https://www.proxyclick.com/pricing)
* A Proxyclick rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="add-proxyclick-from-the-gallery"></a>Proxyclick hozzáadása a gyűjteményből

A Proxyclick konfigurálása az Azure AD automatikus felhasználói kiépítéséhez, hozzá kell adnia a Proxyclicket az Azure AD-alkalmazásgyűjteményből a felügyelt SaaS-alkalmazások listájához.

**Ha az Azure AD alkalmazásgyűjteményből szeretne proxykattintást hozzáadni, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Proxyclick ( Proxyclick**) kifejezést, válassza a **Proxyclick** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Proxyclick az eredménylistában](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Felhasználók hozzárendelése a Proxyclickhez

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Proxyclickhez. Miután úgy döntött, akkor hozzá ezeket a felhasználókat és / vagy csoportokat Proxyclick az alábbi utasításokat itt:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Fontos tippek a felhasználók Proxyclickhez való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve proxyclick az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót proxykattintáshoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Automatikus felhasználói kiépítés konfigurálása a Proxyclick alkalmazásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Proxyclickben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Dönthet úgy is, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Proxyclick számára, a [Proxyclick egyszeri bejelentkezési oktatóanyagutasításait](proxyclick-tutorial.md)követve. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>A Proxyclick automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Proxyclick lehetőséget.**

    ![A Proxyclick hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Proxyclick-fiók bérlői URL-címének** és **titkos jogkivonatának** lekéréséhez kövesse a 6.

6. Jelentkezzen be a [Proxyclick Felügyeleti konzolra.](https://app.proxyclick.com/login//?destination=%2Fdefault) Keresse meg a **Beállítások** > **integrációk** > **Tallózás piactéren**.

    ![Proxykattintási beállítások](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Proxykattintási integrációk](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick piactér](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Válassza az **Azure AD**lehetőséget. Kattintson a **Telepítés gombra.**

    ![Proxyclick Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Proxyclick telepítése](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Válassza a **Felhasználói kiépítés lehetőséget,** és kattintson **az Integráció indítása gombra.** 

    ![Proxyclick felhasználói kiépítés](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    A megfelelő beállításkonfigurációs felhasználói felületnek most meg kell jelennie a **Beállítások** > **integrációk**területen. Válassza a **Beállítások lehetőséget** az **Azure AD (Felhasználói kiépítés)** területen.

    ![Proxyclick létrehozása](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    A **bérlői URL-cím** és a **titkos jogkivonat** itt található.

    ![Proxyclick Token létrehozása](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Az 5. **Test Connection** Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Proxyclick-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

8. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

9. Kattintson a **Mentés** gombra.

10. A **Leképezések** csoportban válassza az **Azure Active Directory felhasználóinak szinkronizálása proxykattintással**lehetőséget.

    ![Proxyclick felhasználói leképezések](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Tekintse át az Azure AD-ből az **Attribútumleképezés** szakaszproxykattintásához szinkronizált felhasználói attribútumokat. Az **Egyező** tulajdonságokként kijelölt attribútumok a Proxyclick felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Proxyclick felhasználói attribútumai](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

14. Az Azure AD-létesítési szolgáltatás proxykattintás engedélyezéséhez módosítsa a **Kiépítés állapotát** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

15. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket a Proxyclick be szeretne építeni, ha a **Beállítások** szakasz **hatókörében** kiválasztja a kívánt értékeket.

    ![Kiépítési hatókör](common/provisioning-scope.png)

16. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Proxyclick en végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai

* A Proxyclick használatához **az e-mailek** és a **felhasználónév** azonos forrásértékkel rendelkeznek. Bármelyik attribútum frissítése módosítja a másik értéket.
* A Proxyclick nem támogatja a csoportok kiépítését.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

