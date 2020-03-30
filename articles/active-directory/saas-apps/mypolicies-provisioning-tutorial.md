---
title: 'Oktatóanyag: A myPolicies konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok myPolicies-hez való automatikus kiépítésére és kiépítésének kiépítésével.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f000896d-a78c-4d20-a79c-74c1f9b4961a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 353da826b6e339d40a5d85bbf63caac5bf7094f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061362"
---
# <a name="tutorial-configure-mypolicies-for-automatic-user-provisioning"></a>Oktatóanyag: A myPolicies konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a myPolicies és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok myPolicies automatikus kiépítéséhez és de-provision kiépítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [A myPolicies bérlő](https://mypolicies.com/index.html#section10).
* A myPolicies rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="assigning-users-to-mypolicies"></a>Felhasználók hozzárendelése a myPolicies-hez

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a myPolicies-hez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a myPolicies-hez az alábbi utasításokat követve:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mypolicies"></a>Fontos tippek a felhasználók myPolicies-hez való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van hozzárendelve a myPolicies az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a myPolicies-hez rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="setup-mypolicies-for-provisioning"></a>Saját házirendek beállítása a kiépítéshez

A myPolicies konfigurálása az Azure AD automatikus felhasználói kiépítés, engedélyeznie kell az SCIM kiépítése a myPolicies.

1. Az SCIM-kiépítés **support@mypolicies.com** konfigurálásához szükséges titkos jogkivonat beszerzéséhez forduljon a myPolicies képviselőjéhez.

2.  Mentse a myPolicies képviselő által biztosított tokenértéket. Ez az érték a **titkos jogkivonat** mezőben lesz megadva a myPolicies alkalmazás kiépítés lapján az Azure Portalon.

## <a name="add-mypolicies-from-the-gallery"></a>Saját irányelvek hozzáadása a gyűjteményből

A myPolicies konfigurálásához az Azure AD automatikus felhasználói kiépítés, hozzá kell adnia a myPolicies az Azure AD-alkalmazáskatalógusa a felügyelt SaaS-alkalmazások listájához.

**Ha az Azure AD alkalmazásgyűjteményből szeretné hozzáadni a myPolicies alkalmazást, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **MyPolicies**kifejezést, válassza a **MyPolicies** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![myPolicies az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mypolicies"></a>Automatikus felhasználói kiépítés konfigurálása a myPolicies rendszerbe 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a myPolicies ben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Azt is választhatja, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a myPolicies-hez, a [myPolicies Single sign-on oktatóanyag](mypolicies-tutorial.md)utasításait követve. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-mypolicies-in-azure-ad"></a>A myPolicies automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **MyPolicies lehetőséget.**

    ![A MyPolicies hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://<myPoliciesCustomDomain>.mypolicies.com/scim` szakaszban adja meg a **bérlői URL-címet,** ahol `<myPoliciesCustomDomain>` a myPolicies egyéni tartomány. A myPolicies ügyféltartományát az URL-címről is lekérheti.
Példa: `<demo0-qa>`.mypolicies.com.

6. A **Titkos jogkivonat**mezőbe írja be a korábban beolvasott jogkivonat-értéket. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a myPolicies-hez. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a myPolicies fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

7. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

8. Kattintson a **Mentés** gombra.

9. A **Leképezések** csoportban válassza az **Azure Active Directory-felhasználók szinkronizálása a myPolicies lehetőséget.**

    ![myPolicies felhasználói leképezések](media/mypolicies-provisioning-tutorial/usermapping.png)

10. Tekintse át az Azure AD-ből a myPolicies szolgáltatással szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok a myPolicies frissítési műveletek felhasználói fiókjainak egyeztetésére szolgálnak. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![myPolicies felhasználói leképezések](media/mypolicies-provisioning-tutorial/userattribute.png)

11. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

12. Az Azure AD-létesítési szolgáltatás engedélyezése a myPolicies, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

13. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a myPolicies-be a kívánt értékek kiválasztásával a **Beállítások** szakasz **hatókörében.**

    ![Kiépítési hatókör](common/provisioning-scope.png)

14. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a myPolicies-en végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai

* a myPolicies mindig megköveteli **a felhasználónevet,** **az e-mailt** és **a külső azonosítót.**
* A myPolicies nem támogatja a felhasználói attribútumok kemény törléseit.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
