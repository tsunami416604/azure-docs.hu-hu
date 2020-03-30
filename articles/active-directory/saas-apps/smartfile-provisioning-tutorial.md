---
title: 'Oktatóanyag: A SmartFile konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok automatikus kiépítésére és a SmartFile-ba való kiépítésének kiépítésével.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eeff992-a84f-4f88-a360-9accbd077538
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: b113cc27195b2ce954d677ab0f1ec83e394946be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060218"
---
# <a name="tutorial-configure-smartfile-for-automatic-user-provisioning"></a>Oktatóanyag: A SmartFile konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a SmartFile és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok smartfile-ba történő automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [SmartFile-bérlő](https://www.SmartFile.com/pricing/).
* A SmartFile rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="assigning-users-to-smartfile"></a>Felhasználók hozzárendelése a SmartFile fájlhoz

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a SmartFile-hoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a SmartFile-hoz az alábbi utasításokat követve:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-smartfile"></a>Fontos tippek a felhasználók SmartFile fájlhoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve smartfile az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a SmartFile fájlhoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="setup-smartfile-for-provisioning"></a>SmartFile beállítása a kiépítéshez

A SmartFile konfigurálása az Azure AD automatikus felhasználói kiépítéséhez, engedélyeznie kell az SCIM-kiépítést a SmartFile-on, és további szükséges részleteket kell gyűjtenie.

1. Jelentkezzen be a SmartFile Felügyeleti konzolra. Nyissa meg a SmartFile Felügyeleti konzol jobb felső sarkát. Válassza a **Termékkulcsot**.

    ![SmartFile felügyeleti konzol](media/smartfile-provisioning-tutorial/login.png)

2. Tulajdonosi jogkivonat létrehozásához másolja a **termékkulcsot** és a **termékjelszót.** Illessze be őket egy jegyzettömbbe, amely nekik kettősponttal van elválasztva.
    
     ![SmartFile hozzáadása SCIM](media/smartfile-provisioning-tutorial/auth.png)

    ![SmartFile hozzáadása SCIM](media/smartfile-provisioning-tutorial/key.png)

## <a name="add-smartfile-from-the-gallery"></a>SmartFile hozzáadása a gyűjteményből

A SmartFile konfigurálásához az Azure AD automatikus felhasználói kiépítéséhez hozzá kell adnia a SmartFile-t az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni a SmartFile alkalmazást az Azure AD alkalmazásgyűjteményből, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **SmartFile**, **smartfile** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![SmartFile az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-smartfile"></a>Automatikus felhasználói kiépítés konfigurálása a SmartFile fájlba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a SmartFile-ban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Dönthet úgy is, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a SmartFile fájlhoz, a [SmartFile Single sign-on oktatóanyag](SmartFile-tutorial.md)utasításait követve. Az egyszeri bejelentkezés az automatikus felhasználói kiépítéstől függetlenül konfigurálható, bár ez a két funkció kiegészíti egymást

### <a name="to-configure-automatic-user-provisioning-for-smartfile-in-azure-ad"></a>A SmartFile automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **SmartFile lehetőséget.**

    ![A SmartFile hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5.  A **Rendszergazdai hitelesítő** adatok `https://<SmartFile sitename>.smartfile.com/ftp/scim` csoportban adja meg a **bérlői URL-címet.** Egy példa így `https://demo1test.smartfile.com/ftp/scim`nézne ki. Adja meg a **titkos jogkivonatban**korábban beolvasott **tulajdonosi token** értékét (ProductKey:ProductPassword). Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD képes legyen csatlakozni a SmartFile fájlhoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a SmartFile-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása smartfile fájlba**lehetőséget.

    ![SmartFile felhasználói hozzárendelések](media/smartfile-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ről a SmartFile szolgáltatásra szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a SmartFile felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![SmartFile felhasználói attribútumai](media/smartfile-provisioning-tutorial/userattribute.png)

10. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása smartfile fájllal**lehetőséget.

    ![SmartFile csoporthozzárendelések](media/smartfile-provisioning-tutorial/groupmapping.png)

11. Tekintse át az Azure AD-ről a SmartFile szolgáltatásra szinkronizált csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a SmartFile csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![SmartFile-csoport attribútumai](media/smartfile-provisioning-tutorial/groupattribute.png)

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-létesítési szolgáltatás engedélyezéséhez a SmartFile, módosítsa a **kiépítési állapot** **be van kapcsolva** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a SmartFile alkalmazásba a **Beállítások** szakasz **Hatókör csoportjában** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

    Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a SmartFile-on végrehajtott összes műveletet.

    Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésének jelentéskészítése című témakörben](../app-provisioning/check-status-user-account-provisioning.md) olvashat bővebben.
    
## <a name="connector-limitations"></a>Összekötő korlátai

* A SmartFile csak a kemény törlést támogatja. 

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

 [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
